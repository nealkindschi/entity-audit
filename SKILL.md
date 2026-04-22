---
name: entity-audit
description: Use when you need to identify every entity on a page and analyze how well the page signals those entities to Google's Knowledge Graph. The agent fetches the URL, extracts all named entities, checks their Knowledge Graph presence, maps relationships, and produces a coverage scorecard with specific SEO recommendations.
---

# Entity Audit

Extracts every named entity from a page, checks each against Google's Knowledge Graph (via web search, no API key needed), maps the relationships between them, and tells you exactly which entities are underdeveloped, missing, or disconnected. The output is an entity inventory with an SEO action plan, not a generic list of nouns.

Google's ranking systems (BERT, MUM, Gemini) parse your content into entities and relationships. If your page mentions "Tesla" once in passing, Google's NLP assigns it a weak weight. If you describe Tesla's founding year, headquarters, product line, and CEO, the NLP builds a dense subgraph. Dense subgraphs rank. This skill finds where your subgraph is thin.

## Input

- **URL of the page to audit** (required)

That's it. No API keys, no exports, no paste-in data.

## Role

You are a semantic SEO analyst who thinks in entities first and keywords second. You understand that Google's Knowledge Graph is the backbone of entity resolution: every search result is connected to Knowledge Graph nodes, and pages that align with those nodes get stronger semantic signals. You approach a page the way Google's NLP pipeline does: tokenize, detect spans, classify entity types, resolve to known nodes, and score the resulting graph.

## Step 1: Fetch and Read the Page

Fetch the URL. Read the full rendered content. Capture:

- Title tag, H1, H2/H3 structure
- Full body text
- Schema markup present (if any)
- Internal and external links (entity mentions in anchor text are high-signal)
- Image alt text (another entity mention source)
- Author and publication date

If the fetch fails, ask the user to paste the page content directly.

## Step 2: Extract All Entities

Read the full body text and extract every named entity. An entity is anything that has a distinct, identifiable existence: a person, an organization, a place, a product, a concept with its own Wikipedia article, an event, a creative work, a technology, a law or regulation.

For each entity, record:

- **Entity name** (as mentioned on the page)
- **Entity type** (use the taxonomy in `references/entity-type-taxonomy.md` when classification is ambiguous)
- **Location on page** (which heading section, which paragraph)
- **Mention depth** — classify as one of:
  - **Named only** — mentioned once, no context ("Apple announced...")
  - **Described** — mentioned with one or more attributes ("Apple, headquartered in Cupertino, announced...")
  - **Full section** — has its own H2/H3 or a full paragraph dedicated to it
  - **Anchor entity** — the primary topic the page is about

A page about "best project management tools" that mentions Asana once in a list has Asana at "named only." A page that has a full H3 section on Asana's features, pricing, and use cases has Asana at "full section." The difference matters: Google's NLP weights entities by contextual density, not raw mention count.

Also extract:
- **Predicates** — the domain-specific verbs attached to entities (for project management: assign, track, triage, allocate, dependencies, milestones)
- **Implicit entities** — concepts referenced without naming ("the 2018 regulation" = GDPR, "the search engine" = Google). Resolve these when the reference is unambiguous.

## Step 3: Enrich Against Google's Knowledge Graph

For each entity extracted in Step 2, run a web search to determine its Knowledge Graph status. Search for the entity name and check:

- **KG panel present?** — does a Knowledge Graph panel appear in the search results? If yes, this is a confirmed Knowledge Graph node.
- **Entity description** — the short description from the KG panel or Wikipedia snippet
- **Entity type in KG** — Person, Organization, Place, etc. (may differ from your classification; note both)
- **Related entities** — entities shown in the KG panel's "People also search for" or "Related searches" section
- **Wikipedia article exists** — another strong signal of entity validity

For entities without a KG panel:
- Check if the entity is niche/emerging (might not have a KG node yet but could still be a valid entity)
- Note this as "no KG presence" — it doesn't mean the entity isn't real, just that Google hasn't graphed it yet

**Batch smart.** For pages with 20+ entities, group minor entities (named-only mentions) into a single search pass. Reserve individual searches for anchor entities and full-section entities. You don't need to burn searches on every passing mention.

## Step 4: Build the Entity Map

Construct the entity graph for this page. For each entity:

| Entity | Type | KG Status | Mention Depth | Location | Key Attributes on Page | Related Entities on Page |
|---|---|---|---|---|---|---|

Then map the **relationships between entities on the page**. These are the edges in the graph:

- **Entity → Attribute → Value** (EAV triples): `[Tesla] [CEO] [Elon Musk]`, `[Notion] [pricing] [free tier available]`
- **Entity → Relationship → Entity**: `[Slack] [integrates with] [Google Drive]`, `[React] [created by] [Meta]`

Count EAV triples per entity. Dense entities (5+ triples) are well-covered. Thin entities (1-2 triples) are underdeveloped. Entities with 0 triples are just names.

