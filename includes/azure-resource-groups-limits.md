---
author: tfitzmac
ms.service: azure-resource-manager
ms.topic: include
ms.date: 08/19/2019
ms.author: tomfitz
ms.openlocfilehash: b4338560d515289d3e9aedfa716ec7c3ace51131
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/25/2019
ms.locfileid: "75392256"
---
| Prostředek | Výchozí omezení | Maximální omezení |
| --- | --- | --- |
| Prostředky na [skupinu prostředků](../articles/azure-resource-manager/management/overview.md#resource-groups), podle typu prostředku |800 |Některé typy prostředků mohou překročit limit 800. Viz [zdroje, které nejsou omezeny na 800 instancí na skupinu prostředků](../articles/azure-resource-manager/management/resources-without-resource-group-limit.md). |
| Nasazení na skupinu prostředků v historii nasazení |800<sup>1</sup> |800 |
| Prostředky na nasazení |800 |800 |
| Zámky pro správu na jedinečný obor |20 |20 |
| Počet značek na prostředek nebo skupinu prostředků |50 |50 |
| Délka klíče značky |512 |512 |
| Délka hodnoty značky |256 |256 |

<sup>1</sup> Pokud dosáhnete limitu nasazení 800 na jednu skupinu prostředků, odstraňte nasazení z historie, která už nepotřebujete. Odstranění položky z historie nasazení nemá vliv na nasazené prostředky. Další informace najdete v tématu [řešení chyb při překročení počtu nasazení na 800](../articles/azure-resource-manager/templates/deployment-quota-exceeded.md).

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

Můžete překročit několik omezení šablon pomocí vnořené šablony. Další informace najdete v tématu [použití propojených šablon při nasazení prostředků Azure](../articles/azure-resource-manager/templates/linked-templates.md). Chcete-li snížit počet parametrů, proměnných nebo výstupů, můžete zkombinovat několik hodnot do objektu. Další informace najdete v tématu [objekty jako parametry](../articles/azure-resource-manager/resource-manager-objects-as-parameters.md).
