# [Associate] SEPHORA Terraform Course - Lab 01

![SEPHORA_TERRAFORM](https://storage.googleapis.com/s4a-shared-terraform-gcs-lab-materials/sephora_terraform_bw.png)

After following the tutorial steps, you'll aquire the following knowledges:
- Know how to run terraform commands (init, plan and apply)
- know how to read and inspect Terraform code
- Apply modification to your infrastructure (update and destroy)


Firstly, we'll authorize gcloud to access Google Cloud Platform with user credentials :

```bash
gcloud auth login
```

Answer **Yes** when prompted, click the link that appears, chose your account then click allow **(Make sure to select your Sephora account)**, copy the code and paste it back to cloud shell.

press Enter. Now you should be able to interact with GCP via gcloud command. :white_check_mark:

Let's see what we have. Open Cloud Shell Editor and navigate to **associate/lab_01/iac/**

![](https://storage.googleapis.com/s4a-shared-terraform-gcs-lab-materials/cloudshell_editor.png)

<walkthrough-open-cloud-shell-button></walkthrough-open-cloud-shell-button>


<walkthrough-editor-open-file filePath="associate/lab_01/iac/main.tf">LINK_TEXT</walkthrough-editor-open-file>

<walkthrough-editor-spotlight spotlightId="activity-bar-cloud-k8s">LINK_TEXT</walkthrough-editor-spotlight>


<walkthrough-editor-open-file filePath="FILE_PATH">LINK_TEXT</walkthrough-editor-open-file>

Files in this directory are intended to deploy a GCS bucket to Google Cloud.

Replace **"your-project-id"** with an existing project id (Should be provided by socle GCP Team).
Bucket name should be unique accross the globe, we suggest you add your intials as suffix to the bucket name.

__Exemple__ : Jhon Do -> Bucket name = "auto-expiring-bucket**-jdo"**

Run :
```bash
terraform init
```
>The terraform init command is used to initialize a working directory containing Terraform configuration files. This is the first command that should be run after writing a new Terraform configuration or cloning an existing one from version control. It is safe to run this command multiple times.  
<em><u>source</u> :  [https://www.terraform.io/cli/commands/init](https://www.terraform.io/cli/commands/init)</em>

```bash
terraform plan
```
> The terraform plan command creates an execution plan, which lets you preview the changes that Terraform plans to make to your infrastructure. By default, when Terraform creates a plan it:
- Reads the current state of any already-existing remote objects to make sure that the Terraform state is up-to-date.  
- Compares the current configuration to the prior state and noting any differences.
- Proposes a set of change actions that should, if applied, make the remote objects match the configuration.
</br><br>
<em><u>source</u> :  [https://www.terraform.io/cli/commands/plan](https://www.terraform.io/cli/commands/plan)</em>

It's time now to create the resources describe by our IaC. To do this run:
```bash
terraform apply
```
Optioanally you can add the `--auto-approve` flag to the previous command to Ssip interactive approval of plan before applying. This can be usefull when running `terraform apply` command in CICD pipelines.

In the Google Cloud console, go to the Cloud Storage Buckets page and verify that the bucket is created successfully.

Notice that Terraform generates a **tfstate** file locally that contains traces of what has been created. Open the terraform.tfstate file with Cloud Shell Editor and see it's content.

<walkthrough-conclusion-trophy></walkthrough-conclusion-trophy>

You're all set!
