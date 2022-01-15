# BayPass_scripts
A compilation of notes/commands used for identifying markers associated with population-specific covariates with BayPass (Gautier 2015) 

BayPass is a freely available software developed by Mathieu Gautier and used for identifying genetic markers associated with selection or population-specific covariates. The method is described in _Gautier, M. 2015. Genome-Wide Scan for Adaptive Divergence and Association with Population-Specific Covariates. Genetics 201: 1555-1579_. 

Although powerful, I have found BayPass particularly difficult to navigate as a first-time user. There's a dearth of materials related to how to efficiently prepare files for input or troubleshoot errors, and the manual and associated paper are incredibly dense. Here I'm gathering notes for myself as I run an analysis to identify sites associated with a single environmental covariate using an existing SNP dataset that I generated with RAD data. 

# Getting Started 
I am using BayPass version 2.3, which can be downloaded here: http://www1.montpellier.inra.fr/CBGP/software/baypass/download.html. The manual suggests that BayPass is very simple to install -- and it probably is if you know what you're doing -- but I had a lot of difficulty getting this installed on the server I use, for which my permissions are limited. I couldn't make sense of the errors but I did get it installed without issue on a second server with a gfortran compiler so all of my commands will start with **prefix "g_"**. This should be obvious if you look at the executable you've installed but it is not specified in the manual so it took me a minute. If you compile with iFort then you will need to use the **prefix "i_"** instead.

## Input files
BayPass requires a genotype file to run, which is (predictably) not in any standard format that you might already have your SNPs in. The file needs to have the allele counts for each population for each SNP in the dataset. Each SNP is represented by a single row in the file and each column is the count data for the reference or alternate markers at the position for a given population, meaning the file should have as many rows as there are SNPs and as many columns as there are populations x 2. There are no headers in this file, so making sure that you know the order of the SNPs and populatiosn is important. My SNPs were in VCF format so I used a script that Yann Dorant wrote to do this efficiently: https://gitlab.com/YDorant/Toolbox. This script requires the VCF file and a population map file that includes the population assignment for each individual in the VCF. This script works great but, importantly, you need to pay attention to the order of the output, which is printed to the screen and is not the same as the input. 

I am also using a covariate file that contains the values of, in this case, one environmental covariate for each population in my analysis. Each row can represent a differnt covariate and each column is the value for one population. In my case, this is Annual Temperature Range data downloaded from the WorldClim dataset for each sampling site in my dataset. Again, this file doesn't have any headers. Make sure that the order of populations matches the order in the genotype file. 

# Running the models 
BayPass is easy to run from the command line, e.g.

_g_baypass -gfile snps_filename -outprefix core_

The manual has rather thorough descriptions of all of the options available to use. BayPass also comes with a R utility directory (baypass_utils.R) to help convert the output files generated by BayPass into useful plots. 

However, there is also a nice wrapper that Francisco Pina Martins wrote that automates everything in the BayPass Manual: https://github.com/StuntsPT/pyRona/blob/master/pyRona/R/Baypass_workflow.R. Basically, you just have to modify the top of this script to include the exectuable path, filenames, prefix, and analysis details of your choice. The workflow is run in R by sourcing the script, e.g. _source("baypass.workflow.R")_. There are a few dependencies that must be installed, as well (R libraries corrplot, ape, mvtnorm, geigen). I installed pyRona (https://pyrona.readthedocs.io/en/latest/install/), which automatically downloaded the baypass.workflow.R script but note that it was slightly modified from that available on github above. I would recommend this version of the workflow because it uses one additional file (which is not required by BayPass itself) specified as "cs.snp.id". Basically, this is a nice way to incorpoate the information from the first three columns of your VCF into your output so that you know which markers are where, which BayPass doesn't do. There is no description for this file but it should contain information about the position of each SNP in the dataset with a minimum requirement of columns ID and CHROM, though you can pass additional arguments, like POS here, too.

Overall, this workflow is exceedinly easy to use and it's nice because it automates the production of output plots for you. However, it is missing a few key features that I found helpful to add in, allowing me to adjust the number of iterations run, the thinning size, and the seed set. On top of that, it is recommended to run each model in triplicate, which the wrapper did not do. So I modified the workflow to meet my needs and will post that here. 

So far, I haven't had any luck running the MCMC models with option _-covmcmc_. I'm not sure why yet...the errors are not particularly helpful.

_More soon..._
