# PyTorch repair candidates — 2026-10-09

These are independent candidate patches, NOT applied production changes or merge-ready PRs. Do not cherry-pick this evidence commit onto a PR: apply only that PR's patch in a separate clean worktree at its exact pinned head. No existing PR branch, workflow, or upstream review is modified by this evidence commit.

## Scope and pins

| PR | Pinned head | Candidate |
| --- | --- | --- |
| 199264 | 86336b68dfee61dad5c84cc1b9e9d20ca3aecf68 | Preserve all function/method dictionary keys; use Dynamo's sorted path rather than silently filtering non-string keys. Add mixed-key error and deletion regressions. |
| 199268 | 17642ea66eaa213bac5cbedd06d54057d3bd2881 | Remove misplaced wrapper comparison from PolyfilledFunctionVariable. Keep MethodWrapperVariable's existing comparison helper unchanged; narrow to metadata on nonconstant receivers. Restore the broad CPython expected-failure marker. |
| 199249 | 37e07db579309787d9d54ce0878b93cddeaae900 | Move type.__doc__ handling from MemberDescriptorVariable to GetSetDescriptorVariable. Avoid metaclass __name__/__flags__ hooks; catch observed errors inside compiled tests. Restore the broader writable-doc expected-failure marker. |

The broad xfail markers are restored because these bounded candidates do not establish that the entire corresponding CPython test is fixed. They are not deleted merely to make the feature look complete.

## Evidence and limits

Twenty-one source/patch/CPython-oracle unittest checks passed: 13 source/patch gates and 8 CPython semantic checks. Original whole production files were verified against GitHub blob hashes. Patches passed git apply/check/reverse checks against those files and exact test-method excerpts with surrounding context. This is NOT full-file test-suite validation, a PyTorch build, or an actual Dynamo regression pass.

The exact Python 3.13 CI wheel for PR199268 was retrieved from run 36829573189, artifact 11245736569 (torch 2.15.0a0+git17642ea). It cannot import in the available environment because libiomp5.so is missing. Dependency downloads failed. The installed torch 2.10.0+cpu is not treated as exact-head evidence.

No rebases, upstream comments, CI approvals, production PR pushes, or merges were performed. The two remaining PRs are held: 199266 needs a genuine base-failing reproduction before retaining its shadowed/redundant __self__ implementation; 199267 must be reconciled with Guilherme Leobas's still-open PR193844 at 5af895d11bfcbe15da248d6418debe37aa0786ac. Do not assume that refactor has landed.

## Continue locally

1. Fetch the live PR head and comments. Stop and reconcile if the head differs from the pin; preserve other agents' changes.
2. In an isolated, clean worktree at the pin, run git apply --check on only the matching patch. Apply it and inspect git diff --check and the complete diff. Do not apply all three patches to one branch.
3. With PyTorch built from that checkout, run the affected focused tests before/after, neighboring Dynamo tests, project lint and Python 3.13 CPython tests. Verify torch.__file__ and torch.version.git_version so a preinstalled wheel is not mistaken for the checkout.
4. Rebase/merge current upstream only after reviewing intervening work; repeat tests. Remove an xfail only when its entire test genuinely passes.
5. Push verified repairs to the EXISTING PR branch and reply to the existing review thread with exact commands/results. For 199268, update its title/body to metadata-only if this narrowing is accepted. Do not create a new PR or claim green before runtime validation.

Patch SHA-256 checksums:

- 199264.patch: 5ee2af969315e866626f5333869a49fc22b007fbf8ff4147d7e7281a6ea55269
- 199268.patch: d81f9f86db323aa7d979403b4c6abc28272ade6c3842e13ba6ec44a5e530c3aa
- 199249.patch: 1daf8f52cf4df60b76c52c111291c6bfe8182a0c22c97407702ce9ea1c29f3e5

## Credit / provenance

Guilherme Leobas identified the CPython-compatible dir invariant, suggested python_constant_richcompare_impl on 199268, maintains issue195901, and owns the tp_setattro_impl refactor. PyTorch's automated review/Dr. CI identified the shadowed __self__ method, ineffective tests, wrong-class comparator and CI failures. Edward Yang authored the adjacent builtin-method __self__ work in 196052. Nitin Jaiswal's 198919 addresses adjacent read-only function slots. These original contributions and diagnoses are not claimed as new work here.

Class-targeted repairs and the additional mixed-key dir check were prepared with ChatGPT assistance for Kevin Rajan. PyTorch source excerpts and patches remain subject to the repository's license.

Sources: https://github.com/pytorch/pytorch/pull/199264 ; https://github.com/pytorch/pytorch/pull/199268 ; https://github.com/pytorch/pytorch/pull/199249 ; https://github.com/pytorch/pytorch/pull/199266 ; https://github.com/pytorch/pytorch/pull/199267 ; https://github.com/pytorch/pytorch/pull/193844
