---
name: proof-pack
description: Builds a claim-ready evidence pack for one real-life incident (car damage, a missing delivery, a faulty product under warranty, a rental deposit dispute, home water or storm damage) from what is already stored on the user's UGREEN AI NAS. It gathers camera events from the incident window, photos from that window, and the matching policy, receipt, lease or warranty documents. Every fact goes on one dated timeline that names its source. Each checklist item is marked Verified (two sources agree), Single source, Conflict, Weak or Missing. Where documents have several versions, it shows what changed. It flags photos showing people the NAS has grouped so they can be left out. After confirmation it copies the evidence into a case folder, creates an album and saves a cited summary note. Originals stay untouched. Use when the user asks for something like "build my insurance claim for the car scratch last Tuesday".
---

# Proof Pack

# Goal

When something goes wrong, the proof is usually already on the NAS, but it is spread across camera events, phone photo backups and scanned documents. The user wants one organized pack they can hand to an insurer, landlord, courier or seller, and an honest view of which facts are backed up and which conflict or are missing.

Trigger phrases include:

- "Build a claim pack for the dent on my car from last Tuesday night."
- "My parcel went missing on 3 November. Pull together everything I have."
- "Get my deposit evidence ready. I moved in March 2025 and moved out last week."
- "My laptop died. Is it still under warranty, and what proof do I have?"
- "Update my Proof Pack for the car claim." (re-checks an existing case)

Do not use this Skill for legal advice, to judge who is at fault, or to write to the other party on the user's behalf.

# Required input

| Input | Required | How it is obtained |
|---|---|---|
| Claim type | Yes | Inferred from the request and confirmed with the user. One of the five recipes in [claim-recipes.md](references/claim-recipes.md). |
| Incident window | Yes | A start and end date-time. If the user gives only a day, use 00:00 to 23:59 of that day and say so. If the user says "recently" or similar, ask for a date. Never guess. |
| Location or object keywords | Optional | For example "driveway", "front door", "laptop", "flat 4B". Used to narrow camera and file searches. |
| Comparison window | Deposit claims only | The move-in period, used to pair "before" photos with "after" photos. |
| People who may appear | Optional | Names of people the user is happy to show in the pack (usually the user). Used in Step 7. |
| Photo folder | Optional | A folder the user says holds photos for this incident. It is the only place undated photos are looked for. |
| Case name | Optional | Default: `{claim-type}-{YYYY-MM-DD of incident start}`. |

# Generation-time capability evidence

Each CLI and its single registered expert, from the UGOS capability catalog:

- `ug-surveillance` → `security-monitor-agent`
- `ug-photo` → `photo-agent`
- `ug-filesystem` → `file-agent`
- `ug-filemgr` → `file-agent`
- `file-workflow` → `file-agent`
- `ug-document` → `document-agent`
- `ug-knowledge` → `knowledge-agent`

| Step | Expert | Capability used | Effect |
|---|---|---|---|
| 2 | `security-monitor-agent` | `cameras`, `event-timeline` | Read |
| 3 | `photo-agent` | `search-picture`, `meta` | Read |
| 3 | `file-agent` | `find` | Read |
| 3 | `knowledge-agent` | `collect-evidence` | Read |
| 4 | `document-agent` | `extract`, `reconcile`, `translate` (text returned to the session only) | Read; `translate` may ask for approval |
| 4 | `knowledge-agent` | `audio-transcription` | Read |
| 7 | `photo-agent` | `figure-albums`, `search-picture` | Read |
| 8 | `file-agent` | `get-user-dir`, `stat` | Read |
| 9 | `file-agent` | `mkdir`, `cpmv` as copy, `tag-get-by-name`, `tag-add`, `tag-add-file` | Write, after confirmation |
| 9 | `photo-agent` | `create-album` | Write, after confirmation |
| 9 | `knowledge-agent` | `save-text` | Write, after confirmation |
| 10 | `file-agent` | `cat` | Read |

The Skill never moves, renames, overwrites or deletes an original file, and never sends or shares anything outside the NAS. The experts perform all NAS work; this Skill only delegates to them and never uses `cli.invoke` itself.

# Orchestration

