## Description

Brief description of the changes made in this pull request.

## Type of Change

Please delete options that are not relevant:

- [ ] Bug fix (non-breaking change which fixes an issue)
- [ ] New feature (non-breaking change which adds functionality)
- [ ] Breaking change (fix or feature that would cause existing functionality to not work as expected)
- [ ] Documentation update
- [ ] Performance improvement
- [ ] Code refactoring
- [ ] Test improvements

## Related Issues

Fixes #(issue number)
Closes #(issue number)
Related to #(issue number)

## Changes Made

- [ ] Added support for [package manager/feature]
- [ ] Fixed issue with [specific problem]
- [ ] Improved [specific functionality]
- [ ] Updated documentation for [specific area]

## Testing

Please describe the tests that you ran to verify your changes:

### Manual Testing
- [ ] Tested with Ubuntu images
- [ ] Tested with Alpine images
- [ ] Tested with CentOS/RHEL images
- [ ] Tested with Debian images
- [ ] Tested with distroless images
- [ ] Tested with application images (nginx, node, etc.)

### Output Format Testing
- [ ] Tested JSON output format
- [ ] Tested CSV output format
- [ ] Tested text output format

### Edge Case Testing
- [ ] Tested with invalid image names
- [ ] Tested with invalid output formats
- [ ] Tested help functionality
- [ ] Tested error handling
- [ ] Tested container cleanup

### Performance Testing
- [ ] Tested with large images
- [ ] Verified reasonable execution time
- [ ] Checked memory usage

## Test Configuration

**Environment:**
- OS: [e.g., Ubuntu 20.04]
- Container Runtime: [e.g., Docker 20.10.8]
- Bash Version: [e.g., 5.0.17]

**Test Commands:**
```bash
# List the exact commands you used for testing
./collect-packages-licenses.bash ubuntu:20.04 json
./collect-packages-licenses.bash alpine:3.18 csv
# etc.
```

## Checklist

- [ ] My code follows the style guidelines of this project
- [ ] I have performed a self-review of my own code
- [ ] I have commented my code, particularly in hard-to-understand areas
- [ ] I have made corresponding changes to the documentation
- [ ] My changes generate no new warnings
- [ ] I have added tests that prove my fix is effective or that my feature works
- [ ] New and existing unit tests pass locally with my changes
- [ ] Any dependent changes have been merged and published

## Documentation Updates

- [ ] Updated README.md
- [ ] Updated CHANGELOG.md
- [ ] Updated CONTRIBUTING.md (if adding new package manager support)
- [ ] Added inline code comments
- [ ] Updated help text/usage information

## Breaking Changes

If this is a breaking change, please describe:
- What functionality is being changed or removed
- How users should update their usage
- Migration guide (if applicable)

## Screenshots (if applicable)

Add screenshots to help explain your changes, especially for:
- New output formats
- Error message improvements
- Performance improvements

## Additional Notes

Add any additional notes, concerns, or questions for reviewers here.

## Reviewer Checklist

For maintainers reviewing this PR:

- [ ] Code quality and style
- [ ] Test coverage
- [ ] Documentation completeness
- [ ] Performance impact
- [ ] Security considerations
- [ ] Backward compatibility
- [ ] CI/CD pipeline passes
