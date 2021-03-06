---

copyright:
  years: 2017, 2020
lastupdated: "2020-04-20"

keywords: schematics workspaces, schematics workspace vs github repo, schematics workspace access, schematics freeze workspace

subcollection: schematics

---
{:new_window: target="_blank"}
{:shortdesc: .shortdesc}
{:screen: .screen}
{:pre: .pre}
{:table: .aria-labeledby="caption"}
{:codeblock: .codeblock}
{:tip: .tip}
{:note: .note}
{:important: .important}
{:deprecated: .deprecated}
{:download: .download}
{:preview: .preview}
{:external: target="_blank" .external}
{:support: data-reuse='support'}
{:help: data-hd-content-type='help'}

# Setting up workspaces
{: #workspace-setup}

With {{site.data.keyword.bplong_notm}} workspaces, you can organize your Terraform templates and control who has access to run infrastructure code in your {{site.data.keyword.cloud_notm}} account. Before you create a workspace, make sure that you design the organizational structure of your GitHub repository and workspaces so that you can replicate and manage your configurations across multiple environments. 
{: shortdesc} 

## Designing your workspace and GitHub repository structure
{: #structure-workspace}
{: help}
{: support}

Plan out the organizational structure of your workspaces and GitHub repositories so that they match the microservice and permission structure of your organization. 
{: shortdesc}

### How many workspaces do I need?
{: #plan-number-of-workspaces}

To find out how many workspaces you need in {{site.data.keyword.bplong_notm}}, look at the microservices that build your app and the environments that you need to develop, test, and publish your microservice. 
{: shortdesc}

As a rule of thumb, consider creating separate workspaces for each of your microservices and the environment that you use. For example, if you have a product app that consists of a search, payment, and review microservice component, consider creating a separate workspace for each microservice component and development, staging, and production environment. With separate workspaces for each microservice component and environment, you can develop, deploy, and manage the Terraform configuration files and associated {{site.data.keyword.cloud_notm}} resources without affecting the resources of other microservices. 

Review the following image to see the number of workspaces in {{site.data.keyword.bplong_notm}} for an app that consists of three microservices. 

<img src="images/workspace-structure.png" alt="Workspace structure for {{site.data.keyword.bplong_notm}}" width="800" style="width: 800px; border-style: none"/>

Do not use one workspace to manage an entire staging or production environment. When you deploy all of your {{site.data.keyword.cloud_notm}} resources into a single workspace, it can become difficult for various teams to coordinate updates and manage access for these resources.
{: important}

### How do I structure my GitHub repository to map my workspaces?
{: #plan-github-structure}

Structure your GitHub repository so that you have one repository for all your Terraform configuration files that build your microservice, and use input variables in {{site.data.keyword.bpshort}}, or GitHub branches or directories to differentiate between your development, staging, and production environment. 
{: shortdesc}

Review the following table to find a list of options for how to structure your GitHub repository to map the different workspace environments. 
{: shortdesc}

| Option | Description | 
| ------- | ---------------------------- | 
| One GitHub repo, use variables to distinguish between environments | Create one GitHub repository where you store the Terraform configuration files that make up your microservice component. Make your Terraform configuration files as general as possible so that you can reuse the same configuration across your environments. To configure the specifics of your development, staging, and production environment, use [Terraform input variables](/docs/schematics?topic=schematics-create-tf-config#configure-variables) in your configuration files. Input variables are automatically loaded into {{site.data.keyword.bplong_notm}} when you create your workspace. To customize your workspace, you enter the environment-specific values for your variables. This setup is useful if you have one team that manages the lifecycle of the microservice component and where the configuration of your environments does not differ drastically. |
|One GitHub repo, use branches to distinguish between environments | Create one GitHub repository for your microservice component, and use different GitHub branches to store the Terraform configuration files for each of your environments. With this setup, you have a clear distinction between your environments and more control over who can access and change a particular configuration. Make sure to set up a process for how changes in one configuration file are populated across branches to avoid that you have different configurations in each environment. |
| One GitHub repo, use directories to distinguish between environments | For organizations that prefer short-lived branches, and where configurations differ drastically across environments, consider creating directories that represent the different configurations of your environments. With this setup, all your directories listen for changes that are committed to the `master` branch. Make sure to set up a process for how changes in one configuration file are populated across directories to avoid having different configurations in each environment. |
| Use one GitHub repo per environment | Use one GitHub repository for each of your environments. With this setup, you have a 1:1 relationship between your workspace and GitHub repository and you can apply separate permissions for each of your GitHub repositories. Make sure that your team can manage multiple GitHub repositories and keep them in sync. | 

For more information about how to structure your GitHub repository, see [Repository Structure](https://www.terraform.io/docs/cloud/workspaces/repo-structure.html){: external}. 

### How can I reuse configuration files across environments and workspaces? 
{: #plan-reuse}

Try to minimize the number of Terraform configuration files that you need to manage by creating standardized resource templates and by using variables to customize the template to your needs. 
{: shortdesc}

With standardized resource templates, you can ensure that development best practices are followed within your organization and that all Terraform configuration files have the same structure. Knowing the structure of a Terraform configuration file makes it easier for your developers to understand a file, declare variables, contribute to the code, and troubleshoot the errors. 

### How do I control access to my workspaces? 
{: #plan-workspace-access}

{{site.data.keyword.bplong_notm}} is fully integrated with {{site.data.keyword.cloud_notm}} Identity and Access Management. To control access to a workspace, and who can execute your infrastructure code with {{site.data.keyword.bplong_notm}}, see [Managing user access](/docs/schematics?topic=schematics-access). 

### What do I need to be aware of when I have a repository that I managed with native Terraform?
{: #plan-terraform-migration}

Because {{site.data.keyword.bplong_notm}} delivers Terraform-as-a-Service, you can import your existing Terraform templates into {{site.data.keyword.bpshort}} workspaces. Depending on how your Terraform templates and GitHub repositories are structured, you might need to make changes so that you can successfully use {{site.data.keyword.bplong_notm}}. 
{: shortdesc}

- **`provider` block declaration**: Because {{site.data.keyword.bplong_notm}} is integrated with {{site.data.keyword.cloud_notm}} Identity and Access Management, your {{site.data.keyword.cloud_notm}} API key is automatically retrieved for all IAM-enabled resources and you don't have to provide this information in the `provider` block. However, the API key is not retrieved for classic infrastructure and Cloud Foundry resources. For more information, see [Configuring the `provider` block](/docs/schematics?topic=schematics-create-tf-config#configure-provider). 
- **`terraform.tfstate` file**: If you used native Terraform before to provision and manage {{site.data.keyword.cloud_notm}} resources, you might have a `terraform.tfstate` file in your GitHub repository that stores the current state of your Terraform-deployed {{site.data.keyword.cloud_notm}} resources. {{site.data.keyword.bplong_notm}} does not support importing an existing `terraform.tfstate` file. Because the `terraform-tfstate` file is not available to {{site.data.keyword.bplong_notm}}, you cannot use the service to manage {{site.data.keyword.cloud_notm}} resources that you already started managing with native Terraform. 
- **Terraform CLI and {{site.data.keyword.cloud_notm}} provider plug-in:** To use {{site.data.keyword.bplong_notm}}, you don't need to install the Terraform CLI or the {{site.data.keyword.cloud_notm}} Provider plug-in for Terraform. If you want to automate the provisioning of resources, try out the [{{site.data.keyword.bplong_notm}} CLI plug-in](/docs/schematics?topic=schematics-setup-cli) instead. 

## Creating workspaces
{: #create-workspace}

Create your workspace that points to the GitHub repository that hosts your Terraform template by using the {{site.data.keyword.bplong_notm}} console. 
{: shortdesc} 

**Before you begin**
- [Create a Terraform configuration](/docs/schematics?topic=schematics-create-tf-config), and store the configuration in a GitHub or GitLab repository. 
- Make sure that you have the [required permissions](/docs/schematics?topic=schematics-access) to create a workspace. 

**To create a workspace**:
1. From the {{site.data.keyword.cloud_notm}} menu, select [**{{site.data.keyword.bpshort}}**](https://cloud.ibm.com/schematics/overview){: external}. 
2. Click **Create a workspace**. 
3. Configure your workspace. 
   1. Enter a descriptive name for your workspace. The name can be up to 128 characters long and can include alphanumeric characters, spaces, dashes, and underscores. When you create a workspace for your own Terraform template, consider including the microservice component that you set up with your Terraform template and the {{site.data.keyword.cloud_notm}} environment where you want to deploy your resources in your name. For more information about how to structure your workspaces, see [Designing your workspace structure](/docs/schematics?topic=schematics-workspace-setup#structure-workspace).
   2. Optional: Enter tags for your workspace. You can use the tags later to find workspaces that are related to each other.
   3. Select the resource group where you want to create the workspace.
   4. Optional: Enter a description for your workspace.
   5. Click **Create** to create your workspace. Your workspace is created with a **Draft** state and the workspace **Settings** page opens.
4. Connect your workspace to the GitHub or GitLab source repository where your Terraform configuration files are stored.
   1. On the workspace **Settings** page, enter the link to your GitHub or GitLab repository. The link can point to the `master` branch, any other branch, or a subdirectory. 
      - Example for `master` branch: `https://github.com/myorg/myrepo`
      - Example for other branches: `https://github.com/myorg/myrepo/tree/mybranch`
      - Example for subdirectory: `https://github.com/mnorg/myrepo/tree/mybranch/mysubdirectory`
   2. If you want to use a private GitHub repository, enter your personal access token. The personal access token is used to authenticate with your GitHub repository to access your Terraform template. For more information, see [Creating a personal access token for the command line](https://help.github.com/en/articles/creating-a-personal-access-token-for-the-command-line).
   3. Select the Terraform version that your Terraform configuration files are written in. {{site.data.keyword.bpshort}} supports Terraform version 0.11 and 0.12. 
   4. Click **Save template information**. {{site.data.keyword.bplong_notm}} automatically downloads the configuration files, scans them for syntax errors, and retrieves any input variables.
   5. If you specified input variables, enter the values that you want to use, and click **Save changes**. 
5. Wait for your workspace to reach an **Inactive** state. This state is reached when {{site.data.keyword.bpshort}} successfully downloads your configuration files and no syntax errors are found. 
6. [Create an execution plan for your workspace](/docs/schematics?topic=schematics-manage-lifecycle#deploy-resources). 

## Freezing and unfreezing workspaces 
{: #lock-workspace}

As the {{site.data.keyword.cloud_notm}} account owner or an {{site.data.keyword.bplong_notm}} user who is assigned the **Manager** IAM service access role for {{site.data.keyword.bpshort}}, you can disable changes to a workspace (freeze) so that you cannot create a Terraform execution plan or run your infrastructure code to provision or modify your {{site.data.keyword.cloud_notm}} resources. 
{: shortdesc}

Before you begin, make sure that you are assigned the [**Manager** IAM service access role](/docs/schematics?topic=schematics-access) for {{site.data.keyword.bplong_notm}}. 

**To freeze a workspace**: 
1. From the [workspace dashboard](https://cloud.ibm.com/schematics/workspaces){: external}, select the workspace that you want to freeze. 
2. Select the **Settings** tab. 
3. In the **State** section on the workspace settings page, set the toggle to **Frozen** to disable changes to your workspace. The ID of the user who freezes the workspace and a timestamp are automatically logged. After you freeze a workspace, no user can generate a Terraform execution plan or apply the plan in {{site.data.keyword.cloud_notm}}. 

**To unfreeze a workspace**: 
1. From the [workspace dashboard](https://cloud.ibm.com/schematics/workspaces){: external}, select the workspace that you want to unfreeze. 
2. Select the **Settings** tab. 
3. In the **State** section on the workspace settings page, set the toggle to **Unfrozen**. The ID of the user who unfreezes the workspace and a timestamp are automatically logged. After you unfreeze a workspace, you can generate new Terraform execution plans or run your infrastructure code by applying the plan in {{site.data.keyword.cloud_notm}}.

## Setting up a continuous delivery toolchain for your workspace
{: #continuous-delivery}
  
Connect your source repository to a continuous delivery pipeline in {{site.data.keyword.cloud_notm}} to automatically generate a Terraform execution plan and run your Terraform code in {{site.data.keyword.cloud_notm}} whenever you update your Terraform configuration files. 
{: shortdesc}
  
1. If you do not have a {{site.data.keyword.contdelivery_short}} service instance in your account yet, create one.
   1. From the {{site.data.keyword.cloud_notm}} catalog, open the [{{site.data.keyword.contdelivery_short}} service](https://cloud.ibm.com/catalog/services/continuous-delivery).
   2. Select the {{site.data.keyword.cloud_notm}} region where you want to create the service.
   3. Select a pricing plan.
   4. Enter a name for your service instance, select a resource group, and enter any tags that you want to associate with your service instance.
   5. Click **Create** to create the service instance in your account.  
2. From the [workspace dashboard](https://cloud.ibm.com/schematics/workspaces){: external}, select a workspace. 
3. Select the **Settings** tab. 
4. In the **Summary** section, click **Enable continuous delivery**. 
5. Configure your toolchain. 
   1. Enter a name for your toolchain, and select the region and resource group where you want to deploy this toolchain. The region and resource group can be different from the region and resource group that you used for your {{site.data.keyword.bpshort}} workspace.
   2. Select the type of source repository where your Terraform configuration files are stored. For example: GitHub. 
   3. Review the information for your source repository. For example, if your Terraform files are stored in GitHub, review the GitHub server and the repository for which you want to create a continuous delivery toolchain. These fields are prepopulated based on your workspace configuration.
   4. Optional: Choose if you want to enable GitHub issues and code change tracking for your toolchain. 
6. Select the **Delivery Pipeline** icon to configure your Delivery Pipeline. 
   1. Verify that the workspace ID that is displayed to you is correct.
   2. Enter an {{site.data.keyword.cloud_notm}} API key. If you do not have an API key, click **New +** to create one. 
7. Click **Create** to finish the setup of your toolchain. You see an overview of tools that were configured for your toolchain. 
8. Open the **Delivery Pipeline**. The Delivery Pipeline includes stages to retrieve updates from your source repository, create a Terraform execution plan, apply this plan, and to run a health check against your workspace.
9. Update the Terraform file in your source repository and review how this change is processed in your Delivery Pipeline. If one of the stages fails, click **View logs and history** to start troubleshooting errors. 
   
## Overview of workspace states
{: #workspace-states}

The state of a workspace indicates if you have successfully created a Terraform execution plan and applied the plan to provision your resources in your {{site.data.keyword.cloud_notm}} account. 
{: shortdesc}

Review the states that a workspace can have in the following table. You might not see all states in the {{site.data.keyword.cloud_notm}} console. Some states are only visible when using the CLI or API. 

| State | Description | 
| ------- | ---------------------------- | 
| Active | After you successfully ran your infrastructure code with {{site.data.keyword.bplong_notm}} by applying your Terraform execution plan, the state of your workspace changes to **Active**. |
| Connecting | {{site.data.keyword.bpshort}} tries to connect to the template in your source repo. If successfully connected, the template is downloaded and metadata, such as input parameters, is extracted. After the template is downloaded, the state of the workspace changes to **Scanning**. |
| Draft | The workspace is created without a reference to a GitHub or GitLab repository.   |
| Failed | If errors occur during the execution of your infrastructure code in {{site.data.keyword.bplong_notm}}, your workspace state is set to **Failed**. To troubleshoot errors, open the logs on the workspace **Activity** page. |
| Inactive | The {{site.data.keyword.bpshort}} template was scanned successfully and the workspace creation is complete. You can now start running {{site.data.keyword.bpshort}} plan and apply actions to provision the {{site.data.keyword.cloud_notm}} resources that you specified in your template. If you have an **Active** workspace and decide to remove all your resources, your workspace is set to **Inactive** after all your resources are removed.  |
| In progress | When you instruct {{site.data.keyword.bplong_notm}} to run your infrastructure code by applying your Terraform execution plan, the state of your workspace changes to **In progress**. |
| Scanning | The download of the {{site.data.keyword.bpshort}} template is complete and vulnerability scanning started. If the scan is successful, the workspace state changes to **Inactive**. If errors in your template are found, the state changes to **Template Error**. |
| Stopped | The {{site.data.keyword.bpshort}} plan, apply, or destroy action was cancelled manually. |
| Template Error | The {{site.data.keyword.bpshort}} template contains errors and cannot be processed.|

