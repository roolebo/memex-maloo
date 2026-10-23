# memex-maloo

A [memex] adapter for the `ci` role in Maloo. The role is specified in
memex's [ci.md].

Tests run offline, with a fake curl: `test/run [TEST...]`.

## Reaching Maloo

ADDR is the base URL of the server. The adapter uses the JSON API under
`/api/`, with the user and password from `MALOO_USER` and
`MALOO_PASSWORD`, or else for ADDR's host in `~/.netrc`, and reads every
page of a list by `offset`. `retest` signs in to the web form, which
needs the password from the environment.


## Commands

    runs COMMIT     GET code_reviews?commit_id=COMMIT, then
                    test_sessions?id= for each test_session_id
    run RUN         GET test_sessions?id=RUN
    suites RUN      GET test_sets?test_session_id=RUN
    tests SUITE     GET sub_tests?test_set_id=SUITE
    logs ID         not yet known
    retest RUN      POST test_sessions/RUN/retest, retest_option single;
                    `reason` is the issue id Maloo requires

Capabilities: `tests`, `retest` and `bugs` are true, `logs` false.

## Run

- RUN is the test session id.
- `links`: `commit`; `change` and `revision` from `review_no` and
  `patch_no` in the code review's `data`.
- `status`: done; queued and running sessions are in `test_queues`,
  which the adapter does not read.
- `result`: fail if a test set failed, else error if one aborted, else
  pass.
- `finished`: `submission`; `started`: `submission` less `duration`.
- `config`: `test_group`, `test_name`, `test_host` and `trigger_job`.
- `url`: ADDR/test_sessions/RUN.

## Suite and test

- SUITE is the test set id, TEST the sub test id.
- `name`: the name of the test set's or sub test's script, cached.
- `result`: PASS is pass, FAIL and CRASH fail, SKIP skip, TIMEOUT
  timeout, any other status error.
- `links`: an `issue` for each accepted bug link on the test set or sub
  test (`bug_links?buggable_id=`). Those of a suite's sub tests come in
  one request, with `&related=true`.

## Open questions

- Jenkins: a separate ci binding with its own adapter, or followed from
  the session by this one?

[memex]: https://github.com/roolebo/memex
[ci.md]: https://github.com/roolebo/memex/blob/main/ci.md
