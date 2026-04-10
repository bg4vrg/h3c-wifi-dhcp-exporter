# H3C WiFi DHCP Exporter

<p>
  <a href="#简体中文">简体中文</a> |
  <a href="#english">English</a>
</p>

<details open>
<summary><strong>简体中文</strong></summary>

## 简介

这是一个用于 H3C 交换机 DHCP 审计留存的小工具。

程序通过 SSH 登录交换机，抓取 DHCP 租约信息，按天生成 CSV 文件，并把同一组 `IP + MAC` 做聚合，方便后续审计、追溯和留存。

适合的场景：

- 需要长期保留无线终端 IP / MAC 对应关系
- 需要定期导出 DHCP 数据
- 需要用 Windows 计划任务自动执行

## 包含文件

- `H3C-WiFi-DHCP-Exporter-release.exe`
- `plink.exe`
- `README.md`

说明：

- 极简包默认不附带 `config.json`
- 程序首次启动时会自动生成默认配置文件

## 功能说明

- 通过 SSH 连接 H3C 交换机
- 执行 `display dhcp server ip-in-use`
- 按指定 DHCP 地址池网段过滤记录
- 生成每日 CSV 文件
- 按 `IP + MAC` 合并重复记录
- 保存原始命令输出到 `raw` 目录
- 支持安装 Windows 计划任务

## 运行环境

- Windows 10 / Windows Server
- PowerShell 5.1 或更高版本
- 交换机已启用 SSH
- 本机可以访问交换机管理地址
- 交换机本身提供 DHCP Server

## 首次使用

1. 确保 `H3C-WiFi-DHCP-Exporter-release.exe` 和 `plink.exe` 在同一目录。
2. 双击运行 `H3C-WiFi-DHCP-Exporter-release.exe`。
3. 程序会自动生成 `config.json`。
4. 填写交换机参数和 DHCP 地址池参数。
5. 点击 `Test SSH`。
6. 点击 `Run Export`。
7. 确认 CSV 正常生成后，再安装计划任务。

## 配置项说明

常用配置项：

- `username`：SSH 用户名
- `password`：SSH 密码
- `switch_ip`：交换机管理 IP
- `ssh_port`：SSH 端口
- `vlan_id`：无线业务 VLAN
- `plink_path`：`plink.exe` 路径
- `output_root`：输出目录
- `host_key`：SSH 主机指纹
- `pool_name`：DHCP 地址池名称
- `pool_network`：地址池网段
- `pool_mask`：子网掩码
- `lease_hours`：租期小时数
- `log_tail_lines`：读取 DHCP 日志尾部行数
- `schedule_minutes`：计划任务执行间隔
- `task_name`：计划任务名称

通常必须正确填写的字段：

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

## 输出结果

程序会在 `output_root` 下按日期生成目录，例如：

```text
<output_root>\
  20260410\
    wifi_vlan_200_leases.csv
    raw\
      vlan_200_20260410_104000.txt
      vlan_200_dhcp_log_20260410_104000.txt
```

CSV 字段：

- `地址池`
- `VLAN`
- `IP地址`
- `MAC地址`
- `首次分配时间`
- `最后活跃时间`
- `存活分钟`
- `最近租约到期时间`
- `地址类型`

字段含义：

- `首次分配时间`：该 `IP + MAC` 首次出现的时间
- `最后活跃时间`：该 `IP + MAC` 最近一次被采集到的时间
- `存活分钟`：从首次分配到最后活跃的分钟数
- `最近租约到期时间`：最近一次采集到的租约到期时间

## 时间说明

- 如果交换机日志中能识别到 DHCP 分配事件，程序优先使用日志时间。
- 如果识别不到，程序会按 `租约到期时间 - lease_hours` 推算 `首次分配时间`。
- 因此 `首次分配时间` 可能是精确时间，也可能是推算值。

## 图形界面说明

当前版本为了稳定性，`Test SSH` 和 `Run Export` 都采用同步执行。

这意味着：

- 点击后界面会短暂停顿
- 按钮会暂时禁用
- 执行完成后恢复

这样做是为了避免之前异步实现带来的崩溃、锁死和状态异常。

## 计划任务

程序界面可以直接安装 Windows 计划任务。

推荐周期：

- 每 `30` 分钟执行一次

适合做审计留存和长期追溯。

## 常见问题

