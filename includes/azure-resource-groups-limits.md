---
author: rothja
ms.service: billing
ms.topic: include
ms.date: 11/09/2018
ms.author: jroth
ms.openlocfilehash: 5c2c7b621512be7b81d14b99069d52f4f3aa3f33
ms.sourcegitcommit: 8d88a025090e5087b9d0ab390b1207977ef4ff7c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/21/2018
ms.locfileid: "52279521"
---
| Prostředek | Výchozí omezení | Maximální omezení |
| --- | --- | --- |
| Prostředky na [skupiny prostředků](../articles/azure-resource-manager/resource-group-overview.md#resource-groups) (podle typu prostředku) |800 |Se liší podle typu prostředku |
| Nasazení na skupinu prostředků v historii nasazení |800 |800 |
| Prostředky na nasazení |800 |800 |
| Zámky pro správu (za jedinečný rozsah) |20 |20 |
| Počet značek (každý prostředek nebo skupinu prostředků) |15 |15 |
| Délka klíče značky |512 |512 |
| Délka hodnoty značky |256 |256 |


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

Pokud překročíte limit 800 nasazení na skupinu prostředků, odstraňte nasazení z historie, ke které jsou už je nepotřebujete. Můžete odstranit položky z historie s [odstranit nasazení skupiny pro az](/cli/azure/group/deployment#az_group_deployment_delete) pro rozhraní příkazového řádku Azure, nebo [Remove-AzureRmResourceGroupDeployment](/powershell/module/azurerm.resources/remove-azurermresourcegroupdeployment) v prostředí PowerShell. Odstranění záznamu z historie nasazení nemá vliv na nasazení prostředků. 