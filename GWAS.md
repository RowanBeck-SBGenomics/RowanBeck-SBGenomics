# **Overview of the GENESIS pipeline**

The GENetic EStimation and Inference in Structured samples (GENESIS) R package provides efficient methods of working with genotypes measured in sequencing and microarrays. These tools were developed by the TOPMed Data Coordinating Center (DCC) at the University of Washington. The Seven Bridges team worked with the TOPMed DCC to create Common Workflow Language (CWL) tools for the GENESIS R functions and arranged these tools into computationally efficient workflows.


The GENESIS workflows are a good fit for performing association studies on TOPMed data because of their robust ability to estimate and account for population and pedigree structure. GENESIS implements linear mixed models for association testing of quantitative phenotypes and logistic mixed models using the penalized quasi-likelihood approach of GMMAT for association testing 1 of binary (e.g. case/control) phenotypes. The mixed models utilize the PC-AiR PCs and a relatedness matrix of PC-Relate kinship coefficient estimates to accurately and efficiently adjust for ancestry and relatedness in the sample. When no relatedness matrix is provided, simple linear or logistic regression models are used for quantitative or binary traits, respectively. Heterogeneous residual variances can be used to account for differences in quantitative phenotype variability by user-specified group. For computational efficiency, a “null model” is fit once under the null hypothesis of no genotype effect, and variant association is subsequently tested genome-wide. Available association tests include single variant tests as well as multiple variant aggregate tests. Single variant tests are performed with score tests, and approximations of effect sizes are provided. The saddlepoint approximation (SPA) of p-values is available when testing binary phenotypes. Multiple variant tests can be performed using the burden, SKAT, SKAT-O, fastSKAT, and SMMAT methods. Each of these methods can be run with user-defined variant groupings (e.g. by gene) or with a sliding window approach, and they can incorporate either allele frequency-based or user-defined (e.g. utilizing variant annotation) variant weighting. GENESIS can utilize sparse matrix representation of relatedness and genotype matrices for a substantial reduction in computational demand in large samples such as TOPMed.


This tutorial will first guide you through the steps of performing a single variant association test using TOPMed study data and then move on to a multiple variant test. The overall steps of the single variant test are as follows:

1. Create your project
2. Bring VCF data to your project 
  - OPTIONAL: Upload kinship and PCA data
  - OPTIONAL: Bring the GENESIS workflows to your project
3. Convert VCF files to GDS file format
  - OPTIONAL: Upload harmonized phenotype or create harmonized phenotype in RStudio on platform
4. Fit Null Model
5. Run the single variant association test workflow

For more information on association testing with the GENESIS R package, refer to the following:

- __Gogarten SM__, Sofer T, Chen H, Yu C, Brody JA, Thornton TA, Rice KM, Conomos MP. 2019. Genetic association testing using the GENESIS R/Bioconductor package. Bioinformatics. Btz567. https://doi.org/10.1093/bioinformatics/btz567
- Course materials for “Computational Pipeline for WGS Data” at the Summer Institute for Statistical Genetics: https://uw-gac.github.io/SISG_2020/  
- GENESIS software can be found on Bioconductor and Github: http://bioconductor.org/packages/release/bioc/html/GENESIS.html https://github.com/UW-GAC/GENESIS


# **Organizing a Workspace and Preparing Data** 

Prior to running the association test, you will need to complete some preparation work. The following steps will cover:
- Create a project for the test GWAS
- Add variant data to a secure project on the platform
- Find the GENESIS workflows on the platform and add them to a project
- Run the VCF to GDS tool to get the variant data in the appropriate format for the GENESIS workflows
- Access RStudio for use in phenotype harmonization
- Fit Null Model

# **Project Creation**

The first step is to create a secure project for working with the WGS data. Projects are workspaces that serve as the core building blocks of BioData Catalyst Powered by Seven Bridges. Each project corresponds to a distinct scientific investigation and serves as a container for data, analysis workflows, and results. Multiple analyses can be carried out within a project.

For more information about project creation, see the Getting Started Guide. The following terms will be used throughout the tutorial.

- __Project__: Workspace for organizing files and analyses.
- __App__: Bioinformatics tools and workflows. There are hundreds of Apps hosted on the platform and users can also bring their own.
- __CWL__: Common Workflow Language. All hosted tools and workflows are described in CWL which is both human and machine-readable and has all the necessary information to run the tool in a reproducible way.
- __Task__: Single execution of a bioinformatics tool or workflow.

