---
name: poslovna
description: Use when answering questions about Croatian companies, sole traders, associations or the people behind them — counterparty and KYB checks, ownership chains, filed accounts, tax debt, insolvency, public procurement, land registry, or the official gazettes. Also use when a Croatian OIB, MBS or MBO appears in the text.
---

# Croatian business registers

The `poslovna` MCP server reads the Croatian registers. Seven tools; this file is about
**choosing between them and reading the answers correctly**, which is where the mistakes are.

## Pick the tool by the shape of the question

| the question | the tool |
|---|---|
| "can we deal with them?" — one company, one identifier | `poslovna_kyb_check` |
| a NAME, and you need an identifier | `poslovna_search` |
| any question with a CRITERION in it | `poslovna_browse` |
| one company, in depth | `poslovna_company` |
| who runs / owns one company | `poslovna_people_of_company` |
| one person | `poslovna_person` |
| a specific gazette or filing | `poslovna_document` |

Two rules that save most of the wasted calls:

- **`poslovna_kyb_check` first, for anything resembling due diligence.** One call answers most of
  it. Drill in only where it flags something.
- **`poslovna_search` matches NAMES, nothing else.** "construction companies in Split" sent to it
  returns companies *called* "construction". Anything with a criterion goes to `poslovna_browse`.

## Three kinds of absence, and they are not interchangeable

This is the one section worth re-reading. Getting it wrong produces a confident, clean-looking
answer about a company nobody actually checked — in a chat window, to somebody deciding whether
to extend credit.

| what you see | what it means | what you must write |
|---|---|---|
| "could not be read" | **our** outage | UNKNOWN. Never "none", never "clean" |
| "none recorded" / "not on the list" | a fact about the subject | none recorded |
| "not included in this view" | nobody asked | say nothing either way |

State plainly which risk signals were **checked and clear** and which **could not be read**. A
summary that merges the two is the failure mode with the worst consequences.

Absence of a subject from a list is also not always absence of the fact. Where a publisher prints
the identifier, absence is real; where it does not, the subject may simply be unmatched. The tool
output says which.

## Four more things the data will do to you

**A bound is not a date.** An ended role usually reads "between X and Y" — the register brackets
a departure between two snapshots and does not know the day. Never narrow that to a date. Where a
date IS exact, the output says so.

**Inferred identities carry a confidence, and it is the reason the data is served at all.** Where
a model matched a person to a record rather than the register naming them, the line says so. Keep
the qualifier. Do not round it away, and do not restate an inference as a register fact.

**A name is not a person.** A name bucket may be several people and has no profile. When a tool
says a result is FINAL, it is final — do not retry it.

**Before 2009 there is no OIB.** The identifier did not exist, so historical records link by MBS
or not at all. Quote attachment rates by era rather than overall.

Register values stay in Croatian, because a translated legal form is a different legal form.
Translate for your reader as you like, but quote names, legal forms and statuses as printed.

## References — read the one you need

- `references/query-grammar.md` — the operator syntax for `poslovna_browse` and
  `poslovna_search`: every field name, its aliases, the accepted values, ranges, negation and
  grouping. **Read this before composing any query with a criterion in it.**
- `references/registers.md` — what each register actually covers, where the coverage boundaries
  are, and which register answers which question.
- `references/publishing.md` — what may be restated in a document you publish. An agent
  restating a fact can be a redistributor in a way a browser page is not. **Read this before
  putting register data into anything that leaves the conversation.**

## Setup

The server needs an API key from <https://poslovna.com/api/v1/mcp/setup>, exported as
`POSLOVNA_API_KEY`. Queries count against that account's daily quota and are recorded in its
lookup log, exactly like searches on the website.
