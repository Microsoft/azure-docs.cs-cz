---
title: Zásady označování zdrojů
description: Popisuje zásady Azure, které můžete přiřadit k zajištění dodržování předpisů značky.
ms.topic: conceptual
ms.date: 03/20/2020
ms.openlocfilehash: e7febe4c833cefd0ee9a5c49b4b70f4901fea8d7
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "80147015"
---
# <a name="assign-policies-for-tag-compliance"></a>Přiřazení zásad pro dodržování předpisů značky

[Zásady Azure](../../governance/policy/overview.md) se používají k vynucení pravidel a konvencí označování. Vytvořením zásady se vyhnete scénáři prostředků nasazených do vašeho předplatného, které nemají očekávané značky pro vaši organizaci. Místo ručního použití značek nebo hledání prostředků, které nejsou kompatibilní, vytvoříte zásadu, která automaticky použije potřebné značky během nasazení. Značky lze nyní také použít na existující zdroje s novým efektem [Modify](../../governance/policy/concepts/effects.md#modify) a [úkolem nápravy](../../governance/policy/how-to/remediate-resources.md). V následující části jsou uvedeny ukázkové zásady pro značky.

## <a name="policies"></a>Zásady

[!INCLUDE [Tag policies](../../../includes/azure-policy-samples-policies-tags.md)]

## <a name="next-steps"></a>Další kroky

* Další informace o označování prostředků najdete [v tématu Použití značek k uspořádání prostředků Azure](tag-resources.md).
* Značky nepodporují všechny typy prostředků. Pokud chcete zjistit, jestli můžete použít značku pro typ prostředku, přečtěte si informace [o podpoře prostředků Azure](tag-support.md).
