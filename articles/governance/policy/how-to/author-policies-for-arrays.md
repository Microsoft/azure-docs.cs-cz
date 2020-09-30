---
title: Vytváření zásad pro vlastnosti polí u prostředků
description: Naučte se pracovat s parametry pole a výrazy jazyka pole, vyhodnotit alias [*] a přidat prvky pomocí pravidel Definice Azure Policy.
ms.date: 09/30/2020
ms.topic: how-to
ms.openlocfilehash: c67982197c0161d99f29747d6fd11166cba86079
ms.sourcegitcommit: a422b86148cba668c7332e15480c5995ad72fa76
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/30/2020
ms.locfileid: "91576893"
---
# <a name="author-policies-for-array-properties-on-azure-resources"></a>Vytváření zásad pro vlastnosti pole v prostředcích Azure

Vlastnosti Azure Resource Manager jsou běžně definovány jako řetězce a logické hodnoty. Když existuje vztah 1: n, místo toho se jako pole definují komplexní vlastnosti. V Azure Policy se pole používají několika různými způsoby:

- Typ [parametru definice](../concepts/definition-structure.md#parameters), který poskytuje více možností
- Součást [pravidla zásad](../concepts/definition-structure.md#policy-rule) s použitím podmínek **v** nebo **notIn**
- Součást pravidla zásad, které vyhodnocuje [ \[ \* \] alias](../concepts/definition-structure.md#understanding-the--alias) k vyhodnocení:
  - Scénáře, jako je **none**, **Any**nebo **All**
  - Komplexní scénáře s **počtem**
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

Pokud chcete vytvořit definici zásady, která povoluje více možností, použijte _array_ **typ**pole. Stejné zásady je možné přepsat následujícím způsobem:

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

- Azure CLI: příkaz [AZ Policy Assignment Create](/cli/azure/policy/assignment#az-policy-assignment-create) with Parameter **param**
- Azure PowerShell: rutina [New-AzPolicyAssignment](/powershell/module/az.resources/New-Azpolicyassignment) s parametrem **PolicyParameter**
- REST API: v rámci těla požadavku jako hodnota vlastnosti **Properties. Parameters** _v rámci textu_ [create](/rest/api/resources/policyassignments/create) žádosti.

## <a name="policy-rules-and-arrays"></a>Pravidla a pole zásad

### <a name="array-conditions"></a>Podmínky pole

[Podmínky](../concepts/definition-structure.md#conditions) pravidla zásad, které _array_ 
 mohou být použity jako**typ** pole, jsou omezeny na `in` a `notIn` . Následující definici zásad proveďte `equals` jako příklad:

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

Očekávaným **typem** podmínky `equals` je _řetězec_. Vzhledem k tomu, že **allowedLocations** je definován jako _pole_ **typu** , modul zásad vyhodnotí výraz jazyka a vyvolá chybu. V případě `in` `notIn` podmínky a modul zásad očekává _pole_ **typu** ve výrazu jazyka. Chcete-li tuto chybovou zprávu vyřešit, změňte `equals` hodnotu na `in` nebo `notIn` .

### <a name="evaluating-the--alias"></a>Vyhodnocení aliasu [*]

Aliasy, které jsou **\[\*\]** připojeny k jejich názvu, označují **typ** je _pole_. Místo vyhodnocení hodnoty celého pole je **\[\*\]** možné vyhodnotit každý prvek pole jednotlivě, s logickým a mezi nimi. Existují tři standardní scénáře, které jsou pro vyhodnocení každé položky užitečné: _žádné_, _žádné_nebo _všechny_ prvky se shodují. U složitých scénářů použijte [počet](../concepts/definition-structure.md#count).

Modul zásad aktivuje **efekt** v **a pak** jenom v případě, že se pravidlo **if** vyhodnotí jako true.
Tento fakt je důležitý pro pochopení v kontextu způsobu, jak **\[\*\]** vyhodnotit každý jednotlivý prvek pole.

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

Pro každý příklad podmínky Nahraďte parametr `<field>` `"field": "Microsoft.Storage/storageAccounts/networkAcls.ipRules[*].value"` .

Následující výsledky jsou výsledkem kombinace podmínky a ukázkového pravidla zásad a pole stávajících hodnot výše:

|Stav |Výsledek | Scénář |Vysvětlení |
|-|-|-|-|
|`{<field>,"notEquals":"127.0.0.1"}` |Nothing |Žádná shoda |Jeden prvek pole se vyhodnotí jako false (127.0.0.1! = 127.0.0.1) a jeden jako true (127.0.0.1! = 192.168.1.1), takže podmínka **notEquals** je _nepravdivá_ a efekt se neaktivuje. |
|`{<field>,"notEquals":"10.0.4.1"}` |Vliv na zásady |Žádná shoda |Obě prvky pole se vyhodnocují jako true (10.0.4.1! = 127.0.0.1 a 10.0.4.1! = 192.168.1.1), takže podmínka **notEquals** je _pravdivá_ a výsledek se aktivuje. |
|`"not":{<field>,"notEquals":"127.0.0.1" }` |Vliv na zásady |Jedna nebo více shod |Jeden prvek pole se vyhodnotí jako false (127.0.0.1! = 127.0.0.1) a jeden jako true (127.0.0.1! = 192.168.1.1), takže podmínka **notEquals** je _NEPRAVDA_. Logický operátor se vyhodnotí jako true (**ne** _false_), takže se efekt aktivuje. |
|`"not":{<field>,"notEquals":"10.0.4.1"}` |Nothing |Jedna nebo více shod |Obě prvky pole jsou vyhodnoceny jako true (10.0.4.1! = 127.0.0.1 a 10.0.4.1! = 192.168.1.1), takže podmínka **notEquals** je _pravdivá_. Logický operátor se vyhodnotí jako false (**ne** _true_), takže se efekt neaktivuje. |
|`"not":{<field>,"Equals":"127.0.0.1"}` |Vliv na zásady |Neshoda |Jeden prvek pole se vyhodnotí jako true (127.0.0.1 = = 127.0.0.1) a jeden jako false (127.0.0.1 = = 192.168.1.1), takže podmínka **Equals** je _false_. Logický operátor se vyhodnotí jako true (**ne** _false_), takže se efekt aktivuje. |
|`"not":{<field>,"Equals":"10.0.4.1"}` |Vliv na zásady |Neshoda |Obě prvky pole jsou vyhodnoceny jako false (10.0.4.1 = = 127.0.0.1 a 10.0.4.1 = = 192.168.1.1), takže podmínka **Equals** je _false_. Logický operátor se vyhodnotí jako true (**ne** _false_), takže se efekt aktivuje. |
|`{<field>,"Equals":"127.0.0.1"}` |Nothing |Všechny shody |Jeden prvek pole se vyhodnotí jako true (127.0.0.1 = = 127.0.0.1) a jeden jako false (127.0.0.1 = = 192.168.1.1), takže podmínka **Equals** je _false_ a efekt se neaktivuje. |
|`{<field>,"Equals":"10.0.4.1"}` |Nothing |Všechny shody |Obě prvky pole jsou vyhodnoceny jako false (10.0.4.1 = = 127.0.0.1 a 10.0.4.1 = = 192.168.1.1), takže podmínka **Equals** je _false_ a účinek není aktivován. |

## <a name="modifying-arrays"></a>Úprava polí

Příkazy [připojit](../concepts/effects.md#append) a [Upravit](../concepts/effects.md#modify) vlastnosti prostředku během vytváření nebo aktualizace. Při práci s vlastnostmi pole závisí chování těchto efektů na tom, zda se operace pokouší změnit  **\[\*\]** alias nebo ne:

> [!NOTE]
> Použití `modify` efektu s aliasy je v současnosti ve **verzi Preview**.

|Alias |Účinek | Výsledek |
|-|-|-|
| `Microsoft.Storage/storageAccounts/networkAcls.ipRules` | `append` | Pokud chybí, Azure Policy připojí celé pole zadané v podrobnostech o účinku. |
| `Microsoft.Storage/storageAccounts/networkAcls.ipRules` | `modify` s `add` operací | Pokud chybí, Azure Policy připojí celé pole zadané v podrobnostech o účinku. |
| `Microsoft.Storage/storageAccounts/networkAcls.ipRules` | `modify` s `addOrReplace` operací | Azure Policy připojí celé pole zadané v podrobnostech o účinku, pokud chybí nebo nahradí existující pole. |
| `Microsoft.Storage/storageAccounts/networkAcls.ipRules[*]` | `append` | Azure Policy připojí člena pole zadaného v podrobnostech o vlivu. |
| `Microsoft.Storage/storageAccounts/networkAcls.ipRules[*]` | `modify` s `add` operací | Azure Policy připojí člena pole zadaného v podrobnostech o vlivu. |
| `Microsoft.Storage/storageAccounts/networkAcls.ipRules[*]` | `modify` s `addOrReplace` operací | Azure Policy odebere všechny existující členy pole a připojí člena pole zadaného v podrobnostech o účinku. |
| `Microsoft.Storage/storageAccounts/networkAcls.ipRules[*].action` | `append` | Azure Policy připojí hodnotu k `action` vlastnosti každého člena pole. |
| `Microsoft.Storage/storageAccounts/networkAcls.ipRules[*].action` | `modify` s `add` operací | Azure Policy připojí hodnotu k `action` vlastnosti každého člena pole. |
| `Microsoft.Storage/storageAccounts/networkAcls.ipRules[*].action` | `modify` s `addOrReplace` operací | Azure Policy připojí nebo nahradí existující `action` vlastnost každého člena pole. |

Další informace najdete v [příkladech připojení](../concepts/effects.md#append-examples).

## <a name="next-steps"></a>Další kroky

- Přečtěte si příklady na [Azure Policy Samples](../samples/index.md).
- Projděte si [strukturu definic Azure Policy](../concepts/definition-structure.md).
- Projděte si [Vysvětlení efektů zásad](../concepts/effects.md).
- Zjistěte, jak [programově vytvářet zásady](programmatically-create.md).
- Přečtěte si, jak [opravit prostředky, které nedodržují předpisy](remediate-resources.md).
- Seznamte se s tím, co skupina pro správu [organizuje vaše prostředky pomocí skupin pro správu Azure](../../management-groups/overview.md).
