# d-compose

Convert Docker files to Podman-compatible Containerfiles automatically.

## What it does

- Renames `Dockerfile` → `Containerfile`
- Renames `docker-compose.yml` → `compose.yml`
- Updates content to replace Docker commands with Podman equivalents
- Recursively processes entire directory trees
- Skips hidden directories and common ignore patterns (`.git`, `node_modules`, `venv`, etc.)

## Installation

### Quick Install (Add to PATH)

1. Add this directory to your PATH by adding to your `~/.zshrc`:

```bash
export PATH="$PATH:/Users/sbowerma/Code/d-compose"
```

2. Reload your shell:

```bash
source ~/.zshrc
```

3. Verify installation:

```bash
d-compose --help
```

### Alternative: Symlink to local bin

```bash
ln -s /Users/sbowerma/Code/d-compose/d-compose /usr/local/bin/d-compose
```

## Usage

```bash
# Convert files in current directory
d-compose .

# Convert files in specific directory
d-compose /path/to/project

# Preview changes without applying (dry run)
d-compose . --dry-run

# Only rename files, don't update content
d-compose . --no-content

# Verbose output
d-compose . -v

# Combine options
d-compose ../4_pillars_demos --dry-run -v
```

## Examples

```bash
# Test on the 4_pillars_demos directory
cd /Users/sbowerma/Code
d-compose --dry-run 4_pillars_demos/2_inference_vllm

# Apply changes
d-compose 4_pillars_demos/2_inference_vllm
```

## What gets converted

### File renames:
- `Dockerfile` → `Containerfile`
- `Dockerfile.dev` → `Containerfile.dev`
- `docker-compose.yml` → `compose.yml`
- `docker-compose.yaml` → `compose.yaml`

### Content replacements:
- `docker build` → `podman build`
- `docker run` → `podman run`
- `docker push` → `podman push`
- `docker pull` → `podman pull`
- `docker-compose` → `podman-compose`
- `FROM docker.io/` → `FROM `

## Safety

- Skips files if target already exists (won't overwrite `Containerfile` if it exists)
- Use `--dry-run` to preview changes first
- Only processes files in the specified directory tree
- Automatically skips hidden directories and common patterns

## Requirements

- Python 3.6+
- No external dependencies
