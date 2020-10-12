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
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "80334988"
---
| Prostředek | Omezení |
| --- | --- |
| Předplatná na tenanta Azure Active Directory | Počet. |
| [Spolusprávci](../articles/cost-management-billing/manage/add-change-subscription-administrator.md) na předplatné |Počet. |
| [Skupiny prostředků](../articles/azure-resource-manager/management/overview.md) na předplatné |980 |
| Velikost požadavku rozhraní API pro Azure Resource Manager |4 194 304 bajtů. |
| Značky na předplatné<sup>1</sup> |50 |
| Jedinečné výpočty značek na předplatné<sup>1</sup> | 10 000 |
| [Nasazení na úrovni předplatného](../articles/azure-resource-manager/templates/deploy-to-subscription.md) na umístění | 800<sup>2</sup> |

<sup>1</sup> Můžete použít až 50 značek přímo k předplatnému. Předplatné ale může obsahovat neomezený počet značek, které se aplikují na skupiny prostředků a prostředky v rámci předplatného. Počet značek na prostředek nebo skupinu prostředků je omezený na 50. Správce prostředků vrátí [seznam jedinečných názvů a hodnot značek](/rest/api/resources/tags) v rámci předplatného pouze v případě, že je počet značek 10 000 nebo méně. I když číslo překračuje 10 000, můžete pořád najít prostředek podle značky.  

<sup>2</sup> . Pokud dosáhnete limitu nasazení 800, odstraňte nasazení z historie, která už nepotřebujete. Pokud chcete odstranit nasazení na úrovni předplatného, použijte [Remove-AzDeployment](/powershell/module/az.resources/Remove-AzDeployment) nebo [AZ Deployment sub Delete](/cli/azure/deployment/sub?view=azure-cli-latest#az-deployment-sub-delete).
