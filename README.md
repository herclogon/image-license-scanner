# Docker Image Package & License Scanner

A comprehensive bash script to scan Docker images for installed packages and their licenses. Supports multiple package managers and extracts copyright files from container filesystems.

## Features

- **Multi-Package Manager Support**: APT (Debian/Ubuntu), RPM (RedHat/CentOS/Fedora), APK (Alpine), and NPM (Node.js)
- **Multiple Output Formats**: JSON, CSV, and human-readable text
- **License Detection**: Automatically detects and maps package licenses
- **Copyright File Extraction**: Finds and extracts copyright files from the container filesystem
- **Container Runtime Agnostic**: Works with Docker and nerdctl (containerd)
- **Distroless Image Support**: Handles minimal/distroless images gracefully
- **Comprehensive Logging**: Detailed progress reporting with colored output

## Quick Start

```bash
# Basic usage with text output
./collect-packages-licenses.bash ubuntu:20.04

# Generate JSON report
./collect-packages-licenses.bash nginx:latest json

# Generate CSV report
./collect-packages-licenses.bash alpine:3.18 csv
```

## Installation

1. **Download the script:**
   ```bash
   curl -O https://raw.githubusercontent.com/YOUR_USERNAME/collect-packages-licenses/main/collect-packages-licenses.bash
   chmod +x collect-packages-licenses.bash
   ```

2. **Prerequisites:**
   - Docker or nerdctl (containerd) installed and accessible
   - Bash 4.0 or later
   - Standard Unix utilities (grep, sed, awk, find)

## Usage

```bash
./collect-packages-licenses.bash <image-name> [output-format]
```

### Arguments

- `image-name` (required): Docker image name or tag
- `output-format` (optional): Output format - `json`, `csv`, or `txt` (default: `txt`)

### Examples

```bash
# Scan Ubuntu image with default text output
./collect-packages-licenses.bash ubuntu:20.04

# Scan with JSON output
./collect-packages-licenses.bash nginx:latest json

# Scan private registry image with CSV output
./collect-packages-licenses.bash registry.example.com/myapp:v1.0 csv

# Scan Alpine Linux image
./collect-packages-licenses.bash alpine:3.18 txt
```

## Output Formats

### Text Format (Default)
Human-readable report with package information organized by package manager:

```
Package Scan Report for ubuntu:20.04
========================================

Operating System: Ubuntu 20.04.6 LTS
OS Family: Debian

APT/DPKG Packages:
==================
Package Name                   Version              License                        Manager
--------------------------------------------------------------------------------
adduser                        3.118ubuntu2         GPL-2.0                        APT
apt                            2.0.9                GPL-2.0                        APT
base-files                     11.1ubuntu2.2        GPL                            APT
...
```

### JSON Format
Structured data suitable for programmatic processing:

```json
{
  "image": "ubuntu:20.04",
  "scan_date": "2025-01-15T10:30:00Z",
  "operating_system": "Ubuntu 20.04.6 LTS",
  "os_family": "Debian",
  "packages": [
    {
      "name": "adduser",
      "version": "3.118ubuntu2",
      "license": "GPL-2.0",
      "package_manager": "APT"
    }
  ],
  "copyright_files": [
    {
      "path": "/usr/share/doc/adduser/copyright",
      "size_bytes": 1234,
      "extracted_file": "usr/share/doc/adduser/copyright"
    }
  ]
}
```

### CSV Format
Comma-separated values for spreadsheet analysis:

```csv
package_name,version,license,package_manager
"adduser","3.118ubuntu2","GPL-2.0","APT"
"apt","2.0.9","GPL-2.0","APT"
"base-files","11.1ubuntu2.2","GPL","APT"
```

## Supported Package Managers

| Package Manager | Operating Systems | License Detection |
|----------------|-------------------|-------------------|
| **APT/DPKG** | Debian, Ubuntu | ✅ Advanced (copyright files + fallback mappings) |
| **RPM** | RedHat, CentOS, Fedora, Rocky, AlmaLinux | ✅ Native RPM license field |
| **APK** | Alpine Linux | ✅ Native APK license info |
| **NPM** | Node.js applications | ✅ Package.json license field |

## License Detection Methods

1. **Native Package Manager Data**: Uses built-in license information when available
2. **Copyright File Analysis**: Parses Debian copyright files for license information
3. **Fallback Mappings**: Comprehensive database of known package licenses
4. **Pattern Matching**: Extracts license information from copyright text

## Output Files

The script generates timestamped files in the `./scan-results/` directory:

- `{image_name}_{timestamp}.txt` - Human-readable report
- `{image_name}_{timestamp}.csv` - CSV data
- `{image_name}_{timestamp}.json` - JSON data
- `{image_name}_{timestamp}_copyright_files/` - Extracted copyright files

## Special Image Types

### Distroless Images
The script automatically detects distroless images (minimal images with no package managers) and provides appropriate messaging:

```
DISTROLESS IMAGE DETECTED
=========================
This image appears to be a distroless image containing only:
- Application binary and runtime dependencies
- No package management system (APT, RPM, APK, etc.)
- No shell or standard Unix utilities
```

## Error Handling

- **Container Runtime Detection**: Automatically detects and uses Docker or nerdctl
- **Image Availability**: Pulls images if not available locally
- **Timeout Protection**: All container operations have timeouts to prevent hanging
- **Graceful Cleanup**: Removes temporary containers even on script interruption

## Troubleshooting

### Permission Issues
```bash
# If Docker requires sudo
sudo ./collect-packages-licenses.bash ubuntu:20.04

# Or add your user to the docker group
sudo usermod -aG docker $USER
```

### Container Runtime Not Found
```bash
# Install Docker
curl -fsSL https://get.docker.com -o get-docker.sh
sh get-docker.sh

# Or install nerdctl
# See: https://github.com/containerd/nerdctl
```

### Image Pull Failures
```bash
# For private registries, login first
docker login registry.example.com
./collect-packages-licenses.bash registry.example.com/private-image:tag
```

## Contributing

1. Fork the repository
2. Create a feature branch (`git checkout -b feature/amazing-feature`)
3. Commit your changes (`git commit -m 'Add amazing feature'`)
4. Push to the branch (`git push origin feature/amazing-feature`)
5. Open a Pull Request

### Adding Package Manager Support

To add support for a new package manager:

1. Add detection logic in the `collect_packages()` function
2. Implement package listing and license extraction
3. Add fallback license mappings for common packages
4. Update documentation

## License

This project is licensed under the MIT License - see the [LICENSE](LICENSE) file for details.

## Acknowledgments

- Inspired by various SBOM (Software Bill of Materials) tools
- Built for compliance and security auditing workflows
- Supports DevSecOps and supply chain security initiatives

## Related Projects

- [Syft](https://github.com/anchore/syft) - SBOM generation tool
- [Trivy](https://github.com/aquasecurity/trivy) - Vulnerability scanner
- [Grype](https://github.com/anchore/grype) - Vulnerability scanner for container images

## Changelog

See [CHANGELOG.md](CHANGELOG.md) for version history and changes.
