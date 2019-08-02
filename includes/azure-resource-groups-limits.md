---
author: tfitzmac
ms.service: azure-resource-manager
ms.topic: include
ms.date: 07/30/2019
ms.author: tomfitz
ms.openlocfilehash: f027f5b295d75c105e81360a10718239e5b08e4d
ms.sourcegitcommit: 800f961318021ce920ecd423ff427e69cbe43a54
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/31/2019
ms.locfileid: "68698266"
---
| Resource | Výchozí omezení | Maximální omezení |
| --- | --- | --- |
| Prostředky na [skupinu prostředků](../articles/azure-resource-manager/resource-group-overview.md#resource-groups), podle typu prostředku |800 |Některé typy prostředků mohou překročit limit 800. Viz [zdroje, které nejsou omezeny na 800 instancí na skupinu prostředků](../articles/azure-resource-manager/resources-without-rg-limit.md). |
| Nasazení na skupinu prostředků v historii nasazení |800<sup>1</sup> |800 |
| Prostředky na nasazení |800 |800 |
| Zámky pro správu na jedinečný obor |20 |20 |
| Počet značek na prostředek nebo skupinu prostředků |15 |15 |
| Délka klíče značky |512 |512 |
| Délka hodnoty značky |256 |256 |

<sup>1</sup> Pokud dosáhnete limitu nasazení 800 na jednu skupinu prostředků, odstraňte nasazení z historie, která už nepotřebujete. Odstranění položky z historie nasazení nemá vliv na nasazené prostředky. Položky z historie můžete odstranit pomocí [AZ Group Deployment Delete](/cli/azure/group/deployment) pro Azure CLI nebo rutinu [Remove-AzResourceGroupDeployment](/powershell/module/az.resources/remove-azresourcegroupdeployment) v prostředí PowerShell.  Skript PowerShellu, který automatizuje odstraňování nasazení ve scénáři průběžná integrace a průběžného doručování (CI/CD), najdete v tématu [Remove-Deployments. ps1](https://gist.github.com/bmoore-msft/ed33fb940dafb09380174b7fca57651f).

#### <a name="template-limits"></a>Omezení šablon

| Hodnota | Výchozí omezení | Maximální omezení |
| --- | --- | --- |
| Parametry |256 |256 |
| Proměnné |256 |256 |
| Prostředky (včetně počtu kopií) |800 |800 |
| Výstupy |64 |64 |
| Výraz šablony |24 576 znaků |24 576 znaků |
| Prostředky v exportovaných šablonách |200 |200 | 
| Velikost šablony |4 MB |4 MB |
| Velikost souboru parametrů |64 kB |64 kB |

Můžete překročit několik omezení šablon pomocí vnořené šablony. Další informace najdete v tématu [použití propojených šablon při nasazení prostředků Azure](../articles/azure-resource-manager/resource-group-linked-templates.md). Chcete-li snížit počet parametrů, proměnných nebo výstupů, můžete zkombinovat několik hodnot do objektu. Další informace najdete v tématu [objekty jako parametry](../articles/azure-resource-manager/resource-manager-objects-as-parameters.md).
