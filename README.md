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
modules run natively. The pattern is the point rather than any single ratio: **the source grows
sixty-fold down the table and the injected block stays between 6.4 and 6.6 KB.** That is a bound,
not compression.

| repository | language | source | injected | ratio |
|---|---|---|---|---|
| chalk/chalk | JavaScript | 60 KB | 2,678 B | 23:1 |
| psf/requests | Python | 398 KB | 6,485 B | 63:1 |
| pallets/flask | Python | 576 KB | 6,391 B | 92:1 |
| sinatra/sinatra | Ruby | 663 KB | 6,570 B | 103:1 |
| gin-gonic/gin | Go | 675 KB | 6,620 B | 104:1 |
| rust-lang/mdBook | Rust | 1,187 KB | 6,465 B | 188:1 |
| tokio-rs/tokio | Rust | 2,609 KB | 6,492 B | 412:1 |
| torvalds/linux | C | 2,874 KB | 6,545 B | 450:1 |
| vuejs/core | TypeScript | 3,762 KB | 6,492 B | 593:1 |

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
