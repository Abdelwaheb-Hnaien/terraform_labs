# [Associate] SEPHORA Terraform Course - Lab 01

![SEPHORA_TERRAFORM](https://lh3.googleusercontent.com/NC4SHCVQ51rrLfnLXlTgyuWdY8KTiGyXzx_1fp1ESDc7Vb_RZb8_iuxOPHfPnjGdZbw7Rw3SxR1RL20-p6MIGkmeCQ7ra4uaJKnJ3R6NLM-3QFv7dwwINVxpC0P_FBE-nJJQgUaobC1H4jDQ60P-ViA1mqGBXaRtObmS6o8lXqe2VPQ2F4I2XF0KFvizGVrqUYGgqwWkEtbHYQb-Mu7n48srDLjvhPr_0wiXzG8TSJfaWsGHvmFiyQlfS4W-Z2TRh9SIP6Am1YTGUYDhMdwNzbMsO-cJHjee2I1faLVC4cFeBUkryhGSa09dT4ESrJ1VcyKM1Wh6EiQ3Pjm32Ftg6oMJDVY1VKf08wcAjPVq98tAsuzUWNa_xeM8etjmtsaos_NthVRgIgfopzbi2Khd_3hm7pG9Gt2ol0v06DDyTQjhlN65mnOoBFg86-TDP9IxQg7VmpzffKlBHhnR5zOOeD1heZmvfXrobFKsFB0oj4pEdGRdSU8KBxmw7isG7_qFxfEoFHJTIGYLo97IypmMRonDUakow2LAGPGmo_J7it4S_lSNMbxoEND0DjvciY9rSpGEnq0Kqr6sErjSySH-O_Eryls9wOKKsLeduKD0VBUPG3HmprUAnOy1BsHdOfM9sNHkBWNLcK4xbEWaTc1TgKDhd6TacrHrJAh0nHO95Agko1a3BHEbBEbxVH5bMx2CUHUOhU4TtNr9TWqzC5iPv1_YpZW3spVj5yMpL8p8S0HMoLbIp37mfPsS3A0lvTmWCwYQQtPRHmrzIJ7IeG-dbub4qOgPRMWVSEtsSFSmPbS5lA8BwBZq0aRaKS-o9zoKIj3btbQwfJSridLi5YpgfVZxpRCE4uDrxPyj2dE=w3584-h1506-no?authuser=0)

After following the tutorial steps, you'll aquire the following knowledges:
- Run basic terraform commands
- know how to read and inspect Terraform code

Firstly, we'll authorize gcloud to access the Cloud Platform with Google user credentials

```bash
gcloud auth login
```

Answer Yes when prompted, click the link that should appear after that, chose your account then click allow.

Let's inspect what we have,
```bash
cd iac
cat main.tf
```

This is the code to deploy a Google Storage Bucket.

Replace **"your-project-id"** with an existing project id (Should be provided by socle GCP Team).
Bucket name should be unique accross the globe, so add a random suffix to the bucket name, example : "auto-expiring-bucket**-8653"**

Run :
```bash
Terraform init
```
this command will initialise terraform, i.e download locally Google Cloud SDK to be able to run api calls next.

Let's see what resources are going to be created by the code, to do this run:
```bash
Terraform Plan
```

It's time now to create the resources describe by our IaC. to do this run :
```bash
Terraform apply --auto-approve
```

Verify that the bucket is created successefully via the Google Cloud Console.

Notice that Terraform generates a **tfstate** file locally that contains traces of what has been created. inspect it.
```bash
cat terraform.tfstate
```
