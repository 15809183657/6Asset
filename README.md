# 6Asset

By constructing an association mapping table, the 16-bit port numbers are mapped to hexadecimal values as part of the address, unifying the prediction of IPv6 addresses and ports, 6Asset can directly predict potential active assets. At the same time, it utilizes an asset space tree and a reinforcement learning feedback mechanism to generate target assets and proposes the online lightweight alias prefix detection method, effectively reducing the impact of aliased assets on detection.

## Installation

6Asset supports mainstream platforms such as Windows, Linux, macOS, and BSD.

### Rust Environment

Refer to the official documentation for installation:

[Other Installation Methods - Rust Forge (rust-lang.org)](https://forge.rust-lang.org/infra/other-installation-methods.html)

### Build and Installation

#### Preparation

1. Open **sys_conf.ini** in the **root directory** and modify the default configurations and prompt messages. 

   These configurations will be read and written into the program during compilation. Unless recompiled, the configurations in this file will remain unchanged.

   All prompt messages are written by this file. You can translate the program into another language by modifying the messages in this file.

2. Open **Cargo.toml** in the **root directory** and adjust the necessary settings according to your system platform and requirements.

   Recommendation: Set `opt-level` under `[profile.release]` to `3`.

3. 6Asset depends on pcap, which is checked and installed by the automated installation script. No manual installation or configuration is required.

#### Installation

In the **root directory**, choose the installation command corresponding to your system platform and follow the prompts to enter the installation path or select the default installation path.

Note:

- Ensure you are connected to the internet during installation.
- The custom installation path must include the program name.
- On Windows, run the PowerShell script using a terminal application, and the default compilation target is `stable-x86_64-pc-windows-gnu`.
- Do not set the installation path to the source code directory.

##### Windows (root)

   ```powershell
   .\install_windows.ps1
   ```

##### Linux (root)

```shell
./install_linux.sh
```

##### Macos (root)

```shell
./install_macos.sh
```
🔴⚠️ Note: When prompted for keyboard input during installation, please press Enter without typing 'y'.

## Pre-Configuration
Check the configuration of the destination and source address black and white lists (Path: block_list folder under the installation path; you can enter the command 6Asset to get the current installation path). The black and white lists support domain names (multiple addresses corresponding to the domain name can be added simultaneously), single addresses, and subnets.

Note: The source address checker will automatically filter private addresses and marked subnets, regardless of whether they are automatically retrieved from the system or manually entered. If you need to use private addresses as source addresses, such as those from campus networks, corporate, or home internal networks, you must add the local network to the source address whitelist.

# 6Asset Parameters

- **f**: File path for port mapping
- **budget**: Total budget size
- **batch_size**: Number of items generated per round
- **divide_dim**: Division dimension (default is 4, which means splitting the entire IPv6 address into 128/4 blocks)
- **max_leaf_size**: Maximum leaf node size (default is 16; if the node size is less than this value, splitting will stop)
- **no_allow_gen_seeds**: Whether to allow the generation of seed addresses (default is not allowed)
- **learning_rate**: Learning rate (default is 0.1)
- **region_extraction_num**: Number of region extractions (default is 1000)
- **aliased_threshold**: Alias threshold (default is 0.8)
- **no_allow_gen_seeds_from_file**: Whether to allow generating addresses from the input file (default is allowed)
- **aliased_prefixes_check**: Whether to perform alias checks (default is performed)
- **aliased_prefixes_path**: Output file for aliased prefixes (default does not output)

---

# IPv6AliasedCheck Parameters

- **f**: File path of the addresses to be checked
- **prefix_len**: Prefix length (default is 64)
- **prefix_count**: Known or predicted number of prefixes, used only to optimize memory usage (default is 1 million)
- **rand_addr_len**: Number of random addresses generated for each prefix
- **aliased_threshold**: Alias threshold. When the number of response addresses for a prefix reaches `alias threshold * number of random addresses per prefix`, that prefix is counted as an aliased prefix. The value range is (0.0, 1.0], and it must include a decimal point.
- **output_alia_addrs**: Whether to count and output the aliased addresses under the aliased prefixes
- **prefixes_len_per_batch**: Number of prefixes to probe in each round (default is 1 million)
- **not_aliased_records_path**: Path for outputting non-aliased records (default does not output)

---

# Usage Example

To probe with a budget of 50M, bandwidth of 30M, and the number of addresses generated per round based on reinforcement learning feedback is 2M, using online alias prefix detection:

```bash
smap -m a6 -b 60m -f ./testData.txt -a budget=5000000 -a region_extraction_num=1000 --cool_seconds 3 --output_file_v6 ./res.txt -a batch_size=2000000 -a aliased_threshold=0.8 -a aliased_prefixes_check=true
