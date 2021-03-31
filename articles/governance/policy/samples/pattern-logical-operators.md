---
title: 'Vzor: logické operátory v definici zásady'
description: Tento vzor Azure Policy poskytuje příklady použití logických operátorů v definici zásady.
ms.date: 08/17/2020
ms.topic: sample
ms.openlocfilehash: 3f644cdbfc45b06d1ad5db8e7727c0fa69742f00
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/29/2021
ms.locfileid: "88545586"
---
# <a name="azure-policy-pattern-logical-operators"></a>Azure Policy Pattern: logické operátory

Definice zásady může obsahovat několik podmíněných příkazů. Může být nutné, aby každý příkaz byl pravdivý, nebo jen některé z nich musí být pravdivé. Pro podporu těchto potřeb má jazyk [logické operátory](../concepts/definition-structure.md#logical-operators) pro **Not**, **allOf** a **anyOf**. Jsou volitelné a můžou být vnořené pro vytváření složitých scénářů.

## <a name="sample-1-one-logical-operator"></a>Ukázka 1: jeden logický operátor

Tato definice zásad vyhodnocuje účty CosmosDB, aby bylo možné zjistit, zda jsou konfigurovány automatické převzetí služeb při selhání a více umístění pro zápis. Pokud nejsou, [audit](../concepts/effects.md#audit) triggery a vytvoří položku protokolu, pokud je prostředek nedodržující předpisy vytvořen nebo aktualizován.

:::code language="json" source="~/policy-templates/patterns/pattern-logical-operators-1.json":::

### <a name="sample-1-explanation"></a>Ukázka 1: vysvětlení

:::code language="json" source="~/policy-templates/patterns/pattern-logical-operators-1.json" range="6-22" highlight="3":::

**PolicyRule. if** blok používá jeden **allOf** k zajištění toho, aby všechny tři podmínky byly pravdivé.
Aktivační událost pro **audit** se dá aktivovat jenom v případě, že se všechny tyto podmínky vyhodnotí jako true.

## <a name="sample-2-multiple-logical-operators"></a>Ukázka 2: více logických operátorů

Tato definice zásad vyhodnocuje prostředky pro vzor pojmenování. Pokud se prostředek neshoduje, je [odepřený](../concepts/effects.md#deny).

:::code language="json" source="~/policy-templates/patterns/pattern-logical-operators-2.json":::

### <a name="sample-2-explanation"></a>Ukázka 2: vysvětlení

:::code language="json" source="~/policy-templates/patterns/pattern-logical-operators-2.json" range="7-21" highlight="2,3,9":::

Tento **policyRule. if** blok také obsahuje jeden **allOf**, ale každá podmínka je zabalena s nelogickým operátorem **Not** . Podmíněný v rámci logického operátoru **Not** vyhodnocuje jako první a pak vyhodnotí, aby se určilo **, jestli** je celá klauzule true nebo false. Pokud se **oba logické** operátory nevyhodnotí jako true, uplatní se u něj efekt zásad.

## <a name="sample-3-combining-logical-operators"></a>Ukázka 3: kombinování logických operátorů

Tato definice zásad vyhodnocuje účty na jaře Java, aby bylo možné zjistit, zda není buď trasování zapnuto, nebo pokud trasování není v úspěšném stavu.

:::code language="json" source="~/policy-templates/patterns/pattern-logical-operators-3.json":::

### <a name="sample-3-explanation"></a>Ukázka 3: vysvětlení

:::code language="json" source="~/policy-templates/patterns/pattern-logical-operators-3.json" range="6-28" highlight="3,8":::

Tento **policyRule. if** blok zahrnuje logické operátory **allOf** a **anyOf** . Logický operátor **anyOf** vyhodnocuje hodnotu true, pokud je jedna zahrnutá podmínka pravdivá. Vzhledem k tomu, že _typ_ je v jádru **allOf**, musí vždy vyhodnotit hodnotu true. Pokud je _typ_ a jedna z podmínek v **anyOf** true, uplatní se vliv na zásady.

## <a name="next-steps"></a>Další kroky

- Zkontrolujte další [vzory a předdefinované definice](./index.md).
- Projděte si [strukturu definic Azure Policy](../concepts/definition-structure.md).
- Projděte si [Vysvětlení efektů zásad](../concepts/effects.md).