# Pack formats

Back to [SKILL.md](../SKILL.md).

## Timeline

One numbered line per item, oldest first. Times use the NAS time zone and are shown as `YYYY-MM-DD HH:MM`.

```
T1  2026-11-03 22:41  CAMERA  Driveway: Vehicle, 22:41 to 22:43           [event: Driveway 22:41]
T2  2026-11-04 07:58  PHOTO   Rear bumper, close-up                        [/Photos/2026/11/IMG_1182.jpg]
T3  2026-11-04 08:00  PHOTO   Rear bumper, wide                            [/Photos/2026/11/IMG_1183.jpg]
T4  2026-11-05 11:20  DOC     Repair quote, 185,000 (Sample Motors)        [/Documents/Car/quote-v1.pdf]
T5  2026-11-07 09:05  DOC     Revised repair quote, 210,000 (Sample Motors) [/Documents/Car/quote-v2.pdf]
--  (any date)        DOC     Policy SAMPLE-0001, cover 2026-02-01 to 2027-01-31, excess 50,000
                                                                           [/Documents/Car/policy-schedule.pdf]
```

Items without a date (for example a policy document) are listed after the dated items. Undated photos, which are looked for only in a folder the user names, are listed separately and never placed on the timeline.

## Evidence check

```
CONFLICT
- (none)

MISSING
- Police report reference. Searched documents dated 2026-11-03 to 2026-12-03 for "police", "report". None found.

WEAK
- (none)

SINGLE SOURCE
- Repair quote: quote-v2.pdf (current version)

VERIFIED
- Damage occurred in window: camera event T1 and photos T2, T3 the next morning
- Insurance cover: policy-schedule.pdf; incident date 2026-11-03 is inside 2026-02-01 to 2027-01-31

CHANGES
- Repair quote amount: 185,000 → 210,000 (quote-v1.pdf → quote-v2.pdf)

CHECKS
- Current quote 210,000 is above the excess of 50,000.
```

## Share-safe list (Step 7)

```
These photos show people you didn't list. Keep or leave out each one?
- IMG_1190.jpg: 1 unnamed person
- IMG_1191.jpg: "Tunde" (named person)
Leaving a photo out only removes it from the pack. The original stays where it is.
I can only spot people your NAS has already grouped. Please look through the photos before sharing.
```

## Summary note

```
<case name> - summary

What the evidence shows
A vehicle event was recorded on the Driveway camera at 22:41 on 3 November [T1].
Photos of rear bumper damage were taken the next morning at 07:58 and 08:00 [T2, T3].
Sample Motors quoted 185,000 on 5 November and revised it to 210,000 on 7 November [T4, T5].
The incident date is inside the policy cover period [policy-schedule.pdf].

Timeline
(timeline as above)

Evidence check
(evidence check as above)

This pack organizes your own files. It is not legal or insurance advice.
```

## Confirmation message (Step 8)

```
Here is what I will create. Nothing will be moved or deleted. (The folder shown is an example; use the real path from Step 8.)

Folder:  /Personal/Proof Pack/vehicle-damage-2026-11-03/
Copy in: 5 files (2 photos, 3 documents)
Album:   "Proof Pack: vehicle-damage-2026-11-03" (2 photos; 1 left out at your request)
Note:    "vehicle-damage-2026-11-03 - summary"

Remove anything first, or say "yes" to create the pack.
```

## Completion message

```
Pack created.
- Folder: /Personal/Proof Pack/vehicle-damage-2026-11-03/ (5 files copied)
- Album: "Proof Pack: vehicle-damage-2026-11-03" (2 photos, 1 left out)
- Note: "vehicle-damage-2026-11-03 - summary"

Missing: Police report reference.
Say "update my Proof Pack for vehicle-damage-2026-11-03" when you have it.

This pack organizes your own files. It is not legal or insurance advice.
```
