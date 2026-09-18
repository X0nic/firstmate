# Downstream effect: bin/fm-contributions.sh coverage on a large backlog

Home with SIX owned in-flight contributions in data/backlog.md, of which
THREE have a durable saved observation, plus 300 queued rows
(data/backlog.md = 61164 bytes).
This is the configuration behind the 2026-09-18 digest report.

```
$ bin/fm-fleet-snapshot.sh --contribution-input > input.json
$ bin/fm-contributions.sh snapshot input.json

BEFORE (base 1bb72cc)  input.json = 0 bytes
{"known":3,"checked":3,"unmeasured":0,"complete":false,"proven_clear":false,"unreadable_records":1}
  -> only 3 of the 6 owned contributions are even KNOWN; the backlog half of
     the ownership pair vanished, so three owned PRs are invisible to the digest.

AFTER (a7122cf)        input.json = 407727 bytes
{"known":6,"checked":3,"unmeasured":0,"complete":false,"proven_clear":false,"unreadable_records":0}
  -> all 6 owned contributions are known, 3 checked: the digest's real
     "3 of 6 checked" instead of a fabricated complete-looking 3 of 3.
```

## bin/fm-contributions.sh poll (the mode that calls --contribution-input itself)
```
BEFORE (base 1bb72cc)
  stderr: /tmp/nm-contrib-drive.rAvh39/base/bin/fm-fleet-snapshot.sh: line 1978: /usr/bin/jq: Argument list too long
  exit status: 0  <- poll ran against empty input and said nothing about it
AFTER (a7122cf)
  stderr: (empty)
  exit status: 0
```
