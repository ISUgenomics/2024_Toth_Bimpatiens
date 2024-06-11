# Reannotate Bombus impatiens with new RNAseq reads using braker

### Download RNAseq reads from NCBI SRA with at least 150bp in length
```
SRR15927739 - SRR15927792
SRR26132658 - SRR26132679
SRR19756248 - SRR19756304


# Could only download 6 at a time without filling up the download folder to max.   

less sra.list |while read line; do echo "/work/LAS/BioDatabase/tools/micromamba/envs/tools/bin/fasterq-dump --split-files  "$line" &";done  | sed '0~6 s/&$/\nwait/g' >DownloadSRA.sh
```

### fastqc and multiqc 
```
ml fastqc; ml parallel; parallel "fastqc --nogroup {}" ::: *.fastq.gz

ml miniconda3;source activate multiqc
multiqc -p .

sh  ~/common_scripts/qcMarkdownGenerator.sh
```


### Align RNAseq with star
```
/work/gif3/masonbrink/Toth/02_Bimpatiens/03_STARAlignment

ln -s /work/gif4/gif/remkv6/Toth/03_Busco/BimpatiensGenome.FINAL.fasta
ml star;STAR  --runMode genomeGenerate --genomeDir /work/gif3/masonbrink/Toth/02_Bimpatiens/03_STARAlignment/ --genomeFastaFiles BimpatiensGenome.FINAL.fasta  --genomeSAindexNbases 12

for f in ../01_data/*fastq; do ln -s $f;done

for f in *_1.fastq; do echo "ml star;STAR --runMode alignReads --outSAMtype BAM SortedByCoordinate  --genomeDir /work/gif/remkv6/Toth/04_Annotation/01_Bimpatiens/02_AlignStar/ --outFileNamePrefix  "${f%*_1.fastq}" --readFilesIn  "$f" "${f%*_1.fastq}"_2.fastq";done >AlignStar.sh
```


