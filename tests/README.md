# Testing the Ansible PowerShell GitHub Executor Role

This directory contains test playbooks and examples for using the `ansible-powershell-github-executor` role.

## Prerequisites

1. **Install required collections**:
   ```bash
   ansible-galaxy install -r tests/requirements.yml
   ```

2. **Configure your inventory**:
   - Edit `inventory.ini` with your Windows server details
   - Update credentials as needed

3. **Ensure WinRM is configured** on target Windows hosts:
   ```powershell
   # On Windows hosts, run:
   Enable-PSRemoting -Force
   Set-Item -Path WSMan:\localhost\Service\Auth\Basic -Value $true
   Set-Item -Path WSMan:\localhost\Service\AllowUnencrypted -Value $true
   ```

## Running the Tests

### Run all tests
```bash
ansible-playbook -i tests/inventory.ini tests/test-playbook.yml
```

### Run specific test scenarios
```bash
# Simple execution test
ansible-playbook -i tests/inventory.ini tests/test-playbook.yml --tags simple_test

# Checksum verification test
ansible-playbook -i tests/inventory.ini tests/test-playbook.yml --tags checksum_test

# Custom paths test
ansible-playbook -i tests/inventory.ini tests/test-playbook.yml --tags custom_paths_test

# Script with arguments test
ansible-playbook -i tests/inventory.ini tests/test-playbook.yml --tags args_test

# Sequential execution test
ansible-playbook -i tests/inventory.ini tests/test-playbook.yml --tags sequence_test

# Check audit logs
ansible-playbook -i tests/inventory.ini tests/test-playbook.yml --tags audit_check

# Error handling tests
ansible-playbook -i tests/inventory.ini tests/test-playbook.yml --tags error_test
```

## Test Scenarios

1. **Simple Test**: Downloads and executes a PowerShell script without checksum verification
2. **Checksum Test**: Verifies script integrity before execution (will fail with placeholder checksum)
3. **Custom Paths Test**: Uses custom directories and keeps the script after execution
4. **Arguments Test**: Passes arguments to the PowerShell script
5. **Sequence Test**: Executes multiple scripts in sequence
6. **Audit Check**: Reads and displays audit logs
7. **Error Test**: Tests error handling with invalid inputs

## Using the Role in Your Playbooks

### Basic Usage
```yaml
- hosts: windows_servers
  roles:
    - role: ansible-powershell-github-executor
      vars:
        powershell_script_url: "https://raw.githubusercontent.com/user/repo/main/script.ps1"
```

### With Checksum Verification
```yaml
- hosts: windows_servers
  roles:
    - role: ansible-powershell-github-executor
      vars:
        powershell_script_url: "https://raw.githubusercontent.com/user/repo/main/script.ps1"
        powershell_verify_checksum: true
        powershell_checksum_algorithm: "sha256"
        powershell_checksum_value: "your-checksum-here"
```

### With Custom Configuration
```yaml
- hosts: windows_servers
  roles:
    - role: ansible-powershell-github-executor
      vars:
        powershell_script_url: "https://raw.githubusercontent.com/user/repo/main/script.ps1"
        powershell_script_args: "-Param1 value1 -Param2 value2"
        powershell_work_dir: "D:\\CustomPath"
        powershell_audit_log_dir: "D:\\Logs"
        powershell_execution_timeout: 600
        powershell_cleanup_script: false
```

## Verifying Audit Logs

Audit logs are created in JSON format at:
- Default: `C:\ProgramData\ansible-powershell-logs`
- Custom: As specified in `powershell_audit_log_dir`

Log files are named: `powershell_execution_<hostname>_<timestamp>.json`

## Troubleshooting

1. **WinRM Connection Issues**:
   - Verify WinRM service is running: `Get-Service WinRM`
   - Check firewall rules for port 5985/5986
   - Test connection: `Test-WSMan -ComputerName <hostname>`

2. **Execution Policy Errors**:
   - The role sets execution policy per-process
   - Default is "RemoteSigned"
   - Can be overridden with `powershell_execution_policy` variable

3. **Download Failures**:
   - Check network connectivity to GitHub
   - Verify the script URL is accessible
   - Review proxy settings if applicable

4. **Checksum Mismatches**:
   - Ensure you have the correct checksum for your script
   - Use the same algorithm that was used to generate the checksum
   - Common algorithms: sha256, sha1, md5
