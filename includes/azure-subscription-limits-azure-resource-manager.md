---
title: zahrnout soubor
description: zahrnout soubor
services: azure-resource-manager
author: tfitzmac
ms.service: cost-management-billing
ms.topic: include
ms.date: 02/10/2020
ms.author: tomfitz
ms.custom: include file
ms.openlocfilehash: b7db49d09d2292c08ce33ce86b1b7f427ef75fbc
ms.sourcegitcommit: f15f548aaead27b76f64d73224e8f6a1a0fc2262
ms.translationtype: HT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/26/2020
ms.locfileid: "77618504"
---
| Prostředek | Výchozí omezení | Maximální omezení |
| --- | --- | --- |
| Předplatná na tenanta Azure Active Directory | Neomezený počet. | Neomezený počet. |
| [Spolusprávci](../articles/cost-management-billing/manage/add-change-subscription-administrator.md) na předplatné |Neomezený počet. |Neomezený počet. |
| [Skupiny prostředků](../articles/azure-resource-manager/management/overview.md) na předplatné |980 |980 |
| Velikost požadavku rozhraní API pro Azure Resource Manager |4 194 304 bajtů. |4 194 304 bajtů. |
| Značky na předplatné<sup>1</sup> |Neomezený počet. |Neomezený počet. |
| Jedinečné výpočty značek na předplatné<sup>1</sup> | 10 000 | 10 000 |
| [Nasazení na úrovni předplatného](../articles/azure-resource-manager/templates/deploy-to-subscription.md) na umístění | 800<sup>2</sup> | 800 |

<sup>1</sup> Na jedno předplatné můžete použít neomezený počet značek. Počet značek na prostředek nebo skupinu prostředků je omezený na 50. Správce prostředků vrátí [seznam jedinečných názvů a hodnot značek](/rest/api/resources/tags) v rámci předplatného pouze v případě, že je počet značek 10 000 nebo méně. I když číslo překračuje 10 000, můžete pořád najít prostředek podle značky.  

<sup>2</sup> . Pokud dosáhnete limitu nasazení 800, odstraňte nasazení z historie, která už nepotřebujete. Pokud chcete odstranit nasazení na úrovni předplatného, použijte [Remove-AzDeployment](/powershell/module/az.resources/Remove-AzDeployment) nebo [AZ Deployment Delete](/cli/azure/deployment?view=azure-cli-latest#az-deployment-delete).
