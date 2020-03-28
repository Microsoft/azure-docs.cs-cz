---
title: 'Vzor: Účinky definice zásad'
description: Tento vzor zásad Azure poskytuje příklad použití různých účinků definice zásad.
ms.date: 01/31/2020
ms.topic: sample
ms.openlocfilehash: 1a9aec50bd328b76271d54f7830c75e0848d3cde
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "80372633"
---
# <a name="azure-policy-pattern-effects"></a>Vzor zásad Azure: efekty

Azure Policy má řadu [efektů,](../concepts/effects.md) které určují, jak služba reaguje na nekompatibilní prostředky. Některé efekty jsou jednoduché a nevyžadují žádné další vlastnosti v definici zásady, zatímco jiné vyžadují několik vlastností.

## <a name="sample-1-simple-effect"></a>Ukázka 1: Jednoduchý efekt

Tato definice zásad zkontroluje, zda značka definovaná v parametru **tagName** existuje na hodnoceném prostředku. Pokud značka ještě neexistuje, spustí se [efekt úpravy](../concepts/effects.md#modify) pro přidání značky s hodnotou v parametru **tagValue**.

:::code language="json" source="~/policy-templates/patterns/pattern-effect-details-1.json":::

### <a name="sample-1-explanation"></a>Ukázka 1: Vysvětlení

:::code language="json" source="~/policy-templates/patterns/pattern-effect-details-1.json" range="40-50":::

Efekt **změny** vyžaduje blok **policyRule.then.details,** který definuje **roleDefinitionIds** a **operace**. Tyto parametry informují Zásady Azure, jaké role jsou potřeba k přidání značky a nápravě prostředku a které **upravují** operaci k provedení. V tomto příkladu je **operace** _přidat_ a parametry se používají k nastavení značky a její hodnoty.

## <a name="sample-2-complex-effect"></a>Vzorek 2: Komplexní efekt

Tato definice zásad audituje každý virtuální počítač, pokud rozšíření definované v parametrech **vydavatela** a **typu**, neexistuje. Používá [auditIfNotExists](../concepts/effects.md#auditifnotexists) ke kontrole prostředku souvisejícího s virtuálním počítačem, aby zjistil, zda existuje instance, která odpovídá definovaným parametrům. Tento příklad kontroluje typ **rozšíření.**

:::code language="json" source="~/policy-templates/patterns/pattern-effect-details-2.json":::

### <a name="sample-2-explanation"></a>Ukázka 2: Vysvětlení

:::code language="json" source="~/policy-templates/patterns/pattern-effect-details-2.json" range="45-58":::

Efekt **auditIfNotExists** vyžaduje blok **policyRule.then.details** k definování **typu** i **stavu existence,** který má být vyhledán. Existcondition používá prvky jazyka zásad, například [logické operátory](../concepts/definition-structure.md#logical-operators), k určení, zda existuje odpovídající související prostředek. **existenceCondition** V tomto příkladu jsou hodnoty zaškrtnuté proti každému [aliasu](../concepts/definition-structure.md#aliases) definovány v parametrech.

## <a name="next-steps"></a>Další kroky

- Zkontrolujte další [vzory a předdefinované definice](./index.md).
- Projděte si [strukturu definic Azure Policy](../concepts/definition-structure.md).
- Projděte si [Vysvětlení efektů zásad](../concepts/effects.md).