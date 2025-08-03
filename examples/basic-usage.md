# Basic Usage Examples

This document provides practical examples of using the Docker Image Package & License Scanner.

## Table of Contents

- [Quick Start](#quick-start)
- [Common Use Cases](#common-use-cases)
- [Output Format Examples](#output-format-examples)
- [Advanced Scenarios](#advanced-scenarios)
- [Troubleshooting Examples](#troubleshooting-examples)

## Quick Start

### Scan a Standard Ubuntu Image

```bash
# Basic scan with default text output
./collect-packages-licenses.bash ubuntu:20.04

# Expected output location: ./scan-results/ubuntu_20_04_YYYYMMDD_HHMMSS.txt
```

### Scan with JSON Output

```bash
# Generate JSON report for programmatic processing
./collect-packages-licenses.bash ubuntu:20.04 json

# Expected output: ./scan-results/ubuntu_20_04_YYYYMMDD_HHMMSS.json
```

### Scan Alpine Linux Image

```bash
# Scan lightweight Alpine image
./collect-packages-licenses.bash alpine:3.18 csv

# Expected output: ./scan-results/alpine_3_18_YYYYMMDD_HHMMSS.csv
```

## Common Use Cases

### 1. Security Compliance Scanning

```bash
# Scan production application images for compliance
./collect-packages-licenses.bash myapp:production json
./collect-packages-licenses.bash nginx:1.21-alpine csv
./collect-packages-licenses.bash postgres:13 txt
```

### 2. License Audit for Legal Review

```bash
# Generate comprehensive license reports
./collect-packages-licenses.bash ubuntu:20.04 json
./collect-packages-licenses.bash node:18-alpine json
./collect-packages-licenses.bash python:3.9-slim json

# Combine results for legal review
jq -s '.' scan-results/*.json > combined-license-report.json
```

### 3. CI/CD Pipeline Integration

```bash
#!/bin/bash
# Example CI script

IMAGE_NAME="myapp:${BUILD_NUMBER}"
REPORT_FILE="scan-results/${IMAGE_NAME//[:\/]/_}_$(date +%Y%m%d_%H%M%S).json"

# Scan the built image
./collect-packages-licenses.bash "$IMAGE_NAME" json

# Check for GPL licenses (example policy)
if jq -r '.packages[].license' "$REPORT_FILE" | grep -q "GPL"; then
    echo "Warning: GPL licenses detected"
    # Add to compliance report or fail build based on policy
fi
```

### 4. Multi-Architecture Scanning

```bash
# Scan different architectures of the same image
./collect-packages-licenses.bash ubuntu:20.04 json
./collect-packages-licenses.bash --platform linux/arm64 ubuntu:20.04 json
./collect-packages-licenses.bash --platform linux/arm/v7 ubuntu:20.04 json
```

## Output Format Examples

### Text Format Output

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
bash                           5.0-6ubuntu1.2       GPL-3.0                        APT
bsdutils                       1:2.34-0.1ubuntu9.3  BSD                            APT
...
```

### JSON Format Output

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
    },
    {
      "name": "apt",
      "version": "2.0.9",
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

### CSV Format Output

```csv
package_name,version,license,package_manager
"adduser","3.118ubuntu2","GPL-2.0","APT"
"apt","2.0.9","GPL-2.0","APT"
"base-files","11.1ubuntu2.2","GPL","APT"
"bash","5.0-6ubuntu1.2","GPL-3.0","APT"
"bsdutils","1:2.34-0.1ubuntu9.3","BSD","APT"
```

## Advanced Scenarios

### 1. Scanning Private Registry Images

```bash
# Login to private registry first
docker login registry.company.com

# Scan private images
./collect-packages-licenses.bash registry.company.com/myapp:v1.2.3 json
./collect-packages-licenses.bash registry.company.com/backend:latest csv
```

### 2. Batch Scanning Multiple Images

```bash
#!/bin/bash
# Batch scan script

IMAGES=(
    "ubuntu:20.04"
    "ubuntu:22.04"
    "alpine:3.18"
    "debian:11"
    "nginx:alpine"
    "node:18-alpine"
    "python:3.9-slim"
)

for image in "${IMAGES[@]}"; do
    echo "Scanning $image..."
    ./collect-packages-licenses.bash "$image" json
    
    # Optional: Add delay to avoid overwhelming the system
    sleep 2
done

echo "Batch scan completed. Results in scan-results/"
```

### 3. Filtering and Analysis

```bash
# Scan image and analyze results
./collect-packages-licenses.bash ubuntu:20.04 json

# Extract only GPL licenses
jq -r '.packages[] | select(.license | contains("GPL")) | "\(.name): \(.license)"' \
    scan-results/ubuntu_20_04_*.json

# Count packages by license type
jq -r '.packages[].license' scan-results/ubuntu_20_04_*.json | sort | uniq -c

# Find packages without known licenses
jq -r '.packages[] | select(.license == "Unknown") | .name' \
    scan-results/ubuntu_20_04_*.json
```

### 4. Comparing Images

```bash
# Scan two different versions
./collect-packages-licenses.bash ubuntu:20.04 json
./collect-packages-licenses.bash ubuntu:22.04 json

# Compare package lists (requires jq)
diff \
    <(jq -r '.packages[].name' scan-results/ubuntu_20_04_*.json | sort) \
    <(jq -r '.packages[].name' scan-results/ubuntu_22_04_*.json | sort)
```

### 5. Distroless Image Handling

```bash
# Scan distroless images (will detect and handle appropriately)
./collect-packages-licenses.bash gcr.io/distroless/base-debian11 json
./collect-packages-licenses.bash gcr.io/distroless/java17-debian11 json

# Expected output will indicate distroless detection
```

## Troubleshooting Examples

### 1. Permission Issues

```bash
# If Docker requires sudo
sudo ./collect-packages-licenses.bash ubuntu:20.04 json

# Or add user to docker group (requires logout/login)
sudo usermod -aG docker $USER
```

### 2. Container Runtime Issues

```bash
# Check if Docker is running
docker version

# Check if nerdctl is available (alternative)
nerdctl version

# Test container creation
docker run --rm hello-world
```

### 3. Image Pull Issues

```bash
# For private registries, ensure you're logged in
docker login registry.company.com

# Check image exists
docker image inspect ubuntu:20.04

# Pull image manually if needed
docker pull ubuntu:20.04
```

### 4. Large Image Optimization

```bash
# For very large images, monitor progress
./collect-packages-licenses.bash large-image:latest json 2>&1 | tee scan.log

# Check if scan is progressing
tail -f scan.log
```

### 5. Output Validation

```bash
# Validate JSON output
jq . scan-results/ubuntu_20_04_*.json

# Check CSV format
head -5 scan-results/ubuntu_20_04_*.csv

# Verify file sizes are reasonable
ls -lh scan-results/
```

## Integration Examples

### 1. GitLab CI Integration

```yaml
# .gitlab-ci.yml
license-scan:
  stage: test
  image: docker:latest
  services:
    - docker:dind
  script:
    - apk add --no-cache bash jq
    - chmod +x collect-packages-licenses.bash
    - ./collect-packages-licenses.bash $CI_REGISTRY_IMAGE:$CI_COMMIT_SHA json
    - jq . scan-results/*.json  # Validate JSON
  artifacts:
    reports:
      license_scanning: scan-results/*.json
    expire_in: 1 week
```

### 2. GitHub Actions Integration

```yaml
# .github/workflows/license-scan.yml
name: License Scan
on: [push, pull_request]

jobs:
  scan:
    runs-on: ubuntu-latest
    steps:
    - uses: actions/checkout@v4
    - name: Scan Image
      run: |
        chmod +x collect-packages-licenses.bash
        ./collect-packages-licenses.bash ubuntu:20.04 json
    - name: Upload Results
      uses: actions/upload-artifact@v4
      with:
        name: license-scan-results
        path: scan-results/
```

### 3. Jenkins Pipeline Integration

```groovy
// Jenkinsfile
pipeline {
    agent any
    stages {
        stage('License Scan') {
            steps {
                script {
                    sh 'chmod +x collect-packages-licenses.bash'
                    sh './collect-packages-licenses.bash ${IMAGE_NAME} json'
                    
                    // Archive results
                    archiveArtifacts artifacts: 'scan-results/**/*', fingerprint: true
                    
                    // Parse and report
                    def scanResults = readJSON file: 'scan-results/*.json'
                    echo "Found ${scanResults.packages.size()} packages"
                }
            }
        }
    }
}
```

## Performance Tips

### 1. Optimize for Large Images

```bash
# Use timeout for very large images
timeout 600 ./collect-packages-licenses.bash large-image:latest json

# Monitor system resources
htop &
./collect-packages-licenses.bash large-image:latest json
```

### 2. Parallel Scanning

```bash
#!/bin/bash
# Parallel scan script (use with caution)

IMAGES=("ubuntu:20.04" "alpine:3.18" "debian:11")

for image in "${IMAGES[@]}"; do
    (
        echo "Starting scan of $image"
        ./collect-packages-licenses.bash "$image" json
        echo "Completed scan of $image"
    ) &
done

# Wait for all background jobs to complete
wait
echo "All scans completed"
```

### 3. Cleanup and Maintenance

```bash
# Clean up old scan results (keep last 10)
ls -t scan-results/*.txt | tail -n +11 | xargs rm -f
ls -t scan-results/*.json | tail -n +11 | xargs rm -f
ls -t scan-results/*.csv | tail -n +11 | xargs rm -f

# Clean up Docker images after scanning
docker image prune -f
```

This completes the basic usage examples. For more advanced use cases, see the main README.md and CONTRIBUTING.md files.
