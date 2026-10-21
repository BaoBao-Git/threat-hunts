# Harborlight Dental & Insurance: Akira-style ransomware hunt

**SancLogic Cyber Range, Hunt 22 · case HL-INC-2026-0204 · Microsoft Sentinel / KQL**

Ransom notes appeared in every shared folder at a small dental and insurance practice that had full Sysmon and Windows Security logging and nobody watching it. I rebuilt the whole intrusion from raw telemetry: phishing at 01:57 UTC, encryption alert at 05:14, and attacker callbacks still running at 18:47.

## Contents

| Path | What it is |
|---|---|
| [`index.html`](index.html) | Interactive writeup (search, collapsible questions, screenshots) |
| [`report/Harborlight_Threat_Hunt_Report.pdf`](report/Harborlight_Threat_Hunt_Report.pdf) | Printable report, 49 pages |
| [`queries/harborlight.kql`](queries/harborlight.kql) | Every KQL query, labeled by question |
| [`detections/`](detections/) | 10 detection rules derived from what the attacker did |
| [`evidence/screenshots/`](evidence/screenshots/) | My query results, named by question (Q1.2-1.png, Q9.3-1.png, ...) |

## What happened

1. **Initial access (01:57):** Emily Grant's workstation downloaded a spoofed invoice `.7z` containing an ISO, which bypasses Mark-of-the-Web.
2. **Execution (02:25):** `rundll32 E:\review.dll,StartW` ran from the mounted ISO and injected into `notepad.exe`.
3. **Privilege escalation (02:42):** An `ms-settings` registry hijack made `fodhelper.exe` run a fake `C:\Users\Public\taskhostw.exe` at High integrity.
4. **Credential access (02:49):** The injected `spoolsv.exe` read LSASS with `0x1fffff`.
5. **Lateral movement (03:34, 04:28):** One stolen domain credential reached the file server and then the domain controller over SMB admin shares on a flat network.
6. **Exfiltration (03:57–04:03):** rclone renamed `MsMpEng.exe` copied `C:\Shares` to a Mega account.
7. **Impact prep (04:04):** Shadow copies, boot recovery and the backup catalog were destroyed, twice.
8. **NTDS dump and implants (04:33–04:54):** `ntdsutil` IFM copy, then three C2 implants beaconing to `cdn.cloud-endpoint.net` behind Cloudflare.
9. **Log wipes (05:13–05:20):** Two waves, up to 16 channels per host.
10. **Domain persistence and encryption (05:14–05:23):** A backdoor `svc_sql` account was made a Domain Admin; `akira_readme.txt` was dropped 128 times and 27 distinct `.akira` files appear in the telemetry.

## Key numbers

| | |
|---|---|
| Hosts compromised | 3 (BACKOFFICE-PC1, HL-FS01, ADDC01) |
| C2 connections | 14,218 (70% port 443, 30% port 80) to two Cloudflare IPs |
| Access seen in telemetry | 02:25:08 to 18:47:29, about 16 h 22 min |
| Ransom notes | 128 (HL-FS01 77, ADDC01 38, BACKOFFICE-PC1 13) |

## MITRE ATT&CK coverage

T1566.001, T1553.005, T1204.002, T1218.011, T1055.001, T1548.002, T1112, T1036.005, T1055, T1003.001, T1003.003, T1021.002, T1078.002, T1047, T1070.001, T1567.002, T1039, T1490, T1053.005, T1105, T1071.001, T1090.004, T1136.002, T1098, T1087.002, T1486.

## Corrections I kept

The writeup shows where I was wrong or only partly right, for example: I answered Q8.4 "simultaneous" when my own query showed the wipes were staggered; I submitted hop-two values for the hop-one question (Q6.2); and my first anti-recovery query (Q10.2) counted every process-creation row, so I corrected it. The full list is in section 8 of the report.

## Limitations

- The `detections/` rules were written from this hunt's findings and are **not validated against live data**. Test and tune them before using them for alerting.
- Some queries were reconstructed from screenshots and my notes; the screenshots are the exact record.
- Group, member and subject names inside the 4728/4732 events were not extracted, and the encryptor binary (possibly `updater.exe`) was not identified.
- 27 `.akira` files is what the telemetry shows, not a complete count.

## AI use

Claude (Anthropic) helped with KQL query formulation, CSV triage and wording. I read the telemetry and made the noise-versus-attack calls myself, and I corrected Claude where the data disagreed.

## Scope and disclaimer

This is a training-range exercise. All hosts, users and credentials are fictional. Platform briefs, alert text and screenshots from the range are not reproduced here.
