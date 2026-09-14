# chamnan-measure

**[Measure any public GitHub repository →](https://arcticfox2029.github.io/chamnan-measure/)**

A one-page harness for [chamnan](https://github.com/ArcticFox2029/chamnan). Paste a public
repository, and it reports what chamnan would inject into a coding agent's context — measured, in
your browser, with nothing uploaded and no server involved.

## What it actually does

It runs **chamnan's own modules**, not a JavaScript re-implementation of them. `build.py` copies
the import closure of `mapper`, `rollup`, `workspace` and `redact` out of the plugin, and
[Pyodide](https://pyodide.org) executes them in the page. The numbers on screen are the tool's own
output, so they cannot quietly drift away from what the plugin does.

The repository is read the way your browser reads any other page: one request to `api.github.com`
for the file list, then `raw.githubusercontent.com` for the source. Both send
`access-control-allow-origin: *`, which is why no backend is needed. Nothing you measure is sent
anywhere except from GitHub to your own browser.

## What it measures

1. **The map** — files indexed, `MAP.md` on disk, and the rolled-up block actually injected per
   session against the byte ceiling that bounds it.
2. **Fifty turns** — the block is injected once per session, not per turn. The comparison figure is
   the whole indexable source, shown as a bound on what orienting by reading files could cost.
3. **Credentials** — how many files `redact.scrub` would alter before any of it reached a model.
   Counted, never displayed: this demonstrates that the redactor runs, and it is not a secret
   scanner. It will not tell you or anyone else what it matched or where.

## What it cannot show you

Stated here as plainly as on the page itself, because a demo that oversells is worse than none.

- **Skills, session handoffs and recorded decisions need installing and using.** Their value comes
  from a week of real work accumulating in a repository. A one-shot measurement cannot produce that.
- **Anything that asks git.** There is no process layer in a browser, so churn ranking and commit
  history are absent. chamnan degrades to its no-git mode, which is real and supported — but the
  numbers here are that mode.
- **Large repositories are sampled**, so the source figure is a floor. The cap in force is reported
  with every result.
- **No model is called**, and you are never asked for an API key. The turn figures are arithmetic
  over a measured per-session cost, not an observation of an agent working.
- **GitHub's unauthenticated API allows 60 requests an hour per IP.** Two are used per measurement.

## The claim is efficiency, not correctness

chamnan bounds what reaches the model. It does not make the model better at your task. The
[evidence section of chamnan's README](https://github.com/ArcticFox2029/chamnan#evidence) carries
the research on that, including the measurements that argue against doing this at all.

## Measured with this page

Every row below was produced by this page in a real browser and cross-checked against the same
modules run natively. Fourteen repositories across eight languages. The pattern is the point rather
than any single ratio: **the source grows nearly seventy-fold down the table and the injected block
holds between 6.2 and 6.7 KB for every repository but the smallest.** That is a bound, not
compression.

Measured 2026-09-14. These repositories are worked on daily, so re-running one today will not match
to the byte — the shape holds, the digits move.

| repository | language | source | injected | ratio |
|---|---|---|---|---|
| chalk/chalk | JavaScript | 56 KB | 2,449 B | 24:1 |
| fastapi/fastapi | Python | 61 KB | 6,454 B | 10:1 |
| facebook/react | JavaScript | 256 KB | 6,649 B | 39:1 |
| psf/requests | Python | 392 KB | 6,345 B | 63:1 |
| pallets/flask | Python | 576 KB | 6,391 B | 92:1 |
| gin-gonic/gin | Go | 660 KB | 6,636 B | 102:1 |
| sinatra/sinatra | Ruby | 663 KB | 6,570 B | 103:1 |
| yumiaura/myCat | Mixed | 719 KB | 6,409 B | 115:1 |
| rust-lang/mdBook | Rust | 1,191 KB | 6,470 B | 188:1 |
| rails/rails | Ruby | 1,550 KB | 6,537 B | 243:1 |
| django/django | Python | 2,209 KB | 6,245 B | 362:1 |
| torvalds/linux | C | 3,028 KB | 6,603 B | 470:1 |
| tokio-rs/tokio | Rust | 3,481 KB | 6,441 B | 553:1 |
| vuejs/core | TypeScript | 3,782 KB | 6,250 B | 620:1 |

## Rebuilding the bundled modules

The copy under `lib/` must be regenerated when chamnan's own `lib/` changes, or the page stops
reporting what the plugin does. From a chamnan checkout:

```bash
python3 site/build.py
```

The module set is derived from the import graph rather than listed, so a dependency added later is
picked up without anyone having to remember it.

## Licence

MIT, the same as chamnan. The files under `lib/` are copies of chamnan's own modules and carry its
licence with them.
