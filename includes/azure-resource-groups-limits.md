---
author: tfitzmac
ms.service: azure-resource-manager
ms.topic: include
ms.date: 02/24/2020
ms.author: tomfitz
ms.openlocfilehash: 33a63280f6973d2c5e29db29f7a6f3fc68c57c77
ms.sourcegitcommit: c052c99fd0ddd1171a08077388d221482026cd58
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/04/2020
ms.locfileid: "84424715"
---
| Prostředek | Omezení |
| --- | --- |
| Prostředky na [skupinu prostředků](../articles/azure-resource-manager/management/overview.md#resource-groups) | Prostředky nejsou omezeny skupinou prostředků. Místo toho jsou omezeny typem prostředků ve skupině prostředků. Podívejte se na další řádek. |
| Prostředky na skupinu prostředků, podle typu prostředku |800 – některé typy prostředků mohou překročit limit 800. Viz [zdroje, které nejsou omezeny na 800 instancí na skupinu prostředků](../articles/azure-resource-manager/management/resources-without-resource-group-limit.md). |
| Nasazení na skupinu prostředků v historii nasazení |800<sup>1</sup> |
| Prostředky na nasazení |800 |
| Zámky pro správu na jedinečný obor |20 |
| Počet značek na prostředek nebo skupinu prostředků |50 |
| Délka klíče značky |512 |
| Délka hodnoty značky |256 |

<sup>1</sup> Od června 2020 se nasazení z historie automaticky odstraní, protože se blíží limitu. Odstranění položky z historie nasazení nemá vliv na nasazené prostředky. Další informace najdete v tématu [Automatické odstraňování z historie nasazení](../articles/azure-resource-manager/templates/deployment-history-deletions.md).

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
