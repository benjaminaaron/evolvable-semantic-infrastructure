# Evolvable Semantic Infrastructure

This repository is a practical prototyping playground for what "evolvable semantic infrastructure" could look like. The exploration revolves around three interconnected topics: shared vocabulary development, decoupling processing logic and sliding level of details.


### Shared vocabulary development
In various scenarios it would be beneficial to share common vocabulary across multiple domains. Rather than mainly designing this initially and then patching it up along the way, we want to explore what a truly dynamic vocabulary development process could look like. One that avoids path dependencies and workarounds and embraces "breaking changes" as the cost for working towards the most salient, compact and elegant map of any territory.

Can information- and graph theory help with mathematical instruments? Are we after the model with the least "friction" or the most compressible one? To which degree can this problem be approached algorithmically, given that machines do not "understand" the meaning of vocabulary terms and how they are connected? However, isn't semantic modeling precisely what enables machines to compute meaning? Can an LLM generate suggestions for better "mental models" and symbolic math then judge them?

What does an infrastructure look like that doesn’t offload the burden of breaking changes to the participating apps? Can change itself semantically be modeled in a way that its impact can be tracked and measured and apps can automatically adjust?

### Decoupling processing logic
Web decentralization projects like Solid decouple (user) data from apps. For App A to ask for the dietary preferences of a user and App B to take that info from the Solid pod without having to also ask, they need to share the same vocabulary. Decoupling also the processing logic of apps goes one step further and lets apps say: "We are not storing your user data. Furthermore, we are not even asking for it from your pod to process it on our own servers. Instead, we are passing the processing logic to you so that you can run it on your trusted system (e.g., a sidecar to the Solid pod)." Optionally, the results can be passed back to the app for display.

If both the vocabulary and the processing logic operate on the same semantic model, maybe the notion of breaking changes could be reconceptualized entirely? For example, maybe what's stable are discovery-queries instead of static identifiers?

### Sliding level of details

Depending on the scenario, there is a degree of granularity that can be chosen in the way vocabulary terms are modeled. In the Law-as-Code domain, for instance, one might think of this as a sliding level of detail: is the person an adult or not / what is their age / when is their birthday. What does a modeling approach look like that allows the (automatic) sliding along such levels of details, based on an appropriate calibration level in a given context?


### Possibly relevant fields to look into

- Information theory
- Graph theory
- Causality algorithms
- Model alignment (`owl:equivalentClass`, ...)
- The adjacent possible (Stuart Kauffman)
- Category theory
- Concept formation
- Ontology-based data integration (Micro-Ontologies with bridges)
- Ontology Design Patterns
- Minimum Description Length (MDL)
- ... ?

### Outcome

The outcomes of this work will be open and reusable software artifacts, accompanied by scholarly publications that analyze, formalize and evaluate the insights gained. It could become a tool that can be "dropped" in any situation of heterogeneous data sources and overlaying schemata - there it starts mapping the current situation, makes all the issues explicit (and thus measurable) and works towards the most meaningful and salient model.


## Scenarios

Scenarios are used to ground the abstract concepts and ideas in something practical to work with. They are like stories that highlight various aspects of evolvable semantic infrastructure.

So far there are two stories in `scenarios/` that are a bit fleshed out, yet processable artifacts are missing for them.

Additional scenarios could be, for instance:
- `federate-and-unify`: multiple heterogeneous data sources are federated into a single emerging semantic model
- `open-data-enrichment`: an Open Data Portal is creating mappings between tabular data to RDF at scale – it's crucial to keep a handle on the mappings by focusing on both individual correctness and a sensible overall use of external vocabularies and their respective "mental models"
- `challenges-in-one-domain`: also just one domain can run into various challenges of semantic modeling, it might be worth fleshing that out in a scenario
- `city-statistics`: change from yearly district snapshots of measured data (like births or air quality) to a model allowing various time spans like yearly, quarterly and up to real-time

Many more scenarios and different stories within. Maybe they can be plotted in 2D or 3D to categorize them?


## Open questions and notes

An unsorted, incomplete and under-commented list of open questions and notes:

- Evolution of explicit schemata (ontologies) vs. knowledge graphs without an ontology
- Why would apps be willing to participate in a system where the vocabulary might change "underneath them"? 
- From a fully internal model with self-invented terms to maximal reuse of international vocabularies/ontologies - the whole spectrum should be supported
- Can we support non-semantic formats?
- Is the system allowed to optionally ask clarifying questions back to the user?
- Optimize for inferrability: ask for birthdate because we can calculate adult yes/no, age and if someone is pensionable
- Support someone deferring an answer for whatever reason
- Support someone saying "I already receive this benefit" &rarr; infer data points from that info
- How far back to we have to go to find the shared ancestor of these concepts
- Building bridges between pockets of local schemata
- Purely structural math-based computation first and then presenting outcomes to LLMs/humans who can "understand" meaning?
- Vocabulary reuse: cherry-pick single terms vs. getting pulled into that mental model and reusing more because it makes sense
- Will a set of basic building blocks emerge as we see semantic infrastructure evolve in various scenarios?
- What's the non-negotiable minimal structure that gets frozen while "everything else" can evolve? Triples?


## The invitation

If you remember modeling challenges that you ran into in your work, please consider sharing them! I would like to collect a variety of scenarios and stories to illuminate the problem space a bit wider. Write me an [email](mailto:benjamin.degenhart@foerderfunke.org), make a PR here, open an issue or let's set up a call!

Anything is interesting to me. Some types of challenges from the top of my head:

- Temporal granularity: e.g., having to change from a yearly measurement to a quarterly one
- Geographic boundary changes: e.g., districts get redrawn or postal codes change
- Unit or currency transitions
- Entity identities change: e.g., an organization gets renamed, merges or gets split up
- Expanding or shrinking categorization
- ... ?
