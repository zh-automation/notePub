# V201 Batching Process — Task List

**Source:** Text conversation between C (+ Omar) and Z  
**Vessel:** V201  
**Date:** April 23, 2026

---

## Current Behavior (Existing Automation)

The system currently automates **one material at a time**. Here's how operators use it today:

1. Operator enters the **final batch weight** and presses **Calculate** — the system auto-populates the "Target (lb)" column for each raw material (mineral oil, Hypermer, G946-Atlas).
2. Operator reviews the calculated targets. If correct, they press **Enable** next to a raw material, then press **Start**.
3. The system pumps that single material to the target weight, then automatically stops the pump and closes the valve.
4. Operator visually confirms the weight, then presses **Accept**.
5. Operator presses **Tare Scale**, then **Net Weight** to zero out the scale.
6. Operator manually repeats steps 2–5 for each remaining material.
7. The agitator currently turns on automatically when the second product is being added.

---

## Requested Changes (New Automation)

C and Omar want to eliminate the manual steps between materials so the operator can **press Start once and walk away**. The full requested sequence is:

### Task List

- [ ] **1. Recipe selection** — Operator selects the recipe and enters weights for materials, then presses Start. *(No change — already exists.)*

- [ ] **2. Auto-add Mineral Oil** — System adds mineral oil to target weight, then stops the pump and closes the valve automatically. *(No change — already exists.)*

- [ ] **3. Auto-accept & tare after Mineral Oil** — Once mineral oil hits target weight, the system should automatically accept the weight, tare the scale, and move to the next material **without operator intervention**. *(NEW)*

- [ ] **4. Agitator starts after Mineral Oil** — The agitator should turn on after mineral oil reaches its target weight and remain on through the rest of the batch (including during Silverson mixing and after). *(CLARIFIED — currently turns on at second product addition; confirm behavior matches.)*

- [ ] **5. Auto-add Hypermer** — System moves to Hypermer and adds to target weight. If the target is zero pounds, it should recognize that, skip it, and move to the next material. *(NEW — zero-weight skip logic.)*

- [ ] **6. Auto-accept & tare after Hypermer** — Same as Task 3 but for Hypermer. Auto-accept, tare, and advance. *(NEW)*

- [ ] **7. Auto-add G946-Atlas** — System adds G946-Atlas to target weight, then stops the pump and closes the valve. *(NEW — auto-advance to this step.)*

- [ ] **8. Auto-accept & tare after G946-Atlas** — Auto-accept the weight and tare the scale after G946-Atlas addition. *(NEW)*

- [ ] **9. Silverson auto-start & 5-min mix** — After all materials are added, the Silverson starts automatically and runs for 5 minutes to mix. *(NEW — C noted this may be added later.)*

- [ ] **10. Agitator remains on after Silverson** — The agitator stays running even while the Silverson is on and continues running after the Silverson's 5-minute cycle completes. *(NEW)*

- [ ] **11. Switch scale to gross weight** — After all additions and mixing, the scale should display **gross weight** so the operator can confirm the total batch weight. *(NEW)*

---

## Open Questions / Notes

- **Silverson timing:** C said "I don't know if this is new and this can be added later" — confirm whether Silverson auto-start is in scope for the initial release or deferred.
- **Testing:** Omar mentioned mineral oil was being transferred to Liquid Blend and testing may happen the following morning. Coordinate with Omar on test schedule.
- **Material order is fixed:** Mineral Oil → Hypermer → G946-Atlas (always in this sequence).
- **Hypermer is rarely used** but must still be handled (including the zero-weight skip case).
