
## Mirroring GitHub to Bitbucket

### Overview

These are the steps to configure a GitHub Action that mirrors a GitHub repository to a Bitbucket repository. The workflow will automatically sync changes from GitHub to Bitbucket whenever a commit is made to the specified branch.

### Steps to Set Up the Mirroring Process

#### 1. **Generate a Bitbucket Repository Access Token**

1. **Log in to Bitbucket:**
   - Navigate to [Bitbucket](https://bitbucket.org) and log in to your account.

2. **Create a Repository Access Token:**
   - Click on your avatar in the bottom-left corner of the page.
   - Select **Personal settings** from the menu.
   - Go to **App passwords** under the **Access management** section.
   - Click **Create app password**.
   - Enter a label for the app password (e.g., "GitHub Actions Token").
   - Set the required permissions:
     - **Repositories**: Read (for cloning) and Write (for pushing).
   - Click **Create**.
   - Copy the token immediately as it will not be shown again.

3. **Record the Git Author Email:**
   - When creating the app password, Bitbucket may provide a Git author email address that must be configured in the repository settings for authentication.
   - This email address typically has the format `your-email@bots.bitbucket.org`. Ensure you note this email as it will be used in the GitHub Actions workflow.

#### 2. **Configure GitHub Repository**

1. **Access GitHub Secrets:**
   - Go to your GitHub repository.
   - Navigate to `Settings` > `Secrets and variables` > `Actions`.

2. **Add Bitbucket Token to GitHub Secrets:**
   - Click **New repository secret**.
   - Name the secret `BITBUCKET_TOKEN`.
   - Paste the Bitbucket access token you copied earlier as the value.
   - Click **Add secret**.

3. **Ensure GitHub Token is Available:**
   - GitHub Actions automatically provide a `GITHUB_TOKEN` for repository operations. Ensure this token has appropriate permissions.

#### 3. **Create GitHub Actions Workflow**

1. **Add Workflow YAML File:**
   - In your GitHub repository, create a directory `.github/workflows` if it doesn’t already exist.
   - Create a file named `mirror.yml` in this directory.

2. **Add the Following Configuration to `mirror.yml`:**

   ```yaml
   name: Mirror GitHub to Bitbucket

   on:
     push:
       branches:
         - main  # Monitors changes to the 'main' branch
     workflow_dispatch:  # Allows manual triggering of the workflow

   jobs:
     mirror:
       runs-on: ubuntu-latest

       steps:
       - name: Checkout GitHub Repository
         uses: actions/checkout@v3

       - name: Set up Git
         run: |
           git config --global user.name "github-actions"
           git config --global user.email "github-actions@github.com"

       - name: Clone GitHub Repository
         run: |
           git clone --mirror https://github.com/USERNAME/REPOSITORY.git github-repo

       - name: Set Git Author Email for Bitbucket
         run: |
           cd github-repo
           git config user.email "your-email@bots.bitbucket.org"

       - name: Push to Bitbucket Repository
         env:
           BITBUCKET_TOKEN: ${{ secrets.BITBUCKET_TOKEN }}
         run: |
           cd github-repo
           git remote add bitbucket https://USERNAME:${{ secrets.BITBUCKET_TOKEN }}@bitbucket.org/USERNAME/REPOSITORY.git
           git push --mirror bitbucket
   ```

   **Replace Placeholder Values:**
   - Replace `USERNAME/REPOSITORY` with your actual GitHub and Bitbucket repository names.
   - Replace `your-email@bots.bitbucket.org` with the Git author email address provided by Bitbucket when you created the repository access token.

#### 4. **Trigger the Workflow**

1. **Commit Changes:**
   - Commit and push the `.github/workflows/mirror.yml` file to the `main` branch of your GitHub repository.

2. **Test the Workflow:**
   - The workflow will automatically run on each commit to the `main` branch.
   - You can also manually trigger the workflow from the GitHub Actions tab in your repository.

### Summary

The GitHub Action will now automatically mirror your GitHub repository to Bitbucket. This setup ensures that changes made to the GitHub repository are reflected in the Bitbucket repository, keeping both repositories in sync.

### Troubleshooting

- **Invalid Credentials**: Double-check that the Bitbucket token has the correct permissions and is correctly set in GitHub Secrets.
- **Authentication Errors**: Ensure that the Bitbucket token and the Git author email are correctly referenced and have the appropriate permissions.

This is part of the Bitbucket Github project done with Solavise Technologies Institute from [Mirror-and-Synchronizing](https://github.com/asaphdanchi/Mirror-and-synchronizing) by @AsaphDanchi

This documentation was AI-Assisted, human-verified and updated by @kevshakes

---
