---
title: Zásady pro označování prostředků
description: Popisuje zásady Azure, které můžete přiřadit k zajištění shody značek.
ms.topic: conceptual
ms.date: 03/20/2020
ms.openlocfilehash: c6867bc01306ac3c08a9797ece0567a45e060af2
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2021
ms.locfileid: "89293727"
---
# <a name="assign-policies-for-tag-compliance"></a>Přiřazení zásad pro požadavky na značky

Pomocí [Azure Policy](../../governance/policy/overview.md) vynutili pravidla a konvence označování. Vytvořením zásady se vyhnete scénáři nasazení prostředků ve vašem předplatném, které nemají očekávané značky pro vaši organizaci. Místo ručního použití značek nebo hledání prostředků, které nedodržují předpisy, vytvoříte zásadu, která při nasazení automaticky použije potřebné značky. Značky se teď dají použít u existujících prostředků s novým efektem [změny](../../governance/policy/concepts/effects.md#modify) a [úlohou nápravy](../../governance/policy/how-to/remediate-resources.md). V následující části jsou uvedeny příklady zásad pro značky.

## <a name="policies"></a>Zásady

[!INCLUDE [Tag policies](../../../includes/policy/reference/bycat/policies-tags.md)]

## <a name="next-steps"></a>Další kroky

* Další informace o označování prostředků najdete v tématu [použití značek k uspořádání prostředků Azure](tag-resources.md).
* Ne všechny typy prostředků podporují značky. Pokud chcete zjistit, jestli můžete pro typ prostředku použít značku, přečtěte si téma [Podpora značek pro prostředky Azure](tag-support.md).
