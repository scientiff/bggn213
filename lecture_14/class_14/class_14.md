Transcriptomics and the analysis of RNA-Seq data
================

# 1\. Read the data for today and start analyzing it.

``` r
counts <- read.csv("airway_scaledcounts.csv", stringsAsFactors = FALSE)
metadata <- read.csv("airway_metadata.csv", stringsAsFactors = FALSE)
```

`stringsAsFactors = FALSE` makes it so that your data doesn’t become
factors. (The gene names\!) That’s only useful for categorical data, not
ours.

``` r
head(counts)
```

    ##           ensgene SRR1039508 SRR1039509 SRR1039512 SRR1039513 SRR1039516
    ## 1 ENSG00000000003        723        486        904        445       1170
    ## 2 ENSG00000000005          0          0          0          0          0
    ## 3 ENSG00000000419        467        523        616        371        582
    ## 4 ENSG00000000457        347        258        364        237        318
    ## 5 ENSG00000000460         96         81         73         66        118
    ## 6 ENSG00000000938          0          0          1          0          2
    ##   SRR1039517 SRR1039520 SRR1039521
    ## 1       1097        806        604
    ## 2          0          0          0
    ## 3        781        417        509
    ## 4        447        330        324
    ## 5         94        102         74
    ## 6          0          0          0

``` r
head(metadata)
```

    ##           id     dex celltype     geo_id
    ## 1 SRR1039508 control   N61311 GSM1275862
    ## 2 SRR1039509 treated   N61311 GSM1275863
    ## 3 SRR1039512 control  N052611 GSM1275866
    ## 4 SRR1039513 treated  N052611 GSM1275867
    ## 5 SRR1039516 control  N080611 GSM1275870
    ## 6 SRR1039517 treated  N080611 GSM1275871

How many genes are we dealing with?

``` r
nrow(counts)
```

    ## [1] 38694

We will compare the control vs. treated count data contained in `counts`
object. We will first extract the control columns by looking up their
“colnames” in the `metadata` object.

Use `==` to compare objects. It returns TRUE or FALSE. To print ONLY the
TRUE statements, use brackets. Use \[item,\] the comma second means
you’re calling the ROW.

``` r
#metadata$dex
#metadata$dex == "control"

# Use this to create a robust way of accessing the control column of this data and assigning the ID's to it.
control <- metadata[metadata$dex =="control",]

head(control)
```

    ##           id     dex celltype     geo_id
    ## 1 SRR1039508 control   N61311 GSM1275862
    ## 3 SRR1039512 control  N052611 GSM1275866
    ## 5 SRR1039516 control  N080611 GSM1275870
    ## 7 SRR1039520 control  N061011 GSM1275874

Used \[,item\] the comma first means you’re calling the COLUMN.

``` r
# Access the count columns with control$id.
head(counts[,control$id])
```

    ##   SRR1039508 SRR1039512 SRR1039516 SRR1039520
    ## 1        723        904       1170        806
    ## 2          0          0          0          0
    ## 3        467        616        582        417
    ## 4        347        364        318        330
    ## 5         96         73        118        102
    ## 6          0          1          2          0

``` r
# Calculate the mean value for each gene (i.e. each row).
control.mean <- rowMeans(counts[,control$id])

head(control.mean)
```

    ## [1] 900.75   0.00 520.50 339.75  97.25   0.75

``` r
# Here's the hard way!

control.mean2 <- rowSums(counts[,control$id])/nrow(control)

head(control.mean2)
```

    ## [1] 900.75   0.00 520.50 339.75  97.25   0.75

Now let’s make a set of data for “treated” data.

``` r
treated <- metadata[metadata$dex =="treated",]
head(treated)
```

    ##           id     dex celltype     geo_id
    ## 2 SRR1039509 treated   N61311 GSM1275863
    ## 4 SRR1039513 treated  N052611 GSM1275867
    ## 6 SRR1039517 treated  N080611 GSM1275871
    ## 8 SRR1039521 treated  N061011 GSM1275875

