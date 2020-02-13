---
title: 'Vzor: parametry v definici zásady'
description: Tento model Azure Policy poskytuje příklad použití parametrů v definici zásady.
ms.date: 01/31/2020
ms.topic: sample
ms.openlocfilehash: 4921bb216ef67b561bc8986cf48239e6448beafc
ms.sourcegitcommit: bdf31d87bddd04382effbc36e0c465235d7a2947
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/12/2020
ms.locfileid: "77172804"
---
# <a name="azure-policy-pattern-parameters"></a>Azure Policy – vzor: parametry

Definici zásady je možné nastavit jako dynamickou a snížit tak počet definic zásad, které jsou potřeba pomocí [parametrů](../concepts/definition-structure.md#parameters). Parametr je definován během přiřazování zásad. Parametry mají sadu předdefinovaných vlastností, které popisují parametr a způsob použití.

## <a name="sample-1-string-parameters"></a>Ukázka 1: parametry řetězce

Tato definice zásady používá dva parametry, **TagName** a **tagValue** k nastavení toho, co přiřazení zásad hledá u prostředků. Tento formát umožňuje použít zásady pro libovolný počet kombinací názvu značky a hodnoty značek, ale udržuje jenom jednu definici zásady.

:::code language="json" source="~/policy-templates/patterns/pattern-parameters-1.json":::

### <a name="sample-1-explanation"></a>Ukázka 1: vysvětlení

:::code language="json" source="~/policy-templates/patterns/pattern-parameters-1.json" range="8-13":::

V této části definice zásady je parametr **TagName** definovaný jako _řetězec_ a pro jeho použití je k dispozici popis.

Parametr se pak použije v bloku **policyRule. if** , aby se zásada využívala jako dynamická. Zde se používá k definování vyhodnoceného pole, což je značka s hodnotou **TagName**.

:::code language="json" source="~/policy-templates/patterns/pattern-parameters-1.json" range="22-27" highlight="3":::

## <a name="sample-2-array-parameters"></a>Ukázka 2: parametry pole

Tato definice zásady používá jeden parametr **listOfBandwidthinMbps**a kontroluje, jestli prostředek okruhu Express Route nakonfiguroval nastavení šířky pásma na jednu ze schválených hodnot. Pokud se neshoduje, je vytvoření nebo aktualizace prostředku [odepřena](../concepts/effects.md#deny).

:::code language="json" source="~/policy-templates/patterns/pattern-parameters-2.json":::

### <a name="sample-2-explanation"></a>Ukázka 2: vysvětlení

:::code language="json" source="~/policy-templates/patterns/pattern-parameters-2.json" range="6-12":::

V této části definice zásad je parametr **listOfBandwidthinMbps** definován jako _pole_ a popis je k dispozici pro jeho použití. Jako _pole_má více hodnot, které mají být shodné.

Parametr je pak použit v bloku **policyRule. if** . Jako parametr _pole_ **se musí** použít _pole_
[Podmínka](../concepts/definition-structure.md#conditions)nebo **notIn** .
Tady se používá pro alias **serviceProvider. bandwidthInMbps** jako jedna z definovaných hodnot.

:::code language="json" source="~/policy-templates/patterns/pattern-parameters-2.json" range="21-24" highlight="3":::

## <a name="next-steps"></a>Další kroky

- Zkontrolujte další [vzory a předdefinované definice](./index.md).
- Projděte si [strukturu definic Azure Policy](../concepts/definition-structure.md).
- Projděte si [Vysvětlení efektů zásad](../concepts/effects.md).