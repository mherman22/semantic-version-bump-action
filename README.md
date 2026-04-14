# Semantic Version Bump Action

A GitHub Action that handles semantic versioning for Maven and Node.js projects. Supports various release stages and automatically updates both `pom.xml` and `package.json` files.

## Features

- ✅ **Semantic Versioning** - Proper major.minor.patch bumping
- ✅ **Release Stages** - alpha, beta, final, snapshot support  
- ✅ **Multi-Platform** - Maven (pom.xml) + Node.js (package.json)
- ✅ **Flexible File Detection** - Works with Maven-only, Node.js-only, or hybrid projects
- ✅ **Development Versions** - Automatic next dev version calculation
- ✅ **Configurable** - Custom file paths and intelligent fallbacks

## Usage

### Basic Usage

```yaml
- name: Bump version
  id: bump
  uses: mherman22/semantic-version-bump-action@v1
  with:
    version-type: 'minor'
    release-stage: 'beta'
```

### Advanced Usage

```yaml
- name: Bump version
  id: bump
  uses: mherman22/semantic-version-bump-action@v1
  with:
    version-type: 'major'
    release-stage: 'final'
    backend-file: 'custom-pom.xml'
    frontend-file: 'ui/package.json'
```

### Node.js-Only Projects

```yaml
- name: Bump version in Node.js project
  id: bump
  uses: mherman22/semantic-version-bump-action@v1
  with:
    version-type: 'minor'
    release-stage: 'beta'
    backend-file: 'nonexistent.xml'  # Will be ignored if doesn't exist
    frontend-file: 'package.json'    # Version read from here
```

### Maven-Only Projects

```yaml
- name: Bump version in Maven project
  id: bump
  uses: mherman22/semantic-version-bump-action@v1
  with:
    version-type: 'fix'
    release-stage: 'final'
    backend-file: 'pom.xml'          # Version read from here
    frontend-file: 'nonexistent.json' # Will be ignored if doesn't exist
```

## Inputs

| Input | Description | Required | Default |
|-------|-------------|----------|---------|
| `version-type` | Type of version bump (`major`, `minor`, `fix`) | Yes | `fix` |
| `release-stage` | Release stage (`alpha`, `beta`, `final`, `snapshot`) | Yes | `alpha` |
| `backend-file` | Path to Maven pom.xml file | No | `pom.xml` |
| `frontend-file` | Path to Node.js package.json file | No | `frontend/package.json` |

## Outputs

| Output | Description | Example |
|--------|-------------|---------|
| `new-version` | The new semantic version (without suffix) | `1.2.3` |
| `release-tag` | The release tag with stage suffix | | `1.2.3-beta` |
| `frontend-version` | The frontend version | `1.2.3` |
| `stage-label` | The release stage label | `beta` |
| `next-dev-version` | The next development version (for final releases) | `1.2.4-SNAPSHOT` |
| `current-version` | The current version before bump | `1.2.2-SNAPSHOT` |

## File Detection Logic

The action intelligently detects project types and reads versions from available files:

1. **Backend file first** - Tries to read from `backend-file` (default: `pom.xml`)
2. **Frontend fallback** - If backend file doesn't exist, reads from `frontend-file` (default: `package.json`)
3. **Version format detection** - Supports both Maven (`<version>1.2.3</version>`) and NPM (`"version": "1.2.3"`) formats
4. **Flexible updates** - Updates only the files that exist in your project

### Supported Project Types

| Project Type | Version Source | Updated Files |
|--------------|----------------|---------------|
| **Maven + Node.js** | `pom.xml` → `package.json` | Both files |
| **Maven only** | `pom.xml` | `pom.xml` only |
| **Node.js only** | `package.json` | `package.json` only |

## Examples

### Version Bump Types

```yaml
# Patch release: 1.2.3 → 1.2.4
version-type: 'fix'

# Minor release: 1.2.3 → 1.3.0  
version-type: 'minor'

# Major release: 1.2.3 → 2.0.0
version-type: 'major'
```

### Release Stages

```yaml
# Alpha release: 1.2.3-alpha
release-stage: 'alpha'

# Beta release: 1.2.3-beta
release-stage: 'beta'

# Final release: 1.2.3
release-stage: 'final'

# Snapshot: 1.2.3-SNAPSHOT
release-stage: 'snapshot'
```

### Complete Workflow Example

```yaml
name: Release
on:
  workflow_dispatch:
    inputs:
      version_type:
        type: choice
        options: [fix, minor, major]
      release_stage:
        type: choice  
        options: [alpha, beta, final]

jobs:
  release:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v4
      
      - name: Bump version
        id: bump
        uses: mherman22/semantic-version-bump-action@v1
        with:
          version-type: ${{ inputs.version_type }}
          release-stage: ${{ inputs.release_stage }}
      
      - name: Use outputs
        run: |
          echo "New version: ${{ steps.bump.outputs.new-version }}"
          echo "Release tag: ${{ steps.bump.outputs.release-tag }}"
          echo "Next dev: ${{ steps.bump.outputs.next-dev-version }}"
```

## License

MIT License - see [LICENSE](LICENSE) file for details.

## Contributing

1. Fork the repository
2. Create a feature branch
3. Make your changes
4. Add tests if needed
5. Submit a pull request

## Support

If you encounter any issues or have feature requests, please [open an issue](../../issues).