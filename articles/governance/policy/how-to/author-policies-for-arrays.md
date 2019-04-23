---
title: Vytvořit zásady pro vlastnosti pole pro prostředky Azure
description: Další informace k vytvoření pole parametrů, vytvořit pravidla pro pole výrazy jazyka, vyhodnocení alias [*] a k přidání prvků do existujícího pole s pravidly definic Azure Policy.
author: DCtheGeek
ms.author: dacoulte
ms.date: 03/06/2019
ms.topic: conceptual
ms.service: azure-policy
manager: carmonm
ms.openlocfilehash: 38cf6decb8e61768faa9680058f6366e1550ba40
ms.sourcegitcommit: c174d408a5522b58160e17a87d2b6ef4482a6694
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/18/2019
ms.locfileid: "59793268"
---
# <a name="author-policies-for-array-properties-on-azure-resources"></a>Vytvořit zásady pro vlastnosti pole pro prostředky Azure

Azure Resource Manageru vlastnosti jsou běžně definované jako řetězce a logické hodnoty. Pokud mezi doménami existuje vztah jeden mnoho komplexní vlastnosti se místo toho definují jako pole. Ve službě Azure Policy pole se používají v několika různými způsoby:

- Typ [parametr definice](../concepts/definition-structure.md#parameters), k poskytování více možností
- Část [pravidlo zásad](../concepts/definition-structure.md#policy-rule) použití podmínek **v** nebo **notIn**
- Součástí pravidla zásad, který se vyhodnotí [ \[ \* \] alias](../concepts/definition-structure.md#understanding-the--alias) vyhodnotit konkrétní scénáře, jako **žádný**, **jakékoli**, nebo  **Všechny**
- V [přidat efekt](../concepts/effects.md#append) nahradit nebo přidat do existujícího pole

Tento článek popisuje každé použití Azure Policy a poskytuje několik definic příklad.

## <a name="parameter-arrays"></a>Pole parametrů

### <a name="define-a-parameter-array"></a>Definování pole parametrů.

Definování parametr jako pole umožňuje flexibilitu zásad, když je nutný více než jednu hodnotu.
Tato definice zásady umožňuje libovolné jednotné místo pro parametr **allowedLocations** a výchozí hodnota je _eastus2_:

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

Jako **typ** byl _řetězec_pouze jednu hodnotu můžete nastavit při přiřazování zásady. Pokud tyto zásady přiřazeny, prostředky v oboru jsou povolené jenom v rámci jedné oblasti Azure. Většina definice zásad musí mít obousměrně seznam schválených možnosti, například můžete umožnit _eastus2_, _eastus_, a _westus2_.

Chcete-li vytvořit definici zásady pro povolení více možností, použijte _pole_ **typ**. Stejná zásada může být přepsán následujícím způsobem:

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
> Po uložení definici zásady **typ** vlastnost na parametru nelze změnit.

Tato nová definice parametru má více než jednu hodnotu při přiřazování zásady. S vlastností pole **allowedValues** definované, jsou k dispozici při přiřazování hodnot dále omezena na předdefinované seznamu nabízených možností. Použití **allowedValues** je volitelný.

### <a name="pass-values-to-a-parameter-array-during-assignment"></a>Předání hodnot pro pole parametrů během přiřazení

Při přiřazování zásady prostřednictvím webu Azure portal, parametr **typ** _pole_ se zobrazí jako jedné textové pole. Pomocný parametr říká "použije. oddělte hodnoty. (např. London; New York) ". Předání hodnoty povolených umístění _eastus2_, _eastus_, a _westus2_ do parametru, použijte následující řetězec:

`eastus2;eastus;westus2`

Formát pro hodnoty parametru se liší, při použití rozhraní příkazového řádku Azure, Azure Powershellu nebo rozhraní REST API. Řetězec formátu JSON, který také obsahuje název parametru se předaly hodnoty.

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

Chcete-li použít tento řetězec s jednotlivých sadách SDK, použijte následující příkazy:

- Azure CLI: Příkaz [vytvoření přiřazení zásady az](/cli/azure/policy/assignment?view=azure-cli-latest#az-policy-assignment-create) s parametrem **parametry**
- Azure PowerShell: Rutiny [New-AzPolicyAssignment](/powershell/module/az.resources/New-Azpolicyassignment) s parametrem **PolicyParameter**
- REST API: V _UMÍSTIT_ [vytvořit](/rest/api/resources/policyassignments/create) operace jako součást text žádosti jako hodnotu **properties.parameters** vlastnost

## <a name="policy-rules-and-arrays"></a>Pravidla zásad a polí

### <a name="array-conditions"></a>Pole podmínky

Pravidlo zásad [podmínky](../concepts/definition-structure.md#conditions) , která _pole_
**typ** parametru může být použit s je omezená na `in` a `notIn`. Využijte následující definice zásad s podmínkou `equals` jako příklad:

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

Došlo k pokusu o vytvoření tato definice zásady pomocí Azure portal vede k chybě například tato chybová zpráva:

- "Zásady {GUID} se nepovedlo parametrizovat kvůli chybám při ověření. Zkontrolujte prosím, pokud jsou parametry zásad správně definované. Vnitřní výjimku, kde "vyhodnocení výsledek výrazu jazyka [parameters('allowedLocations')] je typ"Pole"očekávaných je typ"Řetězec""."

Očekávané **typ** podmínky `equals` je _řetězec_. Protože **allowedLocations** je definován jako **typ** _pole_, modul zásad vyhodnotí výraz jazyka a vyvolá chybu. S `in` a `notIn` podmínku, očekává, že modul zásad **typ** _pole_ ve výrazu jazyka. Chcete-li vyřešit tuto chybovou zprávu, změňte `equals` buď `in` nebo `notIn`.

### <a name="evaluating-the--alias"></a>Vyhodnocení alias [*]

Aliasy, které mají **[\*]** připojené k jejich název označení **typ** je _pole_. Namísto vyhodnocování hodnota celého pole **[\*]** díky tomu je možné vyhodnotit každý prvek pole. Existují tři scénáře, které to za vyhodnocení položky je užitečné v: NONE, libovolné a všechny.

Aktivační procedury modulu zásad **efekt** v **pak** pouze tehdy, když **Pokud** pravidlo vyhodnotí jako true.
Je důležité pochopit v kontextu tak, jak tuto skutečnost **[\*]** vyhodnocuje každý jednotlivý prvek pole.

Příklad pravidla zásad níže uvedené tabulce scénář:

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

**IpRules** pole je níže uvedené tabulce scénář následujícím způsobem:

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

Pro každou podmínku následujícím příkladu nahraďte `<field>` s `"field": "Microsoft.Storage/storageAccounts/networkAcls.ipRules[*].value"`.

Následující výsledky jsou výsledkem kombinace podmínku a příklad pravidla zásad a pole hodnot existující výše:

|Podmínka |Výsledek |Vysvětlení |
|-|-|-|
|`{<field>,"notEquals":"127.0.0.1"}` |Nic |Jeden element pole vyhodnotí jako false (127.0.0.1! = 127.0.0.1) a jako hodnotu true (127.0.0.1! = 192.168.1.1), takže **notEquals** podmínka je _false_ a účinek neaktivuje. |
|`{<field>,"notEquals":"10.0.4.1"}` |Účinku zásad |Oba prvky pole vyhodnotí jako true (10.0.4.1! = 127.0.0.1 a 10.0.4.1! = 192.168.1.1), takže **notEquals** podmínka je _true_ a účinek se aktivuje. |
|`"not":{<field>,"Equals":"127.0.0.1"}` |Účinku zásad |Jeden element pole vyhodnotí jako true (127.0.0.1 == 127.0.0.1) a jednu hodnotu false (127.0.0.1 == 192.168.1.1), takže **rovná** podmínka je _false_. Logický operátor, který se vyhodnotí jako true (**není** _false_), takže se aktivuje efekt. |
|`"not":{<field>,"Equals":"10.0.4.1"}` |Účinku zásad |Oba prvky pole vyhodnotí jako false (10.0.4.1 == 127.0.0.1 a 10.0.4.1 == 192.168.1.1), takže **rovná** podmínka je _false_. Logický operátor, který se vyhodnotí jako true (**není** _false_), takže se aktivuje efekt. |
|`"not":{<field>,"notEquals":"127.0.0.1" }` |Účinku zásad |Jeden element pole vyhodnotí jako false (127.0.0.1! = 127.0.0.1) a jako hodnotu true (127.0.0.1! = 192.168.1.1), takže **notEquals** podmínka je _false_. Logický operátor, který se vyhodnotí jako true (**není** _false_), takže se aktivuje efekt. |
|`"not":{<field>,"notEquals":"10.0.4.1"}` |Nic |Oba prvky pole vyhodnotí jako true (10.0.4.1! = 127.0.0.1 a 10.0.4.1! = 192.168.1.1), takže **notEquals** podmínka je _true_. Logický operátor, který se vyhodnotí jako false (**není** _true_), takže efekt neaktivuje. |
|`{<field>,"Equals":"127.0.0.1"}` |Nic |Jeden element pole vyhodnotí jako true (127.0.0.1 == 127.0.0.1) a jednu hodnotu false (127.0.0.1 == 192.168.1.1), takže **rovná** podmínka je _false_ a účinek neaktivuje. |
|`{<field>,"Equals":"10.0.4.1"}` |Nic |Oba prvky pole vyhodnotí jako false (10.0.4.1 == 127.0.0.1 a 10.0.4.1 == 192.168.1.1), takže **rovná** podmínka je _false_ a účinek neaktivuje. |

## <a name="the-append-effect-and-arrays"></a>Přidat efekt a pole

[Přidat efekt](../concepts/effects.md#append) chová odlišně podle toho, pokud **details.field** je **[\*]** alias nebo ne.

- Pokud není **[\*]** alias, připojte nahradí celého pole s **hodnotu** vlastnost
- Při **[\*]** alias, připojte přidá **hodnotu** vlastnosti pro existující pole nebo vytvoří nové pole

Další informace najdete v tématu [přidat příklady](../concepts/effects.md#append-examples).

## <a name="next-steps"></a>Další postup

- Projděte si příklady v [ukázek Azure Policy](../samples/index.md)
- Zkontrolujte [struktura definic zásad](../concepts/definition-structure.md)
- Kontrola [Principy účinky zásad](../concepts/effects.md)
- Pochopit postup [programové vytváření zásad](programmatically-create.md)
- Zjistěte, jak [opravit nekompatibilní prostředky](remediate-resources.md)
- Připomenutí skupin pro správu v článku [Uspořádání prostředků pomocí skupin pro správu Azure](../../management-groups/overview.md)