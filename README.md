# WN10-AU-000500
STIG Remediation WN10-AU-000500
<#
.SYNOPSIS
    This PowerShell script ensures that the maximum size of the Windows Application event log is at least 32768 KB (32 MB).

.NOTES
    Author          : Zachary Williams
    LinkedIn        : linkedin.com/in/zacharywilliams05/
    GitHub          : github.com/zacharywilliams05
    Date Created    : 2025-08-18
    Last Modified   : 2024-08-18
    Version         : 1.0
    CVEs            : N/A
    Plugin IDs      : N/A
    STIG-ID         : WN10-AU-000500

.TESTED ON
    Date(s) Tested  : 
    Tested By       : 
    Systems Tested  : 
    PowerShell Ver. : 

.USAGE
    Put any usage instructions here.
    Example syntax:
    PS C:\> .\__remediation_template(STIG-ID-WN10-AU-000500).ps1 
#>

# Define the registry path and value
$registryPath = "HKLM:\SOFTWARE\Policies\Microsoft\Windows\EventLog\Application"
$valueName = "MaxSize"
$expectedValue = 32768  # 0x00008000 in decimal

# Check if the registry path exists
if (-not (Test-Path $registryPath)) {
    # Create the registry path
    New-Item -Path $registryPath -Force | Out-Null
    Write-Host "The registry path '$registryPath' did not exist and has been created."
    
    # Set the MaxSize value
    New-ItemProperty -Path $registryPath -Name $valueName -Value $expectedValue -PropertyType DWord -Force
    Write-Host "The registry value '$valueName' has been set to $expectedValue."
} else {
    # Get the registry value
    $registryValue = Get-ItemProperty -Path $registryPath -Name $valueName -ErrorAction SilentlyContinue

    if ($null -eq $registryValue) {
        # If the value does not exist, create it
        New-ItemProperty -Path $registryPath -Name $valueName -Value $expectedValue -PropertyType DWord -Force
        Write-Host "The registry value '$valueName' did not exist and has been created with a value of $expectedValue."
    } elseif ($registryValue.$valueName -lt $expectedValue) {
        # If the value is less than expected, update it
        Set-ItemProperty -Path $registryPath -Name $valueName -Value $expectedValue
        Write-Host "The registry value '$valueName' was less than $expectedValue and has been updated to $expectedValue."
    } else {
        Write-Host "The registry value '$valueName' is configured correctly with a value of $($registryValue.$valueName)."
    }
}
