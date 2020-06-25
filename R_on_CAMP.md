---


---

<blockquote>
<h1 id="r-on-camp-hpc">R on CAMP HPC</h1>
</blockquote>
<h1 id="run-rscript-on-camp-in-batch-mode">Run Rscript on CAMP in batch mode</h1>
<p><a href="https://quantdev.ssri.psu.edu/sites/qdev/files/High_Performance_Computing_in_R_Tutorial.html">https://quantdev.ssri.psu.edu/sites/qdev/files/High_Performance_Computing_in_R_Tutorial.html</a><br>
<a href="https://support.rstudio.com/hc/en-us/articles/218012917-How-to-run-R-scripts-from-the-command-line">https://support.rstudio.com/hc/en-us/articles/218012917-How-to-run-R-scripts-from-the-command-line</a><br>
<a href="https://www.changyuchang.name/2018/06/13/run-r-script-on-hpc/">https://www.changyuchang.name/2018/06/13/run-r-script-on-hpc/</a><br>
R scripts requiring lots of memory may not work on an interactive node and need to be submitted as sbatch. They can also be run MUCH faster on CAMP as can use multiple nodes &amp; multiple cores.</p>
<ol>
<li>Create the R script and save it in the appropriate location on CAMP with an appropriate name</li>
<li>Create .txt file with tasks for the bash enivronment. Each line is a different independent task. Within a task, separate commands with colon on one line e.g.</li>
</ol>
<pre class=" language-bash"><code class="prism  language-bash">ml R<span class="token punctuation">;</span> Rscript name.R
</code></pre>
<p>Save the .txt file with appropriate location &amp; name</p>
<ol start="3">
<li>Run the script in CAMP command line:</li>
</ol>
<pre class=" language-bash"><code class="prism  language-bash"><span class="token comment"># ml R/3.6.0-foss-2019a</span>
ml Anaconda2
ml pandoc
<span class="token comment">#source activate rtest</span>

sbatch -N 1 -c 8 --mem 64G -t 12:00:00 --wrap<span class="token operator">=</span><span class="token string">"Rscript ~/home/projects/vcp_fractionation/expression/deseq2/prepare_DESeq2.R"</span>

sbatch -N 1 -c 8 --mem<span class="token operator">=</span>50G -t 2:00:00 --wrap<span class="token operator">=</span><span class="token string">"Rscript ~/home/projects/vcp_fractionation/expression/deseq2/DESeq2_analysis.R"</span>

sbatch -N 1 -c 8 --mem<span class="token operator">=</span>0 -t 12:00:00 --wrap<span class="token operator">=</span><span class="token string">"Rscript ~/home/projects/vcp_fractionation/expression/deseq2/DESeq2_GO_analysis.R"</span>


sbatch -N 1 -c 8 --mem<span class="token operator">=</span>100G -t 12:00:00 --wrap<span class="token operator">=</span><span class="token string">"Rscript ~/home/projects/vcp_fractionation/expression/sleuth/sleuth.R"</span>

sbatch -N 1 -c 8 --mem<span class="token operator">=</span>0 -t 12:00:00 --wrap<span class="token operator">=</span><span class="token string">"Rscript ~/home/projects/vcp_fractionation/expression/sleuth/sleuth_GO_analysis.R"</span>




sbatch -N 1 -c 8 --mem<span class="token operator">=</span>0 -t 12:00:00 --wrap<span class="token operator">=</span><span class="token string">"Rscript ~/home/projects/scrnaseq-astrocyte-motor-neuron/expression/seurat/scRNAseq_astrocytes.vcp.R"</span>


sbatch --cpus-per-task<span class="token operator">=</span>8 --mem<span class="token operator">=</span>1500G -N 1 --partition<span class="token operator">=</span>hmem --time<span class="token operator">=</span>24:00:00 --wrap<span class="token operator">=</span><span class="token string">"Rscript ~/home/projects/scrnaseq-astrocyte-motor-neuron/expression/seurat/scRNAseq_seurat.R"</span>

sbatch -N 1 -c 8 --mem<span class="token operator">=</span>0 -t 12:00:00 --wrap<span class="token operator">=</span><span class="token string">"Rscript ~/home/projects/scrnaseq-astrocyte-motor-neuron/expression/seurat/scRNAseq_seurat.R"</span>


sbatch --dependency<span class="token operator">=</span>afterany:7925767 --cpus-per-task<span class="token operator">=</span>8 --mem<span class="token operator">=</span>1500G -N 1 --partition<span class="token operator">=</span>hmem --time<span class="token operator">=</span>12:00:00 --wrap<span class="token operator">=</span><span class="token string">"Rscript ~/home/projects/scrnaseq-astrocyte-motor-neuron/expression/seurat/scRNAseq_astrocytes.allRmd.R"</span> 

