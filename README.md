# PROJECT 1: MIGRATION OF SOURCE CODE FROM BITBUCKET TO GITHUB

## Mirror-and-synchronizing (BITBUCKET TO GITHUB MIGRTION AND SYNCRONIZATION)


## OBJECTIVE:
Migrating your existing bitbucket repository to GitHub repository, synchronizing them in shuch a way that when ever a change is made in the source repository(Bitbucket) the same change will be replecated in GitHub without any manuel intervention.

# Prerequisites
Before starting, ensure you have:

- A Bitbucket repository with admin access
- A GitHub account with repository creation permissions
- SSH and Git installed on your local machine
- Basic knowledge of CI/CD pipelines

 # Project Architecture:
 
The migration process involves the following components:
- Source Platform (Bitbucket):- Contains the repository to be migrated.
- Includes repository history, branches, and pull requests.

- Destination Platform (GitHub):- The target platform where repository will be hosted.
- Supports Git-based version control and collaboration.

- Migration Tools:- Git: Used to clone and push repositories.
- GitHub CLI: Facilitates repository creation and management on GitHub.

- Workflow:- Clone repositories from Bitbucket.
- Push repositories to GitHub.
- Verify repository integrity and permissions.


 <img width="531" alt="migration-datat center" src="https://github.com/user-attachments/assets/c691c056-8adb-43fc-94e5-14abaad501bd" />

# Step-by-Step Guide
## On GitHub, create a new empty repository
- Go to GitHub
- Click on New Repository
- Select README file
- Copy the repository URL

 ## On your Bitbucket
 
- Navigate to your Bitbucket repository Example "Mirroring-Repo" Create an access token under `Repository settings > Security > Access tokens`
 - Create Repository Access Token with selecting all the "READ" Permission and check the Read and write checkbox under Webhooks. Copy the first Token and save it in your notepad because you can view it just once.
  - Copy the first Token and save it in your notepad because you can view it just once.
    
<img width="890" alt="Image" src="https://github.com/user-attachments/assets/397c5e97-6230-44b5-9e3f-d8decd1eef03" />

