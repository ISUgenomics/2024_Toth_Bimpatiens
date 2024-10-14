# Run nanoplot to evaluate read quality of B. impatiens Pacbio HIFI

```
/work/gif3/masonbrink/Toth/02_Bimpatiens/10_ReadHistogram/B_impatiens

/work/LAS/amytoth-lab/Dovetail_data_2021/Bombus_impatiens/PacBio_FASTQ/XDOVE_20201028_S64049_PL100161762-1_C01.subreads.fastq.gz

echo "ml miniconda3; source activate nanoplot; sh runNanoPlot.sh XDOVE_20201028_S64049_PL100161762-1_C01.subreads.fastq.gz B_impatiens" >nanoplot.sh

Using 5466901 reads with read length N50 of 38124bp and maximum of 316801bp.
```

![Histogram Read Length](https://github.com/ISUgenomics/2024_Toth_Bimpatiens/blob/main/Assets/B_impatiens_HistogramReadlength1.png)

![Histogram Read Length Log Transformed](https://github.com/ISUgenomics/2024_Toth_Bimpatiens/blob/main/Assets/B_impatiens_LogTransformed_HistogramReadlength1.png)
