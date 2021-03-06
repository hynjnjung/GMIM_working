# GMIM: Genome-wide multi-track integrator using MBF
GMIM is a two-part command-line program that can integrate multiple NGS data tracks via transformation to the complements of the minimum Bayes factor (*cMBF*) (range: 0-1) from signal-to-noise ratios as a function of genomic position. 
The cMBF and integraton calculations help separate signal (or higher read count positions) from noise (or lower read count positions). 
This is **NOT** a peak-calling algorithm as it does not specifically locate peaks (though you can use a peak-calling program on the output!). 

### MBF Background
The minimum Bayes factor ............

## Part 1: cMBF Calcualations
The first GMIM program **calculates** the cMBF for each position for later integration. It first splits a given genome/chromosome file into a directory of separate chromosomes to facilitate parallelizing computation. For each chromosome file, the program uses a "moving window" to calculate the cMBF. 

### Input
GMIM takes as input a *sorted* BED file of sequencing read counts (generated using *samtools* (depth), *bedtools* (coverage, genomecov), etc.)
The bed file should have the following (whitespace/tab separated) format:

`Chr  start end read_count`

The commandline options for changing the parameters are as below:
```
-h,--help
-i,--input <arg>         [req] input file path, must be a bed file
-m,--medMult <arg>       [opt] median multiple, cannot be 0                     default: 1
-o,--output <arg>        [opt] output file base name                            default: "out"
-w,--windowSize <arg>    [req] window size for calculating cMBF, must be        default: 10000 (bp)
                          a multiple of interval size
-z,--defaultZero <arg>   [opt] default number to replace zero                   default: 0.1
```

### Run
`java -jar Driver.jar -i input.bed -w 1000 [other options]`

### Output
This part produces a directory of chromosome BedGraph files with the calculated cMBFs. 
The files have the following tab-separated format, `Chr start end cMBF`, and include headers identifying the file as a BedGraph file (see the [UCSC BedGraph Format](https://genome.ucsc.edu/goldenpath/help/bedgraph.html)).
The directory also includes a final BedGraph file that concatenates all chromosomes in the order of the given file. 



## Part Two: Integration
The second part of the GMIM program **integrates** the calculated cMBFs for the different tracks by multiplying the cMBF at each position. 

### Input
Inputs should be Part One generated files, or have the same format (`Chr start end cMBF`). 
An output filename also needs to included as the **first** input. 
The inputs should be ordered as follows:

### Run
`java Integration_Output_FileName.bedGraph file1.bedGraph file2.bedGraph ...`

### Output
The output will be a BedGraph file of the same format (`Chr start end cMBF`) and of the specified name. 



## Example Run(s)

## Things to Consider
- Window size: Larger is more accurate, but will take longer - 10K bp window takes about an hour with 16 threads and 16 GB free (*should I put this here*)
- Median multiple: Generally, a larger median multiple will reduce the background noise, but may also reduce signal. (*Find a browser to show*)
- Default zero: (*same as above?*)
- If you have multiple separated regions of the same chromosome, please place them into separate files to run the Driver (Part One). 

