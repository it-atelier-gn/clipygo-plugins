# clipygo-plugins

[![Registry](https://img.shields.io/badge/clipygo-plugin--registry-blue)](https://github.com/it-atelier-gn/clipygo-plugins)

The official plugin registry for [clipygo](https://github.com/it-atelier-gn/clipygo) — a clipboard monitor that routes content to configured targets.

---

## How it works

`registry.json` is a simple index of published clipygo plugins. clipygo fetches this file from GitHub and displays available plugins in the Settings → Registry panel. Users can install a plugin with one click — the binary is downloaded, the SHA256 is verified, and the plugin is added automatically.

---

## registry.json format

```json
{
  "version": 1,
  "plugins": [
    {
      "id": "my-plugin",
      "name": "My Plugin",
      "description": "What it does",
      "author": "Your Name",
      "version": "1.0.0",
      "repo": "https://github.com/you/my-plugin",
      "platforms": {
        "windows-x64": {
          "url": "https://github.com/you/my-plugin/releases/download/v1.0.0/my-plugin-windows-x64.exe",
          "sha256": "abc123..."
        },
        "linux-x64": {
          "url": "https://github.com/you/my-plugin/releases/download/v1.0.0/my-plugin-linux-x64",
          "sha256": "def456..."
        },
        "macos-arm64": {
          "url": "https://github.com/you/my-plugin/releases/download/v1.0.0/my-plugin-macos-arm64",
          "sha256": "ghi789..."
        }
      }
    }
  ]
}
```

| Field | Description |
|---|---|
| `id` | Unique slug, lowercase with hyphens |
| `name` | Display name shown in clipygo UI |
| `description` | One-line description |
| `author` | Author name or GitHub handle |
| `version` | SemVer release tag |
| `repo` | GitHub repo URL |
| `platforms` | Per-platform download URL + SHA256 hash |

Supported platform keys: `windows-x64`, `linux-x64`, `macos-arm64`, `macos-x64`.

---

## Publishing a plugin

1. Write a plugin that speaks the [clipygo plugin protocol](https://github.com/it-atelier-gn/clipygo#-plugin-system) (JSON over stdin/stdout)
2. Set up a GitHub Actions release workflow that builds binaries and uploads them with `.sha256` files (see [clipygo-plugins-demo](https://github.com/it-atelier-gn/clipygo-plugins-demo) for a working example)
3. Open a PR adding your entry to `registry.json`

---

## Available plugins

| Plugin | Description | Author |
|---|---|---|
| [Demo Plugin](https://github.com/it-atelier-gn/clipygo-plugins-demo) | Demo subprocess provider with two hardcoded targets | Georg Nelles |

---

## Writing a plugin

A clipygo plugin is any executable that reads JSON from stdin and writes JSON to stdout — one object per line. The process stays alive for the session.

See the full protocol documentation in the [clipygo README](https://github.com/it-atelier-gn/clipygo#-plugin-system).

### Quick example (Rust)

```rust
// Cargo.toml: serde + serde_json
use std::io::{BufRead, Write};
use serde::{Deserialize, Serialize};

#[derive(Deserialize)]
#[serde(tag = "command", rename_all = "snake_case")]
enum Request { GetInfo, GetTargets, Send { target_id: String, content: String, format: String } }

fn main() {
    let stdin = std::io::stdin();
    let stdout = std::io::stdout();
    let mut out = stdout.lock();
    for line in stdin.lock().lines().flatten() {
        let response = match serde_json::from_str::<Request>(&line) {
            Ok(Request::GetInfo) => serde_json::json!({"name":"My Plugin","version":"1.0.0","description":"...","author":"..."}),
            Ok(Request::GetTargets) => serde_json::json!({"targets":[{"id":"t1","provider":"My Plugin","formats":["text"],"title":"Target","description":"...","image":""}]}),
            Ok(Request::Send { .. }) => serde_json::json!({"success":true}),
            Err(e) => serde_json::json!({"error": e.to_string()}),
        };
        let _ = writeln!(out, "{}", response);
        let _ = out.flush();
    }
}
```

---

## License

MIT © 2026 Georg Nelles
