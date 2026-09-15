# NOTICE

## Unofficial guide

This guide is **not** an official Anthropic publication. It was written by
[Renato Tadeu Figueiredo](https://github.com/RenatoTadeuFigueiredo) as an independent,
learner-oriented path through Anthropic's public documentation.

Nothing here overrides the official documentation. Where the two disagree, the official
documentation is correct.

## Source material

The technical content is derived from, and cross-checked against, Anthropic's public Claude Code
documentation at <https://code.claude.com/docs>. Command names, settings keys, permission modes,
and file locations originate there.

Anthropic publishes that documentation; this repository adds ordering, framing for beginners, and
worked examples. Any error introduced by that framing is the author's.

## License

The text of this guide is licensed under the
[Creative Commons Attribution 4.0 International License](LICENSE) (CC BY 4.0).

In plain language: **you may share it and adapt it, including commercially, as long as you give
appropriate credit.** The full legal code is in [`LICENSE`](LICENSE); canonical source at
<https://creativecommons.org/licenses/by/4.0/legalcode>.

This covers **this guide's text** only. It grants no rights over the Claude Code software itself,
nor over Anthropic's documentation.

## Trademarks

**Claude** and **Claude Code** are trademarks of Anthropic PBC. Other product names mentioned
(Homebrew, Google Drive, Node.js, Prettier, macOS) are trademarks of their respective owners.

## Version pinning

The guide is written against a specific Claude Code version and reviewed on a specific date, both
recorded in [`README.md`](README.md). Claude Code ships on a weekly cadence, so treat the
`Last reviewed` date as the expiry of the detail, not of the approach.

## No warranty

Provided as-is. Running commands from this guide on your machine is your responsibility, and the
sections on permission modes exist precisely because Claude Code can modify files and run
commands with your user's access.

## Reporting a problem

If a command no longer works, or the official docs have moved on, open an issue with:

- your Claude Code version (`claude --version`)
- your macOS version (`sw_vers`)
- the chapter and the exact command
- what happened instead

Corrections are welcome. See [`CONTRIBUTING.md`](CONTRIBUTING.md).