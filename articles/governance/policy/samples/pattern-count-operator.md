---
title: 'Vzor: operátor Count v definici zásady'
description: Tento model Azure Policy poskytuje příklad použití operátoru Count v definici zásady.
ms.date: 10/14/2020
ms.topic: sample
ms.openlocfilehash: 1339dff7f8bc92a8e38ec5635690cc2069dd8df4
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/25/2020
ms.locfileid: "96005414"
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