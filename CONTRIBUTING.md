# Contributing to Docker Image Package & License Scanner

Thank you for your interest in contributing to the Docker Image Package & License Scanner! This document provides guidelines and information for contributors.

## Table of Contents

- [Code of Conduct](#code-of-conduct)
- [Getting Started](#getting-started)
- [Development Setup](#development-setup)
- [How to Contribute](#how-to-contribute)
- [Coding Standards](#coding-standards)
- [Testing](#testing)
- [Submitting Changes](#submitting-changes)
- [Adding Package Manager Support](#adding-package-manager-support)
- [Reporting Issues](#reporting-issues)

## Code of Conduct

This project adheres to a code of conduct that we expect all contributors to follow. Please be respectful and constructive in all interactions.

### Our Standards

- Use welcoming and inclusive language
- Be respectful of differing viewpoints and experiences
- Gracefully accept constructive criticism
- Focus on what is best for the community
- Show empathy towards other community members

## Getting Started

1. **Fork the repository** on GitHub
2. **Clone your fork** locally:
   ```bash
   git clone https://github.com/YOUR_USERNAME/collect-packages-licenses.git
   cd collect-packages-licenses
   ```
3. **Create a branch** for your feature or fix:
   ```bash
   git checkout -b feature/your-feature-name
   ```

## Development Setup

### Prerequisites

- Bash 4.0 or later
- Docker or nerdctl (containerd)
- Standard Unix utilities (grep, sed, awk, find, jq)
- Git for version control

### Testing Environment

```bash
# Test the script with various images
./collect-packages-licenses.bash ubuntu:20.04 json
./collect-packages-licenses.bash alpine:3.18 csv
./collect-packages-licenses.bash nginx:latest txt

# Test with distroless images
./collect-packages-licenses.bash gcr.io/distroless/base-debian11
```

## How to Contribute

### Types of Contributions

1. **Bug Reports**: Report issues with detailed reproduction steps
2. **Feature Requests**: Suggest new functionality or improvements
3. **Code Contributions**: Submit bug fixes or new features
4. **Documentation**: Improve README, comments, or examples
5. **Package Manager Support**: Add support for new package managers
6. **License Mappings**: Expand the license detection database

### Priority Areas

- **Package Manager Support**: Adding support for new package managers
- **License Detection**: Improving license detection accuracy
- **Performance**: Optimizing scan speed for large images
- **Error Handling**: Improving robustness and error messages
- **Documentation**: Examples, tutorials, and use cases

## Coding Standards

### Bash Style Guide

1. **Use `set -euo pipefail`** at the beginning of scripts
2. **Quote variables** to prevent word splitting: `"$variable"`
3. **Use meaningful function names** with descriptive comments
4. **Handle errors gracefully** with appropriate error messages
5. **Use consistent indentation** (4 spaces)
6. **Add timeouts** to external commands to prevent hanging

### Example Function Structure

```bash
# Function to detect package manager
# Arguments: None
# Returns: Sets global variable PACKAGE_MANAGER
detect_package_manager() {
    log "Detecting package manager..."
    
    if timeout 10 ${CONTAINER_CMD} exec "${TEMP_CONTAINER}" which dpkg &> /dev/null; then
        PACKAGE_MANAGER="apt"
        log "Detected APT/DPKG package manager"
    elif timeout 10 ${CONTAINER_CMD} exec "${TEMP_CONTAINER}" which rpm &> /dev/null; then
        PACKAGE_MANAGER="rpm"
        log "Detected RPM package manager"
    else
        PACKAGE_MANAGER="unknown"
        warning "No supported package manager detected"
    fi
}
```

### Code Organization

- **Functions**: Group related functionality into well-named functions
- **Global Variables**: Use UPPERCASE for global variables
- **Local Variables**: Use lowercase for local variables
- **Constants**: Define at the top of the script
- **Error Handling**: Use consistent error handling patterns

## Testing

### Manual Testing

Test your changes with various image types:

```bash
# Test with different Linux distributions
./collect-packages-licenses.bash ubuntu:20.04
./collect-packages-licenses.bash centos:8
./collect-packages-licenses.bash alpine:3.18
./collect-packages-licenses.bash debian:11

# Test with application images
./collect-packages-licenses.bash nginx:latest
./collect-packages-licenses.bash node:18-alpine
./collect-packages-licenses.bash python:3.9-slim

# Test with distroless images
./collect-packages-licenses.bash gcr.io/distroless/base-debian11
./collect-packages-licenses.bash gcr.io/distroless/java17-debian11

# Test different output formats
./collect-packages-licenses.bash ubuntu:20.04 json
./collect-packages-licenses.bash ubuntu:20.04 csv
./collect-packages-licenses.bash ubuntu:20.04 txt
```

### Edge Cases to Test

- Images with no packages (distroless)
- Images with mixed package managers
- Very large images (performance)
- Images with special characters in names
- Private registry images
- Images that fail to start containers

### Validation Checklist

- [ ] Script runs without errors
- [ ] All output formats work correctly
- [ ] License detection is accurate
- [ ] Copyright files are extracted properly
- [ ] Temporary containers are cleaned up
- [ ] Error messages are helpful
- [ ] Performance is acceptable

## Submitting Changes

### Pull Request Process

1. **Update documentation** if you're changing functionality
2. **Add tests** for new features
3. **Update CHANGELOG.md** with your changes
4. **Ensure your code follows** the style guidelines
5. **Write a clear commit message** describing your changes

### Commit Message Format

```
type(scope): brief description

Detailed explanation of the changes made, why they were necessary,
and any potential impacts.

Fixes #issue_number
```

Types:
- `feat`: New feature
- `fix`: Bug fix
- `docs`: Documentation changes
- `style`: Code style changes
- `refactor`: Code refactoring
- `test`: Adding or updating tests
- `chore`: Maintenance tasks

### Pull Request Template

```markdown
## Description
Brief description of the changes

## Type of Change
- [ ] Bug fix
- [ ] New feature
- [ ] Documentation update
- [ ] Performance improvement
- [ ] Code refactoring

## Testing
- [ ] Tested with Ubuntu images
- [ ] Tested with Alpine images
- [ ] Tested with CentOS/RHEL images
- [ ] Tested with distroless images
- [ ] Tested all output formats

## Checklist
- [ ] Code follows style guidelines
- [ ] Self-review completed
- [ ] Documentation updated
- [ ] CHANGELOG.md updated
```

## Adding Package Manager Support

### Step-by-Step Guide

1. **Research the package manager**:
   - Command to list packages
   - Command to get package information
   - License information availability
   - Common package naming conventions

2. **Add detection logic**:
   ```bash
   # Add to detect_package_manager() function
   elif timeout 10 ${CONTAINER_CMD} exec "${TEMP_CONTAINER}" which your_pm &> /dev/null; then
       PACKAGE_MANAGER="your_pm"
       log "Detected YOUR_PM package manager"
   ```

3. **Implement package collection**:
   ```bash
   # Add new section in collect_packages() function
   if timeout 10 ${CONTAINER_CMD} exec "${TEMP_CONTAINER}" which your_pm &> /dev/null; then
       echo "" >> "${txt_file}"
       echo "YOUR_PM Packages:" >> "${txt_file}"
       echo "=================" >> "${txt_file}"
       
       # Implementation here
   fi
   ```

4. **Add license mappings**:
   - Research common packages and their licenses
   - Add fallback mappings for packages without license info
   - Test with real-world images

5. **Update documentation**:
   - Add to supported package managers table
   - Update examples if needed
   - Add to testing checklist

### Package Manager Examples

#### APK (Alpine)
```bash
${CONTAINER_CMD} exec "${TEMP_CONTAINER}" apk list -I 2>/dev/null | while read -r line; do
    if [[ "$line" =~ ^([^[:space:]]+)-([0-9][^[:space:]]*)[[:space:]] ]]; then
        pkg_name="${BASH_REMATCH[1]}"
        pkg_version="${BASH_REMATCH[2]}"
        license=$(${CONTAINER_CMD} exec "${TEMP_CONTAINER}" apk info -a "$pkg_name" 2>/dev/null | grep -A1 "license:" | tail -1 | xargs || echo "Unknown")
        add_package "$pkg_name" "$pkg_version" "$license" "APK"
    fi
done
```

## Reporting Issues

### Bug Reports

When reporting bugs, please include:

1. **Environment information**:
   - Operating system
   - Container runtime (Docker/nerdctl version)
   - Bash version
   - Script version

2. **Reproduction steps**:
   - Exact command used
   - Image name and tag
   - Expected vs actual behavior

3. **Logs and output**:
   - Complete error messages
   - Relevant log output
   - Generated files (if applicable)

### Feature Requests

For feature requests, please provide:

1. **Use case description**: Why is this feature needed?
2. **Proposed solution**: How should it work?
3. **Alternatives considered**: Other approaches you've thought about
4. **Additional context**: Any other relevant information

### Issue Template

```markdown
## Bug Report / Feature Request

### Environment
- OS: [e.g., Ubuntu 20.04]
- Container Runtime: [e.g., Docker 20.10.8]
- Bash Version: [e.g., 5.0.17]

### Description
[Clear description of the issue or feature request]

### Steps to Reproduce (for bugs)
1. Run command: `./collect-packages-licenses.bash image:tag`
2. Observe error: [error message]

### Expected Behavior
[What should happen]

### Actual Behavior
[What actually happens]

### Additional Context
[Any other relevant information]
```

## Development Workflow

### Branching Strategy

- `main`: Stable release branch
- `develop`: Development branch for new features
- `feature/*`: Feature development branches
- `fix/*`: Bug fix branches
- `docs/*`: Documentation update branches

### Release Process

1. Features merged to `develop`
2. Testing and stabilization on `develop`
3. Release candidate created
4. Final testing and bug fixes
5. Merge to `main` and tag release
6. Update package managers and distribution channels

## Getting Help

- **GitHub Issues**: For bugs and feature requests
- **GitHub Discussions**: For questions and general discussion
- **Documentation**: Check README.md and inline comments
- **Examples**: Look at existing code patterns

## Recognition

Contributors will be recognized in:
- CHANGELOG.md for their contributions
- GitHub contributors list
- Special recognition for significant contributions

Thank you for contributing to the Docker Image Package & License Scanner!