To complete the steps in this tutorial, create a new project with the title test GWAS. Choose the following settings for the project:
- __Billing Group__: Pilot Funds
- __Location__: aws-us-east-1
- __Spot instances__: On
- __Memoization__: Off
- __Controlled data__: Yes
- __Network Access__: Block Network Access

# **Adding GENESIS Apps from Public Gallery**:
The Seven Bridges bioinformatics team worked with the TOPMed Data Coordinating Center to bring CWL versions of the GENESIS pipelines to BioData Catalyst. These workflows were optimized for the cloud and can be found in the Public Gallery as shown below:

![image](https://user-images.githubusercontent.com/104579474/167710507-d85f6acd-56cb-4ad1-8748-cd465e11f52e.png)

To find the pipelines click on __Public Gallery - Apps__ and search for __“GENESIS.”__ You will find the VCF to GDS workflow, the null model workflow, and 3 options for association testing (single variant, aggregate, sliding window). For the needed workflows, click Copy and select your working project as a destination.
For this tutorial, please copy the following workflows to the “test GWAS” project:
- VCF to GDS
- Null model
- Single-variant association testing

On the right side of the screen click on “copy” and add these tools to your test GWAS project. You should now see all three workflows in the Apps tab of your “test GWAS” project.

![image](https://user-images.githubusercontent.com/104579474/167710729-151be7a9-8f4c-464c-8686-aab3c149d133.png)

# **VCF to GDS Conversion**

The next key step for running the GENESIS pipelines is to convert the VCF files into the GDS format. Variant data is often in the VCF file format. However, the GENESIS packages require the inputs to be in the Genomic Data Structure (GDS) format. Therefore, now that the data files have been added to the project, the next step is to convert them to GDS files using the “GENESIS VCF to GDS” workflow.

This workflow is composed of three tools. Step 1 (vcf2gds) converts VCF or BCF files (one per chromosome) into GDS files, with the option to keep a subset of FORMAT fields, by default only GT field. Step 2 (Unique Variant IDs) ensures that each variant has a unique integer ID across the genome. Step 3 (Check GDS) ensures that no important information is lost during conversion. If Check GDS fails, it is likely that there was an issue during the conversion. This step is time-consuming (and therefore expensive). The execution is approximately 10 times longer than the rest of the workflow. Also, the failure of Check GDS is rare and for those reasons this step is optional. The workflow will run vcf2gds once per chromosome and check that each uses the unique_variant_id app to make sure each variant in the new GDS has a unique integer id.

When you are ready to run the workflow, click on the Apps tab in your project and select “Run” on the VCF to GDS conversion app. Your next screen will show a DRAFT Task setup page and look like this:

![image](https://user-images.githubusercontent.com/104579474/167711106-e0c3df7a-328b-46aa-812e-41eb40be2820.png)

The task will go through 3 status labels: __Queued, Running__, and __Complete__. Once your task is complete you will be able to continue with the next section. Once completed, your GDS file outputs will be listed under the “Outputs” section of the completed Task page. Please note that you will only need to generate the GDS files once for a particular set of samples. These GDS files can be used in multiple association tests with different phenotypes.
This pipeline expects that input VCF files are separated per chromosome and that file is properly named. The expected format of the file name is: __prefix_chr##_suffix.[bcf, vcf, vcf.gz]__, where the “##” is the name of the chromosome (1-24, or X, Y).

The parameter for “number-of-CPUs” should only be used when working with VCF or VCF.GZ files. The workflow is unable to utilize more than one thread when working with BCF files and changing this input parameter will have no effect in this case. Variant IDs in output workflow might be different than expected. Unique variants are assigned for one chromosome at a time, in ascending order, and chromosomes are sorted in natural order (1,2,3,..,24, or X,Y). Variant IDs are integer IDs unique to your data and do not map to rsIDs or any other standard identifier. Be sure to use __variant_id__ file for down-the-line workflows generated based on GDS files created by this workflow.

__Please note that if you are working with multiple studies or consent groups, we recommend first merging VCFs from the separate studies/consent groups into a single VCF and then converting that single VCF file to the GDS format__. This was found to be faster than converting multiple VCFs to the GDS format and then merging the GDS files. We recommend using Bcftools Merge to combine the VCF files. In the Public Apps Gallery there are two versions of this app: BCFtools merge and BCFtools merge DCC. The second version is BCFtools merge which includes a filter for monomorphic variants. When working with the datasets that have monomorphic variants such as TOPMed datasets, BCFtools merge DCC is highly recommended. The end results should be a single VCF file per chromosome. Each VCF should include all samples in your analysis cohort.


# **Uploading Additional Files for the Association Test**

In addition to the VCF files, you will need the following files to perform the GWAS:
- Principal components
- Kinship matrix
- Phenotype file

Researchers can upload these file types to the project using either the command line uploader (linux) or the GUI-based Desktop Uploader (Windows, Mac OS, Linux), both of which can be found under “Data Tools” in the Data tab of the top navigation bar. Please refer to the Documentation pages on “Bring your own data” to read additional details on how to use these features. In addition, for researchers who already have their own harmonized phenotype file, they may also upload this directly to the system.

# **Fit Null Model**

The next step is to fit a __Null Model__. The Null Model workflow fits the regression or mixed effects model under the null hypothesis of no genotype effects; the outcome variable is regressed on only the specified fixed effect covariates and random effects. The single variant genotypes or multiple variant aggregation units are not included in this model. The output of this Null Model is then used in the association test workflow of choice.

This workflow consists of two steps: The first step fits the Null Model, and the second step generates reports based on the fitted model and data. Reports are available both in Rmd and html format. This workflow utilizes the fitNullModel function from the GENESIS software.

Both continuous (__family__ = “gaussian”) and discrete (__family__ = “binomial” or “poisson”) phenotypes are supported. The outcome and covariate values must be included in the __Phenotype__ file and specified with the __Outcome__ and __Covariates__ parameters. Principal components of ancestry can be included as covariates using the PCA file input, and genetic relatedness can be accounted for as a random effect using the __Relatedness matrix file__ input. 

When working with a quantitative phenotype, heterogeneous residual variances by group can be specified using the __Group variate__ parameter.
If __pca_file__ is not provided, the __n_pcs__ parameter must be set to 0. __pca_file__ must be an RData file with object type “pcair”, data.frame, or matrix. For more information, see the section on Ancestry and Relatedness above. The Null Model job can be very computationally demanding when fitting mixed models in large samples (for example, greater than 20K). GENESIS supports using sparse representations of matrices in the relatedness_matrix_file using the R Matrix package, and this can substantially reduce memory usage and CPU time.

# **Running Single Variant Association Tests**

To perform a single-variant association test, use the __GENESIS Single Variant Association Testing__ workflow. The single-variant test is similar to a traditional GWAS and consists of several steps. First, the __define_segments.R__ tool divides the genome into segments, either by a set number of segments or by segment length. Note that number of segments refers to the whole genome, not a number of segments per chromosome. Next, the association test is performed for each segment in parallel, before combining results on the chromosome level. The final step in the workflow produces QQ and Manhattan plots of the association p-values.

This workflow uses the output from the null model workflow and the genotype data to perform score tests for all variants individually. The reported effect estimate is for the alternate allele, and multiple alternate alleles for a single variant are tested separately. When testing a binary outcome, the saddlepoint approximation (SPA) for p-values (see here and here), can be used by specifying __test_type 11 12 = ‘score.spa’__; this is generally recommended. SPA will provide better calibrated p-values, particularly for rarer variants in samples with case-control imbalance.

If your genotype data has sporadic missing values, they are imputed to the mean alternate allele count using the allele frequency observed in the sample.
On the X chromosome, males have genotype values coded as 0/2 (females as 0/1/2).
This workflow utilizes the assocTestSingle function from the GENESIS software.
__Assoc_single job__ can be very memory demanding, depending on the number of samples and null model used. For more information on optimal resources needed for an Assoc_single job, see the GENESIS benchmarking guide. If a run fails with __error 137__, and with the message “killed” displayed, the likely cause is a lack of memory.
Your completed task for the GENESIS Single Variant Association test should be similar to the following image.

![image](https://user-images.githubusercontent.com/104579474/167712654-859f735b-8a32-4e32-97ba-d0a51f206f9d.png)


**Results and Quality Control**

The typical quality control plots are __QQ plot__ and __Manhattan plot__. These are both available to you regardless of the association test you run. The QQ plots are also available at the chromosome level. The .png files can be viewed natively on the platform using your web browser. As you iterate over different association test experiments, these plots will remain saved in your project for future reference.

<img width="689" alt="Screen Shot 2022-05-10 at 4 10 26 PM" src="https://user-images.githubusercontent.com/104579474/167713547-d21cc87b-00ed-4ccd-8578-65a2dc479029.png">

<img width="1099" alt="Screen Shot 2022-05-10 at 4 09 55 PM" src="https://user-images.githubusercontent.com/104579474/167713551-a38ae928-151e-4730-83fc-9cf10833c8ec.png">


