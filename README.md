# Contribution #1657: Improve distributed tests for Confusion Matrix (CM and MLCM)

**Contribution Number:** 1  
**Student:** Nafiul Khalid | Florida International University  
**Issue:** [Link](https://github.com/pytorch/ignite/issues/1657)  
**Forked**: [Link](https://github.com/nafiulkhalid/ignite)

**Status:** Phase I Complete

---

## Why I Chose This Issue

**1. Clear and complete instructions by the maintainer:** Maintainer opened the issue with clear instructions and relevant files [comment](https://github.com/pytorch/ignite/issues/1657#issue-812708915). Two issue files and one reference file are given with clear line numbers. Functions already exist in both issue files. I am not building or designing anything from scratch. I am only rewriting the internals of these functions following the reference file. Maintainer has also clearly stated the current and expected conditions of this issue. 

**2. Technically challenging, but doable in 3-4 weeks:** I already understand Python, Pytest, and PyTorch. With this issue, I will get an explosure and practical knowledge about distributed computing patterns. With assistance of Claude, I have drafted a 5-day roadmap to learn the fundamentals of distributed computing, and a 14-day extensive roadmap to learn in-depth of these patterns.

**3. Future Goal aligned:** I aim to be a Software Engineer specialized in Machine Learning (ML). Spending time in this PyTorch library, specifically Ignite, is an investment to my future goal, regardless of PR result. Because distributed-context-aware patterns are common or expected to appear in real ML engineering work.

**4. Checklist score:** This project meets all 6 points in the checklist for me. I understand the problem. I am experienced with the tech stack. There are clear setup documentation and instructions. The issue is doable in 3-4 weeks. There are enough comments and a merged PR added in the issue that collectively give me a good context.

---

## Understanding the Issue

### Problem Description

There are two files `test_confusion_matrix.py` and `test_multilabel_confusion_matrix.py`. Here, the distributed tests are broken. Maintainer provided a reference file, `test_recall.py` for the fix.
The maintainer clearly mentioned that in both CM and MLCM, the test data is generating without considering distributed context. It needs to consider distributed context.
* In technical terms: existing `_test_distrib_multiclass_images()` of CM and MLCM calls `get_y_true_y_pred()`, which returns a fixed 30 * 30 numpy image. As every distributed process/rank uses the same data, the tests aren't actually validating the distributed reduction (all-reduce) logic.
<img width="560" height="140" alt="Screenshot 2026-06-07 at 11 26 18 PM" src="https://github.com/user-attachments/assets/64b57529-403a-496c-b911-3de42445ab69" />

Code from `test_confusion_matrix.py`

<img width="637" height="147" alt="Screenshot 2026-06-07 at 11 24 50 PM" src="https://github.com/user-attachments/assets/66a4c6d8-5018-43a6-a8d4-98fa431f661b" />

Code from `test_multilabel_confusion_matrix.py`

> [!NOTE]
> CM = Confusion Matrix
> 
> MLCM = MultiLabel Confusion Matrix


### Expected Behavior

Data generation should be rank-aware.
* Each process takes its own slice based on `idist.get_rank(), so that the full distributed run collectively covers the entire global dataset – and compute() can be verified against the sklearn result on the whole dataset.
* **Fix**: Only needs to change 'how the data is generated' for CM and MLCM tests, similar to `test_recall.py`.

### Current Behavior

Current distributed tests for CM and MLCM are broken.
* While distributed test functions exist, the data inside them is **rank-unaware**
* **Issue**: When running in a multi-process distributed setup, each process/rank currently generates the same test data, because each rank doesn't account for the distributed context.

### Affected Components

No production code changes are required. 
1. `tests/ignite/metrics/test_confusion_matrix.py` — specifically the `_test_distrib_multiclass_images` helper and its inner _test function
2. `tests/ignite/metrics/test_multilabel_confusion_matrix.py` — the same `_test_distrib_multiclass_images` helper, and the seven commented-out distributed test entry points at the end of the file

---

## Reproduction Process

### Environment Setup

[Notes on setting up your local development environment - challenges you faced, how you solved them]

### Steps to Reproduce

1. [Step 1]
2. [Step 2]
3. [Observed result]

### Reproduction Evidence

- **Commit showing reproduction:** [Link to commit in your fork]
- **Screenshots/logs:** [If applicable]
- **My findings:** [What you discovered during reproduction]

---

## Solution Approach

### Analysis

[Your analysis of the root cause - what's causing the issue?]

### Proposed Solution

[High-level description of your fix approach]

### Implementation Plan

Using UMPIRE framework (adapted):

**Understand:** [Restate the problem]

**Match:** [What similar patterns/solutions exist in the codebase?]

**Plan:** [Step-by-step implementation plan]
1. [Modify file X to do Y]
2. [Add function Z]
3. [Update tests]

**Implement:** [Link to your branch/commits as you work]

**Review:** [Self-review checklist - does it follow the project's contribution guidelines?]

**Evaluate:** [How will you verify it works?]

---

## Testing Strategy

### Unit Tests

- [ ] Test case 1: [Description]
- [ ] Test case 2: [Description]
- [ ] Test case 3: [Description]

### Integration Tests

- [ ] Integration scenario 1
- [ ] Integration scenario 2

### Manual Testing

[What you tested manually and results]

---

## Implementation Notes

### Week [X] Progress

[What you built this week, challenges faced, decisions made]

### Week [Y] Progress

[Continue documenting as you work]

### Code Changes

- **Files modified:** [List]
- **Key commits:** [Links to important commits]
- **Approach decisions:** [Why you chose certain approaches]

---

## Pull Request

**PR Link:** [GitHub PR URL when submitted]

**PR Description:** [Draft or final PR description - much of the content above can be adapted]

**Maintainer Feedback:**
- [Date]: [Summary of feedback received]
- [Date]: [How you addressed it]

**Status:** [Awaiting review / Iterating / Approved / Merged]
---

## Learnings & Reflections

### Technical Skills Gained

[What you learned technically]

### Challenges Overcome

[What was hard and how you solved it]

### What I'd Do Differently Next Time

[Reflection on your process]

---

## Resources Used

- [Link to helpful documentation]
- [Tutorial or Stack Overflow post that helped]
- [GitHub issues or discussions that helped]

