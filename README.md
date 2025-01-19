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

To probe with a budget of 50M, bandwidth of 30M, and the number of addresses generated per round based on reinforcement learning feedback, using online alias prefix detection:

```bash
smap -m a6 -b 30m -f ./testData.txt -a budget=50000000 -a region_extraction_num=1000 --cool_seconds 3 --output_file_v6 ./res.txt -a batch_size=2000000 -a aliased_threshold=0.8 -a aliased_prefixes_check=true
