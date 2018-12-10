---
layout: post
title: "Volcano plot to visualize gene expression data using Python"
date:   2018-11-30 02:15:18
author: Renesh Bedre
description: "Advanced Bioinformatics"
permalink: blog/volcano.html
comments: true
---

**<span style="color:#33a8ff">What is Volcano plot?</span>**
 - 2-dimensional (2D) scatter plot having a shape like volcano
 - Used to visualize and identify statistically significant gene expression changes from two different conditions (eg. normal vs. 
   treated) in terms of log fold change (X-axis) and P-value (Y-axis)
   
**<span style="color:#33a8ff">Applications</span>**   
 - Easy to visualize expression of thousands of genes obtained from omics research (eg. Transcriptomics, genomics, 
   proteomics etc.) and pinpoint genes with significant changes 
   
**<span style="color:#33a8ff">How to create Volcano plot in Python?</span>**

For generating volcano plot, I have used gene expression data published in Bedre et al. 2016 to identify statistically significantly
induced or downregulated genes in response to salt stress in <i>Spartina alterniflora</i> 
(<a href="https://bmcgenomics.biomedcentral.com/articles/10.1186/s12864-016-3017-3">Read paper</a>). 
Here's you can download gene expression dataset used for generating volcano plot: 
[dataset]({{"/myfiles/volcano/SaLR_DEGseq.txt" target="_blank""| absolute_url }})

```python
# I am using Python3
# load required packages
# make sure you have installed required packages
import pandas as pd
import matplotlib.pyplot as plt
import numpy as np

# load data file
d = pd.read_csv("https://reneshbedre.github.io/myfiles/volcano/SaLR_DEGseq.txt", sep="\t")

# to see first few lines of table and get table dimensions
print(d.head()) # first five lines
print(d.info()) # table information including dimensions

# this gene expression dataset has 19025 genes
# we need to add color column to visualize up, downregulated and intermediate genes
# here you can also change the log2 fold change (log2FC) and P-value (p-value) numbers 
# as per your need
d.loc[(d['log2FC'] >= 1) & (d['p-value'] < 0.05), 'color'] = "green"  # upregulated
d.loc[(d['log2FC'] <=- 1) & (d['p-value'] < 0.05), 'color'] = "red"   # downregulated
d['color'].fillna('grey', inplace=True) # intermediate

# to reduce the noise, filter out genes with low expression counts across treatments 
# (say, < 10 normalized expression count)
# you can change this number as per your requirement and based on expression unit 
# value1 and value2 represents counts for stress and control treatments respectively
d = d.loc[(d['value1'] >= 10) & (d['value2'] >= 10)]

# Now, data is ready for volcano plot
# In volcano plot, Y-axis is -log10 normalized P-value
# NOTE: Here you may get "RuntimeWarning: divide by zero encountered in log10" where 
# there is 0 P-value. To avoid this warning replace 0 with smallest non-zero P-value. 
# To get smallest non-zero P-value, you can use d.nsmallest(2, 'p-value')
# replace 0 P-value with lowest non-zero P-value
# convert P-value to -log10 normalized P-value
d['logpv']=-(np.log10(d['p-value']))

# plot 
plt.scatter(d['log2FC'], d['logpv'], c=d['color'])
plt.xlabel('log2 Fold Change',fontsize=15, fontname="sans-serif", fontweight="bold")
plt.ylabel('-log10(P-value)', fontsize=15, fontname="sans-serif", fontweight="bold")
plt.xticks(fontsize=12, fontname="sans-serif")
plt.yticks(fontsize=12, fontname="sans-serif")
plt.show()
```

Generated volcano plot by above code (green: upregulated and red: downregulated genes),

![screenshot]({{ "/myfiles/volcano/SaLR_DEGseq.png" | absolute_url }})

If you would like to add specific gene names in volcano plot, use following code,

```python
# plot 
plt.scatter(d['log2FC'], d['logpv'], c=d['color'])
plt.xlabel('log2 Fold Change',fontsize=15, fontname="sans-serif", fontweight="bold")
plt.ylabel('-log10(P-value)', fontsize=15, fontname="sans-serif", fontweight="bold")
plt.xticks(fontsize=12, fontname="sans-serif")
plt.yticks(fontsize=12, fontname="sans-serif")
# I have added two gene names. You can add multiple gene names to corresponding point  
# using axis coordinates
plt.text(4.09, 53.65, "CPuORF26")
plt.text(-2.23, 39.73, "CIA")
plt.show()
# To save volcano plot to file, replace  plt.show() with following line
plt.savefig('volcano.png', format='png', bbox_inches='tight', dpi=300)
```

Generated volcano plot by adding above code,

![screenshot]({{ "/myfiles/volcano/SaLR_DEGseq_text.png" | absolute_url }})

To create a inverted volcano plot,

```python
# plot 
plt.scatter(d['log2FC'], d['logpv'], c=d['color'])
# create inverted Y-axis
plt.gca().invert_yaxis()
plt.xlabel('log2 Fold Change',fontsize=15, fontname="sans-serif", fontweight="bold")
plt.ylabel('-log10(P-value)', fontsize=15, fontname="sans-serif", fontweight="bold")
plt.xticks(fontsize=12, fontname="sans-serif")
plt.yticks(fontsize=12, fontname="sans-serif")
# I have added two gene names. You can add multiple gene names to corresponding point  
# using axis coordinates
plt.text(4.09, 53.65, "CPuORF26")
plt.text(-2.23, 39.73, "CIA")
plt.show()
```

Generated inverted volcano plot by adding above code,

![screenshot]({{ "/myfiles/volcano/SaLR_DEGseq_text_invert.png" | absolute_url }})


**<span style="color:#33a8ff">How to cite?</span>**
Bedre, R. “Volcano plot to visualize gene expression data using Python” Renesh Bedre (blog), November 30, 2018, 
https://reneshbedre.github.io/blog/volcano.html.

<span style="color:#9e9696">If you have any questions, comments or recommendations, please email me at 
<b>reneshbe@gmail.com</b></span>