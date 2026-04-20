# CSCE 412 Project 5 Documentation

## Website

- Website URL: `https://rahulgonsalves.me`
- GitHub Repository: `git@github.com:Rahul-Gonsalves/serverless-website-setup.git`

## Overview

Project 5 builds on top of the secure website completed in Project 4. In Project 4, the goal was to publish a website with a custom domain and HTTPS. In Project 5, the goal is to make that website dynamically updateable through source control and CI/CD. Instead of manually uploading changed files, the website should update after a commit and push to GitHub.

The final design uses GitHub as the source repository and AWS CodePipeline to automatically deploy website changes. The public website is still served securely through CloudFront with a certificate from AWS Certificate Manager, while Amazon S3 stores the website files.

## Services Used and Why

### Namecheap

Namecheap was used as the domain registrar for `rahulgonsalves.me`. Its role in the project is to provide ownership of the domain name so the website can be reached through a custom public address instead of an AWS-generated URL. This makes the site easier to access and more professional for demonstration.

### Amazon S3

Amazon S3 is used to store the static website files such as `index.html`, CSS, JavaScript, fonts, and images. S3 is a good fit because the website content is static and S3 is inexpensive, reliable, and easy to integrate with other AWS services. In the final Project 5 design, S3 acts as the website origin that holds the deployed files.

### Amazon Route 53

Route 53 is used for DNS management. Its purpose is to map the custom domain to the correct AWS resources. In this project, Route 53 allows `rahulgonsalves.me` or a subdomain to point to the CloudFront distribution so users can reach the website through the domain name instead of the raw CloudFront URL.

### Amazon CloudFront

CloudFront is used to securely deliver the website to users over HTTPS. It provides caching, fast content delivery, and acts as the public-facing service in front of S3. For Project 5, CloudFront is important because it should be the only public entry point to the site, while the S3 bucket remains inaccessible directly.

### AWS Certificate Manager

AWS Certificate Manager is used to create and manage the SSL/TLS certificate for the website. This allows the website to use HTTPS so traffic is encrypted and browsers recognize the site as secure. ACM is important because CloudFront requires a valid certificate for custom HTTPS domains.

### GitHub

GitHub is used as the code repository for the website. Its purpose is to store version history, track changes, and act as the source for automated deployments. GitHub makes it possible to update the live website by changing code locally and pushing the commit to the repository.

### AWS CodePipeline

AWS CodePipeline is used to automate the deployment workflow. Its role is to watch the GitHub repository for changes and trigger the deployment process after a push to the main branch. This removes the need to manually upload website files after every change.

### AWS CodeBuild

AWS CodeBuild is used by the pipeline to execute the deployment commands from `buildspec.yml`. In this project, CodeBuild syncs the updated website files to S3 and creates a CloudFront invalidation so the latest version of the website is served quickly.

## Repository Creation and Pipeline Connection

Project 5 begins by putting the website files from Project 4 into a GitHub repository. The repository created for this project is:

`git@github.com:Rahul-Gonsalves/serverless-website-setup.git`

The steps used to create and connect the repository were:

1. Start with the Project 4 website files in the local project folder.
2. Initialize a Git repository in that folder.
3. Add the website files and Project 5 deployment files.
4. Create the initial commit.
5. Rename the branch to `main`.
6. Add the GitHub repository as the `origin` remote.
7. Push the project to GitHub.

Example commands:

```bash
git init
git add .
git commit -m "Initial Project 5 website setup"
git branch -M main
git remote add origin git@github.com:Rahul-Gonsalves/serverless-website-setup.git
git push -u origin main
```

After the repository is in GitHub, the next task is to connect it to AWS CodePipeline.

The steps to connect the repository through AWS CodePipeline are:

1. Create a CodeBuild project.
2. Configure the CodeBuild project to use the repository content provided by CodePipeline.
3. Use the `buildspec.yml` file in the repository as the build specification.
4. Add environment variables for the deployment bucket and CloudFront distribution.
5. Give the CodeBuild service role permission to upload files to S3 and create CloudFront invalidations.
6. Create a CodePipeline pipeline.
7. Set GitHub as the source provider.
8. Select the repository and the `main` branch.
9. Set CodeBuild as the build/deployment stage.
10. Run the pipeline and verify that the first deployment completes successfully.

The file [buildspec.yml](/Users/rahulgonsalves/Documents/School/CSCE412/project4/buildspec.yml) contains the deployment commands used by CodeBuild. Those commands:

- sync the website files to the S3 bucket
- remove deleted files from the bucket
- invalidate CloudFront so the new version appears on the website

## How the Website Is Updated

Once GitHub and CodePipeline are connected, updating the website becomes much simpler than the manual upload process from Project 4. The update process is:

1. Edit the website locally.
2. Commit the change in Git.
3. Push the commit to GitHub.
4. GitHub triggers AWS CodePipeline.
5. CodePipeline starts CodeBuild.
6. CodeBuild deploys the new files to S3.
7. CodeBuild invalidates CloudFront.
8. The updated website becomes visible at the public domain.

This process demonstrates the main advantage of CI/CD: website updates are deployed automatically from source control.

Example update commands:

```bash
git add index.html
git commit -m "Update homepage demo version"
git push origin main
```

## Pre-Change and Post-Change Example

An example update for the website is changing the visible deployment version text on the homepage.

### Pre-change

Before the GitHub push, the homepage shows:

`Demo version: v1.0 - initial pipeline-ready site`

### Post-change

After editing the file, committing, and pushing to GitHub, the homepage shows:

`Demo version: v1.1 - updated through GitHub push`

This is a good demonstration because the change is easy to see on the live website and proves that the deployment happened from a commit and push only.

## Suggested Screenshots

The following screenshots can be included in the final submission:

1. Secure website open in the browser at `https://rahulgonsalves.me`
2. GitHub repository showing the website files
3. CodePipeline showing a successful pipeline run
4. CodeBuild showing a successful build
5. Homepage before the update
6. Homepage after the update

## Conclusion

Project 5 extends the secure website from Project 4 by adding version control and automated deployment. The website remains securely served through CloudFront and AWS Certificate Manager, while GitHub, CodePipeline, and CodeBuild make it possible to update the site automatically with a commit and push. This approach is more efficient and more realistic for modern software deployment than manually uploading files after every change.
