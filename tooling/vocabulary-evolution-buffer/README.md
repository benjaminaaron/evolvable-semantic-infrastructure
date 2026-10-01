# Vocabulary Evolution Buffer

*Adapting queries as getters so applications can migrate at their own pace.*

Can queries (e.g. SPARQL SELECT) acting as getters buffer an application's internal model from otherwise breaking changes in an evolving knowledge graph? The idea is to adapt how data is retrieved while preserving the structure and meaning the application expects.

This worked example extends [Apps share a Pod](../../scenarios/01-apps-share-pod/README.md). The library app uses hobbies to recommend books. Its application logic expects a table with columns `?user` (IRI) and `?hobby` (string literal), with one row per user/hobby pair. The graph can change while this contract stays stable, provided the query can still recover the same information and meaning.

```turtle
@prefix : <https://example.org/default#> .
```

## Change 1: Rename a predicate

Initially, the graph contains:

```turtle
:user :hasHobby "Gardening", "Hiking" .
```

The library's query is:

```sparql
PREFIX : <https://example.org/default#>
SELECT ?user ?hobby WHERE {
  ?user :hasHobby ?hobby .
}
```

The first change replaces `:hasHobby` with `:hobby`. The migration explicitly declares that the meaning and values are preserved:

```turtle
:user :hobby "Gardening", "Hiking" .
```

Only the query's predicate changes:

```sparql
PREFIX : <https://example.org/default#>
SELECT ?user ?hobby WHERE {
  ?user :hobby ?hobby .
}
```

## Change 2: Turn hobby literals into nodes

The second change gives each hobby its own node, so it can later carry additional information. The hobby names stay the same:

```turtle
:user :hobbyItem :gardening, :hiking .
:gardening :name "Gardening" .
:hiking :name "Hiking" .
```

The query follows two predicates instead of one:

```sparql
PREFIX : <https://example.org/default#>
SELECT ?user ?hobby WHERE {
  ?user :hobbyItem ?hobbyNode .
  ?hobbyNode :name ?hobby .
}
```

All three query/snapshot pairs produce the same table. The app's recommendation logic stays unchanged:

| user | hobby |
| --- | --- |
| `:user` | `"Gardening"` |
| `:user` | `"Hiking"` |

## Change 3: Replace hobbies with skills

Suppose the graph now records only skills and removes the hobby relationships:

```turtle
:user :hasSkill :gardening, :hiking .
:gardening :name "Gardening" .
:hiking :name "Hiking" .
```

Replacing `:hobbyItem` with `:hasSkill` in the query would return the same table for this snapshot, but its meaning would change. Knowing how to garden does not mean treating gardening as a hobby. The graph no longer records which skills are also hobbies, and a query cannot recover that information.

Automatic adaptation must stop here: the app needs hobby information restored, or its recommendation logic must change to use skills. The notification could warn: “Meaning has changed beyond a rename or shallow restructuring. Skills do not identify hobbies, so we cannot preserve your query's meaning automatically. Please review the application's assumptions.”

## Announcing changes

Before applying Change 2, a migration service could notify affected applications like this (using an illustrative vocabulary):

```turtle
@prefix : <https://example.org/default#> .

:change2 a :VocabularyChange ;
    :status :Proposed ;
    :message "Hobbies will become nodes; their names stay the same." ;
    :affectedPredicate :hobby ;
    :replacementPath ( :hobbyItem :name ) ;
    :compatibility "Preserves hobby strings if each user/hobby name has one node." .
```

The migration service's implementation and communication channels would still need to be defined—for example, an SDK integration, webhooks or a polling endpoint. Query adaptations would be checked, approved when needed and activated alongside the graph change, with the old state available during transition.

[LDES](https://w3id.org/ldes/specification) could be used to publish these notifications as a linked data event stream.

## Limits

Even valid rewrites can accumulate complexity: this buffer buys time before application changes, not permanent compatibility.
