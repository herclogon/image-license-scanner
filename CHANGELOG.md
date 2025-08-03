# Changelog

All notable changes to the Docker Image Package & License Scanner will be documented in this file.

The format is based on [Keep a Changelog](https://keepachangelog.com/en/1.0.0/),
and this project adheres to [Semantic Versioning](https://semver.org/spec/v2.0.0.html).

## [Unreleased]

### Added
- Initial release of Docker Image Package & License Scanner
- Support for APT/DPKG package manager (Debian/Ubuntu)
- Support for RPM package manager (RedHat/CentOS/Fedora/Rocky/AlmaLinux)
- Support for APK package manager (Alpine Linux)
- Support for NPM package manager (Node.js applications)
- Multiple output formats: JSON, CSV, and human-readable text
- Automatic license detection with fallback mappings
- Copyright file extraction from container filesystems
- Container runtime auto-detection (Docker and nerdctl)
- Distroless image detection and handling
- Comprehensive error handling and timeout protection
- Colored logging output with progress indicators
- Graceful cleanup of temporary containers
- OS detection and family classification
- Extensive license mapping database for common packages

### Features
- **Multi-format output**: Generate reports in JSON, CSV, or text format
- **License intelligence**: Advanced license detection using multiple methods
- **Copyright extraction**: Automatically finds and extracts copyright files
- **Robust scanning**: Handles various image types including distroless
- **Runtime agnostic**: Works with Docker or nerdctl (containerd)
- **Timeout protection**: All operations have timeouts to prevent hanging
- **Clean architecture**: Modular functions for easy maintenance and extension

### Security
- Safe temporary container handling with automatic cleanup
- Input validation for image names and output formats
- Timeout protection against hanging operations
- No persistent container modifications

### Documentation
- Comprehensive README with usage examples
- MIT license for open source distribution
- Detailed troubleshooting guide
- Contributing guidelines for community development

## [1.0.0] - 2025-01-15

### Added
- Initial stable release
- Core functionality for Docker image package scanning
- Support for major Linux package managers
- Multiple output format support
- License detection and mapping
- Copyright file extraction
- Comprehensive documentation

### Technical Details
- Bash script compatible with Bash 4.0+
- Container runtime detection and fallback
- Robust error handling and logging
- Modular architecture for extensibility
- Comprehensive test coverage for common scenarios

### Known Limitations
- Python package scanning temporarily disabled (performance optimization)
- Limited support for proprietary package managers
- Requires container runtime access (Docker or nerdctl)

### Future Roadmap
- Python package scanning re-enablement with performance improvements
- Support for additional package managers (Pacman, Zypper, etc.)
- Integration with SBOM standards (SPDX, CycloneDX)
- Performance optimizations for large images
- Web interface for easier usage
- CI/CD pipeline integrations
