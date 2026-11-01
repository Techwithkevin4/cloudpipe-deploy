# CloudPipe — Automated CI/CD Pipeline with GitHub Actions and AWS S3

![AWS](https://img.shields.io/badge/AWS-S3-orange)
![GitHub Actions](https://img.shields.io/badge/GitHub_Actions-CI%2FCD-blue)
![CloudFormation](https://img.shields.io/badge/IaC-CloudFormation-purple)
![OIDC](https://img.shields.io/badge/Security-OIDC-green)

## Project Overview

CloudPipe is a hands-on cloud engineering project demonstrating how to automate website deployments using **GitHub Actions, AWS S3, AWS IAM, and OpenID Connect (OIDC)**.

The goal was to replace a manual deployment process with a reliable, repeatable workflow that automatically publishes website changes whenever new code is pushed to the GitHub repository's `main` branch.

The completed pipeline successfully deploys a static website to Amazon S3 without requiring manually managed AWS access keys.

## The Problem

CloudPipe's original deployment process relied on manually transferring website files to production.

This approach introduced several challenges:

- Manual deployments consumed valuable development time.
- Missing files could break website functionality.
- Deployment steps were repetitive and susceptible to human error.
- Developers lacked a consistent, automated release process.

**Objective:** Build an automated deployment pipeline that reduces manual intervention and improves deployment consistency.

## Solution Architecture

The solution uses GitHub Actions to automate deployments and AWS S3 to host the website.

**Deployment workflow:**

1. A developer updates the website in VS Code.
2. Changes are committed and pushed to the `main` branch.
3. GitHub Actions automatically starts the deployment workflow.
4. GitHub Actions authenticates to AWS using OIDC.
5. AWS IAM authorizes access to the deployment role.
6. The workflow synchronizes website files with the S3 bucket.
7. The updated website becomes available through the S3 website endpoint.

### Architecture Diagram

```text
Developer / VS Code
        |
        | git push origin main
        v
GitHub Repository
        |
        v
GitHub Actions Workflow
        |
        | OIDC Authentication
        v
AWS IAM Role
        |
        | Authorized S3 Access
        v
Amazon S3 Bucket
        |
        v
Static Website Hosting
        |
        v
Updated CloudPipe Website
```

## Technologies Used

| Technology | Purpose |
|---|---|
| GitHub | Source code management |
| GitHub Actions | Automated deployment workflow |
| AWS S3 | Static website hosting |
| AWS IAM | Role-based access control |
| AWS OIDC | Secure authentication without long-lived AWS keys |
| AWS CloudFormation | Infrastructure as Code |
| AWS CLI | Synchronizing website files |
| HTML, CSS, JavaScript | Static website development |
| Git | Version control |
| VS Code | Development environment |

## Repository Structure

```text
cloudpipe-deploy/
│
├── .github/
│   └── workflows/
│       └── deploy.yml
│
├── infrastructure/
│   └── template.yaml
│
├── website/
│   ├── css/
│   │   └── style.css
│   ├── js/
│   │   └── script.js
│   └── index.html
│
└── README.md
```

## GitHub Actions Deployment Workflow

The deployment workflow is defined in `.github/workflows/deploy.yml`.

It is triggered automatically whenever code is pushed to `main` and can also be started manually using GitHub Actions.

The workflow performs the following operations:

- Checks out the repository.
- Authenticates to AWS using GitHub OIDC.
- Assumes the designated AWS IAM role.
- Verifies the authenticated AWS identity.
- Synchronizes website files to Amazon S3.
- Reports the deployment result.

### Deployment Command

```bash
aws s3 sync ./website/ \
  s3://cloudpipe-stack-websitebucket-ztlf3d3as63y/ \
  --delete
```

The `--delete` option removes files from the destination that no longer exist in the local website directory, keeping the deployed content synchronized with the repository.

## AWS Authentication and Security

The project uses **OpenID Connect (OIDC)** to authenticate GitHub Actions with AWS.

Rather than storing long-lived AWS access keys in GitHub, the workflow requests a temporary identity token and uses it to assume an AWS IAM role.

The IAM role is configured with a trust policy that restricts which GitHub repository and branch can assume the role.

The deployment permissions are scoped to the website's S3 bucket and include:

- `s3:ListBucket`
- `s3:GetObject`
- `s3:PutObject`
- `s3:DeleteObject`

This approach reduces the need to manage persistent AWS credentials and follows the principle of least privilege.

## Deployment Testing and Validation

The pipeline was tested by modifying the website's main heading in `website/index.html`.

The updated heading was:

**CloudPipe CI/CD Pipeline Successfully Automated!**

After committing and pushing the change, GitHub Actions automatically triggered a deployment.

### Verified Results

| Test | Result |
|---|---|
| Git push triggers workflow | Passed |
| Repository checkout | Passed |
| AWS OIDC authentication | Passed |
| IAM role assumption | Passed |
| S3 file synchronization | Passed |
| GitHub Actions deployment job | Passed |
| Updated website visible in browser | Passed |

### Deployment Performance

A successful deployment completed in approximately **13 seconds**, with the deployment job taking approximately **11 seconds**.

These results demonstrate that small website changes can be deployed quickly without manually uploading files.

## Troubleshooting and Lessons Learned

### Issue: AWS OIDC Role Assumption Failed

During the initial deployment attempts, GitHub Actions returned the following error:

```text
Error: Could not assume role with OIDC:
Not authorized to perform
sts:AssumeRoleWithWebIdentity
```

### Investigation

The GitHub Actions workflow was correctly configured to request an OIDC token, but AWS rejected the role assumption.

The GitHub OIDC token claims were inspected to identify the repository, branch, audience, and issuer information.

### Resolution

The AWS IAM trust policy was reviewed and corrected to match the GitHub OIDC token's subject and audience claims.

After correcting the trust relationship, the deployment workflow successfully authenticated with AWS.

### Key Lessons

- IAM trust policies must match the identity claims provided by GitHub.
- OIDC provides a secure alternative to persistent AWS access keys.
- GitHub Actions logs are valuable for identifying deployment failures.
- Deployment automation should be tested with actual website changes.
- Successful workflow execution should be followed by verification of the live application.

## Project Results

The completed project demonstrates:

- Automated deployments triggered by GitHub pushes.
- Secure AWS authentication using OIDC.
- Role-based access to S3 resources.
- Repeatable website deployments.
- Reduced manual deployment steps.
- Successful troubleshooting of AWS IAM authentication issues.
- Verification of production website updates.

## Future Improvements

Potential enhancements include:

- Adding automated HTML, CSS, and JavaScript validation.
- Running automated tests before deployment.
- Adding HTTPS through Amazon CloudFront.
- Implementing deployment notifications.
- Adding branch protection and pull request reviews.
- Improving rollback capabilities and deployment monitoring.

## Skills Demonstrated

**Cloud Engineering:** AWS S3, AWS IAM, AWS CloudFormation, AWS CLI

**DevOps:** GitHub Actions, CI/CD pipelines, Git, deployment automation

**Cloud Security:** OIDC federation, IAM trust policies, least-privilege access

**Troubleshooting:** GitHub Actions logs, AWS authentication errors, deployment validation

## Repository

[CloudPipe CI/CD Project — GitHub](https://github.com/Techwithkevin4/cloudpipe-deploy)

## Conclusion

This project demonstrates how GitHub Actions and AWS services can be integrated to build a secure, automated deployment pipeline.

By replacing manual website uploads with an event-driven deployment workflow, CloudPipe establishes a more consistent and efficient deployment process.

The project also provided practical experience with cloud security, Infrastructure as Code, CI/CD automation, and troubleshooting AWS IAM authentication issues.
