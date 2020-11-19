---
title: Vytváření zásad pro vlastnosti polí u prostředků
description: Naučte se pracovat s parametry pole a výrazy jazyka pole, vyhodnotit alias [*] a přidat prvky pomocí pravidel Definice Azure Policy.
ms.date: 10/22/2020
ms.topic: how-to
ms.openlocfilehash: 60044d4a599c14088ea923a6a14cb46543646995
ms.sourcegitcommit: 03c0a713f602e671b278f5a6101c54c75d87658d
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/19/2020
ms.locfileid: "94920453"
---
# <a name="author-policies-for-array-properties-on-azure-resources"></a>Vytváření zásad pro vlastnosti pole v prostředcích Azure

Vlastnosti Azure Resource Manager jsou běžně definovány jako řetězce a logické hodnoty. Když existuje vztah 1: n, místo toho se jako pole definují komplexní vlastnosti. V Azure Policy se pole používají několika různými způsoby:

- Typ [parametru definice](../concepts/definition-structure.md#parameters), který poskytuje více možností
- Součást [pravidla zásad](../concepts/definition-structure.md#policy-rule) s použitím podmínek **v** nebo **notIn**
- Součást pravidla zásad, které vyhodnocuje [ \[ \* \] alias](../concepts/definition-structure.md#understanding-the--alias) k vyhodnocení:
  - Scénáře, jako je **none**, **Any** nebo **All**
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

Jako _řetězec_ **typu** se dá nastavit jenom jedna hodnota při přiřazování zásady. Pokud je tato zásada přiřazena, prostředky v oboru jsou povoleny pouze v rámci jedné oblasti Azure. Většina definic zásad musí povolit seznam schválených možností, jako je například povolování _eastus2_, _eastus_ a _westus2_.

Pokud chcete vytvořit definici zásady, která povoluje více možností, použijte _array_ **typ** pole. Stejné zásady je možné přepsat následujícím způsobem:

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

Při přiřazování zásad prostřednictvím Azure Portal se jako jedno textové pole zobrazí parametr **typu** _Array_ . Nápověda říká "use; pro oddělení hodnot. (např. Londýn; New York) ". Chcete-li předat povoleným hodnotám umístění _eastus2_, _eastus_ a _westus2_ parametru, použijte následující řetězec:

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

- Azure CLI: příkaz [AZ Policy Assignment Create](/cli/azure/policy/assignment#az_policy_assignment_create) with Parameter **param**
- Azure PowerShell: rutina [New-AzPolicyAssignment](/powershell/module/az.resources/New-Azpolicyassignment) s parametrem **PolicyParameter**
- REST API: v rámci těla požadavku jako hodnota vlastnosti **Properties. Parameters** _v rámci textu_ [create](/rest/api/resources/policyassignments/create) žádosti.

## <a name="array-conditions"></a>Podmínky pole

[Podmínky](../concepts/definition-structure.md#conditions) pravidla zásad, které _array_ 
 mohou být použity jako **typ** pole, jsou omezeny na `in` a `notIn` . Následující definici zásad proveďte `equals` jako příklad:

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

## <a name="referencing-array-resource-properties"></a>Odkazy na vlastnosti prostředku pole

Mnoho případů použití vyžaduje práci s vlastnostmi pole ve vyhodnoceném prostředku. Některé scénáře vyžadují odkazování na celé pole (například na kontrolu jeho délky). Jiné vyžadují použití podmínky u jednotlivých členů pole (například zajistěte, aby všechna pravidla brány firewall blokovala přístup z Internetu). Porozumění různým způsobům, Azure Policy mohou odkazovat na vlastnosti prostředku a jak se tyto odkazy chovají, když odkazují na vlastnosti pole, je klíč pro zápis podmínek, které se vztahují k těmto scénářům.

### <a name="referencing-resource-properties"></a>Odkazování na vlastnosti prostředku
Na vlastnosti prostředku se dá odkazovat Azure Policy pomocí [aliasů](../concepts/definition-structure.md#aliases) existují dva způsoby, jak odkazovat na hodnoty vlastnosti prostředku v Azure Policy:

- Podmínka [pole](../concepts/definition-structure.md#fields) se používá ke kontrole, zda **všechny** vybrané vlastnosti prostředku splňují podmínku. Příklad:

  ```json
  {
    "field" : "Microsoft.Test/resourceType/property",
    "equals": "value"
  }
  ```

- Použijte `field()` funkci pro přístup k hodnotě vlastnosti. Příklad:

  ```json
  {
    "value": "[take(field('Microsoft.Test/resourceType/property'), 7)]",
    "equals": "prefix_"
  }
  ```

Podmínka pole má implicitní chování "vše z". Pokud alias představuje kolekci hodnot, zkontroluje, zda všechny jednotlivé hodnoty splňují podmínku. `field()`Funkce vrátí hodnoty reprezentované aliasem, jak je, což může být manipulováno jinými funkcemi šablony.

### <a name="referencing-array-fields"></a>Odkazovaná pole polí

Vlastnosti prostředků pole jsou obvykle reprezentovány dvěma různými typy aliasů. Jeden "normální" alias a [aliasy pole](../concepts/definition-structure.md#understanding-the--alias) , které jsou `[*]` k němu připojené:

- `Microsoft.Test/resourceType/stringArray`
- `Microsoft.Test/resourceType/stringArray[*]`

#### <a name="referencing-the-array"></a>Odkazování na pole

První alias představuje jednu hodnotu, hodnotu `stringArray` vlastnosti z obsahu žádosti. Vzhledem k tomu, že hodnota této vlastnosti je pole, není velmi užitečné v podmínkách zásad. Například:

```json
{
  "field": "Microsoft.Test/resourceType/stringArray",
  "equals": "..."
}
```

Tento stav porovnává celé `stringArray` pole s jednou hodnotou řetězce. Většina podmínek, včetně `equals` , akceptuje pouze řetězcové hodnoty, takže není k dispozici žádné použití při porovnávání pole s řetězcem. Hlavní scénář, kde odkazuje na vlastnost pole, je užitečný při kontrole, zda existuje:

```json
{
  "field": "Microsoft.Test/resourceType/stringArray",
  "exists": "true"
}
```

Pomocí `field()` funkce je vrácená hodnota pole z obsahu žádosti, která se pak dá použít s kteroukoli z [podporovaných funkcí šablon](../concepts/definition-structure.md#policy-functions) , které přijímají argumenty pole. Například následující podmínka kontroluje, zda `stringArray` je délka větší než 0:

```json
{
  "value": "[length(field('Microsoft.Test/resourceType/stringArray'))]",
  "greater": 0
}
```

#### <a name="referencing-the-array-members-collection"></a>Odkazování na kolekci členů pole

Aliasy, které používají `[*]` syntaxi, reprezentují **kolekci hodnot vlastností vybraných z vlastnosti pole**, která se liší od výběru samotné vlastnosti pole. V případě `Microsoft.Test/resourceType/stringArray[*]` vrátí kolekce, která má všechny členy `stringArray` . Jak již bylo zmíněno dříve, `field` Podmínka kontroluje, zda všechny vybrané vlastnosti prostředku splňují podmínku, takže následující podmínka je pravdivá pouze v případě, že se **všechny** členy `stringArray` rovnají hodnotě "".

```json
{
  "field": "Microsoft.Test/resourceType/stringArray[*]",
  "equals": "value"
}
```

Pokud pole obsahuje objekty, lze pomocí `[*]` aliasu vybrat hodnotu konkrétní vlastnosti z každého člena pole. Příklad:

```json
{
  "field": "Microsoft.Test/resourceType/objectArray[*].property",
  "equals": "value"
}
```

Tato podmínka je pravdivá, pokud jsou hodnoty všech `property` vlastností v systému `objectArray` rovny `"value"` .

Při použití `field()` funkce pro odkaz na alias pole je vrácená hodnota pole všech vybraných hodnot. Toto chování znamená, že běžným případem použití `field()` funkce je možnost použít šablony funkcí na hodnoty vlastností prostředku velmi omezená. Jediné funkce šablony, které lze použít v tomto případě jsou ty, které přijímají argumenty pole. Například je možné získat délku pole s `[length(field('Microsoft.Test/resourceType/objectArray[*].property'))]` . Nicméně složitější scénáře, jako je použití šablony funkce na jednotlivé členy pole a jejich porovnání s požadovanou hodnotou, jsou možné pouze při použití `count` výrazu. Další informace najdete v tématu [výraz Count](#count-expressions).

Chcete-li vytvořit souhrn, přečtěte si následující ukázkový obsah prostředku a vybrané hodnoty vrácené různými aliasy:

```json
{
  "tags": {
    "env": "prod"
  },
  "properties":
  {
    "stringArray": [ "a", "b", "c" ],
    "objectArray": [
      {
        "property": "value1",
        "nestedArray": [ 1, 2 ]
      },
      {
        "property": "value2",
        "nestedArray": [ 3, 4 ]
      }
    ]
  }
}
```

Při použití podmínky pole v ukázkovém obsahu prostředku jsou výsledky následující:

| Alias | Vybrané hodnoty |
|:--- |:---|
| `Microsoft.Test/resourceType/missingArray` | `null` |
| `Microsoft.Test/resourceType/missingArray[*]` | Prázdná kolekce hodnot |
| `Microsoft.Test/resourceType/missingArray[*].property` | Prázdná kolekce hodnot |
| `Microsoft.Test/resourceType/stringArray` | `["a", "b", "c"]` |
| `Microsoft.Test/resourceType/stringArray[*]` | `"a"`, `"b"`, `"c"` |
| `Microsoft.Test/resourceType/objectArray[*]` |  `{ "property": "value1", "nestedArray": [ 1, 2 ] }`,<br/>`{ "property": "value2", "nestedArray": [ 3, 4 ] }`|
| `Microsoft.Test/resourceType/objectArray[*].property` | `"value1"`, `"value2"` |
| `Microsoft.Test/resourceType/objectArray[*].nestedArray` | `[ 1, 2 ]`, `[ 3, 4 ]` |
| `Microsoft.Test/resourceType/objectArray[*].nestedArray[*]` | `1`, `2`, `3`, `4` |

Při použití `field()` funkce v ukázkovém obsahu prostředku jsou výsledky následující:

| Výraz | Vrácená hodnota |
|:--- |:---|
| `[field('Microsoft.Test/resourceType/missingArray')]` | `""` |
| `[field('Microsoft.Test/resourceType/missingArray[*]')]` | `[]` |
| `[field('Microsoft.Test/resourceType/missingArray[*].property')]` | `[]` |
| `[field('Microsoft.Test/resourceType/stringArray')]` | `["a", "b", "c"]` |
| `[field('Microsoft.Test/resourceType/stringArray[*]')]` | `["a", "b", "c"]` |
| `[field('Microsoft.Test/resourceType/objectArray[*]')]` |  `[{ "property": "value1", "nestedArray": [ 1, 2 ] }, { "property": "value2", "nestedArray": [ 3, 4 ] }]`|
| `[field('Microsoft.Test/resourceType/objectArray[*].property')]` | `["value1", "value2"]` |
| `[field('Microsoft.Test/resourceType/objectArray[*].nestedArray')]` | `[[ 1, 2 ], [ 3, 4 ]]` |
| `[field('Microsoft.Test/resourceType/objectArray[*].nestedArray[*]')]` | `[1, 2, 3, 4]` |

## <a name="count-expressions"></a>Výrazy Count

[Count](../concepts/definition-structure.md#count) – počet výrazů, kolik členů pole splňuje podmínku, a porovnání počtu s cílovou hodnotou. `Count` je intuitivnější a univerzální pro vyhodnocení polí v porovnání s `field` podmínkami. Syntaxe je:

```json
{
  "count": {
    "field": <[*] alias>,
    "where": <optional policy condition expression>
  },
  "equals|greater|less|any other operator": <target value>
}
```

Při použití bez podmínky Where `count` jednoduše vrátí délku pole. S ukázkovým obsahem prostředků z předchozí části `count` je následující výraz vyhodnocen na, `true` protože `stringArray` má tři členy:

```json
{
  "count": {
    "field": "Microsoft.Test/resourceType/stringArray[*]"
  },
  "equals": 3
}
```

Toto chování funguje i u vnořených polí. Například následující `count` výraz se vyhodnotí na, `true` protože pole obsahují čtyři členy pole `nestedArray` :

```json
{
  "count": {
    "field": "Microsoft.Test/resourceType/objectArray[*].nestedArray[*]"
  },
  "greaterOrEquals": 4
}
```

Mocnina `count` je v `where` podmínce. Je-li zadána, Azure Policy vytvoří výčet členů pole a vyhodnotí každý s podmínkou a spočítá, na kolik členů pole bylo vyhodnoceno `true` . Konkrétně při každé iteraci `where` vyhodnocení podmínky Azure Policy vybere jednoho člena pole ***i** _ a vyhodnotí obsah prostředků proti `where` podmínce _* jako if **_i_*_ je jediným členem array_ *. Když je v každé iteraci dostupný jenom jeden člen pole, poskytuje způsob, jak u každého člena pole použít komplexní podmínky.

Příklad:
```json
{
  "count": {
    "field": "Microsoft.Test/resourceType/stringArray[*]",
    "where": {
      "field": "Microsoft.Test/resourceType/stringArray[*]",
      "equals": "a"
    }
  },
  "equals": 1
}
```
Aby bylo možné vyhodnotit `count` výraz, Azure Policy vyhodnotí `where` stav 3 časy, jednou pro každého člena `stringArray` a spočítá, kolikrát byl vyhodnocen `true` . Když `where` Podmínka odkazuje na `Microsoft.Test/resourceType/stringArray[*]` členy pole, místo výběru všech členů `stringArray` , vybere pouze jeden člen pole pokaždé, když:

| Iterace | Vybrané `Microsoft.Test/resourceType/stringArray[*]` hodnoty | `where` Výsledek vyhodnocení |
|:---|:---|:---|
| 1 | `"a"` | `true` |
| 2 | `"b"` | `false` |
| 3 | `"c"` | `false` |

A proto se `count` vrátí `1` .

Tady je složitější výraz:
```json
{
  "count": {
    "field": "Microsoft.Test/resourceType/objectArray[*]",
    "where": {
      "allOf": [
        {
          "field": "Microsoft.Test/resourceType/objectArray[*].property",
          "equals": "value2"
        },
        {
          "field": "Microsoft.Test/resourceType/objectArray[*].nestedArray[*]",
          "greater": 2
        }
      ]
    }
  },
  "equals": 1
}
```

| Iterace | Vybrané hodnoty | `where` Výsledek vyhodnocení |
|:---|:---|:---|
| 1 | `Microsoft.Test/resourceType/objectArray[*].property` => `"value1"` </br> `Microsoft.Test/resourceType/objectArray[*].nestedArray[*]` => `1`, `2` | `false` |
| 2 | `Microsoft.Test/resourceType/objectArray[*].property` => `"value2"` </br> `Microsoft.Test/resourceType/objectArray[*].nestedArray[*]` => `3`, `4`| `true` |

A tak `count` vrátí `1` .

Skutečnost, že `where` výraz je vyhodnocen proti **celému** obsahu žádosti (se změnami pouze pro člena pole, který je právě vyčíslen) znamená, že `where` podmínka může také odkazovat na pole mimo pole:
```json
{
  "count": {
    "field": "Microsoft.Test/resourceType/objectArray[*]",
    "where": {
      "field": "tags.env",
      "equals": "prod"
    }
  }
}
```

| Iterace | Vybrané hodnoty | `where` Výsledek vyhodnocení |
|:---|:---|:---|
| 1 | `tags.env` => `"prod"` | `true` |
| 2 | `tags.env` => `"prod"` | `true` |

Jsou povolené taky vnořené výrazy Count:
```json
{
  "count": {
    "field": "Microsoft.Test/resourceType/objectArray[*]",
    "where": {
      "allOf": [
        {
          "field": "Microsoft.Test/resourceType/objectArray[*].property",
          "equals": "value2"
        },
        {
          "count": {
            "field": "Microsoft.Test/resourceType/objectArray[*].nestedArray[*]",
            "where": {
              "field": "Microsoft.Test/resourceType/objectArray[*].nestedArray[*]",
              "equals": 3
            },
            "greater": 0
          }
        }
      ]
    }
  }
}
```
 
| Iterace vnější smyčky | Vybrané hodnoty | Iterace vnitřních smyček | Vybrané hodnoty |
|:---|:---|:---|:---|
| 1 | `Microsoft.Test/resourceType/objectArray[*].property` => `"value1`</br> `Microsoft.Test/resourceType/objectArray[*].nestedArray[*]` => `1`, `2` | 1 | `Microsoft.Test/resourceType/objectArray[*].nestedArray[*]` => `1` |
| 1 | `Microsoft.Test/resourceType/objectArray[*].property` => `"value1`</br> `Microsoft.Test/resourceType/objectArray[*].nestedArray[*]` => `1`, `2` | 2 | `Microsoft.Test/resourceType/objectArray[*].nestedArray[*]` => `2` |
| 2 | `Microsoft.Test/resourceType/objectArray[*].property` => `"value2`</br> `Microsoft.Test/resourceType/objectArray[*].nestedArray[*]` => `3`, `4` | 1 | `Microsoft.Test/resourceType/objectArray[*].nestedArray[*]` => `3` |
| 2 | `Microsoft.Test/resourceType/objectArray[*].property` => `"value2`</br> `Microsoft.Test/resourceType/objectArray[*].nestedArray[*]` => `3`, `4` | 2 | `Microsoft.Test/resourceType/objectArray[*].nestedArray[*]` => `4` |

### <a name="the-field-function-inside-where-conditions"></a>`field()`Funkce v `where` podmínkách

Způsob, jakým `field()` se funkce chovají, `where` když je uvnitř podmínky založena na následujících konceptech:
1. Aliasy pole jsou přeloženy do kolekce hodnot vybraných ze všech členů pole.
1. `field()` funkce odkazující na aliasy pole vracejí pole s vybranými hodnotami.
1. Odkaz na počítané aliasy pole uvnitř `where` podmínky vrátí kolekci s jednou hodnotou vybranou ze člena pole, který je vyhodnocován v aktuální iteraci.

Toto chování znamená, že při odkazování na počítaného člena pole pomocí `field()` funkce uvnitř `where` podmínky **vrátí pole s jedním členem**. I když to nemusí být intuitivní, je konzistentní s nápadem, že aliasy pole vždycky vracejí kolekci vybraných vlastností. Tady je příklad:

```json
{
  "count": {
    "field": "Microsoft.Test/resourceType/stringArray[*]",
    "where": {
      "field": "Microsoft.Test/resourceType/stringArray[*]",
      "equals": "[field('Microsoft.Test/resourceType/stringArray[*]')]"
    }
  },
  "equals": 0
}
```

| Iterace | Hodnoty výrazů | `where` Výsledek vyhodnocení |
|:---|:---|:---|
| 1 | `Microsoft.Test/resourceType/stringArray[*]` => `"a"` </br>  `[field('Microsoft.Test/resourceType/stringArray[*]')]` => `[ "a" ]` | `false` |
| 2 | `Microsoft.Test/resourceType/stringArray[*]` => `"b"` </br>  `[field('Microsoft.Test/resourceType/stringArray[*]')]` => `[ "b" ]` | `false` |
| 3 | `Microsoft.Test/resourceType/stringArray[*]` => `"c"` </br>  `[field('Microsoft.Test/resourceType/stringArray[*]')]` => `[ "c" ]` | `false` |

Proto pokud je potřeba přístup k hodnotě počítaného aliasu pole pomocí `field()` funkce, tak, jak tak učinit, je zabalit `first()` funkci šablony:

```json
{
  "count": {
    "field": "Microsoft.Test/resourceType/stringArray[*]",
    "where": {
      "field": "Microsoft.Test/resourceType/stringArray[*]",
      "equals": "[first(field('Microsoft.Test/resourceType/stringArray[*]'))]"
    }
  }
}
```

| Iterace | Hodnoty výrazů | `where` Výsledek vyhodnocení |
|:---|:---|:---|
| 1 | `Microsoft.Test/resourceType/stringArray[*]` => `"a"` </br>  `[first(field('Microsoft.Test/resourceType/stringArray[*]'))]` => `"a"` | `true` |
| 2 | `Microsoft.Test/resourceType/stringArray[*]` => `"b"` </br>  `[first(field('Microsoft.Test/resourceType/stringArray[*]'))]` => `"b"` | `true` |
| 3 | `Microsoft.Test/resourceType/stringArray[*]` => `"c"` </br>  `[first(field('Microsoft.Test/resourceType/stringArray[*]'))]` => `"c"` | `true` |

Užitečné příklady najdete v tématu [Příklady počtu](../concepts/definition-structure.md#count-examples).

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
