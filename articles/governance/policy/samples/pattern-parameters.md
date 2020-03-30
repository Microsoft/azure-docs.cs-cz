---
title: 'Vzor: Parametry v definici zásady'
description: Tento vzor zásad Azure poskytuje příklad použití parametrů v definici zásad.
ms.date: 01/31/2020
ms.topic: sample
ms.openlocfilehash: 4921bb216ef67b561bc8986cf48239e6448beafc
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/24/2020
ms.locfileid: "77172804"
---
# <a name="azure-policy-pattern-parameters"></a>Vzor zásad Azure: parametry

Definici zásad lze dynamicky snížit počet definic zásad, které jsou potřebné pomocí [parametrů](../concepts/definition-structure.md#parameters). Parametr je definován během přiřazení zásad. Parametry mají sadu předdefinovaných vlastností, které popisují parametr a způsob jeho použití.

## <a name="sample-1-string-parameters"></a>Ukázka 1: Parametry řetězce

Tato definice zásad používá dva parametry, **tagName** a **tagValue** k nastavení toho, co přiřazení zásad hledá pro prostředky. Tento formát umožňuje zásady, které mají být použity pro libovolný počet kombinací název značky a hodnoty značky, ale pouze udržovat jednu definici zásady.

:::code language="json" source="~/policy-templates/patterns/pattern-parameters-1.json":::

### <a name="sample-1-explanation"></a>Ukázka 1: Vysvětlení

:::code language="json" source="~/policy-templates/patterns/pattern-parameters-1.json" range="8-13":::

V této části definice zásadje parametr **tagName** definován jako _řetězec_ a pro jeho použití je k dispozici popis.

Parametr se pak použije v bloku **policyRule.if** k dynamickému nastavení zásady. Zde se používá k definování pole, které je vyhodnoceno, což je značka s hodnotou **tagName**.

:::code language="json" source="~/policy-templates/patterns/pattern-parameters-1.json" range="22-27" highlight="3":::

## <a name="sample-2-array-parameters"></a>Ukázka 2: Parametry pole

Tato definice zásad používá jeden parametr **listOfBandwidthinMbps**ke kontrole, zda prostředek okruhu express route nakonfiguroval nastavení šířky pásma na jednu ze schválených hodnot. Pokud se neshoduje, [je](../concepts/effects.md#deny)odmítnuto vytvoření nebo aktualizace prostředku .

:::code language="json" source="~/policy-templates/patterns/pattern-parameters-2.json":::

### <a name="sample-2-explanation"></a>Ukázka 2: Vysvětlení

:::code language="json" source="~/policy-templates/patterns/pattern-parameters-2.json" range="6-12":::

V této části definice zásady **listOfBandwidthinMbps** parametr je definován jako _pole_ a popis je k dispozici pro jeho použití. Jako _pole_má více hodnot, které odpovídají.

Parametr se pak použije v bloku **policyRule.if.** Jako parametr _pole_ musí být[použita podmínka](../concepts/definition-structure.md#conditions) _pole_
's **in** nebo **notIn.**
Zde se používá proti aliasu **serviceProvider.bandwidthInMbps** jako jedna z definovaných hodnot.

:::code language="json" source="~/policy-templates/patterns/pattern-parameters-2.json" range="21-24" highlight="3":::

## <a name="next-steps"></a>Další kroky

- Zkontrolujte další [vzory a předdefinované definice](./index.md).
- Projděte si [strukturu definic Azure Policy](../concepts/definition-structure.md).
- Projděte si [Vysvětlení efektů zásad](../concepts/effects.md).