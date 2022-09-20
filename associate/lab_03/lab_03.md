# [Associate] SEPHORA Terraform Course - Lab 03 : Providers initialization
## Introduction
![SEPHORA_TERRAFORM](https://storage.googleapis.com/s4a-shared-terraform-gcs-lab-materials/sephora_terraform_bw.png)

In this lab, we will see :
  - Deploy resource
  - Use variables and locals
  - Use Data sources
  - Outputs
  - Resource dependencies

## Deploy resource
Let's use what we have learned in previous labs to create a bigquery dataset.

Decalre the provider first. Create a file named `provider.tf` under the folder `iac` with the following content:
```tf
provider "google" {
  project     = "PROJECT-ID"
  region      = "europe-west1"
}
```
**Tip** : Replace **PROJECT-ID** with a valid project id.

Now, let's declare the resource :
Create `/iac/main.tf`:
```tf
resource "google_bigquery_dataset" "dataset" {
  dataset_id                  = "example_dataset"
  friendly_name               = "test"
  description                 = "This is a test"
  location                    = "EU"
}
```

You can do this via Cloud Shell editor.

![cloud_shell_editor](https://storage.googleapis.com/s4a-shared-terraform-gcs-lab-materials/cloudshell_editor.png)

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
You should see similar output :
![tf_init](https://storage.googleapis.com/s4a-shared-terraform-gcs-lab-materials/tf_init.png)

Terraform GCP provider definition block supports multiple arguments. Here for instance we specified `project` and `region`, which means all resources are going to be created in that project and in that specefic region unless it is specified differently in the resource bloc. i.e `project` and `region` in the resource bloc overrides those in the provider definition bloc.

## Provider credentials

Terraform Provider supports the credentials field (Optional) that should be set to the path of a service account key file, not user account credentials file.
```tf
provider "google" {
  project     = "PROJECT-ID"
  region      = "europe-west1"
  credentials = "/path/to/service_account.key"
}
```

If you want to authenticate with your user account try omitting credentials and then running :
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

## Create resources

Let's try out to create a GCS storage bucket using that provider.

Create a file named `main.tf` at the same directory as `provider.tf`.

```tf
resource "google_storage_bucket" "static" {
  provider      = "google"
  name          = "Bucket-Name"
  storage_class = "COLDLINE"
  force_destroy = true

  uniform_bucket_level_access = true
}
```
**Notice**: Bucket name should be unique accross the globe, we suggest you add your intials as suffix to the bucket name in `main.tf`.

__Example__ : Jhon Do -> Bucket name = "auto-expiring-bucket**-jdo"**

Verify the bucket is created in **europe-west1** in the project defined in the provider resource bloc.

## Override Provider attributes

Let's deploy the bucket to another project in a a different location.

```tf
resource "google_storage_bucket" "static" {
  provider      = "google"
  project       = "Another-Project-ID"
  name          = "Bucket-Name"
  Location      = "europe-west1-c"
  storage_class = "COLDLINE"
  force_destroy = true

  uniform_bucket_level_access = true
}
```

**Tip** : Replace **Another-Project-ID** with a valid project id.

Verify that the bucket is recreated in "europe-west1-c" in the right project.

## Use different providers for resources
Use the provider argument for every GCP resource: `google-beta` for any resource that has at least one enabled Beta feature:
```tf
resource "google_container_cluster" "beta_cluster" {
   . . .
   provider        = google-beta
   . . .
}
```

and and `google` for every other resource:
```tf
resource "google_container_node_pool" "general_availability_node_pool" {
  . . .
  provider       = google
  . . .
}
```
