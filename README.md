# entity-audit

A standalone skill that audits any URL for entity coverage and semantic SEO signals. No API keys required.

## What it does

1. Fetches the page and extracts every named entity (people, organizations, technologies, concepts, products, events)
2. Checks each entity against Google's Knowledge Graph via web search
3. Maps entity relationships (EAV triples) and scores density
4. Produces a coverage scorecard (/50), gap analysis, quick wins, and schema recommendations

## Install

Copy the `skills/` folder into your skills directory:

```bash
cp -r skills/entity-audit ~/.claude/skills/
```

Or symlink:

```bash
ln -s "$(pwd)/skills/entity-audit" ~/.claude/skills/entity-audit
```

## Usage

```
run entity-audit on https://example.com/your-page
```

## Structure

```
skills/
  entity-audit/
    SKILL.md                           # Main skill prompt
    references/
      entity-type-taxonomy.md          # Entity classification system
      eav-enrichment-guide.md          # How to turn thin mentions into dense triples
```

## License

Apache License 2.0
