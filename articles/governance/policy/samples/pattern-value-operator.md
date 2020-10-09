---
title: 'Vzor: operátor hodnoty v definici zásady'
description: Tento model Azure Policy poskytuje příklad použití operátoru value v definici zásady.
ms.date: 06/29/2020
ms.topic: sample
ms.openlocfilehash: e246e3a5e2517fa80626081227070bcb2f967784
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "85565671"
---
# <a name="azure-policy-pattern-the-value-operator"></a>Vzor Azure Policy: operátor hodnoty

Operátor [hodnoty](../concepts/definition-structure.md#value) vyhodnotí [parametry](../concepts/definition-structure.md#parameters), [podporované funkce šablony](../concepts/definition-structure.md#policy-functions)nebo literály na zadanou hodnotu pro danou [podmínku](../concepts/definition-structure.md#conditions).

> [!WARNING]
> Pokud je výsledkem _funkce šablony_ chyba, vyhodnocení zásad se nezdařilo. Neúspěšné vyhodnocení je implicitní **odmítnutí**. Další informace najdete v tématu [předcházení chybám šablon](../concepts/definition-structure.md#avoiding-template-failures).

## <a name="sample-policy-definition"></a>Definice ukázkové zásady

Tato definice zásady přidává nebo nahrazuje značku zadanou v parametru **TagName** (_String_) u prostředků a dědí hodnotu pro **TagName** ze skupiny prostředků, ve které je prostředek. Toto vyhodnocení nastane, když se prostředek vytvoří nebo aktualizuje. V důsledku [úprav](../concepts/effects.md#modify) se může náprava spustit u existujících prostředků prostřednictvím [nápravné úlohy](../how-to/remediate-resources.md).

:::code language="json" source="~/policy-templates/patterns/pattern-value-operator.json":::

### <a name="explanation"></a>Vysvětlení

:::code language="json" source="~/policy-templates/patterns/pattern-value-operator.json" range="20-30" highlight="7,8":::

Operátor **hodnoty** se používá v rámci **policyRule. if** bloku v rámci **vlastností**. V tomto příkladu je [logický operátor](../concepts/definition-structure.md#logical-operators) **allOf** použit k zaznamenání, že oba podmíněné příkazy musí být pravdivé pro daný efekt, aby bylo provedeno **Úpravy**.

**hodnota** vyhodnotí výsledek funkce šablony [Resource ()](../../../azure-resource-manager/templates/template-functions-resource.md#resourcegroup) na podmínku **notEquals** prázdné hodnoty. Pokud existuje název značky, který jste zadali na webu **TagName** v nadřazené skupině prostředků, podmíněný výsledek se vyhodnotí jako true.

## <a name="next-steps"></a>Další kroky

- Zkontrolujte další [vzory a předdefinované definice](./index.md).
- Projděte si [strukturu definic Azure Policy](../concepts/definition-structure.md).
- Projděte si [Vysvětlení efektů zásad](../concepts/effects.md).