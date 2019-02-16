---
author: rothja
ms.service: billing
ms.topic: include
ms.date: 11/09/2018
ms.author: jroth
ms.openlocfilehash: cdebdf7258e99457191754cd73513fdb3744f8e9
ms.sourcegitcommit: f7be3cff2cca149e57aa967e5310eeb0b51f7c77
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/15/2019
ms.locfileid: "56323452"
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

Pokud překročíte limit 800 nasazení na skupinu prostředků, odstraňte nasazení z historie, ke které jsou už je nepotřebujete. Můžete odstranit položky z historie s [odstranit nasazení skupiny pro az](/cli/azure/group/deployment) pro rozhraní příkazového řádku Azure, nebo [Remove-AzureRmResourceGroupDeployment](/powershell/module/azurerm.resources/remove-azurermresourcegroupdeployment) v prostředí PowerShell. Odstranění záznamu z historie nasazení nemá vliv na nasazení prostředků. 