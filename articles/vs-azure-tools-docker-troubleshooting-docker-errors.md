---
title: Troubleshooting Docker client errors on Windows by using Visual Studio | Microsoft Docs
description: Troubleshoot problems you encounter when using Visual Studio to create and deploy web apps to Docker on Windows by using Visual Studio 2017.
services: azure-container-service
documentationcenter: na
author: devinb
manager: douge
editor: ''

ms.assetid: 346f70b9-7b52-4688-a8e8-8f53869618d3
ms.service: multiple
ms.devlang: dotnet
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: multiple
ms.date: 10/13/2017
ms.author: devinb

---

# Troubleshoot Visual Studio 2017 development with Docker

When you're working with Visual Studio Tools for Docker, you may encounter issues while building or debugging your application. Below are some common troubleshooting steps.

## Windows container compatibility issues
When building or running a Windows container image, you must ensure that the version of Windows specified in the Dockerfile for use  by the container is compatible with the version of Windows used on the host. For example, hosts running Windows Server version 1803 can only run Windows Server version 1803 containers. Please reference [Windows Container Version Compatibility](https://docs.microsoft.com/en-us/virtualization/windowscontainers/deploy-containers/version-compatibility) for more details.

By default, when you use the Visual Studio Tools for Docker to create a Dockerfile, the Dockerfile will use a tag that specifies the same build of Windows as is running on the development workstation. For example, if your development workstation is running Windows 10 2016 (build 16299), then the Dockerfile will use container images for Windows Server 2016 (build 16299).

This ensures that your containers will always build and run properly on your development workstation; however, this Dockerfile may not be compatible with other machines that need to run your container images, including your teammates' development workstations, your build servers, or your production servers. In the above example, if your build server is running Windows Server version 1709, and your production server is running Windows Server version 1803, then you will experience errors when building your container images on your build server, or when attempting to run them in production, because those versions of Windows cannot run Windows Server 2016 containers.

To resolve this, you can update your Dockerfile to specify a different base image tag in the FROM statement. You can find available base image tags in the following repositories:
* The [microsoft/aspnetcore repository](https://hub.docker.com/r/microsoft/aspnetcore/), for ASP.NET Core web applications
* The [microsoft/aspnet repository](https://hub.docker.com/r/microsoft/aspnet/), for ASP.NET web applications leveraging the .NET Framework
* The [microsoft/dotnet repository](https://hub.docker.com/r/microsoft/dotnet/), for .NET Core console applications
* The [microsoft/dotnet-framework repository](https://hub.docker.com/r/microsoft/dotnet-framework/), for .NET Framework console applications

### Container compatibility for Azure services
* **Azure Container Instances** requires Windows Server 2016 container images.
* The private preview for **Azure App Service Windows** requires Windows Server 2016 container images.

## Volume sharing is not enabled. Enable volume sharing in the Docker CE for Windows settings  (Linux containers only)

To resolve this issue:

1. Right-click **Docker for Windows** in the notification area, and then select **Settings**.
1. Select **Shared Drives** and share the system drive along with the drive where the project resides.

> [!NOTE]
> If files appear shared, you may still need to click the "Reset credentials..." link at the bottom of the dialog in order to re-enable volume sharing.

![shared drives](./media/vs-azure-tools-docker-troubleshooting-docker-errors/shareddrives.png)

## Unable to start debugging

One reason could be related to having stale debugging components in your user profile folder. Execute the following commands to remove these folders so that the latest debugging components are downloaded on the next debug session.

- del %userprofile%\vsdbg
- del %userprofile%\onecoremsvsmon

## Errors specific to networking when debugging your application

Try executing the script downloadable from [Cleanup Container Host Networking](https://github.com/MicrosoftDocs/Virtualization-Documentation/tree/master/windows-server-container-tools/CleanupContainerHostNetworking),
which will refresh the network-related components on your host machine.


## Microsoft/DockerTools GitHub repo

For any other issues you encounter, see  [Microsoft/DockerTools](https://github.com/microsoft/dockertools/issues) issues.
