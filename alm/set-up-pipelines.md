---
title: Set up pipelines in Power Platform
description: Learn how to create, configure, share, and manage Power Platform pipelines.
author: caburk
ms.author: matp
ms.service: powerapps
ms.topic: how-to
ms.date: 02/28/2023
ms.custom: template-how-to
---
# Set up pipelines in Power Platform

Create and run pipelines to easily deploy solutions to environments.

## Create a pipeline

Power Platform administrators can create one or more pipelines, associate any number of environments, then share access with those that will administer or run pipelines.

### Prerequisites

- Four environments are recommended, but you can use as few as three Power Platform environments to create a pipeline.
- All environments used in pipelines must have a Microsoft Dataverse database.
- Microsoft Dataverse for Teams environments aren't supported for use with pipelines.
- You must have a Power Platform administrator or Dataverse system administrator role.
- Pipelines are a feature of Managed Environments. As such, the development and target environments used in a pipeline must be enabled as a managed environment. Standalone licenses won't be required when you use developer or trial environments with pipelines. More information: [Managed Environments](../admin/managed-environment-overview.md)

> [!IMPORTANT]
>
> - Managed environment enforcement is enabled for pipelines upon general availability (GA). As a result, any pre-existing pipelines during preview may no longer function until enabled as a managed environment.
> - End users within any production environment(s) linked to pipelines will require a standalone license for either Power Apps, Power Automate, or Dynamics 365. Makers generally won't require a standalone license unless they're also an end user.

#### Choose environments for pipelines

Before you begin, you’ll need to choose which environments will participate in pipelines. A common setup might include the following:

| Environment purpose | Environment type | Managed environment | Standalone license required |
|--------------|-----|----|-----------|
| Host | Production | No | No |
| Development | Developer | Yes at GA | No |
| QA |  Developer | Yes at GA | No |
| Production |  Production | Yes at GA | Yes for end users at GA |

- **Host environment (required)**. This special-purpose environment acts as the **storage** and management plane for all pipeline configuration, security, and run history.
  - As this is the control center for all deployment activities, we recommend you keep this as a **dedicated production environment** separate from the development and target QA and production environments that participate in pipelines.
  - If desired, you can configure multiple different hosts within a tenant, such as for separately managing pipelines for different business organizations or geographic locations. One host for the entire tenant is also acceptable as long as all environments are in the same geographic location.

   > [!IMPORTANT]
   >
   > Deleting the host environment deletes all pipelines and run data. Use caution and understand the impact of data and configuration loss as well as maker access to pipelines hosted in the environment.
   >
- **Development (required)**. This is where you’ll develop solutions. 
- **QA environment (optional)**. This is where you’ll deploy solutions for testing prior to moving them to production.
- **Production (required)**. The final destination for a deployment pipeline. This is where end users will run the apps that are deployed.

> [!TIP]
> Use environment names that indicate their purpose. For example, *Contoso Host*, *Contoso Development*, *Contoso QA*, and so forth.

### Install the pipelines application in your host environment

This step is only required for the initial host setup. You may skip to the next section if you already have access to a host environment where you'll create pipelines.

