# [Associate] SEPHORA Terraform Course - Lab 02 : Providers initialization
## Introduction
![SEPHORA_TERRAFORM](https://storage.googleapis.com/s4a-shared-terraform-gcs-lab-materials/sephora_terraform_bw.png)

In this lab, you'll learn how to work with Terraform **GCP Provider**.

## Initialize Provider

<walkthrough-project-setup></walkthrough-project-setup>

**Tip** : Socle GCP Team should tell you what project to select.

Copy and paste the following code to the file named <walkthrough-editor-open-file filePath="cloudshell_open/terraform_labs/associate/lab_02/iac/provider.tf">provider.tf</walkthrough-editor-open-file>

```tf
provider "google" {
  project     = "<walkthrough-project-id/>"
  region      = "europe-west1"
}

provider "google-beta" {
  project     = "<walkthrough-project-id/>"
  region      = "europe-west1"
}
```

Run
```bash
cd ~/cloudshell_open/terraform_labs/associate/lab_02/iac
```
```bash
terraform init
```
You should see similar output :
![tf_init](https://storage.googleapis.com/s4a-shared-terraform-gcs-lab-materials/tf_init.png)

Terraform GCP provider definition block supports multiple arguments. Here for instance we specified `project` and `region`, which means all resources are going to be created in that project and in that specefic region unless it is specified differently in the resource bloc. i.e `project` and `region` in the resource bloc overrides those in the provider definition bloc.

## Provider credentials

Terraform Provider supports the credentials field (Optional) that should be set to the path of a service account key file, not user account credentials file.
```tf
provider "google" {
  project     = "<walkthrough-project-id/>"
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

 Run :
 ```bash
  gcloud auth application-default set-quota-project <walkthrough-project-id/>
 ```

 This command will add a quota project in application default credentials and saves the credentials file to a temp directory :

 ![SEPHORA_TERRAFORM](https://storage.googleapis.com/s4a-shared-terraform-gcs-lab-materials/cred_path.png)

 Run :
 ```bash
 export GOOGLE_APPLICATION_CREDENTIALS=<path-to-cred-file>
 ```

## Use default provider attributes

Let's try to create a GCS storage bucket using that provider.

Copy the following content to the file `main.tf`.

<walkthrough-editor-open-file filePath="cloudshell_open/terraform_labs/associate/lab_02/iac/main.tf">Open main.tf</walkthrough-editor-open-file>

```tf
resource "google_storage_bucket" "static" {
  provider      = google
  name          = "auto-expiring-bucket"
  location      = "EU"
  storage_class = "COLDLINE"
  force_destroy = true

  uniform_bucket_level_access = true
}
```
**Notice**: Bucket name should be unique accross the globe, we suggest you add your intials as suffix to the bucket name in `main.tf`.

__Example__ : Jhon Do -> Bucket name = "auto-expiring-bucket**-jdo"**

Run
```bash
terraform plan
```
**Notice** : The resource "google_storage_bucket" supports the attribute `project`which is The ID of the project in which the resource belongs. But since it is not provided, the provider project is used. You can see this in the output of the previous command.

Now, let's deploy the bucket :
```bash
terraform apply --auto-approve
```

Go to Google Cloud Console > Cloud Storage > Buckets and verify that the bucket is created in the project <walkthrough-project-id/>

## Override Provider attributes

Let's deploy another bucket to a different project and in a different location.

<walkthrough-project-setup></walkthrough-project-setup>

Add the following code to main.tf

<walkthrough-editor-open-file filePath="cloudshell_open/terraform_labs/associate/lab_02/iac/main.tf">Open main.tf</walkthrough-editor-open-file>


```tf
resource "google_storage_bucket" "static" {
  provider      = google
  project       = "<walkthrough-project-id/>"
  name          = "auto-expiring-bucket-2"
  Location      = "europe-west1-c"
  storage_class = "COLDLINE"
  force_destroy = true

  uniform_bucket_level_access = true
}
```

**Notice**: Bucket name should be unique accross the globe, we suggest you add your intials as suffix to the bucket name in `main.tf`.

Run
```bash
terraform plan  
```
```bash
terraform apply --auto-approve  
```
Verify that the bucket is recreated in "europe-west1-c" in the project <walkthrough-project-id/>.

## Cleanup

```bash
terraform destroy --auto-approve
```

## Conclusion
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

You're all set!

<walkthrough-conclusion-trophy></walkthrough-conclusion-trophy>
