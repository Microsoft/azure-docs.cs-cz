---
title: Podrobnosti o struktuře přiřazení zásad
description: Popisuje definici přiřazení zásad, kterou zásady Azure používají ke propojení definic a parametrů zásad s prostředky pro vyhodnocení.
ms.date: 09/23/2019
ms.topic: conceptual
ms.openlocfilehash: f03c654dfc4c8dfdf2bdc5103a5961b4d8ce1e64
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "79265295"
---
# <a name="azure-policy-assignment-structure"></a>Struktura přiřazení Azure Policy

Přiřazení zásad se používá zásady Azure definovat, které prostředky jsou přiřazeny které zásady nebo iniciativy. Přiřazení zásad může určit hodnoty parametrů pro tuto skupinu zdrojů v době přiřazení, což umožňuje znovu použít definice zásad, které řeší stejné vlastnosti prostředků s různými potřebami dodržování předpisů.

JSON slouží k vytvoření přiřazení zásad. Přiřazení zásad obsahuje prvky pro:

- zobrazovaný název
- description
- zprostředkovatele identity
- režim vynucení
- definice zásad
- parameters

Například následující JSON zobrazuje přiřazení zásad v režimu _DoNotEnforce_ s dynamickými parametry:

```json
{
    "properties": {
        "displayName": "Enforce resource naming rules",
        "description": "Force resource names to begin with DeptA and end with -LC",
        "metadata": {
            "assignedBy": "Cloud Center of Excellence"
        },
        "enforcementMode": "DoNotEnforce",
        "policyDefinitionId": "/subscriptions/{mySubscriptionID}/providers/Microsoft.Authorization/policyDefinitions/ResourceNaming",
        "parameters": {
            "prefix": {
                "value": "DeptA"
            },
            "suffix": {
                "value": "-LC"
            }
        }
    }
}
```

Všechny ukázky zásad Azure jsou na [ukázkách zásad Azure](../samples/index.md).

## <a name="display-name-and-description"></a>Zobrazovaný název a popis

DisplayName **displayName** a **popis** slouží k identifikaci přiřazení zásad a poskytnutí kontextu pro jeho použití s konkrétní sadou prostředků. **displayName** má maximální délku _128_ znaků a **popis** maximální délku _512_ znaků.

## <a name="enforcement-mode"></a>Režim vynucení

Vlastnost **enforcementMode** poskytuje zákazníkům možnost otestovat výsledek zásady na existující prostředky bez zahájení efektu zásad nebo aktivaci položek v [protokolu aktivit Azure](../../../azure-monitor/platform/platform-logs-overview.md). Tento scénář se běžně označuje jako "Co kdyby" a zarovná se k bezpečné nasazení postupy. **enforcementMode** se liší od [efektu Disabled,](./effects.md#disabled) protože tento efekt zabraňuje vyhodnocení prostředků vůbec.

Tato vlastnost má následující hodnoty:

|Mode |JSON Hodnota |Typ |Ruční náprava |Položka protokolu aktivit |Popis |
|-|-|-|-|-|-|
|Povoleno |Výchozí |řetězec |Ano |Ano |Efekt zásad je vynucenběhem vytváření nebo aktualizace prostředků. |
|Zakázáno |Donotenforce |řetězec |Ano |Ne | Efekt zásad není vynucenběhem vytváření nebo aktualizace prostředků. |

Pokud **enforcementMode** není zadán v definici zásady nebo iniciativy, použije se hodnota _Default._ [Nápravné úlohy](../how-to/remediate-resources.md) lze spustit pro [deployIfNotExists zásady,](./effects.md#deployifnotexists) i když **enforcementMode** je nastavena na _DoNotEnforce_.

## <a name="policy-definition-id"></a>ID definice zásad

Toto pole musí být úplný název cesty definice zásady nebo definice iniciativy.
`policyDefinitionId`je řetězec a nikoli pole. Pokud je často přiřazeno více zásad, použijte místo toho [iniciativu.](./definition-structure.md#initiatives)

## <a name="parameters"></a>Parametry

Tento segment přiřazení zásad poskytuje hodnoty parametrů definovaných v [definici zásady nebo definici iniciativy](./definition-structure.md#parameters).
Tento návrh umožňuje znovu použít definici zásady nebo iniciativy s různými prostředky, ale zkontrolovat různé obchodní hodnoty nebo výsledky.

```json
"parameters": {
    "prefix": {
        "value": "DeptA"
    },
    "suffix": {
        "value": "-LC"
    }
}
```

V tomto příkladu jsou `prefix` parametry dříve definované `suffix`v definici zásady a . Toto přiřazení `prefix` zásad nastaví `suffix` na **Oddělení** a **-LC**. Stejná definice zásad je opakovaně použitelná s jinou sadou parametrů pro jiné oddělení, což snižuje duplicitu a složitost definic zásad a současně poskytuje flexibilitu.

## <a name="next-steps"></a>Další kroky

- Informace o [struktuře definice zásad](./definition-structure.md).
- Pochopit, jak [programově vytvářet zásady](../how-to/programmatically-create.md).
- Přečtěte si, jak [získat data o dodržování předpisů](../how-to/get-compliance-data.md).
- Přečtěte si, jak [napravit nekompatibilní prostředky](../how-to/remediate-resources.md).
- Zkontrolujte, co je skupina pro správu [pomocí organizace Uspořádat prostředky pomocí skupin pro správu Azure](../../management-groups/overview.md).