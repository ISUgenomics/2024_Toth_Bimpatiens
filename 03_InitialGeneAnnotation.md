
# Annotate the Bombus impatiens genome!

### Download RNAseq
```
/work/gif/remkv6/Toth/04_Annotation/01_Bimpatiens
```
List of 52 RNAseq samples
```
vi SRRnumbers.txt

SRX8430784
...
SRX8430835
```

Download the files
```
for f in SRX*; do echo "fastq-dump --split-files --origfmt --gzip "$f" &";done >fastqdump.sh
sh fastqdump.sh
```

### Run repeatmodeler and repeatmasker
```
/work/gif/remkv6/Toth/04_Annotation/01_Bimpatiens/01_Repeatmodeler
ln -s /work/gif/remkv6/Toth/02_Juicer/01_Bombus_impatiens/01_Juicer/3ddna/04_3d-dnaDiploidRepCovFinalize/BimpatiensGenome.FINAL.fasta

ml miniconda3; source activate repeatmodeler2;
BuildDatabase -name impatiens BimpatiensGenome.FINAL.fasta
ml miniconda3; source activate repeatmodeler2; RepeatModeler -database impatiens -pa 36

ln -s RM_31387.ThuSep91628542021/consensi.fa

ml miniconda3; source activate repeatmasker; RepeatMasker -pa 36 -norna  -dir RepeatmaskerOut -small -gff -lib consensi.fa BimpatiensGenome.FINAL.fasta
```
### Align the RNA


##### Align with Star
```
/work/gif/remkv6/Toth/04_Annotation/01_Bimpatiens/02_AlignStar

ln -s /work/gif/remkv6/Toth/02_Juicer/01_Bombus_impatiens/01_Juicer/3ddna/04_3d-dnaDiploidRepCovFinalize/BimpatiensGenome.FINAL.fasta

for f in ../SRX*;do ln -s $f;done
```
Create the genome database
```
ml star;STAR  --runMode genomeGenerate --genomeDir /work/gif/remkv6/Toth/04_Annotation/01_Bimpatiens/02_AlignStar/ --genomeFastaFiles BimpatiensGenome.FINAL.fasta 

for f in *_1.fastq.gz; do echo "ml star;STAR --runMode alignReads --outSAMtype BAM SortedByCoordinate --readFilesCommand zcat --genomeDir /work/gif/remkv6/Toth/04_Annotation/01_Bimpatiens/02_AlignStar/ --outFileNamePrefix  "${f%*_1.fastq.gz}" --readFilesIn  "$f" "${f%*_1.fastq.gz}"_2.fastq.gz";done >AlignStar.sh
```
Unique alignment rates were pretty low, as most reads were considered too short.  

##### Align with Hisat2

```
/work/gif/remkv6/Toth/04_Annotation/01_Bimpatiens/05_AlignHisat

#softlink genome and reads
ln -s ../BimpatiensGenome.FINAL.fasta
for f in ../*fastq.gz; do ln -s  $f;done

#build genome database
ml hisat2; hisat2-build BimpatiensGenome.FINAL.fasta BimpatiensGenome.FINAL

paste <(ls -1 *_1.fastq.gz )  <(ls -1 *_2.fastq.gz ) |tr "\t" " " |while read line; do echo "sh runPilon.sh BimpatiensGenome.FINAL.fasta "$line; done >BIHISAT.sh
```

runPilon.sh (alignment and sorting only)
```
#runPilon.sh
###############################################################################
#!/bin/bash
GENOME="$1"
R1_FQ="$2"
R2_FQ="$3"

module load hisat2
#hisat2-build ${GENOME} ${GENOME%.*}
hisat2 -p 36 -x ${GENOME%.*} -1 $R1_FQ -2 $R2_FQ -S ${R1_FQ%.*}.sam
module load samtools
samtools view --threads 32 -b -o ${GENOME%.*}.${R1_FQ%.*}.bam ${R1_FQ%.*}.sam
mkdir TEMP.${R1_FQ%.*}
samtools sort -m 3G -o ${GENOME%.*}.${R1_FQ%.*}_sorted.bam -T TEMP.${R1_FQ%.*} --threads 12 ${GENOME%.*}.${R1_FQ%.*}.bam
samtools index ${GENOME%.*}.${R1_FQ%.*}_sorted.bam
samtools flagstat ${GENOME%.*}.${R1_FQ%.*}_sorted.bam
```

