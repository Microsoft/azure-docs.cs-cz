---
title: zahrnout soubor
description: zahrnout soubor
services: azure-resource-manager
author: tfitzmac
ms.service: cost-management-billing
ms.topic: include
ms.date: 03/26/2020
ms.author: tomfitz
ms.custom: include file
ms.openlocfilehash: 49c0a2a37fa72cd8c4750b39ff422a90721e3940
ms.sourcegitcommit: bbd66b477d0c8cb9adf967606a2df97176f6460b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/03/2020
ms.locfileid: "93233983"
---
| Prostředek | Omezení |
| --- | --- |
| Předplatná na tenanta Azure Active Directory | Unlimited |
| [Spolusprávci](../articles/cost-management-billing/manage/add-change-subscription-administrator.md) na předplatné |Unlimited |
| [Skupiny prostředků](../articles/azure-resource-manager/management/overview.md) na předplatné |980 |
| Velikost požadavku rozhraní API pro Azure Resource Manager |4 194 304 bajtů |
| Značky na předplatné<sup>1</sup> |50 |
| Jedinečné výpočty značek na předplatné<sup>1</sup> | 10 000 |
| [Nasazení na úrovni předplatného](../articles/azure-resource-manager/templates/deploy-to-subscription.md) na umístění | 800<sup>2</sup> |

<sup>1</sup> Můžete použít až 50 značek přímo k předplatnému. Předplatné ale může obsahovat neomezený počet značek, které se aplikují na skupiny prostředků a prostředky v rámci předplatného. Počet značek na prostředek nebo skupinu prostředků je omezený na 50. Správce prostředků vrátí [seznam jedinečných názvů a hodnot značek](/rest/api/resources/tags) v rámci předplatného pouze v případě, že je počet značek 10 000 nebo méně. I když číslo překračuje 10 000, můžete pořád najít prostředek podle značky.  

<sup>2</sup> . Pokud dosáhnete limitu nasazení 800, odstraňte z historie nasazení, která už nepotřebujete. Pokud chcete odstranit nasazení na úrovni předplatného, použijte [Remove-AzDeployment](/powershell/module/az.resources/Remove-AzDeployment) nebo [AZ Deployment sub Delete](/cli/azure/deployment/sub#az-deployment-sub-delete).
