# Verdicts

> Skeleton. This is the rubric: Trevor labels against it and the judge prompt is derived from it. If the two ever disagree, this page wins and the prompt is fixed.

## One judgment

For each requirement the judge returns four things, in this order, so that the reasoning comes before the answer:

| Field | Content |
|---|---|
| `evidence` | Quotes from the wiki, each with the page it came from. May be empty. |
| `rationale` | Two or three sentences linking the evidence to the requirement. |
| `verdict` | `yes`, `no` or `unclear` |
| `flag` | Optional. Set when the judge wants a human to look even though it gave a verdict. |

## What each verdict means

**yes**: the wiki shows Trevor meets the requirement. At least one quote supports it.

**no**: the wiki shows he does not meet it, or the wiki covers the area thoroughly enough that its absence is informative. *(Draft wording. See the open question below.)*

**unclear**: the wiki does not say enough either way. This is a gap in the wiki, and it becomes a question for Trevor.

## Hard calls

These need a written rule each, with one worked example Trevor agrees with. They are also where the hard eval cases come from.

- **Adjacent experience.** The posting wants tool X, the wiki shows tool Y that does the same job. **OPEN**
- **Year thresholds.** "5+ years" when the wiki shows 4. **OPEN**
- **Soft requirements.** "Strong communicator", "thrives in ambiguity". **OPEN**
- **Compound requirements.** "Python and Go": one item or two? **OPEN** (proposal: extraction splits them)
- **Absence.** When does silence in the wiki mean no rather than unclear? **OPEN**
- **Not about skills.** Location, work authorisation, salary, clearance. **OPEN** (proposal: judged the same way, tagged `logistics`, and a no here can sink the fit on its own)

## Rolling up

Done in code, never by the model, so that the same verdicts always give the same fit.

| Input | Proposal |
|---|---|
| Required items | Any no is reported at the top. Fit needs most of them yes. |
| Preferred items | Counted, shown, never blocking. |
| Unclear items | Shown as a range: fit if all resolve yes, fit if all resolve no. |

**OPEN**: the actual thresholds, and whether the output is a label (strong / possible / poor), a score, or both.

## What the report shows

- The fit, with the range that unclear items leave open
- Required and preferred counts: yes / no / unclear
- Every judgment, with its quotes
- The questions that would settle the unclear items, most valuable first
