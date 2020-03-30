---
title: Zásady autora pro vlastnosti pole na prostředcích
description: Naučte se pracovat s parametry pole a výrazy jazyka pole, vyhodnoťte alias [*] a připojujte prvky s pravidly definice zásad Azure.
ms.date: 11/26/2019
ms.topic: how-to
ms.openlocfilehash: 991d159f6444133d902382bc9ca43bc2acd201e2
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "79280661"
---
# <a name="author-policies-for-array-properties-on-azure-resources"></a>Zásady autora pro vlastnosti pole v prostředcích Azure

Vlastnosti Azure Resource Manager udávají běžně jako řetězce a logické hodnoty. Pokud existuje vztah 1:N, komplexní vlastnosti jsou místo toho definovány jako pole. V Azure Policy se pole používají několika různými způsoby:

- Typ [parametru definice](../concepts/definition-structure.md#parameters), který poskytuje více možností
- Část [pravidla zásad](../concepts/definition-structure.md#policy-rule) pomocí podmínek **v** **nebo notIn**
- Část pravidla zásad, které [ \[ \* \] ](../concepts/definition-structure.md#understanding-the--alias) vyhodnocuje alias k vyhodnocení:
  - Scénáře jako **Žádné**, **Žádné**nebo **Všechny**
  - Složité scénáře s **počtem**
- V [efektu připojení,](../concepts/effects.md#append) který má nahradit nebo přidat do existujícího pole

Tento článek popisuje každé použití zásad Azure a poskytuje několik příkladů definice.

## <a name="parameter-arrays"></a>Pole parametrů

### <a name="define-a-parameter-array"></a>Definování pole parametrů

Definování parametru jako pole umožňuje flexibilitu zásad, pokud je potřeba více než jednu hodnotu.
Tato definice zásad umožňuje libovolné jedno umístění pro parametr **allowedLocations** a defaults na _eastus2_:

```json
"parameters": {
    "allowedLocations": {
        "type": "string",
        "metadata": {
            "description": "The list of allowed locations for resources.",
            "displayName": "Allowed locations",
            "strongType": "location"
        },
        "defaultValue": "eastus2"
    }
}
```

Jako **typ** byl _řetězec_, lze nastavit pouze jednu hodnotu při přiřazování zásady. Pokud je tato zásada přiřazena, prostředky v oboru jsou povoleny pouze v rámci jedné oblasti Azure. Většina definic zásad musí umožňovat seznam schválených možností, například povolení _eastus2_, _eastus_a _westus2_.

Chcete-li vytvořit definici zásad, která umožní více možností, použijte **typ** _pole_ . Stejné zásady lze přepsat takto:

```json
"parameters": {
    "allowedLocations": {
        "type": "array",
        "metadata": {
            "description": "The list of allowed locations for resources.",
            "displayName": "Allowed locations",
            "strongType": "location"
        },
        "defaultValue": "eastus2",
        "allowedValues": [
            "eastus2",
            "eastus",
            "westus2"
        ]

    }
}
```

> [!NOTE]
> Po uložení definice zásad nelze změnit vlastnost **typu** parametru.

Tato nová definice parametru trvá více než jednu hodnotu během přiřazení zásad. S vlastností pole **allowedValues jsou** hodnoty dostupné během přiřazení dále omezeny na předdefinovaný seznam voleb. Použití **allowedValues** je volitelné.

### <a name="pass-values-to-a-parameter-array-during-assignment"></a>Předat hodnoty poli parametrů během přiřazení

Při přiřazování zásad prostřednictvím portálu Azure se parametr _pole_ **typu** zobrazí jako jediné textové pole. Nápověda říká: "Použijte ; k oddělují hodnoty. (např. londýn; v New Yorku". Chcete-li parametru předat povolené hodnoty umístění _eastus2_, _eastus_a _westus2,_ použijte následující řetězec:

`eastus2;eastus;westus2`

Formát hodnoty parametru se liší při použití Azure CLI, Azure PowerShell nebo rozhraní REST API. Hodnoty jsou předávány prostřednictvím řetězce JSON, který také obsahuje název parametru.

```json
{
    "allowedLocations": {
        "value": [
            "eastus2",
            "eastus",
            "westus2"
        ]
    }
}
```

Chcete-li tento řetězec použít s každou sadou SDK, použijte následující příkazy:

- Azure CLI: Příkaz [přiřazení zásad az vytvořit](/cli/azure/policy/assignment?view=azure-cli-latest#az-policy-assignment-create) s **parametrem params**
- Azure PowerShell: Rutina [New-AzPolicyAssignment](/powershell/module/az.resources/New-Azpolicyassignment) s parametrem **PolicyParameter**
- REST API: V _put_ [vytvořit](/rest/api/resources/policyassignments/create) operaci jako součást tělo požadavku jako hodnota **vlastnostvlastnosti properties.parameters**

## <a name="policy-rules-and-arrays"></a>Pravidla zásad a pole

### <a name="array-conditions"></a>Podmínky pole

[Podmínky](../concepts/definition-structure.md#conditions) pravidla zásad, se kterými lze použít**typ** `notIn` _pole_
parametru, je omezen na `in` a . Jako příklad ujměte následující definici zásad s podmínkou: `equals`

```json
{
  "policyRule": {
    "if": {
      "not": {
        "field": "location",
        "equals": "[parameters('allowedLocations')]"
      }
    },
    "then": {
      "effect": "audit"
    }
  },
  "parameters": {
    "allowedLocations": {
      "type": "Array",
      "metadata": {
        "description": "The list of allowed locations for resources.",
        "displayName": "Allowed locations",
        "strongType": "location"
      }
    }
  }
}
```

Pokus o vytvoření této definice zásad prostřednictvím portálu Azure vede k chybě, jako je tato chybová zpráva:

- "Zásadu {GUID} nelze parametrizovat z důvodu chyb ověření. Zkontrolujte, zda jsou správně definovány parametry zásad. Vnitřní výjimka 'Výsledek vyhodnocení výrazu jazyka '[parameters('allowedLocations')]' je typ "Array", očekávaný typ je "String".

Očekávaný **typ** podmínky `equals` je _řetězec_. Vzhledem k **tomu, že allowedLocations** je definovánjako _pole_ **typu** , modul zásad vyhodnotí výraz jazyka a vyvolá chybu. S `in` podmínkou a `notIn` modul zásad očekává _pole_ **typu** ve výrazu jazyka. Chcete-li tuto chybovou `in` zprávu `notIn`vyřešit, změňte `equals` na buď nebo .

### <a name="evaluating-the--alias"></a>Vyhodnocení aliasu [*]

Aliasy, ** \[ \* ** které jsou připojeny k jejich názvu označují **typ** je _pole_. Namísto vyhodnocení hodnoty celého pole ** \[ \* ** umožňuje vyhodnotit každý prvek pole jednotlivě, s logickým and mezi nimi. Existují tři standardní scénáře, které je užitečné pro každé vyhodnocení položky: _Žádné_, _Any_nebo _Všechny_ prvky se shodují. Pro složité scénáře použijte [count](../concepts/definition-structure.md#count).

Modul zásad spustí **efekt** v **pak** pouze v **případě, že if** pravidlo vyhodnotí jako true.
Tato skutečnost je důležité pochopit v ** \[ \* ** kontextu způsobu vyhodnocuje každý jednotlivý prvek pole.

Příklad pravidla zásad pro níže uvedenou tabulku scénářů:

```json
"policyRule": {
    "if": {
        "allOf": [
            {
                "field": "Microsoft.Storage/storageAccounts/networkAcls.ipRules",
                "exists": "true"
            },
            <-- Condition (see table below) -->
        ]
    },
    "then": {
        "effect": "[parameters('effectType')]"
    }
}
```

Pole **ipRules** je následující pro následující tabulku scénářů:

```json
"ipRules": [
    {
        "value": "127.0.0.1",
        "action": "Allow"
    },
    {
        "value": "192.168.1.1",
        "action": "Allow"
    }
]
```

Pro každý příklad podmínky `<field>` `"field": "Microsoft.Storage/storageAccounts/networkAcls.ipRules[*].value"`níže nahraďte .

Následující výsledky jsou výsledkem kombinace podmínky a ukázkového pravidla zásad a pole existujících hodnot výše:

|Podmínka |Výsledek | Scénář |Vysvětlení |
|-|-|-|-|
|`{<field>,"notEquals":"127.0.0.1"}` |Nothing |Žádná shoda |Jeden prvek pole vyhodnocuje jako false (127.0.0.1 != 127.0.0.1) a jeden jako true (127.0.0.1 != 192.168.1.1), takže **podmínka notEquals** je _false_ a efekt není spuštěn. |
|`{<field>,"notEquals":"10.0.4.1"}` |Efekt zásad |Žádná shoda |Oba prvky pole vyhodnotit jako true (10.0.4.1 != 127.0.0.1 a 10.0.4.1 != 192.168.1.1), takže **notEquals** podmínka je _true_ a efekt je spuštěn. |
|`"not":{<field>,"notEquals":"127.0.0.1" }` |Efekt zásad |Jedna nebo více shod |Jeden element pole vyhodnocuje jako false (127.0.0.1 != 127.0.0.1) a jeden jako true (127.0.0.1 != 192.168.1.1), takže **podmínka notEquals** je _false_. Logický operátor vyhodnotí jako true (**není** _false_), takže efekt se aktivuje. |
|`"not":{<field>,"notEquals":"10.0.4.1"}` |Nothing |Jedna nebo více shod |Oba prvky pole vyhodnotit jako true (10.0.4.1 != 127.0.0.1 a 10.0.4.1 != 192.168.1.1), takže **notEquals** podmínka je _true_. Logický operátor vyhodnotí jako false (**není** _pravda_), takže efekt není spuštěn. |
|`"not":{<field>,"Equals":"127.0.0.1"}` |Efekt zásad |Ne všechny odpovídají |Jeden prvek pole vyhodnocuje jako true (127.0.0.1 == 127.0.0.1) a jeden jako false (127.0.0.1 == 192.168.1.1), takže podmínka **Equals** je _false_. Logický operátor vyhodnotí jako true (**není** _false_), takže efekt se aktivuje. |
|`"not":{<field>,"Equals":"10.0.4.1"}` |Efekt zásad |Ne všechny odpovídají |Oba prvky pole vyhodnotit jako false (10.0.4.1 == 127.0.0.1 a 10.0.4.1 == 192.168.1.1), takže **Equals** podmínka je _false_. Logický operátor vyhodnotí jako true (**není** _false_), takže efekt se aktivuje. |
|`{<field>,"Equals":"127.0.0.1"}` |Nothing |Všechny shody |Jeden element pole vyhodnocuje jako true (127.0.0.1 == 127.0.0.1) a jeden jako false (127.0.0.1 == 192.168.1.1), takže **podmínka Equals** je _false_ a efekt není spuštěn. |
|`{<field>,"Equals":"10.0.4.1"}` |Nothing |Všechny shody |Oba prvky pole vyhodnotit jako false (10.0.4.1 == 127.0.0.1 a 10.0.4.1 == 192.168.1.1), takže **Equals** podmínka je _false_ a efekt není spuštěn. |

## <a name="the-append-effect-and-arrays"></a>Efekt připojení a pole

[Efekt apendise](../concepts/effects.md#append) se chová odlišně v závislosti ** \[ \* ** na tom, zda je **pole details.field** alias nebo ne.

- Pokud není ** \[ \* ** alias, připojí se k celému poli vlastnost **value**
- Když alias, připojit přidá **vlastnost value** do existujícího pole nebo vytvoří nové pole ** \[ \* **

Další informace naleznete v [příkladech připojení](../concepts/effects.md#append-examples).

## <a name="next-steps"></a>Další kroky

- Projděte si příklady na [ukázkách zásad Azure](../samples/index.md).
- Projděte si [strukturu definic Azure Policy](../concepts/definition-structure.md).
- Projděte si [Vysvětlení efektů zásad](../concepts/effects.md).
- Pochopit, jak [programově vytvářet zásady](programmatically-create.md).
- Přečtěte si, jak [napravit nekompatibilní prostředky](remediate-resources.md).
- Zkontrolujte, co je skupina pro správu [pomocí organizace Uspořádat prostředky pomocí skupin pro správu Azure](../../management-groups/overview.md).
