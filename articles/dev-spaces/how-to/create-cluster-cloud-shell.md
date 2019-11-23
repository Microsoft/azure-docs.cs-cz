---
title: Create a Kubernetes cluster with Azure Dev Spaces enabled - Azure Cloud Shell
services: azure-dev-spaces
ms.date: 10/04/2018
ms.topic: conceptual
description: Learn how to quickly create a Kubernetes cluster enabled for Azure Dev Spaces directly from your browser without installing anything.
keywords: Docker, Kubernetes, Azure, AKS, Azure Kubernetes Service, containers, Helm, service mesh, service mesh routing, kubectl, k8s
ms.openlocfilehash: ac825caa7aacbc6ac21dd1f9fe7acbb9cf12e900
ms.sourcegitcommit: b77e97709663c0c9f84d95c1f0578fcfcb3b2a6c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/22/2019
ms.locfileid: "74325755"
---
# <a name="create-a-kubernetes-cluster-with-azure-dev-spaces-enabled-with-azure-cloud-shell"></a>Create a Kubernetes cluster with Azure Dev Spaces enabled with Azure Cloud Shell

You can use [Azure Cloud Shell](/azure/cloud-shell) to create an Azure Kubernetes Service cluster by using the **Try It** button from this page. If you aren't signed in, follow the prompts to sign in with an Azure account, then type the commands at the Azure Cloud Shell prompt when it appears.

## <a name="create-the-cluster"></a>Vytvoření clusteru

First, create the resource group in a [region that supports Azure Dev Spaces][supported-regions].

```azurecli-interactive
az group create --name MyResourceGroup --location <region>
```

Pomocí následujícího příkazu vytvořte cluster Kubernetes:

```azurecli-interactive
az aks create -g MyResourceGroup -n MyAKS --location <region> --disable-rbac --generate-ssh-keys
```

Vytvoření clusteru bude trvat několik minut.  When complete, the output is shown in the JSON format. Look for `provisioningState` and verify it's `Succeeded`.

## <a name="next-steps"></a>Další kroky

See [Azure Dev Spaces](/azure/dev-spaces/) for links to full tutorials.

> [!IMPORTANT]
> Many of the Azure Dev Spaces quickstarts and tutorials use the Azure Dev Spaces CLI to perform operations. You cannot install the Azure Dev Spaces CLI in the Azure Cloud Shell.


[supported-regions]: ../about.md#supported-regions-and-configurations