---
search:
  exclude: true
---

# Pipeline Description

!!! warning
    This documentation is for AMR++ version 1.1. Click [here](./../latest/introduction.md) for the latest docs.


Below we explain the use of each component in the AmrPlusPlus pipeline.

??? example "Step 1: Input"
    ### Input
    The raw data input to AmrPlusPlus will be a single pair of fastq files, one forward and one reverse. Additionally, you will need a database file in FASTA format (for the targets you wish to find in your data) such as the [MEGARes](http://megares.meglab.org/) database file, and a FASTA file for a host organism.

??? example "Step 2: Quality Control"
    ### Quality Control
    Depending on the sequencing platform, your data will likely contain some kind of error or bias.

    For Illumina sequencers, the quality of the sequence (the confidence that a given nucleotide is correct) drops off toward the end of the read. For Proton Torrent machines, there will be tandem repeats of nucleotides that aren't correct. Additionally, random error will be dispersed throughout.

    Quality control programs utilize statistics and other mathematical conventions to remove these regions of error from your data.

    While it is not necessarily required, it is highly recommended to run a quality control program on your raw data, as it is fairly efficient and will give you information about the quality of the sequencing performed on your samples. Ideally, less than 10% of the data will be of poor quality.

    For AmrPlusPlus, we use Trimmomatic^4^ to perform quality control.

??? example "Step 3: Removal of Host DNA"
    ### Removal of Host DNA
    If your samples are collected from an animal source, there will be DNA that is off-target in your sample. We define off-target as being a sequence that is not contained within the database of interest, so for our typical use case, this would be any DNA that is not related to antimicrobial resistance. While it is not required to perform this step, it is recommended, as it can reduce the rate of false positive classifications.

    Reviewers have also commented on the quality of the pipeline when this step has been left out in our previous manuscripts, so we now perform host removal on all samples. We use the Burrows Wheeler Aligner^5^ and a FASTA file of known host genetic sequences to match reads from the sample to the host genome. We then utilize Samtools^6^ to filter out the reads that align to the host genome.

??? example "Step 4: Alignment to Target Database"
    ### Alignment to Target Database
    This step is the same as the prior step, except we use the database of interest for alignment.

    This will match the raw short read sequences to the target database using BWA and provide a SAM file as output. SAM stands for Sequence Alignment/Map and provides information for how the short fragments map to the database sequences; the SAM file specification is fairly detailed, so these files are large and often contain information that isn't useful in many contexts.

    We have written programs to parse this file and obtain the information of interest about the aligned reads.

??? example "Step 5: Resistome Analysis"
    ### Resistome Analysis
    This part of the pipeline takes the SAM file generated in Step 4 and transforms it into the sample resistome. It does this by counting each alignment for each gene found in the SAM file (1 aligned read = 1 count). The output consists of 4 tab-delimited text files, one for each level of the database hierarchy (gene, group, mechanism and class). Within each file, the first column lists the sample that was analyzed (taken from the file name), the second column lists the gene, group, mechanism or class that was identified, and the third column gives the total count of reads that aligned to that gene, group, mechanism or class. For the gene-level output, there is a fourth column that lists the gene fraction. Gene fraction is defined as the proportion of nucleotides in the reference sequence that were aligned to by at least one sequence read. Our program allows you to set a gene fraction threshold at which genes are considered to be positively identified in the sample. This threshold is meant to decrease false positive identification. For example, if you set the gene fraction threshold at 80%, then only genes with at least 80% nucleotide coverage will be included in the output.

??? example "Step 6: Rarefaction Analysis"
    ### Rarefaction Analysis
    If a rarefaction analysis is desired, our program called RarefactionAnalyzer can perform it at this step. Rarefaction can be of interest when you wish to know the fraction of the target population that is captured in your sequence data versus the fraction of the target population that has not been described due to not sequencing deep enough. This is a standard analysis performed for microbiome research and is typically recommended for any kind of metagenomics where counts are of interest.

    Using our program, you are able to specify the increments (i.e., "levels") of your rarefaction (i.e., whether you subsample your sequence data in 5-percent increments, 20-percent increments, etc...), and the number of random subsamples (i.e., "iterations") to perform at each increment. In order to obtain a smooth rarefaction curve, we suggest performing at least 5 iterations at each level so that each point on the curve is an average of at least 5 random subsamples. In addition to setting the "level" and "iteration" parameters, you are also able to apply a gene fraction threshold to the rarefaction analysis (see Step 5 for more details).

    The output of the Rarefaction Analysis includes 4 rarefaction curves -- one for each level of the annotation hierarchy (gene, group, mechanism and class). For each graph, the x-axis is the rarefaction level (from 0 to 100%), and the y-axis is the number of unique genes, group, mechanisms or classes identified in the rarefied data.

??? example "Step 7: Read-pair Haplotyping"
    ### Read-pair Haplotyping
    Variants are defined as nucleotides that differ from the reference database target. For reads that have aligned to a given reference sequence, we check each nucleotide against the reference target to determine if a variant is present. If multiple variants are present on the same read pair, we can make the assumption that they come from the same fragment of DNA (the same organism), so we record these variants that fall on the same read pair as haplotypes. The output from the variant calling step is a count file similar to the Resistome Analysis step, however the counts are of the haplotypes as determined with respect to the reference target. This information can be used in statistical analysis of counts, as in the previous step, or variants of interest can be manually found in the file if they are of interest.

    Output from this module is a tab delimited text file with three columns:

    - **Gene:** Name of the gene that contains the corresponding haplotype
    - **Haplotype Pattern:** Location(s) of each SNP, followed by reference nucleotide -> variant nucleotide. SNP locations and variants are separated by full colons.
    - **Occurrence:** The number of reads (or read pairs) with the given haplotype pattern.
 
    !!! info "Output"
        | Gene | Haplotype Pattern | Occurrence |
        | ---- | ----------------- | ---------- |
        | gene1 | 494T->A:987C->A:1001A->T | 2 |
        | gene2 | 56T->A:80A->C:93A->C:109T->A | 8 |
        | gene3 | 285T->A:1022:C->G | 3 |
        | gene4 | 585T->A:1051:C->A | 1 |

??? example "Output"

    As output from running AmrPlusPlus, you will receive two files for each sample: a file with a count of how many reads aligned to each target from the reference database, and a file with a count of haplotypes that were detected within aligned reads.

    Our recommendation is to then combine files of the same type into two “master” files that are count matrices. Depending on the subsequent application you will be using for statistical analysis, you may want to arrange the matrices with gene targets as rows and the samples as columns, where the counts of the targets are the entries in the matrix.

    Alternatively, you could transpose that matrix and have the gene targets be the columns and the samples be the rows.

??? example "Next Steps"
    With the two count matrices, you can then perform statistical analyses using publicly available tools. We make use of the R programming language and the package [metagenomeseq](https://www.bioconductor.org/packages/devel/bioc/vignettes/metagenomeSeq/inst/doc/metagenomeSeq.pdf) to perform a count matrix analysis.

    The questions that are typically asked of count data are:
        
    **How similar or different are the samples, both by pairwise comparison and by study design?**
        
    **If there is a time series in the experimental design, is there a significant trend over time related to the count data?**
      
    **Are the statistically significant differences related to any biologically meaningful group of targets?**

    The metagenomeseq package can perform the analyses in (1) and (2), and the user can obtain information about (3) based on the results. Another option, if relative abundance changes are of interest, is to use an RNA-seq count matrix analysis package such as [DESeq2](http://bioconductor.org/packages/release/bioc/html/DESeq.html) (also an R package) to gather statistics on the log-fold changes from one sample group to another.

*[Burrows Wheeler Aligner]: BWA is a software package for mapping low-divergent sequences against a large reference genome, such as the human genome.
*[Samtools]: SAM (Sequence Alignment/Map) format is a generic format for storing large nucleotide sequence alignments.