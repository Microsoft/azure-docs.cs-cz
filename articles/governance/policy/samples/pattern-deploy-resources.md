---
title: 'Vzor: Nasazení prostředků s definicí zásad'
description: Tento vzor zásad Azure poskytuje příklad, jak nasadit prostředky s definicí zásad.
ms.date: 01/31/2020
ms.topic: sample
ms.openlocfilehash: a8b6528afbd21c7c667e48965574c9b48c403654
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/24/2020
ms.locfileid: "77172671"
---
# <a name="azure-policy-pattern-deploy-resources"></a>Vzor zásad Azure: nasazení prostředků

Efekt [deployIfNotExists](../concepts/effects.md#deployifnotexists) umožňuje nasadit [šablonu Azure Resource Manageru](../../../azure-resource-manager/templates/overview.md) při vytváření nebo aktualizaci prostředku, který není kompatibilní. Tento přístup lze upřednostňovat pomocí [efektu odepřít,](../concepts/effects.md#deny) protože umožňuje, aby prostředky byly nadále vytvářeny, ale zajišťuje, že jsou provedeny změny, aby byly kompatibilní.

## <a name="sample-policy-definition"></a>Ukázková definice zásad

Tato definice zásad **field** používá operátor `type` pole k vyhodnocení vytvořeného nebo aktualizovaného zdroje. Pokud je tento prostředek _Microsoft.Network/virtualNetworks_, zásada hledá sledovací proces sítě v umístění nového nebo aktualizovaného prostředku. Pokud není umístěn odpovídající sledovací proces sítě, šablona Správce prostředků se nasadí k vytvoření chybějícího prostředku.

:::code language="json" source="~/policy-templates/patterns/pattern-deploy-resources.json":::

### <a name="explanation"></a>Vysvětlení

#### <a name="existencecondition"></a>existencePodmínka

:::code language="json" source="~/policy-templates/patterns/pattern-deploy-resources.json" range="18-23":::

Blok **properties.policyRule.then.details** říká Azure Policy, co hledat související s vytvořeným nebo aktualizovaným prostředkem v bloku **properties.policyRule.if.** V tomto příkladu musí existovat sledovací proces sítě v síti skupiny **prostředkůWatcherRG** s **polem** `location` rovným umístění nového nebo aktualizovaného prostředku. Pomocí `field()` funkce umožňuje **existenciCondition** pro přístup k vlastnostem na `location` nový nebo aktualizovaný prostředek, konkrétně vlastnost.

#### <a name="roledefinitionids"></a>roleDefinitionIds

:::code language="json" source="~/policy-templates/patterns/pattern-deploy-resources.json" range="24-26":::

Vlastnost _pole_ **roleDefinitionIds** v bloku **properties.policyRule.then.details** sděluje definici zásad, která práva spravovaná identita potřebuje k nasazení zahrnuté šablony Správce prostředků. Tato vlastnost musí být nastavena tak, aby zahrnovala role, které mají oprávnění potřebná pro nasazení šablony, ale měla by používat koncept "zásady nejnižšího oprávnění" a mít pouze potřebné operace a nic víc.

#### <a name="deployment-template"></a>Šablona nasazení

Část definice zásad **pro nasazení** má blok **vlastností,** který definuje tři základní součásti:

- **režim** - Tato vlastnost nastaví [režim nasazení](../../../azure-resource-manager/templates/deployment-modes.md) šablony.

- **šablona** - Tato vlastnost obsahuje samotnou šablonu. V tomto příkladu parametr šablony **umístění** nastaví umístění nového prostředku sledování sítě.

  :::code language="json" source="~/policy-templates/patterns/pattern-deploy-resources.json" range="30-44":::
  
- **parametry** - Tato vlastnost definuje parametry, které jsou k dispozici **v šabloně**. Názvy parametrů se musí shodovat s tím, co je definováno v **šabloně**. V tomto příkladu je parametr pojmenován **umístění** tak, aby odpovídalo. Hodnota **umístění** používá `field()` funkci znovu získat hodnotu vyhodnocovaných prostředků, což je virtuální síť v **policyRule.if** bloku.

  :::code language="json" source="~/policy-templates/patterns/pattern-deploy-resources.json" range="45-49":::

## <a name="next-steps"></a>Další kroky

- Zkontrolujte další [vzory a předdefinované definice](./index.md).
- Projděte si [strukturu definic Azure Policy](../concepts/definition-structure.md).
- Projděte si [Vysvětlení efektů zásad](../concepts/effects.md).