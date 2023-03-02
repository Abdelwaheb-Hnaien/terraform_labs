# [Advanced] SEPHORA Terraform Course - Lab 01 : Resource Lifecycle
## Introduction
![SEPHORA_TERRAFORM](https://storage.googleapis.com/s4a-shared-terraform-gcs-lab-materials/sephora_terraform_bw.png)

This lab is about setting resource behaviour using the special nested **lifecycle** block within a resource block body.

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
Let's deploy some resources:

Edit provider.tf and add the following bloc :

<walkthrough-editor-open-file
    filePath="cloudshell_open/terraform_labs/advanced/lab_01/iac/provider.tf">
    Open provider.tf
</walkthrough-editor-open-file>

```tf
provider "google" {
  project     = "<walkthrough-project-id/>"
  region      = "europe-west1"
}
```

Edit main.tf

<walkthrough-editor-open-file
    filePath="cloudshell_open/terraform_labs/advanced/lab_01/iac/main.tf">
    Open main.tf
</walkthrough-editor-open-file>

```tf
# Bigquery Dataset
resource "google_bigquery_dataset" "dataset" {
  dataset_id                  = "tf_ad_lab_bqd"
  friendly_name               = "test"
  description                 = "This is a test"
  location                    = "EU"
}

# GCS Bucket
resource "google_storage_bucket" "static-site" {
  name          = "tf_ad_lab_gcs"
  location      = "EU"
  force_destroy = true

  uniform_bucket_level_access = true
}
```

dataset_id and bucket name should be unique in the project, to avoid potential conflict with other students we recommend you to add your intials as suffix.
.

__Example__ : John Do -> dataset_id = "tf_ad_lab_bqd_jdo"

Let's access the working directory :
```bash
cd ~/cloudshell_open/terraform_labs/advanced/lab_01/iac/
```
Initialize terraform :
```bash
terraform init
```
Plan your infrastructure :
```bash
terraform plan
```
You should see similar output :
![tf_apply](https://storage.googleapis.com/s4a-shared-terraform-gcs-lab-materials/tf_apply.png)

Let's deploy the resource :
```bash
terraform apply --auto-approve
```
[Go to Bigquery Dataset list page](https://console.cloud.google.com/bigquery?referrer=search&orgonly=true&project=<walkthrough-project-id/>)

Verify that the bucket and the dataset are created successfully.
