# GitHub Actions + AWS CodeBuild Setup

This repository is configured to use AWS CodeBuild as a runner for GitHub Actions.

## Files

- `.github/workflows/codebuild.yml` - GitHub Actions workflow that triggers CodeBuild
- `buildspec.yml` - CodeBuild build specification

## Setup Instructions

1. **Copy files to your repository:**
   ```bash
   cp -r .github buildspec.yml /path/to/your/trial/repo/
   cd /path/to/your/trial/repo/
   git add .github/ buildspec.yml
   git commit -m "Add CodeBuild integration"
   git push
   ```

2. **Update CodeBuild project to use GitHub source:**
   ```bash
   aws codebuild update-project \
     --name github-action-runner \
     --source type=GITHUB,location=https://github.com/prasadk01985303-hash/trial.git \
     --region us-east-1
   ```

3. **Connect GitHub to CodeBuild (one-time):**
   - Go to AWS Console → CodeBuild → Source providers
   - Connect your GitHub account
   - Or use personal access token

4. **Test the workflow:**
   - Push to main branch or create a PR
   - Check Actions tab in GitHub
   - View logs in AWS CodeBuild console

## Environment Variables

The workflow passes these to CodeBuild:
- `GITHUB_SHA` - Commit SHA
- `GITHUB_REF` - Branch/tag reference
- `GITHUB_REPOSITORY` - Repository name

## Customization

Edit `buildspec.yml` to add your build commands:
- Install dependencies
- Run tests
- Build Docker images
- Deploy to EKS/ECS
- Push to ECR

## Adding Secrets

Store secrets in AWS Systems Manager Parameter Store:
```bash
aws ssm put-parameter \
  --name /codebuild/my-secret \
  --value "secret-value" \
  --type SecureString \
  --region us-east-1
```

Then reference in buildspec.yml:
```yaml
env:
  parameter-store:
    MY_SECRET: /codebuild/my-secret
```
