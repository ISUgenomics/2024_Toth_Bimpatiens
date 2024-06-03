# 01_Bombus_impatiens 


### Run juicer
```
/work/gif/remkv6/Toth/02_Juicer/01_Bombus_impatiens/01_Juicer

references/
ln -s /work/LAS/amytoth-lab/Dovetail_data_2021/Bombus_impatiens/HiRise/jasmine-iow2046-mb-hirise-wutw5__02-23-2021__hic_output.fasta BimpatiensGenome.fa

fastq/
ln -s ../../../../01_ExtractBams/Bombus_impatiens.fastqR1.fq Bimpatiens_R1.fastq
ln -s ../../../../01_ExtractBams/Bombus_impatiens.fastqR2.fq Bimpatiens_R2.fastq

splits/
split -a 3 -l 90000000 -d --additional-suffix=_R2.fastq ../fastq/Bimpatiens_R2.fastq &
split -a 3 -l 90000000 -d --additional-suffix=_R1.fastq ../fastq/Bimpatiens_R1.fastq &

/01_Juicer/
module load bioawk
bioawk -c fastx '{print $name"\t"length($seq)}' references/BimpatiensGenome.fa >chrom.sizes

ml jdk; ml bwa; ml juicer/1.5.7; juicer.sh -d /work/gif/remkv6/Toth/02_Juicer/01_Bombus_impatiens/01_Juicer -p /work/gif/remkv6/Toth/02_Juicer/01_Bombus_impatiens/01_Juicer/chrom_sizes -s none -z /work/gif/remkv6/Toth/02_Juicer/01_Bombus_impatiens/01_Juicer/references/BimpatiensGenome.fa  -q short -Q 2:00:00 -l medium -L 12:00:00 -t 36
```

### Run 3ddna
three different runs to see which produces the best assembly. 
```
==> 3Ddna_0.sub <==
module load miniconda3/4.3.30-qdauveb;source activate 3d-dna;module load jdk;module load parallel;cd /work/gif/remkv6/Toth/02_Juicer/01_Bombus_impatiens/01_Juicer/3ddna/01_3d-dnaDefault/ ;bash run-asm-pipeline.sh /work/gif/remkv6/Toth/02_Juicer/01_Bombus_impatiens/01_Juicer/3ddna/01_3d-dnaDefault/BimpatiensGenome.fa /work/gif/remkv6/Toth/02_Juicer/01_Bombus_impatiens/01_Juicer/3ddna/01_3d-dnaDefault/merged_nodups.txt

==> 3Ddna_1.sub <==
module load miniconda3/4.3.30-qdauveb;source activate 3d-dna;module load jdk;module load parallel;cd /work/gif/remkv6/Toth/02_Juicer/01_Bombus_impatiens/01_Juicer/3ddna/02_3d-dnaRepCov/;bash run-asm-pipeline.sh --editor-repeat-coverage 20 /work/gif/remkv6/Toth/02_Juicer/01_Bombus_impatiens/01_Juicer/3ddna/02_3d-dnaRepCov/BimpatiensGenome.fa /work/gif/remkv6/Toth/02_Juicer/01_Bombus_impatiens/01_Juicer/3ddna/02_3d-dnaRepCov/merged_nodups.txt

==> 3Ddna_2.sub <==
module load miniconda3/4.3.30-qdauveb;source activate 3d-dna;module load jdk;module load parallel;cd /work/gif/remkv6/Toth/02_Juicer/01_Bombus_impatiens/01_Juicer/3ddna/03_3d-dnaDiploidRepCov/ ;bash run-asm-pipeline.sh -m diploid --editor-repeat-coverage 20 /work/gif/remkv6/Toth/02_Juicer/01_Bombus_impatiens/01_Juicer/3ddna/03_3d-dnaDiploidRepCov/BimpatiensGenome.fa /work/gif/remkv6/Toth/02_Juicer/01_Bombus_impatiens/01_Juicer/3ddna/03_3d-dnaDiploidRepCov/BimpatiensGenome.fa/merged_nodups.txt
```


