# Scenario 1: Apps share a Pod

This scenario assumes a "citizen pod" that a growing number of apps and services from a city are using to provide personalized services. We don't know in advance which apps will join and what type of user data they each need. Some might come with a vocabulary/schemata already, others might be open to developing this as they join the ecosystem. The ones with existing schemata might have an internal data model underlying where the datafield vocabulary stems from, others might already be oriented towards reusing vocabularies or ontologies out there.

## App 1: Library app

The first app is a library that uses the citizen pod to store personal data like the lending history, preferred genres and hobbies of the user. The library is using this for personalized recommendations.

```turtle
@prefix xsd: <http://www.w3.org/2001/XMLSchema#> .
@prefix foaf: <http://xmlns.com/foaf/0.1/> .
@prefix schema: <http://schema.org/> .
@prefix : <https://evo-sem-inf.org/default#> .

:user a schema:Person ;
    foaf:name "Martina Musterfrau" ;
    schema:email "martina@example.com" ;
    schema:memberOf :cityLibrary1 ;
    schema:knowsLanguage "de", "en" ;
    :interestedInGenre "Science Fiction", "Urban Gardening", "Bavarian History" ;
    :hasHobby "Gardening", "Hiking" ;
    :borrowedItem :lendingEvent1 .

:lendingEvent1 a schema:LendAction ;
    schema:object :bookDune ;
    schema:startTime "2025-11-10"^^xsd:date ;
    schema:endTime "2025-12-01"^^xsd:date ;
    schema:actionStatus schema:CompletedActionStatus .

:bookDune a schema:Book ;
    schema:isbn "9780441172719" ;
    schema:name "Dune" ;
    schema:author "Frank Herbert" .
```

## App 2: Service finder

The second app joining the ecosystem is the service finder of the city where citizens can find services like change of address or register a dog. The service finder uses the personal data from the pod to sort the available services by relevance. 

```turtle
@prefix foaf: <http://xmlns.com/foaf/0.1/> .
@prefix schema: <http://schema.org/> .
@prefix xsd: <http://www.w3.org/2001/XMLSchema#> .
@prefix : <https://evo-sem-inf.org/default#> .

:user a schema:Person ;
    foaf:firstName "Martina" ;
    foaf:lastName "Musterfrau" ;
    schema:address "Beispielweg 3, 12345 Musterstadt" ;
    :hasMonthlyBruttoIncome 4000 ;
    :hasChild :child1 ;
    :hasPet :dog1 .

:child1 a :Child ;
    schema:birthDate "2017-04-03"^^xsd:date ;
    :hasGender schema:Female .

:dog1 a :Pet ;
    :petType "Dog" ;
    :isRegistered false .
```

## App 3: Lifelong Learning Centre (Volkshochschule)

The third app is the Lifelong Learning Centre of the city (Volkshochschule) where adults can find educational courses. The pod data is used to surface relevant offers.

```turtle
@prefix rdfs: <http://www.w3.org/2000/01/rdf-schema#> .
@prefix foaf: <http://xmlns.com/foaf/0.1/> .
@prefix schema: <http://schema.org/> .
@prefix : <https://evo-sem-inf.org/default#> .

:user a schema:Person ;
    :hasSkill :enLangSkill , :deLangSkill , :gardeningSkill ;
    :wouldJoinLanguageTandem true ;
    :hasDegree "B.Sc." ;
    :ownsDog true .

:enLangSkill a :Skill ;
    rdfs:label "English" ;
    :level "Advanced" ;
    :cerfLevel: "B1" ;
    :wantsToImprove true .

:deLangSkill a :Skill ;
    rdfs:label "German" ;
    :level "Native" ;
    :cerfLevel: "C2" ;
    :wantsToImprove false .

:gardeningSkill a :Skill ;
    rdfs:label "Gardening" ;
    :level "Beginner" .
```

## Vocabulary evolution pressure

- App 1 uses `foaf:name` whereas app 2 splits it into `foaf:firstName` and `foaf:lastName`. For most cases this would be easy to transform both ways, but there will be ambiguous cases with second names and stuff.
- App 3 only asks if the user has a dog or not (to recommend courses for dog-owners) whereas app 2 talks about `:Pet` individuals of different types. This should be easy to unify or bridge.
- App 3 uses `:hasSkill` that points to `:Skill` individuals that seem to cover both `:hasHobby` from app 1 as well as `schema:knowsLanguage`. Can those concepts be unified or at least bridged?

### Evolved vocabulary

_TODO_

### More apps and services that could join

- kita finder
- events
- job board
- resources for:
  - international newcomers
  - students
  - parents
- ...
