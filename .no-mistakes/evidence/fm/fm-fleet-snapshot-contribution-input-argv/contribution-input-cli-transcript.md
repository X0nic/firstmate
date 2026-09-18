# fm-fleet-snapshot.sh --contribution-input : live CLI transcript

Isolated firstmate home built the way a long-running fleet's is: one owned
in-flight ship contribution plus 300 ordinary queued backlog rows.
  data/backlog.md = 60459 bytes
  rendered backlog JSON = 284665 bytes (single --argjson value pre-fix)
  host getconf ARG_MAX = 2097152, Linux per-argument limit = 131072 bytes

## BEFORE (base 1bb72cc) - the reported silent failure
```
$ FM_HOME=$HOME_FIXTURE bin/fm-fleet-snapshot.sh --contribution-input > out.json
/tmp/nm-contrib-drive.rAvh39/base/bin/fm-fleet-snapshot.sh: line 1978: /usr/bin/jq: Argument list too long
exit status: 0
out.json size: 0 bytes
```

## AFTER (a7122cf) - real backlog-and-tasks JSON
```
$ FM_HOME=$HOME_FIXTURE bin/fm-fleet-snapshot.sh --contribution-input > out.json
exit status: 0
stderr: (empty)
out.json size: 401482 bytes

$ jq '{keys:keys, backlog_present:.backlog.present, backlog_records:(.backlog.records|length), tasks:.tasks}' out.json
{
  "keys": [
    "backlog",
    "tasks"
  ],
  "backlog_present": true,
  "backlog_records": 301,
  "tasks": [
    {
      "id": "lido-upgrade",
      "kind": "ship",
      "pr": {
        "url": "https://github.com/kunchenguid/firstmate/pull/4812",
        "head": "abc123def4567890abc123def4567890abc123de"
      },
      "merge_authority": "attended"
    }
  ]
}
```

## Failure is now reported as failure

### Real, unstubbed transport failure (unwritable TMPDIR)
```
$ TMPDIR=$READONLY_DIR bin/fm-fleet-snapshot.sh --contribution-input > out.json
mktemp: failed to create directory via template ‘/tmp/nm-contrib-drive.rAvh39/readonly-tmp/fm-fleet-snapshot.XXXXXX’: Permission denied
fm-fleet-snapshot: temporary transport directory creation failed
exit status: 1   stdout: 0 bytes
```

### Assembly failure (jq fault-injected on PATH; the product script itself is real)
```
$ PATH=$FAULT_JQ:$PATH bin/fm-fleet-snapshot.sh --contribution-input > out.json
--- fixed tree ---
jq: error: simulated assembly failure
fm-fleet-snapshot: contribution input assembly failed
exit status: 1   stdout: 0 bytes   transport files left behind: none
--- base tree (same fault) ---
/tmp/nm-contrib-drive.rAvh39/base/bin/fm-fleet-snapshot.sh: line 1978: /tmp/nm-contrib-drive.rAvh39/faultbin/jq: Argument list too long
exit status: 0   stdout: 0 bytes   <- silent empty success
```
