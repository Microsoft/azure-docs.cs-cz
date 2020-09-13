---
author: tfitzmac
ms.service: azure-resource-manager
ms.topic: include
ms.date: 09/01/2020
ms.author: tomfitz
ms.openlocfilehash: 949118214851c3eceffd8c1d638a4093bdf7f366
ms.sourcegitcommit: 58d3b3314df4ba3cabd4d4a6016b22fa5264f05a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/02/2020
ms.locfileid: "89304007"
---
| Prostředek | Omezení |
| --- | --- |
| Prostředky na [skupinu prostředků](../articles/azure-resource-manager/management/overview.md#resource-groups) | Prostředky nejsou omezeny skupinou prostředků. Místo toho jsou omezeny typem prostředků ve skupině prostředků. Podívejte se na další řádek. |
| Prostředky na skupinu prostředků, podle typu prostředku |800 – některé typy prostředků mohou překročit limit 800. Viz [zdroje, které nejsou omezeny na 800 instancí na skupinu prostředků](../articles/azure-resource-manager/management/resources-without-resource-group-limit.md). |
| Nasazení na skupinu prostředků v historii nasazení |800<sup>1</sup> |
| Prostředky na nasazení |800 |
| Zámky pro správu na jedinečný [obor](../articles/azure-resource-manager/management/overview.md#understand-scope)  |20 |
| Počet značek na prostředek nebo skupinu prostředků |50 |
| Délka klíče značky |512 |
| Délka hodnoty značky |256 |

<sup>1</sup> Nasazení se automaticky odstraní z historie, protože se blížíte limitu. Odstranění položky z historie nasazení nemá vliv na nasazené prostředky. Další informace najdete v tématu [Automatické odstraňování z historie nasazení](../articles/azure-resource-manager/templates/deployment-history-deletions.md).

#### <a name="template-limits"></a>Omezení šablon

| Hodnota | Omezení |
| --- | --- |
| Parametry |256 |
| Proměnné |256 |
| Prostředky (včetně počtu kopií) |800 |
| Výstupy |64 |
| Výraz šablony |24 576 znaků |
| Prostředky v exportovaných šablonách |200 |
| Velikost šablony |4 MB |
| Velikost souboru parametrů |64 kB |

Můžete překročit několik omezení šablon pomocí vnořené šablony. Další informace najdete v tématu [použití propojených šablon při nasazení prostředků Azure](../articles/azure-resource-manager/templates/linked-templates.md). Chcete-li snížit počet parametrů, proměnných nebo výstupů, můžete zkombinovat několik hodnot do objektu. Další informace najdete v tématu [objekty jako parametry](../articles/azure-resource-manager/resource-manager-objects-as-parameters.md).