### Results of 3DDNA runs
```
#Input
---------------- Information for assembly 'BimpatiensGenome.fa' ----------------


                                         Number of scaffolds        786
                                     Total size of scaffolds  266302045
                                            Longest scaffold   18540056
                                           Shortest scaffold         89
                                           Number of scaffolds > 1K nt        782  99.5%
                                          Number of scaffolds > 10K nt        589  74.9%
                                         Number of scaffolds > 100K nt         47   6.0%
                                           Number of scaffolds > 1M nt         19   2.4%
                                          Number of scaffolds > 10M nt         15   1.9%
                                                    Mean scaffold size     338807
                                                  Median scaffold size      20575
                                                   N50 scaffold length   15710883
                                                    L50 scaffold count          8
                                                   n90 scaffold length    3147645
                                                    L90 scaffold count         19
                                                           scaffold %A      31.28
                                                           scaffold %C      18.73
                                                           scaffold %G      18.71
                                                           scaffold %T      31.28
                                                           scaffold %N       0.01
                                                   scaffold %non-ACGTN       0.00
                                       Number of scaffold non-ACGTN nt          0

                          Percentage of assembly in scaffolded contigs      91.4%
                        Percentage of assembly in unscaffolded contigs       8.6%
                                Average number of contigs per scaffold        1.2
          Average length of break (>25 Ns) between contigs in scaffold        100

#Default 3ddna
---------------- Information for assembly 'BimpatiensGenome.FINAL.fasta' ----------------


                                         Number of scaffolds        820
                                     Total size of scaffolds  266367545
                                            Longest scaffold   18528710
                                           Shortest scaffold         89
                                 Number of scaffolds > 1K nt        812  99.0%
                                Number of scaffolds > 10K nt        555  67.7%
                               Number of scaffolds > 100K nt         27   3.3%
                                 Number of scaffolds > 1M nt         20   2.4%
                                Number of scaffolds > 10M nt         15   1.8%
                                          Mean scaffold size     324838
                                        Median scaffold size      16722
                                         N50 scaffold length   15675000
                                          L50 scaffold count          8
                                         n90 scaffold length    4732018
                                          L90 scaffold count         18
                                                 scaffold %A      31.29
                                                 scaffold %C      18.73
                                                 scaffold %G      18.69
                                                 scaffold %T      31.25
                                                 scaffold %N       0.03
                                         scaffold %non-ACGTN       0.00
                             Number of scaffold non-ACGTN nt          0

Percentage of assembly in scaffolded contigs      93.9%
Percentage of assembly in unscaffolded contigs       6.1%
Average number of contigs per scaffold        1.3
Average length of break (>25 Ns) between contigs in scaffold        289


### Rep cov 20 3ddna assembly
---------------- Information for assembly 'BimpatiensGenome.FINAL.fasta' ----------------


                                         Number of scaffolds        768
                                     Total size of scaffolds  266382545
                                            Longest scaffold   18534760
                                           Shortest scaffold         89
                                 Number of scaffolds > 1K nt        758  98.7%
                                Number of scaffolds > 10K nt        506  65.9%
                               Number of scaffolds > 100K nt         25   3.3%
                                 Number of scaffolds > 1M nt         20   2.6%
                                Number of scaffolds > 10M nt         15   2.0%
                                          Mean scaffold size     346852
                                        Median scaffold size      16055
                                         N50 scaffold length   15644808
                                          L50 scaffold count          8
                                         n90 scaffold length    6123483
                                          L90 scaffold count         18
                                                 scaffold %A      31.30
                                                 scaffold %C      18.73
                                                 scaffold %G      18.69
                                                 scaffold %T      31.24
                                                 scaffold %N       0.04
                                         scaffold %non-ACGTN       0.00
                             Number of scaffold non-ACGTN nt          0

                Percentage of assembly in scaffolded contigs      94.4%
              Percentage of assembly in unscaffolded contigs       5.6%
                      Average number of contigs per scaffold        1.4
Average length of break (>25 Ns) between contigs in scaffold        309
```




