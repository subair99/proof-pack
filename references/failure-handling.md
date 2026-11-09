# Failure handling

Every case below ends with a clear message to the user. The Skill never fills a gap with similar or unrelated content.

Back to [SKILL.md](../SKILL.md).

| Situation | Step | What the Skill does | Message to the user |
|---|---|---|---|
| No date given ("recently", "a while ago") | 1 | Asks for a date and does not search | "Which day did it happen? An approximate day is fine." |
| Claim type does not match any recipe | 1 | Stops | "I can build packs for vehicle damage, deliveries, warranties, deposits and home damage. This doesn't match one of those." |
| No cameras on the NAS | 2 | Skips the camera sweep and continues | "No cameras are set up, so the pack has no camera events." |
| Cameras exist, no events in window | 2 | Records it and continues; does not widen the window | "No camera events between <start> and <end>." |
| Camera results reach the 200-event limit | 2 | Marks camera-dependent items "Not checked (results cut off)" and offers a shorter window | "There were too many camera events to be sure I have them all. Try a shorter window?" |
| Camera footage for the window has passed its retention period | 2 | Records it as Missing with the reason | "Camera events for this period are no longer stored." |
| No photos in window | 3 | Marks photo items Missing | "No photos taken between <start> and <end>." |
| Photos without a capture date, in a folder the user named | 3 | Lists them as undated and leaves them off the timeline | "<n> photos have no date and were left off the timeline." |
| No matching documents | 3 | Marks document items Missing with the keywords and dates searched | "No <item> found in documents dated <range> for <keywords>." |
| Document unreadable (blurred scan, handwriting) | 4 | Keeps the file as evidence and marks the fields "not found in <file>" | "I couldn't read the <field> in <file>. You may want to check it by hand." |
| Two or more candidate documents (for example two policies) | 4 | Shows them side by side and asks | "I found two policies. Which one covers this?" |
| Two versions of a document with the same or no date | 4 | Asks which one is current; keeps both | "These two quotes have the same date. Which one is current?" |
| Two sources disagree | 6 | Marks the item Conflict and shows both values; does not choose | "Courier record: <value>. Camera: <value>." |
| Person search unavailable | 7 | Skips the share-safe check and warns | "I couldn't check these photos for other people. Please look through them before sharing." |
| Person search completes | 7 | Always adds a reminder, because only people the NAS has grouped can be spotted | "I can only spot people your NAS has already grouped. Please look through the photos before sharing." |
| User leaves out every photo | 7 | Continues with documents and camera events only; photo items become Missing | "No photos will be included, so the photo items are now Missing." |
| User declines document reading | 4 | Reads no documents, marks document items "Not checked (document reading declined)" and continues | "I won't read your documents, so those items are marked Not checked." |
| Translation declined or fails | 4 | Shows the original text and keeps the original file as evidence | "I couldn't translate <file>, so its details are shown in the original language." |
| Nothing found at all | 6 | Stops; creates nothing | "I found no camera events, photos or documents for this window. I searched: <list>." |
| User declines or does not answer at Step 8 | 8 | Writes nothing | "No pack created. Nothing was changed." |
| Case folder already exists | 8 | Stops and asks | "A pack named <case> already exists. Update it, or use a new name?" |
| Copy fails for some files | 9 | Keeps successful copies and lists the failed ones; never deletes partial results | "<n> files could not be copied: <list>. The rest of the pack was created." |
| A write in Step 9 is waiting for approval, or approval is refused | 9 | Pauses while waiting; if refused, reports what was already written and leaves it in place | "This step needs your approval on the NAS: <step>." / "Stopped at <step>. Already created: <list>." |
| Expert agent error or timeout | Any | Keeps other results, marks affected items "Not checked (agent error)", offers to retry that step only | "The <area> step failed. Retry just that step?" |
| Permission denied on a folder | 3, 9 | Skips that folder and continues with authorized folders | "I don't have access to <folder>, so it wasn't searched." |
