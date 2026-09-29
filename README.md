# plugin-kernel-param

The `kernel-param` multi-role state-provision verb for
[opencharly/charly](https://github.com/opencharly/charly) — read a kernel sysctl
value (CHECK) and render a `sysctl -w` write (ACT).

The verb is host-coupled on the `sdk/kit` contract (`CheckVerbProvider` +
`ProvisionActor`), so it is **compiled-in only**.

## What it provides

| Capability | Surface |
|---|---|
| `verb:kernel-param` | the declarative `kernel-param:` check step, plus its `do:act` provision path |

## The verb

An authored `kernel-param: <key>` step (scalar sugar) or
`kernel-param: {kernel-param: …, value: …}` (map form). The
`kernel-param`-exclusive fields live in the plugin's own `#KernelParamInput`
(`schema/kernel_param.cue`); the matcher evaluation reuses the shared
`sdk.MatchAll`.

| Field | Meaning |
|---|---|
| `kernel-param` | the sysctl key, dot-separated (the verb discriminator) |
| `value` | a scalar or matcher list the read value is asserted against |

The CHECK reads `/proc/sys/<key-as-slashes>` directly (equivalent to
`sysctl -n` but needing no `procps-ng`, which minimal images omit). The ACT
renders `sysctl -w key=value` (the act runs where `procps-ng` is present).

```yaml
- check: the kernel reports Linux
  id: kernel-param-ostype
  kernel-param:
      kernel-param: kernel.ostype
      value:
          - Linux
  context: [runtime]
```

## How to use it

Compose the plugin candy in a box or check bed's `candy:` list:

```yaml
- '@github.com/opencharly/plugin-kernel-param/candy/plugin-kernel-param:<tag>'
```

## Layout

- `candy/plugin-kernel-param/` — the plugin module: `plugin.go`,
  `schema/kernel_param.cue` (the self-contained `#KernelParamInput`),
  `params/cue_types_gen.go`, `cmd/serve/main.go`.
- `candy/plugin-kernel-param/charly.yml` — the `plugin-kernel-param:` candy
  entity.
- `charly.yml` — the root project manifest (`discover: candy`).
- `.github/workflows/tag-on-merge.yml` — CalVer tag + `CHANGELOG/` on merge.

## Related

- Owning skill: `/charly-check:check` — the check verb catalog. This candy
  carries no `skill:` entity of its own; the gap is tracked in
  [opencharly/opencharly#291](https://github.com/opencharly/opencharly/issues/291).
- `/charly-internals:plugin` — the plugin/provider model, including the
  multi-role (check + act) provider.
- [`opencharly/charly`](https://github.com/opencharly/charly) — the charly CLI.
