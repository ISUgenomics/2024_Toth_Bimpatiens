# Add functional annotation information to gff for B impatiens

# Add functional annotations to gff

### Swissprot
```
/work/gif3/masonbrink/Toth/02_Bimpatiens/07_RenameFinalize/01_Uniprot

 ln -s ../Bimp_proteins.fasta
 sed 's/\.//g' Bimp_proteins.fasta >NoStopBimp_proteins.fasta
for f in NoStopBimp_proteins.fasta; do echo "ml  diamond/2.0.15-4c4o2ux; ml diamond-DB; diamond blastp --query  "$f"  -d /work/LAS/BioDatabase/diamondDB/swissprot/current/swissprot  --outfmt 6 qseqid sseqid pident length mismatch gapopen qstart qend sstart send evalue bitscore stitle --strand both --query-cover .5 --id 0.5 >"${f%.*}".diamondout";done >DiamondUP.sh

#compile best hit annotations
cat Bimp_proteins.diamondout |grep -v -i -e "hypothetical" -e "unnamed" -e "uncharacterized" |sort -k1,1V -u >DefinedSPGenes.tab
cat DefinedSPGenes.tab <(sort -u -k1,1V Bimp_proteins.diamondout ) |sort -k1,1V -u |cut -f 1,13 | sed  's/ RecName: Full=/ /g' |sed  's/;.*\[/\ [/g' >AllSPAnnotations.tab

sed -i 's/\t/\tSPBLAST_/1' AllSPAnnotations.tab

```

### NR
```
/work/gif3/masonbrink/Toth/02_Bimpatiens/07_RenameFinalize/02_NR

 ln -s ../Bimp_proteins.fasta
 sed 's/\.//g' Bimp_proteins.fasta >NoStopBimp_proteins.fasta
 for f in NoStopBimp_proteins.fasta; do echo "ml diamond/2.0.15-4c4o2ux; ml diamond-DB; diamond blastp --query "$f"   -d /work/LAS/BioDatabase/diamondDB/nr/current/nr  --outfmt 6 qseqid sseqid pident length mismatch gapopen qstart qend sstart send evalue bitscore stitle --strand both --query-cover .5 --id 0.5  >"${f%.*}"NRblast";done >NRblast.sh

#compile best hit annotations
cat Bimp_ProteinsNRblast |grep -v -i -e "hypothetical" -e "unnamed" -e "uncharacterized" |sort -k1,1V -u >DefinedNRGenes.tab
cat DefinedNRGenes.tab <(sort -u -k1,1V Bimp_ProteinsNRblast ) |sort -k1,1V -u |cut -f 1,13 | sed  's/ RecName: Full=/ /g' |sed  's/;.*\[/\ [/g' >AllNRAnnotations.tab

sed -i 's/\t/\tNRBLAST_/1' AllNRAnnotations.tab
```


##### Combine Annotations
```
/work/gif3/masonbrink/Toth/02_Bimpatiens/07_RenameFinalize

 awk -F"\t" '{arr[$1]=arr[$1] ";" $2}END{for(i in arr)print i,arr[i]}' 02_NR/AllNRAnnotations.tab 01_Uniprot/AllSPAnnotations.tab |sed 's/;//1' |sed 's/;/#/g' |sed 's/ /\t/1' >CombinedNRandSPAnnotations.tab

```



### Generate statistics on genes and annotations
```
/work/gif3/masonbrink/Toth/02_Bimpatiens/07_RenameFinalize

#how many mRNAs are there in the genome?
grep -c ">" Bimp_proteins.fasta
35653


#total number of annotations obtained.
wc CombinedNRandSPAnnotations.tab
  33412  438536 4953386 CombinedNRandSPAnnotations.tab


#what percentages of the proteins have a functional annotation?
33412/35653 = 93.7%

#how many transposon associated transcripts?
 grep -i -e "transposase" -e "transcriptase" -e "retroelement" -e "helitron" -e "reverse" -e "helitron" -e "transposon"  CombinedNRandSPAnnotations.tab |wc
    204    2426   29118

```


### Attach functional annotations to mRNA in gff3s
```
awk -F"\t" '{arr[$1]=arr[$1] ";" $2}END{for(i in arr)print i,arr[i]}'  <(less BimpGeneAnnotation.gff3 |awk 'substr($1,1,1)!="#"' |awk '$3=="mRNA"' |sed 's/;/\t/1'  |sed 's/ID=/ID=\t/g' |sed 's/;/;\t/1' |awk '{print $10"\t"$1,$2,$3,$4,$5,$6,$7,$8,$9,$10,$11}' |sed 's/ //9' |sed 's/ /;/9'  ) CombinedNRandSPAnnotations.tab |sed 's/ ;H/\tH/g' |cut -f 2- |sed 's/;;/;Note=/g' |sed 's/ /\t/1'|sed 's/ /\t/1'|sed 's/ /\t/1'|sed 's/ /\t/1'|sed 's/ /\t/1'|sed 's/ /\t/1'|sed 's/ /\t/1'|sed 's/ /\t/1' >mRNAsAnnotated.gff3


less mRNAsAnnotated.gff3 |cut -f 9 |sed 's/ID=//g' |sed 's/Parent=//g' |sed 's/;/\t/1' |sed 's/;/\t/1'|sort -k3,3Vr |sort -k2,2 -u |sed '/^$/d' |cut -f 2-  >Gene2Annotation.list

awk -F"\t" '{arr[$1]=arr[$1] ";" $2}END{for(i in arr)print i,arr[i]}'  <(less BimpGeneAnnotation.gff3 |awk 'substr($1,1,1)!="#"' |awk '$3=="gene"'  |sed 's/;/\t/1' |sed 's/ID=/ID=\t/g'| cut -f 1-10 |awk '{print $10"\t"$1,$2,$3,$4,$5,$6,$7,$8,$9,$10";"}' |sed 's/ //9'  )  Gene2Annotation.list  |sed 's/ ;/\t/1' |cut -f 2- |sed 's/ /\t/1'|sed 's/ /\t/1'|sed 's/ /\t/1'|sed 's/ /\t/1'|sed 's/ /\t/1'|sed 's/ /\t/1'|sed 's/ /\t/1'|sed 's/ /\t/1' |sed 's/;;/;/g' >genesAnnotated.gff3

awk '$3!="gene" && $3!="mRNA" && substr($1,1,1)!="#"' BimpGeneAnnotation.gff3 >OtherFeatures.gff3
cat OtherFeatures.gff3 genesAnnotated.gff3 mRNAsAnnotated.gff3  >UnorderedMerge.gff3
gff3sort.pl --precise --chr_order natural UnorderedMerge.gff3  >SortedFunctionalAnnotationBimpatiens.gff3
ml tabix
bgzip SortedFunctionalAnnotationBimpatiens.gff3
tabix -p gff SortedFunctionalAnnotationBimpatiens.gff3.gz

```
