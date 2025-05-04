
# AWS ECR Docker Builder Action

This GitHub Action automates the process of building and pushing Docker images to Amazon Elastic Container Registry (ECR). It simplifies the integration of Docker builds into your CI/CD pipeline, handling AWS credentials, image tagging, and pushing to the specified ECR repository.

## Features

- **AWS ECR Integration**: Seamlessly build and push Docker images to Amazon ECR.
- **Flexible Configuration**: Allows custom Dockerfile paths, image tags, and build contexts.
- **Support for Branch-Specific Image Tags**: Automatically tags images with the branch name, or use a custom tag.

## Prerequisites

Before using this action, ensure that:

- You have an AWS account with permission to access ECR.
- You have a pre-existing ECR repository where the Docker images will be pushed.
- You have configured your AWS credentials as GitHub Secrets.

## GitHub Secrets Setup

To securely store your AWS credentials in GitHub, navigate to your repository's **Settings** > **Secrets** > **New repository secret**, and add the following secrets:

- `AWS_ACCESS_KEY_ID`: Your AWS Access Key ID.
- `AWS_SECRET_ACCESS_KEY`: Your AWS Secret Access Key.

## Usage

### Basic Workflow

Here’s an example of how to use this action in your GitHub Actions workflow:

```yaml
name: Build and Push Image to ECR

on:
  push:
    branches:
      - master
  workflow_dispatch:

env:
  ECR_REGISTRY: you-id.dkr.ecr.your-region.amazonaws.com
  ECR_REPOSITORY: demo-django-backend
  AWS_REGION: your-region

jobs:
  build:
    name: Build and Push
    runs-on: self-hosted
    outputs:
      branch: ${{ steps.extract_branch.outputs.branch }}
    steps:
      - name: Extract Branch Name
        shell: bash
        run: |
          echo "branch=${GITHUB_HEAD_REF:-${GITHUB_REF#refs/heads/}}" >> $GITHUB_OUTPUT
        id: extract_branch

      - name: Build and Push Image to ECR
        uses: anilrajrimal1/aws-ecr-docker-builder-action@v1.0.0
        with:
          AWS_ACCESS_KEY_ID: ${{ secrets.AWS_ACCESS_KEY_ID }}
          AWS_SECRET_ACCESS_KEY: ${{ secrets.AWS_SECRET_ACCESS_KEY }}
          AWS_REGION: ${{ env.AWS_REGION }}
          ECR_REGISTRY: ${{ env.ECR_REGISTRY }}
          ECR_REPOSITORY: ${{ env.ECR_REPOSITORY }}
          BRANCH_NAME: ${{ steps.extract_branch.outputs.branch }}
```

### Inputs

This action accepts the following inputs:

| **Input**           | **Description**                                                                 |
|---------------------|---------------------------------------------------------------------------------|
| `AWS_ACCESS_KEY_ID` | AWS Access Key ID for configuring AWS credentials.                             |
| `AWS_SECRET_ACCESS_KEY` | AWS Secret Key for configuring AWS credentials.                               |
| `AWS_REGION`        | AWS region where your ECR is located (e.g., `us-east-1`).                      |
| `ECR_REGISTRY`      | The URL of your Amazon ECR registry (e.g., `123456789012.dkr.ecr.us-east-1.amazonaws.com`). |
| `ECR_REPOSITORY`    | The name of your ECR repository (e.g., `my-app-repo`).                         |
| `BRANCH_NAME`       | The branch name for tagging the image (default is set automatically).          |
| `DOCKERFILE_PATH`   | Path to your Dockerfile (default: `./Dockerfile`).                             |
| `BUILD_CONTEXT`     | Path to your build context (default: `.`).                                     |
| `IMAGE_TAG`         | Custom tag for the image (optional). Default will be the branch name.          |

### Workflow Explanation

- **Extract Branch Name**: The branch name is extracted using a shell script and passed to the action as an input.
- **Build and Push Image to ECR**: The Docker image is built using the `docker build` command and then pushed to the specified Amazon ECR registry with the appropriate tag.

## Contributing

We welcome contributions! Feel free to fork the repository and submit pull requests.

## License

This project is licensed under the MIT License - see the [LICENSE](LICENSE) file for details.
