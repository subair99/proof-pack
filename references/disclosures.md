# Disclosures

Back to [SKILL.md](../SKILL.md).

## Third-party components and open-source libraries

None. This Skill contains only Markdown instructions. It uses UGOS capabilities through the expert agents provided by the UGREEN Skill Developer Package, under that package's license agreement. It includes no scripts, executables, libraries, models or external downloads.

## External services and data flows

- Default: all processing runs on the NAS local AI model. No data leaves the NAS.
- Optional: if the user's Uliya settings route text processing to a supported cloud AI service, only extracted document text is sent, and only after the user agrees in the session. Camera event data, photos, person information and original files are never sent.
- The Skill makes no direct calls to any third-party API.

## Principal functionality developed for this Hackathon

All of the following was written for this Hackathon:

- the evidence-pack workflow: incident window intake, camera, photo and document sweeps, timeline assembly, evidence check, share-safe review, confirmation and organization
- the five-status evidence rules (Verified, Single source, Conflict, Weak, Missing), the independent-source and time-tolerance rules, and the document version comparison
- the five claim recipes, with their checklists, "weak evidence" rules and reconcile checks
- the before-and-after photo pairing for deposit claims
- the cited summary format, the pack formats, the failure-handling table and the test plan

## AI tools used

- Claude (Anthropic): used to help structure and proofread SKILL.md and the reference files, to compare draft concepts, and to check the files against the hackathon rules. The idea, the claim recipes and the design decisions are the entrant's own.
- UGOS Skill Creation Tool (UGREEN), with a supported AI assistant: used to check the Skill against the Uliya Skill Development Standard.
