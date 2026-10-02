# Vocabulary Evolution Buffer

*Adapting queries as getters so applications can migrate at their own pace.*

The core idea is **query = getter = buffer**: adapting queries (e.g. SPARQL SELECT) can shield an application's internal model from otherwise breaking changes in an evolving knowledge graph, preserving the structure and meaning the application expects.

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

The KG host has no access to applications' queries or internal logic, but could publish structured change notices a few days before they take effect. For Change 2, a notice could look like this (using an illustrative vocabulary):

```turtle
@prefix : <https://example.org/default#> .

:change2 a :VocabularyChange ;
    :status :Proposed ;
    :message "Hobbies will become nodes; their names stay the same." ;
    :affectedPredicate :hobby ;
    :replacementPath ( :hobbyItem :name ) ;
    :compatibility "Preserves hobby strings if each user/hobby name has one node." .
```

Notices could include transformers that run on the application side and propose query edits—for Change 1, “replace `:hasHobby` with `:hobby`.” Developers could accept an edit with one click, or opt into automatic adoption of simple, meaning-preserving changes. Adapted queries would take effect with the matching graph change.

Without queries that such transformers can adapt, the notice still provides advance warning: “Update how your application retrieves this data before the change takes effect.” The migration service and delivery channels remain to be defined; SDKs could receive notices automatically, and [LDES](https://w3id.org/ldes/specification) could publish them as a linked data event stream.

## Limits

Even valid rewrites can accumulate complexity: this buffer buys time before application changes, not permanent compatibility.
