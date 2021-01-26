---
title: zahrnout soubor
description: zahrnout soubor
services: azure-resource-manager
author: tfitzmac
ms.service: cost-management-billing
ms.topic: include
ms.date: 01/25/2021
ms.author: tomfitz
ms.custom: include file
ms.openlocfilehash: ad0c532c2ac80fd8a3bb3e68431ff7fc274d73e0
ms.sourcegitcommit: a055089dd6195fde2555b27a84ae052b668a18c7
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/26/2021
ms.locfileid: "98792305"
---
| Prostředek | Omezení |
| --- | --- |
| Předplatná na tenanta Azure Active Directory | Unlimited |
| [Spolusprávci](../articles/cost-management-billing/manage/add-change-subscription-administrator.md) na předplatné |Unlimited |
| [Skupiny prostředků](../articles/azure-resource-manager/management/overview.md) na předplatné |980 |
| Velikost požadavku rozhraní API pro Azure Resource Manager |4 194 304 bajtů |
| Značky na předplatné<sup>1</sup> |50 |
| Jedinečné výpočty značek na předplatné<sup>1</sup> | 80,000 |
| [Nasazení na úrovni předplatného](../articles/azure-resource-manager/templates/deploy-to-subscription.md) na umístění | 800<sup>2</sup> |

<sup>1</sup> Můžete použít až 50 značek přímo k předplatnému. Předplatné ale může obsahovat neomezený počet značek, které se aplikují na skupiny prostředků a prostředky v rámci předplatného. Počet značek na prostředek nebo skupinu prostředků je omezený na 50. Správce prostředků vrátí [seznam jedinečných názvů a hodnot značek](/rest/api/resources/tags) v rámci předplatného pouze v případě, že je počet značek 80 000 nebo méně. I když číslo překračuje 80 000, můžete pořád najít prostředek podle značky.

<sup>2</sup> . Pokud dosáhnete limitu nasazení 800, odstraňte z historie nasazení, která už nepotřebujete. Pokud chcete odstranit nasazení na úrovni předplatného, použijte [Remove-AzDeployment](/powershell/module/az.resources/Remove-AzDeployment) nebo [AZ Deployment sub Delete](/cli/azure/deployment/sub#az-deployment-sub-delete).
