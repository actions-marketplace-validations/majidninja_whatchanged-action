# whatchanged-action

See what a CI job changed on its runner. This action runs
[whatchanged](https://github.com/majidninja/whatchanged) and puts one timeline
of installed packages, edited `/etc` files, systemd units, cron jobs and shell
startup files into the job summary.

It's useful for:

- checking what a setup script or `apt-get install` step really did
- turning "works in CI but not on my machine" into a list of differences
- reviewing third-party actions that touch the system

## Usage

Mark the start of the job, then report at the end:

```yaml
steps:
  - uses: majidninja/whatchanged-action@v1
    with:
      mode: start

  - uses: actions/checkout@v4
  - run: ./scripts/setup-ci.sh

  - uses: majidninja/whatchanged-action@v1
```

The job summary then shows something like:

```
### What changed on the runner since 2026-10-31 18:02

2026-10-31
  18:03  pkg     installed cowsay 3.03+dfsg2-8
  18:03  config  /etc/environment edited
```

Without a `start` step, give a time instead:

```yaml
  - uses: majidninja/whatchanged-action@v1
    with:
      since: 2h
```

## Inputs

| Input     | Default  | Description |
|-----------|----------|-------------|
| `mode`    | `report` | `start` saves the current time; `report` prints the timeline |
| `since`   |          | Start of the timeline (`30m`, `2h`, `1d`, `"2024-05-01 09:00"`). Defaults to the `start` time, or `1h` |
| `version` | `main`   | Branch, tag or commit of whatchanged to build |
| `sudo`    | `true`   | Run with sudo when it works without a password, so root-only files can be read |
| `summary` | `true`   | Write the timeline to the job summary |

## Outputs

| Output     | Description |
|------------|-------------|
| `timeline` | Path to a file with the timeline text |
| `changes`  | Number of timeline lines (`0` when nothing changed) |

## Notes

- Linux runners only.
- whatchanged is built from source with `cargo` (Rust 1.85 or newer, which
  GitHub-hosted Ubuntu runners have). The binary is cached per commit, so
  only the first run builds it.
- Timestamps have minute precision, so changes made in the same minute as
  the `start` step can show up too.

## License

MIT
