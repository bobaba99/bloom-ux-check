# Research Grounding

This skill's methodology is grounded in peer-reviewed HCI research. This
reference documents the empirical evidence behind each phase, helping agents
explain *why* these checks matter — not just what to do.

---

## Why Combine Multiple Methods (Phase Architecture)

The skill uses personas + heuristic evaluation + cognitive walkthrough +
automated checks in concert. This is not arbitrary — research consistently
shows no single usability method is sufficient.

**Key finding:** "No single method has revealed any significant results
indicating that it is singularly effective in all circumstances. A combination
of different techniques that complement one another should preferably be used
as their collective application will be more powerful than applied in
isolation." — Jaspers (2009), *International Journal of Medical Informatics*,
656 citations.

**Empirical support:**
- John & Marks (1997) tracked 6 UE methods through a full development cycle
  and concluded "predictive methods are not as effective as the HCI field
  would like" when used alone. *Behaviour & Information Technology*, 137
  citations.
- Kushniruk et al. (2015) developed a hybrid HE + CW method that
  "leverages the advantages of both" and found it effective for EHR systems.
  *Studies in Health Technology and Informatics*, 20 citations.
- Zhang, Basili & Shneiderman (1998) found perspective-based inspection
  (splitting evaluation into focused perspectives) found 30% more usability
  problems than heuristic evaluation alone. *Proceedings of the Human
  Factors and Ergonomics Society*, 34 citations.

**Application to this skill:** Each phase covers a different perspective:
heuristics catch structural issues, cognitive walkthrough catches learnability,
personas catch goal-specific friction, and automated checks catch invisible
runtime bugs. Together they provide coverage no single method achieves.

---

## Phase 2: Persona Generation — Empirical Basis

**Key finding:** In a 34-participant controlled experiment, personas afforded
significantly faster task completion and higher user identification accuracy
than raw analytics. "Qualitative analysis of think-aloud transcripts shows
that personas have other benefits regarding learnability and consistency." —
Salminen et al. (2020), *CHI Conference on Human Factors*, 53 citations.

**Why personas improve heuristic evaluation specifically:**
- Friess (2015) found that persona-based HE evaluators were "more likely to
  report findings related to navigation" and "include language that directly
  references users and user needs." Evaluators who created their own personas
  were "more likely to include complex issues in their reports." — *IEEE
  Transactions on Professional Communication*, 22 citations.
- Kneale et al. (2017) extended heuristic evaluation with task-based
  scenarios and personas of older adults and found it could "identify
  potential usability challenges for specific types of users" and "better
  understand how the interactions of different users may vary." — *Journal
  of Biomedical Informatics*, 13 citations.

**Theoretical foundation:** Pruitt & Grudin (2003) established that personas
are "more engaging than design based primarily on scenarios" because they
create empathy and provide "a conduit for conveying a broad range of
qualitative and quantitative data." — 979 citations.

**AI-generated personas caveat:** Kaate et al. (2025) identified the "fourth
wall" effect in AI-generated personas — users feel they're "talking to someone
describing the persona rather than the persona itself." Chat-format personas
were perceived more favorably, but profile personas led to better task
performance despite lower usability scores. — *Behaviour & Information
Technology*, 3 citations.

**Application to this skill:** Personas ground abstract heuristics in concrete
user motivations. The skill requires agents to generate specific, realistic
personas (not archetypes) because Friess showed that evaluator-created
personas produce more complex findings. The diversity checklist ensures
coverage across technical comfort, constraints, and domain variety.

---

## Phase 3: Heuristic Evaluation — Empirical Basis

**Key finding:** Heuristic evaluation finds ~5x more individual problems than
user testing, but user testing finds slightly more severe problems. — Maguire
& Isherwood (2018), 16 citations.

