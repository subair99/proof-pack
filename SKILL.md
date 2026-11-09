---
name: proof-pack
description: Builds a claim-ready evidence pack for one real-life incident (car damage, a missing delivery, a faulty product under warranty, a rental deposit dispute, home water or storm damage) from what is already stored on the user's UGREEN AI NAS. It queries camera events in the incident window, finds photos from that window, and reads the matching policy, receipt, lease or warranty documents. Every fact goes on one dated timeline that names its source file. Each checklist item is then marked Verified (two sources agree), Single source, Conflict, Weak or Missing. Where documents have several versions, it shows what changed. Before anything is written, it flags photos that show other people so they can be left out. After confirmation it copies the evidence into a case folder, creates an album and saves a cited summary note. It never moves, deletes or shares files. Use for requests like "build my insurance claim for the car scratch last Tuesday".
---

# Proof Pack

## User goal

When something goes wrong, the proof is usually already on the NAS, but it is spread across camera events, phone photo backups and scanned documents. The user wants one organized pack they can hand to an insurer, landlord, courier or seller, an honest view of which facts are backed up and which conflict or are missing, and confidence that the pack exposes nobody else. They want all of this without uploading private footage or documents anywhere.

## When to use

Trigger phrases include:

- "Build a claim pack for the dent on my car from last Tuesday night."
- "My parcel went missing on 3 November. Pull together everything I have."
- "Get my deposit evidence ready. I moved in March 2025 and moved out last week."
- "My laptop died. Is it still under warranty, and what proof do I have?"
- "Update my Proof Pack for the car claim." (re-checks an existing case)

Do not use this Skill for legal advice, to judge who is at fault, or to write to the other party on the user's behalf.

## Required inputs

| Input | Required | How it is obtained |
|---|---|---|
| Claim type | Yes | Inferred from the request and confirmed with the user. One of the five recipes in [claim-recipes.md](references/claim-recipes.md). |
| Incident window | Yes | A start and end date-time. If the user gives only a day, use 00:00 to 23:59 of that day and say so. If the user says "recently" or similar, ask for a date. Never guess. |
| Location or object keywords | Optional | For example "driveway", "front door", "laptop", "flat 4B". Used to narrow camera and file searches. |
| Comparison window | Deposit claims only | The move-in period, used to pair "before" photos with "after" photos. |
| People who may appear | Optional | Names of people the user is happy to show in the pack (usually the user). Used in Step 7. |
| Case name | Optional | Default: `<claim-type>-<YYYY-MM-DD of incident start>`. |

## Supported NAS capabilities used

Only capabilities listed in the UGOS Skill Capability Scope are used. Each is called through its registered expert agent.

| Step | Capability area | Action | Access |
|---|---|---|---|
| 2 | Surveillance | Query camera events for the incident window and keywords; organize event details (camera, time, event type) | Read |
| 3 | Albums | Find photos by time (and location when available) in the incident and comparison windows | Read |
| 3 | Files | Find documents by keyword and date (policy, receipt, invoice, lease, warranty, quote, police or courier reference) | Read |
| 4 | Documents | Read found documents, extract key fields, translate non-English documents, reconcile amounts, dates and versions across documents | Read |
| 4 | Knowledge | Transcribe a voice note in which the user describes the incident, if one is supplied | Read |
| 7 | Albums | Find people in the selected photos (person search) | Read |
| 9 | Files | Create the case folder, copy evidence files into it, tag them | Write, after confirmation |
| 9 | Albums | Create the evidence album from the confirmed photos | Write, after confirmation |
| 9 | Knowledge | Save the timeline, evidence check and summary as a text note in the case folder | Write, after confirmation |

The Skill never moves, renames, overwrites or deletes an original file, and never sends or shares anything outside the NAS.

## Expert agent orchestration workflow

Each step uses the actual output of the step before it: the event IDs, photo IDs and file paths returned by the agents, not descriptions of them.

### Step 1. Intake

1. Identify the claim type and open its recipe in [claim-recipes.md](references/claim-recipes.md).
2. Collect the required inputs from the table above. Ask only for what is missing, one question at a time.
3. Repeat back the claim type, window and keywords in one sentence and wait for "yes" before searching.

### Step 2. Camera sweep (Surveillance expert agent)

1. Query camera events between the window start and end. If keywords name a place, limit to cameras whose names match it; if none match, query all cameras and say so.
2. Keep each returned event's camera name, start time, end time and event type.
3. If the Surveillance capability is unavailable or returns no cameras, record "No cameras available" and continue. If cameras exist but return no events, record "No camera events in this window". Do not widen the window unless the user asks.

### Step 3. Photo and document sweep (Albums and Files expert agents)

1. Ask the Albums agent for photos taken inside the incident window and for the recipe's follow-up period after it (people photograph damage after it happens). For deposit claims, also query the comparison window.
2. Ask the Files agent for documents matching the recipe's document keywords, limited to the date range the recipe gives.
3. Keep the returned photo IDs and file paths. Photos with no capture date are listed separately as "undated" and are never placed on the timeline.

### Step 4. Read, extract and reconcile (Documents and Knowledge expert agents)

