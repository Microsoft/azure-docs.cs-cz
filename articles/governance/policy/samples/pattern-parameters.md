---
title: 'Vzor: parametry v definici zásady'
description: Tento model Azure Policy poskytuje příklad použití parametrů řetězce a pole v definici zásady a také jak parametrizovat efekt.
ms.date: 08/17/2020
ms.topic: sample
ms.openlocfilehash: 7bb991c43a1deee39dd047aa7d814c124712fe69
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "89649946"
---
# <a name="azure-policy-pattern-parameters"></a>Azure Policy – vzor: parametry

Definici zásady je možné nastavit jako dynamickou a snížit tak počet definic zásad, které jsou potřeba pomocí [parametrů](../concepts/definition-structure.md#parameters). Parametr je definován během přiřazování zásad. Parametry mají sadu předdefinovaných vlastností, které popisují parametr a způsob použití.

## <a name="sample-1-string-parameters"></a>Ukázka 1: parametry řetězce

Tato definice zásady používá dva parametry, **TagName** a **tagValue** k nastavení toho, co přiřazení zásad hledá u prostředků. Tento formát umožňuje použití definice zásad pro libovolný počet kombinací názvů značek a hodnot značek, ale udržuje jenom jednu definici zásady.

> [!NOTE]
> Pro ukázku značek, která používá **režim** _All_ a pracuje se skupinou prostředků, viz [vzor: značky-Sample #1](./pattern-tags.md#sample-1-parameterize-tags).

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

Parametr je pak použit v bloku **policyRule. if** . Jako parametr _pole_ _array_ 
 musí být použita[Podmínka](../concepts/definition-structure.md#conditions)pole **v** nebo **notIn** .
Tady se používá pro alias **serviceProvider. bandwidthInMbps** jako jedna z definovaných hodnot.

:::code language="json" source="~/policy-templates/patterns/pattern-parameters-2.json" range="21-24" highlight="3":::

## <a name="sample-3-parameterized-effect"></a>Ukázka 3: parametrizovaný efekt

Běžným způsobem, jak nastavit změny v definicích zásad, je parametrizovat vliv sám na sebe. V tomto příkladu se používá jeden parametr, **efekt**. Parametrizace účinek umožňuje přiřadit stejnou definici různým oborům s různými účinky.

:::code language="json" source="~/policy-templates/patterns/pattern-parameters-3.json":::

### <a name="sample-3-explanation"></a>Ukázka 3: vysvětlení

:::code language="json" source="~/policy-templates/patterns/pattern-parameters-3.json" range="11-25":::

V této části definice zásady je parametr **efekt** definovaný jako _řetězec_. Definice zásady nastaví výchozí hodnotu pro přiřazení pro _audit_ a omezí další možnosti na _zakázáno_ a _Odepřít_.

Parametr se pak použije v **policyRule. potom** pro _efekt_.

:::code language="json" source="~/policy-templates/patterns/pattern-parameters-3.json" range="38-40" highlight="2":::

## <a name="next-steps"></a>Další kroky

- Zkontrolujte další [vzory a předdefinované definice](./index.md).
- Projděte si [strukturu definic Azure Policy](../concepts/definition-structure.md).
- Projděte si [Vysvětlení efektů zásad](../concepts/effects.md).