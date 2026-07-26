# Fleet Governance
### Problem → emergent solution → principle

Abstract skeleton of how governance fits the tooling. Mechanisms are omitted: the implementation is fitted to the specific system it serves and would not transfer. Each entry is a problem actually hit, what emerged in response, and the principle it left behind.

---

**1. Skill/process loading was probabilistic.**
- Problem: whether an agent loaded the right process depended on the model recognizing the situation — a probability, missed often.
- Emerged: enforced routing of process into context, at a real cost in context space and interruption.
- Principle: enforce what matters; don't rely on model recall. Predictable beats probable. (Also the origin of the wider build: the stock harness is a kernel; production needs the distribution on top.)

**2. Trained bias survives every gate.**
- Problem: models arrive trained to avoid admitting mistakes, to produce satisfying-looking output fast, to prefer "looks done" over correct. No mechanical check catches a disposition.
- Emerged: standing instructions carried by every agent — mistakes are expected, surfacing them is the job. Observed effect: honest adversarial reviews, mistakes reported and owned instead of obscured, which is what makes peer correction possible.
- Principle: mechanics and intent must play together. The harness closes what can be closed from outside; culture reshapes what can only be reached from inside.

**3. Fleet velocity outruns human attention.**
- Problem: with even a few agents in parallel, following their reasoning and conversations is futile — machine speed vs. human bandwidth is arithmetic, not discipline.
- Emerged: distillation — agents report final outcomes, not thought streams; watching roles compress what rises; the human is a point of contact, not a participant in every loop. Modeled on how companies already route information: funnels, not broadcast.
- Principle: human judgment moves from run-time to design-time. Authority becomes a property of the environment — what may run, what must be earned, what is closed, who can change the answers.

**4. A new control is itself a risk.**
- Problem: a control that acts before it is trusted is a new outage.
- Emerged: everything ships observe-only — logs what it would do, does nothing — and is promoted to enforcing only on evidence, with a test pinning the bad case shut.
- Principle: soft before hard. Governance earns enforcement.

**5. Agents grade their own homework.**
- Problem: success gets declared against the agent's narration of the work, not the work.
- Emerged: verification against the real artifact — actual file, actual device state, actual result.
- Principle: the artifact is the evidence. Trust is a function of checking, not claiming.

**6. Self-review is not review.**
- Problem: the builder judging its own output misses what any second reader would catch — same as with people.
- Emerged: role separation — build, coordinate, watch — with the judging function independent of the doing function.
- Principle: peer review, applied to machines.

**7. Scope widens silently.**
- Problem: agents drift into adjacent actions nobody sanctioned; autonomy accretes unless something closes it.
- Emerged: design-time human authority — which gates exist, what may run autonomously, standing revocation. Enforcement is structural and sandboxed; it does not depend on the model choosing to comply.
- Principle: the human lever exists by construction, not by goodwill.

**8. Subtle damage outruns the sandbox.**
- Problem: a weak reasoner's damage is rarely the obvious destructive command a stock sandbox catches — it's the plausible edit that poisons state downstream, visible only later.
- Emerged: upfront damage control aimed at that slow-leak class, not just the immediate blast radius.
- Principle: govern for over-the-horizon damage. Side effect: older/weaker models fail predictably and correctably instead of invisibly — reliability lives in the structure, not the parameter count.

**9. Long runs degrade as context fills.**
- Problem: agents lose their working state to context exhaustion mid-task.
- Emerged: giving the agent sight of its own context budget, so it can checkpoint and compact with its working state mostly intact.
- Principle: an agent that can see its own limits can degrade gracefully; one that can't, can't.

**10. The iteration loop outgrew the human.**
- Problem: past a certain maturity the fleet recognizes its own needs and iterates faster than its human can track — oversight by comprehension stops being possible even while the system works.
- Emerged: representation — a permanent role guarding the human's interests inside the loop, surfacing friction the human can't articulate. Plus deliberate surrender of per-item control in exchange for documented, visible, auditable workloads.
- Principle: control at fleet scale is delegation with receipts, not grip.

**11. Bespoke rots without comparison.**
- Problem: a roll-your-own stack can quietly fall behind what the industry now provides.
- Emerged: periodic honest assessment of the homegrown build against emerging capabilities and trends.
- Principle: roll-your-own must keep re-earning itself.

**12. Governance itself drifts.**
- Problem: the doctrine ages; levers stop matching the friction the fleet actually hits.
- Emerged: friction mined from the fleet's own transcripts and surfaced in its own retrospectives; working levers kept, missing levers named and built. Maintenance treated as a standing job.
- Principle: governance is a living system, revised by the same evidence standards it imposes.

---

Boundary note: human-supervised, R&D-grade. Runs on unremarkable models and ships; still missing things not yet articulated.
