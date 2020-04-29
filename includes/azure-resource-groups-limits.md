---
author: tfitzmac
ms.service: azure-resource-manager
ms.topic: include
ms.date: 02/24/2020
ms.author: tomfitz
ms.openlocfilehash: c883383d3c870689bb95f808f6f60c5185c165c3
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/28/2020
ms.locfileid: "80334746"
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

<sup>1</sup> Pokud dosáhnete limitu nasazení 800 na jednu skupinu prostředků, odstraňte nasazení z historie, která už nepotřebujete. Odstranění položky z historie nasazení nemá vliv na nasazené prostředky. Další informace najdete v tématu [řešení chyb při překročení počtu nasazení na 800](../articles/azure-resource-manager/templates/deployment-quota-exceeded.md).

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