Results of Star alignment
```
grep "Uniquely mapped reads %" *final.out

SRR15927739Log.final.out:                        Uniquely mapped reads % |      92.09%
SRR15927740Log.final.out:                        Uniquely mapped reads % |      91.42%
SRR15927741Log.final.out:                        Uniquely mapped reads % |      92.09%
SRR15927742Log.final.out:                        Uniquely mapped reads % |      91.89%
SRR15927743Log.final.out:                        Uniquely mapped reads % |      91.01%
SRR15927744Log.final.out:                        Uniquely mapped reads % |      91.01%
SRR15927745Log.final.out:                        Uniquely mapped reads % |      91.30%
SRR15927746Log.final.out:                        Uniquely mapped reads % |      91.85%
SRR15927747Log.final.out:                        Uniquely mapped reads % |      91.80%
SRR15927748Log.final.out:                        Uniquely mapped reads % |      92.33%
SRR15927749Log.final.out:                        Uniquely mapped reads % |      91.39%
SRR15927750Log.final.out:                        Uniquely mapped reads % |      91.82%
SRR15927751Log.final.out:                        Uniquely mapped reads % |      91.81%
SRR15927752Log.final.out:                        Uniquely mapped reads % |      92.33%
SRR15927753Log.final.out:                        Uniquely mapped reads % |      92.03%
SRR15927754Log.final.out:                        Uniquely mapped reads % |      91.30%
SRR15927755Log.final.out:                        Uniquely mapped reads % |      91.56%
SRR15927756Log.final.out:                        Uniquely mapped reads % |      91.74%
SRR15927757Log.final.out:                        Uniquely mapped reads % |      92.19%
SRR15927758Log.final.out:                        Uniquely mapped reads % |      92.28%
SRR15927759Log.final.out:                        Uniquely mapped reads % |      90.73%
SRR15927760Log.final.out:                        Uniquely mapped reads % |      92.04%
SRR15927761Log.final.out:                        Uniquely mapped reads % |      90.59%
SRR15927762Log.final.out:                        Uniquely mapped reads % |      92.26%
SRR15927763Log.final.out:                        Uniquely mapped reads % |      90.97%
SRR15927764Log.final.out:                        Uniquely mapped reads % |      91.15%
SRR15927765Log.final.out:                        Uniquely mapped reads % |      90.57%
SRR15927766Log.final.out:                        Uniquely mapped reads % |      91.55%
SRR15927767Log.final.out:                        Uniquely mapped reads % |      91.84%
SRR15927768Log.final.out:                        Uniquely mapped reads % |      92.41%
SRR15927769Log.final.out:                        Uniquely mapped reads % |      92.13%
SRR15927770Log.final.out:                        Uniquely mapped reads % |      82.09%
SRR15927771Log.final.out:                        Uniquely mapped reads % |      91.33%
SRR15927772Log.final.out:                        Uniquely mapped reads % |      91.69%
SRR15927773Log.final.out:                        Uniquely mapped reads % |      91.74%
SRR15927774Log.final.out:                        Uniquely mapped reads % |      91.86%
SRR15927775Log.final.out:                        Uniquely mapped reads % |      91.22%
SRR15927776Log.final.out:                        Uniquely mapped reads % |      84.48%
SRR15927777Log.final.out:                        Uniquely mapped reads % |      91.54%
SRR15927778Log.final.out:                        Uniquely mapped reads % |      92.36%
SRR15927779Log.final.out:                        Uniquely mapped reads % |      92.13%
SRR15927780Log.final.out:                        Uniquely mapped reads % |      92.34%
SRR15927781Log.final.out:                        Uniquely mapped reads % |      92.25%
SRR15927782Log.final.out:                        Uniquely mapped reads % |      91.90%
SRR15927783Log.final.out:                        Uniquely mapped reads % |      92.32%
SRR15927784Log.final.out:                        Uniquely mapped reads % |      91.35%
SRR15927785Log.final.out:                        Uniquely mapped reads % |      90.66%
SRR15927786Log.final.out:                        Uniquely mapped reads % |      91.42%
SRR15927787Log.final.out:                        Uniquely mapped reads % |      91.73%
SRR15927788Log.final.out:                        Uniquely mapped reads % |      89.55%
SRR15927789Log.final.out:                        Uniquely mapped reads % |      91.57%
SRR15927790Log.final.out:                        Uniquely mapped reads % |      92.49%
SRR15927791Log.final.out:                        Uniquely mapped reads % |      91.63%
SRR15927792Log.final.out:                        Uniquely mapped reads % |      89.14%
SRR19756248Log.final.out:                        Uniquely mapped reads % |      87.72%
SRR19756249Log.final.out:                        Uniquely mapped reads % |      86.63%
SRR19756250Log.final.out:                        Uniquely mapped reads % |      87.39%
SRR19756251Log.final.out:                        Uniquely mapped reads % |      82.28%
SRR19756252Log.final.out:                        Uniquely mapped reads % |      89.98%
SRR19756253Log.final.out:                        Uniquely mapped reads % |      74.94%
SRR19756254Log.final.out:                        Uniquely mapped reads % |      93.26%
SRR19756255Log.final.out:                        Uniquely mapped reads % |      91.32%
SRR19756256Log.final.out:                        Uniquely mapped reads % |      90.35%
SRR19756257Log.final.out:                        Uniquely mapped reads % |      92.93%
SRR19756258Log.final.out:                        Uniquely mapped reads % |      85.73%
SRR19756259Log.final.out:                        Uniquely mapped reads % |      91.11%
SRR19756260Log.final.out:                        Uniquely mapped reads % |      88.30%
SRR19756261Log.final.out:                        Uniquely mapped reads % |      74.88%
SRR19756262Log.final.out:                        Uniquely mapped reads % |      85.54%
SRR19756263Log.final.out:                        Uniquely mapped reads % |      84.64%
SRR19756264Log.final.out:                        Uniquely mapped reads % |      50.87%
SRR19756265Log.final.out:                        Uniquely mapped reads % |      84.87%
SRR19756266Log.final.out:                        Uniquely mapped reads % |      93.30%
SRR19756267Log.final.out:                        Uniquely mapped reads % |      85.90%
SRR19756268Log.final.out:                        Uniquely mapped reads % |      92.25%
SRR19756269Log.final.out:                        Uniquely mapped reads % |      89.68%
SRR19756270Log.final.out:                        Uniquely mapped reads % |      92.96%
SRR19756271Log.final.out:                        Uniquely mapped reads % |      85.39%
SRR19756272Log.final.out:                        Uniquely mapped reads % |      91.82%
SRR19756273Log.final.out:                        Uniquely mapped reads % |      92.44%
SRR19756274Log.final.out:                        Uniquely mapped reads % |      87.15%
SRR19756275Log.final.out:                        Uniquely mapped reads % |      79.31%
SRR19756276Log.final.out:                        Uniquely mapped reads % |      90.43%
SRR19756277Log.final.out:                        Uniquely mapped reads % |      86.98%
SRR19756278Log.final.out:                        Uniquely mapped reads % |      80.03%
SRR19756279Log.final.out:                        Uniquely mapped reads % |      91.12%
SRR19756280Log.final.out:                        Uniquely mapped reads % |      83.14%
SRR19756281Log.final.out:                        Uniquely mapped reads % |      89.92%
SRR19756282Log.final.out:                        Uniquely mapped reads % |      87.67%
SRR19756283Log.final.out:                        Uniquely mapped reads % |      93.15%
SRR19756284Log.final.out:                        Uniquely mapped reads % |      91.29%
SRR19756285Log.final.out:                        Uniquely mapped reads % |      87.77%
SRR19756286Log.final.out:                        Uniquely mapped reads % |      92.81%
SRR19756287Log.final.out:                        Uniquely mapped reads % |      91.58%
SRR19756288Log.final.out:                        Uniquely mapped reads % |      80.08%
SRR19756289Log.final.out:                        Uniquely mapped reads % |      87.79%
SRR19756290Log.final.out:                        Uniquely mapped reads % |      92.52%
SRR19756291Log.final.out:                        Uniquely mapped reads % |      91.95%
SRR19756292Log.final.out:                        Uniquely mapped reads % |      84.14%
SRR19756293Log.final.out:                        Uniquely mapped reads % |      48.69%
SRR19756294Log.final.out:                        Uniquely mapped reads % |      91.28%
SRR19756295Log.final.out:                        Uniquely mapped reads % |      85.48%
SRR19756296Log.final.out:                        Uniquely mapped reads % |      90.83%
SRR19756297Log.final.out:                        Uniquely mapped reads % |      91.20%
SRR19756298Log.final.out:                        Uniquely mapped reads % |      85.82%
SRR19756299Log.final.out:                        Uniquely mapped reads % |      92.65%
SRR19756300Log.final.out:                        Uniquely mapped reads % |      90.50%
SRR19756302Log.final.out:                        Uniquely mapped reads % |      89.22%
SRR19756303Log.final.out:                        Uniquely mapped reads % |      83.24%
SRR19756304Log.final.out:                        Uniquely mapped reads % |      89.20%
SRR26132660Log.final.out:                        Uniquely mapped reads % |      92.30%
SRR26132668Log.final.out:                        Uniquely mapped reads % |      93.23%
SRR26132675Log.final.out:                        Uniquely mapped reads % |      89.82%
SRR26132676Log.final.out:                        Uniquely mapped reads % |      91.98%
SRR26132679Log.final.out:                        Uniquely mapped reads % |      92.74%
```

