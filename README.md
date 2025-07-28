# Ansible Role: PowerShell GitHub Executor

An Ansible role that downloads PowerShell scripts from GitHub, optionally verifies checksums, executes them with specified arguments, and provides audit logging.

## Requirements

- Windows target hosts with PowerShell 5.0 or higher
- Ansible 2.9 or higher
- WinRM properly configured on target hosts
- Ansible Windows collection (`ansible.windows`)

## Installation

### Install required collections
```bash
ansible-galaxy collection install ansible.windows
ansible-galaxy collection install community.windows
```

### Install the role
```bash
# From GitHub
ansible-galaxy install git+https://github.com/keepithuman/ansible-powershell-github-executor.git

# Or clone the repository
git clone https://github.com/keepithuman/ansible-powershell-github-executor.git
```

## Role Variables

### Required Variables

```yaml
# URL of the PowerShell script on GitHub
powershell_script_url: "https://raw.githubusercontent.com/username/repo/main/script.ps1"
```

### Optional Variables

```yaml
# Working directory for script download and execution
powershell_work_dir: "C:\\temp\\ansible-powershell"

# Script filename (defaults to extracting from URL)
powershell_script_name: "script.ps1"

# Enable checksum verification
powershell_verify_checksum: true

# Checksum algorithm (sha256, sha1, md5)
powershell_checksum_algorithm: "sha256"

# Expected checksum value
powershell_checksum_value: ""

# Script execution arguments
powershell_script_args: ""

# Script execution timeout (seconds)
powershell_execution_timeout: 300

# Remove script after execution
powershell_cleanup_script: true

# Audit log directory
powershell_audit_log_dir: "C:\\ProgramData\\ansible-powershell-logs"

# Enable audit logging
powershell_enable_audit: true

# PowerShell execution policy
powershell_execution_policy: "RemoteSigned"

# Download settings
powershell_download_timeout: 60
powershell_download_retries: 3
powershell_download_delay: 5
```

## Dependencies

None.

## Example Playbook

### Basic Usage
```yaml
---
- hosts: windows_servers
  roles:
    - role: ansible-powershell-github-executor
      vars:
        powershell_script_url: "https://raw.githubusercontent.com/myorg/scripts/main/configure-app.ps1"
        powershell_script_args: "-Environment dev -Verbose"
        powershell_enable_audit: true
```

### Production Usage with Checksum Verification
```yaml
---
- hosts: windows_servers
  roles:
    - role: ansible-powershell-github-executor
      vars:
        powershell_script_url: "https://raw.githubusercontent.com/myorg/scripts/main/configure-app.ps1"
        powershell_verify_checksum: true
        powershell_checksum_algorithm: "sha256"
        powershell_checksum_value: "a665a45920422f9d417e4867efdc4fb8a04a1f3fff1fa07e998e86f7f7a27ae3"
        powershell_script_args: "-Environment prod -Verbose"
        powershell_execution_timeout: 600
        powershell_enable_audit: true
```

## Testing

Comprehensive test playbooks are provided in the `tests/` directory:

```bash
# Run all tests
ansible-playbook -i tests/inventory.ini tests/test-playbook.yml

# Run specific test scenarios
ansible-playbook -i tests/inventory.ini tests/test-playbook.yml --tags simple_test
```

See [tests/README.md](tests/README.md) for detailed testing instructions.

## Audit Logging

When audit logging is enabled, the role creates detailed logs including:
- Script download information
- Checksum verification results
- Script execution output
- Execution timestamps
- Error information (if any)

Logs are stored in JSON format for easy parsing and analysis.

### Example Audit Log
```json
{
  "timestamp_start": "2025-01-28T10:30:00Z",
  "hostname": "WIN-SERVER01",
  "script_url": "https://raw.githubusercontent.com/...",
  "script_name": "configure-app.ps1",
  "download": {
    "status": "success",
    "file_size": 2048,
    "download_time": 1.23
  },
  "checksum_verification": {
    "algorithm": "sha256",
    "expected": "a665a45920422f9d417e4867efdc4fb8a04a1f3fff1fa07e998e86f7f7a27ae3",
    "actual": "a665a45920422f9d417e4867efdc4fb8a04a1f3fff1fa07e998e86f7f7a27ae3",
    "status": "verified"
  },
  "execution": {
    "status": "success",
    "return_code": 0,
    "execution_time": "00:00:05.123",
    "script_args": "-Environment dev -Verbose"
  },
  "timestamp_end": "2025-01-28T10:30:06Z"
}
```

## Security Considerations

1. **Always use HTTPS URLs** for script downloads
2. **Enable checksum verification** for production environments
3. **Review PowerShell execution policy** settings
4. **Ensure proper WinRM security** configuration
5. **Restrict access** to audit log directories
6. **Use encrypted connections** (HTTPS/5986) for WinRM in production
7. **Rotate credentials** regularly
8. **Monitor audit logs** for suspicious activity

## Troubleshooting

### Common Issues

1. **WinRM Connection Failed**
   ```bash
   # Test WinRM connectivity
   ansible -i inventory.ini windows_servers -m win_ping
   ```

2. **Script Download Failed**
   - Check network connectivity to GitHub
   - Verify proxy settings if applicable
   - Check the URL is accessible

3. **Checksum Verification Failed**
   - Ensure the checksum value matches the algorithm used
   - Verify the script hasn't been modified

4. **Execution Policy Error**
   - The role sets execution policy per-process
   - Override with `powershell_execution_policy` if needed

## Contributing

1. Fork the repository
2. Create a feature branch
3. Make your changes
4. Add tests if applicable
5. Submit a pull request

## License

MIT

## Author Information

This role was created for production use with enterprise Windows environments.

## Support

For issues, questions, or contributions, please use the [GitHub issue tracker](https://github.com/keepithuman/ansible-powershell-github-executor/issues).
