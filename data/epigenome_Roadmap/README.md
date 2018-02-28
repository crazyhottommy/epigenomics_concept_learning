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
