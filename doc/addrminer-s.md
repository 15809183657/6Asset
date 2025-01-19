# AddrMiner-S Parameters

- `space_tree_type`: Type of space tree.
- `budget`: Default budget.
- `batch_size`: Budget per round (maximum number of addresses generated per round).
- `divide_dim`: Division dimension, e.g., 4 represents nibble division.
- `divide_range`: Division range, specifying which part of the address structure to split. Other parts will be replaced with 0 during input. For example, if set to 1-64, the last 64 bits of all addresses will be replaced with 0 and will not be used for splitting or generation.
- `max_leaf_size`: Maximum number of seed addresses in a cluster region (nodes with fewer than or equal to this number will not split further).
- `no_allow_gen_seeds`: Disallow generating seed addresses (but allows generating other addresses from the input file that are not used as seed addresses).
- `no_allow_gen_seeds_from_file`: Disallow generating any addresses from the input file. If this is true, `no_allow_gen_seeds` will be forced to true.
- `learning_rate`: Learning rate.
- `region_extraction_num`: Number of regions to extract. During address generation, the top n regions (with the highest reward ranking) will be selected, where n is the minimum of the region extraction number and the queue length.
- `seeds_num`: Number of seed addresses. A specified number of addresses will be randomly selected from the input file as seed addresses.
