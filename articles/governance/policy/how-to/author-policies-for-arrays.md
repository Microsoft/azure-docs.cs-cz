---
title: Vytváření zásad pro vlastnosti polí u prostředků
description: Naučte se pracovat s parametry pole a výrazy jazyka pole, vyhodnotit alias [*] a přidat prvky pomocí pravidel Definice Azure Policy.
ms.date: 03/31/2021
ms.topic: how-to
ms.openlocfilehash: 18afbee0ca8b1c488e3bd3ce50dacc726bd2ef25
ms.sourcegitcommit: b4fbb7a6a0aa93656e8dd29979786069eca567dc
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/13/2021
ms.locfileid: "107305187"
---
# <a name="author-policies-for-array-properties-on-azure-resources"></a>Vytváření zásad pro vlastnosti pole v prostředcích Azure

Vlastnosti Azure Resource Manager jsou běžně definovány jako řetězce a logické hodnoty. Když existuje vztah 1: n, místo toho se jako pole definují komplexní vlastnosti. V Azure Policy se pole používají několika různými způsoby:

- Typ [parametru definice](../concepts/definition-structure.md#parameters), který poskytuje více možností
- Součást [pravidla zásad](../concepts/definition-structure.md#policy-rule) s použitím podmínek **v** nebo **notIn**
- Součást pravidla zásad, které počítá, kolik členů pole splní podmínku
- Aktualizace existujícího pole v efektech [připojit](../concepts/effects.md#append) a [Upravit](../concepts/effects.md#modify)

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

Pokud chcete vytvořit definici zásady, která povoluje více možností, použijte  **typ** pole. Stejné zásady je možné přepsat následujícím způsobem:

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
- REST API: v rámci těla požadavku jako hodnota vlastnosti **Properties. Parameters** _v rámci textu_ [](/rest/api/policy/policyassignments/create) žádosti.

## <a name="using-arrays-in-conditions"></a>Použití polí v podmínkách

### <a name="in-and-notin"></a>V a notIn

`in`Podmínky a `notIn` fungují pouze s hodnotami pole. Kontrolují existenci hodnoty v poli. Pole může být literální pole JSON nebo odkaz na parametr array. Například:

```json
{
      "field": "tags.environment",
      "in": [ "dev", "test" ]
}
```

```json
{
      "field": "location",
      "notIn": "[parameters('allowedLocations')]"
}
```

### <a name="value-count"></a>Počet hodnot

Výraz [počtu hodnot](../concepts/definition-structure.md#value-count) počítá, kolik členů pole splňuje podmínku. Poskytuje způsob, jak několikrát vyhodnotit stejnou podmínku pomocí různých hodnot u každé iterace. Například následující podmínka kontroluje, zda název prostředku odpovídá jakémukoli vzoru z pole vzorů:

```json
{
    "count": {
        "value": [ "test*", "dev*", "prod*" ],
        "name": "pattern",
        "where": {
            "field": "name",
            "like": "[current('pattern')]"
        }
    },
    "greater": 0
}
```

Aby bylo možné vyhodnotit výraz, Azure Policy vyhodnotí `where` podmínku třikrát, jednou pro každého člena `[ "test*", "dev*", "prod*" ]` a spočítá počet, kolikrát byl vyhodnocen `true` . V každé iteraci je hodnota aktuálního člena pole spárována s `pattern` názvem indexu definovaným pomocí `count.name` . Tato hodnota se pak může odkazovat uvnitř `where` podmínky voláním speciální funkce šablony: `current('pattern')` .

| Iterace | `current('pattern')` Vrácená hodnota |
|:---|:---|
| 1 | `"test*"` |
| 2 | `"dev*"` |
| 3 | `"prod*"` |

Podmínka je pravdivá pouze v případě, že je výsledný počet větší než 0.

Chcete-li nastavit podmínku nad obecnější, použijte odkaz na parametr namísto pole literálu:

 ```json
{
    "count": {
        "value": "[parameters('patterns')]",
        "name": "pattern",
        "where": {
            "field": "name",
            "like": "[current('pattern')]"
        }
    },
    "greater": 0
}
```

Pokud výraz **Count hodnoty** není v žádném jiném výrazu **Count** , `count.name` je volitelný a `current()` funkce může být použita bez argumentů:

```json
{
    "count": {
        "value": "[parameters('patterns')]",
        "where": {
            "field": "name",
            "like": "[current()]"
        }
    },
    "greater": 0
}
```

**Počet hodnot** také podporuje pole komplexních objektů, což umožňuje složitější podmínky. Například následující podmínka definuje požadovanou hodnotu značky pro každý vzor názvu a kontroluje, zda název prostředku odpovídá vzoru, ale nemá požadovanou hodnotu značky:

```json
{
    "count": {
        "value": [
            { "pattern": "test*", "envTag": "dev" },
            { "pattern": "dev*", "envTag": "dev" },
            { "pattern": "prod*", "envTag": "prod" },
        ],
        "name": "namePatternRequiredTag",
        "where": {
            "allOf": [
                {
                    "field": "name",
                    "like": "[current('namePatternRequiredTag').pattern]"
                },
                {
                    "field": "tags.env",
                    "notEquals": "[current('namePatternRequiredTag').envTag]"
                }
            ]
        }
    },
    "greater": 0
}
```

Užitečné příklady najdete v tématu [Příklady počtu hodnot](../concepts/definition-structure.md#value-count-examples).

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

První alias představuje jednu hodnotu, hodnotu `stringArray` vlastnosti z obsahu žádosti. Vzhledem k tomu, že hodnota této vlastnosti je pole, není vhodné v podmínkách zásad. Například:

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

Tato podmínka je pravdivá, pokud jsou hodnoty všech `property` vlastností v systému `objectArray` rovny `"value"` . Další příklady najdete v tématu [Další \[ \* \] Příklady aliasů](#additional--alias-examples).

Při použití `field()` funkce pro odkaz na alias pole je vrácená hodnota pole všech vybraných hodnot. Toto chování znamená, že běžným případem použití `field()` funkce je možnost použít funkce šablony na hodnoty vlastností prostředku, je omezená. Jediné funkce šablony, které lze použít v tomto případě jsou ty, které přijímají argumenty pole. Například je možné získat délku pole s `[length(field('Microsoft.Test/resourceType/objectArray[*].property'))]` . Nicméně složitější scénáře, jako je použití šablony funkce na jednotlivé členy pole a jejich porovnání s požadovanou hodnotou, jsou možné pouze při použití `count` výrazu. Další informace najdete v tématu [výraz počtu polí](#field-count-expressions).

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

### <a name="field-count-expressions"></a>Výrazy počtu polí

Výrazy [počtu polí](../concepts/definition-structure.md#field-count) počítají, kolik členů pole splňuje podmínku a porovnávají počet s cílovou hodnotou. `Count` je intuitivnější a univerzální pro vyhodnocení polí v porovnání s `field` podmínkami. Syntaxe je:

```json
{
  "count": {
    "field": <[*] alias>,
    "where": <optional policy condition expression>
  },
  "equals|greater|less|any other operator": <target value>
}
```

Při použití bez `where` podmínky `count` jednoduše vrátí délku pole. S ukázkovým obsahem prostředků z předchozí části `count` je následující výraz vyhodnocen na, `true` protože `stringArray` má tři členy:

```json
{
  "count": {
    "field": "Microsoft.Test/resourceType/stringArray[*]"
  },
  "equals": 3
}
```

Toto chování funguje i u vnořených polí. Například následující `count` výraz je vyhodnocen k tomu, `true` protože pole obsahují čtyři členy pole `nestedArray` :

```json
{
  "count": {
    "field": "Microsoft.Test/resourceType/objectArray[*].nestedArray[*]"
  },
  "greaterOrEquals": 4
}
```

Mocnina `count` je v `where` podmínce. Když je zadáno, Azure Policy vytvoří výčet členů pole a vyhodnotí každý z nich s podmínkou, což spočítá, kolik členů pole bylo vyhodnoceno `true` . Konkrétně při každé iteraci `where` vyhodnocení podmínky Azure Policy vybere jednoho člena pole ***i** _ a vyhodnotí obsah prostředků proti `where` podmínce _* jako if ***i**_ je jediným členem array_ *. Když je v každé iteraci dostupný jenom jeden člen pole, poskytuje způsob, jak u každého člena pole použít komplexní podmínky.

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

Aby bylo možné vyhodnotit `count` výraz, Azure Policy vyhodnotí `where` podmínku třikrát, jednou pro každého člena `stringArray` a spočítá počet, kolikrát byl vyhodnocen `true` .
Pokud `where` Podmínka odkazuje na `Microsoft.Test/resourceType/stringArray[*]` členy pole, místo výběru všech členů `stringArray` , vybere pouze jeden člen pole pokaždé, když:

| Iterace | Vybrané `Microsoft.Test/resourceType/stringArray[*]` hodnoty | `where` Výsledek vyhodnocení |
|:---|:---|:---|
| 1 | `"a"` | `true` |
| 2 | `"b"` | `false` |
| 3 | `"c"` | `false` |

`count`Vrátí `1` .

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

`count`Vrátí `1` .

Skutečnost, že `where` výraz je vyhodnocen proti **celému** obsahu žádosti (se změnami pouze pro člena pole, který je právě vyčíslen) znamená, že `where` podmínka může také odkazovat na pole mimo pole:

```json
{
  "count": {
    "field": "Microsoft.Test/resourceType/objectArray[*]",
    "where": {
      "field": "tags.env",
      "equals": "prod"
    }
  },
  "equals": 0
}
```

| Iterace | Vybrané hodnoty | `where` Výsledek vyhodnocení |
|:---|:---|:---|
| 1 | `tags.env` => `"prod"` | `true` |
| 2 | `tags.env` => `"prod"` | `true` |

Vnořené výrazy Count lze použít pro použití podmínek pro vnořená pole polí. Například následující podmínka kontroluje, zda `objectArray[*]` má pole přesně dva členy `nestedArray[*]` , které obsahují jednoho nebo více členů:

```json
{
  "count": {
    "field": "Microsoft.Test/resourceType/objectArray[*]",
    "where": {
      "count": {
        "field": "Microsoft.Test/resourceType/objectArray[*].nestedArray[*]"
      },
      "greaterOrEquals": 1
    }
  },
  "equals": 2
}
```

| Iterace | Vybrané hodnoty | Výsledek vyhodnocení vnořeného počtu |
|:---|:---|:---|
| 1 | `Microsoft.Test/resourceType/objectArray[*].nestedArray[*]` => `1`, `2` | `nestedArray[*]` má 2 členy => `true` |
| 2 | `Microsoft.Test/resourceType/objectArray[*].nestedArray[*]` => `3`, `4` | `nestedArray[*]` má 2 členy => `true` |

Vzhledem k tomu, že oba členové `objectArray[*]` mají podřízené pole `nestedArray[*]` se dvěma členy, výraz vnějšího počtu vrátí `2` .

Složitější příklad: Ověřte, zda `objectArray[*]` má pole přesně dva členy s `nestedArray[*]` libovolnými členy, které se rovnají `2` nebo `3` :

```json
{
  "count": {
    "field": "Microsoft.Test/resourceType/objectArray[*]",
    "where": {
      "count": {
        "field": "Microsoft.Test/resourceType/objectArray[*].nestedArray[*]",
        "where": {
            "field": "Microsoft.Test/resourceType/objectArray[*].nestedArray[*]",
            "in": [ 2, 3 ]
        }
      },
      "greaterOrEquals": 1
    }
  },
  "equals": 2
}
```

| Iterace | Vybrané hodnoty | Výsledek vyhodnocení vnořeného počtu
|:---|:---|:---|
| 1 | `Microsoft.Test/resourceType/objectArray[*].nestedArray[*]` => `1`, `2` | `nestedArray[*]` zobrazí `2` => `true` |
| 2 | `Microsoft.Test/resourceType/objectArray[*].nestedArray[*]` => `3`, `4` | `nestedArray[*]` zobrazí `3` => `true` |

Vzhledem k tomu, že oba členové `objectArray[*]` mají podřízené pole obsahující `nestedArray[*]` buď `2` nebo `3` , vrátí výraz vnějšího počtu `2` .

> [!NOTE]
> Vnořené výrazy počtu polí mohou odkazovat pouze na vnořená pole. Například výraz Count, který odkazuje na, `Microsoft.Test/resourceType/objectArray[*]` může mít vnořený počet cílící na vnořené pole `Microsoft.Test/resourceType/objectArray[*].nestedArray[*]` , ale nemůže mít cílený výraz vnořeného počtu `Microsoft.Test/resourceType/stringArray[*]` .

#### <a name="accessing-current-array-member-with-template-functions"></a>Přístup k aktuálnímu členu pole pomocí funkcí šablony

Při použití funkcí šablony použijte `current()` funkci pro přístup k hodnotě aktuálního člena pole nebo k hodnotám kterékoli z jeho vlastností. Chcete-li získat přístup k hodnotě aktuálního člena pole, předejte alias definovaný v rámci `count.field` nebo jako kterýkoli z jeho podřízených aliasů jako argument `current()` funkce. Například:

```json
{
  "count": {
    "field": "Microsoft.Test/resourceType/objectArray[*]",
    "where": {
        "value": "[current('Microsoft.Test/resourceType/objectArray[*].property')]",
        "like": "value*"
    }
  },
  "equals": 2
}

```

| Iterace | `current()` Vrácená hodnota | `where` Výsledek vyhodnocení |
|:---|:---|:---|
| 1 | Hodnota `property` v prvním členovi `objectArray[*]` : `value1` | `true` |
| 2 | Hodnota `property` v prvním členovi `objectArray[*]` : `value2` | `true` |

#### <a name="the-field-function-inside-where-conditions"></a>Funkce Field v podmínkách WHERE

`field()`Funkci lze také použít pro přístup k hodnotě aktuálního člena pole, pokud výraz **Count** není uvnitř **podmínky existence** ( `field()` funkce vždy odkazuje na prostředek vyhodnocený v podmínky **if** ). Chování `field()` při odkazování na vyhodnocené pole je založeno na následujících konceptech:

1. Aliasy pole jsou přeloženy do kolekce hodnot vybraných ze všech členů pole.
1. `field()` funkce odkazující na aliasy pole vracejí pole s vybranými hodnotami.
1. Odkaz na počítané aliasy pole uvnitř `where` podmínky vrátí kolekci s jednou hodnotou vybranou ze člena pole, který je vyhodnocován v aktuální iteraci.

Toto chování znamená, že při odkazování na počítaného člena pole pomocí `field()` funkce uvnitř `where` podmínky **vrátí pole s jedním členem**. I když toto chování nemusí být intuitivní, je konzistentní s nápadem, že aliasy pole vždycky vracejí kolekci vybraných vlastností. Tady je příklad:

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

Proto pokud je potřeba přístup k hodnotě počítaného aliasu pole pomocí `field()` funkce, tak, aby to bylo možné provést, je zabalit `first()` funkci šablony:

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

Užitečné příklady najdete v tématu [Příklady počtu polí](../concepts/definition-structure.md#field-count-examples).

## <a name="modifying-arrays"></a>Úprava polí

Příkazy [připojit](../concepts/effects.md#append) a [Upravit](../concepts/effects.md#modify) vlastnosti prostředku během vytváření nebo aktualizace. Při práci s vlastnostmi pole závisí chování těchto efektů na tom, zda se operace pokouší změnit **\[\*\]** alias nebo ne:

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

## <a name="additional--alias-examples"></a>Další příklady aliasů [*]

Pro kontrolu, zda všechny členy pole v obsahu žádosti splňují určitou podmínku, se doporučuje použít [výrazy počtu polí](#field-count-expressions) . V některých jednoduchých podmínkách je však možné dosáhnout stejného výsledku pomocí přístupového objektu pole s aliasem pole, jak je popsáno v tématu [odkazování na kolekci členů pole](#referencing-the-array-members-collection). Tento model může být užitečný v pravidlech zásad, která překračují limit povolených výrazů **Count** . Tady jsou příklady pro běžné případy použití:

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

|Podmínka |Výsledek | Scenario |Vysvětlení |
|-|-|-|-|
|`{<field>,"notEquals":"127.0.0.1"}` |Nothing |Žádná shoda |Jeden prvek pole se vyhodnotí jako false (127.0.0.1! = 127.0.0.1) a jeden jako true (127.0.0.1! = 192.168.1.1), takže podmínka **notEquals** je _nepravdivá_ a efekt se neaktivuje. |
|`{<field>,"notEquals":"10.0.4.1"}` |Vliv na zásady |Žádná shoda |Obě prvky pole se vyhodnocují jako true (10.0.4.1! = 127.0.0.1 a 10.0.4.1! = 192.168.1.1), takže podmínka **notEquals** je _pravdivá_ a výsledek se aktivuje. |
|`"not":{<field>,"notEquals":"127.0.0.1" }` |Vliv na zásady |Jedna nebo více shod |Jeden prvek pole se vyhodnotí jako false (127.0.0.1! = 127.0.0.1) a jeden jako true (127.0.0.1! = 192.168.1.1), takže podmínka **notEquals** je _NEPRAVDA_. Logický operátor se vyhodnotí jako true (**ne** _false_), takže se efekt aktivuje. |
|`"not":{<field>,"notEquals":"10.0.4.1"}` |Nothing |Jedna nebo více shod |Obě prvky pole jsou vyhodnoceny jako true (10.0.4.1! = 127.0.0.1 a 10.0.4.1! = 192.168.1.1), takže podmínka **notEquals** je _pravdivá_. Logický operátor se vyhodnotí jako false (**ne** _true_), takže se efekt neaktivuje. |
|`"not":{<field>,"Equals":"127.0.0.1"}` |Vliv na zásady |Neshoda |Jeden prvek pole se vyhodnotí jako true (127.0.0.1 = = 127.0.0.1) a jeden jako false (127.0.0.1 = = 192.168.1.1), takže podmínka **Equals** je _false_. Logický operátor se vyhodnotí jako true (**ne** _false_), takže se efekt aktivuje. |
|`"not":{<field>,"Equals":"10.0.4.1"}` |Vliv na zásady |Neshoda |Obě prvky pole jsou vyhodnoceny jako false (10.0.4.1 = = 127.0.0.1 a 10.0.4.1 = = 192.168.1.1), takže podmínka **Equals** je _false_. Logický operátor se vyhodnotí jako true (**ne** _false_), takže se efekt aktivuje. |
|`{<field>,"Equals":"127.0.0.1"}` |Nothing |Všechny shody |Jeden prvek pole se vyhodnotí jako true (127.0.0.1 = = 127.0.0.1) a jeden jako false (127.0.0.1 = = 192.168.1.1), takže podmínka **Equals** je _false_ a efekt se neaktivuje. |
|`{<field>,"Equals":"10.0.4.1"}` |Nothing |Všechny shody |Obě prvky pole jsou vyhodnoceny jako false (10.0.4.1 = = 127.0.0.1 a 10.0.4.1 = = 192.168.1.1), takže podmínka **Equals** je _false_ a účinek není aktivován. |

## <a name="next-steps"></a>Další kroky

- Přečtěte si příklady na [Azure Policy Samples](../samples/index.md).
- Projděte si [strukturu definic Azure Policy](../concepts/definition-structure.md).
- Projděte si [Vysvětlení efektů zásad](../concepts/effects.md).
- Zjistěte, jak [programově vytvářet zásady](programmatically-create.md).
- Přečtěte si, jak [opravit prostředky, které nedodržují předpisy](remediate-resources.md).
- Seznamte se s tím, co skupina pro správu [organizuje vaše prostředky pomocí skupin pro správu Azure](../../management-groups/overview.md).