### After manual intervention with Juicebox
Used the initial scaffolding without breaking any contigs in 3ddna. 
```

/work/gif/remkv6/Toth/02_Juicer/01_Bombus_impatiens/01_Juicer/3ddna/04_3d-dnaDiploidRepCovFinalize
#uploaded from laptop
BimpatiensGenome.0.review.assembly
ln -s ../../references/BimpatiensGenome.fa
ln -s ../../aligned/merged_nodups.txt


module load miniconda3/4.3.30-qdauveb;source activate 3d-dna;module load jdk;module load parallel;cd /work/gif/remkv6/Toth/02_Juicer/01_Bombus_impatiens/01_Juicer/3ddna/04_3d-dnaDiploidRepCovFinalize;bash run-asm-pipeline-post-review.sh --sort-output -s seal -i 500 -r /work/gif/remkv6/Toth/02_Juicer/01_Bombus_impatiens/01_Juicer/3ddna/04_3d-dnaDiploidRepCovFinalize/BimpatiensGenome.0.review.assembly /work/gif/remkv6/Toth/02_Juicer/01_Bombus_impatiens/01_Juicer/3ddna/04_3d-dnaDiploidRepCovFinalize/BimpatiensGenome.fa /work/gif/remkv6/Toth/02_Juicer/01_Bombus_impatiens/01_Juicer/3ddna/04_3d-dnaDiploidRepCovFinalize/merged_nodups.txt

---------------- Information for assembly 'BimpatiensGenome.FINAL.fasta' ----------------


                                         Number of scaffolds        210
                                     Total size of scaffolds  266606045
                                            Longest scaffold   20973032
                                           Shortest scaffold      15040
                                 Number of scaffolds > 1K nt        210 100.0%
                                Number of scaffolds > 10K nt        210 100.0%
                               Number of scaffolds > 100K nt         19   9.0%
                                 Number of scaffolds > 1M nt         18   8.6%
                                Number of scaffolds > 10M nt         16   7.6%
                                          Mean scaffold size    1269553
                                        Median scaffold size      28615
                                         N50 scaffold length   16149712
                                          L50 scaffold count          8
                                         n90 scaffold length   11380321
                                          L90 scaffold count         16
                                                 scaffold %A      31.26
                                                 scaffold %C      18.70
                                                 scaffold %G      18.69
                                                 scaffold %T      31.22
                                                 scaffold %N       0.12
                                         scaffold %non-ACGTN       0.00
                             Number of scaffold non-ACGTN nt          0

                Percentage of assembly in scaffolded contigs      97.8%
              Percentage of assembly in unscaffolded contigs       2.2%
                      Average number of contigs per scaffold        4.6
Average length of break (>25 Ns) between contigs in scaffold        422
```



### Genome busco scores before and after scaffolding with HiC after dovetails work. 
```
#Input
--------------------------------------------------
|Results from generic domain eukaryota_odb10      |
--------------------------------------------------
|C:97.6%[S:97.6%,D:0.0%],F:1.6%,M:0.8%,n:255      |
|249    Complete BUSCOs (C)                       |
|249    Complete and single-copy BUSCOs (S)       |
|0      Complete and duplicated BUSCOs (D)        |
|4      Fragmented BUSCOs (F)                     |
|2      Missing BUSCOs (M)                        |
|255    Total BUSCO groups searched               |
--------------------------------------------------

--------------------------------------------------
|Results from dataset hymenoptera_odb10           |
--------------------------------------------------
|C:95.8%[S:95.6%,D:0.2%],F:1.5%,M:2.7%,n:5991     |
|5744   Complete BUSCOs (C)                       |
|5730   Complete and single-copy BUSCOs (S)       |
|14     Complete and duplicated BUSCOs (D)        |
|89     Fragmented BUSCOs (F)                     |
|158    Missing BUSCOs (M)                        |
|5991   Total BUSCO groups searched               |
--------------------------------------------------


#Final
--------------------------------------------------
|Results from generic domain eukaryota_odb10      |
--------------------------------------------------
|C:97.6%[S:97.6%,D:0.0%],F:1.6%,M:0.8%,n:255      |
|249    Complete BUSCOs (C)                       |
|249    Complete and single-copy BUSCOs (S)       |
|0      Complete and duplicated BUSCOs (D)        |
|4      Fragmented BUSCOs (F)                     |
|2      Missing BUSCOs (M)                        |
|255    Total BUSCO groups searched               |
--------------------------------------------------

--------------------------------------------------
|Results from dataset hymenoptera_odb10           |
--------------------------------------------------
|C:96.0%[S:95.8%,D:0.2%],F:1.3%,M:2.7%,n:5991     |
|5752   Complete BUSCOs (C)                       |
|5738   Complete and single-copy BUSCOs (S)       |
|14     Complete and duplicated BUSCOs (D)        |
|80     Fragmented BUSCOs (F)                     |
|159    Missing BUSCOs (M)                        |
|5991   Total BUSCO groups searched               |
--------------------------------------------------
```