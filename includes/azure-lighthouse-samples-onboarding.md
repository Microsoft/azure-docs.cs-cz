---
title: zahrnout soubor
description: zahrnout soubor
services: lighthouse
author: JnHs
ms.service: lighthouse
ms.topic: include
ms.date: 07/07/2020
ms.author: jenhayes
ms.custom: include file
ms.openlocfilehash: d80786bbdfc4f53254dc98d79ac3badcf5dd3b24
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: HT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "86111645"
---
Poskytujeme šablony zaměřené na konkrétní scénáře onboardingu. Zvolte si možnost, která vám nejlépe vyhovuje, a nezapomeňte upravit soubor parametrů tak, aby odpovídal vašemu prostředí. Další informace o tom, jak používat tyto soubory ve vašem nasazení, najdete v tématu [Onboarding zákazníků do služby Azure Lighthouse](../articles/lighthouse/how-to/onboard-customer.md).

| **Šablona** | **Popis** |
|---------|---------|
| [delegated-resource-management](https://github.com/Azure/Azure-Lighthouse-samples/tree/master/templates/delegated-resource-management) | Nasaďte předplatné zákazníka do služby Azure Lighthouse. Pro každé předplatné se musí provést samostatné nasazení. |
| [rg-delegated-resource-management](https://github.com/Azure/Azure-Lighthouse-samples/tree/master/templates/rg-delegated-resource-management) | Nasaďte jednu nebo několik zákaznických skupin prostředků do služby Azure Lighthouse. K nasazení jedné skupiny prostředků použijte **rgDelegatedResourceManagement**, k nasazení několika skupin prostředků ve stejném předplatném použijte **multipleRgDelegatedResourceManagement**. |
| [marketplace-delegated-resource-management](https://github.com/Azure/Azure-Lighthouse-samples/tree/master/templates/marketplace-delegated-resource-management) | Pokud jste [publikovali nabídku spravovaných služeb na Azure Marketplace ](../articles/lighthouse/how-to/publish-managed-services-offers.md), můžete tuto šablonu volitelně použít k nasazení prostředků pro zákazníky, kteří tuto nabídku využili. Hodnoty pro marketplace v souboru parametrů se musí shodovat s hodnotami, které jste použili při publikování vaší nabídky. |

Obvykle je potřeba pro každé nasazované předplatné samostatné nasazení, ale můžete také nasadit šablony v rámci více předplatných.

| **Šablona** | **Popis** |
|---------|---------|
| [cross-subscription-deployment](https://github.com/Azure/Azure-Lighthouse-samples/tree/master/templates/cross-subscription-deployment) | Nasazení šablon Azure Resource Manageru napříč několika předplatnými |
