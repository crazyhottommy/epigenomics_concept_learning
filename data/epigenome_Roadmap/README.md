The main site to visit for epigenome roadmap data is: http://egg2.wustl.edu/roadmap/web_portal/

The segmentation files are downloaded from http://egg2.wustl.edu/roadmap/data/byFileType/chromhmmSegmentations/ChmmModels/coreMarks/jointModel/final/

The meta data annotation file was downloaded from https://docs.google.com/spreadsheets/d/1yikGx4MsO9Ei36b64yOy9Vb6oPC5IBGlFbYEt-N6gOM/edit#gid=15
I converted it to a tsv file `Consolidated_EpigenomeIDs_summary_Table.tsv`. The file is still not tidy, but can serve as a good start point.


### RNAseq

RNAseq was downloaded from http://egg2.wustl.edu/roadmap/web_portal/processed_data.html#RNAseq_uni_proc
Two files were downloaded on 08/28/2018

* 57epigenomes.RPKM.pc: RPKM expression matrix for protein coding genes
* 57epigenomes.N.pc: RNA-seq read counts matrix for protein coding genes

use `57epigenomes.RPKM.pc` for analysis (normalized gene expression values). `57epigenomes.N.pc` is the raw counts might be useful for differential
gene expression analysis with `DESeq2`.

```bash
wget http://egg2.wustl.edu/roadmap/data/byDataType/rna/expression/57epigenomes.RPKM.pc.gz
wget http://egg2.wustl.edu/roadmap/data/byDataType/rna/expression/57epigenomes.N.pc.gz
```

### download chromHMM state data

```bash
# on our shark HPC:
cd /rsrch2/genomic_med/krai/epigenome_roadmap/concept_learning_chromHMM_segs
cat sample_ids.txt | while read -r id
do
  wget -r --no-parent -nH --cut-dir=8 -A ${id}*segments.bed http://egg2.wustl.edu/roadmap/data/byFileType/chromhmmSegmentations/ChmmModels/coreMarks/jointModel/final/
done

```

chromHMM was called in 15 state model with 200 bp bin. If consecutive bins are of the same state, it will be combined.

```bash
cd /rsrch2/genomic_med/krai/epigenome_roadmap/concept_learning_chromHMM_segs
head E027_15_coreMarks_segments.bed
chr10	0	110000	E15
chr10	110000	110400	E1
chr10	110400	119600	E15
chr10	119600	120200	E1
chr10	120200	120400	E7
chr10	120400	122000	E5
chr10	122000	122800	E1
chr10	122800	125000	E5
chr10	125000	125600	E7
chr10	125600	125800	E12

```

chr10 from 0 to 110000 are of state E15. I will tile the bins back to 200bp.
one can probably write some python script to do the same, but the `tile` function
in `GenomicRanges` can handle the edge cases when the length of the whole chromosome
is not `n * 200`, which is almost always the case.

```r
library(GenomicRanges)
library(rtracklayer)
library(purrr)
library(tidyverse)
segs<- list.files(".", pattern = "segments.bed")

segs<- set_names(segs, gsub("_15_coreMarks_segments.bed", "", segs))
chromHMM_segs<- purrr::map(segs, import, format = "BED")

tile_chromHMM<- function(chromHMM_seg){
  chromHMM_seg_tile<- tile(chromHMM_seg, width = 200)
  names(chromHMM_seg_tile)<- chromHMM_seg$name
  stack(chromHMM_seg_tile, "state")
}

# this can take a while..
chromHMM_tiles<- map(chromHMM_segs, tile_chromHMM)

## write back to file as a bed file. bed file is 0 based, in R, everything is 1 based.
## start(gr) -1

make_df<- function(gr){
  data.frame(chr = seqnames(gr), start = start(gr) - 1, end = end(gr), state = gr$state)
}

chromHMM_dfs<- map(chromHMM_tiles, make_df)

walk2(chromHMM_dfs, names(chromHMM_dfs), function(x,y) write.table(x, paste0(y, "_segments.bed"), row.names =F, col.names =F, sep = "\t", quote =F))
```

Now, it is 200 bp per bin.

```bash
head E027_segments.bed
chr10	0	200	E15
chr10	200	400	E15
chr10	400	600	E15
chr10	600	800	E15
chr10	800	1000	E15
chr10	1000	1200	E15
chr10	1200	1400	E15
chr10	1400	1600	E15
chr10	1600	1800	E15
chr10	1800	2000	E15

```

### first run testing data

I choose the ones with both chromHMM data and rna seq data.

```
id    tissue    has_rnaseq
E055	Epithelial	yes
E056	Epithelial	yes
E059	Epithelial	yes
E061	Epithelial	yes
E057	Epithelial	yes
E058	Epithelial	yes
E028	Epithelial	yes
E027	Epithelial	yes
E054	Neurosph	yes
E053	Neurosph	yes
E092	Digestive	no
E085	Digestive	yes
E084	Digestive	yes
E109	Digestive	yes
E106	Digestive	yes
E075	Digestive	no
E101	Digestive	no
E102	Digestive	no
E110	Digestive	no
E077	Digestive	no
E079	Digestive	yes
E094	Digestive	yes

```

`test_run_rnaseq.tsv` in the `rnaseq` folder contains a subset of `57epigenomes.RPKM.pc` with
only the samples listed above.


### gene annotation file

```bash
# this one is used for RNAseq quantification

wget http://egg2.wustl.edu/roadmap/data/byDataType/rna/annotations/gen10.long.gtf.gz

## extract only the gene information.
zless -S  gen10.long.gtf.gz | awk '$3 == "gene"' > gencode_10_genes.gtf
bgzip gencode_10_genes.gtf
```
`genecode_10_genes.gtf.gz` is in the annotation folder.

simplify that file further:

```bash
zless -S gencode_10_genes.gtf.gz | cut -f1,5 -d ";" | sed 's/gene_id //' | sed 's/"//g' | sed 's/gene_name //' | sed 's/; /\t/' | sed -E 's/\.[0-9]+//' | cut -f1,4,5,7,9,10 > genecode_10_simplified.tsv
```
