# Pmap
- pmap_sampling_pro: pmap sampling ratio (pre-scan ratio)
- pmap_min_sample_num: specifies the minimum number of pmap samples (number of pre-scanned target addresses).
- pmap_budget: indicates the recommended pmap port budget
- pmap_batch_num: pmap recommended scan cycle (for example, if the value is set to 10, all target addresses in the recommended scan phase are divided into 10 copies. After scanning one copy (all recommended ports), the next copy is scanned. In the process, whether to update the probability correlation graph according to the user's Settings is selected.)
- pmap_allow_graph_iter: Specifies whether to allow probability correlation graphs to be updated
- pmap_use_hash_recorder: Specifies whether to use a hash table as a recorder. If true, the hash table is used as the logger by default (for cases with a larger total range and more recommended rounds), and if false, the bitmap is used as the logger (for cases with a smaller total range and a higher percentage of activity).
- pmap_port_num_limit: Addresses whose open ports exceed this limit are regarded as abnormal addresses and do not participate in probability correlation graph training