### 为什么发布包里没有 `config.json`？

因为极简发布包面向分发，程序首次启动时会自动生成默认模板。

### 为什么 `Test SSH` 会短暂卡一下？

这是当前版本为了优先保证稳定性而做的取舍。

### 导出失败时优先检查什么？

- SSH 用户名和密码是否正确
- SSH 端口是否正确
- 交换机管理 IP 是否可达
- `plink.exe` 是否与 EXE 在同一目录
- `output_root` 是否可写
- 地址池名称、网段和掩码是否填写正确

### 这个工具适合做什么？

这个工具更适合 DHCP 审计、历史追溯和数据留存，不是实时在线终端监控工具。

</details>

<details>
<summary><strong>English</strong></summary>

## Overview

This tool is designed for DHCP audit retention on H3C switches.

It connects to the switch over SSH, collects DHCP lease data, generates daily CSV files, and aggregates repeated `IP + MAC` rows for traceability and audit purposes.

Typical use cases:

- keeping long-term historical IP / MAC mappings
- exporting DHCP lease data on a schedule
- running automatic collection through Windows Task Scheduler

## Package Contents

- `H3C-WiFi-DHCP-Exporter-release.exe`
- `plink.exe`
- `README.md`

Notes:

- the minimal package does not include `config.json`
- the program creates a default `config.json` on first launch

## Features

- connect to an H3C switch over SSH
- run `display dhcp server ip-in-use`
- filter records by DHCP pool network
- generate daily CSV files
- merge repeated `IP + MAC` rows
- preserve raw command output in the `raw` folder
- install a Windows scheduled task

## Requirements

- Windows 10 / Windows Server
- PowerShell 5.1 or later
- SSH enabled on the switch
- network access to the switch management IP
- the switch acts as DHCP server for the target VLAN

## First-Time Setup

1. Keep `H3C-WiFi-DHCP-Exporter-release.exe` and `plink.exe` in the same folder.
2. Launch `H3C-WiFi-DHCP-Exporter-release.exe`.
3. Let the program create `config.json`.
4. Fill in the switch and DHCP pool settings.
5. Click `Test SSH`.
6. Click `Run Export`.
7. After a successful export, install the scheduled task if needed.

## Configuration Fields

Common fields:

- `username`
- `password`
- `switch_ip`
- `ssh_port`
- `vlan_id`
- `plink_path`
- `output_root`
- `host_key`
- `pool_name`
- `pool_network`
- `pool_mask`
- `lease_hours`
- `log_tail_lines`
- `schedule_minutes`
- `task_name`

Normally required for operation:

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

## Output Layout

The program writes files under `output_root`, grouped by date, for example:

```text
<output_root>\
  20260410\
    wifi_vlan_200_leases.csv
    raw\
      vlan_200_20260410_104000.txt
      vlan_200_dhcp_log_20260410_104000.txt
```

CSV columns:

- `地址池`
- `VLAN`
- `IP地址`
- `MAC地址`
- `首次分配时间`
- `最后活跃时间`
- `存活分钟`
- `最近租约到期时间`
- `地址类型`

Meaning:

- `首次分配时间`: first observed time for the `IP + MAC` pair
- `最后活跃时间`: latest observed time for the `IP + MAC` pair
- `存活分钟`: minutes between first observed time and latest observed time
- `最近租约到期时间`: latest observed lease expiration time

## Time Handling

- if DHCP assignment events can be identified from switch logs, the program uses log time first
- otherwise it estimates `首次分配时间` from `lease_expiration - lease_hours`
- so the first assignment time can be exact or estimated

## GUI Behavior

For stability, the current version uses synchronous execution for both `Test SSH` and `Run Export`.

That means:

- the UI may pause briefly during execution
- buttons are temporarily disabled
- the UI returns to normal after completion

This is intentional and prioritizes reliability over asynchronous behavior.

## Scheduled Task

The GUI can install a Windows scheduled task directly.

Recommended interval:

- every `30` minutes

## Troubleshooting

Check these first if export fails:

- correct SSH username and password
- correct SSH port
- reachable switch management IP
- `plink.exe` is in the same folder as the EXE
- `output_root` is writable
- correct DHCP pool name, network, and mask

## Intended Use

This tool is meant for DHCP audit retention and historical traceability, not for real-time endpoint monitoring.

</details>