Run the steps in order. Values in curly braces are filled in at run time with real results returned by the upstream expert, such as event IDs, photo IDs and file paths, or with what the user said. If a required value is unavailable, stop the steps that depend on it. Handle every delegation result as described in [Completion and failure](#completion-and-failure).

## Step 1. Intake

1. Identify the claim type and open its recipe in [claim-recipes.md](references/claim-recipes.md).
2. Collect the required inputs from the table above. Ask only for what is missing, one question at a time.
3. Repeat back the claim type, window and keywords in one sentence and wait for "yes" before searching.

## Step 2. Camera sweep

If the recipe says the camera sweep is not used, record "Not needed for this claim type" and go to Step 3.

1. Get the real camera list:

   `delegate --target security-monitor-agent --instruction 'List the cameras on this NAS. For each camera return its real camera ID, camera name and location. If there are no cameras, say so. Change nothing.'`

2. Match the keywords and the recipe's camera keywords against the returned names and locations. If none match, use all returned cameras and say so.
3. Query events using the matched camera IDs:

   `delegate --target security-monitor-agent --instruction 'Query camera events from {window start} to {window end} in time zone {NAS time zone} for camera IDs {matched camera IDs}, returning up to 200 events. For each event return the real event ID, camera name, start time, end time and event type. Say whether the result reached the limit of 200. If there are no results, say so and do not widen the time range or add other cameras. Change nothing.'`

4. If the expert is unavailable or returns no cameras, record "No cameras available" and continue. If cameras exist but return no events, record "No camera events in this window". Do not widen the window unless the user asks.
5. If the result reached the limit of 200, tell the user that later events may be cut off and offer a shorter window. Until the user answers, mark camera-dependent items "Not checked (results cut off)".

## Step 3. Photo and document sweep

1. Find photos in the incident window and the recipe's follow-up period (people photograph damage after it happens). For deposit claims, run this again for the comparison window.

   `delegate --target photo-agent --instruction 'Find photos taken from {photo window start} to {photo window end}. For each photo return its real photo ID, file path, capture time and location if available. If there are no results, say so and do not widen the time range or use similar photos. Change nothing.'`

2. If the user named a photo folder, look for undated photos there. Without a photo folder, undated photos are not looked for.

   `delegate --target photo-agent --instruction 'In the folder {photo folder}, list the photos that have no capture date. For each one return its real photo ID and file path. If there are no results, say so. Change nothing.'`

3. Find documents by file name and date, using the recipe's keywords and date range:

   `delegate --target file-agent --instruction 'Find files whose names contain any of these keywords: {recipe document keywords and user keywords}, dated from {document range start} to {document range end}. Return each real file path, file type and modified date. If there are no results, say so and do not use similar files. Change nothing.'`

4. Find scanned documents with generic file names by searching their content:

   `delegate --target knowledge-agent --instruction 'Search the content of my documents for: {recipe document keywords and user keywords}. Return the real file path of each matching document. If there are no results, say so. Change nothing.'`

5. Merge the file paths from items 3 and 4, removing duplicates. Keep the returned photo IDs and file paths. Undated photos are listed separately as "undated" and are never placed on the timeline.

## Step 4. Read, extract and reconcile

Before the first document is read, tell the user: "I'll read your documents using the AI set up on your NAS. If you've set Uliya to use a cloud AI service, text from these documents may be processed there. Continue?" If the user says no, do not read the documents. Mark document items "Not checked (document reading declined)" and go to Step 5.

1. Extract the recipe's fields, for example policy number, cover period, excess, purchase date, serial number, amount, or the lease's deposit and condition clauses.

   `delegate --target document-agent --instruction 'From the files {real file paths}, extract these fields: {recipe fields}. For each value return the file path it came from and the date printed in the document. If a field cannot be read, report it as not found for that file and do not fill it from another file. Change nothing.'`

2. Keep each fact's source file and its date source: the date printed in the document, or the file date if the document shows none.
3. If a document is not in English, translate the extracted fields and keep the original-language file as the evidence item. If translation is declined or fails, show the original text and say it was not translated.

   `delegate --target document-agent --instruction 'Translate into English the following fields from {real file path}: {extracted fields}. Return the translated text in this conversation only. Do not save, create or change any file.'`

4. **Version check.** If two or more documents are versions of the same thing (two repair quotes from the same garage, an updated policy schedule), compare them. Use the one with the latest document date and list what changed, for example "Quote revised from 185,000 to 210,000 on 7 Nov". Never silently drop the older version.

   `delegate --target document-agent --instruction 'Compare the document versions {real file paths} and list every field that differs (amount, date, clause text, item list) with its old value, new value and file path. If nothing differs, say so. Change nothing.'`

5. Run the recipe's reconcile checks, for example: is the incident inside the cover or warranty period? Does the quote exceed the excess? Do serial numbers match between receipt and warranty card?

   `delegate --target document-agent --instruction 'Using the files {real file paths}, check these items: {recipe reconcile checks with the incident date}. For each check return the values compared and the file each value came from. If a value cannot be found, say so and do not estimate it. Change nothing.'`

6. If the user supplied a voice note, transcribe it and keep the transcript as "User statement (transcribed)".

   `delegate --target knowledge-agent --instruction 'Transcribe the audio file {real file path} and return the text in this conversation only. Do not save anything.'`

7. If a field cannot be read, write "not found in {file}" and do not fill it from another source.

## Step 5. Build the timeline

Merge camera events, dated photos and dated document facts into one list, oldest first, using the format in [pack-format.md](references/pack-format.md). Each line has a time, what it is and its source. No line may exist without a source.

## Step 6. Evidence check

Compare what was found against the recipe checklist and the rules in [evidence-rules.md](references/evidence-rules.md). If the camera, photo and document sweeps all found nothing, say "I found no camera events, photos or documents for this window," list what was searched, and stop. Otherwise each required item gets exactly one status:

- **Verified**: found, and supported by at least two independent sources that agree (for example a camera event at 22:41 and damage photos the next morning, or a serial number that matches on the receipt and the warranty card).
- **Single source**: found in one source only. This is normal and not a fault, but it is shown.
- **Conflict**: two sources disagree, for example the courier's "delivered 14:32" against no camera events at the front door from 14:00 to 15:00. Both sources are shown side by side. The Skill states the disagreement and never decides which source is right.
- **Weak**: found, but with a problem the recipe names, for example photos taken outside the follow-up period, or a quote dated before the incident.
- **Missing**: not found. Say where you looked, for example "No police reference found in documents dated 1 to 10 November."

The order shown is Conflict, Missing, Weak, Single source, Verified, so the problems come first.

## Step 7. Share-safe review

1. The NAS searches by person rather than by photo, so ask for each person's photos and match them against the selected photos.

   `delegate --target photo-agent --instruction 'List every person group on this NAS, including unnamed groups. For each group, find its photos taken from {earliest selected photo time} to {latest selected photo time}. Return only photos whose ID is one of {real selected photo IDs}, with the person name, or the word unnamed for an unnamed group. If there are no results, say so. Change nothing.'`

2. List any photo that shows a person the user did not name in "People who may appear", and any photo matched to an unnamed group, for example "IMG_1190.jpg: 1 unnamed person". Children, neighbours and passers-by often appear in damage and deposit photos.
3. Ask the user, per photo, to keep or leave it out. Leaving a photo out removes it from the pack only; the original is untouched.
4. Always add: "I can only spot people your NAS has already grouped. Please look through the photos before sharing."
5. If person search is not available, say "I couldn't check these photos for other people. Please look through them before sharing." and continue.
6. Person results stay in this session. They are not saved in the pack or the note.

## Step 8. Show and confirm

1. Find the real save location and check for an existing case:

   `delegate --target file-agent --instruction 'Return my real user directory, then check whether a folder named Proof Pack/{case name} exists inside it. Return the full path and whether it exists. Change nothing.'`

2. If the folder exists, stop and ask whether to update it (Step 10) or use a new name. Never overwrite.
3. Show the user the timeline, the evidence check, any changes found in Step 4 and a plan of exactly what will be written: the case folder path, the list of files to copy, the album name and photo count, and the note name.
4. Ask: "Create this pack? Nothing will be moved or deleted." The user may remove items first. Proceed only on an explicit yes. If the user declines, write nothing and say so.

## Step 9. Organize

Nothing is written before this step. The NAS may ask the user to approve individual writes, even after the yes in Step 8.

1. Create the folder. If this does not complete, stop and write nothing else.

   `delegate --target file-agent --instruction 'Create the folder {case folder path}, including any missing parent folders. If it already exists, stop and say so.'`

2. Copy the confirmed files using the folder path returned above. Originals are only read.

   `delegate --target file-agent --instruction 'Copy these files into {returned case folder path}: {real confirmed file paths}. Copy only: do not move, rename, overwrite or delete anything. Report each file as copied or failed, with the reason.'`

3. Tag the copies.

   `delegate --target file-agent --instruction 'Tag the files {real copied file paths in the case folder} with the tags proof-pack and {case name}, creating those tags if they do not exist. Tag only those copies. Report each file as tagged or failed.'`

4. Create the album from the confirmed photo IDs.

   `delegate --target photo-agent --instruction 'Create an album named Proof Pack: {case name} containing exactly these photos: {real confirmed photo IDs}. Add no other photos. Return the real album ID and photo count.'`

5. Save the summary note. It contains a short "What the evidence shows" paragraph in which every sentence cites a timeline line, followed by the timeline, the evidence check and the version changes. Do not include person results.

   `delegate --target knowledge-agent --instruction 'Save this text as a new note in the folder {returned case folder path}, with the file name {case name} - summary. Do not overwrite any file. Return the real saved file name. Text: {summary text}'`

## Step 10. Update an existing case

When the user asks to update a pack:

1. Read the most recent saved summary note.

   `delegate --target file-agent --instruction 'In the folder {real case folder path}, find the most recent file whose name contains summary and return its full text. If there are no results, say so. Change nothing.'`

2. Re-run Steps 2 to 7 only for items marked Conflict, Missing, Weak or Single source, show what changed, and ask before copying anything new in.
3. After a yes, copy new files into the existing folder as in Step 9. The NAS has no published way to add photos to an existing album, so create a new album named "Proof Pack: {case name} (update {YYYY-MM-DD})" holding all confirmed photos, and save a new note named "{case name} - summary (update {YYYY-MM-DD})". The earlier album and note stay as they are.

# Completion and failure

The Skill is complete when it reports:

- the real case folder path and the number of files copied
- the album name and number of photos, and how many were left out at the share-safe review
- the real saved note name
- the evidence check, with Conflict and Missing items listed first

and ends with: "This pack organizes your own files. It is not legal or insurance advice."

Report a result only after the expert returns it. Local generation or checking of this Skill is not evidence of anything on the NAS.

| Delegation state | What the Skill does |
|---|---|
| `completed` | Use only the real results returned. A completed query with no results is reported as empty, and the item becomes Missing, naming what was searched. Never substitute similar content. |
| `failed` | Name the step and the expert, keep what other experts returned, mark affected items "Not checked (agent error)", stop dependent steps and offer to retry that step only. |
| `cancelled` | Stop dependent steps. In Step 9, report exactly what was already written and leave it in place. |
| `waiting_user` | Pass the expert's question to the user and continue with the answer. Do not guess. |
| `waiting_approval` | Tell the user what is waiting for approval and pause. It does not count as done. If approval is refused, treat the step as `cancelled`. |

See [failure-handling.md](references/failure-handling.md) for the full list. The main rules:

- **Nothing found at all**: say "I found no camera events, photos or documents for this window," list what was searched, and stop. Do not create an empty folder.
- **An expert fails or times out**: follow the `failed` row above.
- **Ambiguous match**, for example two different insurance policies: show both with their cover dates and ask the user to pick. Never pick for them.
- **User declines at Step 8**: write nothing and say so.
- **Permission denied on a folder**: report the folder and continue with the folders that are authorized.
- **Partial writes in Step 9**: keep what succeeded, list what did not, and never delete partial results.

# AI model and data handling

- This Skill makes no external calls, and it does not ask any expert to send or share files, photos, camera data or person results outside the NAS.
- Reading, translation, reconciliation and transcription are done by the NAS experts, using the AI set up in the user's Uliya settings. This Skill cannot read or change those settings, so it asks before Step 4 in case they use a cloud AI service.
- Person results from Step 7 are used only inside the session to show the review list. They are not saved in the pack or the note.

# References

- [README.md](references/README.md): a plain-language overview for users
- [claim-recipes.md](references/claim-recipes.md): checklists, search keywords, fields and reconcile checks for each claim type
- [evidence-rules.md](references/evidence-rules.md): how Verified, Single source, Conflict, Weak and Missing are decided, and how versions are compared
- [pack-format.md](references/pack-format.md): timeline, evidence check, share-safe list, summary and confirmation formats
- [failure-handling.md](references/failure-handling.md): every failure case and the exact response
- [test-plan.md](references/test-plan.md): synthetic test data and expected results for NAS validation
- [disclosures.md](references/disclosures.md): third-party components, licenses, AI tools used and original work
