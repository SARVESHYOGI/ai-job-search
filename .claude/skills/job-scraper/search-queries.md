# Search Queries for Job Scraper

<!-- Populated by /setup (Section 9: Job Search Configuration). Re-run `/setup --section search` to update. -->

## Installed portal CLIs (primary for `/scrape`)

`/scrape` discovers every portal skill under `.agents/skills/*/SKILL.md` and runs its CLI first. Shipped country-agnostic CLIs include `linkedin-search` and `freehire-search`; Danish demos and any skill you add with `/add-portal` are included the same way. You do **not** need a matching `site:` line below for those CLIs to run.

The `site:` query templates in this file are the **WebSearch fallback** — for portals without a CLI, company career pages, or when a CLI fails.

**Language scope:** English is your professional working language and the language the Indian tech boards write postings in, so it leads every category. Hindi (native) and Marathi (conversational) variants are listed beneath each category — postings in those languages are in scope too. A posting requiring a language you have *not* declared, as a job condition, is excluded before scoring; a posting requiring a *higher level* than you declared in a language you *do* work in is flagged for your own judgment, not excluded — see `04-job-evaluation.md`'s Language Gate, the single source of truth for this rule.

## Search Sites

Primary (your market's job boards):
- **naukri.com** - India's largest general job board (no CLI shipped; scaffold one with `/add-portal`, WebSearch fallback until then)
- **linkedin.com/jobs** - LinkedIn job listings (filter: India / Mumbai); also covered by the `linkedin-search` CLI
- **indeed.co.in** - major India board (WebSearch fallback)
- **freehire.me** - country-agnostic tech aggregator; covered by the `freehire-search` CLI

Secondary (company career pages via Google):
- Direct Google searches with `site:` filters for known target companies

## Query Categories

Queries are grouped by priority. Write **each category in English with Hindi/Marathi variants beneath** (see Language scope above). Combine each query with your location terms (Mumbai, or "India"/"remote") where the site supports it.

**Organize by function, not job title.** The same underlying work carries different titles across companies and markets (a "Backend Engineer" role at one employer may be posted as "Software Engineer" or "SDE II" at another). Name each priority category after the function it covers, and list several plausible job titles as query variants within that category rather than betting an entire priority tier on one exact title string.

### Priority 1: Backend Engineering

Your strongest and most desired direction: Java/Spring Boot, distributed systems, REST APIs, event-driven messaging.

```
site:naukri.com "Backend Engineer" Mumbai
site:naukri.com "Software Engineer" Java Mumbai
site:linkedin.com/jobs "Backend Engineer" India
site:linkedin.com/jobs "Java Developer" Mumbai
"Spring Boot" developer India remote
"distributed systems" engineer India
```

Hindi / Marathi:
```
site:naukri.com "बैकएंड इंजीनियर" मुंबई
site:naukri.com "जावा डेवलपर" मुंबई
site:naukri.com "बॅकएंड इंजिनिअर" मुंबई
```

### Priority 2: AI / ML Engineering (RAG & LLM systems)

Your domain expertise: production RAG, LangGraph orchestration, multimodal document intelligence, LLM APIs.

```
site:naukri.com "AI Engineer" RAG India
site:naukri.com "Machine Learning Engineer" India
site:linkedin.com/jobs "LLM Engineer" India
site:linkedin.com/jobs "GenAI Engineer" Mumbai
"RAG" "LangGraph" engineer India
"Python" "AI Engineer" India remote
```

Hindi / Marathi:
```
site:naukri.com "एआई इंजीनियर" भारत
site:naukri.com "मशीन लर्निंग इंजीनियर" मुंबई
site:naukri.com "एआय इंजिनिअर" भारत
```

### Priority 3: Full-Stack Engineering

Adjacent roles you could pivot into: React/Next.js front ends with Node/NestJS backends.

```
site:naukri.com "Full Stack Developer" TypeScript Mumbai
site:linkedin.com/jobs "Full Stack Engineer" India
"React" "Node.js" developer India remote
"Next.js" developer India
```

Hindi / Marathi:
```
site:naukri.com "फुल स्टैक डेवलपर" मुंबई
site:naukri.com "फुल स्टॅक डेव्हलपर" मुंबई
```

### Priority 4: Platform / General Software Engineering

Wider net: platform/infrastructure and general SDE roles across the stack.

```
site:naukri.com "Software Engineer" AWS India
site:naukri.com "Platform Engineer" India
site:linkedin.com/jobs "SDE" India
site:linkedin.com/jobs "Software Development Engineer" Mumbai
"PostgreSQL" "AWS" backend engineer India
```

Hindi / Marathi:
```
site:naukri.com "सॉफ्टवेयर इंजीनियर" भारत
site:naukri.com "सॉफ्टवेअर इंजिनिअर" भारत
```

## Location Filter

When evaluating results, verify the job location is within reasonable distance from your home in Mumbai. You are open to relocation anywhere in India (and remote-first roles), so the tiers are:

- **Ideal:** Mumbai and surrounding metro area, or a fully remote (India-based) role
- **Acceptable:** Pune, Bengaluru, Hyderabad, Chennai, Delhi NCR, Ahmedabad — strong tech-hiring metros, relocation is welcome
- **Borderline:** Tier-2 Indian cities with limited role density (~relocation required)
- **Too far:** Outside India, unless the role is explicitly remote

## Language Filter

Your working languages and levels are in CLAUDE.md's Languages table (Hindi — native, Marathi — conversational, English — professional). When filtering scraped results, apply `04-job-evaluation.md`'s Language Gate: a posting requiring a language you haven't declared at all is excluded; a posting requiring a higher level than you declared in a language you do work in is not excluded, flag it clearly instead (see `job-scraper/SKILL.md`'s Step 3 "Quick Fit Assessment" for how the flag surfaces in `/scrape` output). Postings simply *written* in a language you don't work in, that don't require it on the job, are fine.

## Date Filter

Only include jobs posted within the last 14 days, or with an application deadline that has not yet passed. If a posting date cannot be determined, include it but flag as "date unknown".

## Adapting Queries

If the user specifies a focus area, select queries from the matching category and also generate 2-3 custom queries for that focus. For example:
- "/scrape [focus_area]" -> relevant category queries + custom focus-specific queries
