# dprint-plugin-json-schema-sort

A [dprint](https://dprint.dev) Wasm plugin that deterministically sorts JSON
Schema files into a stable, schema-aware key order so diffs stop being noisy.

It wraps the [`json-schema-sort`](https://github.com/kjanat/json-schema-sort-rs)
library. It sorts keys; it does not reformat whitespace — pair it with
`dprint-plugin-json` for that.

## Install

```sh
dprint config add kjanat/json-schema-sort
```

This resolves to this repository's latest release via plugins.dprint.dev. You
can also pin the full URL:

```sh
dprint config add https://github.com/kjanat/dprint-plugin-json-schema-sort/releases/latest/download/plugin.wasm
```

Because dprint associates one plugin per file extension, scope it to schema
files with an `associations` entry in `dprint.json`:

```jsonc
{
  "json": {},
  "jsonSchemaSort": {},
  "associations": {
    "jsonSchemaSort": ["**/*.schema.json", "**/schema.json"]
  },
  "plugins": [
    "https://plugins.dprint.dev/json-x.x.x.wasm",
    "https://plugins.dprint.dev/kjanat/json-schema-sort-x.x.x.wasm"
  ]
}
```

## Configuration

| Key                     | Type    | Default | Description                                                          |
| ----------------------- | ------- | ------- | -------------------------------------------------------------------- |
| `sortArrays`            | boolean | `true`  | Sort schema-safe string arrays such as `required` and `type`.        |
| `preservePropertyOrder` | boolean | `false` | Keep property and `$defs` names in document order instead of sorting. |

## Build

```sh
cargo test
cargo build --profile wasm-release --target wasm32-unknown-unknown
```

The release workflow publishes `plugin.wasm` and `deployment/schema.json` on
each `v*` tag.
