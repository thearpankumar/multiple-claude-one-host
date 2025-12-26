# Claude Personal (Dockerized Claude Code)

Run a **second Claude Pro account in parallel** using Docker, without affecting your native Claude Code installation.

This setup provides **strict isolation**, **persistent authentication**, **correct file ownership**, and opens Claude **in the exact directory you run it from**.

---

## What This Solves

- Claude Code supports only **one active account per environment**
- Logging in/out repeatedly is disruptive
- Docker allows a clean, parallel environment with zero conflicts

---

## Features

- Parallel Claude accounts (native + Docker)
- Persistent authentication using a Docker volume
- Claude opens in the current working directory
- Files created/edited are owned by your user (not root)
- No config or credential leakage
- Rust 1.91.1 and Python 3.13.9 included
- Supports fish, bash, and zsh

---

## Requirements

- Docker
- Claude Code installed locally (left untouched)

---

## Build the Image

```bash
docker build -t claude-personal -f Dockerfile.claude-personal .
```

---

## One-Time Authentication Setup

Create a persistent auth volume:

```bash
docker volume create claude_personal_auth
```

Login once:

```bash
docker run -it --rm \
  -v claude_personal_auth:/home/claude \
  -e HOME=/home/claude \
  claude-personal
```

Inside the container:

```bash
claude auth login
```

---

## Shell Setup

### Fish

```fish
funced claude-personal
```

```fish
function claude-personal
    set host_pwd (pwd)
    set uid (id -u)
    set gid (id -g)

    docker run -it --rm \
        --user "$uid:$gid" \
        -e HOME=/home/claude \
        -v claude_personal_auth:/home/claude \
        -v "$host_pwd:$host_pwd" \
        -w "$host_pwd" \
        claude-personal
end
```

```fish
funcsave claude-personal
```

---

### Bash / Zsh

Add to `~/.bashrc` or `~/.zshrc`:

```bash
claude-personal() {
  UID=$(id -u)
  GID=$(id -g)
  HOST_PWD="$(pwd)"

  docker run -it --rm \
    --user "$UID:$GID" \
    -e HOME=/home/claude \
    -v claude_personal_auth:/home/claude \
    -v "$HOST_PWD:$HOST_PWD" \
    -w "$HOST_PWD" \
    claude-personal
}
```

Reload shell:

```bash
source ~/.bashrc   # or source ~/.zshrc
```

---

## Usage

Run Claude Personal from any project directory:

```bash
cd ~/codes/my-project
claude-personal
```

Claude opens **inside that exact directory**.

Verify if needed:

```bash
pwd
```

---

## Daily Workflow Example

```bash
cd ~/codes/work-project
claude           # native Claude (work account)

cd ~/codes/side-project
claude-personal  # Docker Claude (personal account)
```

Both run in parallel with full isolation.

---

## Installed Toolchain

- Claude Code
- Rust 1.91.1
- Python 3.13.9
- Git, curl, build essentials

---

## Notes

- The container runs as your host user (UID/GID)
- Files created or modified by Claude are owned by you
- Only the current directory is mounted into the container
- Do not reuse the auth volume for other containers
- One account equals one container environment

---

## License

Internal / personal tooling.  
Use in accordance with Anthropic’s terms of service.
