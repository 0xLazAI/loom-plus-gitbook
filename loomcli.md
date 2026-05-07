# loomcli Access

`loomcli` is a lightweight Node.js CLI for calling Loom+ APIs from your terminal.

Repository: [0xLazAI/loomcli](https://github.com/0xLazAI/loomcli)

## Requirements

- Node.js 18 or newer.
- A Loom+ access token from `Admin -> Access Token`.
- Network access to your Loom+ deployment.

## Install

```bash
git clone https://github.com/0xLazAI/loomcli.git
cd loomcli
npm install
npm link
```

## Login

```bash
loom login --server https://YOUR_DOMAIN --token YOUR_ACCESS_TOKEN
```

You can also run:

```bash
loom login
```

## Common Commands

```bash
loom list
loom help create_project
loom run create_project --json '{"name":"My Project"}'
loom whoami
loom logout
```

## Config Files

- Config: `~/.loom-cli/config.json`
- Tool cache: `~/.loom-cli/tools.json`

Run `loom list` to refresh the tool cache.
