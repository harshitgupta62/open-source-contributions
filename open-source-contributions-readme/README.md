<div align="center">

<img src="assets/banner.svg" alt="Open Source Engineering Portfolio: making real-world C++ codebases safer, one validated input at a time" width="100%"/>

<br/><br/>

<img src="assets/stats.svg" alt="3 merged pull requests, 4 in review, 3 organizations, 8 pull requests opened" width="100%"/>

<br/><br/>

<img src="assets/skills.svg" alt="Skills: Modern C++, Defensive Programming, Input Validation, Regression Testing, API Documentation, CI/CD, GitHub Actions, JSON, Git, Code Review" width="100%"/>

</div>

<br/>

## 👋 About This Repository

Hi, I'm **Harshit Gupta**, a B.Tech (Information Technology) student at ABV-IIITM Gwalior.

This repository is a living record of my open-source work: what I fixed, why it mattered, and how each pull request went from first commit to merge. My core focus is **defensive programming**: making software fail clearly and safely when it receives bad input, and proving it with tests.

![C++](https://img.shields.io/badge/C%2B%2B-00599C?style=flat-square&logo=cplusplus&logoColor=white)
![Git](https://img.shields.io/badge/Git-F05032?style=flat-square&logo=git&logoColor=white)
![GitHub Actions](https://img.shields.io/badge/GitHub_Actions-2088FF?style=flat-square&logo=githubactions&logoColor=white)
![JSON](https://img.shields.io/badge/JSON-000000?style=flat-square&logo=json&logoColor=white)
![Updated](https://img.shields.io/badge/Last_updated-October_2026-8957e5?style=flat-square)

<img src="assets/divider.svg" alt="" width="100%" height="6"/>

## 🎯 The Scoreboard

<div align="center">
<img src="assets/breakdown.svg" alt="Pull request status: 3 merged, 4 in review, 1 closed, broken down by organization" width="100%"/>
</div>

| Organization | Project | PRs | Merged |
| :--- | :--- | :---: | :---: |
| **XRPLF** (XRP Ledger Foundation) | [rippled](https://github.com/XRPLF/rippled) | 6 | 2 |
| **P4Lang** | [behavioral-model](https://github.com/p4lang/behavioral-model) | 1 | 1 |
| **OpenTimelineIO** (Academy Software Foundation) | [raven](https://github.com/OpenTimelineIO/raven) | 1 | 0 |

<img src="assets/divider.svg" alt="" width="100%" height="6"/>

## ✅ Merged Contributions

### 🔐 [XRPLF/rippled #7583](https://github.com/XRPLF/rippled/pull/7583): Validate `vetoed` in the `feature` RPC

![Merged](https://img.shields.io/badge/Merged-8957e5?style=flat-square&logo=githubactions&logoColor=white) ![Tests](https://img.shields.io/badge/Tests-added-2ea44f?style=flat-square) ![Changelog](https://img.shields.io/badge/API_changelog-updated-0969da?style=flat-square)

- **Problem:** a non-boolean `vetoed` value could throw an exception or silently coerce to `false`, accidentally **un-vetoing an amendment**.
- **Fix:** an explicit `isBool()` check that returns `invalidParams` for any other type.
- **Closes:** [#6757](https://github.com/XRPLF/rippled/issues/6757)

### 🔐 [XRPLF/rippled #7582](https://github.com/XRPLF/rippled/pull/7582): String validation for `channel_id` and `signature`

![Merged](https://img.shields.io/badge/Merged-8957e5?style=flat-square&logo=githubactions&logoColor=white) ![Tests](https://img.shields.io/badge/Tests-added-2ea44f?style=flat-square) ![Changelog](https://img.shields.io/badge/API_changelog-updated-0969da?style=flat-square)

- **Problem:** `channel_authorize` and `channel_verify` accepted integers and arrays, relying on silent coercion.
- **Fix:** `isString()` checks before conversion, returning `invalidParams` for the wrong type, with regression tests in `PayChan_test.cpp`.
- **Closes:** [#6765](https://github.com/XRPLF/rippled/issues/6765)

### ⚙️ [P4Lang/behavioral-model #1409](https://github.com/p4lang/behavioral-model/pull/1409): Remove dead `bmv2.org` references

![Merged](https://img.shields.io/badge/Merged-8957e5?style=flat-square&logo=githubactions&logoColor=white) ![CI](https://img.shields.io/badge/CI-fixed-2088FF?style=flat-square&logo=githubactions&logoColor=white)

- **Problem:** the README linked to a domain that no longer existed, and the Doxygen CI workflow failed on every run trying to upload to it.
- **Fix:** removed the dead link and disabled the failing S3 upload step, restoring a clean CI signal.
- **Closes:** [#1408](https://github.com/p4lang/behavioral-model/issues/1408)

<img src="assets/divider.svg" alt="" width="100%" height="6"/>

## 🟡 In Review

| Pull Request | What it does | Where it stands |
| :--- | :--- | :--- |
| [rippled #7606](https://github.com/XRPLF/rippled/pull/7606) | `fetch_info`: strict boolean check on `clear`. Values like `"false"`, `[1]` or `{}` used to be treated as `true` and could clear ledger-fetch state. Includes tests and changelog. | 🛠️ Addressing maintainer feedback |
| [rippled #7595](https://github.com/XRPLF/rippled/pull/7595) | `log_level`: type checks on `severity` and `partition`, so bad input returns `invalidParams` instead of throwing. Includes tests and changelog. | ⏳ Awaiting final review |
| [rippled #7590](https://github.com/XRPLF/rippled/pull/7590) | `owner_info`: type validation for `account` and `ident`, plus correct ledger-selector handling. | 🛠️ Iterating on review feedback |
| [rippled #7589](https://github.com/XRPLF/rippled/pull/7589) | `account_lines`: validates that `peer` is a string before parsing. | ⏳ Awaiting review |

<img src="assets/divider.svg" alt="" width="100%" height="6"/>

## 🔒 Spotlight: Hardening `rippled`'s RPC Layer

Most of my `rippled` pull requests share one root cause: JSON values were converted with `asBool()` or `asString()` **without checking their type first**. A malformed request could throw, or worse, be silently coerced into a valid-looking value with real side effects.

<div align="center">
<img src="assets/validation-flow.svg" alt="Animated diagram: a valid request passes the isBool() type check and reaches the handler, while a request with the string &quot;false&quot; is rejected with invalidParams" width="100%"/>
</div>

A simplified illustration of the fix:

```cpp
// Before: any JSON type is silently coerced
bool clear = params[jss::clear].asBool();

// After: the wrong type is rejected explicitly
if (params.isMember(jss::clear) && !params[jss::clear].isBool())
    return rpcError(rpcINVALID_PARAMS);

bool clear = params.isMember(jss::clear) && params[jss::clear].asBool();
```

Every PR in the series follows the same standard: **fix, test, document**.

<img src="assets/divider.svg" alt="" width="100%" height="6"/>

## 🛠️ How I Approach a Contribution

<div align="center">
<img src="assets/workflow.svg" alt="Six steps: find a scoped issue, reproduce and understand, smallest root-cause fix, add regression tests, docs and API changelog, iterate on review and CI" width="100%"/>
</div>

<img src="assets/divider.svg" alt="" width="100%" height="6"/>

## 🔴 Closed: What I Learned

### 🎨 [OpenTimelineIO/raven #161](https://github.com/OpenTimelineIO/raven/pull/161): Official Clip Color schema support

I set out to make Raven read and write the official OpenTimelineIO Clip Color schema while staying compatible with its legacy color metadata. Maintainer feedback pushed the work further: Raven is an *inspection* tool, so it must show a file's contents exactly as they are and never lose a custom color. That led to preserving arbitrary RGB values instead of snapping them to a fixed palette.

I closed this PR while the OpenTimelineIO Technical Steering Committee finalizes its policy on LLM-assisted contributions, which the maintainers asked me to wait for before moving forward.

**Takeaways:** a "simple" schema change can hide data-loss bugs, a tool that displays data has to be faithful to it, and it pays to understand how real users work (here, how editing software treats clip colors) before writing code.

<img src="assets/divider.svg" alt="" width="100%" height="6"/>

## 💡 Skills Demonstrated

| Area | Evidence |
| :--- | :--- |
| **Modern C++** | RPC handlers and unit tests in a large production codebase |
| **Defensive programming** | Type validation, safe error returns, no silent coercion |
| **Testing** | Regression tests for invalid-input paths (`PayChan_test.cpp`, `FetchInfo_test.cpp`) |
| **Technical writing** | API changelog entries, README and documentation cleanup |
| **CI/CD** | Removed a failing GitHub Actions deployment step |
| **Collaboration** | Iterating on maintainer and automated review feedback, rebasing on `develop`, merge queues, signed commits |

<br/>

<div align="center">

**GitHub:** [@harshitgupta62](https://github.com/harshitgupta62) · Every pull request above links to its discussion, reviews, and CI results.

<img src="assets/footer.svg" alt="Thanks for stopping by. Let's build safer, better software together." width="100%"/>

</div>
