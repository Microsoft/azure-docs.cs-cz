---
title: Vytváření zásad pro vlastnosti polí u prostředků
description: Naučte se pracovat s parametry pole a výrazy jazyka pole, vyhodnotit alias [*] a přidat prvky pomocí pravidel Definice Azure Policy.
ms.date: 03/06/2019
ms.topic: conceptual
ms.openlocfilehash: 96598918f0dbcc2f56e8ccc316844ee768306b75
ms.sourcegitcommit: 95931aa19a9a2f208dedc9733b22c4cdff38addc
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/25/2019
ms.locfileid: "74463499"
---
# <a name="author-policies-for-array-properties-on-azure-resources"></a>Vytváření zásad pro vlastnosti pole v prostředcích Azure

Vlastnosti Azure Resource Manager jsou běžně definovány jako řetězce a logické hodnoty. Když existuje vztah 1: n, místo toho se jako pole definují komplexní vlastnosti. V Azure Policy se pole používají několika různými způsoby:

- Typ [parametru definice](../concepts/definition-structure.md#parameters), který poskytuje více možností
- Součást [pravidla zásad](../concepts/definition-structure.md#policy-rule) s použitím podmínek **v** nebo **notIn**
- Součást pravidla zásad, které vyhodnocuje [\[\*\] alias](../concepts/definition-structure.md#understanding-the--alias) pro vyhodnocení konkrétních scénářů, jako jsou **none**, **Any**nebo **All**
- V [efektu připojit](../concepts/effects.md#append) , který se má nahradit nebo přidat k existujícímu poli

Tento článek se zabývá každým použitím Azure Policy a poskytuje několik ukázkových definic.

## <a name="parameter-arrays"></a>Pole parametrů

### <a name="define-a-parameter-array"></a>Definovat pole parametrů

Definování parametru jako pole umožní flexibilitu v případě, že je potřeba zadat víc než jednu hodnotu.
Tato definice zásady umožňuje, aby bylo každé jedno umístění pro parametr **allowedLocations** a výchozí hodnota _eastus2_:

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

Jako _řetězec_ **typu** se dá nastavit jenom jedna hodnota při přiřazování zásady. Pokud je tato zásada přiřazena, prostředky v oboru jsou povoleny pouze v rámci jedné oblasti Azure. Většina definic zásad musí povolit seznam schválených možností, jako je například povolování _eastus2_, _eastus_a _westus2_.

Pokud chcete vytvořit definici zásady, která povoluje více možností, použijte **typ**pole. Stejné zásady je možné přepsat následujícím způsobem:

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
> Po uložení definice zásady nejde změnit vlastnost **typu** u parametru.

Tato nová definice parametru během přiřazování zásad trvá víc než jednu hodnotu. Po definování **allowedValues** vlastností pole jsou hodnoty dostupné během přiřazování dál omezeny na předdefinovaný seznam voleb. Použití **allowedValues** je volitelné.

### <a name="pass-values-to-a-parameter-array-during-assignment"></a>Předání hodnot do pole parametru během přiřazení

Při přiřazování zásad prostřednictvím Azure Portal se jako jedno textové pole zobrazí parametr **typu** _Array_ . Nápověda říká "use; pro oddělení hodnot. (např. Londýn; New York) ". Chcete-li předat povoleným hodnotám umístění _eastus2_, _eastus_a _westus2_ parametru, použijte následující řetězec:

`eastus2;eastus;westus2`

Formát hodnoty parametru se při použití rozhraní příkazového řádku Azure, Azure PowerShell nebo REST API liší. Hodnoty jsou předány řetězcem JSON, který obsahuje také název parametru.

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

Chcete-li použít tento řetězec pro každou sadu SDK, použijte následující příkazy:

- Azure CLI: příkaz [AZ Policy Assignment Create](/cli/azure/policy/assignment?view=azure-cli-latest#az-policy-assignment-create) with Parameter **param**
- Azure PowerShell: rutina [New-AzPolicyAssignment](/powershell/module/az.resources/New-Azpolicyassignment) s parametrem **PolicyParameter**
- REST API: v rámci těla požadavku jako hodnota vlastnosti **Properties. Parameters** _v rámci textu_ [](/rest/api/resources/policyassignments/create) žádosti.

## <a name="policy-rules-and-arrays"></a>Pravidla a pole zásad

### <a name="array-conditions"></a>Podmínky pole

[Podmínky](../concepts/definition-structure.md#conditions) pravidla zásad, které je možné použít jako **typ** parametru _Array_
, jsou omezené na `in` a `notIn`. Jako příklad proveďte následující definici zásady s podmínkou `equals`:

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

Při pokusu o vytvoření této definice zásady prostřednictvím Azure Portal vede k chybě, například k této chybové zprávě:

- Zásady {GUID} nešlo parametrizované kvůli chybám ověření. Zkontrolujte prosím, jestli jsou parametry zásad správně definované. Výsledek vyhodnocení vnitřní výjimky pro výraz jazyka [Parameters (' allowedLocations ')] je typu Array, očekával se typ String.

Očekávaným **typem** podmínky `equals` je _řetězec_. Vzhledem k tomu, že **allowedLocations** je definován jako _pole_ **typu** , modul zásad vyhodnotí výraz jazyka a vyvolá chybu. V případě `in` a `notIn` podmínky očekává modul zásad ve výrazu jazyka _pole_ typu. Chcete-li tuto chybovou zprávu vyřešit, změňte `equals` na `in` nebo `notIn`.

### <a name="evaluating-the--alias"></a>Vyhodnocení aliasu [*]

Aliasy, které mají **[\*]** připojené k jejich názvu, označují, že **typ** je _pole_. Místo vyhodnocení hodnoty celého pole, **[\*]** , umožňuje vyhodnotit každý prvek pole. Existují tři scénáře, které jsou pro vyhodnocení každé položky užitečné: žádné, žádné a všechny.

Modul zásad aktivuje **efekt** v **a pak** jenom v případě, že se pravidlo **if** vyhodnotí jako true.
Tento fakt je důležité pochopit v kontextu způsobu, jakým **[\*]** vyhodnocuje každý jednotlivý prvek pole.

Příklad pravidla zásad pro tabulku scénář:

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

Pole **ipRules** je pro následující tabulku scénář následující:

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

U každého příkladu podmínky nahraďte `<field>` `"field": "Microsoft.Storage/storageAccounts/networkAcls.ipRules[*].value"`.

Následující výsledky jsou výsledkem kombinace podmínky a ukázkového pravidla zásad a pole stávajících hodnot výše:

|Podmínka |Výsledek |Vysvětlení |
|-|-|-|
|`{<field>,"notEquals":"127.0.0.1"}` |Žádným |Jeden prvek pole se vyhodnotí jako false (127.0.0.1! = 127.0.0.1) a jeden jako true (127.0.0.1! = 192.168.1.1), takže podmínka **notEquals** je _nepravdivá_ a efekt se neaktivuje. |
|`{<field>,"notEquals":"10.0.4.1"}` |Vliv na zásady |Obě prvky pole se vyhodnocují jako true (10.0.4.1! = 127.0.0.1 a 10.0.4.1! = 192.168.1.1), takže podmínka **notEquals** je _pravdivá_ a výsledek se aktivuje. |
|`"not":{<field>,"Equals":"127.0.0.1"}` |Vliv na zásady |Jeden prvek pole se vyhodnotí jako true (127.0.0.1 = = 127.0.0.1) a jeden jako false (127.0.0.1 = = 192.168.1.1), takže podmínka **Equals** je _false_. Logický operátor se vyhodnotí jako true (**ne** _false_), takže se efekt aktivuje. |
|`"not":{<field>,"Equals":"10.0.4.1"}` |Vliv na zásady |Obě prvky pole jsou vyhodnoceny jako false (10.0.4.1 = = 127.0.0.1 a 10.0.4.1 = = 192.168.1.1), takže podmínka **Equals** je _false_. Logický operátor se vyhodnotí jako true (**ne** _false_), takže se efekt aktivuje. |
|`"not":{<field>,"notEquals":"127.0.0.1" }` |Vliv na zásady |Jeden prvek pole se vyhodnotí jako false (127.0.0.1! = 127.0.0.1) a jeden jako true (127.0.0.1! = 192.168.1.1), takže podmínka **notEquals** je _NEPRAVDA_. Logický operátor se vyhodnotí jako true (**ne** _false_), takže se efekt aktivuje. |
|`"not":{<field>,"notEquals":"10.0.4.1"}` |Žádným |Obě prvky pole jsou vyhodnoceny jako true (10.0.4.1! = 127.0.0.1 a 10.0.4.1! = 192.168.1.1), takže podmínka **notEquals** je _pravdivá_. Logický operátor se vyhodnotí jako false (**ne** _true_), takže se efekt neaktivuje. |
|`{<field>,"Equals":"127.0.0.1"}` |Žádným |Jeden prvek pole se vyhodnotí jako true (127.0.0.1 = = 127.0.0.1) a jeden jako false (127.0.0.1 = = 192.168.1.1), takže podmínka **Equals** je _false_ a efekt se neaktivuje. |
|`{<field>,"Equals":"10.0.4.1"}` |Žádným |Obě prvky pole jsou vyhodnoceny jako false (10.0.4.1 = = 127.0.0.1 a 10.0.4.1 = = 192.168.1.1), takže podmínka **Equals** je _false_ a účinek není aktivován. |

## <a name="the-append-effect-and-arrays"></a>Efekt připojení a pole

[Efekt připojení](../concepts/effects.md#append) se chová odlišně v závislosti na tom, zda je v **poli podrobnosti** alias **[\*]** .

- Pokud se nejedná o alias **[\*]** , příkaz append nahradí celé pole vlastností **Value** .
- Když alias **[\*]** přidá vlastnost **Value** do existujícího pole nebo vytvoří nové pole.

Další informace najdete v [příkladech připojení](../concepts/effects.md#append-examples).

## <a name="next-steps"></a>Další kroky

- Přečtěte si příklady na [Azure Policy Samples](../samples/index.md).
- Projděte si [strukturu definic Azure Policy](../concepts/definition-structure.md).
- Projděte si [Vysvětlení efektů zásad](../concepts/effects.md).
- Zjistěte, jak [programově vytvářet zásady](programmatically-create.md).
- Přečtěte si, jak [opravit prostředky, které nedodržují předpisy](remediate-resources.md).
- Seznamte se s tím, co skupina pro správu [organizuje vaše prostředky pomocí skupin pro správu Azure](../../management-groups/overview.md).