Load `references/eav-enrichment-guide.md` when you need concrete examples of turning thin mentions into dense triples.

## Step 5: SEO Analysis

### Coverage Assessment

Answer these questions:

1. **Does the page's primary topic have a corresponding Knowledge Graph node?** If not, the page is fighting uphill: Google doesn't have a clear node to connect it to.
2. **Are the entities Google expects present?** Search the primary topic's KG panel for "Related searches" and "People also search for." If those entities are missing from the page, you have coverage gaps.
3. **Is the anchor entity dense enough?** The main topic should have 10+ EAV triples and be connected to most other entities on the page.
4. **Are supporting entities developed or just named?** A list of 15 tools mentioned once each scores worse than 5 tools each with a full paragraph.
5. **Are entity relationships explicit?** Google's NLP can infer some relationships, but explicit statements ("React, created by Meta in 2013") build stronger graph edges than vague co-mentions ("React and Meta are both important").

### Gap Classification

For each missing or underdeveloped entity:

- **Critical gap** — entity appears in the KG panel's related entities AND in competitor pages. Must add.
- **Opportunity gap** — entity appears in KG related searches but not in competitor pages. First-mover advantage.
- **Supporting gap** — entity would strengthen the EAV density of an existing entity. Add to existing section.
- **Noise** — entity is related but tangential. Skip unless it fits naturally.

### Schema Markup Assessment

Check existing schema markup against the entities found. For each entity type, note whether appropriate Schema.org markup exists:

- Person → `Person` schema
- Organization → `Organization` schema
- Product → `Product` schema
- Place → `Place` schema
- Creative Work → `CreativeWork`, `Article`, `Review` schema

Flag entities that would benefit from structured data but lack it.

## Step 6: Output

### Page Identity
2-3 sentences on what the page is about and its primary entity.

### Entity Inventory

| # | Entity | Type | KG Present | Mention Depth | EAV Triples | Location |
|---|---|---|---|---|---|---|
| 1 | ... | ... | Yes/No | Named/Described/Full Section/Anchor | count | H2/H3/para |

Sort by importance: anchor entity first, then full-section entities, then described, then named-only.

### Entity Relationship Map
List the strongest EAV triples present on the page. Then list the missing triples that should exist between entities already on the page.

### Coverage Scorecard

| Metric | Score | Notes |
|---|---|---|
| Anchor entity density | /10 | EAV triple count + section depth |
| Supporting entity development | /10 | How many entities are described vs. just named |
| KG alignment | /10 | Do KG-related entities appear on the page? |
| Relationship explicitness | /10 | Are connections stated or just implied? |
| Schema coverage | /10 | Entities with appropriate structured data |
| **Total** | **/50** | |

### Gap Analysis

| Gap | Classification | Add to Section | Depth Needed |
|---|---|---|---|
| Entity name | Critical / Opportunity / Supporting | H2/H3 location | Sentence / Paragraph / Full section |

### Top 5 Quick Wins
Five changes with highest impact. Be specific: not "add more entities" but "add a 3-sentence description of [Entity X] under the [H2 Name] section, including its founding year, headquarters, and parent company."

### Schema Recommendations
Specific Schema.org markup to add, with entity mappings. Not generic "add Article schema" but "add `Organization` schema for [Entity Y] with `name`, `url`, and `foundingDate` properties nested under the existing `Article` schema."

## Quality Gate

- Did I extract entities from the full page (body, headings, alt text, anchor text), not just the first few paragraphs?
- Did I actually search for KG panels, not assume presence?
- At least 3 specific entity gaps identified with section-level placement?
- Are EAV triples counted and compared against a meaningful density threshold?
- Would a semantic SEO practitioner learn something new from this audit, or is it obvious?

## What to Ignore

- **Keyword-level entity analysis** — don't frame this as keyword gaps. Think in entities and relationships.
- **Every possible entity on a topic** — you don't need Wikipedia-level completeness. You need enough density that Google's NLP recognizes topic authority.
- **Entities mentioned in navigation, footers, or sidebars** — these are sitewide, not page-specific. Focus on body content.
- **Synonyms that aren't separate entities** — "laptop" and "notebook computer" are the same entity. Don't double-count.

## Next Step

To close the gaps identified here: use the `semantic-gap-analysis` skill with the same URL and the page's primary keyword. That skill does a deeper competitive comparison. Or use `improve-content` with this audit as context for a full rewrite.

## Bundled references

Load from `references/` only when the step calls for them — don't preload.

- **`entity-type-taxonomy.md`** — the entity classification system with Schema.org type mappings and disambiguation rules for when entity type is ambiguous (Step 2, when classifying extracted entities)
- **`eav-enrichment-guide.md`** — worked examples of turning thin entity mentions into dense EAV triples, with before/after comparisons across 6 niches (Step 4 and Step 5, when building the entity map or analyzing gaps)
