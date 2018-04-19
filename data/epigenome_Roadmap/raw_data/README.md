Download the histone modification raw data

on a linux machine:

```bash
mkdir tagAlign_files
cd tagAlign_files
cat sample_with_rnaseq.txt | sed '1d' | cut -f1 > ID.txt

cat ID.txt | parallel -j5 'echo wget -r --no-parent -nH --cut-dir=5 -A {}* http://egg2.wustl.edu/roadmap/data/byFileType/alignments/consolidated/'

wget -r --no-parent -nH --cut-dir=5 -A E056* http://egg2.wustl.edu/roadmap/data/byFileType/alignments/consolidated/
wget -r --no-parent -nH --cut-dir=5 -A E059* http://egg2.wustl.edu/roadmap/data/byFileType/alignments/consolidated/
wget -r --no-parent -nH --cut-dir=5 -A E061* http://egg2.wustl.edu/roadmap/data/byFileType/alignments/consolidated/
wget -r --no-parent -nH --cut-dir=5 -A E057* http://egg2.wustl.edu/roadmap/data/byFileType/alignments/consolidated/
wget -r --no-parent -nH --cut-dir=5 -A E058* http://egg2.wustl.edu/roadmap/data/byFileType/alignments/consolidated/
wget -r --no-parent -nH --cut-dir=5 -A E028* http://egg2.wustl.edu/roadmap/data/byFileType/alignments/consolidated/
wget -r --no-parent -nH --cut-dir=5 -A E027* http://egg2.wustl.edu/roadmap/data/byFileType/alignments/consolidated/
wget -r --no-parent -nH --cut-dir=5 -A E083* http://egg2.wustl.edu/roadmap/data/byFileType/alignments/consolidated/
wget -r --no-parent -nH --cut-dir=5 -A E104* http://egg2.wustl.edu/roadmap/data/byFileType/alignments/consolidated/
wget -r --no-parent -nH --cut-dir=5 -A E095* http://egg2.wustl.edu/roadmap/data/byFileType/alignments/consolidated/
wget -r --no-parent -nH --cut-dir=5 -A E105* http://egg2.wustl.edu/roadmap/data/byFileType/alignments/consolidated/
wget -r --no-parent -nH --cut-dir=5 -A E065* http://egg2.wustl.edu/roadmap/data/byFileType/alignments/consolidated/
wget -r --no-parent -nH --cut-dir=5 -A E092* http://egg2.wustl.edu/roadmap/data/byFileType/alignments/consolidated/
wget -r --no-parent -nH --cut-dir=5 -A E085* http://egg2.wustl.edu/roadmap/data/byFileType/alignments/consolidated/
wget -r --no-parent -nH --cut-dir=5 -A E084* http://egg2.wustl.edu/roadmap/data/byFileType/alignments/consolidated/
wget -r --no-parent -nH --cut-dir=5 -A E109* http://egg2.wustl.edu/roadmap/data/byFileType/alignments/consolidated/
wget -r --no-parent -nH --cut-dir=5 -A E106* http://egg2.wustl.edu/roadmap/data/byFileType/alignments/consolidated/
wget -r --no-parent -nH --cut-dir=5 -A E075* http://egg2.wustl.edu/roadmap/data/byFileType/alignments/consolidated/
wget -r --no-parent -nH --cut-dir=5 -A E101* http://egg2.wustl.edu/roadmap/data/byFileType/alignments/consolidated/
wget -r --no-parent -nH --cut-dir=5 -A E102* http://egg2.wustl.edu/roadmap/data/byFileType/alignments/consolidated/
wget -r --no-parent -nH --cut-dir=5 -A E110* http://egg2.wustl.edu/roadmap/data/byFileType/alignments/consolidated/
wget -r --no-parent -nH --cut-dir=5 -A E077* http://egg2.wustl.edu/roadmap/data/byFileType/alignments/consolidated/
wget -r --no-parent -nH --cut-dir=5 -A E079* http://egg2.wustl.edu/roadmap/data/byFileType/alignments/consolidated/
wget -r --no-parent -nH --cut-dir=5 -A E094* http://egg2.wustl.edu/roadmap/data/byFileType/alignments/consolidated/
```


This will dowload histone modification reads location files for our test samples.
For some samples, there are more than 5 histone modifications were profiled. For analysis,
only use 5 of them:

>The five marks consist of: histone H3 lysine 4 trimethylation (H3K4me3), associated with promoter regions; H3 lysine 4 monomethylation (H3K4me1), associated with enhancer regions; H3 lysine 36 trimethylation (H3K36me3), associated with transcribed regions; H3 lysine 27 trimethylation (H3K27me3), associated with Polycomb repression; and H3 lysine 9 trimethylation (H3K9me3), associated with heterochromatin regions.
