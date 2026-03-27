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
git clone https://github.com/soyr-redhat/dcomp.git
cd d-compose
chmod +x dcomp
```

Then add to PATH using one of the methods below for your OS.

### Option 2: Download directly

```bash
curl -O https://raw.githubusercontent.com/soyr-redhat/dcomp/main/dcomp
chmod +x dcomp
```

---

### Adding to PATH

#### macOS / Linux (bash)

1. Move the script to a location of your choice (or use the cloned directory):

```bash
# Example: move to ~/bin
mkdir -p ~/bin
mv dcomp ~/bin/
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
mv dcomp ~/bin/
```

2. Add to your PATH by editing `~/.zshrc`:

```bash
echo 'export PATH="$PATH:$HOME/bin"' >> ~/.zshrc
source ~/.zshrc
```

#### Linux (system-wide installation)

```bash
# Install for all users (requires sudo)
sudo mv dcomp /usr/local/bin/
sudo chmod +x /usr/local/bin/dcomp
```

#### Windows (PowerShell)

1. Create a directory for your scripts (if it doesn't exist):

```powershell
New-Item -Path "$env:USERPROFILE\bin" -ItemType Directory -Force
```

2. Move the script there:

```powershell
Move-Item dcomp "$env:USERPROFILE\bin\dcomp"
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

```bash
dcomp --help
```

#### Windows (WSL - Windows Subsystem for Linux)

Follow the Linux instructions above within your WSL environment.

---

### Verify Installation

After adding to PATH, verify it works:

```bash
dcomp --help
```

## Usage

```bash
# Convert all Docker files to Podman (recommended - does everything)
dcomp .

# Convert files in specific directory
dcomp /path/to/project

# Preview changes without applying (dry run)
dcomp . --dry-run

# Skip specific file types
dcomp . --skip-k8s          # Skip Kubernetes/OpenShift YAML
dcomp . --skip-ci           # Skip CI/CD pipelines
dcomp . --skip-scripts      # Skip shell scripts
dcomp . --skip-yaml         # Skip other YAML files

# Only rename Dockerfiles, don't update content
dcomp . --no-content

# Verbose output
dcomp . -v

# Combine options
dcomp ../my-project --dry-run -v
dcomp . --skip-k8s --skip-ci --dry-run
```

## Examples

```bash
# Preview all changes in current directory (recommended first step)
dcomp --dry-run .

# Convert everything in a project (Dockerfiles, K8s, CI/CD, scripts, YAML)
dcomp ~/projects/my-app

# Convert only Dockerfiles and compose files, skip everything else
dcomp . --skip-k8s --skip-ci --skip-scripts --skip-yaml

# Convert a monorepo with verbose output to see all changes
dcomp -v /path/to/monorepo

# Preview K8s manifest changes without touching CI/CD
dcomp --dry-run --skip-ci /path/to/k8s-project
```

## What gets converted

### File renames:
- `Dockerfile` → `Containerfile`
- `Dockerfile.dev` → `Containerfile.dev`
- `Dockerfile.*` → `Containerfile.*`
- `docker-compose.yml` → `compose.yml`
- `docker-compose.yaml` → `compose.yaml`

### Content replacements (across all file types):
**Docker commands (safe, functional replacements):**
- `docker build` → `podman build`
- `docker run` → `podman run`
- `docker push` → `podman push`
- `docker pull` → `podman pull`
- `docker tag` → `podman tag`
- `docker login` → `podman login`
- `docker logout` → `podman logout`
- `docker images` → `podman images`
- `docker ps` → `podman ps`
- `docker exec` → `podman exec`
- `docker logs` → `podman logs`
- `docker stop/start/rm/rmi` → `podman stop/start/rm/rmi`
- `docker-compose` → `podman-compose`

**Socket paths (required for Podman):**
- `/var/run/docker.sock` → `/run/podman/podman.sock`
- `var/run/docker.sock` → `run/podman/podman.sock`

**What we DON'T change:**
- Registry prefixes (`docker.io/`) are kept as-is for safety
- Image names and tags remain unchanged
- Only commands are converted, not image references

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
