---
search:
  exclude: true
---

# What Does AmrPlusPlus Produce?

!!! warning
    This documentation is for AMR++ version 1.1. Click [here](./../latest/introduction.md) for the latest docs.

This section explains the files that you will receive after AmrPlusPlus is finished running. There are a total of 9 files: 4 resistome files, 4 rarefaction files, and 1 SNP file. The output used in the following sections is real output generated using the example data provided on the AmrPlusPlus website.

### 1. Resistome Analysis
The resistome (counts of resistance genes found in your data) are generated in the following order:

!!! info "Note"
    **gene-level counts → Group-level counts → Mechanism-level counts → Class-level counts**

Four files are produced from this analysis step; each file corresponds to an annotation level in the MEGARes database: Class, Mechanism, Group, and gene (sequence-level). Because alignments were performed at the gene level, this file is the starting point for analysis. Within the file gene_level_resistome.tsv, each row represents a data entry, and there are four tab-separated columns.

An example is provided below, and each field is individually explained:
[gene_level_resistome.tsv](https://megares.meglab.org/amrplusplus/latest/_data/gene_level_resistome[4].tsv)



| Sample | Gene | Hits | Gene Fraction |
| ------ | ---- | ---- | ------------- |
| dataset_14 | gi\|13562034\|gb\|AF351241.1\|betalactams\|Class_A_betalactamases\|TEM | 5 |	32.3158 |
| dataset_14 | gi\|14588992\|emb\|AJ277415.1\|betalactams\|Class_A_betalactamases\|TEM | 2 |	14.6341 |
| dataset_14 | gi\|145975406\|gb\|EF534736.1\|betalactams\|Class_A_betalactamases\|TEM | 2 |	23.4637 |
| dataset_14 | gi\|1488048\|gb\|U63835.1\|PAU63835\|betalactams\|Class_D_betalactamases\|OXA | 1 | 10.678 |
| dataset_14 | gi\|149167\|gb\|M88143.1\|KPNBETALAC\|betalactams\|Class_A_betalactamases\|TEM | 1 | 9.93691 |
 

**Sample:** this is the sample ID to which the data belong and is typically related to the name of the input file.

**Gene:** this field is the target gene from the MEGARes database (or whichever database you chose to use during alignment). These are the genes from the database that were found in your input data. If you're using MEGARes as a database, you can copy and paste the gene name into the MEGARes website search bar and it will provide additional information about that particular gene.

**Hits:** this field is the number of short reads that aligned to that particular gene. We use this as a “count” of how many times a given gene is “seen” in the data. These counts can be analyzed relative to counts of other genes.

**Gene Fraction:** we define gene fraction as the percentage of the gene that is covered by at least one read. For example, if a gene has a fraction of 100%, then every nucleotide in that gene has at least one (or more) reads aligning to it. We use this field as a filter to reduce the number of false positives found in our output data. For instance, in the MEG lab, we do not count genes that have a fraction below 80%, so only genes that are covered more than 80% are used to generate counts for statistical analysis.

The counts in this gene-level file are then aggregated up through the annotation graph to produce counts at the Group, then Mechanism, then Class level. In the example above, our next level up from gene is Group, and the Group file would look like so:
[group_level_resistome.tsv](https://megares.meglab.org/amrplusplus/latest/_data/group_level_resistome[5].tsv)

| Sample | Group | Hits | 
| ------ | ----- | ---- |
| dataset_14 | OXA | 1 |
| dataset_14 | TEM | 10 |

!!! info "Note"
    Fields are the same, except there is no Gene Fraction field in this file. Because alignment is performed on the genes, and the Gene Fraction information is calculated using alignment data, the gene-level file is the only one that contains Gene Fraction information. All genes that pass the Gene Fraction threshold will be counted into the next file at the Group level. Here, because we had two Groups represented in our lowest-level file, we see counts for two groups that are equal to the sum of the gene counts in the previous file.

The next level up from Group is the Mechanism level, and the Mechanism file would look like so:
[mechanism_level_resistome.tsv](https://megares.meglab.org/amrplusplus/latest/_data/mech_level_resistome[6].tsv)

| Sample | Mechanism | Hits |
| ------ | --------- | ---- |
| dataset_14 | Class_D_betalactamases | 1 |
| dataset_14 | Class_A_betalactamases |	10 |
 

Because the TEM betalactamases are Ambler Class A betalactamases, their mechanism is “Class_A_betalactamases”. There are 10 counts for the Class A betalactamases because there were 10 counts for TEM in the Group file. Likewise, the OXA betalactamases are Ambler Class D, and therefore we see 1 count for Class D betalactamases in the Mechanism-level file.

The next level up from Mechanism is the Class level, and the Class level file would look like so:
[class_level_resistome.tsv](https://megares.meglab.org/amrplusplus/latest/_data/class_level_resistome[7].tsv)

| Sample | Class | Hits |
| ------ | ----- | ---- |
| dataset_14 | betalactams | 11 |
 
The Class level is the highest level and includes the major drug classes of antimicrobial resistance. In this case, all of our counts were betalactamases, so we see that all 11 counts are now represented in the betalactams Class field.

These four files comprise the output for the resistome analysis and can be combined across multiple samples to produce a count matrix for analysis. A count matrix is a matrix of counts where the rows are the term of interest (e.g. betalactams), the columns are the sample names, and the matrix is filled in with the counts for each of term of interest within each sample. Alternatively, the rows could be sample names and the columns the term of interest.

For suggestions on how to analyze and interpret count data, see the “How do I interpret the AmrPlusPlus output” section.

### 2. Rarefaction Analysis

Rarefaction is an ecological measure of how much of the biological diversity we have captured at a given sampling level. The generation of a rarefaction curve involves, for each sample, picking random sequences from that data and generating counts of how many unique genes were observed. Alternatively, we could ask how many unique Groups, Mechanisms, or Classes were observed at a given sampling level. RarefactionAnalyzer will take three random draws at increments of 5% sampling level, so 5% of the raw data, 10%, … , 100%. The number of unique genes, Groups, Mechanisms, and Classes are then plotted as a function of sampling depth. An example is provided here for our example data at the gene level. Ideally, we would see curves that flatten out toward the right-hand side of the graph (at the 100% sampling level), as this means that we have captured much of the diversity in our sample population. So for this particular figure, we have capture a lot of the diversity but could probably sample deeper in future studies. We largely obtain this result because our sample dataset is small for convenience of showcasing our pipeline. Real data will look slightly different.

Rarefaction Gen Plot Graph
 

#### How do I interpret the AmrPlusPlus output?

At the conclusion of running AmrPlusPlus, you have four graphs, four files with counts, and a single file with SNP data from that sample. The graphs should be interpreted as described above for rarefaction analysis.

1. [View Gene Graph](https://megares.meglab.org/amrplusplus/latest/_pdf/Rarefaction_Gene_Plot[5].pdf)
1. [View Group Graph](https://megares.meglab.org/amrplusplus/latest/_pdf/Rarefaction_Group_Plot[4].pdf)
1. [View Mechanism Graph](https://megares.meglab.org/amrplusplus/latest/_pdf/Rarefaction_Mechanism_Level[5].pdf)
1. [View Class Graph](https://megares.meglab.org/amrplusplus/latest/_pdf/Rarefaction%20Class%20Plot[5].pdf)

The count data should be combined with other samples in your experiment into a count matrix. A count matrix is a matrix of counts where the rows are the term of interest (e.g. betalactams), the columns are the sample names, and the matrix is filled in with the counts for each of term of interest within each sample. Alternatively, the rows could be sample names and the columns the term of interest. You can then use this count matrix as input for many statistical or descriptive procedures, including but not limitd to the following:

- Principal components analysis
- Non-metric multidimensional scaling
- Zero-inflated Gaussian Mixture Models (for example, with the metagenomeseq R package)
- Linear discriminant analysis

The primary question that you'll want to ask of your data depends on your experimental design. However, this question usually involves some kind of comparison between groups: does group A have more betalactamases than group B? How does the resistome differ between group A and group B? Is that significant? What major features in group A make it significantly differ from group B?

These kinds of questions are mostly statistical, and any tool that accepts count data can be used for further analysis. Because this is nearly identical to microbiome analysis, there are plenty of tools out there that can aid in further statistical exploration, such as the R package *metagenomeseq*.