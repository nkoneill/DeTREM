Deconvoultion with Target and Reference differences Extending MuSiC: DeTREM
=============================================

`DeTREM` is a modification of the MuSiC deconvolution algorithm optimized for single-nuclei reference data.

How to cite the original `MuSiC`
-------------------
Please cite the following publication:

> *Bulk tissue cell type deconvolution with multi-subject single-cell expression reference*<br />
> <small>X. Wang, J. Park, K. Susztak, N.R. Zhang, M. Li<br /></small>
> Nature Communications. 2019 Jan 22 [https://doi.org/10.1038/s41467-018-08023-x](https://doi.org/10.1038/s41467-018-08023-x) 

How to cite `DeTREM`
-------------------

> Manuscript is being submitted, citation to be generated.




Installation and Basic Analysis
------------

``` r
# Install devtools if necessary
install.packages('devtools')

# Install the MuSiC package
devtools::install_github('nkoneill/DeTREM')

# Load
library(DeTREM)

# Working with a Seurat object, a common single-cell 
sc=readRDS("seurat_object.rds")


# Extract the (gene x cell) count matrix as sc_expr. Alternative (gene x cell) count matrices can be loaded here
sc_expr=as.matrix(sc@assays$RNA@counts)

# Extract two relevant metadata columns from the seurat object, adjust variable names accordingly
# A data frame with celltype and sample columns can be loaded separately
celltype_column="Celltype"
sample_column="sampID"
sc_meta=sc@meta.data[,c(celltype_column,sample_column)]
sc_meta=AnnotatedDataFrame(sc_meta)
colnames(sc_meta)=c("Celltype","sampID")

# Generate the single cell (single nuclei) ExpressionSet object using counts and metadata
sc.eset=ExpressionSet(assayData=sc_expr,phenoData=sc_meta)

# Load a bulk RNASeq count matrix, (gene x sample)
# We use an 'expected count' matrix generated by RSEM, but any count matrix should work
bulk_expr=readRDS("bulk_expression_matrix.rds")
bulk.eset=ExpressionSet(assayData=as.matrix(bulk_expr))

# Test the overlap of gene names between the two ExpressionSets
# Having a low number of 'TRUE' matches indicates a discrepancy and is a common error
table(rownames(bulk.eset) %in% rownames(sc.eset))

# Run DeTREM using both ExpressionSets, extract cell-fraction estimates from the result (sample x celltype) matrix
estimates=music_prop(bulk.eset = bulk.eset, sc.eset = sc.eset, clusters = 'Celltype',samples = 'sampID', verbose = F)
fractions=estimates$Est.prop.weighted
```

More Information
-----------------
This is the tutorial for the original [MuSiC](http://xuranw.github.io/MuSiC/articles/MuSiC.html).
