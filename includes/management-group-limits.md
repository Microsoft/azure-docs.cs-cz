---
title: zahrnout soubor
description: zahrnout soubor
author: tfitzmac
ms.service: governance
ms.topic: include
ms.date: 03/26/2020
ms.author: tomfitz
ms.custom: include file
ms.openlocfilehash: 9b2bf8763d175bca028be1fcceda921e87eb5b23
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "80334785"
---
| Prostředek | Omezení |
| --- | --- |
| Skupiny pro správu podle adresáře | 10 000 |
| Předplatná pro skupinu pro správu | Neomezené. |
| Úrovně hierarchie skupin pro správu | Kořenová úroveň plus 6 úrovní<sup>1</sup> |
| Přímá nadřazená skupina pro správu podle skupiny pro správu | Jeden |
| [Nasazení na úrovni skupiny pro správu](../articles/azure-resource-manager/templates/deploy-to-management-group.md) podle lokality | 800<sup>2</sup> |

<sup>1.</sup> 6 úrovní nezahrnuje úroveň předplatného.

<sup>2.</sup> Pokud dosáhnete limitu 800 nasazení, odstraňte nasazení z historie, které již nejsou potřeba. Chcete-li odstranit nasazení na úrovni skupiny pro správu, použijte [příkaz Remove-AzManagementGroupDeployment](/powershell/module/az.resources/Remove-AzManagementGroupDeployment) nebo [az deployment mg delete](/cli/azure/deployment/mg?view=azure-cli-latest#az-deployment-mg-delete).
