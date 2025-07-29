Instructions for Using ParseCNV2 for Genome-Wide CNVR Association Analysis

**Section 1: Before running the analysis**
1. Download the Software Package

   Because the size of ParseCNV2 toolbox is big (~400MB), I placed this toolbox on two platforms. Please check the ParseCNV2 package at:

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

4. Preprocessing Phenotype Data

   Prior to running ParseCNV2, it's recommended to regress out covariates (e.g., age, sex, principal components) from your phenotype of interest.

   Use the residuals from this regression as the input phenotype in the CNV association analysis.

5. Additional Documentation

   For more detailed usage instructions and advanced options, refer to the official ParseCNV2 documentation at: https://github.com/CAG-CNV/ParseCNV2

**Section 2: Preparing Input Files for ParseCNV2**

To run genome-wide CNVR association analysis using ParseCNV2, you need to prepare two essential input files:

CNV calls file

Phenotype file

1. CNV Calls File

File extension: The file must have a .rawcnv extension.

Important: Do not use .txt or any other extension — these will not be recognized by the program.

Format:

The file should contain exactly 8 columns, in the specific order listed below.

No header row should be included.

Columns must be space-delimited (i.e., separated by spaces).

Warning: Using tabs instead of spaces will cause errors during processing.

Column descriptions:

The first column: CNV Region Name — e.g., chr4:92886271-93728711, indicating a CNV on chromosome 4, spanning from position 92,886,271 to 93,728,711.

The second column: Number of SNPs in CNV — e.g., numsnp=600

The third column: Length of CNV (in base pairs) — e.g., length=842440

The fourth column: Copy Number State — e.g., state5,cn=3. It's usually obtained from software like QuantiSNP. In this example, it indicates a heterozygous duplication.

The fifth column: Subject ID — e.g., NDAR_INVZZZP87KR

The sixth column: Start SNP ID — e.g., startsnp=rs1472021

The seventh column: End SNP ID — e.g., endsnp=rs10021312

The eighth column: Confidence Score — e.g., conf=334.352

Example entry: chr4:92886271-93728711 numsnp=600 length=842440 state5,cn=3 NDAR_INVZZZP87KR startsnp=rs1472021 endsnp=rs10021312 conf=334.352

2. Phenotype File

File extension: .txt is acceptable.

Format:

The file should contain three columns, with no header, tab-delimited.

Column descriptions:

Dummy Column: Always set to 0. (This column is required by the format but not used.)

Subject ID: Must match the subject IDs used in the CNV calls file.

Phenotype Value: Typically a residual value after regressing out covariates.

Example entry: 0	NDAR_INVZZZP87KR	1.234


**Section 3: Running ParseCNV2**

To perform a genome-wide CNVR association analysis using a continuous phenotype, you can use the following command:

perl ParseCNV2_RevisedABCD.pl \
  -i cnv_input.rawcnv \
  -q pheno.txt \
  -b hg19 \
  -batch batch_id.txt \
  -stat linear \
  -no_freq \
  -m 1 \
  -o pheno

| **Parameter** | **Description**                                                                                                                                                                                                                                        |
| ------------- | ------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------ |
| `-i`          | Specifies the path to the CNV calls file (must end in `.rawcnv`).                                                                                                                                                                                  |
| `-q`          | Specifies the path to the phenotype file (tab-delimited, 3 columns as previously described).                                                                                                                                                       |
| `-b`          | Specifies the genome build used for CNV coordinates. Use `hg19` for GRCh37-based coordinates.                                                                                                                                                      |
| `-batch`      | Specifies the batch ID file, used to adjust for genotype batch effects. This file must be tab-delimited, with two columns:<br>1. Subject ID<br>2. Batch ID                                                                                     |
| `-stat`       | Specifies the statistical model to use. For continuous phenotypes, use `linear` (i.e., linear regression).                                                                                                                                         |
| `-no_freq`    | Disables CNV frequency filtering. By default, ParseCNV2 filters for rare (MAF < 0.01) and recurrent (MAC > 2) CNVs. If you only want to perform CNVR-based GWAS (and not CNV filtering/preprocessing), include this flag.                          |
| `-m`          | Specifies the p-value threshold for reporting results. <br>– `-m 0.05` reports results with *p* < 0.05.<br>– `-m 1` reports all CNVR results (*p* < 1).<br> It is recommended to use `-m 1` to export the full output. Default is 0.05 if omitted. |
| `-o`          | Specifies the prefix for output files (e.g., `pheno`). All result files will begin with this prefix.                                                                                                                                               |



**Section 4: Interpreting ParseCNV2 Output**

Among all the output files generated by ParseCNV2, the most critical file is ParseCNV_Report.txt
 
This file contains the summary statistics for all CNVRs tested in the genome-wide CNV association analysis. The file includes 10 columns, each with specific information:

| **Column #**                       | **Description**                                                                                                                                                                                                                                                                                          |
| ---------------------------------- | -------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| **1. Chromosome**                  | The chromosome number where the CNVR is located. <br>Example: `6`                                                                                                                                                                                                                                        |
| **2. Start Position**              | The genomic start coordinate (based on the specified genome build, e.g., hg19) of the CNVR. <br>Example: `30882277`                                                                                                                                                                                      |
| **3. End Position**                | The genomic end coordinate of the CNVR. <br>Example: `31066419`                                                                                                                                                                                                                                          |
| **4. P-value**                     | The p-value from the association test (e.g., linear regression) for this CNVR. <br>Example: `0.0001`                                                                                                                                                                                                     |
| **5. Beta Value**                  | The estimated regression coefficient (effect size) for the CNVR. <br>Example: `-3.79807`                                                                                                                                                                                                             |
| **6. Number of Cases with CNV**    | Number of cases overlapping this CNVR (for case-control analysis). <br>Example: `1` Note: According to Joe, this value has not been fully debugged and may not accurately reflect the actual number of affected samples. Use with caution.                                                 |
| **7. Number of Controls with CNV** | Number of controls overlapping this CNVR. <br>Example: `1` This value is also potentially inaccurate. To verify the actual subject IDs and CNVs contributing to each region, refer to the `ParseCNV_Report_ContributingCalls.txt` file.                                                        |
| **8. Confidence Label**            | Indicates whether the CNVR is considered reliable based on internal quality metrics (e.g., `PASS`). Note: These internal quality checks are based on criteria described in the original ParseCNV2 paper. However, if your study uses different QC filters, this column can be ignored. |
| **9. CNVR Type**                   | Indicates whether the CNVR is a deletion (`del`) or duplication (`dup`).                                                                                                                                                                                                                         |
| **10. CNVR ID**                    | A unique ID for the CNVR, composed of: `<chromosome>:<start>-<end>-<type>` <br>Example: `6:30882277-31066419-DUP`                                                                                                                                                                                        |







   
