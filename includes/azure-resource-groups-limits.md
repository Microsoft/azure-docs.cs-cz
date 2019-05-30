---
author: tfitzmac
ms.service: azure-resource-manager
ms.topic: include
ms.date: 04/19/2019
ms.author: tomfitz
ms.openlocfilehash: 8bd16378e9c82a011309c12cf241b59d03405a77
ms.sourcegitcommit: 778e7376853b69bbd5455ad260d2dc17109d05c1
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/23/2019
ms.locfileid: "66238360"
---
| Resource | Výchozí omezení | Maximální omezení |
| --- | --- | --- |
| Prostředky na [skupiny prostředků](../articles/azure-resource-manager/resource-group-overview.md#resource-groups), podle typu prostředku |800 |Se liší podle typu prostředku |
| Nasazení na skupinu prostředků v historii nasazení |800<sup>1</sup> |800 |
| Prostředky na nasazení |800 |800 |
| Zámky pro správu za jedinečný obor |20 |20 |
| Počet značek na prostředek nebo skupinu prostředků |15 |15 |
| Délka klíče značky |512 |512 |
| Délka hodnoty značky |256 |256 |

<sup>1</sup>Pokud překročíte limit 800 nasazení na skupinu prostředků, odstranit nasazení z historie, ke které jsou už je nepotřebujete. Odstranění záznamu z historie nasazení nebude mít vliv na nasazených prostředků. Můžete odstranit položky z historie s [odstranit nasazení skupiny pro az](/cli/azure/group/deployment) pro rozhraní příkazového řádku Azure, nebo [odebrat AzResourceGroupDeployment](/powershell/module/az.resources/remove-azresourcegroupdeployment) v prostředí PowerShell.  Powershellu skript, který automatizuje odstranění nasazení v průběžné integrace a průběžné doručování (CI/CD) scénáře, naleznete v tématu [odebrat deployments.ps1](https://gist.github.com/bmoore-msft/ed33fb940dafb09380174b7fca57651f).

#### <a name="template-limits"></a>Omezení šablony

| Hodnota | Výchozí omezení | Maximální omezení |
| --- | --- | --- |
| Parametry |256 |256 |
| Proměnné |256 |256 |
| Prostředky (včetně počet kopií) |800 |800 |
| Výstupy |64 |64 |
| Výraz šablony |24,576 znaků |24,576 znaků |
| Prostředky v exportované šablony |200 |200 | 
| Velikost šablony |1 MB |1 MB |
| Velikost souboru parametru |64 kB |64 kB |

Některá omezení šablony mohou překročit pomocí vnořené šablony. Další informace najdete v tématu [použití propojených šablon při nasazování prostředků Azure](../articles/azure-resource-manager/resource-group-linked-templates.md). Pokud chcete snížit počet parametrů, proměnných nebo výstupů, můžete kombinovat několik hodnot do objektu. Další informace najdete v tématu [objektů jako parametry](../articles/azure-resource-manager/resource-manager-objects-as-parameters.md).