**Effectiveness varies with evaluator expertise:**
- Law & Hvannberg (2004) found that while Nielsen's heuristics outperformed
  alternatives (Gerhardt-Powals' cognitive engineering principles), "in
  general the effectiveness of HE was low" for novice evaluators. — *Nordic
  Conference on HCI*, 84 citations.
- Abulfaraj & Steele (2021) addressed this by converting heuristics into
  specific questions based on interviews with 15 usability experts, making
  them more actionable for non-experts.

**Why Nielsen's 10 specifically:**
- Kurosu et al. (1997) found that splitting evaluation into focused sessions
  (each covering a subset of heuristics) improved detection, suggesting the
  structure of the evaluation matters as much as the heuristics themselves.

**Application to this skill:** The skill uses a question-based scoring format
(score 0-4 per heuristic) rather than open-ended evaluation, because research
shows structured approaches improve novice evaluator effectiveness. The
detailed scoring guide in `heuristic-checklist.md` operationalizes each
heuristic into testable criteria.

---

## Phase 4: Accessibility — Empirical Basis

**Critical finding:** Automated accessibility tools cover at most 50% of WCAG
success criteria, with completeness between 14-38%. "Relying on just automated
tests entails that 1 of 2 success criteria will not even be analysed and
among those analysed, only 4 out of 10 will be caught." — Vigo, Brown &
Conway (2013), *International Cross-Disciplinary Conference on Web
Accessibility*, 211 citations.

**Corroborating evidence:**
- Abduganiev (2017) found maximum 32.4% coverage across 8 tools and
  concluded "applying such approaches alone is a great mistake since
  subjective and less frequent objective success criteria failed to be
  automated." — 34 citations.
- Kodithuwakku & Wickramaarachchi (2025) confirmed these findings remain
  current, recommending QA engineers use automated tools as a starting
  point but rely on manual testing for comprehensive coverage.

**Application to this skill:** The skill runs automated checks first (images
without alt text, unlabeled inputs, empty buttons) as a fast sweep, then
requires a keyboard-only walkthrough for the checks automation can't cover
(focus order, modal escape, custom widget keyboard behavior). This dual
approach reflects the research consensus that automation + manual inspection
together provide significantly better coverage than either alone.

---

## Phase 5: Cognitive Walkthrough — Empirical Basis

**Key finding:** The cognitive walkthrough focuses specifically on "exploratory
learning — first-time use without formal training," motivated by the
observation that "people generally learn to use new computer-based systems by
exploration." — Rieman, Franzke & Redmiles (1995), *CHI Conference Companion*,
200 citations.

**Complementary to heuristic evaluation:**
- Ginting et al. (2021) compared HE and CW directly: HE found more problems
  in "efficiency, memorability, and satisfaction" aspects, while CW found
  more problems in "learnability and error" aspects. CW found higher
  severity problems (average 3 vs HE's average 2). — 24 citations.

**Limitation acknowledged:** John & Mashyna (1997) found that CW "may not
predict most usability errors found empirically" but "the technique is
currently learnable and usable." This means CW is a complement to, not
replacement for, other methods.

**Application to this skill:** The skill's 4-question cognitive walkthrough
(will they know what to do? notice the right action? understand feedback?
recover from mistakes?) is a simplified version of the standard CW protocol.
It's positioned after heuristic evaluation because CW catches learnability
issues that heuristics miss, and before persona flows because it identifies
structural barriers that would affect all personas.

---

## Phase 6: Empty States & First-Time Experience — Empirical Basis

**Key finding:** Loading screen design significantly affects users' emotional
experience, time perception, and tolerable waiting time. Logo-based loading
feedback outperformed empty screens. — Wang et al. (2021), *Displays*, 22
citations.

**Onboarding effectiveness:**
- Fröhlich et al. (2021) found "well-designed onboarding processes can
  improve the perceived usability of first-time users for feature-rich
  mobile apps" using the minimalist instruction framework. — *DIS
  Conference*, 15 citations.
- Megyeri et al. (2021) confirmed via eye tracking that "user onboarding
  solutions are generally effective." — *Ergonomics in Design*, 2 citations.
- Barnett et al. (2018) found FTUE affects the "information quality"
  dimension of usability in games. — *Entertainment Computing*, 16 citations.

**Application to this skill:** Empty state testing is often overlooked because
developers always test with pre-populated data. The research shows first
impressions (loading, empty, onboarding) have outsized impact on perceived
usability and user retention. The skill explicitly tests these states to catch
issues developers don't encounter in their own workflows.

---

## References (Alphabetical)

- Abduganiev, S.G. (2017). Towards Automated Web Accessibility Evaluation:
  A Comparative Study. *Int. J. of IT and Computer Science*, 9(9), 34 cit.
- Abulfaraj, A. & Steele, A. (2021). Operational Usability Heuristics.
  *HCII*, 0 cit.
- Barnett, L. et al. (2018). First Time User Experiences in mobile games.
  *Entertainment Computing*, 16 cit.
- Friess, E. (2015). Personas in Heuristic Evaluation. *IEEE Trans. on
  Professional Communication*, 22 cit.
- Fröhlich, M. et al. (2021). Is it Better With Onboarding? *DIS*, 15 cit.
- Ginting, L.M. et al. (2021). Comparison of HE and CW Methods. *JAMIKA*,
  24 cit.
- Jaspers, M. (2009). A comparison of usability methods for testing
  interactive health technologies. *Int. J. Med. Informatics*, 656 cit.
- John, B. & Marks, S. (1997). Tracking the effectiveness of usability
  evaluation methods. *Behaviour & IT*, 137 cit.
- John, B. & Mashyna, M. (1997). Evaluating a Multimedia Authoring Tool
  with CW and Think-Aloud. 23 cit.
- Kaate, I. et al. (2025). The 'fourth wall' and other usability issues in
  AI-generated personas. *Behaviour & IT*, 3 cit.
- Kneale, L. et al. (2017). Using scenarios and personas to enhance
  heuristic evaluations. *J. Biomedical Informatics*, 13 cit.
- Kodithuwakku, T. & Wickramaarachchi, D. (2025). Assessing the Limits of
  Automated Accessibility Testing. *ICARC*, 1 cit.
- Kurosu, M. et al. (1997). Structured heuristic evaluation method (sHEM).
  *IEEE ICSMC*, 13 cit.
- Kushniruk, A. et al. (2015). Integrating HE with CW: A Hybrid Method.
  *Studies in Health Technology*, 20 cit.
- Law, E. & Hvannberg, E. (2004). Strategies for improving HE
  effectiveness. *NordiCHI*, 84 cit.
- Maguire, M. & Isherwood, P. (2018). Comparison of User Testing and HE.
  16 cit.
- Megyeri, M. et al. (2021). Investigating Effectiveness of User Onboarding
  with Eye Tracking. *Ergonomics in Design*, 2 cit.
- Pruitt, J. & Grudin, J. (2003). Personas: practice and theory. 979 cit.
- Rieman, J. et al. (1995). Usability evaluation with the cognitive
  walkthrough. *CHI Companion*, 200 cit.
- Salminen, J.O. et al. (2020). Personas and Analytics: A Comparative User
  Study. *CHI*, 53 cit.
- Vigo, M. et al. (2013). Benchmarking web accessibility evaluation tools.
  *W4A*, 211 cit.
- Wang, Y. et al. (2021). Effect of loading pages on users' mental state.
  *Displays*, 22 cit.
- Zhang, Z. et al. (1998). Perspective-Based Usability Inspection. *HFES*,
  34 cit.
