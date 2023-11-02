# GeneExpression
#
# ** work in progress: last updated Sept 2023
## Description
This repository includes a collection of scripts and tools for gene expression analyses. It provides a comprehesive of functions for preprocessing, visualization, and conducting differential gene expression analyses providing the code, data, and documentation necessary. Whether you're working with RNA-Seq or microarray data, this toolkit simplifies the process and helps you build insights into gene expression patterns.
#
## Table of Contents
- [Installation](#installation)
- [Usage](#usage)
- [Data](#data)
  - [Microarray](#microarray)
  - [RNA Seq](#rna-seq)
- [Code](#code)
  - [Preprocessing Scripts](#preprocessing-scripts)
  - [Analysis Scripts](#analysis-scripts)
- [Results](#results)
- [Documentation](#documentation)
- [License](#license)
- [Contributing](#contributing)
- [Issues and Bug Reports](#issues-and-bug-reports)
#
#  
## Installation

To use this toolkit, you'll need Python 3.7 or higher. Clone the repository and install the required dependencies using pip:

```bash
git clone https://github.com/shoosein219/GeneExpression.git
cd GeneExpression
pip install -r requirements.txt
```
## Usage

## Data Source

### MicroArray
- Raw Data
- **Dataset Source:** [GEO - GSE10596](https://www.ncbi.nlm.nih.gov/geo/query/acc.cgi?acc=GSE10596)
- **Description:** This dataset investigates the gene expression profiles of _Zea mays_, particularly how genes are differentially expressed under control and water-deficit conditions during _Z. mays_ heading time, a crucial time of productivity before tassel flowering.

### RNA Seq
- Raw Data
- **Dataset Source:** [GEO - GSE61418](https://www.ncbi.nlm.nih.gov/geo/query/acc.cgi?acc=GSE61418)
- **Description:** This dataset evaluates how innoculation with pathogenic bacteria _Vibrio vulnificus_changes influences gene expression profiles of _Arabidopsis thaliana_ over times (0-72 hours after infection).


## Code

### Preprocessing Scripts
#
### Microarray
- **Script 1 :: _Preprocessing_** from microarray output (.cel)
  [TXT_file_R_scrpt](http://localhost:8888/edit/Desktop/microarrayExample/Rcode_convert_CEL_file.txt)
- **Script 2 :: _Preprocessing_** formatted data
  [ipynb_PY_script](http://localhost:8888/notebooks/Desktop/microarrayExample/microarray_gene_expression_data_preprocessing.ipynb)

### RNA Seq
- **Script A :: _Preprocessing_**


#
### Analysis Scripts
#
### Microarray
- **Script 3 :: _Data Analysis and Visualization_** [ipynb_PY_script_boxpl_hist](http://localhost:8888/notebooks/Desktop/microarrayExample/microarray_gene_expression_data_analysis_viz.ipynb)
- #   Data plots for selected GEO samples
library(GEOquery)
library(limma)
library(umap)

# load series and platform data from GEO

gset <- getGEO("GSE10596", GSEMatrix =TRUE, getGPL=FALSE)
if (length(gset) > 1) idx <- grep("GPL5439", attr(gset, "names")) else idx <- 1
gset <- gset[[idx]]

ex <- exprs(gset)
# log2 transform
qx <- as.numeric(quantile(ex, c(0., 0.25, 0.5, 0.75, 0.99, 1.0), na.rm=T))
LogC <- (qx[5] > 100) ||
          (qx[6]-qx[1] > 50 && qx[2] > 0)
if (LogC) { ex[which(ex <= 0)] <- NaN
  ex <- log2(ex) }

# box-and-whisker plot
par(mar=c(7,4,2,1))
title <- paste ("GSE10596", "/", annotation(gset), sep ="")
boxplot(ex, boxwex=0.7, notch=T, main=title, outline=FALSE, las=2)

# expression value distribution plot
par(mar=c(4,4,2,1))
title <- paste ("GSE10596", "/", annotation(gset), " value distribution", sep ="")
plotDensities(ex, main=title, legend=F)

# mean-variance trend
ex <- na.omit(ex) # eliminate rows with NAs
plotSA(lmFit(ex), main="Mean variance trend, GSE10596")

# UMAP plot (multi-dimensional scaling)
ex <- ex[!duplicated(ex), ]  # remove duplicates
ump <- umap(t(ex), n_neighbors = 2, random_state = 123)
plot(ump$layout, main="UMAP plot, nbrs=2", xlab="", ylab="", pch=20, cex=1.5)
library("maptools")  # point labels without overlaps
pointLabel(ump$layout, labels = rownames(ump$layout), method="SANN", cex=0.6)


### RNA Seq

#
## Results

## Documentation

- [README](#readme)
- [License](#license)
