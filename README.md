# plugin-examplerunverb

The reference **host-coupled check verb** — a plugin whose verb reads live engine
state, proving that a verb needing the live `*Runner` relocates into a candy and
still dispatches in-process.

The `examplerunverb` verb echoes its `marker` **and** the live run mode read off
the `kit.CheckContext`, so a bed can assert the value round-trips author →
provider → result and that the verb reached engine state an out-of-process
`Invoke` could not. It is the `*Runner`-keeping analogue of the stateless
`candy/plugin-example` `exampleprobe`.

## What it provides

| Capability | Surface |
|---|---|
| `verb:examplerunverb` | the `examplerunverb:` check verb — a deterministic pass echoing its `marker` + the live run mode |

The plugin is a **`kit.CheckVerbProvider`**: it dispatches in-process via
`RunVerb` and keeps the live `*Runner`. Its `NewMeta()` advertises the
`#ExamplerunverbInput` schema and the `cmd/serve` shim serves it over go-plugin
gRPC via `sdk.ServeCheckVerb`, which reconstructs the `kit.CheckContext` from the
host's reverse channel — the same verb compiles into charly when listed in
`compiled_plugins:`.

## How to use it

Compose the plugin candy in a box or check bed's `candy:` list:

```yaml
- '@github.com/opencharly/plugin-examplerunverb/candy/plugin-examplerunverb:<tag>'
```

Then author the verb in a plan:

```yaml
- check: the examplerunverb verb echoes the marker + live mode
  id: examplerunverb-dispatches
  examplerunverb: {marker: examplerunverb-marker-ok}
  context: [runtime]
```

## Layout

- `candy/plugin-examplerunverb/` — the plugin module: `plugin.go` (the
  `kit.CheckVerbProvider` + `NewCheckVerb()`/`NewMeta()`),
  `schema/examplerunverb.cue` (the self-contained `#ExamplerunverbInput`),
  `params/cue_types_gen.go`, `cmd/serve/main.go`.
- `charly.yml` — the root project manifest (`discover: candy`).
- `.github/workflows/tag-on-merge.yml` — CalVer tag + `CHANGELOG/` on merge.

## Related

- Owning skill: `/charly-internals:plugin` — the plugin/provider model, including
  the `kit` check-verb contract. This candy carries no `skill:` entity of its own;
  the gap is tracked in
  [opencharly/opencharly#291](https://github.com/opencharly/opencharly/issues/291).
- [`opencharly/charly`](https://github.com/opencharly/charly) — the charly CLI.
