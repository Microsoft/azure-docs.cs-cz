---
title: 'Vzor: nasazení prostředků pomocí definice zásady'
description: Tento model Azure Policy poskytuje příklad, jak nasadit prostředky pomocí definice zásady deployIfNotExists.
ms.date: 08/17/2020
ms.topic: sample
ms.openlocfilehash: 0a9eec54954b8963f38b3f19a0d0cabffe1092e5
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/29/2021
ms.locfileid: "89649986"
---
# <a name="azure-policy-pattern-deploy-resources"></a>Azure Policy vzor: nasazení prostředků

[DeployIfNotExists](../concepts/effects.md#deployifnotexists) efekt umožňuje nasadit [šablonu Azure Resource Manager](../../../azure-resource-manager/templates/overview.md) (šablonu ARM) při vytváření nebo aktualizaci prostředku, který nedodržuje předpisy. Tento přístup může být upřednostňován pro použití efektu [zamítnout](../concepts/effects.md#deny) , protože umožňuje vytváření prostředků i nadále, ale zajišťuje, aby byly provedeny změny v souladu s předpisy.

## <a name="sample-policy-definition"></a>Definice ukázkové zásady

Tato definice zásady používá operátor **pole** k vyhodnocení `type` vytvořeného nebo aktualizovaného prostředku. Pokud je tento prostředek _Microsoft. Network/virtualNetworks_, vyhledá zásady sledovací proces sítě v umístění nového nebo aktualizovaného prostředku. Pokud se nezobrazuje vyhovující sledovací proces sítě, nasadí se šablona ARM pro vytvoření chybějícího prostředku.

:::code language="json" source="~/policy-templates/patterns/pattern-deploy-resources.json":::

### <a name="explanation"></a>Vysvětlení

#### <a name="existencecondition"></a>existenceCondition

:::code language="json" source="~/policy-templates/patterns/pattern-deploy-resources.json" range="18-23":::

Blok **Properties. policyRule. then. Details** oznamuje Azure Policy, co hledat v souvislosti s vytvořeným nebo aktualizovaným prostředkem v bloku **Properties. policyRule. if** . V tomto příkladu musí **networkWatcherRG** existovat sledovací proces sítě ve skupině prostředků s **polem** , které se `location` rovná umístění nového nebo aktualizovaného prostředku. Použití `field()` funkce umožňuje **existenceCondition** získat přístup k vlastnostem nového nebo aktualizovaného prostředku, konkrétně k `location` Vlastnosti.

#### <a name="roledefinitionids"></a>roleDefinitionIds

:::code language="json" source="~/policy-templates/patterns/pattern-deploy-resources.json" range="24-26":::

Vlastnost **roleDefinitionIds** _Array_ v bloku **Properties. policyRule. then. Details** oznamuje definici zásady, která musí spravovat ta, která spravovaná identita potřebuje k nasazení zahrnuté šablony Správce prostředků. Tato vlastnost musí být nastavena tak, aby zahrnovala role, které mají oprávnění potřebná pro nasazení šablony, ale měla by používat koncept "principu nejnižší úrovně oprávnění" a mít pouze potřebné operace a nic dalšího.

#### <a name="deployment-template"></a>Šablona nasazení

Část **nasazení** v definici zásady má blok **vlastností** , který definuje tři základní komponenty:

- **Mode** – Tato vlastnost nastaví [režim nasazení](../../../azure-resource-manager/templates/deployment-modes.md) šablony.

- **Šablona** – Tato vlastnost obsahuje vlastní šablonu. V tomto příkladu parametr šablony **umístění** nastaví umístění nového prostředku sledovacího procesu sítě.

  :::code language="json" source="~/policy-templates/patterns/pattern-deploy-resources.json" range="30-44":::
  
- **parametry** – Tato vlastnost definuje parametry, které jsou k dispozici v **šabloně**. Názvy parametrů se musí shodovat s tím, co je definováno v **šabloně**. V tomto příkladu je parametr pojmenován **umístění** , které se má shodovat. Hodnota **umístění** `field()` znovu používá funkci k získání hodnoty vyhodnoceného prostředku, což je virtuální síť v bloku **policyRule. if** .

  :::code language="json" source="~/policy-templates/patterns/pattern-deploy-resources.json" range="45-49":::

## <a name="next-steps"></a>Další kroky

- Zkontrolujte další [vzory a předdefinované definice](./index.md).
- Projděte si [strukturu definic Azure Policy](../concepts/definition-structure.md).
- Projděte si [Vysvětlení efektů zásad](../concepts/effects.md).