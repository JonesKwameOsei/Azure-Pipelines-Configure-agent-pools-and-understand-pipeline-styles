# Azure-Pipelines-Configure-agent-pools-and-understand-pipeline-styles
## Implementing and use self-hosted agents with YAML pipelines

## Overview
`YAML-based pipelines` allow me to fully implement CI/CD as code, in which pipeline definitions reside in the same repository as the code that is part of my Azure DevOps project. YAML-based pipelines support a wide range of features that are part of the classic pipelines, such as `pull requests`, `code reviews`, `history`, `branching`, and `templates`.

Regardless of the choice of the pipeline style, to build the code or deploy the solution by using Azure Pipelines, I need an agent. An agent hosts compute resources that run one job at a time. Jobs can be run directly on the host machine of the agent or in a container. I have the option to run my jobs using Microsoft-hosted agents, which are managed for me, or implementing a self-hosted agent that I set up and manage on my own. 

In this lab, I will demonstrate how to implement and use `self-hosted agents` with YAML pipelines.

### Project Requirements
- Azure DevOps supported browser: I will be using `Microsoft Edge`.
- Create an `Azure DevOps organization`: Click [here](https://docs.microsoft.com/azure/devops/organizations/accounts/create-organization) to set up an Azure DevOps organisation if you do not already have one that can be used for this lab.
- Git for windows
- Visual Studio Code

### Configure the project prerequisites

In this exercise, I will prepare for the lab by setting up the necessary requirements. This includes creating a new `Azure DevOps` project with a repository based on [eShopOnWeb.](https://github.com/MicrosoftLearning/eShopOnWeb)

**Task 1: Establish and configure the team project**

In this task, we will create an **eShopOnWeb** Azure DevOps project to be utilized in multiple labs.

1. Open your Azure DevOps organization in a browser window on your lab computer. Click on **New Project**. Name the project **eShopOnWeb** and keep the other fields as default. Click on **Create**.<p>
Project created:<p>
![eshop](https://github.com/JonesKwameOsei/Azure-Pipelines-Configure-agent-pools-and-understand-pipeline-styles/assets/81886509/b9f8bb75-c7e0-4934-93cf-3403ac89a4c7)<p>
![eshop2](https://github.com/JonesKwameOsei/Azure-Pipelines-Configure-agent-pools-and-understand-pipeline-styles/assets/81886509/f53623e2-b845-457c-b279-1d292a41c656)<p>

**Task 2: Import eShopOnWeb Git Repository**

In this task, we will import the `eShopOnWeb Git repository` to be used in various labs.

Log into Azure DevOps organization and the previously created **eShopOnWeb** project in a browser window on the lab computer. Click on **Repos>Files**, then **Import a Repository**. Select **Import**. In the **Import a Git Repository** window, paste the following URL: `https://github.com/MicrosoftLearning/eShopOnWeb.git` and click **Import**.<p>
eShopOnWeb repo imported from `GitHub` into `Azure DevOps`:<p>
![eshop-clone](https://github.com/JonesKwameOsei/Azure-Pipelines-Configure-agent-pools-and-understand-pipeline-styles/assets/81886509/baf92599-f2f6-4fd4-96d2-3c3238291ee5)<p>
![eshop-clone2](https://github.com/JonesKwameOsei/Azure-Pipelines-Configure-agent-pools-and-understand-pipeline-styles/assets/81886509/bc124cd9-1188-41c0-91b4-396533755cd8)<p>
![eshop-clone3](https://github.com/JonesKwameOsei/Azure-Pipelines-Configure-agent-pools-and-understand-pipeline-styles/assets/81886509/72caed29-b461-4aeb-a3d9-90feaee845aa)<p>

The repository is structured as follows:
- `.ado` folder contains Azure DevOps YAML pipelines.
- `.devcontainer` folder contains setup for developing using containers (locally in VS Code or GitHub Codespaces).
- `infra` folder contains Bicep & ARM infrastructure as code templates used in certain lab scenarios.
- `.github` folder contains YAML GitHub workflow definitions.
- `src` folder contains the .NET 8 website used in the lab scenarios.

### Create an Application Lifecycle Build Pipeline using a YAML-based Template

**Task 1: Create an Azure DevOps YAML Pipeline**

In this task, you will create a template-based Azure DevOps YAML pipeline.

1. From the web browser displaying the `Azure DevOps portal` with the `eShopOnWeb project` open, in the vertical navigational pane on the left side, click **`Pipelines`.<p>
![pipeline1](https://github.com/JonesKwameOsei/Azure-Pipelines-Configure-agent-pools-and-understand-pipeline-styles/assets/81886509/303f4065-d14c-46a2-b3aa-5275d0b41c97)<p>

2. Click the **Create Pipeline** button - if you don't have any other pipelines created yet or click **New pipeline** to create an additional new one.<p>
![pipeline2](https://github.com/JonesKwameOsei/Azure-Pipelines-Configure-agent-pools-and-understand-pipeline-styles/assets/81886509/d70b400e-bf14-4269-ad42-4043b8529cd6)<p>

3. On the **Where is your code?** pane, click **Azure Repos Git**.<p>
![pipeline3](https://github.com/JonesKwameOsei/Azure-Pipelines-Configure-agent-pools-and-understand-pipeline-styles/assets/81886509/432d9a07-6333-4881-a69a-125d1ef49272)<p>

4. On the **Select a repository** pane, click **eShopOnWeb**.<p>
![select-eshop](https://github.com/JonesKwameOsei/Azure-Pipelines-Configure-agent-pools-and-understand-pipeline-styles/assets/81886509/256aae94-9182-4989-bbf6-4b163af3ec95)<p>

5. On the **Configure your pipeline** pane, click **Existing Azure Pipelines YAML File**.<p>
![select-azure-yml](https://github.com/JonesKwameOsei/Azure-Pipelines-Configure-agent-pools-and-understand-pipeline-styles/assets/81886509/a81ecb70-91a1-4ccf-8ecf-c24546a4514e)<p>

6. On the **Select an existing YAML file**, select **main** for the **Branch**, and `/.ado/eshoponweb-ci-pr.yml` for the **Path**.<p>
![select-ci-pr-yml](https://github.com/JonesKwameOsei/Azure-Pipelines-Configure-agent-pools-and-understand-pipeline-styles/assets/81886509/fd9f3fe7-f3df-4c73-ba71-5b1b70532505)<p>

7. Click **Continue**.
8. On the **Review your pipeline YAML** pane, review the sample pipeline.<p>
![review-yml1](https://github.com/JonesKwameOsei/Azure-Pipelines-Configure-agent-pools-and-understand-pipeline-styles/assets/81886509/646c429f-f2fa-49d0-bdda-ceff3839402e)<p>
![save-review2](https://github.com/JonesKwameOsei/Azure-Pipelines-Configure-agent-pools-and-understand-pipeline-styles/assets/81886509/e4825849-2475-4ad2-a408-9e86adb603c7)<p>

This is a relatively simple .NET application Build pipeline, which does the following:
   - A single **Stage**: Build
   - A single **Job**: Build
   - 3 tasks within the **Build Job**:
     - Dotnet Restore
     - Dotnet Build
     - Dotnet Publish
9. On the **Review your pipeline YAML** pane, click the down-facing caret symbol next to the **Run** button, and then click **Save**.<p>
![save-review](https://github.com/JonesKwameOsei/Azure-Pipelines-Configure-agent-pools-and-understand-pipeline-styles/assets/81886509/a58ec3cd-5c03-4eeb-b17e-6e777ec85ee0)<p>
![save-review2](https://github.com/JonesKwameOsei/Azure-Pipelines-Configure-agent-pools-and-understand-pipeline-styles/assets/81886509/6bd66a85-07c1-43e7-b3f8-e565f22fe232)<p>
```
# A .NET application Build pipeline
resources:
  repositories:
    - repository: self
      trigger: none

stages:
- stage: Build
  displayName: Build .Net Core Solution
  jobs:
  - job: Build
    pool:
      vmImage: ubuntu-latest
    steps:
    - task: DotNetCoreCLI@2
      displayName: Restore
      inputs:
        command: 'restore'
        projects: '**/*.sln'
        feedsToUse: 'select'

    - task: DotNetCoreCLI@2
      displayName: Build
      inputs:
        command: 'build'
        projects: '**/*.sln'
    
    - task: DotNetCoreCLI@2
      displayName: Test
      inputs:
        command: 'test'
        projects: 'tests/UnitTests/*.csproj'
    
    - task: DotNetCoreCLI@2
      displayName: Publish
      inputs:
        command: 'publish'
        publishWebProjects: true
        arguments: '-o $(Build.ArtifactStagingDirectory)'
```
### Administer Azure DevOps agent pools
In this session, I will implement a self-hosted Azure DevOps agent to run the pipeline. 

**Task 1: Configure an Azure DevOps self-hosting agent**

In this task, I will configure my lab Virtual Machine as an Azure DevOps self-hosting agent and use it to run a build pipeline.

1. Within the Lab Virtual machine (Lab VM) or my own computer, I will start a web browser, navigate to the Azure DevOps portal and sign in by using the Microsoft account associated with my Azure DevOps organization.

> Note: The Lab Virtual machine should have all necessary prerequisite software installed. If I am installing on my own computer, I will need to install .NET 8 SDKs or higher necessary to build the demo project. See [Download .NET](https://dotnet.microsoft.com/en-us/download).

2. In the Azure DevOps portal, in the upper right corner of the Azure DevOps page, I will click the User settings icon, depending on whether or not I have preview features turned on, I should either see a Security or Personal access tokens item in the menu, if I see Security, I will click on that, then select Personal access tokens. On the Personal Access Tokens pane, and click + New Token.<p>
![PAT](https://github.com/JonesKwameOsei/Azure-Pipelines-Configure-agent-pools-and-understand-pipeline-styles/assets/81886509/33799b99-25fb-48d0-9b35-baf44efba031)<p>
![PAT2](https://github.com/JonesKwameOsei/Azure-Pipelines-Configure-agent-pools-and-understand-pipeline-styles/assets/81886509/ba606a65-b326-42d0-965e-15e980dbd6f0)<p>

3. On the Create a new personal access token pane, I will click the Show all scopes link and, specify the following settings and click Create (leave all others with their default values):

| Setting | Value |
| --- | --- |
| Name | eShopOnWeb |
| Scope (custom defined) | Agent Pools (show more scopes option below if needed) |
| Permissions | Read and manage | <p>

![PAT-agent-pools](https://github.com/JonesKwameOsei/Azure-Pipelines-Configure-agent-pools-and-understand-pipeline-styles/assets/81886509/5eeb1fdb-d896-4107-a52b-1b6134c396c4)<p>

4. On the Success pane, I will copy the value of the personal access token to Clipboard.

> Note: I will ensure I copy the token. I will not be able to retrieve it once I close this pane.

5. On the Success pane, I will click Close.

6. On the Personal Access Token pane of the Azure DevOps portal, I will click Azure DevOps symbol in the upper left corner and then click Organization settings label in the lower left corner.<p>
![organis-settings](https://github.com/JonesKwameOsei/Azure-Pipelines-Configure-agent-pools-and-understand-pipeline-styles/assets/81886509/b33c86ee-916c-401f-8e18-ab5ad5643ddc)<p>

7. To the left side of the Overview pane, in the vertical menu, in the Pipelines section, I will click Agent pools.<p>
![agent-pools](https://github.com/JonesKwameOsei/Azure-Pipelines-Configure-agent-pools-and-understand-pipeline-styles/assets/81886509/3397f5ae-8b89-4e2e-97b3-7e4564309270)<p>

8. On the Agent pools pane, in the upper right corner, I will click `Add pool`.<p>
![agent-pools-add](https://github.com/JonesKwameOsei/Azure-Pipelines-Configure-agent-pools-and-understand-pipeline-styles/assets/81886509/bd048636-5d38-4e9b-9d6c-8467d1b3a4de)<p>

9. On the Add agent pool pane, in the `Pool type` dropdown list, I will select `Self-hosted`, in the Name text box, I will type `az400m03l03a-pool` and then click Create.
![agent-pools-selfhosted](https://github.com/JonesKwameOsei/Azure-Pipelines-Configure-agent-pools-and-understand-pipeline-styles/assets/81886509/49ae52c5-e089-45b9-bafc-4f09c0aa8a2b)<p>

10. Back on the Agent pools pane, I will click the entry representing the newly created `az400m03l03a-pool`.

11. On the Jobs tab of the az400m03l03a-pool pane, I will click the New agent button.<p>
![agent-pools-selfhosted2](https://github.com/JonesKwameOsei/Azure-Pipelines-Configure-agent-pools-and-understand-pipeline-styles/assets/81886509/332b7803-132d-4008-b20e-c73ae419e18c)<p>
![agent-pools-selfhosted-created](https://github.com/JonesKwameOsei/Azure-Pipelines-Configure-agent-pools-and-understand-pipeline-styles/assets/81886509/66c0ee98-0c35-401f-b1ed-1303d9051c55)<p>

12. On the Get the agent pane, I will ensure that the Windows and x64 tabs are selected, and click Download to download the zip archive containing the agent binaries to download it into the local Downloads folder within my user profile.

> Note: If an error message is returned at this point indicating that the current system settings prevent me from downloading the file, in the Browser window, in the upper right corner, click the gearwheel symbol designating the Settings menu header, in the dropdown menu, select Internet Options, in the Internet Options dialog box, click Advanced, on the Advanced tab, click Reset, in the Reset Browser Settings dialog box, click Reset again, click Close, and try the download again.

13. I will start `Windows PowerShell` as administrator and in the Administrator: Windows PowerShell console run the following lines to create the `C:\agent` directory and extract the content of the downloaded archive into it.

```
cd \
mkdir agent ; cd agent
$TARGET = Get-ChildItem "$Home\Downloads\vsts-agent-win-x64-*.zip"
Add-Type -AssemblyName System.IO.Compression.FileSystem
[System.IO.Compression.ZipFile]::ExtractToDirectory($TARGET, "$PWD")
```

14. In the same Administrator: Windows PowerShell console, I will run the following to configure the agent:

```
.\config.cmd
```

**When prompted, I will specify the values of the following settings:**

| Setting | Value |
| --- | --- |
| Enter server URL | the URL of my Azure DevOps organization, in the format https://dev.azure.com/<organization_name>, where <organization_name> represents the name of my Azure DevOps organization |
| Enter authentication type (press enter for PAT) | Enter |
| Enter personal access token | The access token I recorded earlier in this task |
| Enter agent pool (press enter for default) | az400m03l03a-pool |
| Enter agent name | az400m03-vm0 |
| Enter work folder (press enter for _work) | Enter |
| (Only if shown) Enter Perform an unzip for tasks for each step. (press enter for N) | WARNING: only press Enter if the message is shown |
| Enter run agent as service? (Y/N) (press enter for N) | Y |
| enter enable SERVICE_SID_TYPE_UNRESTRICTED (Y/N) (press enter for N) | Y |
| Enter User account to use for the service (press enter for NT AUTHORITY\NETWORK SERVICE) | Enter |
| Enter whether to prevent service starting immediately after configuration is finished? (Y/N) (press enter for N) | Enter |

> **Note**: I can run self-hosted agent as either a service or an interactive process. I might want to start with the interactive mode, since this simplifies verifying agent functionality. For production use, I should consider either running the agent as a service or as an interactive process with auto-logon enabled, since both persist their running state and ensure that the agent starts automatically if the operating system is restarted.

15. witch to the browser window displaying the Azure DevOps portal and close the Get the agent pane.

Back on the Agents tab of the `az400m03l03a-pool` pane, note that the newly configured agent is listed with the Online status.

16. In the web browser window displaying the Azure DevOps portal, in the upper left corner, I will click the Azure DevOps label.

17 From the list of projects, I will select the tile representing my `eShopOnWeb` project.

18. On the eShopOnWeb pane, in the vertical navigational pane on the left side, in the Pipelines section, I will click Pipelines.

19. On the Recent tab of the Pipelines pane, I will select eShopOnWeb and, on the eShopOnWeb pane, select Edit.

20. On the eShopOnWeb edit pane, in the existing YAML-based pipeline, I will replace line 13 which says `vmImage: ubuntu-latest` designating the target agent pool with the following content, designating the newly created self-hosted agent pool:

```
name: az400m03l03a-pool
demands:
- Agent.Name -equals az400m03-vm0
```
Complete Pipeline Yaml file:<p>
```
# A .NET application Build pipeline
resources:
  repositories:
    - repository: self
      trigger: none

stages:
- stage: Build
  displayName: Build .Net Core Solution
  jobs:
  - job: Build
    pool:
      name: az400m03l03a-pool
      demands:
      - Agent.Name -equals az400m03-vm0
    steps:
    - task: DotNetCoreCLI@2
      displayName: Restore
      inputs:
        command: 'restore'
        projects: '**/*.sln'
        feedsToUse: 'select'

    - task: DotNetCoreCLI@2
      displayName: Build
      inputs:
        command: 'build'
        projects: '**/*.sln'
    
    - task: DotNetCoreCLI@2
      displayName: Test
      inputs:
        command: 'test'
        projects: 'tests/UnitTests/*.csproj'
    
    - task: DotNetCoreCLI@2
      displayName: Publish
      inputs:
        command: 'publish'
        publishWebProjects: true
        arguments: '-o $(Build.ArtifactStagingDirectory)'
```

**WARNING: Careful with copy/paste, make sure I have the same indentation shown above.**

21. On the eShopOnWeb edit pane, in the upper right corner of the pane, I will click Validate and save. This will automatically trigger the build based on this pipeline.

22. In the Azure DevOps portal, in the vertical navigational pane on the left side, in the Pipelines section, I will click Pipelines. Depending on my lab setup, the pipeline might prompt me for permissions. I will click Permit to allow the pipeline to run.

23. On the Recent tab of the Pipelines pane, I will click the eShopOnWeb entry, on the Runs tab of the eShopOnWeb pane, I will select the most recent run, on the Summary pane of the run, I will scroll down to the bottom, in the Jobs section, click Phase 1 and monitor the job until its successful completion.<p>:

**Pipelines run successfuly**:
![self-hosted-build](https://github.com/JonesKwameOsei/Azure-Pipelines-Configure-agent-pools-and-understand-pipeline-styles/assets/81886509/069b7c73-8c31-4855-ab85-134d32d45e45)<p>
![self-hosted-build2](https://github.com/JonesKwameOsei/Azure-Pipelines-Configure-agent-pools-and-understand-pipeline-styles/assets/81886509/3887c53b-2610-4ad1-ba94-9b1c39be400d)<p>

**Artifact built and published**:
![self-hosted-build3](https://github.com/JonesKwameOsei/Azure-Pipelines-Configure-agent-pools-and-understand-pipeline-styles/assets/81886509/873d25f2-91a0-4b61-971d-783f5aaa7a33)<p>
![self-hosted-build4](https://github.com/JonesKwameOsei/Azure-Pipelines-Configure-agent-pools-and-understand-pipeline-styles/assets/81886509/57bb04b5-f4ad-4566-868b-9a64f864ec4d)<p>

## Clean Up resources used in the lab

I will stop and remove the agent service by running `.\config.cmd remove` from the command prompt.
I will delete the agent pool.
I will revoke the PAT token.
I will revert the changes in the `eshoponweb-ci-pr.yml` file by navigating to it from `Repos/.ado/eshoponweb-ci-pr.yml`, selecting Edit and removing lines 13-15 (the agent pool snippet), and changing back to `vmImage: ubuntu-latest` as it was originally. (This is because I will use the same sample pipeline file in a future lab exercise.)<p>
![image](https://github.com/JonesKwameOsei/Azure-Pipelines-Configure-agent-pools-and-understand-pipeline-styles/assets/81886509/04e24cfe-2cf4-41f2-9add-6469a21824d1)<p>
Actions.yml edited:
```
stages:
- stage: Build
  displayName: Build .Net Core Solution
  jobs:
  - job: Build
    pool:
      vmImage: ubuntu-latest
```

### Conclusion
During this lab, I have gained knowledge on the implementation and utilisation of self-hosted agents through YAML pipelines.



















