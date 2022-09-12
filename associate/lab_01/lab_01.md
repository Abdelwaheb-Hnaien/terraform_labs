# [Associate] SEPHORA Terraform Course - Lab 01
## Introduction
![SEPHORA_TERRAFORM](https://storage.googleapis.com/s4a-shared-terraform-gcs-lab-materials/sephora_terraform_bw.png)

After following the tutorial steps, you'll aquire the following knowledges:
- Know how to run terraform commands (init, plan and apply)
- know how to read and inspect Terraform code
- Apply modification to your infrastructure (update and destroy)

## Set up authentication

First we need to authenticate and access our project
```bash
gcloud auth application-default login --no-launch-browser
```
Steps:
 - Answer **Yes** when prompted
 - Click the link that appears
 - Chose your account **(Make sure to select your Sephora account)**
 - Click allow
 - Copy the code and paste it back to cloud shell
 - Press Enter.

Now you should be able to interact with GCP with gcloud command.

## Connect to a GCP project

```bash
./associate/lab_01/setup_project.sh <walkthrough-project-name/>
```
**TIPS :** don't forget to change `PROJECT_ID`. Ask Sephora Socle team if you don't have it.

<walkthrough-editor-open-file
    filePath="cloudshell_open/terraform_labs/associate/lab_01/setup_project.sh">
    check script
</walkthrough-editor-open-file>


## Inspect terraform code
Let's see what we have, open Cloud Shell Editor and take a look at the file named main.tf :

<walkthrough-editor-open-file filePath="cloudshell_open/terraform_labs/associate/lab_01/iac/main.tf">Open main.tf</walkthrough-editor-open-file>

Files in this directory are intended to deploy a GCS bucket to Google Cloud.
***
## Make changes to the code
Replace **"your-project-id"** with an existing project id (Should be provided by socle GCP Team). Bucket name should be unique accross the globe, we suggest you add your intials as suffix to the bucket name.

__Example__ : Jhon Do -> Bucket name = "auto-expiring-bucket**-jdo"**

## Run Terraform commands

Run :
```bash
terraform init
```
<em>The terraform init command is used to initialize a working directory containing Terraform configuration files. This is the first command that should be run after writing a new Terraform configuration or cloning an existing one from version control. It is safe to run this command multiple times.</em>  

<em><u>source</u> :  [https://www.terraform.io/cli/commands/init](https://www.terraform.io/cli/commands/init)</em>

```bash
terraform plan
```
<em>The terraform plan command creates an execution plan, which lets you preview the changes that Terraform plans to make to your infrastructure. By default, when Terraform creates a plan it:
- Reads the current state of any already-existing remote objects to make sure that the Terraform state is up-to-date.  
- Compares the current configuration to the prior state and noting any differences.
- Proposes a set of change actions that should, if applied, make the remote objects match the configuration.</em>

<em><u>source</u> :  [https://www.terraform.io/cli/commands/plan](https://www.terraform.io/cli/commands/plan)</em>

It's time now to create the resources describe by our IaC. To do this run:
```bash
terraform apply
```
Optioanally you can add the `--auto-approve` flag to the previous command to Ssip interactive approval of plan before applying.

This can be usefull when running `terraform apply` command in CICD pipelines.

In the Google Cloud console, go to the Cloud Storage Buckets page and verify that the bucket is created successfully.

## Inspect Terraform tfstate

Notice that Terraform generates a `terraform.tfstate` file locally that contains traces of what has been created. Inspect the file:

<walkthrough-editor-open-file filePath="cloudshell_open/terraform_labs/associate/lab_01/iac/terraform.tfstate">Open terraform.tfstate</walkthrough-editor-open-file>

## Cleanup

```bash
terraform destroy --auto-approve
```
In the Google Cloud console, go to the Cloud Storage Buckets page and verify that the bucket is no longer existing.

You're all set!

<walkthrough-conclusion-trophy></walkthrough-conclusion-trophy>