``` r
head(counts[,treated$id])
```

    ##   SRR1039509 SRR1039513 SRR1039517 SRR1039521
    ## 1        486        445       1097        604
    ## 2          0          0          0          0
    ## 3        523        371        781        509
    ## 4        258        237        447        324
    ## 5         81         66         94         74
    ## 6          0          0          0          0

``` r
treated.mean <- rowMeans(counts[,treated$id])

head(treated.mean)
```

    ## [1] 658.00   0.00 546.00 316.50  78.75   0.00

Setup the names:

``` r
names(control.mean) <- counts$ensgene
names(treated.mean) <- counts$ensgene
```

Combine this data for bookkeeping purposes\!

``` r
meancounts <- data.frame(control.mean, treated.mean)
```

Check your data using `colSums()` to show the sum of the mean counts
across all genes for each group:

``` r
colSums(meancounts)
```

    ## control.mean treated.mean 
    ##     23005324     22196524

Let’s visualize our data:

Since our data is very skewed (used the histogram to check), use log to
visualize the data.

``` r
plot.default(meancounts[,1], meancounts[,2], main="Control vs. Treated", ylab = "Treated", xlab = "Control", log = "xy", col = c("blue", "red"))
```

    ## Warning in xy.coords(x, y, xlabel, ylabel, log): 15032 x values <= 0 omitted
    ## from logarithmic plot

    ## Warning in xy.coords(x, y, xlabel, ylabel, log): 15281 y values <= 0 omitted
    ## from logarithmic plot

![](class_14_files/figure-gfm/unnamed-chunk-14-1.png)<!-- -->

Let’s remove our zero count genes because we can’t say anything about
them from this data set\!

Example:

``` r
x <- data.frame( c(1, 3, 10, 0),
                 c(1, 3, 0, 0) )

x
```

    ##   c.1..3..10..0. c.1..3..0..0.
    ## 1              1             1
    ## 2              3             3
    ## 3             10             0
    ## 4              0             0

``` r
# "Which" tells you where your answers' location are.
#which(x ==0)

# Let's idetify the row and column though.
unique(which(x == 0, arr.ind = TRUE)[,"row"])
```

    ## [1] 4 3

``` r
# Use unique to find where only 1 unique 0 value occurs.
```

Let’s find zero rows in our `meancounts` object.

``` r
to.rm <- unique(which(meancounts == 0, arr.ind = TRUE)[,"row"])

# Add a minus - to get data minus that group.
mycounts <- meancounts[-to.rm,]
nrow(mycounts)
```

    ## [1] 21817

Here we will calculate the log2foldchange, add it to our meancounts
data.frame, and inspect the results either with the `head()` or `View()`
function for example.

``` r
mycounts$log2fc <- log2(mycounts[,"treated.mean"]/mycounts[,"control.mean"])

head(mycounts$log2fc)
```

    ## [1] -0.45303916  0.06900279 -0.10226805 -0.30441833  0.35769358 -0.38194109

A common rule of thumb in the field is to use a log2fc of greater than
+2 as *upregulated* and log2fc of less than -2 as *down regulated*.

How many of our genes are up regulated during drug treatment using the
threshold we assigned?

``` r
up.ind <- mycounts$log2fc > 2
down.ind <- mycounts$log2fc < (-2)

# Upregulated.
sum(up.ind)
```

    ## [1] 250

``` r
# Downregulated.
sum(down.ind)
```

    ## [1] 367

# 2\. Let’s use DESeq2\!

Format your data so that DESeq2 will take it:

``` r
dds <- DESeqDataSetFromMatrix(countData=counts, 
                              colData=metadata, 
                              design=~dex, 
                              tidy=TRUE)
```

    ## converting counts to integer mode

    ## Warning in DESeqDataSet(se, design = design, ignoreRank): some variables in
    ## design formula are characters, converting to factors

