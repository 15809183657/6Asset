## Structure overview

<img src="https://cdn.sa.net/2023/12/30/F6K4qU5C8BpNlM1.png" alt="smap.png" style="zoom: 50%;"  />

## core

### Overview

This part is divided into the core part of the program, mainly including configuration analysis, receiving function, sending function, system call.

### conf

**set_conf**: Basic configuration, receive configuration, send configuration configuration module

**args**: Command line parameter Settings

**sys_config**: Parsing module for sys_conf.ini

**modules_config**: A resolution module with custom parameters

**tools**: Parameter analysis, network interface hardware analysis, etc

### receiver

Various receiving functions and their corresponding packet handling functions

Note: Custom receive functions should be at the same level as base, with reference to pmap implementation

### sender

**v4**: ipv4 send function

**v6**: ipv6 send function

**tools**: Auxiliary classes for sending functions, such as PID rate controllers, source address iterators, etc.

Note: The custom send function should be the same as base, with reference to pmap implementation

### sys

**packet_sender**: Packet sending function of each operating system

**logger**: System log configuration

## modes

### Overview

Pattern is the core of this program, generally by the definition of structure (including help information interface), the construction function, the execution function of three parts.

Define the global parameters that need to be used in the definition structure, such as base configuration, send configuration, probe module, interceptor, etc.

Constructors take command line arguments as input, and it is up to the developer to determine the construction target and construction logic.

The execution function is based on the structure constructed by the constructor, executes the probe logic defined by the user, and it is up to the user to decide whether to record and output, and what to record and output.

### Precautions

- The creation of the mode should comply with the specification, and the creation of a separate mod in the form of a separate folder under the corresponding mod.
The rs file in mod is generally named mod, new, execute, corresponding to the definition function, the constructor function, and the execution function respectively. Note the execute function and help function must call the corresponding interface, and the constructor must comply with the specification. To create more rs source files, use the tools.rs or tools folder.
- All available modes must be mounted in mod.rs under the MODES folder and declared in the Modes array.
- ** mode, ipv4 probe module, ipv6 probe module, output module ** help function must be mounted in the helper_mode modules_helper, otherwise invalid

### Submodule

**helper_mode** : indicates the module for printing help information.

**mix** : indicates the mixed mode of ipv4 and ipv6

**v4** : indicates ipv4 mode

**v6** : ipv6 mode

**macros** : Macro definition in the schema

## modules

### Overview

Currently includes output module, probe module, target iterator module, and new module categories may be added later as needed.

### output_modules

Except for the record file (input parameters, summary of probe results), all outputs are output using the output module selected by the user or mode.

Attention:

- The output module in principle uses a single rs source file.
The output module needs to implement the global constructor (new) and thread initialization function (init) and be mounted under mod.rs of output_modules.
- The output module must be declared in OUTPUT_MODS.
- The output module must implement the OutputMethod interface and help interface.

### probe_modules

Each probe module is typically composed of mod.rs and method.rs.

mod.rs includes the definition of probe module, global constructor, thread initialization function, help information function. Each probe module can fully customize the required fields to its own needs, with a fully custom construction process and thread initialization.

Method. rs implements the probe method interface for the probe module. In this part, we provide some basic detection module implementation methods and strive to design a simple, intuitive, high performance code specification for the detection module. It is recommended to refer to our preset detection module for module development.

Our probe module is very similar but slightly different from that of zmap. When we wrote the probe module, we almost copied the core logic of zmap, but also improved many parts in terms of performance, function, simplicity and intuition, and eliminated or corrected the parts that may cause problems.

Attention:

- The creation of the probe module should comply with the specification and create a separate mod in the form of a separate folder under the corresponding mod.

- The probe module consists of two source files, mod.rs and method.rs. The processing of data packets is stored in tools/net_handle/packet.

- The probe module needs to implement a global constructor (new) and a thread initialization function (init)

- The ipv4 probe module must implement the ProbeMethodV4 interface, the ipv6 probe module must implement the ProbeMethodV6 interface, and all probe modules must implement the help interface

- All available ipv4 probe modules should be mounted in probe_mod_v4.rs and declared in PROBE_MODS_V4.

- All available ipv6 probe modules should be mounted in probe_mod_v6.rs and declared in PROBE_MODS_V6.

### target_iterators

Target iterator is the core of all kinds of detection algorithms. For example, 1. The active address generation recommendation algorithm essentially solves the problem of which targets need to be detected next. 2. The active port recommendation algorithm essentially solves the problem of which ports of which targets need to be detected next, 3. Other algorithms, such as target in topology detection, lifetime combination and random address generation in a certain network segment to induce icmp errors to find some valuable targets, are essentially some type of target iteration algorithms.

Iterative objects generally include, but are not limited to, addresses, address port pairs, address ttl(or hop_limit) combinations, address specific payload combinations, etc. We abstract common categories into attributes, and iterators need to implement one of them, either to customize new attributes to apply to specific types of send and receive functions, or as part of more advanced iterators.

