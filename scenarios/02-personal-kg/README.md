# Scenario 2: Personal Knowledge Graph

This scenario is a personal assistant app that continuously builds a knowledge graph based on user input. The user adds content by writing or speaking short messages to the app. An LLM extracts entities and relationships and builds up the knowledge graph. With each new message, the structure of the graph might change to better accommodate the overall knowledge landscape. From all scenarios, this might be the clearest one to work out what evolvable semantic infrastructures could be: everything gets built up dynamically from scratch. There are no initial requirements. The user input dictates the flow of graph construction.

The following flow is adapted from a ChatGPT conversation about this scenario. ChatGPT also nicely added OWL modeling in each step – I am omitting this here for brevity. It shows how each new user input adds to the knowledge graph or reorganizes it because a "better" schema has surfaced. Each modeling choice could, of course, be done differently. The big question here is how to measure "better".

Prefixes used throughout:
```turtle
@prefix rdfs: <http://www.w3.org/2000/01/rdf-schema#> .
@prefix xsd:  <http://www.w3.org/2001/XMLSchema#> .
@prefix : <https://evo-sem-inf.org/default#> .
```

---

### Step 1

&rarr; User input: `Remember: send the invoice today.`

Current "mental model":

```turtle
:invoiceTask a :Task ;
    :text "Send the invoice" ;
    :status :Open .
```

The model is minimal. There are tasks with text and status. Everything is flat.

---

### Step 2

&rarr; User input: `Send it before 11.`

```turtle
:invoiceTask a :Task ;
    :text "Send the invoice" ;
    :status :Open ;
    :hasConstraint :c1 .

:c1 a :TimeConstraint ;
    :latestTime "11:00:00"^^xsd:time .
```

Time becomes part of the structure. The invoice task now has an explicit constraint node.

---

### Step 3

&rarr; User input: `Team sync at 10 with Moritz — talk about the proposal.`

```turtle
:Moritz a :Person ; rdfs:label "Moritz" .

:teamSync a :Event ;
    :time "10:00:00"^^xsd:time ;
    :hasParticipant :Moritz ;
    :hasAgendaItem :proposalItem .

:proposalItem a :AgendaItem ;
    :text "Talk about the proposal" ;
    :status :Open .

:invoiceTask a :AgendaItem ;
    :text "Send the invoice" ;
    :status :Open ;
    :hasConstraint :c1 .

:c1 a :TimeConstraint ;
    :latestTime "11:00:00"^^xsd:time .
```

The model now includes events and agenda items. The invoice task is reinterpreted as an agenda item. The structure expands.

---

### Step 4

&rarr; User input: `Pick up Felix from Kita. If traffic is bad, text grandma.`

```turtle
# previous triples stay the same, new triples:

:Felix a :Person ; rdfs:label "Felix" .
:Grandmother a :Person ; rdfs:label "Grandma" .
:Kita a :Place ; rdfs:label "Kita" .

:pickup a :AgendaItem ;
    :text "Pick up Felix" ;
    :status :Open ;
    :involves :Felix ;
    :at :Kita ;
    :hasContingency :trafficPlan .

:trafficPlan a :Contingency ;
    :when :trafficBad ;
    :thenDo :textGrandmother .

:trafficBad a :Condition ; rdfs:label "Traffic is bad" .

:textGrandmother a :AgendaItem ;
    :text "Text Grandma" ;
    :status :Open ;
    :involves :Grandmother .
```

The topic shifts from work to family, but the same planning patterns still apply. The graph represents a main action plus a conditional follow-up, with people and places attached.

---

### Step 5

&rarr; User input: `Went running. Felt stressed about the invoice. Running helps.`

```turtle
# previous triples stay the same, new triples:

:running a :Event ;
    rdfs:label "Running" ;
    :reduces :stress .

:stress a :State ;
    rdfs:label "Stressed" ;
    :about :invoiceTask .

:invoiceTask :affects :stress .
```

The graph now includes info about the internal state of the user. An earlier commitment (the invoice) is linked to stress, and an event (running) is linked as something that reduces it. 

---

The graph has shifted from representing isolated tasks to representing relationships between experiences, commitments, and internal states.
