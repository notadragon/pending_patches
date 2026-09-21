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

A row moves to [Landed](#landed) when upstream commits the change, **and its
directory is deleted in the same breath** -- the patch and its validation
artifacts have done their job once the commit exists, and a stale copy of a
diff that upstream may since have touched is worse than none.  What the row
keeps is the correspondence: our id, the PR, the message that was accepted,
and the commit.  Anything else is in this repo's history, and in upstream's,
plus the `Fixed upstream by us` table in `../gnu_gcc/bug-reports/README.md`.

**That second record lags, deliberately.** Retiring a `bug-reports/` row is
part of a rebase -- it deletes the writeup and the reproducer, retires the
`verify-cases.txt` case, and re-records `verify-expected.txt` -- so it happens
on the rebase, not the day the patch lands.  GCC-5 and GCC-29 are still in the
open table there as of 2026-09-20 even though both fixes are upstream.

## In flight

Prepared and not yet landed, whether or not it has been sent.

**Sent** is the last revision that reached `gcc-patches@gcc.gnu.org`, and
**Next action** is what is owed right now.  The two together are the whole
point of one table rather than two: a patch that has been sent once can still
have an unsent revision sitting in its directory, and splitting "pending" from
"submitted" gave that state nowhere to live.  The one row below is the other
state again: sent, reviewed, and owing a revision that has not been written.

| Patch directory | Bug | Summary | PR | Commit | Validated | Sent | Next action |
|---|---|---|---|---|---|---|---|
| [`upstream-patch-pr125459-constexpr-repeat-call/`](upstream-patch-pr125459-constexpr-repeat-call/) | [GCC-3](../gnu_gcc/bug-reports/gcc-03/gcc-03-constexpr-repeat-call.md) | A contract predicate re-invoking a constexpr function already called in the same constant evaluation is wrongly rejected as non-constant | [PR125459](https://gcc.gnu.org/bugzilla/show_bug.cgi?id=125459) | `1407b9b035d` | validated 2026-09-17 on `6e53e027e587`, `dg.exp`: 0 regressions, 2 new passes | **v1**, 2026-09-18, [gcc-patches 731861](https://gcc.gnu.org/pipermail/gcc-patches/2026-September/731861.html) -- reviewed 2026-09-19 | **Send v2, which has not been written yet** -- the patch in the directory is still v1.  Jason Merrill accepted treating `void_list_node` in `constexpr_global_ctx::put_value` as equivalent to an unmapped key, but rejected extending that to `void_node`, which indicates an object still within its storage duration ([731926](https://gcc.gnu.org/pipermail/gcc-patches/2026-September/731926.html)).  Agreed in [731931](https://gcc.gnu.org/pipermail/gcc-patches/2026-September/731931.html): v2 drops the `void_node` case and comments the skips that remain deliberate |

## Landed

**Sent** is the revision upstream took, which is not always the first one; the
thread behind that link is where the review rationale lives, and nothing else
records it once the row leaves the table above.

| Bug | PR | Sent | Upstream commit | Landed |
|---|---|---|---|---|
| GCC-9 | [PR127282](https://gcc.gnu.org/bugzilla/show_bug.cgi?id=127282) | **v1**, 2026-09-14, [gcc-patches 731238](https://gcc.gnu.org/pipermail/gcc-patches/2026-September/731238.html) | [`7b60a368fb1`](https://gcc.gnu.org/git/?p=gcc.git;a=commit;h=7b60a368fb19b33f8676482a233d389cbc47b85e) | 2026-09-14 |
| GCC-5 | [PR127281](https://gcc.gnu.org/bugzilla/show_bug.cgi?id=127281) | **v2**, 2026-09-18, [gcc-patches 731860](https://gcc.gnu.org/pipermail/gcc-patches/2026-September/731860.html) -- "Pushed, thanks" ([731990](https://gcc.gnu.org/pipermail/gcc-patches/2026-September/731990.html)) | [`e8207be46a6`](https://gcc.gnu.org/git/?p=gcc.git;a=commit;h=e8207be46a6ebbbc079adc72c763b648de5c2da5) | 2026-09-20 |
| GCC-29 | [PR127295](https://gcc.gnu.org/bugzilla/show_bug.cgi?id=127295) | **v1**, 2026-09-18, [gcc-patches 731862](https://gcc.gnu.org/pipermail/gcc-patches/2026-September/731862.html) -- "Pushed with that adjustment" ([731989](https://gcc.gnu.org/pipermail/gcc-patches/2026-September/731989.html)), the adjustment being two commit-message lines rewrapped to 76 columns | [`3bd34f8936c`](https://gcc.gnu.org/git/?p=gcc.git;a=commit;h=3bd34f8936c7edb25d35bc2816a863ca902938bf) | 2026-09-20 |

Neither 2026-09-20 commit reshaped the diff: both are identical in content to
`89be19f150d` and `cd0063e40f4` on their `upstream-patch/` branches, so unlike
[PR127282](https://gcc.gnu.org/bugzilla/show_bug.cgi?id=127282) there is no
upstream spelling for the next rebase to adopt.

## Candidates: simple, orthogonal fixes

Rows from `../gnu_gcc/bug-reports/README.md` that are still open upstream,
fixed on this branch, and whose fix can stand on its own upstream.  Selected by
reading each branch-history entry against three tests: `depends:` is empty or
resolves inside this set, the entry's `Compile gap` section is `None`, and the
contents are confined to `gcc/cp/` plus tests that need no branch-only option.

**"Orthogonal" is a claim about the fix, not about the commit.**  A branch
commit may carry a trivial fix and still have a long `depends:` list, because
the commit was sized to own a whole function that several papers edit.  The
question is what an upstream patch would have to contain, not what our commit
happens to contain.  Where the two differ, the `Notes` column says so.

| Bug | Summary | Upstream PR | Branch entry | Shape of the fix | Notes |
|---|---|---|---|---|---|
| [GCC-10](../gnu_gcc/bug-reports/gcc-10/gcc-10-lambda-this-capture-in-contract.md) | Contract predicate on a lambda capturing `this` reads the closure object as the enclosing class | [PR127283](https://gcc.gnu.org/bugzilla/show_bug.cgi?id=127283) | [`1180-lambda-this-capture-remap`](../gnu_gcc/branch-history/1000-p2900-fixes/1180-lambda-this-capture-remap.md) | one added type test in an existing walker | ours |
| [GCC-11](../gnu_gcc/bug-reports/gcc-11/gcc-11-lambda-in-postcondition-result-name.md) | Lambda in a non-member postcondition with a result name fails to parse | [PR127284](https://gcc.gnu.org/bugzilla/show_bug.cgi?id=127284) | [`1160-lambda-in-postcondition-result-name`](../gnu_gcc/branch-history/1000-p2900-fixes/1160-lambda-in-postcondition-result-name.md) | two small guards, no new interfaces | ours |
| [GCC-14](../gnu_gcc/bug-reports/gcc-14/gcc-14-contract-capture-note-garbage-location.md) | Garbage source location on a contract-capture diagnostic note | [PR126041](https://gcc.gnu.org/bugzilla/show_bug.cgi?id=126041) | [`1150-capture-note-location`](../gnu_gcc/branch-history/1000-p2900-fixes/1150-capture-note-location.md) | how one note's location is computed | |
| [GCC-16](../gnu_gcc/bug-reports/gcc-16/gcc-16-lambda-capture-contract-in-template.md) | Contract on a capturing lambda inside an instantiated template segfaults | [PR126038](https://gcc.gnu.org/bugzilla/show_bug.cgi?id=126038) | [`1230-lambda-capture-bind-through-statement-list`](../gnu_gcc/branch-history/1000-p2900-fixes/1230-lambda-capture-bind-through-statement-list.md) | two hunks rewriting one block in `contracts.cc` | **partial** -- fixes the non-template shape (`pr126038.C` passes on that commit alone); the template shape also needs `4640-contract-substitution-plumbing`, which is not orthogonal |
| [GCC-22](../gnu_gcc/bug-reports/gcc-22/gcc-22-pack-reference-constification.md) | A parameter pack of reference type in a contract is rejected outright | [PR126878](https://gcc.gnu.org/bugzilla/show_bug.cgi?id=126878), [PR126039](https://gcc.gnu.org/bugzilla/show_bug.cgi?id=126039) | [`1040-pack-reference-constify`](../gnu_gcc/branch-history/1000-p2900-fixes/1040-pack-reference-constify.md) | one new static predicate, one extra conjunct | |
| [GCC-23](../gnu_gcc/bug-reports/gcc-23/gcc-23-contract-on-deleted-or-defaulted.md) | [dcl.contract.func]/6 not applied to deleted or first-declaration-defaulted functions | [PR124486](https://gcc.gnu.org/bugzilla/show_bug.cgi?id=124486), [PR125403](https://gcc.gnu.org/bugzilla/show_bug.cgi?id=125403) | [`1090-contract-on-deleted-or-defaulted`](../gnu_gcc/branch-history/1000-p2900-fixes/1090-contract-on-deleted-or-defaulted.md) | one predicate, its declaration, three call sites | one of the seven tests, `pr124486-accepted.C`, names `-fcontracts-p3097`, a branch-only option, and must be dropped or rewritten for submission; the other six diagnose and are portable as-is |
| [GCC-24](../gnu_gcc/bug-reports/gcc-24/gcc-24-noexcept-body-wrapper-assumption.md) | A noexcept function with a contract segfaults the compiler under `-fno-enforce-eh-specs` | [PR127173](https://gcc.gnu.org/bugzilla/show_bug.cgi?id=127173) | [`1220-noexcept-body-wrapper-detected`](../gnu_gcc/branch-history/1000-p2900-fixes/1220-noexcept-body-wrapper-detected.md) | a self-contained rewrite of one `if` head | every name involved is already upstream |
| [GCC-25](../gnu_gcc/bug-reports/gcc-25/gcc-25-postcondition-result-name-attribute.md) | A postcondition's result-name-introducer does not accept `identifier attribute-specifier-seq :` | [PR125725](https://gcc.gnu.org/bugzilla/show_bug.cgi?id=125725) | [`1170-result-name-introducer`](../gnu_gcc/branch-history/1000-p2900-fixes/1170-result-name-introducer.md) | one new function and both its call sites | |
| [GCC-28](../gnu_gcc/bug-reports/gcc-28/gcc-28-xobj-member-in-predicate-ctor-message.md) | A member named unqualified in an explicit-object member function's contract is diagnosed with a constructor/destructor message | [PR127294](https://gcc.gnu.org/bugzilla/show_bug.cgi?id=127294) | [`1200-xobj-member-in-predicate`](../gnu_gcc/branch-history/1000-p2900-fixes/1200-xobj-member-in-predicate.md) | two added conjuncts | ours |

## Considered and excluded

The below locally identified bugs have been reviewed and will be addressed
at a later date, but are not yet ready to upstream.

**The fix itself needs branch infrastructure.**  Not liftable, and a minimal
upstream fix would be a different change that has not been written.

* GCC-45, GCC-47 -- `1250-contract-on-non-function-declarator` turned out to
  need `1240-contract-specifier-at-declarator-position` under it, and one
  commit now fixes both bugs, so neither lifts alone.  GCC-46 was retired into
  GCC-45.  Listed as a candidate until 2026-09-20 on the belief that this was
  a two-patch series; the fix is much more involved than that.
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

