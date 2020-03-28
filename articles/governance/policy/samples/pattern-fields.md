---
title: 'Vzorek: Vlastnosti pole v definici zásady'
description: Tento vzor zásad Azure poskytuje příklad použití vlastností pole v definici zásad.
ms.date: 01/31/2020
ms.topic: sample
ms.openlocfilehash: e65767dd9cbe7b2192c21f779643289e5a7fc45e
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/24/2020
ms.locfileid: "77172860"
---
# <a name="azure-policy-pattern-field-properties"></a>Vzor zásad Azure: vlastnosti polí

Operátor [pole](../concepts/definition-structure.md#fields) vyhodnotí zadanou vlastnost nebo [alias](../concepts/definition-structure.md#aliases) na zadanou hodnotu pro danou [podmínku](../concepts/definition-structure.md#conditions).

## <a name="sample-policy-definition"></a>Ukázková definice zásad

Tato definice zásad umožňuje definovat povolené oblasti, které splňují požadavky vaší organizace na geografickou polohu. Povolené prostředky jsou definovány v **seznamu parametrůOfAllowedLocations** _(pole)._ Prostředky, které odpovídají definici, jsou [odepřeny](../concepts/effects.md#deny).

:::code language="json" source="~/policy-templates/patterns/pattern-fields.json":::

### <a name="explanation"></a>Vysvětlení

:::code language="json" source="~/policy-templates/patterns/pattern-fields.json" range="18-36" highlight="3,7,11":::

Field **field** Operátor se používá třikrát v rámci [logického operátoru](../concepts/definition-structure.md#logical-operators) **allOf**.

- První použití vyhodnotí `location` vlastnost s **podmínkou notIn** do parametru **listOfAllowedLocations.** **notIn** funguje tak, jak očekává _pole_ a parametr je _pole_. `location` Pokud vytvořený nebo aktualizovaný prostředek není ve schváleném seznamu, tento prvek vyhodnotí na hodnotu true.
- Druhé použití také vyhodnotí `location` vlastnost, ale používá **notEquals** podmínku zjistit, pokud je prostředek _globální_. Pokud vytvořený nebo aktualizovaný prostředek není globální , tento prvek vyhodnotí na hodnotu true. _global_ `location`
- Poslední použití vyhodnotí `type` vlastnost a použije podmínku **notEquals** k ověření typu prostředku není _Microsoft.AzureActiveDirectory/b2cDirectories_. Pokud tomu tak není, tento prvek vyhodnotí true.

Pokud všechny tři příkazy podmínky v **allOf** logický operátor vyhodnotit true, vytvoření prostředků nebo aktualizace je blokován zásady Azure.

## <a name="next-steps"></a>Další kroky

- Zkontrolujte další [vzory a předdefinované definice](./index.md).
- Projděte si [strukturu definic Azure Policy](../concepts/definition-structure.md).
- Projděte si [Vysvětlení efektů zásad](../concepts/effects.md).