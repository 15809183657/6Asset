# IPv6 Aliased Prefixes Checker Parameters

- `f`: Path to the file containing addresses to be checked.
- `prefix_len`: Prefix length, default is 64.
- `prefix_count`: Number of known or predicted prefixes, used only to optimize memory usage, default is 1 million.
- `rand_addr_len`: Number of random addresses generated per prefix.
- `aliased_threshold`: Alias threshold. A prefix is considered aliased if the number of responding addresses reaches `aliased_threshold * rand_addr_len`. The value range is (0.0, 1.0], and it must include a decimal point.
- `output_alia_addrs`: Whether to count and output aliased addresses under aliased prefixes.
- `prefixes_len_per_batch`: Number of prefixes to probe per batch, default is 1 million.
- `not_aliased_records_path`: Path to output non-aliased records. By default, no output is generated.
