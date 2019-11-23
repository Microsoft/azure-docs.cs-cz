---
title: Create an Azure Kubernetes Service cluster resource
titleSuffix: Azure Cognitive Services
description: Learn how to create an Azure Kubernetes Service (AKS) resource.
services: cognitive-services
author: IEvangelist
manager: nitinme
ms.service: cognitive-services
ms.topic: include
ms.date: 11/21/2019
ms.author: dapine
ms.openlocfilehash: 4e3102912e88ef904fed3e680f8cdd23242b1f17
ms.sourcegitcommit: f523c8a8557ade6c4db6be12d7a01e535ff32f32
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/22/2019
ms.locfileid: "74383421"
---
## <a name="create-an-azure-kubernetes-service-cluster-resource"></a>Create an Azure Kubernetes Service cluster resource

1. Go to [Azure Kubernetes Service](https://ms.portal.azure.com/#create/microsoft.aks), and select **Create**.

1. On the **Basics** tab, enter the following information:

    |Nastavení|Hodnota|
    |--|--|
    |Předplatné|Vyberte odpovídající předplatné.|
    |Skupina prostředků|Select an available resource group.|
    |Kubernetes cluster name|Enter a name (lowercase).|
    |Oblast|Select a nearby location.|
    |Kubernetes version|Whatever value is marked as **(default)** .|
    |DNS name prefix|Created automatically, but you can override.|
    |Node size|Standard DS2 v2:<br>`2 vCPUs`, `7 GB`|
    |Node count|Leave the slider at the default value.|

1. On the **Scale** tab, leave **Virtual nodes** and **VM scale sets** set to their default values.
1. On the **Authentication** tab, leave **Service principal** and **Enable RBAC** set to their default values.
1. On the **Networking** tab, enter the following selections:

    |Nastavení|Hodnota|
    |--|--|
    |Směrování aplikace HTTP|Ne|
    |Networking configuration|Úroveň Basic|

1. On the **Monitoring** tab, make sure that **Enable container monitoring** is set to **Yes**, and leave **Log Analytics workspace** as the default value.
1. On the **Tags** tab, leave the name/value pairs blank for now.
1. Select **Review and Create**.
1. After validation passes, select **Create**.

> [!NOTE]
> If validation fails, it might be because of a "Service principal" error. Go back to the **Authentication** tab and then go back to **Review + create**, where validation should run and then pass.
