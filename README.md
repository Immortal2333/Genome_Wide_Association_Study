# Genome Wide Association Study

![Update](https://img.shields.io/badge/Update-01/08/2024-green?logo=github)
![Author](https://img.shields.io/badge/Author-Xu.Wang-orange)
![Email](https://img.shields.io/badge/Email-571720850@qq.com-blue?)

The home page of "**Polygenic Basis Seedless Grapes**" can be accessed by clicking [here](https://github.com/zhouyflab/Polygenic_Basis_Seedless_Grapes).

## Data preparation
**Input Data**
* WGS data: `.R1.fastq.gz` and `.R2.fastq.gz`
* VCF File

**Dependency**
The detials of all tools can be available in their **offical website** as followed and most of them can quickly install using [Anaconda](https://anaconda.org/):
* [PLINK2.0](https://anaconda.org/bioconda/plink2), [VCFtools](https://anaconda.org/bioconda/vcftools), [GEMMA](https://anaconda.org/bioconda/gemma)

## Data filtering
You could be checked the raw VCF file obtained from this repository ([Population Genetics Analysis Pipeline](https://github.com/Immortal2333/Population_Analysis_Pipeline)).

If needed, you can separate the VCF file into SNPs and InDels.
```
vcf="all_miss0.8GQ20maf0.0001.id.vcf"

#vcftools --vcf ${vcf}.vcf --remove-indels --recode --out ${vcf}.rmindel
#vcftools --vcf ${vcf}.vcf --keep-only-indels --recode --out ${vcf}.rmsnp
```

Here, we only retain samples with phenotype values.
```
plink --vcf ./all_miss0.8GQ20maf0.0001.id.vcf \
--vcf-half-call m \
--geno 0.2 \
--maf 0.05 \
--hwe 1e-5 \
--keep keep444.txt \
--recode vcf-iid \
--allow-extra-chr \
--const-fid \
--threads 5 \
--make-bed \
--out 444_geno0.2gq20maf0.005hwe1e-5.id

-------------
./keep444.txt:
0  B1
0  B2
0  B3
...
```

## Insert phenotype values manually
Please replace the sixth line of the `444_geno0.2gq20maf0.005hwe1e-5.id.fam` file with their phenotype values. Using the Excel `VLOOKUP` function is recommended.

## GEMMA and GWAS ploting
We selected the linear mixed model (LMM) and used all three tests: `Wald test`, `likelihood ratio test`, and `score test` with the `-lmm 4` option. Detailed documentation for the GEMMA software is available [here](https://xiangzhou.github.io/software/GEMMAmanual.pdf). The GEMMA offical GitHub: [https://github.com/genetics-statistics](https://github.com/genetics-statistics).
```
name='444_geno0.2gq20maf0.005hwe1e-5.id'
gemma -bfile ${name} -gk 2 -miss 0.2 -maf 0.05 -hwe 1e-5 -o ${name}
gemma -bfile ${name} -n 1 -miss 0.2 -maf 0.05 -hwe 1e-5 -k ./output/${name}.sXX.txt -lmm 4 -o ${name}
```

```
name='444_geno0.2gq20maf0.005hwe1e-5.id'

# Please ensure your chromosome ID only keeping numbers. Or you can use this code to revise your chromosome numbers.
# for i in {01..19}; do sed -i "s/Chr${i}/${i}/g" ${name}.assoc.txt ;done 

Rscript ./manhattan.R ${name}.assoc.txt p_wald

# sudo yum install poppler-utils
pdftoppm -png -singlefile ${name}.assoc.txtp_wald_manhattan.pdf ./manhattan.plot
pdftoppm -png -singlefile ${name}.assoc.txtp_wald_qq.pdf ./qq.plot
```

# Code availability and Citation
All scripts could be found in this repository above. If you have any question, please do not hesitate contact us. Xu Wang: 571720850@qq.com

Wang, Xu, et al. "Integrative genomics reveals the polygenic basis of seedlessness in grapevine" ***Current Biology*** (2024). doi: [https://doi.org/10.1016/j.cub.2024.07.022](https://doi.org/10.1016/j.cub.2024.07.022)

