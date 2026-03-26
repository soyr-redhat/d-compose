# d-compose

Convert Docker files to Podman-compatible Containerfiles automatically.

## What it does

- Renames `Dockerfile` → `Containerfile`
- Renames `docker-compose.yml` → `compose.yml`
- Updates content to replace Docker commands with Podman equivalents
- Recursively processes entire directory trees
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
# Preview changes in current directory
d-compose --dry-run .

# Convert a specific project
d-compose --dry-run ~/projects/my-app

# Apply changes after preview
d-compose ~/projects/my-app

# Process entire directory tree with verbose output
d-compose -v /path/to/monorepo
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
