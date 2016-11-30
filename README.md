# Detect and categorize noncoding RNAs and identify potential targets in an microarray dataset

## Introduction

This tutorial is to analyize microarray dataset, identify differential expression of genes, categorize into different biotypes (both protein coding and noncoding RNAs), then further identify potential targets(protein coding) for long noncoding RNAs like lincRNAs (long intergenic noncoding RNAs) and antisense. This pipeline can also be used to detect pseudogenes and their respective parent protein coding genes.

### Dataset

Microarray dataset in a .csv format.

#### Differential expression of genes

Differential expression of genes can be obtained using GeneSpring GX software V 12.6, Silicon Genetics, Redwood City, CA. Differential expression tests considered significant if differences in expression between the groups with a fold-change of 1.5 and greater, and t-test p-values were less than 0.05. To identify differential expression of probes with a fold-change of 1.5 assigned as cutoff, an unpaired T-test by computing asymptotic p-values, adjusted for multiple testing by using the false discovery rate (FDR) or Benjamini and Hochberg method. The probe sets can be further narrowed to corresponding genes with FDR (q less than 0.05) to bypass the problem of probes mapping to multiple genes. These passing the threshold can be called differentially expressed genes (DEGs). 



#### LINCRNA AND TARGETS 

Extract all lincRNAs, chromosome and their genomic coordinates from step 10 and put them in a file called PC.txt. Extract all protein coding genes, chromosome and their genomic coordinates from step 10 and put them in a file called PC.txt. Use the script called lincToPc.pl to identify the neighboring expressed protein coding genes

Sample lincRNA.txt : SamplelincRNA.txt

Sample PC.txt : SamplePC.txt

Script : lincToPC.pl

To run the script : $bsub < lincToPC.pl OutputFileName

lincToPC.pl :https://github.com/hrccspipeline/HRCCSPipeline/blob/master/scripts/lincToPc.pl

Sample OutputFile : SampleLincAndPC.txt

#### ANTISENSE AND TARGETS 

Extract all Antisense RNAs, chromosome, strand and genomic coordinates from step 10 and put them in a file called asRNA.txt. Extract all protein coding genes chromosome, strand and genomic coordinates from step 10 and put them in a file called PC.txt. Use the script called AntisenseToPConOppositeStrand.pl to identify the neighboring expressed protein coding genes

Sample asRNA.txt : SampleasRNA.txt

Sample PC.txt : SamplePC.txt

Script : antiOppGene.pl

antiOppGene.pl :https://github.com/hrccspipeline/HRCCSPipeline/blob/master/scripts/antiOppGene.pl

To run the script : $bsub < antiOppGene.pl  OutputFileName

Sample OutputFile : AsRNAOutput.txt

#### PSEUDOGENE AND PARENT PROTEIN CODING GENE (TARGETS)

The pseudogene sequences are mapped against the protein coding gene sequences using BLAST, and only the unique hits are  assigned to parent gene-pseudogene associations. 

Fasta sequences for protein-coding genes and pseudogenes are downloaded from the reference genome, Mus_musculus.GRCm38.dna_rm.primary_assembly.fa using the script (MusFastaExtract.pl)

MusFastaExtract.pl: https://github.com/hrccspipeline/HRCCSPipeline/blob/master/scripts/MusFastaExtract.pl

These two sets of sequences are mapped against each other using BLAST using the script (MousePseudoToProtein.sh)and the parameter “-max_target_seqs :1” to detect only those protein-coding genes that have a high-level of sequence homology to the pseudogenes used as query. 

MousePseudoToProtein.sh: https://github.com/hrccspipeline/HRCCSPipeline/blob/master/scripts/MousePseudoToProtein.sh

Finally, the output obtained from BLAST analyzed to overcome the multiple associations, i.e. when a pseudogene aligns to multiple protein-coding genes. The BLAST output was filtered using the “sort –u” option, which sorts the file and pipes the output that contains only unique hits. Therefore, only unique hits are obtained for further analysis.
