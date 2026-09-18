# decision-stack

[繁體中文](README.md)

**A place to keep your own judgment principles, in a form you can take with you when you change jobs.**

> **Language.** The tooling is Traditional Chinese: its output, its `--help`, and the section
> headings it requires. A `Models/` entry must use the headings 信念 / 適用界線 / 不適用的情況,
> and `stack lint` checks those exact strings. Writing entries in another language will not pass.
>
> The method below is language-agnostic and worth reading on its own. The CLI, as it stands,
> is not. Adding configurable section headings is the one change that would fix this; if that
> matters to you, open an issue and say so.

## Why

What you accumulate inside an organisation comes in two kinds. Trust, relationships and context
belong to the organisation and reset to zero when you leave. Calibrated judgment belongs to you
and travels.

This repo is for the second kind.

## What makes it different from a notes system

A notes system asks you to write things down. This one asks for one more thing: **name the
principle's upstream.**

Every principle must name the framework it runs on. Every framework must name the belief behind
it. Both answers have to exist before the file does, and `stack lint` lists the ones that cannot
answer.

Without that gate, what you write accumulates while staying unconnected, and the next new problem
still starts from nothing.

Three layers:

| Layer | Holds | Answers |
|---|---|---|
| `Models/` | A small number of stable beliefs | Why you think this way |
| `Frameworks/` | Reasoning and working skeletons | How to reach a judgment |
| `Decisions/` | Principles a real event revealed | What to do this time |

`Expressions/` holds, for one kind of output, which words, which tone and which content to use —
written the way you actually talk. It is a directory, not a fourth layer, and sits outside the
derivation chain.

## Quick start

```bash
git clone <this repo> my-stack && cd my-stack
tools/stack init            # creates directories and the index; never overwrites
tools/stack install --yes   # wiring, shim, hooksPath
stack doctor                # what is wired and what is missing
```

After `init` the stack is empty, and an empty stack cannot take its first entry: the write
procedure requires an upstream, and none exists yet. `skills/decision-stack-bootstrap/` walks you
backwards from material you already have to a minimal self-consistent set. `examples/` holds one
complete three-entry chain to read.

Retrieval needs Ollama with `bge-m3` locally. Without it `recall` fails loudly and tells you to
read the index instead, rather than returning an empty result that looks normal.

## What you get

```bash
stack recall "<a sentence>"  # semantic retrieval, top-k plus one-hop linked neighbours
stack lint                   # sections, links, index coverage, chain direction
stack lint --shippable       # de-identification: does this hold on another machine, for another person
stack eval                   # retrieval regression against questions you wrote, compared to a baseline
stack usage                  # which entries no situation has ever needed
stack prose <file>           # sentence shape of any text, compared against your entries
stack tree                   # the derivation chain and its orphans
```

Four things become mechanical rather than remembered:

- **Chain completeness.** `lint` and `tree` list orphans and wrong-direction links.
- **De-identification.** `lint --shippable` runs over the region that gets copied outward, and
  pre-commit runs it on every commit.
- **Retrieval quality.** After rewriting a batch, `eval` compares against the baseline, so a
  regression shows up in the comparison line.
- **Retirement.** `usage` reports absence only and deletes nothing. Absence has to accumulate
  before it counts as a signal.

## This repo has no content

It is the template: tooling, specs, procedures, empty directories, plus the `examples/` chain.
The entries are yours and stay in your own repo.

`stack upgrade` pulls tooling updates from here. `stack contribute` sends your tooling changes
back. Both touch only the upgradable region, never entries, and neither merges on its own.

## What it deliberately does not do

No pip dependencies. Nothing is sent to an external service; embedding runs through local Ollama.
The tooling produces candidates and reports only — splitting, merging and rewriting are decided
by a person.

## Going deeper

| Document | Contents |
|---|---|
| [SPEC.md](SPEC.md) | Layer criteria, de-identification, the write procedure, the two gates, retrieval |
| [WRITING.md](WRITING.md) | Naming, section structure, sentence rules, frontmatter |
| [examples/](examples/) | One complete three-entry chain |

Both are Traditional Chinese, as is the tooling.

## Licence

Tooling under MIT, prose and methodology under CC BY 4.0. See [LICENSE](LICENSE).
