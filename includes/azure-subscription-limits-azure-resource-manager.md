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
ms.openlocfilehash: 32d39493e526a4b95369e2998d3f9ade9f2964dc
ms.sourcegitcommit: f718b98dfe37fc6599d3a2de3d70c168e29d5156
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/11/2020
ms.locfileid: "77133723"
---
| Prostředek | Výchozí omezení | Maximální omezení |
| --- | --- | --- |
| [Skupiny prostředků](../articles/azure-resource-manager/management/overview.md) na předplatné |980 |980 |
| Velikost požadavku rozhraní API pro Azure Resource Manager |4 194 304 bajtů. |4 194 304 bajtů. |
| Značky na předplatné<sup>1</sup> |Neomezený počet. |Neomezený počet. |
| Jedinečné výpočty značek na předplatné<sup>1</sup> | 10 000 | 10 000 |
| [Nasazení na úrovni předplatného](../articles/azure-resource-manager/templates/deploy-to-subscription.md) na umístění | 800<sup>2</sup> | 800 |
| Předplatná na tenanta Azure Active Directory | Neomezený počet. | Neomezený počet. |
| [Spolusprávci](../articles/cost-management-billing/manage/add-change-subscription-administrator.md) na předplatné |Neomezený počet. |Neomezený počet. |

<sup>1</sup> Na jedno předplatné můžete použít neomezený počet značek. Počet značek na prostředek nebo skupinu prostředků je omezený na 50. Správce prostředků vrátí [seznam jedinečných názvů a hodnot značek](/rest/api/resources/tags) v rámci předplatného pouze v případě, že je počet značek 10 000 nebo méně. I když číslo překračuje 10 000, můžete pořád najít prostředek podle značky.  

<sup>2</sup> . Pokud dosáhnete limitu nasazení 800, odstraňte nasazení z historie, která už nepotřebujete. Pokud chcete odstranit nasazení na úrovni předplatného, použijte [Remove-AzDeployment](/powershell/module/az.resources/Remove-AzDeployment) nebo [AZ Deployment Delete](/cli/azure/deployment?view=azure-cli-latest#az-deployment-delete).
