# EAV Enrichment Guide

The entity-audit skill loads this when building the entity map (Step 4) or analyzing gaps (Step 5). Use this to turn thin entity mentions into dense Entity-Attribute-Value triples that strengthen semantic signals.

## What an EAV triple is

An EAV triple is a structured fact: **[Entity] has [Attribute] with [Value]**.

- `[Stripe] [pricing model] [usage-based at 2.9% + 30c per transaction]`
- `[Kubernetes] [initial release] [June 2014]`
- `[Tesla Model 3] [battery range] [358 miles (Long Range)]`

Google's NLP extracts these from your text. More triples = denser semantic graph = stronger topical authority signal.

## The density problem

Most pages mention entities without giving them attributes or values. Here is what that looks like:

**Thin (1 EAV triple, scores ~3-4):**
> Notion is a popular project management tool that teams use for collaboration.

NLP extracts: `[Notion] [type] [project management tool]`. That's one triple. Google knows you mentioned Notion but has no idea what you actually know about it.

**Dense (8+ EAV triples, scores ~8-9):**
> Notion, founded in 2016 by Ivan Zhao and Simon Last, is a workspace platform headquartered in San Francisco. The free tier supports up to 10 guests, while the Team plan starts at $10/user/month. Notion uses a block-based editor with over 50 block types, supports relational databases, and integrates with Slack, GitHub, and Figma. Unlike traditional project management tools like Asana or Monday.com, Notion positions itself as an all-in-one workspace combining notes, databases, and wikis.

NLP extracts:
- `[Notion] [founded] [2016]`
- `[Notion] [founders] [Ivan Zhao, Simon Last]`
- `[Notion] [type] [workspace platform]`
- `[Notion] [headquarters] [San Francisco]`
- `[Notion] [free tier limit] [10 guests]`
- `[Notion] [team plan price] [$10/user/month]`
- `[Notion] [editor type] [block-based, 50+ block types]`
- `[Notion] [integrations] [Slack, GitHub, Figma]`
- `[Notion] [competes with] [Asana, Monday.com]`

Nine triples from one paragraph. Google's NLP reads this and builds a dense subgraph for the Notion node. That subgraph signals topic authority.

## Before/after: 6 niches

### SaaS product comparison

**Before (thin, named-only):**
> Other options include Jira, Linear, and Basecamp.

**After (dense, described):**
> Jira, developed by Atlassian and launched in 2002, dominates enterprise issue tracking with its customizable workflows and Jira Query Language (JQL). Linear, founded in 2019 by Karri Saarinen, targets modern product teams with a keyboard-driven interface and sub-50ms performance. Basecamp, created by 37signals (now Basecamp) in 2004, takes a opinionated approach: no Gantt charts, no complexity, just message boards, to-do lists, and schedules.

Added triples per entity: Jira (4), Linear (4), Basecamp (4). Total: 12 triples from three sentences.

### Health / medical

**Before (thin):**
> Common treatments include metformin and insulin therapy.

**After (dense):**
> Metformin, an oral biguanide approved by the FDA in 1994 for type 2 diabetes, works by reducing hepatic glucose production and improving insulin sensitivity. The standard starting dose is 500mg twice daily, titrated up to a maximum of 2550mg/day. Insulin therapy, introduced clinically in 1922 following Banting and Best's discovery, remains the primary treatment for type 1 diabetes and is added for type 2 patients when oral agents fail to maintain HbA1c below 7%.

Added triples: Metformin (6), Insulin therapy (4). Total: 10 triples.

### Finance / investing

**Before (thin):**
> Index funds from Vanguard and Fidelity are popular choices.

**After (dense):**
> Vanguard, founded by John C. Bogle in 1975, pioneered the index fund for retail investors with the Vanguard 500 Index Fund (VFINX), which tracks the S&P 500 with an expense ratio of 0.04%. Fidelity, established in 1946 and headquartered in Boston, introduced zero-expense-ratio index funds in 2018 with FZROX (total market) and FZILX (international). The key difference: Vanguard's investor-owned structure aligns fund performance with shareholder returns, while Fidelity uses index funds as loss leaders for its brokerage platform.

Added triples: Vanguard (5), Fidelity (5), VFINX (2), FZROX (1). Total: 13 triples.

### Technology / developer tools

**Before (thin):**
> Popular frameworks include React and Vue.

**After (dense):**
> React, created by Jordan Walke at Meta (then Facebook) and open-sourced in 2013, uses a virtual DOM and one-way data flow with a component model built around JSX. Vue, created by Evan You in 2014 after leaving Google, combines React's component architecture with Angular's template syntax while keeping a 33KB gzipped bundle size. React dominates in the Stack Overflow survey (40.6% usage in 2024) and has the larger ecosystem, but Vue's gentler learning curve and built-in state management (Pinia) make it the faster choice for small-to-mid applications.

Added triples: React (6), Vue (5). Total: 11 triples.

### Travel / destinations

**Before (thin):**
> Tokyo has great food and temples.

**After (dense):**
> Tokyo, Japan's capital since 1868 and the world's most populous metropolitan area (37.4 million residents), spans 2,194 km² across 23 special wards. The city operates the world's busiest train system (13 million daily riders on JR East alone) and houses 263 Michelin-starred restaurants, more than any other city. Key districts include Shinjuku (commercial hub, world's busiest railway station with 3.6M daily passengers), Shibuya (youth culture, the famous scramble crossing), and Asakusa (historic quarter, Senso-ji temple founded in 628 AD).

Added triples: Tokyo (7), Shinjuku (2), Shibuya (1), Asakusa (1), Senso-ji (1). Total: 12 triples.

### Legal / regulation

**Before (thin):**
> Companies must comply with GDPR and CCPA.

**After (dense):**
> GDPR (General Data Protection Regulation), effective May 25, 2018, applies to any organization processing data of EU residents regardless of the organization's location. Maximum penalties reach 4% of global annual revenue or €20 million, whichever is higher. CCPA (California Consumer Privacy Act), effective January 1, 2020, applies to businesses with over $25M annual revenue or data on 100,000+ consumers, with penalties of $2,500 per unintentional violation and $7,500 per intentional violation. The key difference: GDPR requires opt-in consent before data collection; CCPA allows collection by default with an opt-out right.

Added triples: GDPR (5), CCPA (5). Total: 10 triples.

## How to enrich: the expansion process

When the audit finds a thin entity (1-2 EAV triples), expand it using this sequence:

1. **Founding / origin facts** — when was it created, by whom, where
2. **Defining attributes** — what makes it distinct from peers (price, size, speed, methodology)
3. **Quantified specifications** — numbers, measurements, thresholds, limits
4. **Relationships to other entities on the page** — competes with, integrates with, replaces, depends on
5. **Notable events or milestones** — launches, acquisitions, version releases, legal rulings

Not every entity needs all five. Anchor entities should hit all five. Supporting entities should hit 2-3. Named-only entities that you're enriching should hit at least 2.

## When not to enrich

Don't expand every entity on the page. Enrichment is for entities that:

- The SEO analysis flagged as critical or opportunity gaps
- Are central to the page's topic but have fewer than 3 EAV triples
- Appear in the KG panel's related entities but are underdeveloped on the page

Padding every entity with triples is just as bad as having none. Dense triples where they matter beat thin triples everywhere.
