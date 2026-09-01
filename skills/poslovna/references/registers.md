# What each register covers

Coverage boundaries matter more than field lists: most wrong answers come from asking a register
a question it never had the data to answer.

## The subject registers

**Sudski registar** (court register) — companies. The spine: name, OIB, MBS, legal form, seat,
status, officers, members, share capital. Keyed on **MBS**, not OIB, which is why some things
join by one and not the other.

**Obrtni registar** — sole traders, keyed on **MBO**. Their OIB is the *owner's* personal OIB,
not the business's, so a sole trader's OIB is an attribute of a person.

**Registar udruga** — associations. Officer identities are frequently inferred rather than
named; check the confidence.

## The fact registers

**RSV** — beneficial owners. **Natural persons only.** A corporate shareholder never appears
here; it comes from the court register's members instead. Walking a chain therefore alternates
between the two, and stops at a foreign parent — say so rather than presenting a partial chain
as complete.

**GFI-POD** — filed annual accounts. Recent years only. Contact columns are withheld.

**Tax debt** — published defaulters. Absence from the list is not proof of no debt: the list has
a threshold and a publication cadence.

**Insolvency notices** — name the debtor **and** the court-appointed trustee. The first
identifier in a notice is often the trustee, not the subject.

**Public procurement** — awards. Older records are in HRK, newer in EUR; do not sum across the
boundary without saying so.

**Land registry (ZK)** — at parcel grain. Organisations are served in full; persons are served
under stricter rules. `use_code` is a land-use category and **not** zoning.

## The gazettes

**Narodne novine** — the national gazette. Carries the pre-2009 chronology, which is the era
where no OIB exists.

**e-Oglasna** — court notices. NN and e-Oglasna **hand over** in September 2015 rather than
overlapping, so a gap at the boundary is the handover, not missing data.

**JLS gazettes** — municipal and county official gazettes. The publishing municipality is the
institution; text and OCR are both present and the source says which.

## Identifiers

| identifier | what it keys | note |
|---|---|---|
| OIB | any subject or person | **exists only from 2009** |
| MBS | a company in the court register | the pre-OIB join |
| MBO | a sole trader | |

Every served fact carries its source. `poslovna://catalog/sources` names them all and says what
each permits — see `publishing.md`.
