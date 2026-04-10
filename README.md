# H3C WiFi DHCP Exporter

Windows tool for exporting DHCP lease data from an H3C switch over SSH and generating daily audit CSV files.

## Files

- `H3C-WiFi-DHCP-Exporter-release.exe`
- `plink.exe`
- `README.md`

`config.json` is not included in this minimal package. The program creates it automatically on first launch.

## What It Does

- Connects to an H3C switch by SSH
- Runs `display dhcp server ip-in-use`
- Filters records for the configured DHCP pool network
- Writes daily CSV output
- Merges repeated `IP + MAC` rows for audit purposes
- Can install a Windows scheduled task for periodic collection

## Output Columns

- `地址池`
- `VLAN`
- `IP地址`
- `MAC地址`
- `首次分配时间`
- `最后活跃时间`
- `存活分钟`
- `最近租约到期时间`
- `地址类型`

## Requirements

- Windows with PowerShell 5.1 or later
- Network access to the switch management IP
- SSH enabled on the switch
- The switch is acting as DHCP server for the target VLAN

## First Start

1. Put `H3C-WiFi-DHCP-Exporter-release.exe` and `plink.exe` in the same folder.
2. Run `H3C-WiFi-DHCP-Exporter-release.exe`.
3. The program will auto-create `config.json`.
4. Fill in your switch and DHCP pool settings.
5. Click `Test SSH`.
6. Click `Run Export`.

## Required Config Fields

- `username`
- `password`
- `switch_ip`
- `ssh_port`
- `vlan_id`
- `plink_path`
- `output_root`
- `pool_name`
- `pool_network`
- `pool_mask`
- `lease_hours`

## Scheduled Task

The GUI can install a Windows scheduled task for periodic export.

Current recommended schedule:

- Every `30` minutes

## Output Layout

Example:

```text
<output_root>\
  20260410\
    wifi_vlan_200_leases.csv
    raw\
      vlan_200_20260410_104000.txt
      vlan_200_dhcp_log_20260410_104000.txt
```

## Notes

- `Test SSH` runs synchronously for stability. The window may pause briefly.
- `Run Export` also runs synchronously for stability.
- If the output folder has permission issues, change `output_root` to a writable folder.

## GitHub Release Suggestion

Recommended repository contents:

- `H3C-WiFi-DHCP-Exporter-release.exe`
- `plink.exe`
- `README.md`
- `.gitignore`

Recommended release asset:

- `h3c-wifi-dhcp-export-package-mini.zip`
