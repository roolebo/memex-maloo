# memex-maloo

A [memex] adapter for the `ci` role in Maloo. The role is specified in
memex's [ci.md].

## Reaching Maloo

ADDR is the base URL of the server. The adapter uses the JSON API under
`/api/`, with the user and password for ADDR's host in `~/.netrc`, and
reads every page of a list.

`detect` prints nothing.

## Commands

    runs COMMIT     GET code_reviews?commit_id=COMMIT, then
                    test_sessions?id= for each test_session_id
    run RUN         GET test_sessions?id=RUN
    suites RUN      GET test_sets?test_session_id=RUN
    tests SUITE     GET sub_tests?test_set_id=SUITE
    logs ID         not yet known
    retest RUN      `reason` is the issue id Maloo requires

Capabilities: `tests`, `retest` and `bugs` are true, `logs` false.

## Run

- RUN is the test session id.
- `links`: `commit`; `change` and `revision` from `review_no` and
  `patch_no` in the code review's `data`.
- `config`: not yet known.
- `url`: ADDR/test_sessions/RUN.

## Suite and test

- SUITE is the test set id, TEST the sub test id.
- `name`: the name of the test set's or sub test's script, cached.
- `result`: PASS is pass, FAIL fail, SKIP skip; other statuses not yet
  known.
- `links`: an `issue` for each bug link on the test set or sub test
  (`bug_links?buggable_id=`).

## Open questions

- Report pending bug links as `issue`, or only confirmed ones?
- Jenkins: a separate ci binding with its own adapter, or followed from
  the session by this one?

[memex]: https://github.com/roolebo/memex
[ci.md]: https://github.com/roolebo/memex/blob/main/ci.md
