---
name: no-comments
description: >-
  Hand a diff's code comments to a fresh reviewer who deletes them,
  then fix the workarounds they excused at the root cause.
disable-model-invocation: true
metadata:
  author: aka-cronos
  adapted-from: cursor/plugins pstack no-comments (MIT, Lauren Tan)
---

# No comments

Agents copy what already exists. A comment excusing a workaround is a **weed**: the next
agent reads it as permission and plants another. This skill is the weedkiller. A
**fresh reviewer** that did not write the code deletes every comment outside a short keep
list; you then remove the workarounds those comments were excusing.

Defer to the reviewer's fresh eyes. An author defends its comments; that is why the pass
runs in a separate agent.

## Scope

Use the files or diff the caller names. Otherwise use the current diff against the base
branch (default `main`), working tree included.

## Steps

### Step 1: Spawn the reviewer

Spawn one fresh subagent whose prompt is the full contents of
[references/comment-reviewer.md](references/comment-reviewer.md) followed by the scope.
Give it a judgment-grade model at standard speed. It edits comments only.

If the host cannot spawn a subagent, report that `/no-comments` needs one and stop.

**Done when:** the reviewer has returned its report and its comment deletions sit in the
working tree.

### Step 2: Audit the report

Read the report and the diff it produced. Reject:

- edits to application code, or anything outside the scope;
- deletions of comments that match a keep-list entry, which you restore with the exact
  entry and proof it applies;
- `MUST KILL` flags whose stated reason is false, or that condemn code kept on purpose.

A `MUST KILL` on a surprise in our own code stays actionable; its comment stays deleted.
Scan the scope for lint and type suppressions the reviewer missed. A suppression that
protects correctness or safety becomes a `MUST KILL`.

When a kill or keep rests on a thin `IMPORTANT` or `do not remove`, **trace the claim**
yourself, as the reviewer prompt's "Trace the claim" section defines it. A keep survives
only when the trace proves it is about something we cannot change, true today on a live
path. An ambiguous kill stays deleted; an ambiguous keep is deleted.

If the report fails the audit, revert its edits and rerun Step 1 once on a fresh subagent,
naming the failure in its prompt. If the second report also fails, revert it, report the
pass open, and stop.

**Done when:** every deletion and every flag is accepted, restored, or rejected, each with
its reason.

### Step 3: Fix the flags at the root cause

For each accepted flag, reproduce the behaviour or read its diagnostic, and trace it to the
first wrong decision. If the state that triggers it is legitimate, handle that state where
the code fails; if it should never exist, fix the code that produces it.

Fix trivial accepted flags directly: delete the dead path, drop the parameter, call the
real API.

For a flag that needs a new shape, ask: if this requirement had been there from day one,
what would the code look like? Sketch that shape once for the whole accepted set (the
types, signatures, and module boundaries) in the report, then implement the smallest
in-scope step toward it. Each fix removes the workaround the comment excused and leaves
the code simpler than it found it; a symptom guard on the old shape is a new weed.

When the root cause lies outside the scope, land the smallest in-scope fix and report the
rest open.

Run the project's relevant checks (tests, type check, lint) after the fixes. When a flag on
a deleted lint or type suppression ends open and the checks fail, restore that suppression
and rerun them. Keep it only if the rerun passes; otherwise delete it again and report the
checks red. The report carries the flag either way.

**Done when:** every accepted `MUST KILL` is fixed in scope with the checks green, or
reported open with its cause: out of scope, or verification failed or unavailable.

### Step 4: Encode claimed constraints

A **constraint comment** claims a rule: `do not remove`, `do not change wording`, `talk to
X before changing`. Keep-list comments about things we cannot change stay. Every other
constraint comment is already deleted; the reviewer's report carries the rule it claimed.
Encode only rules that hold today. A claim Step 2 refuted is dropped, and one guarding a
workaround under a `MUST KILL` is settled by its Step 3 fix; neither needs a check. For each
remaining rule, offer the cheapest in-scope enforcement: a type, a runtime check, a test,
or a CI lint. Wait for the user to approve; an unattended run needs the caller's approval up
front.

Approved: encode the rule, then run the new check and watch it pass. A failing or
unrunnable check leaves the constraint open, reported with its cause. Declined: report the
constraint as unenforced. Either way, the comment stays deleted.

**Done when:** each constraint is kept under the keep list, settled by Step 2 or Step 3,
encoded with a passing check, or reported open or unenforced with its claimed rule.

### Step 5: Report

Report: deletion count, restored comments, reruns, the shape sketch, fixes, encoding
offers, encodings, check results, open or unenforced constraints, and other open work.
