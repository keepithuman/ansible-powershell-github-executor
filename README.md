# Ansible Role: PowerShell GitHub Executor

An Ansible role that downloads PowerShell scripts from GitHub, optionally verifies checksums, executes them with specified arguments, and provides audit logging.

## Requirements

- Windows target hosts with PowerShell 5.0 or higher
- Ansible 2.9 or higher
- WinRM properly configured on target hosts

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
```

## Dependencies

None.

## Example Playbook

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
        powershell_script_args: "-Environment dev -Verbose"
        powershell_enable_audit: true
```

## Audit Logging

When audit logging is enabled, the role creates detailed logs including:
- Script download information
- Checksum verification results
- Script execution output
- Execution timestamps
- Error information (if any)

Logs are stored in JSON format for easy parsing and analysis.

## Security Considerations

1. Always use HTTPS URLs for script downloads
2. Enable checksum verification for production environments
3. Review PowerShell execution policy settings
4. Ensure proper WinRM security configuration
5. Restrict access to audit log directories

## License

MIT

## Author Information

This role was created for production use with enterprise Windows environments.