</code></pre>
<ol start="4">
<li>Check on submission with <code>squeue</code> or <code>myq</code> alias</li>
</ol>
<h2 id="run-rmd-rmarkdown-script-on-camp-in-batch-mode">Run Rmd rmarkdown script on CAMP in batch mode</h2>
<p>Finalise the Rmd file. Ensure it loads rmarkdown &amp; knitr:</p>
<pre class=" language-r"><code class="prism  language-r">library<span class="token punctuation">(</span>rmarkdown<span class="token punctuation">)</span>
library<span class="token punctuation">(</span>knitr<span class="token punctuation">)</span>
</code></pre>
<p>Open CAMP terminal</p>
<pre class=" language-r"><code class="prism  language-r">ml Anaconda2
ml pandoc
<span class="token comment">#source activate rtest</span>
</code></pre>
<h3 id="using-knitr-argument-in-rscript">Using knitr argument in Rscript</h3>
<pre class=" language-bash"><code class="prism  language-bash">sbatch -N 1 -c 10 --mem<span class="token operator">=</span>0 -t 48:00:00 --wrap<span class="token operator">=</span><span class="token string">"Rscript -e 'library(knitr); knit("</span>~/home/projects/astrocyte-fractionation-bulk-rnaseq/scripts/sgseq_denovo.Rmd<span class="token string">")'"</span>
</code></pre>
<h3 id="write-a-single-line-rscript-file-that-points-to-rmd-file">write a single line Rscript file that points to Rmd file:</h3>
<p>Alternative option to knitr argument<br>
<a href="http://www.nathalievialaneix.eu/doc/pdf/tutoR_cluster.pdf">http://www.nathalievialaneix.eu/doc/pdf/tutoR_cluster.pdf</a></p>
<pre class=" language-r"><code class="prism  language-r">rmarkdown<span class="token operator">::</span>render<span class="token punctuation">(</span><span class="token string">"~/home/projects/vcp_fractionation/expression/deseq2/DGE_GO_plots.Rmd"</span><span class="token punctuation">,</span> params<span class="token operator">=</span>list<span class="token punctuation">(</span>text <span class="token operator">=</span> <span class="token string">"Hola!"</span><span class="token punctuation">)</span><span class="token punctuation">)</span>
</code></pre>
<p>run this 1 line Rscript:</p>
<pre class=" language-r"><code class="prism  language-r">sbatch <span class="token operator">-</span><span class="token operator">-</span>cpus<span class="token operator">-</span>per<span class="token operator">-</span>task<span class="token operator">=</span><span class="token number">8</span> <span class="token operator">-</span><span class="token operator">-</span>mem<span class="token operator">=</span>1500G <span class="token operator">-</span>N <span class="token number">1</span> <span class="token operator">-</span><span class="token operator">-</span>partition<span class="token operator">=</span>hmem <span class="token operator">-</span><span class="token operator">-</span>time<span class="token operator">=</span><span class="token number">12</span><span class="token operator">:</span><span class="token number">00</span><span class="token operator">:</span><span class="token number">00</span> <span class="token operator">-</span><span class="token operator">-</span>wrap<span class="token operator">=</span><span class="token string">"Rscript ~/home/projects/vcp_fractionation/expression/deseq2/DGE_plotsRmd.R"</span> 

sbatch <span class="token operator">-</span><span class="token operator">-</span>dependency<span class="token operator">=</span>afterany<span class="token operator">:</span><span class="token number">7846377</span> <span class="token operator">-</span>N <span class="token number">1</span> <span class="token operator">-</span>c <span class="token number">8</span> <span class="token operator">-</span><span class="token operator">-</span>mem<span class="token operator">=</span><span class="token number">0</span> <span class="token operator">-</span>t <span class="token number">12</span><span class="token operator">:</span><span class="token number">00</span><span class="token operator">:</span><span class="token number">00</span> <span class="token operator">-</span><span class="token operator">-</span>wrap<span class="token operator">=</span><span class="token string">"Rscript ~/home/projects/vcp_fractionation/expression/deseq2/GO_plotsRmd.R"</span> 

sbatch <span class="token operator">-</span>N <span class="token number">1</span> <span class="token operator">-</span>c <span class="token number">8</span> <span class="token operator">-</span><span class="token operator">-</span>mem<span class="token operator">=</span><span class="token number">0</span> <span class="token operator">-</span>t <span class="token number">12</span><span class="token operator">:</span><span class="token number">00</span><span class="token operator">:</span><span class="token number">00</span> <span class="token operator">-</span><span class="token operator">-</span>wrap<span class="token operator">=</span><span class="token string">"Rscript ~/home/projects/vcp_fractionation/expression/sleuth/transcript_GO_plots.Rmd.R"</span> 



sbatch <span class="token operator">-</span>N <span class="token number">1</span> <span class="token operator">-</span>c <span class="token number">8</span> <span class="token operator">-</span><span class="token operator">-</span>mem<span class="token operator">=</span>40G <span class="token operator">-</span>t <span class="token number">12</span><span class="token operator">:</span><span class="token number">00</span><span class="token operator">:</span><span class="token number">00</span> <span class="token operator">-</span><span class="token operator">-</span>wrap<span class="token operator">=</span><span class="token string">"Rscript ~/home/projects/scrnaseq-astrocyte-motor-neuron/expression/seurat/scripts/scRNAseq_seurat_ctrl2Rmd.R"</span> <span class="token operator">-</span><span class="token operator">-</span>mail<span class="token operator">-</span>type<span class="token operator">=</span>ALL<span class="token punctuation">,</span>ARRAY_TASKS <span class="token operator">-</span><span class="token operator">-</span>mail<span class="token operator">-</span>user<span class="token operator">=</span>oliver.ziff<span class="token operator">@</span>crick.ac.uk

