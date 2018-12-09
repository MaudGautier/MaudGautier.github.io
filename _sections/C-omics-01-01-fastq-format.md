---
layout: course
material: course
topic: omics
chapter: fastq
title: "The FASTQ format for raw sequence data"
meta-title: "FASTQ format"
toc-title: "File format: FASTQ"
permalink: /course/omics/FASTQ-format
tags: [FASTQ, file format]
---

With the increase of DNA sequencing, a general consensus has rapidly emerged over a <i>de facto</i> standard to store nucleotide sequences in a human-readable fashion: the **FASTQ format**.

This file format directly derives from the <a href="">FASTA file format</a> and was used early for <a href="/course/NGS/Sanger">Sanger capillary sequencing</a>.





&nbsp;
<hr>
The FASTQ format: a four-line encoding for each read
----------------------------------------------------

At each run, millions of reads are sequenced. For each of them, two main types of information are reported by the sequencing machine:
* the **sequence** of the read (i.e. each base call);
* the **quality score** associated to each base call (i.e. the *confidence* with which each nucleotide is inferred by the sequencing machine).

On top of that, each sequence is given a unique identifier by the sequencing machine.

All in all, all the information for one read is encoded into 4 lines:
1. the sequence ID;
2. the sequence <i>per se</i>;
3. A "+" sign (occasionnally followed by the sequence ID and other information - but most of the times by nothing);
4. the per-base quality-score associated to the sequence.

The general format for one read in a FASTQ file is thus:

```bash
@<sequence ID and any additional information>
<sequence>
+<empty string OR sequence ID (repeated)>
<quality>
```

For example, here is a portion of a real FASTQ file corresponding to one given sequence:

```bash
@HISEQ-KERMIT:318:C9K39ANXX:8:1101:1441:1892 1:N:0:GAATCTGACAGGACGT
NCACTAGAGGCATTAGAACATGCTGGAAGGCTGGTGCAGTGAATGTAGTAAACAAGAGACTGGCATAATCATTGTGCAGTGCAGTAGCTGAAGTCAGAGA
+
@@@DDEDFHH:CFIDHIIIDGEDHFCHAFHGGGEGIGI@FAGFDGIDHGIEGAGEHAEHGGIIDC@@AC=;?EHF2CH:@@DEH>E9?=B@@CCCCC??#
```



&nbsp;
<hr>
Focus on the Illumina sequence ID
---------------------------------

The sequence ID follows a specific pattern which differs according to the sequencing platform used (Illumina, 454, Ion Torrent, PacBio...). The specific patterns of each platform are described <a href="https://www.ncbi.nlm.nih.gov/sra/docs/submitformats/#platform-specific-fastq-files">here</a> by the NCBI.

This section focuses exclusively on the sequence ID format outputed by Illumina (the one that provides most details).
Illumina has used two different formats according to the version of the <a href="{% post_url 2019-01-27-CASAVA %}">CASAVA software package</a> used. The most recent format is in the following form:

```bash
@<instrument>:<run>:<flowcell>:<lane>:<tile>:<x-pos>:<y-pos> <read>:<filtered or not>:<control number>:<barcode>
```

The first part of the ID (
<span style="color: #000; font-family: Consolas,Monaco,'Andale Mono','Ubuntu Mono',monospace; font-size: small;">\<instrument>:\<run>:\<flowcell>:\<lane>:\<tile>:\<x-pos>:\<y-pos></span>
) is the identifier <i>per se</i>. It indicates the precise position of the read cluster sequenced at a specific run in a specific instrument.

The second part of the ID (
<span style="color: #000; font-family: Consolas,Monaco,'Andale Mono','Ubuntu Mono',monospace; font-size: small;">\<read>:\<filtered or not>:\<control number>:\<barcode></span>
) provides additional information: 

- the member (1 or 2) of the pair. This is useful in the case of paired-end or mate-pair reads (details in <a href="#SEandPE">this section</a>);
- whether the read is filtered (Y) or not (N) by the Illumina Real Time Analysis (RTA) software (details in <a href="{% post_url 2019-01-27-CASAVA %}#cluster_filtering_with_RTA">this note</a>);
- a control number which equals 0 when none of the control bits are on, or an even number otherwise. Control bits are on when a non-indexed control used by with Illumina devices (PhiX) is found in demultiplexed FASTQ data. Basically, it is a flag for the accuracy of multiplexing.
- an index tag (or barcode), which is a unique 6-to-20 bp-long sequence added to each sample so that its sequence reads can be retrieved (which is useful in case of multiplexing).




&nbsp;
<hr>
Focus on the phred quality score
--------------------------------

Each base call (i.e. each nucleotide read by the sequencing machine) is attributed a quality score, called the **phred quality score**.
These scores (Q) are logarithmically related to the probability (P) that a base call is erroneous.


~~~
Q = -10 log10(P)
~~~

From that, the probability that a base is erroneous can be recalculated easily, as done in the table below.


| Phred score | Probability of incorrect call | Base call accuracy |
| :------ |:--- | :--- |
| 10 | 1 in 10 | 90% |
| 20 | 1 in 100 | 99% |
| 30 | 1 in 1,000 | 99.9% |
| 40 | 1 in 10,000 | 99.99% |


So that the string of scores is the same length as the sequence of the read, these scores are represented as <a href="https://en.wikipedia.org/wiki/ASCII">ASCII characters</a>, with an offset of either 33 or 64 according to the sequencing machine used (which should thus be checked individually).





<div id="SEandPE"></div>
&nbsp;
<hr>
Single-end VS paired-end reads
------------------------------

DNA fragments are generally rather long (several hundreds of base pairs), while the [Next-Generation Sequencing]({{ site.baseurl }}{% link _sections/C-NGS-02-01-Second-generation-overview.md %}) devices can read shorter sequences (a few hundreds of base pairs). As such, only the edges of the DNA fragments are sequenced (these sequenced parts are called 'reads'). 



Either both ends or only one end of the DNA fragments are sequenced. 
The first case (both ends read) is called **paired-end sequencing** and leads to the production of two distinct FASTQ files: one for each end sequenced. 
The second case (only one end read) is called **single-end sequencing** and leads to the production of only one FASTQ file. 