This set of reads timed out running and were not used for annotation SRR19756301.




### Merge RNAseq bam files for braker
```
ls -lrth *out.bam |awk '$5!="0"{print $9}' >bam.list
ml samtools; samtools merge -@ 24  -b bam.list MergedRNA.bam ;samtools sort -o MergedRNA_sorted.bam -T TEMP  --threads 24   MergedRNA.bam

# run filter to get rid of low quality mapping, unmapped reads, and non-primary alignments
ml samtools; samtools index MergedRNA_sorted.bam ;samtools view -b -F 260  -q 20 MergedRNA_sorted.bam > HQMergedRNA_sorted.bam; samtools index HQMergedRNA_sorted.bam

```




### Braker
```
cd /work/gif3/masonbrink/Toth/02_Bimpatiens/04_Braker

ln -s /work/gif4/gif/remkv6/Toth/04_Annotation/01_Bimpatiens/01_Repeatmodeler/SoftmaskedBimpatiensGenome.FINAL.fasta
cp -rf /work/gif4/gif/remkv6/Toth/04_Annotation/02_Pdominula/03_Braker/Augustus/ .
cp -rf /work/gif4/gif/remkv6/Toth/04_Annotation/02_Pdominula/03_Braker/bin .
ln -s ../03_STARAlignment/HQMergedRNA_sorted.bam
ln -s ../03_STARAlignment/HQMergedRNA_sorted.bam.bai

# braker3 command
ml samtools; ml bamtools;ml genemark-et/4.38-63ipkx4;ml augustus/3.3.2-py3-openmpi3-gee2bjt; ml braker3;braker.pl --genome=SoftmaskedBimpatiensGenome.FINAL.fasta --softmasking --species=BUSCO_BimpatiensGenome.Busco --bam=HQMergedRNA_sorted.bam  --AUGUSTUS_CONFIG_PATH=/work/gif3/masonbrink/Toth/02_Bimpatiens/04_Braker/Augustus/config/ --overwrite --useexisting --gc_probability= 0.007


## braker2 command, testing braker3
ml samtools; ml bamtools;ml genemark-et/4.38-63ipkx4;ml augustus/3.3.2-py3-openmpi3-gee2bjt; ml braker/2.1.2-py3-openmpi3-75wblif;braker.pl --genome=SoftmaskedBimpatiensGenome.FINAL.fasta --softmasking --species=BUSCO_BimpatiensGenome.Busco --bam=HQMergedRNA_sorted.bam  --AUGUSTUS_CONFIG_PATH=/work/gif3/masonbrink/Toth/02_Bimpatiens/04_Braker/Augustus/config/ --overwrite --useexisting

# convert gtf to gff3
ml augustus/3.4.0-py310-tcknerw
 /opt/rit/el9/20230413/app/linux-rhel9-x86_64_v3/gcc-11.2.1/augustus-3.4.0-tcknerwejf7sfdkp43i4sfr5zug5ku2g/scripts/gtf2gff.pl <braker.gtf --out braker.gff3
```
Results
```
        --------------------------------------------------
        |Results from dataset eukaryota_odb10             |
        --------------------------------------------------
        |C:96.1%[S:70.6%,D:25.5%],F:2.4%,M:1.5%,n:255     |
        |245    Complete BUSCOs (C)                       |
        |180    Complete and single-copy BUSCOs (S)       |
        |65     Complete and duplicated BUSCOs (D)        |
        |6      Fragmented BUSCOs (F)                     |
        |4      Missing BUSCOs (M)                        |
        |255    Total BUSCO groups searched               |
        --------------------------------------------------
        --------------------------------------------------
        |Results from dataset metazoa_odb10               |
        --------------------------------------------------
        |C:94.7%[S:64.5%,D:30.2%],F:2.1%,M:3.2%,n:954     |
        |903    Complete BUSCOs (C)                       |
        |615    Complete and single-copy BUSCOs (S)       |
        |288    Complete and duplicated BUSCOs (D)        |
        |20     Fragmented BUSCOs (F)                     |
        |31     Missing BUSCOs (M)                        |
        |954    Total BUSCO groups searched               |
        --------------------------------------------------
       --------------------------------------------------
        |Results from dataset hymenoptera_odb10           |
        --------------------------------------------------
        |C:92.0%[S:55.8%,D:36.2%],F:3.1%,M:4.9%,n:5991    |
        |5512   Complete BUSCOs (C)                       |
        |3345   Complete and single-copy BUSCOs (S)       |
        |2167   Complete and duplicated BUSCOs (D)        |
        |184    Fragmented BUSCOs (F)                     |
        |295    Missing BUSCOs (M)                        |
        |5991   Total BUSCO groups searched               |
        --------------------------------------------------

awk '$3=="gene"' braker.gff3 |awk 'substr($1,1,5)=="HiC_s"' |awk '{if($5>$4) {print $5-$4} else {print $4-$5}}' |summary.sh
Total:  127,172,056
Count:  17,129
Mean:   7,424
Median: 2,210
Min:    200
Max:    402,600
awk '$3=="transcript"' braker.gff3 |awk 'substr($1,1,5)=="HiC_s"' |awk '{if($5>$4) {print $5-$4} else {print $4-$5}}' |summary.sh
Total:  198,850,912
Count:  23,745
Mean:   8,374
Median: 2,572
Min:    200
Max:    402,600
awk '$3=="CDS"' braker.gff3 |awk 'substr($1,1,5)=="HiC_s"' |awk '{if($5>$4) {print $5-$4} else {print $4-$5}}' |summary.sh
Total:  34,137,495
Count:  155,832
Mean:   219
Median: 164
Min:    2
Max:    9,214


```
### Braker with modified Augustus parameters for less extrapolation
```
/work/gif3/masonbrink/Toth/02_Bimpatiens/05_brakerAugustusMan

ln -s /work/gif4/gif/remkv6/Toth/04_Annotation/01_Bimpatiens/01_Repeatmodeler/SoftmaskedBimpatiensGenome.FINAL.fasta
cp -rf /work/gif4/gif/remkv6/Toth/04_Annotation/02_Pdominula/03_Braker/Augustus/ .
cp -rf /work/gif4/gif/remkv6/Toth/04_Annotation/02_Pdominula/03_Braker/bin .
ln -s ../03_STARAlignment/HQMergedRNA_sorted.bam
ln -s ../03_STARAlignment/HQMergedRNA_sorted.bam.bai

ml bamtools;ml braker3;braker.pl --genome=SoftmaskedBimpatiensGenome.FINAL.fasta --softmasking --species= B.impatiens1 --bam=HQMergedRNA_sorted.bam  --AUGUSTUS_CONFIG_PATH=/work/gif3/masonbrink/Toth/02_Bimpatiens/05_brakerAugustusMan/Augustus/config --overwrite --gff3 --threads 35 --nocleanup --extrinsic Extrinsic_weights_for_Bimpatiens_gene_prediction_exons_ex1.cfg,Extrinsic_weights_for_B.impatiens_gene_prediction_utrs_ex1_utr.cfg --gc_probability= 0.007


   exonpart      10  .995 1  M    1  1e+100  RM  1     1    E 1    1    W 1    1.02     P       1       1
       exon      100        1  M    1  1e+100  RM  1     1    E 1    1    W 1    1      P       1     1e4
 intronpart      10          1  M    1  1e+100  RM  1     1    E 1    1    W 1    1     P       1       1
     intron      1000        1  M    1  1e+100  RM  1     1    E 1  1e6    W 1    1     P       1     100
    CDSpart      10     1 1  M    1  1e+100  RM  1     1    E 1    1      W 1    1      P       1     1e5
        CDS      100        1  M    1  1e+100  RM  1     1    E 1    1    W 1    1      P       1       1

# convert gtf to gff3
ml augustus/3.4.0-py310-tcknerw
 /opt/rit/el9/20230413/app/linux-rhel9-x86_64_v3/gcc-11.2.1/augustus-3.4.0-tcknerwejf7sfdkp43i4sfr5zug5ku2g/scripts/gtf2gff.pl <braker.gtf --out braker.gff3



        --------------------------------------------------
        |Results from dataset eukaryota_odb10             |
        --------------------------------------------------
        |C:94.9%[S:73.7%,D:21.2%],F:3.1%,M:2.0%,n:255     |
        |242    Complete BUSCOs (C)                       |
        |188    Complete and single-copy BUSCOs (S)       |
        |54     Complete and duplicated BUSCOs (D)        |
        |8      Fragmented BUSCOs (F)                     |
        |5      Missing BUSCOs (M)                        |
        |255    Total BUSCO groups searched               |
        --------------------------------------------------
        --------------------------------------------------
        |Results from dataset metazoa_odb10               |
        --------------------------------------------------
        |C:94.9%[S:70.6%,D:24.3%],F:2.4%,M:2.7%,n:954     |
        |906    Complete BUSCOs (C)                       |
        |674    Complete and single-copy BUSCOs (S)       |
        |232    Complete and duplicated BUSCOs (D)        |
        |23     Fragmented BUSCOs (F)                     |
        |25     Missing BUSCOs (M)                        |
        |954    Total BUSCO groups searched               |
        --------------------------------------------------
        --------------------------------------------------
        |Results from dataset hymenoptera_odb10           |
        --------------------------------------------------
        |C:88.5%[S:57.2%,D:31.3%],F:5.7%,M:5.8%,n:5991    |
        |5306   Complete BUSCOs (C)                       |
        |3429   Complete and single-copy BUSCOs (S)       |
        |1877   Complete and duplicated BUSCOs (D)        |
        |340    Fragmented BUSCOs (F)                     |
        |345    Missing BUSCOs (M)                        |
        |5991   Total BUSCO groups searched               |
        --------------------------------------------------

awk '$3=="gene"' braker.gff3 |awk 'substr($1,1,5)=="HiC_s"' |awk '{if($5>$4) {print $5-$4} else {print $4-$5}}' |summary.sh
Total:  106,414,721
Count:  14,480
Mean:   7,349
Median: 1,995
Min:    200
Max:    433,285
awk '$3=="transcript"' braker.gff3 |awk 'substr($1,1,5)=="HiC_s"' |awk '{if($5>$4) {print $5-$4} else {print $4-$5}}' |summary.sh
Total:  174,597,969
Count:  20,958
Mean:   8,330
Median: 2,354
Min:    200
Max:    433,285
awk '$3=="CDS"' braker.gff3 |awk 'substr($1,1,5)=="HiC_s"' |awk '{if($5>$4) {print $5-$4} else {print $4-$5}}' |summary.sh
Total:  30,235,860
Count:  134,215
Mean:   225
Median: 165
Min:    2
Max:    9,818


```


