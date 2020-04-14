---
title: 'Vzor: Logické operátory v definici zásad'
description: Tento vzor zásad Azure obsahuje příklady použití logických operátorů v definici zásad.
ms.date: 04/15/2020
ms.topic: sample
ms.openlocfilehash: 691383b1f8ae34bbd51ce7f4f9310980e3c66537
ms.sourcegitcommit: 530e2d56fc3b91c520d3714a7fe4e8e0b75480c8
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/14/2020
ms.locfileid: "81272504"
---
# <a name="azure-policy-pattern-logical-operators"></a>Vzor zásad Azure: logické operátory

Definice zásady může obsahovat několik podmíněných příkazů. Můžete potřebovat, aby každé prohlášení bylo pravdivé, nebo potřebujete pouze některé z nich, aby byly pravdivé. Pro podporu těchto potřeb má jazyk [logické operátory](../concepts/definition-structure.md#logical-operators) pro **not**, **allOf**a **anyOf**. Jsou volitelné a mohou být vnořeny pro vytvoření složitých scénářů.

## <a name="sample-1-one-logical-operator"></a>Ukázka 1: Jeden logický operátor

Tato definice zásad vyhodnocuje účty CosmosDB a zjištová, zda jsou nakonfigurovány automatické převzetí služeb při selhání a více umístění zápisu. Pokud tomu tak není, [audit](../concepts/effects.md#audit) se aktivuje a vytvoří položku protokolu při vytvoření nebo aktualizaci nekompatibilního prostředku.

:::code language="json" source="~/policy-templates/patterns/pattern-logical-operators-1.json":::

### <a name="sample-1-explanation"></a>Ukázka 1: Vysvětlení

:::code language="json" source="~/policy-templates/patterns/pattern-logical-operators-1.json" range="6-22" highlight="3":::

**PolicyRule.if** blok používá jeden **allOf** k zajištění, že jsou splněny všechny tři podmínky.
Pouze v případě, že všechny tyto podmínky vyhodnotit na **hodnotu** true se aktivační událost auditu.

## <a name="sample-2-multiple-logical-operators"></a>Ukázka 2: Více logických operátorů

Tato definice zásad vyhodnocuje prostředky pro vzor pojmenování. Pokud se prostředek neshoduje, je [odmítnut](../concepts/effects.md#deny).

:::code language="json" source="~/policy-templates/patterns/pattern-logical-operators-2.json":::

### <a name="sample-2-explanation"></a>Ukázka 2: Vysvětlení

:::code language="json" source="~/policy-templates/patterns/pattern-logical-operators-2.json" range="7-21" highlight="2,3,9":::

Tato **policyRule.if** blok také obsahuje jeden **allOf**, ale každá podmínka je zabalena **s není** logický operátor. Podmíněné uvnitř **ne** logické ho vyhodnotí nejprve a potom vyhodnotí **není** k určení, pokud celá klauzule je true nebo false. Pokud oba **nejsou** logické operátory vyhodnotit na true, efekt zásady aktivuje.

## <a name="sample-3-combining-logical-operators"></a>Ukázka 3: Kombinování logických operátorů

Tato definice zásad vyhodnocuje účty Java Spring, aby se zjistilo, jestli trasování není povoleno nebo jestli trasování není v úspěšném stavu.

:::code language="json" source="~/policy-templates/patterns/pattern-logical-operators-3.json":::

### <a name="sample-3-explanation"></a>Ukázka 3: Vysvětlení

:::code language="json" source="~/policy-templates/patterns/pattern-logical-operators-3.json" range="6-28" highlight="3,8":::

Tato **policyRule.if** blok obsahuje **allOf** a **anyOf** logické operátory. **AnyOf** logický operátor vyhodnotí true tak dlouho, dokud jedna zahrnuta podmínka je true. Jako _typ_ je jádrem **allOf**, musí vždy vyhodnotit true. Pokud _typ_ a jedna z podmínek v **anyOf** jsou true, aktivační událost efekt zásady.

## <a name="next-steps"></a>Další kroky

- Zkontrolujte další [vzory a předdefinované definice](./index.md).
- Projděte si [strukturu definic Azure Policy](../concepts/definition-structure.md).
- Projděte si [Vysvětlení efektů zásad](../concepts/effects.md).