``` r
dds
```

    ## class: DESeqDataSet 
    ## dim: 38694 8 
    ## metadata(1): version
    ## assays(1): counts
    ## rownames(38694): ENSG00000000003 ENSG00000000005 ... ENSG00000283120
    ##   ENSG00000283123
    ## rowData names(0):
    ## colnames(8): SRR1039508 SRR1039509 ... SRR1039520 SRR1039521
    ## colData names(4): id dex celltype geo_id

Now let’s act like you didn’t need to manually process the data for the
last 2 hours and just shove it into DESeq2\!

``` r
dds <- DESeq(dds)
```

    ## estimating size factors

    ## estimating dispersions

    ## gene-wise dispersion estimates

    ## mean-dispersion relationship

    ## final dispersion estimates

    ## fitting model and testing

``` r
res <- results(dds)

head(res)
```

    ## log2 fold change (MLE): dex treated vs control 
    ## Wald test p-value: dex treated vs control 
    ## DataFrame with 6 rows and 6 columns
    ##                          baseMean     log2FoldChange             lfcSE
    ##                         <numeric>          <numeric>         <numeric>
    ## ENSG00000000003  747.194195359907 -0.350703020686589 0.168245681332903
    ## ENSG00000000005                 0                 NA                NA
    ## ENSG00000000419  520.134160051965  0.206107766417874 0.101059218008481
    ## ENSG00000000457  322.664843927049 0.0245269479387461 0.145145067649738
    ## ENSG00000000460   87.682625164828 -0.147142049222083 0.257007253995456
    ## ENSG00000000938 0.319166568913118  -1.73228897394308  3.49360097648095
    ##                               stat             pvalue              padj
    ##                          <numeric>          <numeric>         <numeric>
    ## ENSG00000000003  -2.08446967499072 0.0371174658436988 0.163034808643509
    ## ENSG00000000005                 NA                 NA                NA
    ## ENSG00000000419   2.03947517583776 0.0414026263009679  0.17603166488093
    ## ENSG00000000457  0.168982303952169  0.865810560624016 0.961694238404893
    ## ENSG00000000460 -0.572520996721302  0.566969065259218  0.81584858763974
    ## ENSG00000000938 -0.495846258804286  0.620002884826012                NA

This is hard to look at. Let’s summarize the basic tallies using the
summary function\!

``` r
summary(res)
```

    ## 
    ## out of 25258 with nonzero total read count
    ## adjusted p-value < 0.1
    ## LFC > 0 (up)       : 1563, 6.2%
    ## LFC < 0 (down)     : 1188, 4.7%
    ## outliers [1]       : 142, 0.56%
    ## low counts [2]     : 9971, 39%
    ## (mean count < 10)
    ## [1] see 'cooksCutoff' argument of ?results
    ## [2] see 'independentFiltering' argument of ?results

## Volcano plots.

Plot of log2fc versus p value.

``` r
plot(res$log2FoldChange, -log(res$padj), col = "gray")
abline(v=c(-2, +2), lty = 2)
abline(h=-log(0.05), lty = 2)
```

![](class_14_files/figure-gfm/unnamed-chunk-24-1.png)<!-- -->

Here’s just the copy + paste of the nice graph\!

``` r
# Setup our custom point color vector 
mycols <- rep("gray", nrow(res))
mycols[ abs(res$log2FoldChange) > 2 ]  <- "red" 

inds <- (res$padj < 0.01) & (abs(res$log2FoldChange) > 2 )
mycols[ inds ] <- "blue"

# Volcano plot with custom colors 
plot( res$log2FoldChange,  -log(res$padj), 
 col=mycols, ylab="-Log(P-value)", xlab="Log2(FoldChange)" )

# Cut-off lines
abline(v=c(-2,2), col="gray", lty=2)
abline(h=-log(0.1), col="gray", lty=2)
```

![](class_14_files/figure-gfm/unnamed-chunk-25-1.png)<!-- -->