Merge the bams
```
ls -lrth *_sorted.bam |awk '$5!="0"{print $9}' >bam.list
mkdir TEMP
echo "ml samtools; samtools merge -@ 24  -b bam.list MergedRNA.bam ;samtools sort -o MergedRNA_sorted.bam -T TEMP  --threads 24   MergedRNA.bam " >samMergeBams.sh
```

### Softmask the genome
```
ml bedtools2;
bedtools maskfasta -fi BimpatiensGenome.FINAL.fasta -fo SoftmaskedBimpatiensGenome.FINAL.fasta -bed RepeatmaskerOut/BimpatiensGenome.FINAL.fasta.out.gff -soft

```

### Braker
```
cd /work/gif/remkv6/Toth/04_Annotation/01_Bimpatiens/05_AlignHisat/01_braker

ln -s ../../../02_Pdominula/03_Braker/Augustus/
ln -s ../../01_Repeatmodeler/SoftmaskedBimpatiensGenome.FINAL.fasta
ln -s ../MergedRNA.bam

cd /work/gif/remkv6/Toth/04_Annotation/02_Pdominula/03_Braker/Augustus/config/species
cp -rf  /work/gif/remkv6/Toth/03_Busco/BimpatiensGenome.Busco/run_hymenoptera_odb10/augustus_output/retraining_parameters/BUSCO_BimpatiensGenome.Busco .
cd -

ml samtools; ml bamtools;ml genemark-et/4.38-63ipkx4;ml augustus/3.3.2-py3-openmpi3-gee2bjt; ml braker/2.1.2-py3-openmpi3-75wblif;braker.pl --genome=SoftmaskedBimpatiensGenome.FINAL.fasta --softmasking --species=BUSCO_BimpatiensGenome.Busco --bam=MergedRNA_sorted.bam  --AUGUSTUS_CONFIG_PATH=/work/gif/remkv6/Toth/04_Annotation/02_Pdominula/03_Braker/Augustus/config/ --overwrite --useexisting

```

### Results

Honeybee reference stats
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


B impatiens braker annotation stats
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

B. impatiens braker annotation busco protein scores
```
/work/gif/remkv6/Toth/04_Annotation/01_Bimpatiens/05_AlignHisat/01_braker/braker
ml miniconda3; source activate busco5_env ;busco -i augustus.hints.aa -o bu
scotest -m prot -l hymenoptera_odb10 -c 35 -f


ml miniconda3; source activate busco5_env ;busco -i augustus.hints.aa -o bu
scotest -m prot -l eukaryota_odb10 -c 35 -f

       --------------------------------------------------
       |Results from dataset eukaryota_odb10             |
       --------------------------------------------------
       |C:94.2%[S:87.1%,D:7.1%],F:3.9%,M:1.9%,n:255      |
       |240    Complete BUSCOs (C)                       |
       |222    Complete and single-copy BUSCOs (S)       |
       |18     Complete and duplicated BUSCOs (D)        |
       |10     Fragmented BUSCOs (F)                     |
       |5      Missing BUSCOs (M)                        |
       |255    Total BUSCO groups searched               |
       --------------------------------------------------


        --------------------------------------------------
        |Results from dataset hymenoptera_odb10           |
        --------------------------------------------------
        |C:90.0%[S:73.7%,D:16.3%],F:4.1%,M:5.9%,n:5991    |
        |5392   Complete BUSCOs (C)                       |
        |4414   Complete and single-copy BUSCOs (S)       |
        |978    Complete and duplicated BUSCOs (D)        |
        |246    Fragmented BUSCOs (F)                     |
        |353    Missing BUSCOs (M)                        |
        |5991   Total BUSCO groups searched               |
        --------------------------------------------------

```
The buscos look pretty good, but the comparisons with honeybee look pretty abysmal. Smaller and shorter genes are likley due to the 100bp PE alignments.   Some further curation is recommended.