sbatch <span class="token operator">-</span>N <span class="token number">1</span> <span class="token operator">-</span>c <span class="token number">8</span> <span class="token operator">-</span><span class="token operator">-</span>mem<span class="token operator">=</span>28G <span class="token operator">-</span>t <span class="token number">12</span><span class="token operator">:</span><span class="token number">00</span><span class="token operator">:</span><span class="token number">00</span> <span class="token operator">-</span><span class="token operator">-</span>wrap<span class="token operator">=</span><span class="token string">"Rscript ~/home/projects/scrnaseq-astrocyte-motor-neuron/expression/seurat/scRNAseq_seurat_ctrl3Rmd.R"</span> <span class="token operator">-</span><span class="token operator">-</span>mail<span class="token operator">-</span>type<span class="token operator">=</span>ALL<span class="token punctuation">,</span>ARRAY_TASKS <span class="token operator">-</span><span class="token operator">-</span>mail<span class="token operator">-</span>user<span class="token operator">=</span>oliver.ziff<span class="token operator">@</span>crick.ac.uk

sbatch <span class="token operator">-</span>N <span class="token number">1</span> <span class="token operator">-</span>c <span class="token number">8</span> <span class="token operator">-</span><span class="token operator">-</span>mem<span class="token operator">=</span>28G <span class="token operator">-</span>t <span class="token number">12</span><span class="token operator">:</span><span class="token number">00</span><span class="token operator">:</span><span class="token number">00</span> <span class="token operator">-</span><span class="token operator">-</span>wrap<span class="token operator">=</span><span class="token string">"Rscript ~/home/projects/scrnaseq-astrocyte-motor-neuron/expression/seurat/scRNAseq_seurat_gliaRmd.R"</span> <span class="token operator">-</span><span class="token operator">-</span>mail<span class="token operator">-</span>type<span class="token operator">=</span>ALL<span class="token punctuation">,</span>ARRAY_TASKS <span class="token operator">-</span><span class="token operator">-</span>mail<span class="token operator">-</span>user<span class="token operator">=</span>oliver.ziff<span class="token operator">@</span>crick.ac.uk

sbatch <span class="token operator">-</span>N <span class="token number">1</span> <span class="token operator">-</span>c <span class="token number">8</span> <span class="token operator">-</span><span class="token operator">-</span>mem<span class="token operator">=</span>28G <span class="token operator">-</span>t <span class="token number">12</span><span class="token operator">:</span><span class="token number">00</span><span class="token operator">:</span><span class="token number">00</span> <span class="token operator">-</span><span class="token operator">-</span>wrap<span class="token operator">=</span><span class="token string">"Rscript ~/home/projects/scrnaseq-astrocyte-motor-neuron/expression/seurat/scRNAseq_seurat_cb1dRmd.R"</span> <span class="token operator">-</span><span class="token operator">-</span>mail<span class="token operator">-</span>type<span class="token operator">=</span>ALL<span class="token punctuation">,</span>ARRAY_TASKS <span class="token operator">-</span><span class="token operator">-</span>mail<span class="token operator">-</span>user<span class="token operator">=</span>oliver.ziff<span class="token operator">@</span>crick.ac.uk


sbatch <span class="token operator">-</span>N <span class="token number">1</span> <span class="token operator">-</span>c <span class="token number">8</span> <span class="token operator">-</span><span class="token operator">-</span>mem<span class="token operator">=</span><span class="token number">0</span> <span class="token operator">-</span>t <span class="token number">24</span><span class="token operator">:</span><span class="token number">00</span><span class="token operator">:</span><span class="token number">00</span> <span class="token operator">-</span><span class="token operator">-</span>wrap<span class="token operator">=</span><span class="token string">"Rscript ~/home/projects/scrnaseq-astrocyte-motor-neuron/expression/seurat/scripts/scRNAseq_seurat_separateRmd.R"</span> <span class="token operator">-</span><span class="token operator">-</span>mail<span class="token operator">-</span>type<span class="token operator">=</span>ALL<span class="token punctuation">,</span>ARRAY_TASKS <span class="token operator">-</span><span class="token operator">-</span>mail<span class="token operator">-</span>user<span class="token operator">=</span>oliver.ziff<span class="token operator">@</span>crick.ac.uk 

sbatch <span class="token operator">-</span>N <span class="token number">1</span> <span class="token operator">-</span>c <span class="token number">8</span> <span class="token operator">-</span><span class="token operator">-</span>mem<span class="token operator">=</span><span class="token number">0</span> <span class="token operator">-</span>t <span class="token number">24</span><span class="token operator">:</span><span class="token number">00</span><span class="token operator">:</span><span class="token number">00</span> <span class="token operator">-</span><span class="token operator">-</span>wrap<span class="token operator">=</span><span class="token string">"Rscript ~/home/projects/scrnaseq-astrocyte-motor-neuron/expression/seurat/scripts/scRNAseq_seurat_mergedRmd.R"</span> <span class="token operator">-</span><span class="token operator">-</span>mail<span class="token operator">-</span>type<span class="token operator">=</span>ALL<span class="token punctuation">,</span>ARRAY_TASKS <span class="token operator">-</span><span class="token operator">-</span>mail<span class="token operator">-</span>user<span class="token operator">=</span>oliver.ziff<span class="token operator">@</span>crick.ac.uk 

