# action-workflow-build-python-lambda-layer-zip

Reusable Workflow for GitHub Actions to install Python dependencies, zip them for a Lambda Layer, and upload the zip to S3.

## Usage

**This workflow assumes the given S3 Bucket exists already.**

**This action does NOT create a Lambda Layer.** It only prepares the .zip archive to use for one.

This repository provides a reusable GitHub Actions workflow for installing Python dependencies for a Lambda and uploading it to S3 for use with a Lambda Layer.

.zip archives are named with the convention <app_name>-<python_runtime>.zip where `<python_runtime>` has any "." removed. i.e. `my_app` with Python 3.11 will have `my_app-311.zip`. Since these layers are built for specific apps, they will be stored in applayers/<zip_name>.

### Example

In your repository, create a workflow file (e.g., `.github/workflows/deploy.yml`) with the following content:

```yaml
name: Deploy Lambda

on:
  workflow_dispatch:

jobs:
  uploadLambdaLayerArtifact:
    uses: enpicie/action-workflow-build-python-lambda-layer-zip@v0.1.0
    with:
      requirements_path: ./src/requirements.txt # Path relative the repository root
      app_name: ${{ env.APP_NAME }}
      python_runtime: ${{ env.PYTHON_RUNTIME }}
      s3_bucket_name: ${{ env.ARTIFACT_S3_BUCKET }}
      aws_role_arn: ${{ secrets.S3_AWS_ROLE }}
```

### Inputs

| Name              | Description                                                     | Required | Example                  |
| ----------------- | --------------------------------------------------------------- | -------- | ------------------------ |
| requirements_path | Path to requirements.txt file                                   | Yes      | `./src/requirements.txt` |
| app_name          | Name of application (used in .zip name)                         | Yes      | `my-app`                 |
| python_runtime    | Python runtime version Layers are built for (used in .zip name) | Yes      | `3.11`                   |
| s3_bucket_name    | Name of S3 bucket to upload .zip to                             | Yes      | `dev-artifacts`          |
| aws_role_arn      | ARN of IAM Role to assume for S3 access                         | Yes      | `secret`                 |
| aws_region        | AWS region for S3 bucket (defaults us-east-2)                   | No       | `us-east-1`              |

---

## Ignored Libraries

This action is intended to only install pure-Python libraries that are not architecture-dependent so we can avoid architecture conflicts with GitHub Actions runners.

If you depend on an architecture-specific library, create a separate app-independent Lambda layer for it.

`pynacl` is one such library ignored when reading requirements.txt due to its architecture requirement.
