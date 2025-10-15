# Copilot Instructions - Poltergeist Custom Sounds Pack

## Project Type
**Content-only mod pack** for Lethal Company - no code, no build process, no compilation. This repository contains only audio assets that extend the Poltergeist mod's ghost sound library.

## Repository Structure
```
BepInEx/plugins/PoltergeistSounds/  # 42 audio files (.ogg, .mp3, .wav)
.github/workflows/
  ├── build.yml                     # Label-driven versioning & release automation
  └── publish.yml                   # Thunderstore package publishing
.github/labeler.yml                 # Auto-labels PRs based on changed paths
README.md                           # User documentation
icon.png                            # Thunderstore package icon
```

## Critical Workflows

### Label-Driven Release System
The entire CI/CD is driven by automatic PR labels from `.github/labeler.yml`:

**`audio` label** (changes to `BepInEx/plugins/PoltergeistSounds/**`):
- Triggers version bump (patch only)
- Generates changelog from conventional commit messages
- Creates GitHub release
- Auto-publishes to Thunderstore

**`cicd` label** (changes to `.github/**`):
- No version bump or release
- Use for workflow testing on feature branches

**`documentation` label** (changes to `**/*.md`):
- No special automation

### Build Workflow (build.yml)
Runs on push to any branch except `master`:
1. Auto-creates PR to master (if none exists)
2. Applies labels via `actions/labeler@v6` based on file paths
3. **Conditional release**: Only if PR has `audio` label
   - Bumps version using `mathieudutour/github-tag-action@v6.2`
   - Generates `CHANGELOG.MD` with sections: 🎹 Audio, 🐛 Fixes, 🔩 CI/CD, 🧪 Tests
   - Creates GitHub release with tag and changelog

### Publish Workflow (publish.yml)
Triggers on GitHub release (created by build.yml) or manual dispatch:
- Fetches latest release version from GitHub API
- Uploads to Thunderstore using `GreenTF/upload-thunderstore-package@v4.3`
- **Required secret**: `THUNDERSTORE_SVC_API_KEY`
- **Dependency**: `coderCleric-Poltergeist-1.2.8`
- **Namespace**: `Bikininjas` | **Category**: `audio` | **Community**: `lethal-company`

## Development Conventions

### Adding Audio Files
1. Place files directly in `BepInEx/plugins/PoltergeistSounds/` (no subdirectories)
2. Use descriptive names: `SilentHillAmbiance.mp3`, `AirHorn1.ogg`, `Return by Death.mp3`
3. Formats: `.ogg`, `.mp3`, `.wav` (all supported by Unity, no preference)
4. Commit with conventional commit prefix: `audio: Add [description]` or `sound: Add [description]`

### Commit Message Format
Use conventional commit prefixes for proper changelog categorization:
- `audio:` / `sound:` → 🎹 Audio section (triggers release)
- `fix:` / `fixes:` → 🐛 Fixes section
- `ci:` / `cicd:` / `cd:` → 🔩 CI/CD section (no release)
- `test:` / `tests:` → 🧪 Tests section
- `docs:` → Uncategorized (no special handling)

Example: `audio: Add ice cream truck jingle variations`

### Testing Workflow Changes
- Always test on feature branches (not `master`)
- CI/CD changes do NOT trigger releases (by design)
- Use `workflow_dispatch` for manual testing of publish.yml
- Auto-merge for `cicd` PRs is commented out until workflows are stable

## Key Constraints

### What This Repository Is NOT
- ❌ Not a C# mod - don't add `.cs`, `.csproj`, or Visual Studio files
- ❌ Not a Unity project - `.gitignore` is a template artifact, ignore it
- ❌ No build/compile step - audio files are used directly by game
- ❌ No manual versioning - versions are auto-generated from git tags

### Version Management
- **Auto-versioned only**: `mathieudutour/github-tag-action` handles all version bumps
- **Patch bumps only**: Default bump is `patch` (e.g., 1.0.5 → 1.0.6)
- **Never manually edit**: No manifest.json or version files exist - versions live in git tags
- **Thunderstore sync**: publish.yml pulls version from latest GitHub release tag

### Secrets & Permissions
- `THUNDERSTORE_SVC_API_KEY` - Required for Thunderstore publishing
- `PAT` - Optional GitHub Personal Access Token for enhanced PR permissions (falls back to `GITHUB_TOKEN`)
- Workflow needs `permissions: write-all` for tagging and releases

## Common Mistakes to Avoid
1. **Manual versioning**: Don't create version files or try to manage versions manually
2. **Subdirectories**: Don't organize sounds into subfolders - Poltergeist expects flat structure
3. **Wrong commit prefix**: Use `audio:` not `feat:` to ensure release is triggered
4. **Editing master directly**: Always use feature branches - workflows expect PR flow
5. **Missing secrets**: Verify `THUNDERSTORE_SVC_API_KEY` is set before expecting Thunderstore publishes
