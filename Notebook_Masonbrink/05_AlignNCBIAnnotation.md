# Merge braker annotation with alignments of BIMP_2.2 minprot alignments

This code was how we evaluated the Bimp2.2 annotation and integrated it with the final combined braker annotation in 04_ReannotateWithNewReads.md Resulted in a marginal improvement, but a larger than biological representation of mRNAs. 

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
awk '$3=="locus"' SortedFunctionalAnnotationBimpatiens.gff3 |awk 'substr($1,1,5)=="HiC_s"' |awk '{if($5>$4) {print $5-$4} else {print $4-$5}}' |summary.sh
Total:  119,328,257
Count:  13,938
Mean:   8,561
Median: 2,126
Min:    115
Max:    435,723
awk '$3=="mRNA"' SortedFunctionalAnnotationBimpatiens.gff3 |awk 'substr($1,1,5)=="HiC_s"' |awk '{if($5>$4) {print $5-$4} else {print $4-$5}}' |summary.sh
Total:  379,412,063
Count:  35,653
Mean:   10,641
Median: 2,895
Min:    115
Max:    435,720
 awk '$3=="CDS"' SortedFunctionalAnnotationBimpatiens.gff3 |awk 'substr($1,1,5)=="HiC_s"' |awk '{if($5>$4) {print $5-$4} else {print $4-$5}}' |summary.sh
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


### Rename genes and mRNAs
```
less Bimp_NCBIAnnotationMergedBraker.gff3|sed 's/locus=/gene=/g' |cut -f 1-9 |sed 's/genes=/\t/g' |cut -f 1-9 |sed 's/geneID.*gene=/Parent=/g' |sed 's/transcripts=.*//g' |sed 's/locus/gene/g' |sed 's/gene=.*//g' |sed 's/gffcl/AUGUSTUS/g' >CleanAnnotation4Renaming.gff3


 awk '$3=="mRNA" {print $9}' CleanAnnotation4Renaming.gff3 |sed 's/ID=//g' |sed 's/;/\t/g' |awk '{print $1"\tBimpmRNA"NR}'  >mrna.map
 awk '$3=="gene" {print $9}' CleanAnnotation4Renaming.gff3 |sed 's/ID=//g' |sed 's/;/\t/g' |awk '{print $1"\tBimpGene"substr($1,5,length($1))}' >gene.map

ml maker; singularity shell "/opt/rit/singularity/images/maker/2.31.10_3.1/maker.simg"
map_gff_ids mrna.map CleanAnnotation4Renaming.gff3
map_gff_ids gene.map CleanAnnotation4Renaming.gff3

gffread -g SoftmaskedBimpatiensGenome.FINAL.fasta BimpGeneAnnotation.gff3 -x Bimp_transcripts.fasta -y Bimp_proteins.fasta

```

### Create conversion of new IDs to Bimp2.2 IDs
```
bedtools intersect -wo -a <(awk '$3=="mRNA"'  SortedFunctionalAnnotationBimpatiens.gff3) -b <(awk '$3=="mRNA"' ../06_AddNCBIAnnotations/01_Genomethreader/MergedGenomethreader.gff3 )  |cut -f 9,18 |sed 's/Target=/\t/g' |sed 's/Parent=/\t/1' |cut -f 1,4 |sed 's/ID=//g' |sed 's/;//g' |sort -k1,1 -u |sort -k1,1V |awk '{print $1,$2,$5,$8,$11,$14,$17,$21,$24,$27,$30 }' |sed 's/ +//g' >Bimp3_0toBimp2_2.tab
```



### Summary
```
The protein alignments via miniprot of BIMP2.2 achieved another increase in stats for BUSCO, and a few ~20 more fragmented genes were likley fixed. We used cufflinks gffread to combine these two annotations. Due to differences in prediction methods between Braker and NCBI's annotation pipeline, the transcript models were just enough different to not allow their duplicate mRNA to be removed. 31k mRNA models is much higher than it should be biologically, but it allows the user to choose which transcript best represents their gene of interest. The real biological number is probably closer to 21,127 mRNAs that were found in 04_ReannotateWithNewReads.md.
```