<span class="token comment"># to hold job until another is finished add:</span>
<span class="token operator">-</span><span class="token operator">-</span>dependency<span class="token operator">=</span>afterany<span class="token operator">:</span><span class="token number">9364894</span>
</code></pre>
<h1 id="rstudio-run-rscripts-interactively">Rstudio: run Rscripts interactively</h1>
<p>Use VNC rather than x11 forwarding which is slow and clunky.<br>
<a href="https://wiki.thecrick.org/display/HPC/Graphical+Display+-+Visualisation">https://wiki.thecrick.org/display/HPC/Graphical+Display±+Visualisation</a></p>
<p>load rtest env:</p>
<pre class=" language-r"><code class="prism  language-r">srun <span class="token operator">-</span><span class="token operator">-</span>partition int <span class="token operator">-</span><span class="token operator">-</span>x11 <span class="token operator">-</span><span class="token operator">-</span>time<span class="token operator">=</span><span class="token number">16</span><span class="token operator">:</span><span class="token number">00</span><span class="token operator">:</span><span class="token number">00</span> <span class="token operator">-</span><span class="token operator">-</span>mem<span class="token operator">=</span>48G <span class="token operator">-</span><span class="token operator">-</span>cpus<span class="token operator">-</span>per<span class="token operator">-</span>task<span class="token operator">=</span><span class="token number">8</span> <span class="token operator">-</span><span class="token operator">-</span>pty bash <span class="token comment"># request interactive node</span>
ml Anaconda2
source activate rtest <span class="token comment"># activate conda environment</span>
</code></pre>
<p>on CAMP interactive node:</p>
<pre><code>ml Tigervnc
vncpasswd # ONLY DO THIS command the first time
vncstart
</code></pre>
<p>Copy &amp; paste the output of vncstart i.e. <code>int000.camp.thecrick.org:4</code> into the vncTiger app VNC server box. This app can be downloaded from <a href="https://bintray.com/tigervnc/beta/tigervnc/1.10beta">https://bintray.com/tigervnc/beta/tigervnc/1.10beta</a> NB download the <code>TigerVNC-1.9.90.dmg</code> file<br>
Click Connect<br>
Type Password</p>
<p>The Tiger VNC now shows the CAMP terminal but to improve the viewer, now load fluxbox within the VNC viewer CAMP terminal:</p>
<pre><code>ml fluxbox
fluxbox &amp;
</code></pre>
<p>Open another xterm window on Tiger VNC: Right click grey area of window &gt; xterm</p>
<p>Into new terminal type:</p>
<pre><code>ml rstudio/1.2.1335-foss-2019a # the latest version rstudio/1.2.5033-foss-2019b-Java-11 is not working!
ml cairo # require to see plots. must load this after loading rstudio or modules will clash.

#load rstudio with:
rstudio
</code></pre>
<p>Rstudio now opens within the TigerVNC window</p>
<h1 id="installing-r-packages-on-cluster">Installing R packages on cluster</h1>
<p><a href="https://www.hawaii.edu/its/ci/hpc-tutor/using-r/">https://www.hawaii.edu/its/ci/hpc-tutor/using-r/</a><br>
<a href="https://docs.conda.io/projects/conda/en/4.6.0/_downloads/52a95608c49671267e40c689e0bc00ca/conda-cheatsheet.pdf">https://docs.conda.io/projects/conda/en/4.6.0/_downloads/52a95608c49671267e40c689e0bc00ca/conda-cheatsheet.pdf</a></p>
<p>Options:</p>
<ol>
<li>Via CRAN (install.packages)</li>
<li>Bioconducter (BiocManager::install)</li>
<li>Conda: conda install packagename - use this to install r-base packages only. Install all other packages from BiocManager.</li>
</ol>
<p><strong>Stick to one method</strong> and not mix if possible.</p>
<h1 id="libpaths-set-path-to-save-packages">.libPaths() Set path to save packages</h1>
<p>Add to start of all R scripts:</p>
<pre class=" language-r"><code class="prism  language-r">q<span class="token punctuation">(</span><span class="token punctuation">)</span>```
R packages are saved to library paths `.libPaths<span class="token punctuation">(</span><span class="token punctuation">)</span>`
```r
<span class="token comment">#print library paths where pacakges are saved</span>
.libPaths<span class="token punctuation">(</span><span class="token punctuation">)</span>
<span class="token punctuation">[</span><span class="token number">1</span><span class="token punctuation">]</span> <span class="token string">"/camp/home/ziffo/R/x86_64-conda_cos6-linux-gnu-library/3.6"</span>
<span class="token punctuation">[</span><span class="token number">2</span><span class="token punctuation">]</span> <span class="token string">"/camp/lab/luscomben/home/.conda/envs/rtest/lib/R/library"</span>
<span class="token comment"># cannot write to "/camp/apps/eb/software/"</span>
</code></pre>
<p>Ensure that .libPaths is set to <code>"/camp/lab/luscomben/home/.conda/envs/rtest/lib/R/library"</code> and not <code>"/camp/home/ziffo/R/x86_64-conda_cos6-linux-gnu-library/3.6"</code> as you dont have permissions to install into <code>/camp/home/ziffo/</code></p>
<pre class=" language-r"><code class="prism  language-r"><span class="token comment"># set .lib</span>
.libPaths<span class="token punctuation">(</span><span class="token string">"/camp/lab/luscomben/home/.conda/envs/rtest/lib/R/library"</span><span class="token punctuation">)</span>

