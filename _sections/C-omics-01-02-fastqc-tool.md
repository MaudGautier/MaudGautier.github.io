---
layout: course
material: course
topic: omics
chapter: fastq
title: "Read filtering and cleaning"
meta-title: "FastQC tool"
toc-title: "Read filtering and cleaning"
permalink: /course/omics/FastQC-tool
tags: [FastQC, tool]
---

Before doing any further analysis on the sequencing data, the first step consists in checking for the quality of the sequences and, if needed, to clean them up.

In this section, I describe which tools can be used to both assess and improve read quality.

&nbsp;
<hr>
The FastQC tool to assess read quality
--------------------------------------

FastQC, which can be found <a href="http://www.bioinformatics.babraham.ac.uk/projects/fastqc/">here</a>, is a valuable tool to do qualtiy control checks on raw sequence data coming from sequencing machines.

It can be implemented using the following command line:

{% highlight bash %}
fastqc -o <OUTPUT_DIRECTORY> \
       <INPUT_FASTQ_FILE>
{% endhighlight %}




&nbsp;
<hr>
Interpretation of FastQC graphs
-------------------------------
In this post, I will not go through the details of the interpretation of FastQC graphs, but I will detail them in another post (not written yet).
In the meantime, you can find example reports of FastQC for both good- and bad-quality sequencing data directly on the <a href="http://www.bioinformatics.babraham.ac.uk/projects/fastqc/">Babraham Bioinformatics website</a>.




&nbsp;
<hr>
Improving read quality with clipping
------------------------------------

The sequencing process involves the addition of sequencing adapters.
Generally, the adapters have already been removed by the bioinformaticians working at the sequencing platform, but sometimes, it is not the case.
To remove these adapters, you can use the tool **cutadapt** that can be found <a href="https://cutadapt.readthedocs.io/en/stable/">here</a>.

Hereunder is the minimal command line to use the tool:

{% highlight bash %}
cutadapt -a <ADAPTER_FORWARD> \
         -o <OUTPUT_FASTQ_FILE> \
         <INPUT_FASTQ_FILE>
{% endhighlight %}

The necessary options are the following:

- [-a] = The adapter sequence that should be searched for in the 5’-to-3’ direction;
- [-o] = The to the output file (FASTQ format).

If reads are paired (in that case, there are two FASTQ files), other should be added:

- [-A] = The adapter sequence that should be searched for in the 3’-to-5’ direction;
- [-p] = The path to the output file for paired reads (FASTQ format).

As such, the minimal command line becomes:

{% highlight bash %}
cutadapt -a <ADAPTER_FORWARD> \
         -A <ADAPTER_REVERSE> \
         -o <OUTPUT_FASTQ_FILE_READS_1> \
         -p <OUTPUT_FASTQ_FILE_READS_2> \
         <INPUT_FASTQ_FILE_READS_1> \
         <INPUT_FASTQ_FILE_READS_2>
{% endhighlight %}



Alternatively, the **fastx_clipper** tool (which can be found <a href="http://hannonlab.cshl.edu/fastx_toolkit/">here</a>) also exists and can be used as follows:

{% highlight bash %}
fastx_clipper -a <ADAPTER> \
              -Q <33/64> \
							-i <INPUT_FASTQ_FILE> \
              -o <OUTPUT_FASTQ_FILE>
{% endhighlight %}

The options are the same as for cutadapt, but it requires the option [-Q] which correspond to the ASCII offset (generally 33 or 64), which depends on the device used for sequencing.


&nbsp;
<hr>
Improving read quality with trimming
------------------------------------

Read quality is generally lower at the 3’ ends because, in some fragments of each cluster, a base is not incorporated at certain cycles.
Thus, the signal gets diluted while the sequencing process reaches towards the 3’ ends.

Depending on the aim of the analysis, this loss in read quality can get critical and it is necessary to trim (i.e. cut down) the low quality ends.
The common tool used to do this is called **fastx_trimmer** and can be found <a href="http://manpages.ubuntu.com/manpages/bionic/man1/fastx_trimmer.1.html">here</a>.

Hereunder is the minimal command line to use this tool:

{% highlight bash %}
fastx_trimmer -Q <33/64> \
              -l <FINAL_LENGTH> \
              -i <INPUT_FASTQ_FILE> \
              -o <OUTPUT_FASTQ_FILE>
{% endhighlight %}

The list of options are:

- [-Q] = The ASCII offset that should be considered by the tool (generally, 33 or 64 depending on the sequencing device)
- [-l] = The total length (in bp) that should be kept in the trimmed reads;
- [-i] = The path to the input file (FASTQ format);
- [-o] = The path to the output file (FASTQ format).











