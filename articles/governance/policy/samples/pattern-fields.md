---
title: 'Vzor: vlastnosti pole v definici zásady'
description: Tento model Azure Policy poskytuje příklad použití vlastností pole v definici zásady.
ms.date: 01/31/2020
ms.topic: sample
ms.openlocfilehash: e65767dd9cbe7b2192c21f779643289e5a7fc45e
ms.sourcegitcommit: bdf31d87bddd04382effbc36e0c465235d7a2947
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/12/2020
ms.locfileid: "77172860"
---
# <a name="azure-policy-pattern-field-properties"></a>Azure Policy – vzor: vlastnosti pole

Operátor [pole](../concepts/definition-structure.md#fields) vyhodnotí zadanou vlastnost nebo [alias](../concepts/definition-structure.md#aliases) k zadané hodnotě pro danou [podmínku](../concepts/definition-structure.md#conditions).

## <a name="sample-policy-definition"></a>Definice ukázkové zásady

Tato definice zásad vám umožní definovat povolené oblasti, které splňují požadavky vaší organizace na geografické umístění. Povolené prostředky jsou definované v parametru **listOfAllowedLocations** (_Array_). Prostředky, které odpovídají definici, jsou [odepřeny](../concepts/effects.md#deny).

:::code language="json" source="~/policy-templates/patterns/pattern-fields.json":::

### <a name="explanation"></a>Vysvětlení

:::code language="json" source="~/policy-templates/patterns/pattern-fields.json" range="18-36" highlight="3,7,11":::

Operátor **pole** se používá třikrát v rámci [logického operátoru](../concepts/definition-structure.md#logical-operators) **allOf**.

- První použití vyhodnotí vlastnost `location` s podmínkou **notIn** pro parametr **listOfAllowedLocations** . **notIn** funguje, protože očekává _pole_ a parametr je _pole_. Pokud `location` vytvořeného nebo aktualizovaného prostředku není v seznamu schválených, tento prvek se vyhodnotí jako true.
- Druhé použití také vyhodnotí vlastnost `location`, ale pomocí podmínky **notEquals** zjistí, zda je prostředek _globální_. Pokud `location` vytvořeného nebo aktualizovaného prostředku není _globální_, tento prvek se vyhodnotí jako true.
- Poslední použití vyhodnocuje vlastnost `type` a používá podmínku **notEquals** k ověření, že typ prostředku není _Microsoft. azureactivedirectory selhala/b2cDirectories_. Pokud není, tento prvek je vyhodnocen jako true.

Pokud všechny tři příkazy podmínky v logickém operátoru **allOf** vyhodnotí jako true, je vytvoření nebo aktualizace prostředku blokováno Azure Policy.

## <a name="next-steps"></a>Další kroky

- Zkontrolujte další [vzory a předdefinované definice](./index.md).
- Projděte si [strukturu definic Azure Policy](../concepts/definition-structure.md).
- Projděte si [Vysvětlení efektů zásad](../concepts/effects.md).