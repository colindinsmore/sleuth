---
layout: page
title: "Getting Started"
description: ""
group: navigation
---
{% include JB/setup %}

The text below is the __sleuth__ `R` vignette. You can also access it in `R` by
typing `vignette('intro', package = 'sleuth')`


<div id="overview" class="section level2">
<h2>Overview</h2>
<p><strong>sleuth</strong> is a tool for the analysis and comparison of multiple related RNA-Seq experiments. Key features include:</p>
<ol style="list-style-type: decimal">
<li>The use of boostraps to ascertain and correct for technical variation in experiments.</li>
<li>Implemention of a flexible response error measurement model for inference that allows for a multitude of experimental designs.</li>
<li>Interactive plots that enable real-time exploratory data analysis.</li>
</ol>
<p>To use <strong>sleuth</strong>, RNA-Seq data must first be quantified with <a href="http://pachterlab.github.io/kallisto/"><strong>kallisto</strong></a>, which is a program for <em>very</em> fast RNA-Seq quantification based on pseudo-alignment. An important feature of <strong>kallisto</strong> is that it outputs bootstraps along with the estimates of transcript abundances. These can serve as proxies for technical replicates, allowing for an ascertainment of the variability in estimates due to the random processes underlying RNA-Seq as well as the statistical procedure of read assignment. <strong>kallisto</strong> can quantify 30 million human reads in less than 3 minutes on a Mac desktop computer using only the read sequences and a transcriptome index that itself takes less than 10 minutes to build. <strong>sleuth</strong> has also been designed to be lightweight and fast, and therefore RNA-Seq analysis with <strong>kallisto</strong> and <strong>sleuth</strong> is tractable on a laptop computer in a matter of minutes.</p>
<p>The model <strong>sleuth</strong> uses for performing differential analysis is a general linear model where there is error in the response. Formally, in the case of two conditions being assayed, for a transcript <span class="math inline">\(t\)</span> in a sample <span class="math inline">\(i\)</span>, the (log) “true” unobserved abundance <span class="math inline">\(y_i\)</span> measured in read counts is modeled by</p>
<p><span class="math display">\[ y_{t,i} = \beta_{t,0} + \beta_{t,1} x_{t,i} + \epsilon_{t,i} \]</span></p>
<p>where <span class="math inline">\(x_{t,i}\)</span> is an indicator variable describing the condition, <span class="math inline">\(\beta_{t,0}\)</span> and <span class="math inline">\(\beta_{t,1}\)</span> are parameters of the model and <span class="math inline">\(\epsilon_{t,i}\)</span> is biological “noise”. However, conditioned on <span class="math inline">\(x_i\)</span> and <span class="math inline">\(y_i\)</span> the estimated number of counts from the observations in the experiment is given by</p>
<p><span class="math display">\[ d_{t,i} = y_{t,i} + \zeta_{t,i} \]</span></p>
<p>where <span class="math inline">\(\zeta_{t,i}\)</span> represents technical “noise”, i.e. uncertainty in the measurement due to effects other than biological variability. The <strong>sleuth</strong> model incorporates the assumptions that the expectation <span class="math inline">\(E(\zeta_{t,i}|y_{t,i}) = 0\)</span>, that <span class="math inline">\(E(d_{t,i}) = \beta_{t,0} + \beta_{t,1} x_{t,i}\)</span> and that the response error is <em>additive</em>, i.e. if $ the variance V(_{t,i}) = _t^2$ and the variance <span class="math inline">\(V(\zeta_{t,i}|y_{t,i}) = \sigma_t^2\)</span> then the variance <span class="math inline">\(V(d_{t,i}) = \sigma_{t}^2 + \tau_{t}^2\)</span>. <strong>sleuth</strong> makes use of the boostraps from <strong>kallisto</strong> to estimate the <span class="math inline">\(\tau_t^2\)</span>, and after subtracting the estimated technical variance the <span class="math inline">\(\sigma_t^2\)</span> are estimated via a shrinkage procedure similar to that used in <a href="http://www.genomebiology.com/2014/15/2/R29">Limma Voom</a>.</p>
<p>More generally, <strong>sleuth</strong> can be used to analyze multiple conditions organized in complex experimental designs. In the general case the unobserved model is given by</p>
<p><span class="math display">\[ Y_t = X_t \beta_t + \epsilon_t \]</span></p>
<p>where <span class="math inline">\(t\)</span> is a transcript, <span class="math inline">\(Y_t\)</span> is a vector of length <span class="math inline">\(n\)</span> where <span class="math inline">\(n\)</span> is the number of samples, <span class="math inline">\(X_t\)</span> is an <span class="math inline">\(n \times p\)</span> matrix where <span class="math inline">\(p\)</span> is the number of covariates, <span class="math inline">\(\beta_t\)</span> represents the effect sizes and is a vector of length <span class="math inline">\(p\)</span>, and <span class="math inline">\(\epsilon_t\)</span> is a vector of length <span class="math inline">\(n\)</span>. The observed response is described by</p>
<p><span class="math display">\[ D_t = Y_t + \zeta_t \]</span></p>
<p>where <span class="math inline">\(\zeta_t\)</span> and <span class="math inline">\(D_t\)</span> are vectors of length <span class="math inline">\(n\)</span>.</p>
<p><strong>sleuth</strong> has been designed to facilitate the exploration of RNA-Seq data by utilizing the <a href="http://shiny.rstudio.com">Shiny</a> web application framework by RStudio. The worked example below illustrates how to load data into <strong>sleuth</strong> and how to open Shiny plots for exploratory data analysis. The code underlying all plots is available via the Shiny interface so that analyses can be fully “open source”.</p>
</div>
<div id="installation" class="section level2">
<h2>Installation</h2>
<p>To install <strong>sleuth</strong> start <a href="https://www.r-project.org">R</a> and first install <code>rhdf5</code> by typing:</p>
<div class="sourceCode"><pre class="sourceCode r"><code class="sourceCode r"><span class="kw">source</span>(<span class="st">&quot;http://bioconductor.org/biocLite.R&quot;</span>)
<span class="kw">biocLite</span>(<span class="st">&quot;rhdf5&quot;</span>)</code></pre></div>
<p>Then install devtools by typing</p>
<div class="sourceCode"><pre class="sourceCode r"><code class="sourceCode r"><span class="kw">install.packages</span>(<span class="st">&quot;devtools&quot;</span>)</code></pre></div>
<p>and install <strong>sleuth</strong> by typing</p>
<div class="sourceCode"><pre class="sourceCode r"><code class="sourceCode r">devtools::<span class="kw">install_github</span>(<span class="st">&quot;pachterlab/sleuth&quot;</span>)</code></pre></div>
<p>Next load <strong>sleuth</strong> with</p>
<div class="sourceCode"><pre class="sourceCode r"><code class="sourceCode r"><span class="kw">library</span>(<span class="st">&quot;sleuth&quot;</span>)</code></pre></div>
</div>
<div id="example" class="section level2">
<h2>Example</h2>
<p>To explain how to use <strong>sleuth</strong> we provide an example based on the data in the “Cuffdiff2 paper”:</p>
<ul>
<li><a href="http://www.nature.com/nbt/journal/v31/n1/full/nbt.2450.html">Differential analysis of gene regulation at transcript resolution with RNA-seq</a> by Cole Trapnell, David G Henderickson, Martin Savageau, Loyal Goff, John L Rinn and Lior Pachter, Nature Biotechnology <strong>31</strong>, 46–53 (2013).</li>
</ul>
<p>The human fibroblast RNA-Seq data for the paper is available on GEO at accession <a href="http://www.ncbi.nlm.nih.gov/geo/query/acc.cgi?acc=GSE37704">GSE37704</a>. The samples to be analyzed are the six samples LFB_scramble_hiseq_repA, LFB_scramble_hiseq_repB, LFB_scramble_hiseq_repC, LFB_HOXA1KD_hiseq_repA, LFB_HOXA1KD_hiseq_repA, and LFB_HOXA1KD_hiseq_repC. These are three biological replicates in each of two conditions (scramble and HoxA1 knockdown) that will be compared with <strong>sleuth</strong>.</p>
<p>To analyze the data, first download the raw reads, install <strong>kallisto</strong> and then quantify the data with boostraps as described <a href="http://pachterlab.github.io/kallisto/starting.html">here</a>. This step can be skipped for the purposes of the vignette, by downloading the <strong>kallisto</strong> processsed data directly by clicking <a href="http://bio.math.berkeley.edu/sleuth/cuffdiff2/cuffdiff2_data_kallisto_results.zip">here</a>.</p>
<p>The first step in a <strong>sleuth</strong> analysis is to specify where the <strong>kallisto</strong> results are stored. Begin by storing the base directory of the results in a variable,</p>
<div class="sourceCode"><pre class="sourceCode r"><code class="sourceCode r">base_dir &lt;-<span class="st"> &quot;~/Downloads/cuffdiff2_data_kallisto_results&quot;</span></code></pre></div>
<p>Next get the list of sample IDs with</p>
<div class="sourceCode"><pre class="sourceCode r"><code class="sourceCode r">sample_id &lt;-<span class="st"> </span><span class="kw">dir</span>(<span class="kw">file.path</span>(base_dir,<span class="st">&quot;results&quot;</span>))</code></pre></div>
<p>The result can be displayed by typing</p>
<div class="sourceCode"><pre class="sourceCode r"><code class="sourceCode r">sample_id</code></pre></div>
<pre><code>## [1] &quot;SRR493366&quot; &quot;SRR493367&quot; &quot;SRR493368&quot; &quot;SRR493369&quot; &quot;SRR493370&quot; &quot;SRR493371&quot;</code></pre>
<p>In the box above, lines beginning with ## show the output of the command (in what follows we include the output that should appear with each command).</p>
<p>A list of paths to the <strong>kallisto</strong> results indexed by the sample IDs is collated with</p>
<div class="sourceCode"><pre class="sourceCode r"><code class="sourceCode r">kal_dirs &lt;-<span class="st"> </span><span class="kw">sapply</span>(sample_id, function(id) <span class="kw">file.path</span>(base_dir, <span class="st">&quot;results&quot;</span>, id, <span class="st">&quot;kallisto&quot;</span>))
kal_dirs</code></pre></div>
<pre><code>##                                                                SRR493366 
## &quot;~/Downloads/cuffdiff2_data_kallisto_results/results/SRR493366/kallisto&quot; 
##                                                                SRR493367 
## &quot;~/Downloads/cuffdiff2_data_kallisto_results/results/SRR493367/kallisto&quot; 
##                                                                SRR493368 
## &quot;~/Downloads/cuffdiff2_data_kallisto_results/results/SRR493368/kallisto&quot; 
##                                                                SRR493369 
## &quot;~/Downloads/cuffdiff2_data_kallisto_results/results/SRR493369/kallisto&quot; 
##                                                                SRR493370 
## &quot;~/Downloads/cuffdiff2_data_kallisto_results/results/SRR493370/kallisto&quot; 
##                                                                SRR493371 
## &quot;~/Downloads/cuffdiff2_data_kallisto_results/results/SRR493371/kallisto&quot;</code></pre>
<p>The next step is to load an auxillary table that describes the experimental design and the relationship between the kallisto directories and the samples:</p>
<div class="sourceCode"><pre class="sourceCode r"><code class="sourceCode r">s2c &lt;-<span class="st"> </span><span class="kw">read.table</span>(<span class="kw">file.path</span>(base_dir,<span class="st">&quot;hiseq_info.txt&quot;</span>), <span class="dt">header =</span> <span class="ot">TRUE</span>, <span class="dt">stringsAsFactors=</span><span class="ot">FALSE</span>)
s2c &lt;-<span class="st"> </span>dplyr::<span class="kw">select</span>(s2c, <span class="dt">sample =</span> run_accession, condition)
s2c</code></pre></div>
<pre><code>##      sample condition
## 1 SRR493366  scramble
## 2 SRR493367  scramble
## 3 SRR493368  scramble
## 4 SRR493369   HOXA1KD
## 5 SRR493370   HOXA1KD
## 6 SRR493371   HOXA1KD</code></pre>
<p>Now the “sleuth object” can be constructed. This requires three commands that (1) load the kallisto processed data into the object (2) estimate parameters for the <strong>sleuth</strong> response error measurement model and (3) perform differential analyis (testing). On a laptop the three steps should take about 2 minutes altogether.</p>
<p>First type</p>
<div class="sourceCode"><pre class="sourceCode r"><code class="sourceCode r">so &lt;-<span class="st"> </span><span class="kw">sleuth_prep</span>(kal_dirs, s2c, ~<span class="st"> </span>condition)</code></pre></div>
<pre><code>## reading in kallisto results
## ......
## normalizing est_counts
## 42193 targets passed the filter
## normalizing tpm
## normalizing bootstrap samples</code></pre>
<p>then</p>
<div class="sourceCode"><pre class="sourceCode r"><code class="sourceCode r">so &lt;-<span class="st"> </span><span class="kw">sleuth_fit</span>(so)</code></pre></div>
<pre><code>## summarizing bootstraps
## fitting measurement error models
## shrinkage estimation
## computing variance of betas</code></pre>
<p>and finally</p>
<div class="sourceCode"><pre class="sourceCode r"><code class="sourceCode r">so &lt;-<span class="st"> </span><span class="kw">sleuth_test</span>(so, <span class="dt">which_beta =</span> <span class="st">'conditionscramble'</span>)</code></pre></div>
<p>In general, one can see the possible tests that could be performed using the <code>which_beta</code> parameter in <code>sleuth_test</code> and examining the coefficients:</p>
<div class="sourceCode"><pre class="sourceCode r"><code class="sourceCode r"><span class="kw">models</span>(so)</code></pre></div>
<pre><code>## [  full  ]
## formula:  ~condition 
## coefficients:
##  (Intercept)
##      conditionscramble
## tests:
##  conditionscramble</code></pre>
<p>At this point the sleuth object constructed from the kallisto runs has information about the data, the experimental design, the <strong>kallisto</strong> estimates, the model fit, and the testing. In other words it contains the entire analysis of the data. There is, however, one piece of information that can be useful to add in, but that is optional. In reading the kallisto output <strong>sleuth</strong> has no information about <em>genes</em>, but this can be added allowing for searching and analysis by gene instead of transcript.</p>
<p>Since the example was constructed with the ENSEMBL human transcriptome, we will add gene names from ENSEMBL using biomaRt (there are other ways to do this as well):</p>
<p>First, install biomaRt with</p>
<div class="sourceCode"><pre class="sourceCode r"><code class="sourceCode r"><span class="kw">source</span>(<span class="st">&quot;http://bioconductor.org/biocLite.R&quot;</span>)
<span class="kw">biocLite</span>(<span class="st">&quot;biomaRt&quot;</span>)</code></pre></div>
<p>Then collect gene names with</p>
<div class="sourceCode"><pre class="sourceCode r"><code class="sourceCode r">mart &lt;-<span class="st"> </span>biomaRt::<span class="kw">useMart</span>(<span class="dt">biomart =</span> <span class="st">&quot;ensembl&quot;</span>, <span class="dt">dataset =</span> <span class="st">&quot;hsapiens_gene_ensembl&quot;</span>)</code></pre></div>
<pre><code>## Creating a generic function for 'nchar' from package 'base' in package 'S4Vectors'</code></pre>
<p>and add them into the <strong>sleuth</strong> table with</p>
<pre><code>t2g &lt;- biomaRt::getBM(attributes = c(&quot;ensembl_transcript_id&quot;, &quot;ensembl_gene_id&quot;,
    &quot;external_gene_name&quot;), mart = mart)
t2g &lt;- dplyr::rename(t2g, target_id = ensembl_transcript_id,
  ens_gene = ensembl_gene_id, ext_gene = external_gene_name)
so &lt;- sleuth_prep(kal_dirs, s2c, ~ condition, target_mapping = t2g)
so &lt;- sleuth_fit(so)
so &lt;- sleuth_test(so, which_beta = 'conditionscramble')</code></pre>
<p>This addition of metadata to transcript IDs is very general, and can be used to add in other information.</p>
<p>The best way to view the results is to generate the Shiny webpage that allows for exploratory data analysis:</p>
<pre><code>sleuth_live(so)</code></pre>
<p>To generate a table of results for analysis within R type</p>
<div class="sourceCode"><pre class="sourceCode r"><code class="sourceCode r">results_table &lt;-<span class="st"> </span><span class="kw">sleuth_results</span>(so, <span class="st">'conditionscramble'</span>) </code></pre></div>
</div>



<!-- dynamically load mathjax for compatibility with self-contained -->
<script>
  (function () {
    var script = document.createElement("script");
    script.type = "text/javascript";
    script.src  = "https://cdn.mathjax.org/mathjax/latest/MathJax.js?config=TeX-AMS-MML_HTMLorMML";
    document.getElementsByTagName("head")[0].appendChild(script);
  })();
</script>