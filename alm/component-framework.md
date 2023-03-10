---
title: "Power Apps component framework | Microsoft Docs"
description: "Learn about including Power Apps component framework components in solutions."
keywords: 
author: mikkelsen2000
ms.subservice: alm
ms.author: pemikkel
manager: kvivek
ms.custom: ""
ms.date: 05/05/2020
ms.reviewer: "pehecke"

ms.topic: "article"
search.audienceType: 
  - developer
search.app: 
  - PowerApps
  - D365CE
---

# Power Apps component framework

To be accessible by Power Apps makers, components in the Power Apps component framework must be packaged in a
solution, exported, and then imported into a Power Apps environment with Dataverse. The following sections describe how to do this.

For more information about using ALM techniques with code components see
[Code components application lifecycle management (ALM)](/powerapps/developer/component-framework/code-components-alm).

## Package and deploy a code component

This section describes how to import code components into Microsoft Dataverse so
that the components are available to Power Apps makers.

After implementing the code components by using the Power Platform CLI, the next step
is to pack all the code component elements into a solution file and import the
solution file into Dataverse so that you can see the code components
in the maker runtime experience.

To create and import a solution file:

1.  Create a new folder in the folder that has the cdsproj file, and name it
    **Solutions** (or any name of your choice) by using the CLI command 
    `mkdir Solutions`. Navigate to the directory by using the command `cd Solutions`.

2.  Create a new solution project by using the following command. The solution
    project is used for bundling the code component into a solution zip
    (compressed) file that's used for importing into Dataverse.

    ```dotnetcli
    pac solution init --publisher-name \<enter your publisher name\>
    --publisher-prefix \<enter your publisher prefix\>
    ```

    > [!NOTE]
    > The publisher-name and publisher-prefix values must be unique
    > to your environment. More information: [Solution publisher](solution-concepts-alm.md#solution-publisher) and
    > [Solution publisher prefix](solution-concepts-alm.md#solution-publisher-prefix)

3.  After the new solution project is created, refer the **Solutions** folder to
    the location where the created sample component is located. You can add the
    reference by using the command shown below. This reference informs the solution
    project about which code components should be added during the build. You
    can add references to multiple components in a single solution project.

    ```dotnetcli
    pac solution add-reference --path \<path to your Power Apps component framework project\>
    ```dotnetcli

4.  To generate a zip file from the solution project, go to your solution
    project directory and build the project by using the following command. This
    command uses the MSBuild program to build the solution project by pulling down
    the NuGet dependencies as part of the restore. Only use `/restore` the
    first time the solution project is built. For every build after that,
    you can run the command `msbuild`.

    ```dotnetcli
    msbuild /t:build /restore
    ```

    > [!TIP]
    > - If *MSBuild* 15.9.\* is not in the path, open Developer Command Prompt for Visual Studio 2017 to run the `msbuild` commands.
    > - Building the solution in the *debug* configuration generates an unmanaged solution package. A managed solution package is generated by building the solution in *release* configuration. These settings can be overridden by specifying the SolutionPackageType property in the cdsproj file.
    > - You can set the `msbuild` configuration to **Release** to issue a production build.
        Example: `msbuild /p:configuration=Release`
    > - If you encounter an error that says "Ambiguous project name" when running the msbuild command on your solution, ensure that your solution name and project name aren't the same.

5.  The generated solution files are located in the \\bin\\debug\\ (or
    \\bin\\release) folder after the build is successful.

6.  You can use the [Microsoft Power Platform Build Tools](https://marketplace.visualstudio.com/items?itemName=microsoft-IsvExpTools.PowerApps-BuildTools)
    to automate importing the solution into a Dataverse environment;
    otherwise, you can manually [import the solution into Dataverse](/powerapps/maker/common-data-service/import-update-export-solutions) by using
    the web portal.

## Additional tasks that you can do with the framework and solutions

Below are links to additional common tasks that you can do when working with
the framework and solutions.

- [Create a solution project based on an existing solution in Dataverse](/powerapps/developer/component-framework/import-custom-controls#create-a-solution-project-based-on-an-existing-solution-in-common-data-service)

- [Create a plug-in project and add a reference to it in your solution](/powerapps/developer/component-framework/import-custom-controls#create-a-plug-in-project-and-add-a-reference-to-it-in-your-solution)

- [Remove components from a solution](/powerapps/developer/component-framework/import-custom-controls#remove-components-from-a-solution)

### See also

[Plug-ins](plugin-component.md)


[!INCLUDE[footer-include](../includes/footer-banner.md)]