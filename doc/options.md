## Summary

smap [-m < mode name >] [Option...] [-a < User-defined parameter name = User-defined parameter value >]

## Description

smap is a high-performance network detector for researchers and professionals. Support fast and comprehensive deep customization, flexibility to adapt to a variety of possible needs.

### Basic options

- **-m** | **--mode** : Indicates the name of the base mode or user-defined mode. All processes between each mode from parameter parsing, configuration Settings, execution process to result output are completely independent.** Note: If you do not use this option, you will enter Help mode.**

- **-i** | **--interface** : Specifies the name of the local network interface. smap supports multiple network interfaces at the same time, for example:

```shell
smap -m mode_name  -i interface_name_1  -i interface_name_2 -i interface_name_3  ...
` ` `

Note: The first parameter of the send and receive functions is the network interface index (starting at 0), and the base mode usually calls only the first network interface by default

- **-a** : User-defined parameter ** specified by user, mode, probe module, etc. Note that multiple assignments to the same variable name will result in overwriting, for example:

```shell
smap -m mode_name -a name_1=val_1 -a name_2=val_2 -a name_3=val_3  ...
` ` `

- **--probe_v4** : indicates the name of the Ipv4 probe module, which is used to probe ipv4 addresses. The probe module is independent of the ipv6 probe module

- **--probe_v6** : indicates the name of the Ipv6 probe module, which is used to probe ipv6 addresses. The probe module and ipv4 probe module are independent of each other

### Send options

- **-t** | **--tar_ips** : specifies the target ip address range. Note that the format of the target IP address range varies with the mode

- **-f** | **--target_file** : Set the primary target path, which is applicable to the incoming mode of a single target file or the path of the primary target folder in the multi-target file mode

- **-p** | **--tar_ports** : indicates the target port range. For example: **\*** or **a-b,c-d,e**,**\*** denotes all ports here

- * * - * * - * * | b band_width * * : send the bandwidth setting (K, M, G), pay attention to the bandwidth and transmission speed cannot be set at the same time

- **--send_rate** : indicates the number of packets sent per second. Note that the bandwidth and sending rate cannot be set at the same time

- **--ttl** : ttl(ipv4) for sending data packets, set hop_limit. Note that this option is only used in basic mode and some other modes

* * : - * * - saddr to send packet source address range, such as: a - b (ipv4), c - d (ipv6), e, f / 10 (ipv4). Supports different ranges, and supports the input of ipv4 and ipv6 addresses at the same time, supports a single address, network segment, range. Note :(ipv4) and (ipv6) indicates that the previous address is ipv4 or ipv6, please do not enter (ipv4) or (ipv6).

- **--sport** : indicates the range of source ports used to send data packets. For example: **\*** or **a-b,c-d,e**, **\*** denotes all ports here

- **--send_thread_num** : indicates the number of sending threads

- **--send_attempt_num** : Number of retries for sending packets (maximum number of attempts if sending fails)

- **--batch_size** : Specifies the size of each send round

- **--must_sleep** : Specifies the amount of time that must be waited after each send round is completed

- **--cool_seconds** : specifies the cooldown time between the end of the sending thread and the end of the receiving thread

### Receive options

- **-o** | **--output** : Indicates the name of the output module
- **--allow_no_succ** : Allows output that fails to be probed but succeeds, such as Icmp packet raw packets, Rst flag packets, etc
- **--output_file_v4** : indicates the output file path of the ipv4 probe module
- **--output_file_v6** : indicates the output file path of the ipv6 probe module
- **--filter** : indicates the packet filtering method of the receiving thread
- **--fields** : indicates the output field. (If this option is not set, all fields are displayed.)

### Blacklist options

- **--black_list_v4** : indicates the Ipv4 blacklist path

- **--black_list_v6** : indicates the Ipv6 blacklist path

- **--white_list_v4** : indicates the path of the Ipv4 whitelist

- **--white_list_v6** : indicates the whitelist path

- **--source_black_list_v4** : indicates the blacklist path of the Ipv4 source address

- **--source_black_list_v6** : indicates the blacklist path of the Ipv6 source address

- **--source_white_list_v4** : indicates the whitelist path of the Ipv4 source address

- **--source_white_list_v6** : indicates the whitelist path of the Ipv6 source address

### Help options

- None Option (the output command is smap): Displays help information, all modes, probe module, output module name, and current installation path

- **--mode_help** : indicates the mode name. The help information about the mode is displayed, for example:

```shell
smap --mode_help  mode_name_1
` ` `

- **--probe_v4_help** : indicates the name of the ipv4 probe module. The help information about the corresponding ipv4 probe module is displayed

- **--probe_v6_help** : indicates the name of the ipv6 probe module. The help information about the ipv6 probe module is displayed

- **--output_help** : indicates the name of the output module. The help information about the output module is displayed

### Log option

- **-q** | **--disable_sys_log** : Indicates whether to disable the output of the log terminal

- **--log_level** : Log level Example: 0 1 2 3 4 5 The log level is increased from 0 to 5. trace debug info warn error is displayed in lower case. The default value is trace

- **--log_file** : indicates the path of the log output file.** Note: If the log output file or log output directory is not set, the log will not be stored to the file by default.**

- **--log_directory** : log output directory (log files in a specified format are created in the directory). ** Note: If the log output file or log output directory is not set, logs are not stored in the file by default.**

### Other options

- **--seed** : random number seed, which is used to set encryption keys and generate random numbers

- **--summary_file** : indicates the file path. Save the configuration information and scan result of a scan.

Be careful not to end the path with the. Extension. The path will be filled as

```shell
Set path_mode name_target name.csv
` ` `