- Navigate To Github and Import the Repository while keeping the same name "Mirroring-Repo"
     - To navigate to GitHub and import a repository while keeping the same name "Mirroring-Repo", follow these steps:
       
       Step 1: Log in to GitHub
     - Open GitHub in your browser.
     - Sign in with your GitHub credentials.

       **Import a Repository**
     - Click on your profile icon in the top-right corner.
     - Select "Your repositories" from the dropdown.
      - Click the green "New" button and select "Import a repository" (or go directly to GitHub Importer).


       <img width="640" alt="importing repo" src="https://github.com/user-attachments/assets/d034d6aa-ba05-4dfe-8d3f-8091cd1b4885" />


     - Enter the URL of the repository you want to import (e.g., from GitHub, GitLab, or Bitbucket).
     - In the "Repository Name" field, type "Mirroring-Repo" to maintain the same name.
     - Choose your preferred visibility (Public or Private).
     - Click "Begin Import" and wait for the process to complete.
 
  - On Bitbucket, Enable Pipelines under Repository settings > Pipelines > Settings
    <img width="920" alt="enable pipeline" src="https://github.com/user-attachments/assets/702872be-05f7-499d-855a-79fc47a8ba29" />

  - On Bitbucket, Generate keys under Repository settings > Pipelines > SSH keys. Copy the public key to clipboard
    <img width="918" alt="copy public key" src="https://github.com/user-attachments/assets/216bf37f-8df1-40d6-87df-00ba6ea417b6" />

  - On the same page, under Known hosts enter github.com as the Host address and then click Fetch followed by Add host
    <img width="914" alt="github com" src="https://github.com/user-attachments/assets/7acf3c1b-2e88-4837-9952-4db482454f29" />

  - On GitHub, add the public key under Settings > Security > Deploy keys > Add deploy key. Tick the checkbox to Allow write access
   <img width="758" alt="image" src="https://github.com/user-attachments/assets/dca749e2-21b0-4c19-a48a-c2c7d93f3266" />
    
  - On Bitbucket, add the public key under Repository settings > Security > Access keys > Add key; add the key as an environmental variable using the path
    
    <img width="767" alt="add access key" src="https://github.com/user-attachments/assets/4dc06e4b-4fbf-4261-a508-0ffe6d010c8f" />


  - In Bitbucket, create a repository variable by navigating to Repository Settings > Pipeline > Repository Variables. Name the variable, for example, 'BITBUCKET_VARIABLE'.This will be referenced in your bitbucket-pipelines.yml file.
  - Paste the Access Token you generated in the step above 
    
    <img width="931" alt="Repository variable" src="https://github.com/user-attachments/assets/a8c04cce-4093-45c7-832c-b90e8e91e111" />
    
  - On Github, At the top right click on your Profile, Scroll down at the bottom click on settings
    
    <img width="919" alt="settings" src="https://github.com/user-attachments/assets/448e7095-3818-40f1-8f60-4ddbde50aeaf" />
  
  - At the bottom left of the page click Developer Settings, Create a Personal access tokens Under Personal access tokens > Token (classic) > Generate new token > Generate new token (classic)
    
    <img width="931" alt="generate a new token" src="https://github.com/user-attachments/assets/68bcfc49-7a9f-44f7-a138-130c8e678cac" />
    
  - Check the "repo" box, "Workflow" box and the "write:package" box. Genarate Token and copy the token Example "ghp_zY5GxaeytuAZlR4tPcwqovPz7c2ZVy1kdfg"
   
    <img width="929" alt="githubaccess-taken" src="https://github.com/user-attachments/assets/55e25caf-5962-4a3a-b896-79561f6341cd" />

  
  - On Bitbucket, Create a Repository variables with the Access Token from Github under Repository Settings > Pipeline > Repository variable. Naming the variable Example "GITHUB_VARIABLE"

# In Bitbucket repo, edit the bitbucket-pipelines.yml file containing the following:

```
  pipelines:
    default:
      - step:
          name: Sync GitHub Mirror
          image: alpine/git:latest
          clone:
            enabled: false
          script:
            - git clone --mirror https://x-token-auth:"$BITBUCKET_VARIABLE"@bitbucket.org/demo-migration12/Mirroring-Repo.git ## @bitbucket.org follow by your Bitbucket repository path
            - cd Mirroring-Repo.git ## cd followed by your Github repository Name
            - git push --mirror https://x-token-auth:"$GITHUB_VARIABLE"@github.com/asaphdanchi/Mirroring-Repo.git ## @github.com followed by your Github repository path
```

On your code replace the "$BITBUCKET_VARIABLE" and "$GITHUB_VARIABLE" with your corresponding variable names while keeping the $ and the "" sign. 

# Run the pipeline in Bitbucket
-In Bitbucket, select `Pipeline`, then click `Run Pipeline` in the top-right corner. Monitor the pipeline execution and confirm it completes successfully, indicated by a green status

<img width="953" alt="migration successful" src="https://github.com/user-attachments/assets/07c2ab89-1414-424e-9c9a-07e4d800fa7b" />

-Pipe line successful after two attempt 
![Screenshot 2025-02-05 064748](https://github.com/user-attachments/assets/1da1bd2b-91a2-4d83-9613-2de92369c9d3)

-Any change on the main repo automatically triger and synronise with the gitHub repo.

## Test the Pipeline

- Test file was created in bitbucket repo and it automatically syncronised with the gitHub repo https://github.com/kingakwa/Testing-migratio
- Commit and push the bitbucket-pipelines.yml file to Bitbucket.
- Navigate to Bitbucket Repository > Pipelines
- Manually trigger the pipeline or push a change to test.
- Verify that the repository is correctly mirrored to GitHub.
