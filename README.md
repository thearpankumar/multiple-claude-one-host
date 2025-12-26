# Claude Personal (Dockerized Claude Code)

Run a second Claude Pro account in parallel using Docker, without touching your locally installed Claude Code.

This setup provides strict isolation, persistent authentication, and opens Claude in the exact directory you run it from.

---

## Features

- Parallel Claude accounts (local + Docker)
- Persistent authentication via Docker volume
- Opens Claude in the current working directory
- No credential or config conflicts
- Rust 1.91.1 and Python 3.13.9 included
- Works with fish, bash, and zsh

---

## Requirements

- Docker
- Claude Code installed locally (unchanged)

---

## Build Image

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
  -v claude_personal_auth:/root \
  -v ~/codes:/workspace \
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

    docker run -it --rm \
        -v claude_personal_auth:/root \
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
  docker run -it --rm \
    -v claude_personal_auth:/root \
    -v "$(pwd):$(pwd)" \
    -w "$(pwd)" \
    claude-personal
}
```

Reload shell:

```bash
source ~/.bashrc   # or source ~/.zshrc
```

---

## Usage

Run from any project directory:

```bash
cd ~/codes/my-project
claude-personal
```

Claude opens inside that directory.

Verify if needed:

```bash
pwd
```

---

## Daily Workflow Example

```bash
cd ~/codes/work-project
claude           # local Claude account

cd ~/codes/side-project
claude-personal  # Docker Claude account
```

---

## Installed Toolchain

- Claude Code
- Rust 1.91.1
- Python 3.13.9
- Git, curl, build tools

---

## Notes

- Only the current directory is mounted into the container
- Do not reuse the auth volume for other containers
- One account equals one container environment

---

## License

Internal / personal tooling. Use in accordance with Anthropic’s terms.
