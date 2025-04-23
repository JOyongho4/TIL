## GitHub Copilot Chat

- Extension Version: 0.22.4 (prod)
- VS Code: vscode/1.95.3
- OS: Mac

## Network

User Settings:
```json
  "github.copilot.advanced": {
    "debug.useElectronFetcher": true,
    "debug.useNodeFetcher": false
  }
```

Connecting to https://api.github.com:
- DNS ipv4 Lookup: 20.200.245.245 (12 ms)
- DNS ipv6 Lookup: ::ffff:20.200.245.245 (2 ms)
- Electron Fetcher (configured): HTTP 200 (8 ms)
- Node Fetcher: HTTP 200 (27 ms)
- Helix Fetcher: HTTP 200 (142 ms)

Connecting to https://api.individual.githubcopilot.com/_ping:
- DNS ipv4 Lookup: 140.82.113.21 (87 ms)
- DNS ipv6 Lookup: ::ffff:140.82.113.21 (1 ms)
- Electron Fetcher (configured): HTTP 200 (252 ms)
- Node Fetcher: HTTP 200 (874 ms)
- Helix Fetcher: 