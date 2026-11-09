# Disclosures

Back to [SKILL.md](../SKILL.md).

## Third-party components and open-source libraries

None. This Skill contains only Markdown instructions. It uses UGOS capabilities through the expert agents provided by the UGREEN Skill Developer Package, under that package's license agreement. It includes no scripts, executables, libraries, models or external downloads.

## External services and data flows

- The Skill does not send or share files, photos, camera data or person results outside the NAS, and does not ask any expert agent to do so.
- Document reading, translation, reconciliation and transcription are done by the NAS expert agents, using the AI set up in the user's Uliya settings. The Skill cannot read those settings, so it asks the user before reading documents, because text from them may be processed by a cloud AI service if the user has set one up.
- The Skill makes no direct calls to any third-party API.

## Principal functionality developed for this Hackathon

All of the following was written for this Hackathon:

- the evidence-pack workflow: incident window intake, camera, photo and document sweeps, timeline assembly, evidence check, share-safe review, confirmation and organization
- the five-status evidence rules (Verified, Single source, Conflict, Weak, Missing), the independent-source and time-tolerance rules, and the document version comparison
- the five claim recipes, with their checklists, "weak evidence" rules and reconcile checks
- the before-and-after photo pairing for deposit claims
- the cited summary format, the pack formats, the failure-handling table and the test plan

## AI tools used

- Claude and Claude Code (Anthropic): used to help structure, revise and proofread SKILL.md and the reference files, to compare draft concepts, to align the workflow with the UGOS capability catalog, and to check the files against the hackathon rules. The idea, the claim recipes and the design decisions are the entrant's own.
- UGOS Skill Creation Tool (UGREEN), with a supported AI assistant: used to check the Skill against the Uliya Skill Development Standard.