Old Annotations

Published Honeybee reference stats
```
Gene count  12,374


Mean  Median  Min Max
Genes	12,332	15,014	3,614	62	869,734
All_transcripts	28,117	3,508	2,519	19	76,350
mRNA	23,458	3,696	2,658	105	76,350
CDSs	23,471	2,446	1,614	78	75,615
Exons	100,203	401	213	2	22,508

https://www.ncbi.nlm.nih.gov/genome/annotation_euk/Apis_mellifera/104/
```


B impatiens braker annotation stats from 100bp PE reads aligned with HiSat2
```
/work/gif/remkv6/Toth/04_Annotation/01_Bimpatiens/05_AlignHisat/01_braker/braker/BUSCO_BimpatiensGenome.Busco

awk '$3=="gene"' augustus.hints.gff |awk 'substr($1,1,5)=="HiC_s"' |awk '{if($5>$4) {print $5-$4} else {print $4-$5}}' |summary.sh
Total:  116,258,852
Count:  17,208
Mean:   6,756
Median: 2,112
Min:    200
Max:    404,864

awk '$3=="transcript"' augustus.hints.gff |awk 'substr($1,1,5)=="HiC_s"' |awk '{if($5>$4) {print $5-$4} else {print $4-$5}}' |summary.sh
Total:  159,919,211
Count:  19,839
Mean:   8,060
Median: 2,475
Min:    200
Max:    404,864

awk '$3=="CDS"' augustus.hints.gff |awk 'substr($1,1,5)=="HiC_s"' |awk '{if($5>$4) {print $5-$4} else {print $4-$5}}' |summary.sh
Total:  27,348,700
Count:  123,003
Mean:   222
Median: 165
Min:    2
Max:    9,818


Hmm. smaller sized and more genes/exons/transcripts than the honeybee assembly. I bet this is due to the 100bp pe reads.
```

### Create bigwigs of splices to evaluate annotation quality

```
# Isolate only reads that are spliced. 
ml samtools ;samtools view -h HQMergedRNA_sorted.bam |awk '\$0 ~ /^@/ || \$6 ~ /N/' | samtools view -b > HQMergedRNASplices_sorted.bam
# create bigwig
ml miniconda3;source activate deeptools; bamCoverage -b HQMergedRNASplices_sorted.bam -bs 1 -p 36 -of bigwig -o HQMergedRNAplices_sorted.bw

```