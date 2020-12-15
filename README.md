# FMD-index from BWA

## 使用方法

- 下载项目。

```shell
git clone --recursive https://github.com/weiquan/FMD-index.git

```

- 参考example.c，根据自己项目需要修改相应代码。
- 修改Makefile

## 部分常用函数注释

BWA将参考基因组和其反向互补连接，形成双向参考基因组序列。
未避免歧义，用ref、rev_ref和double_ref分表表示原参考基因组、反向互补参考基因组和连接后形成的双向参考基因组序列。

```c
//读取bwa索引， hint是索引前缀，which读取索引类型（BWA_IDX_BWT、BWA_IDX_BNS、BWA_IDX_PAC或BWA_IDX_ALL）
bwaidx_t *bwa_idx_load(const char *hint, int which);
void bwa_idx_destroy(bwaidx_t *idx); //释放bwa索引

//由于bwa-mem是对double_ref构建了FM-index，所以需要将double_ref中位置pos转换为ref中的坐标pos_f。其中is_rev表示是否是反向。
static inline int64_t bns_depos(const bntseq_t *bns, int64_t pos, int *is_rev);
//获取位置pos_f的染色体id
int bns_pos2rid(const bntseq_t *bns, int64_t pos_f);

//2BWT ik为输入， ok为输出，具体使用方法建议参考bwt.c文件中函数bwt_smem1a
void bwt_extend(const bwt_t *bwt, const bwtintv_t *ik, bwtintv_t ok[4], int is_back)；
//将BWT的行号转换为double_ref中坐标pos
bwtint_t bwt_sa(const bwt_t *bwt, bwtint_t k)；
//bwt的精确匹配函数，len为str长度，str为被搜索串，k0, l0为搜索str前的bwt索引区间，调用完成后通过k0,l0返回搜索str后的索引区间。建议仔细看一下这一部分代码，注意k0和l0变换情况。
int bwt_match_exact_alt(const bwt_t *bwt, int len, const ubyte_t *str, bwtint_t *k0, bwtint_t *l0)；
```
