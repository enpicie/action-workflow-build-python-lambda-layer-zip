# action-workflow-upload-lambda-zip

Reusable Workflow for GitHub Actions to zip code for AWS Lambda and upload it to S3.

## Usage

**This workflow assumes the given S3 Bucket exists already.**

This repository provides a reusable GitHub Actions workflow for zipping code for an AWS Lambda and uploading the zipped artifact to a given S3 bucket.

### Example

In your repository, create a workflow file (e.g., `.github/workflows/deploy.yml`) with the following content:

```yaml
name: Deploy Lambda

on:
  workflow_dispatch:

jobs:
  uploadLambdaArtifact:
    uses: chzylee/action-workflow-upload-lambda-zip@v0.2.0
    with:
      source_directory: ./src # Path relative the repository root
      app_name: ${{ env.APP_NAME }}
      artifact_name: ${{ env.ARTIFACT_NAME }}-latest
      s3_bucket_name: ${{ env.ARTIFACT_S3_BUCKET }}
      aws_role_arn: ${{ secrets.S3_AWS_ROLE }}
```

### Inputs

| Name             | Description                                                           | Required | Example         |
| ---------------- | --------------------------------------------------------------------- | -------- | --------------- |
| source_directory | Path to Lambda source code dir                                        | Yes      | `./src`         |
| app_name         | Name of application (artifacts are organized by this in s3 bucket)    | Yes      | `my-app`        |
| artifact_name    | Base name of the zip file (the action will automatically append .zip) | Yes      | `my-app-1.0.0`  |
| s3_bucket_name   | Name of S3 bucket to upload .zip to                                   | Yes      | `dev-artifacts` |
| aws_role_arn     | ARN of IAM Role to assume for S3 access                               | Yes      | `secret`        |
| zip_path         | Path to output the zip (defaults to repo root)                        | No       | `./build`       |
| aws_region       | AWS region for S3 bucket (defaults us-east-2)                         | No       | `us-east-1`     |

---

### Recommendation

_Recommended for most cases_ to call this action twice: once to upload a "latest" version, and once more to upload a version archived for your specific app version. i.e. upload artifacts `my-app-latest.zip` and `my-app-1.0.0.zip`.

This makes it simpler for Terraform to access the latest code while still archiving versions that can be used for disaster recovery.
