# CSCE 412 Project 5

This repository contains the static website for Project 5. The intended deployment model is:

- GitHub as the source repository
- AWS CodePipeline as the CI/CD trigger
- AWS CodeBuild to run the deployment commands
- Private S3 bucket as the website origin
- CloudFront with Origin Access Control
- Route 53 and ACM for the custom domain

## Local files added for Project 5

- `buildspec.yml`: deployment instructions for CodeBuild
- `PROJECT5_SUBMISSION.md`: report template and step-by-step setup guide
- `aws/bucket-policy-template.json`: sample bucket policy for CloudFront OAC access
- `aws/codebuild-policy-template.json`: sample IAM policy for the CodeBuild service role
- `index.html`: updated with Project 5 wording and an easy-to-change demo version string

## Demo update idea

For the required before/after demonstration, change the line below in `index.html`, commit, and push:

`Demo version: v1.0 - initial pipeline-ready site`

Example follow-up change:

`Demo version: v1.1 - updated through GitHub push`
