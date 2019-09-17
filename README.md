BinaryDosage: Creates, Merges, and Reads Binary Dosage Files
================

[![AppVeyor build
status](https://ci.appveyor.com/api/projects/status/github/USCbiostats/BinaryDosage?branch=master&svg=true)](https://ci.appveyor.com/project/USCbiostats/BinaryDosage)

# Binary Dosage Files

### Introduction

Genotype imputation is an essential tool in genomics, enabling
association testing with markers not directly genotyped, increasing
statistical power, and facilitating data pooling between studies that
employ different genotyping platforms. Two commonly used software
packages for imputation are
[minimac](https://genome.sph.umich.edu/wiki/Minimac) and
[Impute2](http://mathgen.stats.ox.ac.uk/impute/impute_v2.html).
Furthermore, services such as the [Michigan Imputation
Server](https://imputationserver.sph.umich.edu/index.html) have made
genotype imputation much more accessible and streamlined.

While a number of software options are available for analyses of imputed
data (e.g. PLINK, EPACTS), fewer are available for Genomewide Gene x
Environment Interaction Scan (GWIS). Furthermore, data management tasks
such as parsing, subsetting, and merging, while manageable in smaller
studies, quickly become unyieldy and prohibitively slow with very large
samples sizes. We aim to address these limitations by converting
imputation outputs into a binary data format, BDose (Binary Dosage). The
benefits of a binary format are two fold - decreased hard drive storage
requirements (compared to a VCF file), and speed of parsing/analyses.
The BinaryDosage package contains functions to convert VCF and Impute2
formatted files into BDose format, along with functions to merge and
subset samples/SNPs.

For GWAS/GWIS analysis of BinaryDosage files, please refer to the
[**GxEScanR**](https://github.com/USCbiostats/GxEScanR) package.

### Description

##### Binary dosage files contain at minimum the following information:

  - Subject IDs  
  - SNP information  
  - Chromosome number  
  - Location in base pairs  
  - Reference Allele  
  - Alternate Allele  
  - Dosage values

##### Binary dosage files may contain any of the following information:

  - Family IDs
  - SNP information
  - SNP name (rs number)
  - Minor allele indicator (reference of alternate allele)
  - Minor allele frequency
  - Average call rate
  - Imputation r squared
  - Genetic probabilities, Pr(g=0), Pr(g=1), Pr(g=2)

##### Merged binary dosage files contain the following information:

  - Number of files combined
  - Number of subjects in each VCF file

### Functions

  - **vcftobd** - Convert a VCF file to binary dosage format
  - **gentobd** - Convert a GEN (impute2) file to binary dosage format
  - **bdmerge** - Merge binary dosage files
  - **getbdinfo** - Create R List containing information about a binary
    dosage file (required for **getsnp** and **bdapply**)
  - **getvcfinfo** - Create R List containing information about a VCF
    file (required for **vcfapply**)
  - **getgeninfo** - Create R List containing information about a GEN
    file (required for **genapply**)
  - **bdapply** - Applies a function to the data for each SNP in a
    binary dosage file (requires list returned by **getbdinfo**)
  - **vcfapply** - Applies a function to the data for each SNP in a VCF
    file (requires list returned by **getvcfinfo**)
  - **genapply** - Applies a function to the data for each SNP in a GEN
    file (requires list returned by **getgeninfo**)
  - **getsnp** - Obtain genotype Dosages/Genotype Probabilities from a
    binary dosage file, outputs results to an R list

# Installation

1.  Install the [devtools](https://github.com/hadley/devtools) package
2.  Install the
    [BinaryDosage](https://github.com/USCbiostats/BinaryDosage) package
    directly from the USCbiostats repository on GitHub:

<!-- end list -->

``` r
remove.packages("BinaryDosage")
devtools::install_github("https://github.com/USCbiostats/BinaryDosage")

library(BinaryDosage)
```

# Usage

#### General Workflow

##### [**BinaryDosage**](https://github.com/USCbiostats/BinaryDosage)

  - Convert VCF or GEN file to a binary dosage file
      - Note: When converting a VCF file to a binary dosage file, the
        information file associated with the vcf can be used to add
        addition data to the binary dosage file
      - Note: When converting a GEN file to a binary dosage file, the
        subject IDs can either be on the first line of the GEN file or
        in a separate sample file

##### [**GxEScanR**](https://github.com/USCbiostats/GxEScanR)

  - Use *ReadBDInfo()* to create an R list of binary dosage file
    attributes
      - Note: This is the only way to see what is in a binary dosage
        file
  - *read.table* phenotype data (*R*)
      - Note: order of covariates matter, refer to package documentation
  - Run GxEScanR to conduct GWAS/GWIS

#### Examples

Example datasets *set1a.vcf* and *set1b.vcf* are representative of VCF
output files obtained from the Michigan Imputation Server. These files
are in the extdata directory of the BinaryDosage package. These files
contain the following:

| Study | N  | Number of SNPs |
| ----- | -- | -------------- |
| 1a    | 60 | 10             |
| 1b    | 40 | 10             |

###### Convert VCF to Binary Dosage Format

``` r
library(BinaryDosage)

# Get the VCF file name
vcf1afile <- system.file("extdata", "set1a.vcf", package = "BinaryDosage")
# Get the name of the information file associated with the VCF file
vcf1ainfo <- system.file("extdata", "set1a.info", package = "BinaryDosage")

bdfiles <- tempfile()
# Convert the file
vcftobd(vcffiles = c(vcf1afile, vcf1ainfo), bdfiles = bdfiles)
# Verify the file was written correctly - an error will be returned if file wasn't written properly
bdinfo <- getbdinfo(bdfiles)
```