1. Sign in to the [Power Platform admin center](https://admin.powerplatform.microsoft.com/), go to **Environments** > **New**, and create a new environment with a Dataverse database. Be sure to choose the same region that your development, QA, and production environments are created in.
2. Install the **Power Platform Pipelines** application in your host environment by selecting the host environment, then select **Resources** > **Dynamics 365 apps**.
3. Select **Install app** and scroll down within the right-side panel until you find **Power Platform Pipelines**.
4. Select **Next**, if you agree, accept the terms, and then select **Install**.
   > [!IMPORTANT]
   > You only need to install the deployment pipelines application in the host environment. You don't need to install it in other environments, such as development, QA or production environments that will be associated with your pipelines.

Once installed, the deployment pipelines configuration application will appear in the list of installed apps.

### Configure a deployment pipeline

1. Copy and paste the environment IDs of all environments participating in the pipelines to a clear text editor such as Notepad. You’ll need these later. More information: [Find your environment and organization ID](/power-platform/admin/determine-org-id-name#find-your-environment-and-organization-id)
1. Once the **Deployment Pipeline** package installation has completed, go to [Power Apps](https://make.powerapps.com/?utm_source=padocs&utm_medium=linkinadoc&utm_campaign=referralsfromdoc), then select the dedicated host environment (where you installed the application).
1. **Play** the **Deployment Pipeline Configuration** app.
1. Select **Environments** on the left pane, and then select **New** to create the environment records in Dataverse:
   - **Name**: Enter the name for the environment record. It's a good idea to include the same name as the actual environment, such as **Contoso Dev**.
   - **Environment Type**: Select either **Development Environment** or **Target Environment**. Select **Development Environment** for all source environments where unmanaged solutions will be developed. Select **Target Environment** for QA and production environments where the managed solution artifacts will be deployed.
   - **Environment Id**: Paste in the environment ID that you copied in the earlier step.
1. Select **Save**.
1. Refresh the form, then verify **Validation Status** equals **Success**.
1. Repeat steps 4-6 until all environments that will participate in the pipeline have environment records created.
1. Select **Pipelines** on the left navigation pane, and then select **New** to create a new deployment pipeline:
   - **Name**: Enter a name for the pipeline, such as *Contoso Standard Deployment Pipeline* or *Contoso Hotfix Pipeline*.
   - **Description**: Optionally, enter a meaningful description for the pipeline.
    :::image type="content" source="media/Create new pipeline.png" alt-text="New pipeline icon":::

1. Within the **Linked Development Environments** grid, select **Add Existing Development Environment**, then associate one or more development environments. Note that a pipeline must have at least one development environment and one stage before it can be run.
:::image type="content" source="media/Pipelines add development environment.png" alt-text="Add development environment icon":::

1. Within the **Deployment Stages** grid, select **New Deployment Stage**, to display the quick create pane.
   :::image type="content" source="media/Pipelines create stage QA.png" alt-text="Pipeline stages icon":::

1. Enter the details for each stage, and then select **Save and Close**:
   - **Name**: The name of the stage.
   - **Description** (optional): Optional description for the stage.
   - **Previous Deployment Stage** (optional): Specifies a deployment stage that must be deployed to before deploying to the current stage. For example, when creating a production stage, you can add the test stage as the **Previous Deployment Stage**. Note that for the first stage, or pipelines containing only one stage, this  should be left blank.
   - **Target Deployment Environment**: This is the target environment where this stage will deploy to.
1. Repeat the previous two steps for each stage you'd like to add to the pipeline. Note that you must have at least one stage. You can add up to seven stages.
   
   :::image type="content" source="media/Pipeline fully configured.png" alt-text="A fully configured pipeline.":::

## Grant access to edit or run pipelines

Pipeline security is managed within the host environment by assigning security roles. Additionally, users must currently have access to all environments associated with a pipeline in order to create or run the pipeline.

When you installed the Power Platform pipelines application, two security roles were added:

- **Deployment Pipeline User**: Has privileges to run pipelines that have been shared with them.
- **Deployment Pipeline Administrator**: Has full control over all pipeline configuration, without needing system administrator security role membership.

### Share pipelines with makers

The **Deployment Pipeline User** security role grants access to run one or more pipelines. It doesn't grant access to create, edit, or delete pipelines. Users with the Deployment Pipeline User security role won't see the host environment within the environment picker in Power Apps or Power Automate, or otherwise need to be aware of it.

To share pipelines:

- Assign makers the **Deployment Pipeline User** security role within the host environment. This security role is installed with the Power Platform pipelines application. More information: [Assign a security role to a user](../admin/assign-security-roles.md)
- In the deployment pipeline configuration app, **Share** the pipeline record with makers (or Azure Active Directory groups). **Read** privilege is sufficient to view and run a pipeline from within the development environment. More information: [Share rows with a user or team](/power-apps/user/share-row)
- Makers must also have privileges to export solutions from the source development environments, as well as privileges to import solutions to the target test and production environments for which the pipeline deploys to. By default, system customizer and environment maker roles have these privileges.

### Share with pipeline administrators

The Deployment Pipeline Administrator security role grants full privileges to all pipelines (and tables, which store pipeline information) within the host environment. It also grants access to run all pipelines associated with the current host. The Deployment Pipeline Administrator security role doesn't grant privileges to perform other activities within the host environment.

> [!IMPORTANT]
> Pipeline administrators must also have access to all development, test, and production environments that are associated with pipelines they create or run.

Assign the **Deployment Pipeline Administrator** security role to users or Azure Active Directory groups within the host environment. This security role is installed with the **Power Platform Pipelines** application. More information: [Assign a security role to a user](../admin/assign-security-roles.md)

## Centrally manage and monitor deployments

The pipeline configuration app and host environment provide many other benefits such as:

- Centrally view all deployment activity. Filtered views are also accessible within the maker experience for a given pipeline and solution.
- Audit where a certain solution version is deployed as well as who initiated the request.
- View error logs, validation results, and deployment settings. Environment variables and connections are provided during deployment.
- Retain backups of all solution artifacts by version.
- Schedule bulk delete jobs to remove unwanted data and conserve database capacity. More information: [Remove a large amount of specific, targeted data with bulk deletion](../admin/delete-bulk-records.md)
- Use a dashboard to visualize deployment metrics. You may also build your own reports using data stored within the host.

## Next steps

[Run pipelines in Power Platform](run-pipeline.md)
