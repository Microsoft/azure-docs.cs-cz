---
author: tfitzmac
ms.service: azure-resource-manager
ms.topic: include
ms.date: 02/24/2020
ms.author: tomfitz
ms.openlocfilehash: c883383d3c870689bb95f808f6f60c5185c165c3
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "80334746"
---
| Prostředek | Omezení |
| --- | --- |
| Prostředky podle [skupin prostředků](../articles/azure-resource-manager/management/overview.md#resource-groups) | Prostředky nejsou omezeny skupinou prostředků. Místo toho jsou omezeny typem prostředku ve skupině prostředků. Viz další řádek. |
| Zdroje podle skupiny prostředků, podle typu prostředku |800 – Některé typy prostředků mohou překročit limit 800. Viz [Prostředky, které nejsou omezeny na 800 instancí na skupinu prostředků](../articles/azure-resource-manager/management/resources-without-resource-group-limit.md). |
| Nasazení podle skupin prostředků v historii nasazení |800<sup>1.</sup> |
| Prostředky na nasazení |800 |
| Zámky správy na jedinečný obor |20 |
| Počet značek na prostředek nebo skupinu prostředků |50 |
| Délka klíče značky |512 |
| Délka hodnoty značky |256 |

<sup>1.</sup> Pokud dosáhnete limitu 800 nasazení na skupinu prostředků, odstraňte nasazení z historie, které již nejsou potřeba. Odstranění položky z historie nasazení nemá vliv na nasazené prostředky. Další informace naleznete v [tématu Resolve error when deployment count exceeds 800](../articles/azure-resource-manager/templates/deployment-quota-exceeded.md).

#### <a name="template-limits"></a>Limity šablon

| Hodnota | Omezení |
| --- | --- |
| Parametry |256 |
| Proměnné |256 |
| Zdroje (včetně počtu kopií) |800 |
| Výstupy |64 |
| Výraz šablony |24 576 znaků |
| Zdroje v exportovaných šablonách |200 |
| Velikost šablony |4 MB |
| Velikost souboru parametru |64 kB |

Některé limity šablon můžete překročit pomocí vnořené šablony. Další informace najdete v tématu [Použití propojených šablon při nasazování prostředků Azure](../articles/azure-resource-manager/templates/linked-templates.md). Chcete-li snížit počet parametrů, proměnných nebo výstupů, můžete do objektu zkombinovat několik hodnot. Další informace naleznete v [tématu Objekty jako parametry](../articles/azure-resource-manager/resource-manager-objects-as-parameters.md).
