# Pending Upstream Patches

Patches prepared from the C++26 contracts work for submission to
`gcc-patches@gcc.gnu.org`, one directory per patch.  A patch lives here rather
than in a build tree so that it survives a clean while it waits for review.

Each row ties together three things kept in three different places:

* the **bug** in [`../gnu_gcc/bug-reports/`](../gnu_gcc/bug-reports/README.md),
  which is the report-ready writeup and the reproducer;
* the **branch commit** in
  [`../gnu_gcc/branch-history/`](../gnu_gcc/branch-history/), which is where
  the fix exists today;
* the **patch directory** here, once one has been prepared.

Relative links assume the usual side-by-side `~/repos` layout.

A row leaves this file when upstream commits the change; the durable record is
then upstream's own history, plus the
`Fixed upstream by us` table in `../gnu_gcc/bug-reports/README.md`.

## Submitted, awaiting response

| Patch directory | Bug | PR | Posted | Status |
|---|---|---|---|---|
| [`upstream-patch-pr127281-retval-double-destroy/`](upstream-patch-pr127281-retval-double-destroy/) | [GCC-5](../gnu_gcc/bug-reports/gcc-05/gcc-05-contract-retval-double-destroy.md) | [PR127281](https://gcc.gnu.org/bugzilla/show_bug.cgi?id=127281) | 2026-09-16, [gcc-patches 731369](https://gcc.gnu.org/pipermail/gcc-patches/2026-September/731369.html) | awaiting review |

Contract on a function returning a non-trivially-destructible class
double-destroys the return value.  Carried on the branch by
[`1240-retval-cleanup-not-respliced`](../gnu_gcc/branch-history/1000-p2900-fixes/1240-retval-cleanup-not-respliced.md);
the submitted form is the single commit on
`upstream-patch/pr127281-retval-double-destroy` in `~/repos/gnu_gcc-patch`.

The posting mentions the follow-up
[PR127414](https://gcc.gnu.org/bugzilla/show_bug.cgi?id=127414) (GCC-44, a
violation handler throwing out of a postcondition leaks the returned object).
That one is **not** a candidate for the next patch: its fix depends on
`1130-result-binding-one-object`, which is interleaved with `4000-p3098`.

## Landed

| Patch directory | Bug | PR | Upstream commit | Landed |
|---|---|---|---|---|
| [`upstream-patch-pr127282-assume-side-effect-leak/`](upstream-patch-pr127282-assume-side-effect-leak/) | GCC-9 | [PR127282](https://gcc.gnu.org/bugzilla/show_bug.cgi?id=127282) | [`7b60a368fb1`](https://gcc.gnu.org/git/?p=gcc.git;a=commit;h=7b60a368fb19b33f8676482a233d389cbc47b85e) | 2026-09-14 |

Nested `[[assume]]` leaks the inner operand's side effects during constant
evaluation.  The first patch from this work to land.  The directory and the
`upstream-patch/pr127282-assume-side-effect-leak` branch were deliberately
left in place after it landed rather than deleted.

Two things learned from it, both worth re-reading before the next submission:
upstream may reshape a patch, and the next rebase must take **their** form
rather than merging the two; and renaming on the branch to pre-match a
submitted patch's spelling makes that rebase cheap.

## Candidates: simple, orthogonal fixes

Rows from `../gnu_gcc/bug-reports/README.md` that are still open upstream,
fixed on this branch, and whose fix can stand on its own upstream.  Selected by
reading each branch-history entry against three tests: `depends:` is empty or
resolves inside this set, the entry's `Compile gap` section is `None`, and the
contents are confined to `gcc/cp/` plus tests that need no branch-only option.

**"Orthogonal" is a claim about the fix, not about the commit.**  The two come
apart, and conflating them is how GCC-29 was nearly left off this list.  A
branch commit may carry a trivial fix and still have a long `depends:` list,
because the commit was sized to own a whole function that several papers edit.
The question is what an upstream patch would have to contain, not what our
commit happens to contain.  Where the two differ, the `Notes` column says so.

| Bug | Summary | Upstream PR | Branch entry | Shape of the fix | Notes |
|---|---|---|---|---|---|
| [GCC-3](../gnu_gcc/bug-reports/gcc-03/gcc-03-constexpr-repeat-call.md) | A contract condition that re-calls a constexpr function already called in the same constant evaluation is wrongly rejected as non-constant | [PR125459](https://gcc.gnu.org/bugzilla/show_bug.cgi?id=125459) | [`1030-constexpr-repeat-call`](../gnu_gcc/branch-history/1000-p2900-fixes/1030-constexpr-repeat-call.md) | one `put_value` body | listed `depends: [1020-constexpr-side-effect]` for review order only; the entry states nothing here needs 1020's fields |
| [GCC-10](../gnu_gcc/bug-reports/gcc-10/gcc-10-lambda-this-capture-in-contract.md) | Contract predicate on a lambda capturing `this` reads the closure object as the enclosing class | [PR127283](https://gcc.gnu.org/bugzilla/show_bug.cgi?id=127283) | [`1180-lambda-this-capture-remap`](../gnu_gcc/branch-history/1000-p2900-fixes/1180-lambda-this-capture-remap.md) | one added type test in an existing walker | ours |
| [GCC-11](../gnu_gcc/bug-reports/gcc-11/gcc-11-lambda-in-postcondition-result-name.md) | Lambda in a non-member postcondition with a result name fails to parse | [PR127284](https://gcc.gnu.org/bugzilla/show_bug.cgi?id=127284) | [`1160-lambda-in-postcondition-result-name`](../gnu_gcc/branch-history/1000-p2900-fixes/1160-lambda-in-postcondition-result-name.md) | two small guards, no new interfaces | ours |
| [GCC-14](../gnu_gcc/bug-reports/gcc-14/gcc-14-contract-capture-note-garbage-location.md) | Garbage source location on a contract-capture diagnostic note | [PR126041](https://gcc.gnu.org/bugzilla/show_bug.cgi?id=126041) | [`1150-capture-note-location`](../gnu_gcc/branch-history/1000-p2900-fixes/1150-capture-note-location.md) | how one note's location is computed | |
| [GCC-16](../gnu_gcc/bug-reports/gcc-16/gcc-16-lambda-capture-contract-in-template.md) | Contract on a capturing lambda inside an instantiated template segfaults | [PR126038](https://gcc.gnu.org/bugzilla/show_bug.cgi?id=126038) | [`1230-lambda-capture-bind-through-statement-list`](../gnu_gcc/branch-history/1000-p2900-fixes/1230-lambda-capture-bind-through-statement-list.md) | two hunks rewriting one block in `contracts.cc` | **partial** -- fixes the non-template shape (`pr126038.C` passes on that commit alone); the template shape also needs `4640-contract-substitution-plumbing`, which is not orthogonal |
| [GCC-22](../gnu_gcc/bug-reports/gcc-22/gcc-22-pack-reference-constification.md) | A parameter pack of reference type in a contract is rejected outright | [PR126878](https://gcc.gnu.org/bugzilla/show_bug.cgi?id=126878), [PR126039](https://gcc.gnu.org/bugzilla/show_bug.cgi?id=126039) | [`1040-pack-reference-constify`](../gnu_gcc/branch-history/1000-p2900-fixes/1040-pack-reference-constify.md) | one new static predicate, one extra conjunct | |
| [GCC-23](../gnu_gcc/bug-reports/gcc-23/gcc-23-contract-on-deleted-or-defaulted.md) | [dcl.contract.func]/6 not applied to deleted or first-declaration-defaulted functions | [PR124486](https://gcc.gnu.org/bugzilla/show_bug.cgi?id=124486), [PR125403](https://gcc.gnu.org/bugzilla/show_bug.cgi?id=125403) | [`1090-contract-on-deleted-or-defaulted`](../gnu_gcc/branch-history/1000-p2900-fixes/1090-contract-on-deleted-or-defaulted.md) | one predicate, its declaration, three call sites | one of the seven tests, `pr124486-accepted.C`, names `-fcontracts-p3097`, a branch-only option, and must be dropped or rewritten for submission; the other six diagnose and are portable as-is |
| [GCC-24](../gnu_gcc/bug-reports/gcc-24/gcc-24-noexcept-body-wrapper-assumption.md) | A noexcept function with a contract segfaults the compiler under `-fno-enforce-eh-specs` | [PR127173](https://gcc.gnu.org/bugzilla/show_bug.cgi?id=127173) | [`1220-noexcept-body-wrapper-detected`](../gnu_gcc/branch-history/1000-p2900-fixes/1220-noexcept-body-wrapper-detected.md) | a self-contained rewrite of one `if` head | every name involved is already upstream |
| [GCC-25](../gnu_gcc/bug-reports/gcc-25/gcc-25-postcondition-result-name-attribute.md) | A postcondition's result-name-introducer does not accept `identifier attribute-specifier-seq :` | [PR125725](https://gcc.gnu.org/bugzilla/show_bug.cgi?id=125725) | [`1170-result-name-introducer`](../gnu_gcc/branch-history/1000-p2900-fixes/1170-result-name-introducer.md) | one new function and both its call sites | |
| [GCC-28](../gnu_gcc/bug-reports/gcc-28/gcc-28-xobj-member-in-predicate-ctor-message.md) | A member named unqualified in an explicit-object member function's contract is diagnosed with a constructor/destructor message | [PR127294](https://gcc.gnu.org/bugzilla/show_bug.cgi?id=127294) | [`1200-xobj-member-in-predicate`](../gnu_gcc/branch-history/1000-p2900-fixes/1200-xobj-member-in-predicate.md) | two added conjuncts | ours |
| [GCC-29](../gnu_gcc/bug-reports/gcc-29/gcc-29-contract-assert-alt-spelling-ice.md) | The `__contract_assert` extension spelling ICEs in `grok_contract` | [PR127295](https://gcc.gnu.org/bugzilla/show_bug.cgi?id=127295) | [`4620-contract-extension-fields`](../gnu_gcc/branch-history/4600-p4283/4620-contract-extension-fields.md) | one more conjunct in `grok_contract`'s if/else chain | **write it fresh against trunk; do not lift the diff.**  `4620` has a long `depends:` list because it rewrites `grok_contract` whole for four papers' fields, and its `grok_contract` takes three parameters trunk's does not.  None of that is needed here: both spellings already reach the function as `RID_CONTASSERT`, and the test needs only `c++26` and `-fcontracts` |
| [GCC-45](../gnu_gcc/bug-reports/gcc-45/gcc-45-contract-on-typedef-declaration.md), [GCC-46](../gnu_gcc/bug-reports/gcc-46/gcc-46-contract-on-function-typed-parameter.md), [GCC-47](../gnu_gcc/bug-reports/gcc-47/gcc-47-requires-clause-on-parameter.md) | A contract specifier on a typedef declaration or on a parameter of function type, and a requires-clause on a parameter of function type, are each accepted and silently dropped | none filed | [`1250-contract-on-non-function-declarator`](../gnu_gcc/branch-history/1000-p2900-fixes/1250-contract-on-non-function-declarator.md) + [`0160-requires-clause-on-parameter`](../gnu_gcc/branch-history/0100-upstream-bugfixes/0160-requires-clause-on-parameter.md) | a coupled two-patch series sharing `classify_non_function_declarator` | GCC-47 is not a contracts bug -- its test needs only C++20 -- which makes it the most independently attractive of the three.  All three need a PR filed first |

### Filing needed before submission

GCC-45, GCC-46 and GCC-47 carry `None found (searched 2026-09-16)` in the bug
table: Bugzilla was searched and nothing matched, so no PR exists to reference.
Filing them is the user's to do, as is every posting to gcc-patches.

## Considered and excluded

Kept because the reason matters: an exclusion here is a judgement about a
specific fix at a specific time, and the first two groups below become
candidates under different conditions.

**The fix itself needs branch infrastructure.**  Not liftable, and a minimal
upstream fix would be a different change that has not been written.

* GCC-12, GCC-33 -- `1130-result-binding-one-object` interleaves the retval
  stand-in prologue, a `4000-p3098` capture-flag loop and the copy-back
  epilogue with no cut that leaves balanced braces.
* GCC-15 -- the reading half of `1120-outlined-checks-by-reference`
  (`remap_as_value` and its callers) lives in `4000-p3098`; neither half works
  alone.
* GCC-44 -- `1140-retval-destroyed-on-unwind` depends on `1130` above.
* GCC-13 -- `4640-contract-substitution-plumbing`.
* GCC-37, GCC-38 -- `2600-source-location-machinery` depends on
  `2200-libcontracts` and leaves an unreferenced trampoline until
  `4200-p3400-core`.
* GCC-1, GCC-26 -- `1100-postcondition-const-across-redeclarations` sits on the
  `1050` to `1060` odr-use chain.
* GCC-19, GCC-36 -- `1060-postcondition-odr-use` depends on `1050`, and one
  hunk carries a rename owned by `4000-p3098`.
* GCC-30 -- `1080-coroutine-postcondition-param` depends on `1060`.
* GCC-31 -- `1190-lambda-capture-in-predicate` needs
  `LAMBDA_EXPR_IN_CONTRACT_PREDICATE_P` from `1070`, itself downstream of
  `1050` and introducing a new `DECLTYPE` bit.
* GCC-18 -- `1050-predicate-lambda-constify` standing alone drops
  [dcl.contract.func]/7 entirely until `1060` reinstates it, so it would
  regress upstream as submitted.

**Self-contained, but not purely a bug fix.**

* GCC-42 -- `1020-constexpr-side-effect` has no dependencies and no compile
  gap, but it introduces a new `-Wcontract-constexpr-side-effect` option with
  its `invoke.texi` entry.  That is a design conversation upstream, not a
  defect report, and no PR is filed.
* GCC-43 -- `1025-contract-discardable-evaluation` depends on 1020 and would
  travel with it.  No PR filed.

**No commit of its own.**

* GCC-21 -- the change that resolves it rides inside a commit that exists for
  another reason, so there is nothing to extract.

**Not fixed on this branch**, so there is nothing to submit: GCC-2, GCC-17,
GCC-27, GCC-39, GCC-49.

## Working a candidate

The mechanics are in the `gcc-dev` skill and `bin/gcc/`: `patch_start.sh
<topic>` cuts `upstream-patch/<topic>` from fresh `upstream/master` in
`~/repos/gnu_gcc-patch` (a separate clone, not a worktree), `test.sh
--patch-baseline [sel]` then `--patch [sel]` establishes and compares the
regression baseline, and `patch_prepare.py` emits the patch into a directory
here.

Two traps that bear repeating.  `--all` and `--full` silently pass on a stock
tree, because every selector is fork-only and DejaGnu matches nothing.  And
`compare_tests` cannot show a fix working when the test file already exists
upstream -- the dg test name is unchanged, so the delta is empty by
construction and proves only "no regressions"; to show the test catches the
bug, compile it with a stock compiler and watch it fail.
