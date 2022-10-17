# [Associate] SEPHORA Terraform Course - Lab 03 : resources/variables/locals/data/outputs
## Introduction
![SEPHORA_TERRAFORM](https://storage.googleapis.com/s4a-shared-terraform-gcs-lab-materials/sephora_terraform_bw.png)

In this lab, we will see :
  - Deploy resource
  - Use variables and locals
  - Use Data sources
  - Outputs
  - Resource dependencies

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

   <walkthrough-project-setup></walkthrough-project-setup>

  Run :
  ```bash
   gcloud auth application-default set-quota-project <walkthrough-project-id/>
  ```
  **Tips**: Socle Team should tell you which project you should select.

  This command will add a quota project in application default credentials and saves the credentials file to a temp directory :

  ![SEPHORA_TERRAFORM](https://storage.googleapis.com/s4a-shared-terraform-gcs-lab-materials/cred_path.png)

  Run :
  ```bash
  export GOOGLE_APPLICATION_CREDENTIALS=<path-to-cred-file>
  ```

## Deploy resource
Let's use what we have learned in previous labs to create a bigquery dataset.

Edit provider.tf and add the following bloc :

<walkthrough-editor-open-file
    filePath="cloudshell_open/terraform_labs/associate/lab_03/iac/provider.tf">
    Open provider.tf
</walkthrough-editor-open-file>

```tf
provider "google" {
  project     = "<walkthrough-project-id/>"
  region      = "europe-west1"
}
```

Now, let's declare the resource, edit

<walkthrough-editor-open-file
    filePath="cloudshell_open/terraform_labs/associate/lab_03/iac/main.tf">
    Open main.tf
</walkthrough-editor-open-file>

```tf
resource "google_bigquery_dataset" "dataset" {
  dataset_id                  = "example_dataset"
  friendly_name               = "test"
  description                 = "This is a test"
  location                    = "EU"
}
```

Run
```bash
cd ~/cloudshell_open/terraform_labs/associate/lab_03/iac/
```

```bash
terraform init
```
```bash
terraform plan
```
```bash
terraform apply
```

You should see similar output :
![tf_apply](https://storage.googleapis.com/s4a-shared-terraform-gcs-lab-materials/tf_apply.png)

## Use variables

Let's make the code looks better by using variables.

Update `main.tf`
<walkthrough-editor-open-file
    filePath="cloudshell_open/terraform_labs/associate/lab_03/iac/main.tf">
    Open main.tf
</walkthrough-editor-open-file>

```tf
resource "google_bigquery_dataset" "dataset" {
  dataset_id                  = var.dataset_id
  friendly_name               = var.friendly_name
  description                 = var.description
  location                    = var.location
}
```

<walkthrough-editor-open-file
    filePath="cloudshell_open/terraform_labs/associate/lab_03/iac/variables.tf">
    Open variables.tf
</walkthrough-editor-open-file>

```tf
variable "dataset_id" {
  type : string
  description : the dataset id
}
variable "friendly_name" {
  type : string
  description : Dataset display name
}
variable "description" {
  type : string
  description : Dataset description
}
variable "location" {
  type : string
  description : the dataset location
  default: "EU"
}
```

Update `terraform.tfvars`
<walkthrough-editor-open-file
    filePath="cloudshell_open/terraform_labs/associate/lab_03/iac/terraform.tfvars">
    Open terraform.tfvars
</walkthrough-editor-open-file>

```tf
dataset_id    = "example_dataset"
friendly_name = "test"
description   = "This is a test"
location      = "EU"
```

Run
```bash
terraform init
```
```bash
terraform plan
```

You should see "No changes. Your infrastructure matches the configuration."

## Use locals
Terraform local values (or "locals") assign a name to an expression or value. Using locals **simplifies** your Terraform configuration – since you can reference the local **multiple** times, you **reduce** duplication in your code. Locals can also help you write **more readable** configuration by using meaningful names **rather** than hard-coding values.

Suppose you have a resource naming convention within your organization that says that a Bigquery Dataset should always be prefixed with the project id. You can use local variables to do so.
Update `main.tf`
```tf
locals {
  org_dataset_id = "my-project-id-${var.dataset_id}"

}
resource "google_bigquery_dataset" "dataset" {
  dataset_id                  = locals.org_dataset_id
  friendly_name               = var.friendly_name
  description                 = var.description
  location                    = var.location
}
```
Run
```bash
terraform init
```
```bash
terraform plan
```
```bash
terraform apply
```