Base iterators now include multiplicative cyclic group iterators (including ipv4, ipv6, ipv6 mode strings, and their port versions), file iterators (divided into byte split file iterators and target split file iterators according to whether the number of targets is known), Active port recommendation iterators (pmap_v4, pmap_v6), and then we will gradually add active address recommendation algorithm iterators, topology detection iterators, and other important iterative algorithms.

## tools

### blocker

A whitelist interceptor is mainly used to filter valid source addresses and avoid sending probe packets to some network segments.

The interceptor algorithm we designed can effectively deal with very large blacklist lists, and the valid information stored in memory (array) must be smaller than the size of the blacklist itself. The maximum number of computation times for a single match is only the number of network segments with effective local constraints (network segment with effective local constraints <= Network segment with prefix aggregation <= address binary bit length). The effective local constraint is the marked network segment contained in the target network segment being detected (the marked network segment is in the current target network segment), and it supports rapid dynamic adjustment as the target range changes.

We designed the interceptor to be completely independent of the rest, which means you can use it in any scenario without restrictions.

### check_duplicates

Object checker, mainly used in the receiving thread to check for duplicate objects, or in some scenarios to avoid immediate output (pmap for example) as a probe result recorder.

The target checker is generally implemented using bitmaps, hash tables, Bloom filters, etc. For accuracy, only bitmap and hash table based checkers are currently provided by default, if you need to make very large measurements and can accept a certain error rate (if 'contains' returns' true', it may be in the filter. If ` contains ` returns false, is absolutely not in bloom filter), suggest call [growable - bloom - filter] (https://crates.io/crates/growable-bloom-filter).

The mod.rs file under this module defines a variety of common interfaces, and the weight finder algorithm needs to implement some of them or customize new interfaces to apply to specific types of receiving functions.

### encryption_algorithm

Perform key generation, random number generation, encryption payload, packet verification and other functions of the module.

### file

File operation module. Mainly used for obtaining path, parsing text, writing files and so on.

### net_handle

A library of tools for processing network data

- **dns** : Various functions used for dns resolution
- **net_interface** : Handles network hardware interfaces, hardware address definitions and their utility functions.
- **net_type** : Network segment definition, network type definition, and related utility functions.
- **packet** : packet definition, packet generation, packet parsing function, field definition and its parsing function, and other tool functions related to packet processing.

### others

Various other tool functions and underlying algorithms, such as sorting algorithms, lookup algorithms, string parsing functions, time processing, etc.

## System parameter (sys_conf.ini)

If the path is absolute, the path is specified by the user. If the path is relative, the path starts from the installation path. You can enter the smap command without parameters to obtain the current installation path.

- summary_file: records the file path. Be careful not to end the path with the. Extension. The path will be filled as

```shell
Set path_mode name_target name.csv
` ` `

- default_output_mod: indicates the default output module name

- default_send_attempt_num: Default number of times to retry after sending failed attempt_num

- default_source_ports: indicates the default source port range.** Note: Some detection modules use the source port as the verification condition. In principle, the range must be large enough

- default_probe_mod_v4: indicates the default ipv4 probe module. Do not change this parameter unless absolutely necessary. You are advised to keep the default probe module as icmp probe module.

- default_probe_mod_v6: indicates the default ipv6 probe module. Do not change this parameter unless absolutely necessary. You are advised to keep the default probe module as icmp probe module.

- default_batch_size: specifies the minimum unit of continuous data packets sent by the sending thread. The rate controller performs the rate control function at least once in each minimum transmit unit.

- default_must_sleep: indicates the minimum wait time after the sending thread sends a minimum unit, in ** microseconds **.

- default_cool_time: specifies the default wait time between the end of the sending thread and the end of the receiving thread.

- default_ports: indicates the default target port. Do not modify this option unless absolutely necessary. Note: ** The network layer protocol probe module checks whether the target port is 0.**

- output_file_pattern_v4:  ipv4 result output file path, can use %d %m %Y %H %M mode fields, these mode fields will be automatically replaced with the current time, Please refer to [DateTime in chrono] (https://docs.rs/chrono/0.4.31/chrono/struct.DateTime.html#method.format)

- output_file_pattern_v6:  ipv6 result output file path, can use %d %m %Y %H %M mode fields, these mode fields will be automatically replaced with the current time, Please refer to [DateTime in chrono] (https://docs.rs/chrono/0.4.31/chrono/struct.DateTime.html#method.format)

- default_output_buffer_capacity: indicates the default output buffer size, in bytes

- active_check_count: specifies how many active packets the receiving thread checks for pipeline messages

- capture_timeout: indicates the read timeout of the trap. Set to 0 to block indefinitely.

- pcap_recv_buffer_size: indicates the size of the pcap receive buffer

- get_socket_attempts: indicates the number of attempts to obtain the system socket after attempts fail

- attempt_sleep_millis: Number of milliseconds to sleep after failed send, valid only for send functions that set to sleep after failed send.

-kp: indicates the p parameter of the pid algorithm. Only modify this configuration if you are sure of your purpose and expect reasonable results.

-ki: indicates the i parameter of the pid algorithm. Only modify this configuration if you are sure of your purpose and expect reasonable results.

- ki_limit: indicates the steady-state error limit parameter of the pid algorithm. If the steady-state error is greater than $abs(ki\_limit * tar\_rate) $, the steady-state error is set to 0. Only modify this configuration if you are sure of your purpose and expect reasonable results.

-kd: indicates the d parameter of the pid algorithm. Only modify this configuration if you are sure of your purpose and expect reasonable results.

- destination_black_list_v4: indicates the default path of the ipv4 destination blacklist.

- destination_white_list_v4: default ipv4 destination whitelist path.

- source_black_list_v4: indicates the default path of the source IP address blacklist.

- source_white_list_v4: indicates the default path of the ipv4 source address whitelist.

- destination_black_list_v6: indicates the blacklisted destination path.

- destination_white_list_v6: indicates the default path of the ipv6 destination whitelist.

- source_black_list_v6: indicates the default path of the blacklist.

- source_white_list_v6: indicates the default whitelist path of the source address.

- fallback_bytes: indicates the number of bytes in the target range of the file iterator

- max_read_buf_bytes: indicates the maximum read buffer size of the file iterator

- default_payload_file: probe the load file path of the module

- log_pattern: log format, see [log_pattern] (https://docs.rs/log4rs/ * / log4rs/encode/pattern/index. The HTML)

- log_name_pattern: specifies the log file name format. This parameter is valid when the log directory is manually specified. Mode fields such as %d %m %Y %H %M can be used, which will automatically be replaced with the current time, Please refer to [DateTime in chrono] (https://docs.rs/chrono/0.4.31/chrono/struct.DateTime.html#method.format)

- running_time_pattern: indicates the pattern of the running time. Fields such as {d} {h} {m} {s} can be used. The system replaces the pattern with the actual running time.

- forecast_completion_time_pattern:  Expected completion time display format, refer to [DateTime in chrono] (https://docs.rs/chrono/0.4.31/chrono/struct.DateTime.html#method.format)

### AddrMiner-S System parameters

- default_code_probe_mod_v6: default coding probe module
- space_tree_type: indicates the type of the space tree
- budget: indicates the default budget
- batch_size: budget per round (maximum number of builds per round)
- divide_dim: indicates the partition of dimensions. For example, 4 indicates half-byte partition
- divide_range: Divide the range according to which part of the address structure is divided. Other parts will be replaced with 0 when input. If it is set to 1-64, the last 64 bits of all addresses will be replaced with 0 and will not be split and generated
- max_leaf_size: indicates the upper limit of the number of seed addresses in a cluster area. Nodes that are less than or equal to this number are no longer split.
- no_allow_gen_seeds: Seed addresses are not allowed to be generated (but other addresses in the input file that are not used as seed addresses can be generated)
- no_allow_gen_seeds_from_file: No addresses in the input file are allowed to be generated. If this is true, no_allow_gen_seeds is forced to be true
- learning_rate: indicates the learning rate
- region_extraction_num: Number of regions extracted. The first n regions are selected each time an address is generated. n is the minimum value of the number of regions extracted and the queue length
- seeds_num: indicates the number of seed addresses. A specified number of seed addresses are randomly selected from the input file


Pmap system parameters

- pmap_default_ports: specifies the default destination port range of pmap
- pmap_default_probe_mod_v4: indicates the default probe module name of pmap_v4
- pmap_default_probe_mod_v6: indicates the default probe module name of pmap_v6
- pmap_sampling_pro: indicates the default pmap sampling ratio (pre-scan ratio).
- pmap_min_sample_num: specifies the minimum number of pmap samples (number of pre-scanned target addresses).
- pmap_budget: indicates the recommended default budget for pmap ports
- pmap_batch_num: pmap recommended scan cycle (for example: If the value is set to 10, all the target addresses in the recommended scanning phase will be divided into 10 copies, one of which will be scanned completely (all recommended ports), and the next one will be scanned, during which the probability correlation graph will be updated according to the user's Settings).
- pmap_allow_graph_iter: Default value for whether to allow probabilistic correlation graph updates
- pmap_use_hash_recorder: Specifies whether to use the hash table as the recorder by default. If true, the hash table is used as the logger by default (for cases with a larger total range and more recommended rounds), and if false, the bitmap is used as the logger (for cases with a smaller total range and a higher percentage of activity).

### topo System parameters

- topo_max_ttl: indicates the default maximum ttl. The maximum value cannot exceed 64
- topo_udp_dest_port: specifies the default udp port number of the topology probe module
- topo_payload: Default payload of the topology probe module
- topo4_rand_bits: string of random bits in ipv4 mode (valid for fixed bits).
- topo4_default_probe_mod: indicates the topo4 default topology detection module
- topo6_rand_bits: string of random bits (valid for fixed bits) in ipv6 mode.
- topo6_default_probe_mod: topo6 default topology probe module
