---
title: 'Vzor: Operátor hodnoty v definici zásady'
description: Tento vzor zásad Azure poskytuje příklad použití operátoru hodnoty v definici zásad.
ms.date: 01/31/2020
ms.topic: sample
ms.openlocfilehash: ace7b7cd4a765cdb8c7aa764b52b180c60508ab2
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/24/2020
ms.locfileid: "77172783"
---
# <a name="azure-policy-pattern-the-value-operator"></a>Vzor zásad Azure: operátor hodnoty

Operátor [hodnoty](../concepts/definition-structure.md#value) vyhodnotí [parametry](../concepts/definition-structure.md#parameters), [podporované funkce šablony](../concepts/definition-structure.md#policy-functions)nebo literály na zadanou hodnotu pro danou [podmínku](../concepts/definition-structure.md#conditions).

> [!WARNING]
> Pokud je výsledkem _funkce šablony_ chyba, vyhodnocení zásad se nezdaří. Neúspěšné vyhodnocení je implicitní **odepřít**. Další informace naleznete v [tématu předcházení chybám šablony](../concepts/definition-structure.md#avoiding-template-failures).

## <a name="sample-policy-definition"></a>Ukázková definice zásad

Tato definice zásad přidá nebo nahradí značku zadanou v parametru **tagName** (_řetězec_) na prostředcích a dědí hodnotu **tagName** ze skupiny prostředků, ve které se prostředek nachází. Toto vyhodnocení se stane při vytvoření nebo aktualizaci prostředku. Jako [efekt změny](../concepts/effects.md#modify) může být náprava spuštěna na existujících zdrojích prostřednictvím [nápravné úlohy](../how-to/remediate-resources.md).

:::code language="json" source="~/policy-templates/patterns/pattern-value-operator.json":::

### <a name="explanation"></a>Vysvětlení

:::code language="json" source="~/policy-templates/patterns/pattern-value-operator.json" range="20-30" highlight="7,8":::

Operátor **value** se používá v rámci **bloku policyRule.if** v **rámci vlastností**. V tomto příkladu [logický operátor](../concepts/definition-structure.md#logical-operators) **allOf** se používá k udání, že oba podmíněné příkazy musí být true pro efekt, **upravit**, provést.

**hodnota** vyhodnotí výsledek funkce šablony [resourceGroup()](../../../azure-resource-manager/templates/template-functions-resource.md#resourcegroup) na podmínku **notEquals** prázdné hodnoty. Pokud existuje název značky uvedený v **tagName** v nadřazené skupině prostředků, podmíněné vyhodnotí se jako true.

## <a name="next-steps"></a>Další kroky

- Zkontrolujte další [vzory a předdefinované definice](./index.md).
- Projděte si [strukturu definic Azure Policy](../concepts/definition-structure.md).
- Projděte si [Vysvětlení efektů zásad](../concepts/effects.md).