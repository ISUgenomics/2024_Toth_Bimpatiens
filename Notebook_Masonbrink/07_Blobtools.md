# Run Blobtools to create snail plot
No contamination was found. 

### Map long reads
```
/work/gif3/masonbrink/Toth/02_Bimpatiens/08_Blobtools

ln -s ../10_FindBuscos/30_537121167.hifi_reads.fastq.gz
ln -s ../07_RenameFinalize/SoftmaskedBimpatiensGenome.FINAL.fasta

echo "sh runMinimapNbamSort.sh XDOVE_20201028_S64049_PL100161762-1_C01.subreads.fastq.gz SoftmaskedBimpatiensGenome.FINAL.fasta" >longreadMapping.sh

#runMinimap.sh
##############################################################################
#!/bin/bash
query=$1
target=$2
outname="${query%.*}_${target%.*}_minimap2.sam"
module load minimap2
minimap2 -x asm5 -a -t 36 $target $query > ${outname}

ml samtools;samtools view --threads 36 -b -o ${outname%.*}.bam ${outname}
samtools sort  -o ${outname%.*}_sorted.bam -T TEMP --threads 36 ${outname%.*}.bam
samtools index ${outname%.*}_sorted.bam
##############################################################################

```

### Megablast to NCBI NT
```
/work/gif3/masonbrink/Toth/02_Bimpatiens/08_Blobtools

fasta-splitter.pl --n-parts 20 SoftmaskedBimpatiensGenome.FINAL.fasta

for f in *part*fasta; do echo "sh runMegablast2nt.sh "$f;done >blasts.sh

#!/bin/bash
#wget ftp://ftp.ncbi.nlm.nih.gov/blast/db/taxdb.tar.gz
#tar -zxvf taxdb.tar.gz

#module load blast-plus
FASTA="$1"
blastn \
-task megablast \n
-query ${FASTA} \
-db  /work/LAS/BioDatabase/BLASTdb/NCBI/Archives/Current/nt \
-outfmt '6 qseqid staxids bitscore std sscinames sskingdoms stitle' \
-culling_limit 10 \
-num_threads 36 \
-evalue 1e-3 \
-out ${FASTA%.**}.vs.nt.cul10.1e3.megablast.out
```


### Blobtools
```
/work/gif/remkv6/Toth/12_Bombus_dahlbomii/14_Blobtools


module load singularity;module load blobtools2;
singularity shell /opt/rit/singularity/images/blobtools2/2.2.0/blobtools2.simg

cp -rf /work/gif3/masonbrink/Serb/08_ContigElimination/01_Blobtools/taxdump/ .
 ln -s /work/gif4/gif/remkv6/Toth/03_Busco/BimpatiensGenome.FINAL.Busco/run_hymenoptera_odb10/full_table.tsv


blobtools create --fasta SoftmaskedBimpatiensGenome.FINAL.fasta --cov XDOVE_20201028_S64049_PL100161762-1_C01.subreads.fastq_SoftmaskedBimpatiensGenome.FINAL_minimap2_sorted.bam --busco full_table.tsv --hits AllBlasts.tab  --taxdump taxdump  Bimpatiens

#downloaded the blobtools folder created and open on my computer
blobtools view --local Bimpatiens/

#Note that this fails if port 8001 is being used.   Go into powershell and run this command to see if those ports are being used 'netstat -ano | findstr 8000'
If so then run kill on those commands
i.e. taskkill /PID 22560 /F
i.e. C:\Users\remkv> taskkill /PID 27488 /F
```
### Results
This is after removing the top 18 largest scaffolds. 
![Blobtools Blob plot](https://github.com/ISUgenomics/2024_Toth_Bimpatiens/blob/main/Assets/Bimpatiens.blob.circle.png)

all scaffolds present
![Blobtools Blob plot](https://github.com/ISUgenomics/2024_Toth_Bimpatiens/blob/main/Assets/Bimpatiens.snail.png)