1. For each found document, ask the Documents agent to extract the fields the recipe lists, for example policy number, cover period, excess, purchase date, serial number, amount, or the lease's deposit and condition clauses.
2. For each extracted fact, keep its source file and its date source: the date printed in the document, or the file date if the document shows none.
3. If a document is not in English, ask the Documents agent to translate the extracted fields and keep the original-language file as the evidence item.
4. **Version check.** If two or more documents are versions of the same thing (two repair quotes from the same garage, an updated policy schedule), ask the Documents agent to compare them. Use the one with the latest document date and list what changed, for example "Quote revised from 185,000 to 210,000 on 7 Nov". Never silently drop the older version.
5. Ask the Documents agent to run the recipe's reconcile checks, for example: is the incident inside the cover or warranty period? Does the quote exceed the excess? Do serial numbers match between receipt and warranty card?
6. If the user supplied a voice note, ask the Knowledge agent to transcribe it and keep the transcript as "User statement (transcribed)".
7. If a field cannot be read, write "not found in <file>" and do not fill it from another source.

### Step 5. Build the timeline

Merge camera events, dated photos and dated document facts into one list, oldest first, using the format in [pack-format.md](references/pack-format.md). Each line has a time, what it is and its source. No line may exist without a source.

### Step 6. Evidence check

Compare what was found against the recipe checklist and the rules in [evidence-rules.md](references/evidence-rules.md). Each required item gets exactly one status:

- **Verified**: found, and supported by at least two independent sources that agree (for example a camera event at 22:41 and damage photos the next morning, or a serial number that matches on the receipt and the warranty card).
- **Single source**: found in one source only. This is normal and not a fault, but it is shown.
- **Conflict**: two sources disagree, for example the courier's "delivered 14:32" against no camera events at the front door from 14:00 to 15:00. Both sources are shown side by side. The Skill states the disagreement and never decides which source is right.
- **Weak**: found, but with a problem the recipe names, for example photos taken outside the follow-up period, or a quote dated before the incident.
- **Missing**: not found. Say where you looked, for example "No police reference found in documents dated 1 to 10 November."

The order shown is Conflict, Missing, Weak, Single source, Verified, so the problems come first.

### Step 7. Share-safe review (Albums expert agent)

1. Ask the Albums agent which recognized people appear in the photos selected so far.
2. List any photo that shows a person the user did not name in "People who may appear", and any photo where the agent reports a person it cannot identify, for example "IMG_1190.jpg shows 1 unrecognized person". Children, neighbours and passers-by often appear in damage and deposit photos.
3. Ask the user, per photo, to keep or leave it out. Leaving a photo out removes it from the pack only; the original is untouched.
4. If person search is not available, say "I couldn't check these photos for other people. Please look through them before sharing." and continue.

### Step 8. Show and confirm

Show the user the timeline, the evidence check, any changes found in Step 4 and a plan of exactly what will be written: the case folder path, the list of files to copy, the album name and photo count, and the note name. Ask: "Create this pack? Nothing will be moved or deleted." The user may remove items first. Proceed only on an explicit yes.

### Step 9. Organize (Files, Albums and Knowledge expert agents)

1. Files agent: create `/Proof Pack/<case name>/` and copy each confirmed file into it. Tag the copies `proof-pack` and `<case name>`.
2. Albums agent: create the album "Proof Pack: <case name>" from the confirmed photo IDs.
3. Knowledge agent: save `<case name> - summary` in the case folder. It contains a short "What the evidence shows" paragraph in which every sentence cites a timeline line, followed by the timeline, the evidence check and the version changes.
4. If a case folder with that name already exists, stop and ask whether to update it (Step 10) or use a new name. Never overwrite.

### Step 10. Update an existing case

When the user asks to update a pack, read the saved summary note, re-run Steps 2 to 7 only for items marked Conflict, Missing, Weak or Single source, show what changed, and ask before copying anything new in.

## Completion

The Skill is complete when it reports:

- the case folder path and the number of files copied
- the album name and number of photos, and how many were left out at the share-safe review
- the summary note name
- the evidence check, with Conflict and Missing items listed first

and ends with: "This pack organizes your own files. It is not legal or insurance advice."

## Failure handling

See [failure-handling.md](references/failure-handling.md) for the full list. The main rules:

- **Nothing found at all**: say "I found no camera events, photos or documents for this window," list what was searched, and stop. Do not create an empty folder.
- **An agent fails or times out**: name the step and the agent, keep what the other agents returned, mark the affected checklist items "Not checked (agent error)", and offer to retry that step only.
- **Ambiguous match**, for example two different insurance policies: show both with their cover dates and ask the user to pick. Never pick for them.
- **User declines at Step 8**: write nothing and say so.
- **Permission denied on a folder**: report the folder and continue with the folders that are authorized.

## AI model and data handling

- Extraction, translation, reconciliation, person search and transcription run on the NAS local AI model by default.
- Camera event data, photos, person information and original documents are never sent to a cloud service by this Skill.
- If the user's Uliya settings route text processing to a supported cloud AI service, the Skill tells the user before Step 4 and asks for consent to send extracted document text only. If the user says no, only the local model is used, and fields it cannot read are marked "not found".
- Person results from Step 7 are used only inside the session to show the review list. They are not saved in the pack or the note.

## References

- [README.md](README.md): a plain-language overview for users
- [claim-recipes.md](references/claim-recipes.md): checklists, search keywords, fields and reconcile checks for each claim type
- [evidence-rules.md](references/evidence-rules.md): how Verified, Single source, Conflict, Weak and Missing are decided, and how versions are compared
- [pack-format.md](references/pack-format.md): timeline, evidence check, share-safe list, summary and confirmation formats
- [failure-handling.md](references/failure-handling.md): every failure case and the exact response
- [test-plan.md](references/test-plan.md): synthetic test data and expected results for NAS validation
- [disclosures.md](references/disclosures.md): third-party components, licenses, AI tools used and original work
