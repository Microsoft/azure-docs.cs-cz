---
title: 'Vzor: operátor Count v definici zásady'
description: Tento model Azure Policy poskytuje příklad použití operátoru Count v definici zásady.
ms.date: 03/31/2021
ms.topic: sample
ms.openlocfilehash: dc2914028887ae5a91e3379e2a94ddbc57a7cef3
ms.sourcegitcommit: 99fc6ced979d780f773d73ec01bf651d18e89b93
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/31/2021
ms.locfileid: "106093448"
---
# <a name="azure-policy-pattern-the-count-operator"></a>Azure Policy vzor: operátor Count

Operátor [Count](../concepts/definition-structure.md#count) vyhodnocuje členy \[ \* \] aliasu.

## <a name="sample-policy-definition"></a>Definice ukázkové zásady

Tato definice zásad [Audituje](../concepts/effects.md#audit) skupiny zabezpečení sítě, které jsou nakonfigurované tak, aby umožňovaly provoz příchozího protokol RDP (Remote Desktop Protocol) (RDP)

:::code language="json" source="~/policy-templates/patterns/pattern-count-operator.json":::

### <a name="explanation"></a>Vysvětlení

Základní komponenty operátoru **Count** jsou _pole_, _kde_ a podmínka. Každá je zvýrazněna v následujícím fragmentu kódu.

- _pole_ udává počet, který [alias](../concepts/definition-structure.md#aliases) vyhodnocuje členy. Tady se díváte na _pole_ aliasu **securityRules \[ \* \]** skupiny zabezpečení sítě.
- _kde_ nástroj používá jazyk zásad k definování, které členy _pole_ splňují kritéria. V tomto příkladu skupiny logických operátorů **allOf** seskupují tři odlišná vyhodnocení podmínky vlastností _pole_ aliasu: _Direction_, _Access_ a _destinationPortRange_.
- Podmínka Count v tomto příkladu je **větší**. Počet se vyhodnotí jako true, pokud jeden nebo více členů _pole_ aliasu odpovídá klauzuli _WHERE_ .

:::code language="json" source="~/policy-templates/patterns/pattern-count-operator.json" range="12-32" highlight="3,4,20":::

## <a name="next-steps"></a>Další kroky

- Zkontrolujte další [vzory a předdefinované definice](./index.md).
- Projděte si [strukturu definic Azure Policy](../concepts/definition-structure.md).
- Projděte si [Vysvětlení efektů zásad](../concepts/effects.md).