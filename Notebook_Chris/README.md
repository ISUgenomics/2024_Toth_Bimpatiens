# Bombus_impatiens
Repo of Bombus genome and toxicity exposure

## RNA-Seq (raw data to counts)

All 60 and 67, brain and fatbody RNA-Seq dataset were run through nf-core rnaseq v3.14.0 (using Nextflow version 24.04.2) separately.

With the following command (e.g. for brain):

`nextflow run main.nf --input /home/ucbtcdr/Scratch/Bombus_rnaseq/rnaseq_brain/rnaseq_run1/sample_sheet-brain.csv --outdir rnaseq_results_brain -profile apptainer,ucl_myriad -resume -bg --gff /home/ucbtcdr/Scratch/Bombus_genome/SortedFunctionalAnnotationBimpatiens.allwithparents.gff --fasta /home/ucbtcdr/Scratch/Bombus_genome/B_impatiensGenome.FINAL.fasta.gz --skip_biotype_qc --skip_markduplicates`

MultiQC reports from the brain and fatbody analysis are stored here:

`.data/multiqc`

Files are gzipped, so to open download the `.html.gz`, then run `gunzip *.html.gz`. Then open them in your web browser. 

## Differential expression

Using the output from the nf-core rnaseq pipeline, I took "deseq2.dds.RData" output, to use as the input to DESeq2.

Then saved the file names to a file:
`sample_names<- dds$sample`
`write.csv(sample_names, "samples.csv", row.names = FALSE, quote=F)`

Before running: `bin/file2coldata.R` to extract the samples and their ID code in the second part of name tag (X1.**A01**.A3_S1_L002):

`Rscript file2coldata.R samples.csv`

Before editing in an editor to match A,C (Controls), B,D,E,F (Exposed) and exntering coldata file to R.

`coldata<- read.csv("coldata.condition.csv", sep="\t", row.names=1)`

Then I ran the R code in bin/differential.expression.R
which runs DESeq2, and makes the PCA, volcano and other plots. Also makes the DESeq2 differtial table different between control and exposed.

## GO annotation

To get the GO annotation for Bombus impatiens, I ran the Eco-Flow pipeline (excon v1.0.1; https://github.com/Eco-Flow/excon).

Using the following command:
`nextflow run main.nf -resume -profile apptainer --custom_config myriad.config --input input.csv --cafe --cafe_go --ensembl_biomart "metazoa_mart" --ensembl_dataset "go_species.txt" --skip_cafe`

Where input was just out Bombus impatien genome. "go_species.txt" was 

 | Name on biomart | Genus/Species | Colloquial name |
| --------------- | --------------- | --------------- | 
 | Agambiae_eg_gene | Anopheles gambiae | African malaria mosquito | 
 | dmelanogaster_eg_gene | Drosophila melanogaster | Fruit fly | 
 | bmori_eg_gene | Bombyx mori | Domestic silkworm | 
 | amellifera_eg_gene | Apis mellifera | Honey bee | 
 | tcastaneum_eg_gene | Tribolium castaneum | Red flour beetle | 
 | apgca005508785v2rs_eg_gene | Acyrthosiphon pisum | Pea aphid | 
 | nvitripennis_eg_gene | Nasonia vitripennis | Jewel wasp |

The output GO hash information is in results/go . The output go file is stored in this repo:
`data/Bombus_impatiens.GO_format.txt`

## Synteny analysis.

To run the synteny analysis, the following tutorial was completed using the JCVI package (https://github.com/tanghaibao/jcvi/wiki/MCscan-(Python-version), see methods in paper). 

This is outlined in the code for this in bin/synteny.sh in this repository. 

It uses various python commands to process the genomes and their associated annotations in gff format, and performs the MSCanX synteny program along with various visualisation plots to represent the syntenic regions between genomes.
