# Codex Integration Guide

## Overview

Ollama provides two integration methods for Codex: CLI (`codex`) and desktop app (`codex-app`). Both configure Codex to use the local Ollama server at `http://localhost:11434/v1`.

## Integration Types

### 1. Codex CLI (`codex`)

**Command**: `ollama launch codex`

**Configuration**:
- Modifies: `~/.codex/config.toml`
- Creates profile: `profiles.ollama-launch`
- Sets `openai_base_url` and `model_provider` settings

### 2. Codex App (`codex-app`)

**Command**: `ollama launch codex-app [--model <name>]`

**Configuration**:
- Modifies: `~/.codex/config.toml`
- Creates profile: `profiles.ollama-launch-codex-app`
- Creates model catalog: `~/.codex/models/ollama-launch-models.json`
- Stores restore state: `~/.ollama/launch/codex-app-restore.json`

## How It Works

### Configuration Flow

1. **Save Original State**: Original config values are backed up before modification
2. **Write New Config**: Ollama profile and provider settings are written to `config.toml`
3. **Activate**: Root-level config keys are set to use the Ollama profile

### File Locations

| File | Description |
|------|-------------|
| `~/.codex/config.toml` | Codex configuration (modified) |
| `~/.codex/models/ollama-launch-models.json` | Model catalog (created by codex-app) |
| `~/.ollama/backup/` | Backup directory for config files |
| `~/.ollama/backup/codex-app/` | Integration-specific backups |
| `~/.ollama/launch/codex-app-restore.json` | Original config state for restoration |

## Restore Process

### To Restore Original Configuration

```bash
ollama launch codex-app --restore
```

### What Restoration Does

1. Reads original config from `~/.ollama/launch/codex-app-restore.json`
2. Restores `~/.codex/config.toml` to its pre-launch state
3. Removes the restore state file
4. Removes the model catalog file

### Manual Restoration

If needed, manually restore using the backup files:

```bash
# List backups
ls -la ~/.ollama/backup/codex-app/

# Copy a backup back
cp ~/.ollama/backup/codex-app/.codex.config.toml.<timestamp> ~/.codex/config.toml
```

## Safety Guarantees

### Backup Mechanism

- **Pre-write backup**: Backup created before any file modification
- **Atomic writes**: Uses temp file + rename for safety
- **Timestamped backups**: Format `.codex.config.toml.<unix_timestamp>`
- **Bounded retention**: Keeps only 5 most recent backups per file
- **Integration isolation**: Backups stored under `~/.ollama/backup/<integration>/`

### Failure Protection

| Failure Type | Protection |
|-------------|------------|
| Backup creation fails | Error returned; no changes made to target |
| Partial write | Temp file ensures atomicity |
| Rename failure | Backup restored to original location |
| Orphan temp files | Cleaned up on failure |

## Configuration Details

### codex Profile Settings

```toml
[profiles.ollama-launch]
openai_base_url = "http://localhost:11434/v1"
model_provider = "ollama-launch"
forced_login_method = "api"

[profiles.ollama-launch.providers.ollama]
name = "Ollama"
base_url = "http://localhost:11434/v1"
wire_api = "responses"
```

### Restore State Format

```json
{
  "had_profile": true,
  "profile": "original-profile-name",
  "had_model": true,
  "model": "original-model",
  "had_model_provider": true,
  "model_provider": "original-provider",
  "had_model_catalog_json": true,
  "model_catalog_json": "original/catalog/path.json"
}
```
