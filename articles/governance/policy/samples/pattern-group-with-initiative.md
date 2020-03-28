---
title: 'Vzor: Definice zásad skupiny s iniciativami'
description: Tento vzor zásad Azure poskytuje příklad, jak seskupit definice zásad do iniciativy
ms.date: 01/31/2020
ms.topic: sample
ms.openlocfilehash: 41c2b0cf3b8f677cdc408e85088c3ca6c2049d6b
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/24/2020
ms.locfileid: "77172853"
---
# <a name="azure-policy-pattern-group-policy-definitions"></a>Vzor zásad Azure: definice zásad skupiny

Iniciativa je skupina definic politik. Seskupením souvisejících definic zásad do jednoho objektu můžete vytvořit jedno přiřazení, které by bylo více přiřazení.

## <a name="sample-initiative-definition"></a>Vzorová definice iniciativy

Tato iniciativa nasadí dvě definice zásad, z nichž každá přebírá parametry **tagName** a **tagValue.** Samotná iniciativa má dva parametry: **costCenterValue** a **productNameValue**.
Tyto parametry iniciativy jsou k dispozici pro každou z definic seskupených zásad. Tento návrh maximalizuje opakované použití existujících definic zásad a zároveň omezuje počet přiřazení vytvořených k jejich implementaci podle potřeby.

:::code language="json" source="~/policy-templates/patterns/pattern-group-with-initiative.json":::

### <a name="explanation"></a>Vysvětlení

#### <a name="initiative-parameters"></a>Parametry iniciativ

Iniciativa může definovat vlastní parametry, které jsou pak předány definice seskupených zásad.
V tomto příkladu jsou jako parametry iniciativy definovány jak **hodnota costCenterValue,** tak **productNameValue.** Hodnoty jsou k dispozici při přiřazení iniciativy.

:::code language="json" source="~/policy-templates/patterns/pattern-group-with-initiative.json" range="5-18":::

#### <a name="includes-policy-definitions"></a>Zahrnuje definice zásad.

Každá zahrnutá definice zásad musí poskytnout **policyDefinitionId** a **pole parametrů,** pokud definice zásady přijímá parametry. V níže uvedeném fragmentu úryvek má zahrnutá definice zásad dva parametry: **tagName** a **tagValue**. **tagName** je definován literálem, ale **hodnota tagValue** používá parametr **costCenterValue** definovaný iniciativou. Tento průchod hodnot zlepšuje opakované použití.

:::code language="json" source="~/policy-templates/patterns/pattern-group-with-initiative.json" range="30-40":::

## <a name="next-steps"></a>Další kroky

- Zkontrolujte další [vzory a předdefinované definice](./index.md).
- Projděte si [strukturu definic Azure Policy](../concepts/definition-structure.md).
- Projděte si [Vysvětlení efektů zásad](../concepts/effects.md).