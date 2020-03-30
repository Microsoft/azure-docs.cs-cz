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
ms.openlocfilehash: 94fd7e692be31ba247e3342246d3940ed08ef9b7
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "80334988"
---
| Prostředek | Omezení |
| --- | --- |
| Předplatná podle klienta Služby Azure Active Directory | Neomezené. |
| [Spolusprávci](../articles/cost-management-billing/manage/add-change-subscription-administrator.md) na předplatné |Neomezené. |
| [Skupiny prostředků](../articles/azure-resource-manager/management/overview.md) na předplatné |980 |
| Velikost požadavku na rozhraní API Správce prostředků Azure |4 194 304 bajtů. |
| Značky na předplatné<sup>1</sup> |50 |
| Jedinečné výpočty značek na předplatné<sup>1</sup> | 10 000 |
| [Nasazení na úrovni předplatného](../articles/azure-resource-manager/templates/deploy-to-subscription.md) pro umístění | 800<sup>2</sup> |

<sup>1.</sup> Můžete použít až 50 značek přímo na předplatné. Předplatné však může obsahovat neomezený počet značek, které jsou použity pro skupiny prostředků a prostředky v rámci předplatného. Počet značek na prostředek nebo skupinu prostředků je omezen na 50. Správce prostředků vrátí [seznam jedinečný název značky a hodnoty](/rest/api/resources/tags) v předplatném pouze v případě, že počet značek je 10 000 nebo méně. Stále můžete najít zdroj podle značky, když číslo překročí 10 000.  

<sup>2.</sup> Pokud dosáhnete limitu 800 nasazení, odstraňte nasazení z historie, které již nejsou potřeba. Chcete-li odstranit nasazení na úrovni předplatného, použijte [použít odebrat-AzDeployment](/powershell/module/az.resources/Remove-AzDeployment) nebo [az nasazení sub delete](/cli/azure/deployment/sub?view=azure-cli-latest#az-deployment-sub-delete).
