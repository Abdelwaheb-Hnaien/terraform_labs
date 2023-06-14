# [Advanced] SEPHORA Terraform Course - Lab 02 : Meta arguments
## Introduction
![SEPHORA_TERRAFORM](https://storage.googleapis.com/s4a-shared-terraform-gcs-lab-materials/sephora_terraform_bw.png)

In this lab you will learn about the following Terraform **meta-arguments**:
- count
- depends_on
- foreach
We will also take a look on how to chain resources when using foreach.

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

## How to use count meta-argument:
The count meta-argument accepts a number and creates the number of instances of the resource specified.
When each instance is created, it has its own distinct infrastructure object associated with it, so each can be managed separately. When the configuration is applied, each object can be created, destroyed, or updated as appropriate.

Let's see how it works:

<walkthrough-editor-open-file
    filePath="cloudshell_open/terraform_labs/advanced/lab_01/iac/provider.tf">
    Edit provider.tf
</walkthrough-editor-open-file>
```tf
provider "google" {
  project     = "<walkthrough-project-id/>"
  region      = "europe-west1"
}
```

<walkthrough-editor-open-file
    filePath="cloudshell_open/terraform_labs/advanced/lab_01/iac/main.tf">
    Edit main.tf
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
  storage_class = "STANDARD"

  uniform_bucket_level_access = true

}
```

**Tips** : dataset_id and bucket name should be unique in the project, to avoid potential conflict with other students we recommend you to add your intials as suffix.
.

_*Example*_ : John Do -> dataset_id = "tf_ad_lab_bqd_jdo"

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
![tf_apply](https://storage.googleapis.com/s4a-shared-terraform-gcs-lab-materials/advanced/lab_01/tf_plan.png)

Let's deploy the resource :
```bash
terraform apply --auto-approve
```

Verify that the bucket and the dataset are created successfully.

[Bigquery Datasets list page](https://console.cloud.google.com/bigquery?referrer=search&orgonly=true&project=<walkthrough-project-id/>)

[Buckets list page](https://console.cloud.google.com/storage/browser?hl=fr&project=<walkthrough-project-id/>)


## Editing Bigquery Dataset resource

Suppose that you have accidentely changed the name of the bigquery dataset. Terraform will delete the current dataset and then create the new one.

Let's do this, try to add a random string to the name of the bucket.

<walkthrough-editor-open-file
    filePath="cloudshell_open/terraform_labs/advanced/lab_01/iac/main.tf">
    Edit main.tf
</walkthrough-editor-open-file>

Run:

```bash
terraform plan
```

You should get the following error :
![tf_apply](https://storage.googleapis.com/s4a-shared-terraform-gcs-lab-materials/advanced/lab_01/plan_error.png)

**Notice** : The meta-argument `prevent_destroy` set to true will cause Terraform to reject with an error any plan that would destroy the infrastructure object associated with the resource, as long as the argument remains present in the configuration.

This can be used as a measure of safety against the accidental replacement of objects that may be costly to reproduce, such as database instances. However, it will make certain configuration changes impossible to apply, and will prevent the use of the terraform destroy command once such objects are created, and so this option should be used sparingly.

Now let's update the lifecycle;

```
lifecycle {
    create_before_destroy = true
  }
```

Run:

```bash
terraform plan
```

You should see the following output :
`Plan: 1 to add, 0 to change, 1 to destroy.`

**Notice** : By default, when Terraform must change a resource argument that cannot be updated in-place due to remote API limitations, Terraform will instead destroy the existing object and then create a new replacement object with the new configured arguments.

The `create_before_destroy` meta-argument changes this behavior so that the new replacement object is created first, and the prior object is destroyed after the replacement is created.

Run:
```
terraform apply --auto-approve
```

## Change the Storage Bucket configuration

Let's manually change the storage class of the GCS bucket you have previously created with Terraform.

[Go to bucket list page](https://console.cloud.google.com/storage/browser?hl=fr&project=<walkthrough-project-id/>).

Locate your bucket > CONFIGURATION > Default storage class > Edit

Set the storage class to `Nearline`.

Run:

```bash
terraform plan
```

**Notice**: Terraform will tell you that it will revert the changes you have done manually :
![tf_apply](https://storage.googleapis.com/s4a-shared-terraform-gcs-lab-materials/advanced/lab_01/plan_revert.png)

Let's edit the gcs bucket resource and add the following lifecycle :
```
  lifecycle {
    ignore_changes = [storage_class]
  }
