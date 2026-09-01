# The query grammar

Two tools take criteria, and they take them differently.

- **`poslovna_browse`** takes them as **arguments** (`county="Splitsko-dalmatska"`, `nkd="41.20"`).
  This is the normal way. The tool compiles them and reports which of its two query plans answered.
- **`poslovna_search`** takes an **operator string** in `q`. Reach for it only when a question
  needs something the arguments cannot express — a disjunction, or free text combined with a
  filter.

Give `poslovna_browse` values as **names, not ids**: `county="Splitsko-dalmatska"`,
`size_band="large"`. It resolves them.

## Fields

Every field, with the spellings the parser accepts. Aliases are folded and lower-cased before
lookup, so case and diacritics do not matter in the FIELD NAME.

| field | aliases | value |
|---|---|---|
| `grad` | `mjesto`, `city` | settlement name as the register writes it |
| `zupanija` | `zup`, `county` | county name |
| `nkd` | `djelatnost`, `nace` | NKD 2007 code, **dotted**: `41.20`, `62.01` |
| `oblik` | `pravnioblik` | legal form, in the register's Croatian |
| `status` | — | `active` or `dissolved` |
| `velicina` | — | `micro`, `small`, `medium`, `large`, `unknown` |
| `godina` | `osnovan`, `godinaosnutka` | year of incorporation |
| `prihod` | `promet` | revenue, in EUR |
| `ulica` | `adresa`, `street` | street name |
| `oib` | — | 11 digits |
| `mbs` | — | court register number |

Croatian value spellings are accepted where the underlying column is not Croatian:
`status:aktivan`, `status:ugašen`, `velicina:mikro` all work. `grad`, `zupanija` and `oblik` hold
the register's own Croatian already.

## Values, ranges and quoting

```
grad:Split                      equals
grad:"Sveta Nedelja"            a value with a space MUST be quoted
                                unquoted, "Nedelja" becomes a free-text term and the filter
                                silently means something else
prihod:>=1000000                at least
prihod:>1000000                 ALSO at least -- bare > is read as >=, not as "more than"
prihod:<=500000                 at most (and bare < is <=)
prihod:1.000.000                dots and spaces are thousands separators and are stripped,
                                so there are no decimals: `prihod:>=1.5` means 15
godina:2010-2015                inclusive range; only the numeric fields read a dash as a range,
                                because a county like SLAVONSKI BROD-POSAVINA contains one
-grad:Split                     negation
(banka OR kreditna) grad:Split  grouping; filters apply to the whole query, not to the group
```

## Three traps that produce a wrong answer rather than an error

**`nkd` matches as a PREFIX, and the dot is cosmetic.** Verified against the running API:
`nkd:41.20`, `nkd:4120`, `nkd:41.2` and `nkd:412` all return the same results, because dots are
stripped and the remaining digits are matched as a prefix. That is useful — `nkd:41.2` deliberately
broadens to the whole 41.2x group — and it is also the trap: a code you truncated by accident
answers a *broader* question and looks like a working query. Give the full dotted code unless you
mean the group.

**A county name may or may not carry the word `županija`.** The register writes it both ways and
so do people. Both are accepted; the token is stripped from both sides before comparison. Do not
add or remove it to "fix" a query that returned nothing — that is not the cause.

**`status` defaults to ACTIVE on `poslovna_browse`.** Croatia has a large number of dissolved
subjects on the register, and including them silently would answer a different question. Pass
`status="any"` when the question is historical.

## Which plan answered, and why it matters

`poslovna_browse` reports the plan in its response.

- The **SQL plan** pages the whole register exhaustively. Totals are paging guarantees.
- The **operator plan** runs when a city or free-text term is involved. It pages a re-ranked
  window, so its total is **a count, not a paging guarantee** — do not promise a reader that
  page 400 exists.

`poslovna_search` with operators switches the helpful behaviour OFF, deliberately: no
spelling-correction branch and no OR fallback. An operator is the caller asserting they are not
wrong, so an empty result means empty, not "try harder".

## Facet vocabularies

`poslovna://facets` lists the accepted values for county, legal form, size band and status. Read
it rather than guessing a spelling — legal forms in particular are the register's own prose and
there are far more of them than the handful in common use.
