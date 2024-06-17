# Merge braker annotation with alignments of BIMP_2.2 minprot alignments


### Obtain data
```
/work/gif3/masonbrink/Toth/02_Bimpatiens/06_AddNCBIAnnotations
#Downloaded protein.fasta from here.
https://www.ncbi.nlm.nih.gov/datasets/genome/GCF_000188095.3/

```


### Miniprot alignment
```
/work/gif3/masonbrink/Toth/02_Bimpatiens/06_AddNCBIAnnotations

echo "ml miniconda3;source activate miniprot; miniprot -N 1 -t 36 -gff SoftmaskedBimpatiensGenome.FINAL.fasta protein.faa" >miniprot.sh


```

### Genomethreader
```
/work/gif3/masonbrink/Toth/02_Bimpatiens/06_AddNCBIAnnotations/01_Genomethreader
ln -s ../SoftmaskedBimpatiensGenome.FINAL.fasta
fasta-splitter.pl --n-parts 99 ../protein.faa

#since I split the protein file up, I make directories named by each protein file, change to that directory, softlink the genome and proteins, then run the gth script.
for f in *part*faa; do echo "mkdir "$f"dir; cd "$f"dir; ln -s ../SoftmaskedBimpatiensGenome.FINAL.fasta; ln -s ../"$f" ; ml miniconda3; source activate genomethreader;gth -genomic SoftmaskedBimpatiensGenome.FINAL.fasta -protein "$f" -gff3out -o "${f%.*}".aln -force -skipalignmentout";done  >gth.sh



#formatting after alignment
gt gff3 -tidy -sort */*aln >genomethreader.gff3
gt merge genomethreader.gff3 >MergedGenomethreader.gff3

 gffread  MergedGenomethreader.gff3 -g SoftmaskedBimpatiensGenome.FINAL.fasta  -o Bimp_NCBIAnnotation.gff3 -y Bimp_NCBIProteins.fasta -x Bimp_NCBITranscripts.fasta

```
Results
```
#How many proteins aligned?
grep -c ">" Bimp_NCBIProteins.fasta
22492
#Total number of input proteins
grep -c ">" ../protein.faa
24471

22492/24471 = 91.9%



        --------------------------------------------------
        |Results from dataset eukaryota_odb10             |
        --------------------------------------------------
        |C:94.5%[S:64.7%,D:29.8%],F:3.1%,M:2.4%,n:255     |
        |241    Complete BUSCOs (C)                       |
        |165    Complete and single-copy BUSCOs (S)       |
        |76     Complete and duplicated BUSCOs (D)        |
        |8      Fragmented BUSCOs (F)                     |
        |6      Missing BUSCOs (M)                        |
        |255    Total BUSCO groups searched               |
        --------------------------------------------------
        --------------------------------------------------
        |Results from dataset metazoa_odb10               |
        --------------------------------------------------
        |C:94.8%[S:63.0%,D:31.8%],F:2.9%,M:2.3%,n:954     |
        |904    Complete BUSCOs (C)                       |
        |601    Complete and single-copy BUSCOs (S)       |
        |303    Complete and duplicated BUSCOs (D)        |
        |28     Fragmented BUSCOs (F)                     |
        |22     Missing BUSCOs (M)                        |
        |954    Total BUSCO groups searched               |
        --------------------------------------------------

        --------------------------------------------------
        |Results from dataset hymenoptera_odb10           |
        --------------------------------------------------
        |C:86.7%[S:46.2%,D:40.5%],F:7.4%,M:5.9%,n:5991    |
        |5196   Complete BUSCOs (C)                       |
        |2770   Complete and single-copy BUSCOs (S)       |
        |2426   Complete and duplicated BUSCOs (D)        |
        |441    Fragmented BUSCOs (F)                     |
        |354    Missing BUSCOs (M)                        |
        |5991   Total BUSCO groups searched               |
        --------------------------------------------------

```




### Merge protein alignments with merged braker anntoation
```
gffread  ../../05_brakerAugustusMan/braker/FinalBimpAnnotation.gff3 MergedGenomethreader.gff3 -g SoftmaskedBimpatiensGenome.FINAL.fasta  -o Bimp_NCBIAnnotationMergedBraker.gff3 -y Bimp_NCBIProteinsMergedBraker.fasta -x Bimp_NCBITranscriptsMergedBraker.fasta -M
```


Results
```
awk '$3=="locus"' Bimp_NCBIAnnotationMergedBraker.gff3 |awk 'substr($1,1,5)=="HiC_s"' |awk '{if($5>$4) {print $5-$4} else {print $4-$5}}' |summary.sh
Total:  119,328,257
Count:  13,938
Mean:   8,561
Median: 2,126
Min:    115
Max:    435,723
awk '$3=="mRNA"' Bimp_NCBIAnnotationMergedBraker.gff3 |awk 'substr($1,1,5)=="HiC_s"' |awk '{if($5>$4) {print $5-$4} else {print $4-$5}}' |summary.sh
Total:  379,412,063
Count:  35,653
Mean:   10,641
Median: 2,895
Min:    115
Max:    435,720
awk '$3=="CDS"' Bimp_NCBIAnnotationMergedBraker.gff3 |awk 'substr($1,1,5)=="HiC_s"' |awk '{if($5>$4) {print $5-$4} else {print $4-$5}}' |summary.sh
Total:  58,591,639
Count:  250,596
Mean:   233
Median: 170
Min:    0
Max:    11,628


        --------------------------------------------------
        |Results from dataset eukaryota_odb10             |
        --------------------------------------------------
        |C:98.8%[S:48.2%,D:50.6%],F:0.4%,M:0.8%,n:255     |
        |252    Complete BUSCOs (C)                       |
        |123    Complete and single-copy BUSCOs (S)       |
        |129    Complete and duplicated BUSCOs (D)        |
        |1      Fragmented BUSCOs (F)                     |
        |2      Missing BUSCOs (M)                        |
        |255    Total BUSCO groups searched               |
        --------------------------------------------------

        --------------------------------------------------
        |Results from dataset metazoa_odb10               |
        --------------------------------------------------
        |C:97.8%[S:47.3%,D:50.5%],F:0.7%,M:1.5%,n:954     |
        |933    Complete BUSCOs (C)                       |
        |451    Complete and single-copy BUSCOs (S)       |
        |482    Complete and duplicated BUSCOs (D)        |
        |7      Fragmented BUSCOs (F)                     |
        |14     Missing BUSCOs (M)                        |
        |954    Total BUSCO groups searched               |
        --------------------------------------------------

        --------------------------------------------------
        |Results from dataset hymenoptera_odb10           |
        --------------------------------------------------
        |C:95.7%[S:36.6%,D:59.1%],F:2.1%,M:2.2%,n:5991    |
        |5732   Complete BUSCOs (C)                       |
        |2193   Complete and single-copy BUSCOs (S)       |
        |3539   Complete and duplicated BUSCOs (D)        |
        |124    Fragmented BUSCOs (F)                     |
        |135    Missing BUSCOs (M)                        |
        |5991   Total BUSCO groups searched               |
        --------------------------------------------------
```