# Assess datasets and putative pipeline

This is to extract and assess Hic fastq reads from Dovetail.  The reads were of high quality. 

```
#data location
/work/LAS/amytoth-lab/Dovetail_data_2021
```

###  Determine mapping percentages for dovetail generated mapping
```
/work/gif/remkv6/Toth/01_ExtractBams
for f in  /work/LAS/amytoth-lab/Dovetail_data_2021/*/HiRise/*.bam ; do echo "ln -s "$f;done |awk -F"/" '{print $0,$6".fastq.bam"}' >softlinkBams.sh
sh softlinkBams.sh
for f in *bam; do echo "ml samtools; samtools flagstat "$f" >"${f%.*}".flagstat";done >flatstat.sh


#grab mapping percentages and duplicates
grep -A 1 "duplicate" *flagstat
Bombus_impatiens.fastq.flagstat:22471790 + 0 duplicates
Bombus_impatiens.fastq.flagstat-177916746 + 0 mapped (99.27% : N/A)

Looks like good mapping, low numbers of duplicate reads.  
```


### Extract the fastq files from the dovetail generated mapping
```
/work/gif/remkv6/Toth/01_ExtractBams

for f in *bam ; do echo "module load picard/2.17.0-ft5qztz; java -jar /opt/rit/spack-app/linux-rhel7-x86_64/gcc-4.8.5/picard-2.17.0-ft5qztzntoymuxiqt3b6yi6uqcmgzmds/bin/picard.jar SamToFastq I="$f" FASTQ="${f%.*}"R1.fq F2="${f%.*}"R2.fq FU="${f%.*}"_unpaired.fq" ;done >picard.sh


```

### Set up Juicer Directories
```
/work/gif/remkv6/Toth/02_Juicer

ls -1 ../01_ExtractBams/*flagstat |awk -F"/" '{print $3}' |sed 's/\.fastq/\t/g' |cut -f 1 |awk '{print "mkdir 0"NR"_"$1}'
mkdir 01_Bombus_impatiens

/work/gif/remkv6/Toth/02_Juicer/01_Bombus_impatiens/01_Juicer

```
