# Entity Type Taxonomy

The entity-audit skill loads this when classifying extracted entities (Step 2). Use this to resolve ambiguous cases where an entity could fit multiple types.

## Primary entity types

These are the types Google's Knowledge Graph and Schema.org recognize. Map every extracted entity to one primary type. If an entity fits two (e.g., a person who is also an organization founder), pick the type that matches how the entity is used **on this specific page**.

| Type | Definition | Schema.org mapping | KG signals |
|---|---|---|---|
| **Person** | A real or fictional individual | `Person` | Wikipedia article, social profiles, KG panel with birth date, occupation |
| **Organization** | A company, institution, agency, or group | `Organization`, `Corporation`, `GovernmentOrganization`, `NGO` | Wikipedia article, official website, KG panel with founded date, headquarters |
| **Place** | A geographic location: city, country, building, landmark | `Place`, `City`, `Country`, `LandmarksOrHistoricalBuildings` | Wikipedia article, maps listing, KG panel with coordinates |
| **Product** | A tangible product or software product | `Product`, `SoftwareApplication` | Product page, reviews, KG panel with price range, manufacturer |
| **Concept** | An abstract idea with its own identity: a methodology, a scientific theory, a legal framework, a technology | `Thing` (fallback), `DefinedTerm` | Wikipedia article, academic citations, KG panel with definition |
| **Event** | A time-bound occurrence: conference, war, product launch, legislation | `Event` | Wikipedia article, date range, KG panel with date and location |
| **Creative Work** | A book, article, film, song, software project, dataset | `CreativeWork`, `Article`, `Book`, `Movie`, `MusicRecording`, `SoftwareSourceCode` | Wikipedia article, catalog listing, KG panel with author/creator and date |
| **Technology** | A specific technology, framework, programming language, protocol | `SoftwareApplication`, `Thing` | GitHub repo, documentation site, Wikipedia article, version history |
| **Law / Regulation** | A named piece of legislation or regulatory framework | `Legislation` | Wikipedia article, government source, effective date |
| **Quantity / Measurement** | A specific numeric value with units that carries semantic meaning | `QuantitativeValue` | Not a KG entity on its own, but becomes one when attached to an entity as an attribute value |

## Disambiguation rules

When an entity fits multiple types, apply these rules in order:

**1. Follow the page's usage context.**

"Apple" on a page about tech companies = Organization. "Apple" on a page about fruit = Concept/Product. The same word is a different entity depending on context. Always resolve based on how the page uses it.

**2. Prefer the more specific type.**

"React" could be a Technology or a Creative Work (open-source software). Technology is more specific and maps to `SoftwareApplication` in Schema.org. Use Technology.

**3. When in doubt, check the KG panel.**

If a KG panel exists for the entity, the panel's stated type takes precedence. Google has already classified it; align with their classification.

**4. Entities without KG presence default to Concept.**

If no KG panel exists and the entity doesn't clearly fit another type, classify as Concept. You can always refine later.

## Sub-entity detection

Some entities contain sub-entities. A page about "AWS" might mention "S3", "Lambda", and "EC2." Each of these is its own entity, not just a feature of AWS. Extract them separately if:

- The sub-entity has its own Wikipedia article or KG panel
- The page devotes a full sentence or more to describing it
- It has distinct attributes (pricing, release date, specifications) separate from the parent entity

Do not extract as separate entities if:
- The sub-entity is mentioned only in a list without any individual description
- It has no independent identity outside the parent (a feature setting, not a product)

## Implicit entity resolution

Pages sometimes reference entities without naming them. Resolve these when the reference is unambiguous:

| Implicit reference | Resolved entity | Confidence |
|---|---|---|
| "the search engine" | Google | High (in SEO context) |
| "the 2018 privacy regulation" | GDPR | High |
| "the iPhone maker" | Apple | High |
| "the social network" | Facebook / Meta | Context-dependent |
| "the cloud provider" | AWS / Azure / GCP | Context-dependent |
| "the framework" | [depends on page topic] | Low without more context |

Only resolve when confidence is high. Mark low-confidence resolutions as `(unresolved)` and note them in the output. Google's NLP does the same: it disambiguates when confident and skips when not.

## Entity density benchmarks

Use these when scoring coverage in the SEO analysis (Step 5):

| Page type | Expected entity count | EAV triples per entity (anchor) | EAV triples per entity (supporting) |
|---|---|---|---|
| Pillar / guide | 15-30 | 10+ | 3+ |
| Product review | 8-15 | 8+ | 4+ |
| Comparison | 10-20 | 5+ | 5+ |
| How-to | 5-12 | 5+ | 2+ |
| News / editorial | 8-20 | 3+ | 2+ |
| Landing page | 3-8 | 3+ | 1+ |

These are rough targets, not hard rules. A page with fewer entities but denser coverage can outrank a page with more entities but thin coverage. Density beats count.
