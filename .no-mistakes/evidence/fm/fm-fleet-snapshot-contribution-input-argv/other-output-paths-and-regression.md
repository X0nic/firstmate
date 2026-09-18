# Other output paths after the shared transport-dir hoist

Same large-backlog home, driven through the two non-contribution-input modes.
```
$ bin/fm-fleet-snapshot.sh --json
exit status: 0   stderr: (empty)   406694 bytes
{"schema":"fm-fleet-snapshot.v1","backlog_present":true,"backlog_records":301,"tasks":1,"contributions":{"known":1,"checked":1}}
transport files left in TMPDIR: none

$ bin/fm-fleet-snapshot.sh --secondmate-home-summary
exit status: 0   stderr: (empty)   12230 bytes
{"schema":"fm-secondmate-home-summary.v1","valid":false,"backlog_rows":20,"contributions_known":1}
transport files left in TMPDIR: none
```

# Regression property of the two authored tests
```
base tree 1bb72cc + new tests:
  not ok - assembly failure injection was not reached
  not ok - large contribution-input snapshot silently succeeded with empty output
target tree a7122cf:
  ok - contribution-input assembly failure exits nonzero with a diagnostic and cleans up
  ok - contribution-input preserves backlog larger than ARG_MAX and task ownership
```

# Behaviour-equivalence of the hoist

Same home driven through base 1bb72cc and target a7122cf:
```
--json                    output byte-identical apart from generated timestamps
                          and the $FM_ROOT path of the tree being run
--secondmate-home-summary output byte-identical (both 12230 bytes)
```

# Boundary cases on the contribution-input path
```
empty home (no data/backlog.md, no owned tasks)
  exit 0, stderr empty, {"backlog_present":false,"records":0,"tasks":[]}

large backlog + 400 owned .meta records (both halves oversized)
  target a7122cf: exit 0, 499267 bytes, 401 tasks, stderr empty
  base    1bb72cc: exit 0, 0 bytes, "/usr/bin/jq: Argument list too long"
```
