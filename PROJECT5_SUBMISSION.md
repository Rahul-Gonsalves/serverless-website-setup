# CSCE 412 Project 5 Submission Notes

## 1. Project overview

Project 5 extends the secure website from Project 4 by placing the site code in GitHub and connecting it to AWS CodePipeline so the website updates automatically after a commit and push.

Architecture used:

- GitHub repository for source control
- AWS CodePipeline for CI/CD orchestration
- AWS CodeBuild for deployment commands
- Private Amazon S3 bucket for website files
- Amazon CloudFront for public delivery
- AWS Certificate Manager for HTTPS
- Amazon Route 53 for the domain and subdomain

Security goal:

Only the domain and subdomain should be accessible by users. The S3 bucket itself should not be public.

## 2. Website link

Replace this section with your real deployed URL.

- Website URL: `https://your-domain.example`

## 3. Beginning-to-end setup steps

### A. Start from Project 4

1. Copy the Project 4 website files into a new Project 5 working folder.
2. Make sure `index.html`, `css`, `js`, `img`, `fonts`, and `font-awesome` are all included.
3. Update the homepage so it clearly reflects Project 5 and includes a visible text string you can change later for the demo.

### B. Create the GitHub repository

1. Create a new GitHub repository.
2. Initialize Git in the project folder:

```bash
git init
git add .
git commit -m "Initial Project 5 website"
git branch -M main
git remote add origin https://github.com/<your-user>/<your-repo>.git
git push -u origin main
```

3. Verify the website files appear in GitHub.

Screenshot to capture:

- GitHub repository showing the initial commit and website files

### C. Create the private S3 bucket

1. Create an S3 bucket for the website content.
2. Keep **Block all public access** enabled.
3. Do not use the public S3 website endpoint.
4. Upload a temporary copy of the site or let CodePipeline handle deployment later.

Screenshot to capture:

- S3 bucket permissions showing public access blocked

### D. Create the CloudFront distribution

1. Create a CloudFront distribution.
2. Set the S3 bucket as the origin.
3. Create and attach an **Origin Access Control (OAC)**.
4. Update the bucket policy so only CloudFront can read the bucket.
   A starter policy is included in `aws/bucket-policy-template.json`.
5. Set `index.html` as the default root object.
6. Redirect HTTP to HTTPS.
7. Add your custom domain or subdomain.
8. Attach an ACM certificate for HTTPS.

Screenshot to capture:

- CloudFront distribution settings showing the custom domain

### E. Configure Route 53

1. Open the hosted zone for your domain.
2. Create an alias record for the subdomain pointing to CloudFront.
3. Wait for DNS propagation.

Screenshot to capture:

- Route 53 record pointing to CloudFront

### F. Create the CodeBuild project

1. Create a CodeBuild project.
2. Use the repository connected through CodePipeline as the source.
3. Choose a managed image that includes the AWS CLI.
4. Use the `buildspec.yml` file from this repository.
5. Add these environment variables:

- `DEPLOY_BUCKET` = your private website bucket name
- `DISTRIBUTION_ID` = your CloudFront distribution ID

6. Grant the CodeBuild service role permission to:

- read the pipeline source artifact
- write objects to the S3 bucket
- create CloudFront invalidations

Suggested actions in the IAM policy:

- `s3:PutObject`
- `s3:DeleteObject`
- `s3:ListBucket`
- `cloudfront:CreateInvalidation`

A starter policy is included in `aws/codebuild-policy-template.json`.

Screenshot to capture:

- CodeBuild environment variables and buildspec source setting

### G. Create the CodePipeline pipeline

1. Create a new CodePipeline pipeline.
2. Choose GitHub as the source provider.
3. Connect the repository and select the `main` branch.
4. For the build stage, choose the CodeBuild project you created.
5. Start the pipeline and confirm the first run succeeds.

Screenshot to capture:

- Successful CodePipeline execution

## 4. Process to update the website

After the pipeline is set up, updating the website should only require a code change, commit, and push.

Example update process:

1. Edit `index.html`.
2. Change the visible version text from:

`Demo version: v1.0 - initial pipeline-ready site`

to:

`Demo version: v1.1 - updated through GitHub push`

3. Commit and push:

```bash
git add index.html
git commit -m "Update homepage demo version"
git push origin main
```

4. GitHub triggers CodePipeline.
5. CodeBuild syncs the site to S3 and invalidates CloudFront.
6. Refresh the public website and confirm the new text appears.

## 5. Pre-change and post-change example

### Before push

- Homepage shows: `Demo version: v1.0 - initial pipeline-ready site`

### After push

- Homepage shows: `Demo version: v1.1 - updated through GitHub push`

Add screenshots for both states here.

## 6. Demonstration notes

For the required video or live demo:

1. Show the current website in the browser.
2. Show the changed line in `index.html`.
3. Commit and push to GitHub.
4. Show CodePipeline starting automatically.
5. Show the successful build.
6. Refresh the website and show the change live.

## 7. Security checks

To satisfy the requirement that only the domain and subdomain are accessible:

- Keep the S3 bucket private
- Use CloudFront OAC instead of a public bucket
- Do not share the S3 website endpoint
- Serve traffic only through the Route 53 record and CloudFront distribution
- Use HTTPS with ACM

## 8. What still needs to be filled in

Replace these placeholders before submitting:

- your GitHub repository URL
- your deployed website URL
- screenshots from GitHub, S3, CloudFront, Route 53, CodeBuild, and CodePipeline
- before/after screenshots of the website update
