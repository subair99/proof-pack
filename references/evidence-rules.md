# Evidence rules

These rules decide the status of every checklist item in Step 6 and how document versions are handled in Step 4. They exist so the pack is honest: it shows what is backed up, what disagrees and what is absent, and it never decides a dispute for the user.

Back to [SKILL.md](../SKILL.md).

## Independent sources

Two items are independent if they come from different capability areas or different makers:

- a camera event and a phone photo: independent
- a receipt and a warranty card from the manufacturer: independent
- two photos from the same phone: not independent (they count as one source)
- a quote and a revised quote from the same garage: not independent (they are versions; see below)
- the user's own transcribed statement: a source, but never enough on its own to make an item Verified

## Status rules

| Status | Rule |
|---|---|
| Verified | The item is found, and at least two independent sources agree on its key fact (time within the recipe's tolerance, matching amount, matching serial number or registration). |
| Single source | The item is found in one source only, with no disagreement. |
| Conflict | Two sources disagree on the key fact beyond the tolerance. Both are shown with their values and file paths. |
| Weak | The item is found but breaks a "weak if" rule in its recipe. |
| Missing | No source was found. The message names the capability, keywords and date range searched. |

An item that is both Weak and in Conflict is shown as Conflict, with the weakness noted underneath.

## Time tolerance

- Camera event against photo: the photo must fall within the recipe's follow-up period after the event.
- Camera event against a courier or document time: they agree if within 30 minutes.
- Document date against document date (for example receipt against warranty start): they agree if within 3 days.

## Wording rules

- State a conflict as two facts, never as a conclusion. Write "Courier record: delivered 14:32. Front Door camera: no events 14:00 to 15:00." Do not write "the parcel was not delivered".
- Never use words that imply a verdict, such as "proves", "fraud", "lied" or "fault".
- In the summary paragraph, each sentence ends with the timeline line it relies on, for example "[T3]".

## Versions

1. Two documents are treated as versions if they are the same type (quote, policy schedule, lease, deduction letter), come from the same issuer, and concern the same object or address.
2. The version with the latest document date is the current one. If the dates are equal or missing, ask the user which one is current.
3. List every changed field (amount, date, clause text, item list) as "<field>: <old> → <new> (<old file> → <new file>)".
4. Keep all versions in the pack. A changed amount between versions is not a Conflict; it is shown under "Changes".
