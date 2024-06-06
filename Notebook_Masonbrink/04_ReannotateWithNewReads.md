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
ml samtools;samtools view -b -F 260  -q 20 MergedLongDistRNA_sorted.bam > HQMergedLongDistRNA_sorted.bam; samtools index HQMergedLongDistRNA_sorted.bam

```




### Braker
```
cd /work/gif3/masonbrink/Toth/02_Bimpatiens/04_Braker

ln -s /work/gif4/gif/remkv6/Toth/04_Annotation/01_Bimpatiens/01_Repeatmodeler/SoftmaskedBimpatiensGenome.FINAL.fasta
cp -rf /work/gif4/gif/remkv6/Toth/04_Annotation/02_Pdominula/03_Braker/Augustus/ .
cp -rf /work/gif4/gif/remkv6/Toth/04_Annotation/02_Pdominula/03_Braker/bin .




ml samtools; ml bamtools;ml genemark-et/4.38-63ipkx4;ml augustus/3.3.2-py3-openmpi3-gee2bjt; ml braker/2.1.2-py3-openmpi3-75wblif;braker.pl --genome=SoftmaskedBimpatiensGenome.FINAL.fasta --softmasking --species=BUSCO_BimpatiensGenome.Busco --bam=MergedRNA_sorted.bam  --AUGUSTUS_CONFIG_PATH=/work/gif/remkv6/Toth/04_Annotation/02_Pdominula/03_Braker/Augustus/config/ --overwrite --useexisting
```