<span class="token comment"># NOT /camp/home/ziffo/R/x86_64-conda_cos6-linux-gnu-library/3.6</span>
</code></pre>
<pre class=" language-r"><code class="prism  language-r"><span class="token comment"># ensure packages are appropriate for the current R vesion</span>
update.packages<span class="token punctuation">(</span>repos<span class="token operator">=</span><span class="token string">'http://cran.rstudio.com/'</span><span class="token punctuation">,</span> ask<span class="token operator">=</span><span class="token boolean">FALSE</span><span class="token punctuation">,</span> checkBuilt<span class="token operator">=</span><span class="token boolean">TRUE</span><span class="token punctuation">)</span>
</code></pre>
<p>installed.packages()</p>
<p>#install packages specifying location: <a href="https://stackoverflow.com/questions/15170399/change-r-default-library-path-using-libpaths-in-rprofile-site-fails-to-work">https://stackoverflow.com/questions/15170399/change-r-default-library-path-using-libpaths-in-rprofile-site-fails-to-work</a><br>
install.packages(“rhdf5”, lib="/camp/lab/luscomben/home/.conda/envs/rtest/lib/R/library")</p>
<pre><code>
R packages should be saved to either:
/camp/home/ziffo/R/x86_64-pc-linux-gnu-library/3.5/
/camp/lab/luscomben/home/.conda/envs/rtest/lib/R/library
</code></pre>
<h1 id="conda-environment">Conda environment</h1>
<h2 id="conda-create">Conda create</h2>
<pre class=" language-bash"><code class="prism  language-bash">ml Anaconda2 <span class="token comment"># load Anaconda clientconda install anaconda-client # install anaconda client#create conda environment   </span>
conda create -n rtest r-base r-essentials r-devtools <span class="token comment"># create R environment with latest R version and basic packages. Install all other packages with BiocManager::install()#alternative write bash script listing conda packages to install</span>

conda create -n r-ret r-base r-essentials r-devtools r-reticulate
</code></pre>
<p>Doing this creates a folder called rtest located at <code>/camp/lab/luscomben/home/.conda/envs/rtest</code><br>
Note that <code>/camp/home/ziffo/.conda</code> is a sym link <code>.conda -&gt; /camp/lab/luscomben/home/users/ziffo/.conda/</code></p>
<pre class=" language-bash"><code class="prism  language-bash"><span class="token comment">#  for Python 2.7</span>
conda create -n my_pymc_env python<span class="token operator">=</span>2.7

<span class="token comment"># update conda environment</span>
conda update --help
conda update -n base -c defaults conda
conda update -c defaults conda -p /camp/lab/luscomben/home
conda update -p /camp/lab/luscomben/home/.conda/envs/rtest r-base
conda update -n rtest r-base

<span class="token comment"># load conda enviroment</span>
<span class="token function">source</span> activate rtest
conda activate rtest

<span class="token comment">#to deactivate environment</span>
<span class="token function">source</span> deactivate rtest
</code></pre>
<h2 id="conda-install">Conda install</h2>
<pre class=" language-bash"><code class="prism  language-bash"><span class="token comment"># do this outside of R but in rtest env</span>
<span class="token function">source</span> activate rtest

<span class="token comment">#search for your package:</span>
anaconda search package_name
<span class="token comment">#Look for a channel that has the right version for you and install: search how to install that specific package</span>
anaconda show USER/PACKAGE

<span class="token comment"># Install as per instruction e.g.</span>
conda <span class="token function">install</span> --channel https://conda.anaconda.org/r r-base<span class="token operator">=</span>3.6.1 <span class="token comment">#specify the latest version</span>
conda <span class="token function">install</span> --channel https://conda.anaconda.org/bioconda bioconductor-deseq2
conda <span class="token function">install</span> --channel https://conda.anaconda.org/bioconda bioconductor-rhdf5
conda <span class="token function">install</span> --channel https://conda.anaconda.org/bioconda r-sleuth
conda <span class="token function">install</span> --channel https://conda.anaconda.org/bioconda kallisto
conda <span class="token function">install</span> --channel https://conda.anaconda.org/anaconda r-dplyr
conda <span class="token function">install</span> -c channel_with_right_version mypackage

</code></pre>
<h2 id="debugging-conda">Debugging conda:</h2>
<p><a href="https://www.anaconda.com/keeping-anaconda-date/">https://www.anaconda.com/keeping-anaconda-date/</a></p>
<pre class=" language-bash"><code class="prism  language-bash">conda clean --all
conda update --all <span class="token comment"># update all pacakges in current env</span>
conda update -n base -c defaults conda
conda <span class="token function">install</span> anaconda
conda <span class="token function">install</span> --channel https://conda.anaconda.org/bioconda r-sleuth
<span class="token comment">#can try setting fee channel on, but dont leave it on</span>
conda config --set restore_free_channel <span class="token boolean">true</span>
</code></pre>
<p>Debugging conda:</p>
<pre class=" language-bash"><code class="prism  language-bash">conda clean --all
conda update --all
conda update -n base -c defaults conda
conda <span class="token function">install</span> anaconda
conda <span class="token function">install</span> --channel https://conda.anaconda.org/bioconda r-sleuth
ml sleuth/0.28.0-foss-2016b-R-3.3.1
</code></pre>
<h1 id="install-r-packages-with-install.packages--and-biocmanagerinstall">Install R packages with <code>install.packages()</code>  and <code>BiocManager::install()</code></h1>
<pre class=" language-r"><code class="prism  language-r"><span class="token comment"># install packages normally by calling R</span>
R
<span class="token comment">#install command</span>
install.packages<span class="token punctuation">(</span><span class="token string">"package name"</span><span class="token punctuation">)</span>

