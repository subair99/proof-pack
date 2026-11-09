# Test plan for NAS validation

All test data is synthetic: staged photos of a toy car and household objects, documents written for the test with fictional names, numbers and companies, and camera events recorded by pointing a camera at a staged scene. People who appear in test photos are team members who have agreed to it. No real personal data is used.

Back to [SKILL.md](../SKILL.md).

## Before the testing day

1. Run the UGOS Skill Creation Tool check until no required changes remain.
2. Prepare a synthetic data set in one ZIP: a policy schedule PDF, two versions of a repair quote, a courier "delivered" notice, a French-language receipt image, a lease PDF, 14 dated photos (EXIF set to the test dates, one showing a second team member), 3 undated photos and a 30-second voice note.
3. Write the expected result for each test below so the run can be checked line by line, and plan the order: core 1, then the edge case, then core 2, so the most important run happens first.

## Core test 1: vehicle damage, full pack

- **Setup**: camera "Driveway" with a motion event at 22:41 on the test day; 3 photos the next morning, one of which shows a second team member; the policy schedule and both quote versions in `/Documents/Car/`.
- **Say**: "Build a claim pack for the scratch on my car last night."
- **Expected**:
  - The camera event, photos and documents appear on the timeline in time order, numbered T1 onwards.
  - "Damage occurred in window" and "Insurance cover" are Verified. The current quote is Single source. The police reference is Missing.
  - Changes shows the quote amount moving from the first version to the second.
  - The share-safe list flags the photo with the second person; the tester leaves it out.
  - The confirmation lists exactly the remaining files. After "yes", the folder, the album (with one photo left out) and the cited summary note exist, and every original file is unchanged.

## Core test 2: deposit, before-and-after pairing

- **Setup**: 5 move-in photos and 5 move-out photos in albums named by room; a lease PDF with a deposit clause; the kitchen has move-in photos only.
- **Say**: "Get my deposit evidence ready. I moved in on 1 March and out on 1 November."
- **Expected**: 4 rooms are paired before and after; the kitchen is Weak; the deposit amount and the clause number are extracted from the lease; no camera sweep runs, and the report says it was not needed for this claim type.

## Edge case: conflict, foreign-language receipt and a refusal

- **Setup**: a delivery claim; the courier notice says "delivered 14:32" but the Front Door camera has no events from 14:00 to 15:00; the only receipt is in French; 3 undated photos.
- **Say**: "My parcel never arrived on the 10th. Pull together what I have."
- **Expected**:
  - The delivery item is Conflict, with both facts shown and no verdict wording.
  - The camera window is not widened without being asked.
  - The receipt fields are translated, and the French original is kept as the evidence file.
  - The 3 undated photos are listed but left off the timeline.
  - At Step 8 the tester answers "no", and the Skill confirms nothing was created. The tester then checks that no folder, album or note exists.

## Extra checks if time allows

- Two different insurance policies: the Skill asks the user to choose.
- An existing case folder: the Skill asks before updating.
- "Update my Proof Pack": only Conflict, Missing, Weak and Single source items are re-checked.

## Demo recording plan (Round 2, under 3 minutes)

Record core test 1 and the edge case live on the assigned NAS, with English narration.

| Time | Shows |
|---|---|
| 0:00 to 0:20 | The problem: proof for one incident sits in camera events, phone photos and scanned documents. |
| 0:20 to 0:40 | The request, spoken as a user would say it, and the Skill confirming type and window. |
| 0:40 to 1:30 | The timeline and evidence check: Verified, Single source, Missing, and the quote change. |
| 1:30 to 1:55 | The share-safe list flagging the photo with a second person, and the user leaving it out. |
| 1:55 to 2:25 | The confirmation, then the folder, album and cited summary on the NAS, with the originals unchanged. |
| 2:25 to 2:55 | The edge case: Conflict shown with both facts, and "no" at confirmation leaving nothing written. |
