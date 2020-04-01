---
title: 'Vzor: Operátor počtu v definici zásady'
description: Tento vzor zásad Azure poskytuje příklad použití operátoru count v definici zásad.
ms.date: 01/31/2020
ms.topic: sample
ms.openlocfilehash: 88c2d1083a92732ac56ca4d6da7087cc4220d9a5
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/24/2020
ms.locfileid: "77172944"
---
# <a name="azure-policy-pattern-the-count-operator"></a>Vzor zásad Azure: operátor počtu

Operátor [count](../concepts/definition-structure.md#count) vyhodnocuje členy aliasu. \[ \* \]

## <a name="sample-policy-definition"></a>Ukázková definice zásad

Tato definice zásad [audituje skupiny](../concepts/effects.md#audit) zabezpečení sítě nakonfigurované tak, aby umožňovaly příchozí přenosy protokolu RDP (Remote Desktop Protocol).

:::code language="json" source="~/policy-templates/patterns/pattern-count-operator.json":::

### <a name="explanation"></a>Vysvětlení

Základní součásti operátoru **počítání** jsou _pole_, _kde_a podmínka. Každý z nich je zvýrazněn ve fragmentu níže.

- _pole_ sděluje počet, který [alias](../concepts/definition-structure.md#aliases) vyhodnotit členy. Zde se díváme na _aliasové pole_ **\[ \* securityRules** skupiny zabezpečení sítě.
- _kde_ používá jazyk zásad k definování, které členy _pole_ splňují kritéria. V tomto příkladu **allOf** logický operátor seskupí tři různé podmínky hodnocení vlastnosti _pole_ alias: _směr_, _přístup_a _cílPortRange_.
- Podmínka počítání v tomto příkladu je **větší**. Count vyhodnotí jako true, když jeden nebo více členů _alias pole_ odpovídá _where_ klauzule.

:::code language="json" source="~/policy-templates/patterns/pattern-count-operator.json" range="12-32" highlight="3,4,20":::

## <a name="next-steps"></a>Další kroky

- Zkontrolujte další [vzory a předdefinované definice](./index.md).
- Projděte si [strukturu definic Azure Policy](../concepts/definition-structure.md).
- Projděte si [Vysvětlení efektů zásad](../concepts/effects.md).