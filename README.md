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
![review-yml2](https://github.com/JonesKwameOsei/Azure-Pipelines-Configure-agent-pools-and-understand-pipeline-styles/assets/81886509/4cef962d-33c9-4319-9a74-3426faa08bbe)<p>

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
