<span class="token keyword">if</span> <span class="token punctuation">(</span><span class="token operator">!</span>requireNamespace<span class="token punctuation">(</span><span class="token string">"BiocManager"</span><span class="token punctuation">,</span> quietly <span class="token operator">=</span> <span class="token boolean">TRUE</span><span class="token punctuation">)</span><span class="token punctuation">)</span>
    install.packages<span class="token punctuation">(</span><span class="token string">"BiocManager"</span><span class="token punctuation">)</span>
BiocManager<span class="token operator">::</span>install<span class="token punctuation">(</span><span class="token string">"package name"</span><span class="token punctuation">)</span>

<span class="token comment"># load library package</span>
library<span class="token punctuation">(</span><span class="token string">"package name"</span><span class="token punctuation">)</span>

<span class="token comment"># quit R</span>
q<span class="token punctuation">(</span><span class="token punctuation">)</span>
</code></pre>
<p>Example:</p>
<pre class=" language-bash"><code class="prism  language-bash">ml R/3.3.1-foss-2016b
ml R/3.6.0-foss-2019a
ml sleuth
R
install.packages<span class="token punctuation">(</span><span class="token string">"mclust"</span>, lib<span class="token operator">=</span><span class="token string">"/camp/home/ziffo/R/x86_64-pc-linux-gnu-library/3.6"</span><span class="token punctuation">)</span>
install.packages<span class="token punctuation">(</span><span class="token string">"tximeta"</span>, <span class="token function">type</span> <span class="token operator">=</span> <span class="token string">"source"</span>, repos <span class="token operator">=</span> <span class="token string">"https://git.bioconductor.org/packages/tximeta"</span><span class="token punctuation">)</span>
install.packages<span class="token punctuation">(</span><span class="token string">"sva"</span><span class="token punctuation">)</span>
install.packages<span class="token punctuation">(</span><span class="token string">"tximeta"</span>, lib<span class="token operator">=</span><span class="token string">"/camp/home/ziffo/R/x86_64-pc-linux-gnu-library/3.6"</span><span class="token punctuation">)</span>

remove.packages<span class="token punctuation">(</span><span class="token string">"data.table"</span>, lib<span class="token operator">=</span><span class="token string">"/camp/home/ziffo/R/x86_64-pc-linux-gnu-library/3.5"</span><span class="token punctuation">)</span>

BiocManager::install<span class="token punctuation">(</span><span class="token string">"IRanges"</span>, lib<span class="token operator">=</span><span class="token string">"/camp/home/ziffo/R/x86_64-pc-linux-gnu-library/3.6"</span><span class="token punctuation">)</span>

BiocManager::install<span class="token punctuation">(</span><span class="token string">"tximeta"</span><span class="token punctuation">)</span>

install.packages<span class="token punctuation">(</span><span class="token string">'https://bioconductor.org/packages/release/bioc/src/contrib/Rhdf5lib_1.6.0.tar.gz'</span>, repos<span class="token operator">=</span>NULL, type<span class="token operator">=</span><span class="token string">'source'</span><span class="token punctuation">)</span>

BiocManager::install<span class="token punctuation">(</span><span class="token string">"sleuth"</span>, lib<span class="token operator">=</span><span class="token string">"/camp/lab/luscomben/home/.conda/envs/rtest/lib/R/library"</span><span class="token punctuation">)</span>
q<span class="token punctuation">(</span><span class="token punctuation">)</span>
save: y
</code></pre>
<pre class=" language-r"><code class="prism  language-r">install.packages<span class="token punctuation">(</span><span class="token string">'https://bioconductor.org/packages/release/bioc/src/contrib/Rhdf5lib_1.6.0.tar.gz'</span><span class="token punctuation">,</span> repos<span class="token operator">=</span><span class="token keyword">NULL</span><span class="token punctuation">,</span> type<span class="token operator">=</span><span class="token string">'source'</span><span class="token punctuation">,</span>configure.args<span class="token operator">=</span><span class="token string">'--with-zlib=/camp/lab/luscomben/home/bin/zlib-1.2.11'</span><span class="token punctuation">,</span> lib<span class="token operator">=</span><span class="token string">"/camp/lab/luscomben/home/.conda/envs/rtest/lib/R/library"</span><span class="token punctuation">)</span>

