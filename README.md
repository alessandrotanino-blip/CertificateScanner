# SSL/TLS Certificate Scanner

A comprehensive and powerful PowerShell script to scan websites, IP addresses, or entire network ranges for SSL/TLS certificate details. 
It collects expiration dates, issuer information, and subject details while supporting HTTPS and LDAPS protocols, with optional OS detection and reverse DNS lookup capabilities.

## Features

- **Single site scanning**: Scan a specific URL or IP address.
- **Bulk file-based scanning**: Load a list of URLs or IPs from a file.
- **Network range scanning**: Scan entire subnets using CIDR notation.
- **Parallel processing**: Multi-threaded network scans for performance optimization.
- **Protocol support**: Detects both HTTPS and LDAPS certificates.
- **OS fingerprinting**: Detect the operating system of the hosts hosting the certificates.
- **Expiration filtering**: Monitor and filter certificates that are expiring soon or already expired.
- **CSV export**: Export results to a CSV file (supports appending).
- **Email reporting**: Send automated reports via email.
- **Reverse DNS resolution**: Map IPs back to hostnames using system or custom DNS servers.

## Prerequisites

- Windows PowerShell 5.1 or PowerShell Core 7+
- Appropriate network access/firewall rules to reach the target servers on the specified ports.

## Usage Examples

You can access the built-in help and comprehensive examples anytime by running:
```powershell
.\CertificateScanner.ps1 -Help
.\CertificateScanner.ps1 -Examples
```

### 1. Single Site Scanning

Scan a basic website:
```powershell
.\CertificateScanner.ps1 -SiteToScan www.example.com
```

Scan with custom port and older SSL protocol:
```powershell
.\CertificateScanner.ps1 -SiteToScan legacy.server.com -ProtocolVersion Ssl3
```

Comprehensive single site analysis with a specific internal DNS server:
```powershell
.\CertificateScanner.ps1 -SiteToScan www.example.com -IncludeReverseDNS -DnsServer 10.10.10.10 -GetOsType -SaveAsTo site_analysis.csv
```

### 2. Network Scanning

Basic network scan:
```powershell
.\CertificateScanner.ps1 -Networks "192.168.1.0/24"
```

Large network with performance tuning (increased threads and lower timeout):
```powershell
.\CertificateScanner.ps1 -Networks "10.0.0.0/16" -MaxThreads 20 -TimeoutSeconds 3
```

Scan multiple HTTPS ports:
```powershell
.\CertificateScanner.ps1 -Networks "192.168.1.0/24" -Port 8443 -AdditionalHTTPSPorts 443,8080,9443
```

Complete network analysis (including LDAPS, OS detection, and custom DNS):
```powershell
.\CertificateScanner.ps1 -Networks "192.168.1.0/24" -IncludeLDAPS -IncludeReverseDNS -DnsServer 192.168.1.1 -GetOsType -SaveAsTo full_scan.csv
```

### 3. File-Based Scanning

Scan targets from a text file:
```powershell
.\CertificateScanner.ps1 -LoadFromFile targets.txt
```

### 4. Expiration Monitoring

Find already expired certificates:
```powershell
.\CertificateScanner.ps1 -Networks "192.168.1.0/24" -ExpiresInDays 0
```

Find certificates expiring within 30 days and save to a CSV:
```powershell
.\CertificateScanner.ps1 -Networks "10.0.0.0/16" -ExpiresInDays 30 -SaveAsTo expiring_soon.csv
```

### 5. Email Reporting

Send an email report for certificates expiring within 30 days:
```powershell
.\CertificateScanner.ps1 -Networks "192.168.1.0/24" -ExpiresInDays 30 `
    -EmailSendTo admin@example.com `
    -EmailFrom scanner@example.com `
    -EmailSMTPServer mail.example.com `
    -EmailSubject "Weekly Certificate Expiration Report"
```

*Note: The script will prompt for credentials securely via `Get-Credential` when sending emails.*

## Parameters Reference

| Parameter | Type | Description |
|-----------|------|-------------|
| `-SiteToScan` | String | Target website or IP address (supports `hostname:port` notation). |
| `-Networks` | String | Comma-separated list of CIDR notation networks to scan. |
| `-LoadFromFile` | String | File path containing a list of URLs/IPs. |
| `-Port` | Int | Primary HTTPS port to scan (default: 443). |
| `-AdditionalHTTPSPorts` | Array | Additional HTTPS ports to scan during network scans. |
| `-ProtocolVersion` | String | SSL/TLS version to use (`Tls12`, `Tls11`, `Tls`, `Ssl3`, `Default`). |
| `-TimeoutSeconds` | Int | Connection timeout in seconds (default: 5). |
| `-MaxThreads` | Int | Number of parallel scanning threads (default: 10). |
| `-ExpiresInDays` | Int | Filter to show certificates expiring in X days (0 = expired only). |
| `-ExcludeExpired` | Switch | Exclude already expired certificates from the filtered results. |
| `-SaveAsTo` | String | Path to the CSV output file. Use `+filename.csv` to append to an existing file. |
| `-IncludeLDAPS` | Switch | Include LDAPS (port 636) in the scan. |
| `-LDAPSOnly` | Switch | Scan ONLY LDAPS certificates. |
| `-IncludeReverseDNS` | Switch | Perform reverse DNS lookups on discovered IPs. |
| `-DnsServer` | String | Optional: Specify a custom DNS server IP for reverse lookups. |
| `-GetOsType` | Switch | Attempt to detect the operating system of the certificate hosts. |
| `-Email*` | Various | Email configuration parameters (`-EmailSendTo`, `-EmailFrom`, `-EmailSMTPServer`, etc.). |

## Author & Acknowledgments

- **Original Author**: Faris Malaeb
- **Original Script**: [Scan Site List for Certificate Expiry Using PowerShell - Faris Malaeb](https://www.powershellcenter.com/2021/12/23/sslexpirationcheck/)
- **Improvements & Maintenance**: Alessandro Tanino

