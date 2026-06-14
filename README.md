# dprint-plugin-json-schema-sort

A [dprint](https://dprint.dev) Wasm plugin that deterministically sorts JSON Schema files into a stable, schema-aware
key order so diffs stop being noisy.

It wraps the [`json-schema-sort`](https://github.com/kjanat/json-schema-sort-rs) library. It sorts keys; it does not
reformat whitespace — pair it with `dprint-plugin-json` for that.

## Install

```sh
dprint add kjanat/json-schema-sort
```

This resolves to this repository's latest release via plugins.dprint.dev. You can also pin the full URL:

```sh
dprint add https://github.com/kjanat/dprint-plugin-json-schema-sort/releases/latest/download/plugin.wasm
```

## Which files it formats

dprint runs exactly one plugin per file. By default this plugin only claims files **named exactly `schema.json`** — it
deliberately does not claim every `.json` (that would collide with `dprint-plugin-json` and reorder unrelated files like
`package.json`). So out of the box, with no extra config, bare `schema.json` files are sorted and everything else is
left to the JSON plugin.

To cover schemas with other names (`product.schema.json`) or in a known directory, add an `associations` glob —
associations take precedence over the built-in matching, so the listed files route to this plugin:

```jsonc
{
  "json": {},
  "jsonSchemaSort": {
    // Optional — widen beyond the default bare `schema.json`.
    // `associations` nests inside the plugin's own config as an array.
    "associations": ["**/schema.json", "**/*.schema.json"],
    // or by location: ["schemas/**/*.json"]
  },
  "plugins": [
    "https://plugins.dprint.dev/json-x.x.x.wasm",
    "https://plugins.dprint.dev/kjanat/json-schema-sort-x.x.x.wasm",
  ],
}
```

Note: a file routed to this plugin is sorted and re-emitted as 2-space JSON; it does not also pass through
`dprint-plugin-json`'s whitespace config.

## Configuration

| Key                     | Type    | Default | Description                                                           |
| ----------------------- | ------- | ------- | --------------------------------------------------------------------- |
| `sortArrays`            | boolean | `true`  | Sort schema-safe string arrays such as `required` and `type`.         |
| `preservePropertyOrder` | boolean | `false` | Keep property and `$defs` names in document order instead of sorting. |

## Build

```sh
cargo test
cargo build --profile wasm-release --target wasm32-unknown-unknown
```

The release workflow publishes `plugin.wasm` and `deployment/schema.json` on each `v*` tag.