BiocManager<span class="token operator">::</span>install<span class="token punctuation">(</span><span class="token string">"Rhdf5lib"</span><span class="token punctuation">,</span>  <span class="token operator">-</span><span class="token operator">-</span>with<span class="token operator">-</span>zlib<span class="token operator">=</span><span class="token operator">/</span>camp<span class="token operator">/</span>lab<span class="token operator">/</span>luscomben<span class="token operator">/</span>home<span class="token operator">/</span>.conda<span class="token operator">/</span>envs<span class="token operator">/</span>rtest<span class="token operator">/</span>lib<span class="token operator">/</span>R<span class="token operator">/</span>library<span class="token punctuation">)</span>
<span class="token operator">-</span><span class="token operator">-</span>with<span class="token operator">-</span>zlib<span class="token operator">=</span><span class="token operator">/</span>camp<span class="token operator">/</span>lab<span class="token operator">/</span>luscomben<span class="token operator">/</span>home<span class="token operator">/</span>bin<span class="token operator">/</span>zlib<span class="token operator">-</span><span class="token number">1.2</span>.<span class="token number">11</span>

devtools<span class="token operator">::</span>install_github<span class="token punctuation">(</span><span class="token string">"grimbough/Rhdf5lib"</span><span class="token punctuation">,</span> lib<span class="token operator">=</span><span class="token string">"/camp/lab/luscomben/home/.conda/envs/rtest/lib/R/library"</span><span class="token punctuation">)</span>
</code></pre>
<pre><code>library(remotes)
install_github("pachterlab/sleuth")
install_bitbucket("packageauthor/foobarbaz")
install_gitorious("packageauthor/foobarbaz")
</code></pre>
<h1 id="r-environment">R environment</h1>
<p>Alternatively to Conda environment, load R and install packages into an acessible folder. Dont need to conda activate rtest for this</p>
<pre class=" language-bash"><code class="prism  language-bash">ml Anaconda2
ml R/3.6.0-foss-2019a
R
</code></pre>
<p>in R:</p>
<pre class=" language-r"><code class="prism  language-r"><span class="token comment"># set libPaths</span>
.libPaths<span class="token punctuation">(</span><span class="token string">"/camp/lab/luscomben/home/.conda/envs/rtest/lib/R/library"</span><span class="token punctuation">)</span>
<span class="token string">"/camp/apps/eb/software/R/3.6.0-foss-2019a/lib64/R/library"</span>

.libPaths<span class="token punctuation">(</span>c<span class="token punctuation">(</span><span class="token string">"/camp/lab/luscomben/home/.conda/envs/rtest/lib/R/library"</span><span class="token punctuation">,</span> <span class="token string">"/camp/apps/eb/software/R/3.6.0-foss-2019a/lib64/R/library"</span><span class="token punctuation">)</span><span class="token punctuation">)</span>

