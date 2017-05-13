# SCALE
Allele-Specific Expression by Single-Cell RNA Sequencing


## Author
Yuchao Jiang, Nancy R. Zhang, Mingyao Li


## Maintainer
Yuchao Jiang <yuchaoj@upenn.edu>


## Description
SCALE is a statistical framework for **S**ingle **C**ell **AL**lelic **E**xpression analysis. SCALE estimates kinetic parameters that characterize the transcriptional bursting process at the allelic level, while accounting for technical bias and other complicating factors such as cell size. SCALE detects genes with significantly different bursting kinetics between the two alleles, as well as genes where the two alleles exhibit dependence in their bursting processes.


## Installation
```r
install.packages("rje")
install.packages("devtools")
library(devtools)
install_github("yuchaojiang/SCALE/package")
```


## Demo code & Vignettes
* Bioinformatic pipeline
  * [Germline heterozygous SNP detection by bulk-tissue sequencing](https://github.com/yuchaojiang/SCALE/blob/master/bioinfo/bulk_SNP.sh)
  * [Allele-specific read counts at germline heterozygous loci by single-cell RNA sequencing](https://github.com/yuchaojiang/SCALE/blob/master/bioinfo/scRNAseq_SNP.sh)
  * [Input for exogenous spike-ins for adjustment of technical variability by single-cell RNA sequencing](https://github.com/yuchaojiang/SCALE/blob/master/bioinfo/scRNAseq_spikein.sh)
* SCALE
  * [Demo code](https://github.com/yuchaojiang/SCALE/blob/master/demo/demo.R)
  * [Vignettes](https://github.com/yuchaojiang/SCALE/blob/master/demo/SCALE_vignettes.pdf)


## Questions & Issues
If you have any questions with the package, feel free to post in our Google user group https://groups.google.com/d/forum/SCALE_scRNAseq or email us at SCALE_scRNAseq@googlegroups.com. We will try our best to reply as soon as we can.


## Citation
Jiang, Yuchao, Nancy R. Zhang, and Mingyao Li. "SCALE: modeling allele-specific gene expression by single-cell RNA sequencing." Genome Biology 18.1 (2017): 74. [link](https://genomebiology.biomedcentral.com/articles/10.1186/s13059-017-1200-8)


## Common questions
* **How to adjust for possible heterogeneity within the cell population?**

 SCALE needs to be applied to a *homogeneous cell population*, where the same bursting kinetics are shared across all cells. Possible heterogeneity due to, for example, cell subgroups, lineages, and donor effects, can lead to biased downstream analysis. We find that an excessive number of significant genes showing coordinated bursting between the two alleles can be indicative of heterogeneity with the cell population, which shoud be stratified. Therefore, it is recommended that users adopt dimensionality reduction and clustering methods (e.g., t-SNE, PCA, [ZIFA](https://genomebiology.biomedcentral.com/articles/10.1186/s13059-015-0805-z), [RCA](http://www.nature.com/ng/journal/v49/n5/full/ng.3818.html), hierarchical clustering, [SC3](https://www.nature.com/nmeth/journal/v14/n5/full/nmeth.4236.html), etc.) on the expression matrix for clustering. SCALE can then be applied to a homogeneous cell cluster that is identified.
  
* **Does SCALE require spike-in in every cell? How to estimate technical noise assoicated parameters?**

 SCALE uses external spike-ins to estimate the technical-noise associated paramters. SCALE does not require spike-in in every cell in the experiment. As long as *some* cells from the same batch have spike-ins, they can be used to model and capture the dropout events and the amplification and sequencing biases during the library prep step. Make sure that the column names of spikein_input are the same as (a subset of) those of alleleA and alleleB.

 The technical noise associated parameters include: 𝛼_𝑐 which models the capture and sequencing efficiency; 𝛽_𝑐 which models the amplification bias; 𝜅_𝑐 and 𝜏_𝑐 which characterize whether a transcript is successfully captured in the library. To estimate 𝛼_𝑐 and 𝛽_𝑐, SCALE uses the external spike-ins to fit a log-linear regression with the true number of molecules as response and the observed number of reads per locus as predictor.


* **How do I compute cell size?**

 Cell size can be inferred from the *GAPDH* expression levels (with adjustment of library size factor which is just due to sequencing depth rather than cell size difference, see first equation under Methods section in our paper) or the ratio of endogenous reads over the total number of spike-in reads. For consistency, these two measurements should NOT be combined together.

 To correctly calculate the cell size factor baed on sequencing reads alone isn’t trivial. We are hoping that there could be measurements available from the single cell isolation machine (e.g., Fluidigm C1) soon. We recommend first estimating the bursting kinetics with all cell size factors set to 1. After this, one can try again using the estimated cell size factors. The correlations between the bursting kinetics of the two alleles can serve as a good sanity check (on the genome wide scale, they should be correlated with a fairly decent correlation coefficient).


* **Which quality control procedures should I adopt?**

 Quality control procedures are recommended to filter out both extreme cells and genes before applying SCALE. Some metrics may include: library size factor (see first equation under Methods in our paper), PCA result (to remove cell outliers or heterogeneity), allelic ratio (standard deviation of a gene across all cells), ratio of reads that map to spike-ins versus endogenous genes (i.e., cells with extreme cell sizes), and true number of spike-in molecules (first column of spikein_input, where spike-ins with small number of molecules should be removed).
 

* **Monoallelic expression?**

 In this paper, a *gene* is categorized to be monoallelically expressed, if only one allele is expressed in a nonzero proportion of cells. Gene categorization is carried out through the empirical Bayes framework outlined in our paper. While SCALE is focused on detecting differential bursting kinetics between the two alleles, monoallelic expression is an extreme case where one allele is completely off (which is equivalent to having an infinitely large burst frequency). We don't infer bursting kinetics -- there is no way nor need to do so -- or perform hypothesis testings on these monoallelically expressed genes. However, we think these genes are just as if not more important in biology. The gene categorization results are included in the table genrated in the last step of SCALE.
 
 

