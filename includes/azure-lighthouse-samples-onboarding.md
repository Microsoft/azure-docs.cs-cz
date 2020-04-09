---
title: zahrnout soubor
description: zahrnout soubor
services: lighthouse
author: JnHs
ms.service: lighthouse
ms.topic: include
ms.date: 12/19/2019
ms.author: jenhayes
ms.custom: include file
ms.openlocfilehash: 6b873508fe29ed0816a8b64b26cc5522ed23a8d6
ms.sourcegitcommit: 7d8158fcdcc25107dfda98a355bf4ee6343c0f5c
ms.translationtype: HT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/09/2020
ms.locfileid: "80986812"
---
Poskytujeme šablony zaměřené na konkrétní scénáře onboardingu. Zvolte si možnost, která vám nejlépe vyhovuje, a nezapomeňte upravit soubor parametrů tak, aby odpovídal vašemu prostředí. Další informace o tom, jak používat tyto soubory ve vašem nasazení, najdete v tématu [Onboard zákazníka do delegované správy prostředků Azure ](../articles/lighthouse/how-to/onboard-customer.md).

| **Šablona** | **Popis** |
|---------|---------|
| [delegated-resource-management](https://github.com/Azure/Azure-Lighthouse-samples/tree/master/templates/delegated-resource-management) | Nasazení předplatných zákazníků do správy delegovaných prostředků Azure Pro každé předplatné se musí provést samostatné nasazení. |
| [rg-delegated-resource-management](https://github.com/Azure/Azure-Lighthouse-samples/tree/master/templates/rg-delegated-resource-management) | Nasaďte jednu nebo několik zákaznických skupin prostředků do správy delegovaných prostředků Azure. K nasazení jedné skupiny prostředků použijte **rgDelegatedResourceManagement**, k nasazení několika skupin prostředků ve stejném předplatném použijte **multipleRgDelegatedResourceManagement**. |
| [marketplace-delegated-resource-management](https://github.com/Azure/Azure-Lighthouse-samples/tree/master/templates/marketplace-delegated-resource-management) | Pokud jste [publikovali nabídku spravovaných služeb na Azure Marketplace ](../articles/lighthouse/how-to/publish-managed-services-offers.md), můžete tuto šablonu volitelně použít k nasazení prostředků pro zákazníky, kteří tuto nabídku využili. Hodnoty pro marketplace v souboru parametrů se musí shodovat s hodnotami, které jste použili při publikování vaší nabídky. |

Obvykle je potřeba pro každé nasazované předplatné samostatné nasazení, ale můžete také nasadit šablony v rámci více předplatných.

| **Šablona** | **Popis** |
|---------|---------|
| [cross-subscription-deployment](https://github.com/Azure/Azure-Lighthouse-samples/tree/master/templates/cross-subscription-deployment) | Nasazení šablon Azure Resource Manageru napříč několika předplatnými |
