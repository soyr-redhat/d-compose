# d-compose

Convert Docker files to Podman-compatible formats automatically across your entire project.

## What it does

**By default, converts everything:**
- Renames `Dockerfile` → `Containerfile`
- Renames `docker-compose.yml` → `compose.yml`
- Updates Kubernetes/OpenShift YAML manifests
- Updates CI/CD pipeline files (GitHub Actions, GitLab CI, etc.)
- Updates shell scripts with Docker commands
- Updates other YAML files with Docker references
- Recursively processes entire directory trees
- Replaces Docker commands with Podman equivalents throughout
- Skips hidden directories and common ignore patterns (`.git`, `node_modules`, `venv`, etc.)

## Installation

### Option 1: Clone the repository

```bash
git clone https://github.com/soyr-redhat/d-compose.git
cd d-compose
chmod +x d-compose
```

Then add to PATH using one of the methods below for your OS.

### Option 2: Download directly

```bash
curl -O https://raw.githubusercontent.com/soyr-redhat/d-compose/main/d-compose
chmod +x d-compose
```

---

### Adding to PATH

#### macOS / Linux (bash)

1. Move the script to a location of your choice (or use the cloned directory):

```bash
# Example: move to ~/bin
mkdir -p ~/bin
mv d-compose ~/bin/
```

2. Add to your PATH by editing `~/.bashrc` (or `~/.bash_profile` on older macOS):

```bash
echo 'export PATH="$PATH:$HOME/bin"' >> ~/.bashrc
source ~/.bashrc
```

#### macOS / Linux (zsh)

1. Move the script to a location of your choice:

```bash
# Example: move to ~/bin
mkdir -p ~/bin
mv d-compose ~/bin/
```

2. Add to your PATH by editing `~/.zshrc`:

```bash
echo 'export PATH="$PATH:$HOME/bin"' >> ~/.zshrc
source ~/.zshrc
```

#### Linux (system-wide installation)

```bash
# Install for all users (requires sudo)
sudo mv d-compose /usr/local/bin/
sudo chmod +x /usr/local/bin/d-compose
```

#### Windows (PowerShell)

1. Create a directory for your scripts (if it doesn't exist):

```powershell
New-Item -Path "$env:USERPROFILE\bin" -ItemType Directory -Force
```

2. Move the script there:

```powershell
Move-Item d-compose "$env:USERPROFILE\bin\d-compose"
```

3. Add to your PATH (run PowerShell as Administrator):

```powershell
# Get current PATH
$currentPath = [Environment]::GetEnvironmentVariable("Path", "User")

# Add new directory to PATH
[Environment]::SetEnvironmentVariable(
    "Path",
    "$currentPath;$env:USERPROFILE\bin",
    "User"
)
```

4. Restart your terminal and verify:

```powershell
d-compose --help
```

#### Windows (WSL - Windows Subsystem for Linux)

Follow the Linux instructions above within your WSL environment.

---

### Verify Installation

After adding to PATH, verify it works:

```bash
d-compose --help
```

## Usage

```bash
# Convert all Docker files to Podman (recommended - does everything)
d-compose .

# Convert files in specific directory
d-compose /path/to/project

# Preview changes without applying (dry run)
d-compose . --dry-run

# Skip specific file types
d-compose . --skip-k8s          # Skip Kubernetes/OpenShift YAML
d-compose . --skip-ci           # Skip CI/CD pipelines
d-compose . --skip-scripts      # Skip shell scripts
d-compose . --skip-yaml         # Skip other YAML files

# Only rename Dockerfiles, don't update content
d-compose . --no-content

# Verbose output
d-compose . -v

# Combine options
d-compose ../my-project --dry-run -v
d-compose . --skip-k8s --skip-ci --dry-run
```

## Examples

```bash
# Preview all changes in current directory (recommended first step)
d-compose --dry-run .

# Convert everything in a project (Dockerfiles, K8s, CI/CD, scripts, YAML)
d-compose ~/projects/my-app

# Convert only Dockerfiles and compose files, skip everything else
d-compose . --skip-k8s --skip-ci --skip-scripts --skip-yaml

# Convert a monorepo with verbose output to see all changes
d-compose -v /path/to/monorepo

# Preview K8s manifest changes without touching CI/CD
d-compose --dry-run --skip-ci /path/to/k8s-project
```

## What gets converted

### File renames:
- `Dockerfile` → `Containerfile`
- `Dockerfile.dev` → `Containerfile.dev`
- `Dockerfile.*` → `Containerfile.*`
- `docker-compose.yml` → `compose.yml`
- `docker-compose.yaml` → `compose.yaml`

### Content replacements (across all file types):
**Docker commands:**
- `docker build` → `podman build`
- `docker run` → `podman run`
- `docker push` → `podman push`
- `docker pull` → `podman pull`
- `docker tag` → `podman tag`
- `docker login` → `podman login`
- `docker images` → `podman images`
- `docker ps` → `podman ps`
- `docker exec` → `podman exec`
- `docker logs` → `podman logs`
- `docker stop/start/rm/rmi` → `podman stop/start/rm/rmi`
- `docker-compose` → `podman-compose`

**Image references:**
- `FROM docker.io/` → `FROM `
- `image: docker.io/` → `image: `

**Kubernetes/OpenShift specific:**
- `/var/run/docker.sock` → `/run/podman/podman.sock`

### Files processed:
**Always processed:**
- Dockerfiles (any name matching `Dockerfile` or `Dockerfile.*`)
- docker-compose files (`docker-compose.yml`, `docker-compose.yaml`)

**Processed by default (use --skip flags to exclude):**
- **Kubernetes/OpenShift:** Deployments, Services, Pods, ConfigMaps, Routes, BuildConfigs, etc.
- **CI/CD pipelines:** `.github/workflows/*.yml`, `.gitlab-ci.yml`, Jenkins files, CircleCI configs
- **Shell scripts:** Any `.sh` files
- **Other YAML:** Any `.yml` or `.yaml` files with Docker references

## Safety

- Skips files if target already exists (won't overwrite `Containerfile` if it exists)
- Use `--dry-run` to preview changes first
- Only processes files in the specified directory tree
- Automatically skips hidden directories and common patterns

## Requirements

- Python 3.6+
- No external dependencies
