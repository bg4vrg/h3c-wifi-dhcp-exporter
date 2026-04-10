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




# H3C WiFi DHCP Exporter

这个工具用于从 H3C 交换机定期抓取无线业务 VLAN 的 DHCP 租约信息，并按审计用途生成每日 CSV。

## 主要功能

- 通过 SSH 连接 H3C 交换机
- 执行 `display dhcp server ip-in-use`
- 过滤指定 DHCP 地址池网段
- 每天生成一个累计 CSV
- 按 `IP + MAC` 合并重复记录
- 保留原始命令输出到 `raw` 目录
- 可安装 Windows 计划任务定时执行

## 分发目录

- `H3C-WiFi-DHCP-Exporter-release.exe`
- `config.json`
- `plink.exe`
- `export-h3c-wifi-dhcp.ps1`
- `run-export.cmd`
- `README.md`

## 输出字段

- `地址池`
- `VLAN`
- `IP地址`
- `MAC地址`
- `首次分配时间`
- `最后活跃时间`
- `存活分钟`
- `最近租约到期时间`
- `地址类型`

## 环境要求

- Windows PowerShell 5.1 或更高
- 能访问交换机 SSH 管理地址
- 交换机已启用 SSH
- 交换机本身提供 DHCP Server

## 配置说明

编辑 `config.json`，至少填写这些字段：

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

当前程序目录默认自带 `plink.exe`，通常不需要再单独下载。

## 使用方式

### 1. 图形界面

双击：

```text
H3C-WiFi-DHCP-Exporter-release.exe
```

可在界面中：

- 保存配置
- 测试 SSH
- 立即采集
- 安装计划任务
- 删除计划任务

### 2. 命令行手工导出

```powershell
powershell.exe -NoProfile -ExecutionPolicy Bypass -File .\export-h3c-wifi-dhcp.ps1 -ConfigPath .\config.json
```

### 3. 计划任务

程序界面可直接安装计划任务。

## 输出目录结构

示例：

```text
<output_root>\
  20260410\
    wifi_vlan_200_leases.csv
    raw\
      vlan_200_20260410_104000.txt
      vlan_200_dhcp_log_20260410_104000.txt
```

## 时间说明

- 如果交换机本地日志缓冲区里能抓到 DHCP 分配事件，程序会优先使用日志时间。
- 如果抓不到，会按 `租约到期时间 - lease_hours` 推算 `首次分配时间`。
- 所以 `首次分配时间` 可能是精确时间，也可能是推算值，取决于设备日志是否可用。

## 已知行为

- `测试 SSH` 采用同步执行，点击后界面会短暂等待，这是为了优先保证稳定。
- `立即采集` 也采用同步执行，适合审计场景，不追求复杂异步交互。

## 建议

- 日常使用直接通过 GUI 操作
- 定时留存建议使用计划任务
- 如果导出目录权限受限，建议改到普通可写目录
- `.gitignore`

Recommended release asset:

- `h3c-wifi-dhcp-export-package-mini.zip`