.libPaths<span class="token punctuation">(</span><span class="token string">"/camp/lab/luscomben/home/envs/renv"</span><span class="token punctuation">)</span>
<span class="token comment"># install via BiocManager::install()</span>
</code></pre>
<p><code>.libPaths</code> can be set in the <code>.Rprofile</code> script.</p>
<h2 id="renv">renv</h2>
<p>Manages library pathwas to help isolate project’s libraries dependendencies.</p>
<p><a href="https://rstudio.github.io/renv/articles/renv.html">https://rstudio.github.io/renv/articles/renv.html</a><br>
<a href="https://rstudio.github.io/renv/index.html">https://rstudio.github.io/renv/index.html</a><br>
<a href="https://rstudio.github.io/renv/">https://rstudio.github.io/renv/</a><br>
<a href="https://rstudio.github.io/renv/articles/renv.html">https://rstudio.github.io/renv/articles/renv.html</a></p>
<p>Open R on CAMP:</p>
<pre class=" language-bash"><code class="prism  language-bash">ml Anaconda2
<span class="token function">source</span> activate rtest
R
</code></pre>
<p>Initialise new R library:</p>
<pre class=" language-r"><code class="prism  language-r">renv<span class="token operator">::</span>init<span class="token punctuation">(</span><span class="token punctuation">)</span>
</code></pre>
<p>This sets up your project with a private library, and also installs all of the packages you’re using into that library. The packages used in your project will be recorded into a <em>lockfile</em>, called <code>renv.lock</code>.<br>
As you work in your project, you can install or upgrade different packages. As these packages are installed,  <code>renv</code>  automatically writes  <code>renv.lock</code>  for you. The  <code>renv.lock</code>  lockfile records the state of your project’s private library, and can be used to restore the state of that library as required.</p>
<p>Later, if you need to port your project to a new machine, you can call  <code>[renv::restore()](https://rstudio.github.io/renv/reference/restore.html)</code>  to reinstall all of the packages as declared in the lockfile.</p>
<p>Save the project library with packages installed<br>
renv::snapshot()</p>
<p>Restore the project library<br>
renv:restore()</p>
<p>To load an renv, use <code>renv/activate.R</code><br>
Sys.setenv(RENV_PATHS_ROOT = “/camp/lab/luscomben/home/envs”)</p>
<h1 id="re-install-all-r-package-in-one-command">Re install all R package in one command</h1>
<pre class=" language-r"><code class="prism  language-r">BiocManager<span class="token operator">::</span>install<span class="token punctuation">(</span>c<span class="token punctuation">(</span><span class="token string">"GenomicFeatures"</span><span class="token punctuation">,</span> <span class="token string">"AnnotationDbi"</span><span class="token punctuation">,</span> <span class="token string">"dplyr"</span><span class="token punctuation">,</span> <span class="token string">"tidyverse"</span><span class="token punctuation">,</span> <span class="token string">"data.table"</span><span class="token punctuation">,</span> <span class="token string">"gsubfn"</span><span class="token punctuation">,</span> <span class="token string">'ggplot2'</span><span class="token punctuation">,</span> <span class="token string">"ggpubr"</span><span class="token punctuation">,</span> <span class="token string">"pheatmap"</span><span class="token punctuation">,</span> <span class="token string">'ggrepel'</span><span class="token punctuation">,</span> <span class="token string">"grid"</span><span class="token punctuation">,</span> <span class="token string">"ggplotify"</span><span class="token punctuation">,</span> <span class="token string">"rmarkdown"</span><span class="token punctuation">,</span> <span class="token string">"clusterProfiler"</span><span class="token punctuation">,</span> <span class="token string">"BiocParallel"</span><span class="token punctuation">,</span> <span class="token string">"EnhancedVolcano"</span><span class="token punctuation">,</span> <span class="token string">"tximeta"</span><span class="token punctuation">,</span> <span class="token string">"tximport"</span><span class="token punctuation">,</span> <span class="token string">"systemPipeR"</span><span class="token punctuation">,</span>  <span class="token string">"org.Hs.eg.db"</span><span class="token punctuation">,</span> <span class="token string">"vsn"</span><span class="token punctuation">,</span> <span class="token string">"limma"</span><span class="token punctuation">,</span> <span class="token string">"AnnotationDbi"</span><span class="token punctuation">,</span> <span class="token string">"Glimma"</span><span class="token punctuation">,</span> <span class="token string">"colorRamps"</span><span class="token punctuation">,</span> <span class="token string">"RColorBrewer"</span><span class="token punctuation">,</span> <span class="token string">"GO.db"</span><span class="token punctuation">,</span> <span class="token string">"fgsea"</span><span class="token punctuation">,</span> <span class="token string">"geneplotter"</span><span class="token punctuation">,</span> <span class="token string">"DESeq2"</span><span class="token punctuation">,</span> <span class="token string">"genefilter"</span><span class="token punctuation">,</span> <span class="token string">"sva"</span><span class="token punctuation">,</span> <span class="token string">"RUVSeq"</span><span class="token punctuation">,</span> <span class="token string">"BiocSingular"</span><span class="token punctuation">,</span> <span class="token string">"sleuth"</span><span class="token punctuation">,</span> <span class="token string">"rhdf5"</span><span class="token punctuation">)</span><span class="token punctuation">)</span>
</code></pre>
<h1 id="rstudio-ondemand">Rstudio OnDemand</h1>
<ol>
<li>Go to <a href="http://ondemand.camp.thecrick.org/pun/sys/dashboard">http://ondemand.camp.thecrick.org/pun/sys/dashboard</a></li>
<li>Login with usual Crick username ( @crick.ac.uk not needed) - may need HPC to add your authorisation</li>
<li>Interactive Apps Dropdown</li>
<li>Rstudio server tab</li>
<li>Set up cpu session: cpu; hours 12; nodes 1; email &gt; launch</li>
<li>Wait for CPU session to start &gt; Connect to Rstudio Server</li>
<li>Make Rstudio full screen with:<br>
3 dots on right toolbar on chrome &gt; Zoom rectangle.<br>
Go to a tab to the left &gt; CMD + SHIFT + F &gt; CTRL + Page UP</li>
</ol>
<p><code>.libPaths()</code> issues<br>
wont remove the eb/software path causing package loading issues</p>
<h1 id="rstudio-server">Rstudio server</h1>
<p>used the other rstudio package and rebinded it to a new port and that allowed you to escape the X display.</p>
<p>X display port is not for helping the font issue but to allow keeping 8787 port free for novice users. The font issue is affected by the way xfs and x11 font libraries work in my opinion for native X11 application execution like typical rstudio module unless in the case of rstudio-server.Anyway, my setup is quire straightforward… It includes installation of following modules in the same order after you secure an interactive node:</p>
<p>Open vncserver as above for rstudio. Run rstudio server out of Tigervnc to keep interactive terminal command line free for other things.<br>
on CAMP interactive node:</p>
<pre><code>ml Tigervnc
vncpasswd # ONLY DO THIS command the first time
vncstart
</code></pre>
<p>Copy &amp; paste the output of vncstart i.e. <code>int000.camp.thecrick.org:4</code> into the vncTiger app VNC server box. This app can be downloaded from <a href="https://bintray.com/tigervnc/beta/tigervnc/1.10beta">https://bintray.com/tigervnc/beta/tigervnc/1.10beta</a><br>
Click Connect<br>
Type Password</p>
<p>The Tiger VNC now shows the CAMP terminal. Into terminal execute the rstudio server script:</p>
<pre class=" language-bash"><code class="prism  language-bash"><span class="token comment">## And then invocation of rserver  </span>
<span class="token function">bash</span> ~/home/scripts/rstudio.sh
<span class="token comment"># i created alias called rserver in bashrc profile</span>
<span class="token function">alias</span> rserver<span class="token operator">=</span><span class="token string">"bash ~/home/scripts/rstudio.sh"</span>
<span class="token comment"># You might need to change the port if it is already in use.</span>
</code></pre>
<p>Copy and paste the URL into web browser:</p>
<pre class=" language-bash"><code class="prism  language-bash">The RStudio-Server will be running on this address:
http://10.28.2.11:8787
Or
http://int001.camp.thecrick.org:8787
</code></pre>

