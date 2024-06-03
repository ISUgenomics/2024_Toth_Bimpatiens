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
