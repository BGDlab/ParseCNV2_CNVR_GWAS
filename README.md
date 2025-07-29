Instructions for Using ParseCNV2 for Genome-Wide CNVR Association Analysis
Section 1: Before running the analysis
1. Download the Software Package

   Because the size of ParseCNV2 toolbox is big, I placed this toolbox on two platforms. Please check the ParseCNV2 software package at:

   PMACS: /project/bbl-bgd/software/ParseCNV2/

   Respublica: /mnt/isilon/bgdlab_processing/code/ParseCNV2/

   Note: This version of ParseCNV2 has been debugged and tested by Joe and Zhiqiang. It is confirmed to run without errors.

2. Environment Setup on PMACS

   If you're running the analysis on PMACS, installation is not necessary. Simply load the required modules using the following commands:

   module load perl

   module load R/4.0

   module load plink/1.9-20210416

   Tip: Make sure you are using the correct versions as specified above to ensure compatibility.

3. Preliminary Debugging Check

   Before running your full analysis, perform a test run to check if the pipeline is functioning properly:

   perl ParseCNV2_RevisedABCD.pl

   Note: If the script returns errors, there may be an issue with the installation or your environment setup. Troubleshoot before proceeding.








   
