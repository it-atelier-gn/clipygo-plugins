# clipygo-plugins

[![Registry](https://img.shields.io/badge/clipygo-plugin--registry-blue)](https://github.com/it-atelier-gn/clipygo-plugins)

The official plugin registry for [clipygo](https://github.com/it-atelier-gn/clipygo).

clipygo fetches `registry.json` from this repo and displays available plugins in Settings → Registry. Users can install a plugin with one click — the binary is downloaded, SHA256-verified, and registered automatically.

## Available plugins

| Plugin | Description |
|---|---|
| [Demo Plugin](https://github.com/it-atelier-gn/clipygo-plugin-demo) | Reference plugin — two demo targets, showcases the full protocol including config and image handling |
| [MS Teams](https://github.com/it-atelier-gn/clipygo-plugin-msteams) | Send clipboard content to Teams chats and channels via the Graph API |
| [Encrypted Relay](https://github.com/it-atelier-gn/clipygo-plugin-relay) | E2E encrypted clipboard sharing between users via a zero-knowledge relay server |
| [Obsidian](https://github.com/it-atelier-gn/clipygo-plugin-obsidian) | Append clipboard content to a daily note, inbox, or new note in an Obsidian vault (text + images) |
| [Telegram](https://github.com/it-atelier-gn/clipygo-plugin-telegram) | Send clipboard content to Telegram chats via a bot (text + images) |
| [Discord](https://github.com/it-atelier-gn/clipygo-plugin-discord) | Send clipboard content to Discord channels via a bot (auto-discovers guilds/channels) |

## Publishing a plugin

1. Write a plugin that speaks the [clipygo plugin protocol](https://github.com/it-atelier-gn/clipygo/blob/main/docs/plugins.md)
2. Set up a release workflow that builds binaries and publishes SHA256 checksums (see [clipygo-plugin-demo](https://github.com/it-atelier-gn/clipygo-plugin-demo) for an example)
3. Open a PR adding your entry to `registry.json`

### registry.json format

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
          "url": "https://github.com/.../my-plugin-windows-x64.exe",
          "sha256": "abc123..."
        },
        "linux-x64": {
          "url": "https://github.com/.../my-plugin-linux-x64",
          "sha256": "def456..."
        },
        "macos-arm64": {
          "url": "https://github.com/.../my-plugin-macos-arm64",
          "sha256": "ghi789..."
        }
      }
    }
  ]
}
```

Supported platform keys: `windows-x64`, `linux-x64`, `macos-arm64`, `macos-x64`.

## License

MIT © 2026 Georg Nelles