```

Run:
```
terraform plan
```
Terraform will say that your infrastructure matches the configuration and there is nothing to change.

**Notice**:  By default, Terraform detects any difference in the current settings of a real infrastructure object and plans to update the remote object to match configuration.

The `ignore_changes` feature is intended to be used when a resource is created with references to data that may change in the future, but should not affect said resource after its creation. In some rare cases, settings of a remote object are modified by processes outside of Terraform, which Terraform would then attempt to "fix" on the next run. In order to make Terraform share management responsibilities of a single object with a separate process, the `ignore_changes` meta-argument specifies resource attributes that Terraform should ignore when planning updates to the associated remote object.

## Redeploy resource depending on other ressource attribute change

In this section we will deploy a Google Cloud Function using terraform.
Edit main.tf and add the following resources.
<walkthrough-editor-open-file
    filePath="cloudshell_open/terraform_labs/advanced/lab_01/iac/main.tf">
    Edit main.tf
</walkthrough-editor-open-file>
```tf
/*
* Google Cloud Function
*/

data "archive_file" "gcf_src" {
  type        = "zip"
  source_dir  = "../src"
  output_path = "../src/src.zip"
}


resource "google_storage_bucket_object" "archive" {
  name   = "src.zip"
  bucket = google_storage_bucket.static-site.name
  source = data.archive_file.gcf_src.output_path
}

resource "google_cloudfunctions_function" "function" {
  name        = "function-test"
  description = "My function"
  runtime     = "python39"

  available_memory_mb   = 128
  source_archive_bucket =google_storage_bucket.static-site.name
  source_archive_object = google_storage_bucket_object.archive.name
  trigger_http          = true
  entry_point           = "say_hello"
}
```
```bash
terraform init
```

```bash
terraform plan
```

The plan should tell you : 2 to add, 0 to change, 0 to destroy.

```bash
terraform apply --auto-approve
```

### Change the source code of the cloud function

Let's edit function source code and make some changes.

<walkthrough-editor-open-file
    filePath="cloudshell_open/terraform_labs/advanced/lab_01/src/main.py">
    Edit main.py
</walkthrough-editor-open-file>
```python
def say_hello(request):
    print("Hello Sephora") # some changes goes here
```

```bash
terraform plan
```

The plan sould says: **No changes. Your infrastructure matches the configuration.**

Terraform says that your infrastructure matches the configuration yet we changed the source code of the application. This happens because the zipped source file is still name "src.zip", neither the name of the output file nor the location has been changed, it is the reason why Terraform can't catch the change. However some hidden attributes related to the resource are going to be changes, for instance the _*md5hash*_ of the archive file, we can make use of that to explicitely tell Terraform to trigger function replacement whenever this attribute changes. We can do this by adding the following block to the cloud function resource definiton.
 <walkthrough-editor-open-file
    filePath="cloudshell_open/terraform_labs/advanced/lab_01/iac/main.tf">
    Edit main.tf
</walkthrough-editor-open-file>

```tf
  lifecycle {
    replace_triggered_by = [
      google_storage_bucket_object.archive.md5hash
    ]
  }
```
```bash
terraform plan
```

```bash
terraform apply
```

The plan should says : **2 to add, 0 to change, 2 to destroy.**

The following resources are going to be replaced:
- google_storage_bucket_object.archive
- google_cloudfunctions_function.function

More information about the exported attributes related to the resource storage_bucket_object : https://registry.terraform.io/providers/hashicorp/google/latest/docs/resources/storage_bucket_object#attributes-reference

## Clean up

You finished the lab, let's clean resources:

First you need to update the lifecycle attribute **prevent_destroy** of the bigquery dataset and set it to false.

Apply changes:
```bash
terraform apply --auto-approve
```

and then

```bash
terraform destroy --auto-approve
```

## End of the lab

You're all set!

<walkthrough-conclusion-trophy></walkthrough-conclusion-trophy>
