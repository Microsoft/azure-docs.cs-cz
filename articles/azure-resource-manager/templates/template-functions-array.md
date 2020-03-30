---
title: Funkce šablony - pole a objekty
description: Popisuje funkce, které se mají použít v šabloně Azure Resource Manager pro práci s maticemi a objekty.
ms.topic: conceptual
ms.date: 07/31/2019
ms.openlocfilehash: 0b4bb80f6d7a7cc20a8b2dcc71e890f2ada7c5be
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "80156371"
---
# <a name="array-and-object-functions-for-arm-templates"></a>Pole a objektové funkce pro šablony ARM

Správce prostředků poskytuje několik funkcí pro práci s maticemi a objekty v šabloně Správce prostředků Azure (ARM).

* [Pole](#array)
* [Coalesce](#coalesce)
* [Concat](#concat)
* [Obsahuje](#contains)
* [createArray](#createarray)
* [empty](#empty)
* [První](#first)
* [Průsečíku](#intersection)
* [Json](#json)
* [Poslední](#last)
* [Délka](#length)
* [Max](#max)
* [Min](#min)
* [Rozsah](#range)
* [Přeskočit](#skip)
* [vzít](#take)
* [Unie](#union)

Chcete-li získat pole řetězcových hodnot oddělených hodnotou, naleznete v [tématu rozdělení](template-functions-string.md#split).

## <a name="array"></a>pole

`array(convertToArray)`

Převede hodnotu na pole.

### <a name="parameters"></a>Parametry

| Parametr | Požaduje se | Typ | Popis |
|:--- |:--- |:--- |:--- |
| převéstToArray |Ano |int, řetězec, pole nebo objekt |Hodnota převést na pole. |

### <a name="return-value"></a>Návratová hodnota

Pole.

### <a name="example"></a>Příklad

Následující [ukázková šablona](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/array.json) ukazuje, jak používat funkci pole s různými typy.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "intToConvert": {
            "type": "int",
            "defaultValue": 1
        },
        "stringToConvert": {
            "type": "string",
            "defaultValue": "efgh"
        },
        "objectToConvert": {
            "type": "object",
            "defaultValue": {"a": "b", "c": "d"}
        }
    },
    "resources": [
    ],
    "outputs": {
        "intOutput": {
            "type": "array",
            "value": "[array(parameters('intToConvert'))]"
        },
        "stringOutput": {
            "type": "array",
            "value": "[array(parameters('stringToConvert'))]"
        },
        "objectOutput": {
            "type": "array",
            "value": "[array(parameters('objectToConvert'))]"
        }
    }
}
```

Výstup z předchozího příkladu s výchozími hodnotami je:

| Name (Název) | Typ | Hodnota |
| ---- | ---- | ----- |
| intOutput | Pole | [1] |
| stringOutput | Pole | ["efgh"] |
| objectOutput | Pole | [{"a": "b", "c": "d"}] |

Chcete-li nasadit tuto ukázkovou šablonu pomocí příkazového příkazového příkazu k řešení Azure, použijte:

```azurecli-interactive
az deployment group create -g functionexamplegroup --template-uri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/functions/array.json
```

Chcete-li nasadit tuto ukázkovou šablonu pomocí PowerShellu, použijte:

```azurepowershell-interactive
New-AzResourceGroupDeployment -ResourceGroupName functionexamplegroup -TemplateUri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/functions/array.json
```

## <a name="coalesce"></a>Coalesce

`coalesce(arg1, arg2, arg3, ...)`

Vrátí první hodnotu bez hodnoty null z parametrů. Prázdné řetězce, prázdná pole a prázdné objekty nejsou null.

### <a name="parameters"></a>Parametry

| Parametr | Požaduje se | Typ | Popis |
|:--- |:--- |:--- |:--- |
| arg1 |Ano |int, řetězec, pole nebo objekt |První hodnota test null. |
| další args |Ne |int, řetězec, pole nebo objekt |Další hodnoty k testování null. |

### <a name="return-value"></a>Návratová hodnota

Hodnota první non-null parametry, které mohou být řetězec, int, pole nebo objekt. Null, pokud jsou všechny parametry null.

### <a name="example"></a>Příklad

Následující [ukázková šablona](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/coalesce.json) ukazuje výstup z různých použití coalesce.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "objectToTest": {
            "type": "object",
            "defaultValue": {
                "null1": null,
                "null2": null,
                "string": "default",
                "int": 1,
                "object": {"first": "default"},
                "array": [1]
            }
        }
    },
    "resources": [
    ],
    "outputs": {
        "stringOutput": {
            "type": "string",
            "value": "[coalesce(parameters('objectToTest').null1, parameters('objectToTest').null2, parameters('objectToTest').string)]"
        },
        "intOutput": {
            "type": "int",
            "value": "[coalesce(parameters('objectToTest').null1, parameters('objectToTest').null2, parameters('objectToTest').int)]"
        },
        "objectOutput": {
            "type": "object",
            "value": "[coalesce(parameters('objectToTest').null1, parameters('objectToTest').null2, parameters('objectToTest').object)]"
        },
        "arrayOutput": {
            "type": "array",
            "value": "[coalesce(parameters('objectToTest').null1, parameters('objectToTest').null2, parameters('objectToTest').array)]"
        },
        "emptyOutput": {
            "type": "bool",
            "value": "[empty(coalesce(parameters('objectToTest').null1, parameters('objectToTest').null2))]"
        }
    }
}
```

Výstup z předchozího příkladu s výchozími hodnotami je:

| Name (Název) | Typ | Hodnota |
| ---- | ---- | ----- |
| stringOutput | Řetězec | default |
| intOutput | Int | 1 |
| objectOutput | Objekt | {"first": "default"} |
| poleOutput | Pole | [1] |
| emptyOutput | Logická hodnota | True |

Chcete-li nasadit tuto ukázkovou šablonu pomocí příkazového příkazového příkazu k řešení Azure, použijte:

```azurecli-interactive
az deployment group create -g functionexamplegroup --template-uri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/functions/coalesce.json
```

Chcete-li nasadit tuto ukázkovou šablonu pomocí PowerShellu, použijte:

```azurepowershell-interactive
New-AzResourceGroupDeployment -ResourceGroupName functionexamplegroup -TemplateUri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/functions/coalesce.json
```

## <a name="concat"></a>concat

`concat(arg1, arg2, arg3, ...)`

Zkombinuje více polí a vrátí zřetězené pole nebo zkombinuje více řetězcových hodnot a vrátí zřetězený řetězec.

### <a name="parameters"></a>Parametry

| Parametr | Požaduje se | Typ | Popis |
|:--- |:--- |:--- |:--- |
| arg1 |Ano |pole nebo řetězec |První pole nebo řetězec pro zřetězení. |
| další argumenty |Ne |pole nebo řetězec |Další pole nebo řetězce v sekvenčním pořadí pro zřetězení. |

Tato funkce může trvat libovolný počet argumentů a může přijmout řetězce nebo pole pro parametry. Však nelze zadat pole a řetězce pro parametry. Pole jsou zřetězena pouze s jinými poli.

### <a name="return-value"></a>Návratová hodnota

Řetězec nebo pole zřetězených hodnot.

### <a name="example"></a>Příklad

Následující [ukázková šablona](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/concat-array.json) ukazuje, jak kombinovat dvě pole.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "firstArray": {
            "type": "array",
            "defaultValue": [
                "1-1",
                "1-2",
                "1-3"
            ]
        },
        "secondArray": {
            "type": "array",
            "defaultValue": [
                "2-1",
                "2-2",
                "2-3"
            ]
        }
    },
    "resources": [
    ],
    "outputs": {
        "return": {
            "type": "array",
            "value": "[concat(parameters('firstArray'), parameters('secondArray'))]"
        }
    }
}
```

Výstup z předchozího příkladu s výchozími hodnotami je:

| Name (Název) | Typ | Hodnota |
| ---- | ---- | ----- |
| return | Pole | ["1-1", "1-2", "1-3", "2-1", "2-2", "2-3"] |

Chcete-li nasadit tuto ukázkovou šablonu pomocí příkazového příkazového příkazu k řešení Azure, použijte:

```azurecli-interactive
az deployment group create -g functionexamplegroup --template-uri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/functions/concat-array.json
```

Chcete-li nasadit tuto ukázkovou šablonu pomocí PowerShellu, použijte:

```azurepowershell-interactive
New-AzResourceGroupDeployment -ResourceGroupName functionexamplegroup -TemplateUri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/functions/concat-array.json
```

Následující [ukázková šablona](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/concat-string.json) ukazuje, jak zkombinovat dvě řetězcové hodnoty a vrátit zřetězený řetězec.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "prefix": {
            "type": "string",
            "defaultValue": "prefix"
        }
    },
    "resources": [],
    "outputs": {
        "concatOutput": {
            "value": "[concat(parameters('prefix'), '-', uniqueString(resourceGroup().id))]",
            "type" : "string"
        }
    }
}
```

Výstup z předchozího příkladu s výchozími hodnotami je:

| Name (Název) | Typ | Hodnota |
| ---- | ---- | ----- |
| concatOutput | Řetězec | předpona-5yj4yjf5mbg72 |

Chcete-li nasadit tuto ukázkovou šablonu pomocí příkazového příkazového příkazu k řešení Azure, použijte:

```azurecli-interactive
az deployment group create -g functionexamplegroup --template-uri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/functions/concat-string.json
```

Chcete-li nasadit tuto ukázkovou šablonu pomocí PowerShellu, použijte:

```azurepowershell-interactive
New-AzResourceGroupDeployment -ResourceGroupName functionexamplegroup -TemplateUri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/functions/concat-string.json
```

## <a name="contains"></a>obsahuje

`contains(container, itemToFind)`

Zkontroluje, zda pole obsahuje hodnotu, objekt obsahuje klíč nebo řetězec obsahuje podřetězec. Porovnání řetězců rozlišuje malá a velká písmena. Však při testování, pokud objekt obsahuje klíč, porovnání je malá a velká písmena.

### <a name="parameters"></a>Parametry

| Parametr | Požaduje se | Typ | Popis |
|:--- |:--- |:--- |:--- |
| kontejner |Ano |pole, objekt nebo řetězec |Hodnota, která obsahuje hodnotu najít. |
| položkaHledání |Ano |řetězec nebo int |Hodnota najít. |

### <a name="return-value"></a>Návratová hodnota

**True,** pokud je položka nalezena; jinak **False**.

### <a name="example"></a>Příklad

Následující [ukázková šablona](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/contains.json) ukazuje, jak používat obsahuje s různými typy:

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "stringToTest": {
            "type": "string",
            "defaultValue": "OneTwoThree"
        },
        "objectToTest": {
            "type": "object",
            "defaultValue": {"one": "a", "two": "b", "three": "c"}
        },
        "arrayToTest": {
            "type": "array",
            "defaultValue": ["one", "two", "three"]
        }
    },
    "resources": [
    ],
    "outputs": {
        "stringTrue": {
            "type": "bool",
            "value": "[contains(parameters('stringToTest'), 'e')]"
        },
        "stringFalse": {
            "type": "bool",
            "value": "[contains(parameters('stringToTest'), 'z')]"
        },
        "objectTrue": {
            "type": "bool",
            "value": "[contains(parameters('objectToTest'), 'one')]"
        },
        "objectFalse": {
            "type": "bool",
            "value": "[contains(parameters('objectToTest'), 'a')]"
        },
        "arrayTrue": {
            "type": "bool",
            "value": "[contains(parameters('arrayToTest'), 'three')]"
        },
        "arrayFalse": {
            "type": "bool",
            "value": "[contains(parameters('arrayToTest'), 'four')]"
        }
    }
}
```

Výstup z předchozího příkladu s výchozími hodnotami je:

| Name (Název) | Typ | Hodnota |
| ---- | ---- | ----- |
| stringTrue | Logická hodnota | True |
| stringFalse | Logická hodnota | False |
| objectTrue | Logická hodnota | True |
| objectFalse | Logická hodnota | False |
| arrayTrue | Logická hodnota | True |
| arrayFalse | Logická hodnota | False |

Chcete-li nasadit tuto ukázkovou šablonu pomocí příkazového příkazového příkazu k řešení Azure, použijte:

```azurecli-interactive
az deployment group create -g functionexamplegroup --template-uri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/functions/contains.json
```

Chcete-li nasadit tuto ukázkovou šablonu pomocí PowerShellu, použijte:

```azurepowershell-interactive
New-AzResourceGroupDeployment -ResourceGroupName functionexamplegroup -TemplateUri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/functions/contains.json
```

## <a name="createarray"></a>createarray

`createArray (arg1, arg2, arg3, ...)`

Vytvoří pole z parametrů.

### <a name="parameters"></a>Parametry

| Parametr | Požaduje se | Typ | Popis |
|:--- |:--- |:--- |:--- |
| arg1 |Ano |Řetězec, celé číslo, pole nebo objekt |První hodnota v poli. |
| další argumenty |Ne |Řetězec, celé číslo, pole nebo objekt |Další hodnoty v poli. |

### <a name="return-value"></a>Návratová hodnota

Pole.

### <a name="example"></a>Příklad

Následující [ukázková šablona](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/createarray.json) ukazuje, jak používat createArray s různými typy:

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "objectToTest": {
            "type": "object",
            "defaultValue": {"one": "a", "two": "b", "three": "c"}
        },
        "arrayToTest": {
            "type": "array",
            "defaultValue": ["one", "two", "three"]
        }
    },
    "resources": [
    ],
    "outputs": {
        "stringArray": {
            "type": "array",
            "value": "[createArray('a', 'b', 'c')]"
        },
        "intArray": {
            "type": "array",
            "value": "[createArray(1, 2, 3)]"
        },
        "objectArray": {
            "type": "array",
            "value": "[createArray(parameters('objectToTest'))]"
        },
        "arrayArray": {
            "type": "array",
            "value": "[createArray(parameters('arrayToTest'))]"
        }
    }
}
```

Výstup z předchozího příkladu s výchozími hodnotami je:

| Name (Název) | Typ | Hodnota |
| ---- | ---- | ----- |
| pole stringArray | Pole | ["a", "b", "c"] |
| intArray | Pole | [1, 2, 3] |
| objectArray | Pole | [{"one": "a", "dva": "b", "tři": "c"}] |
| poleArrayArray | Pole | [["jeden", "dva", "tři"]] |

Chcete-li nasadit tuto ukázkovou šablonu pomocí příkazového příkazového příkazu k řešení Azure, použijte:

```azurecli-interactive
az deployment group create -g functionexamplegroup --template-uri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/functions/createarray.json
```

Chcete-li nasadit tuto ukázkovou šablonu pomocí PowerShellu, použijte:

```azurepowershell-interactive
New-AzResourceGroupDeployment -ResourceGroupName functionexamplegroup -TemplateUri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/functions/createarray.json
```

## <a name="empty"></a>empty

`empty(itemToTest)`

Určuje, zda je pole, objekt nebo řetězec prázdný.

### <a name="parameters"></a>Parametry

| Parametr | Požaduje se | Typ | Popis |
|:--- |:--- |:--- |:--- |
| položkaChcete-totest |Ano |pole, objekt nebo řetězec |Hodnota zkontrolovat, zda je prázdný. |

### <a name="return-value"></a>Návratová hodnota

Vrátí **hodnotu True,** pokud je hodnota prázdná. jinak **False**.

### <a name="example"></a>Příklad

Následující [ukázková šablona](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/empty.json) zkontroluje, zda jsou pole, objekt a řetězec prázdné.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "testArray": {
            "type": "array",
            "defaultValue": []
        },
        "testObject": {
            "type": "object",
            "defaultValue": {}
        },
        "testString": {
            "type": "string",
            "defaultValue": ""
        }
    },
    "resources": [
    ],
    "outputs": {
        "arrayEmpty": {
            "type": "bool",
            "value": "[empty(parameters('testArray'))]"
        },
        "objectEmpty": {
            "type": "bool",
            "value": "[empty(parameters('testObject'))]"
        },
        "stringEmpty": {
            "type": "bool",
            "value": "[empty(parameters('testString'))]"
        }
    }
}
```

Výstup z předchozího příkladu s výchozími hodnotami je:

| Name (Název) | Typ | Hodnota |
| ---- | ---- | ----- |
| poleEmpty | Logická hodnota | True |
| objectEmpty | Logická hodnota | True |
| stringEmpty | Logická hodnota | True |

Chcete-li nasadit tuto ukázkovou šablonu pomocí příkazového příkazového příkazu k řešení Azure, použijte:

```azurecli-interactive
az deployment group create -g functionexamplegroup --template-uri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/functions/empty.json
```

Chcete-li nasadit tuto ukázkovou šablonu pomocí PowerShellu, použijte:

```azurepowershell-interactive
New-AzResourceGroupDeployment -ResourceGroupName functionexamplegroup -TemplateUri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/functions/empty.json
```

## <a name="first"></a>První

`first(arg1)`

Vrátí první prvek pole nebo první znak řetězce.

### <a name="parameters"></a>Parametry

| Parametr | Požaduje se | Typ | Popis |
|:--- |:--- |:--- |:--- |
| arg1 |Ano |pole nebo řetězec |Hodnota načíst první prvek nebo znak. |

### <a name="return-value"></a>Návratová hodnota

Typ (řetězec, int, matice nebo objekt) prvního prvku v poli nebo první znak řetězce.

### <a name="example"></a>Příklad

Následující [ukázková šablona](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/first.json) ukazuje, jak používat první funkci s polem a řetězcem.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "arrayToTest": {
            "type": "array",
            "defaultValue": ["one", "two", "three"]
        }
    },
    "resources": [
    ],
    "outputs": {
        "arrayOutput": {
            "type": "string",
            "value": "[first(parameters('arrayToTest'))]"
        },
        "stringOutput": {
            "type": "string",
            "value": "[first('One Two Three')]"
        }
    }
}
```

Výstup z předchozího příkladu s výchozími hodnotami je:

| Name (Název) | Typ | Hodnota |
| ---- | ---- | ----- |
| poleOutput | Řetězec | jeden |
| stringOutput | Řetězec | O |

Chcete-li nasadit tuto ukázkovou šablonu pomocí příkazového příkazového příkazu k řešení Azure, použijte:

```azurecli-interactive
az deployment group create -g functionexamplegroup --template-uri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/functions/first.json
```

Chcete-li nasadit tuto ukázkovou šablonu pomocí PowerShellu, použijte:

```azurepowershell-interactive
New-AzResourceGroupDeployment -ResourceGroupName functionexamplegroup -TemplateUri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/functions/first.json
```

## <a name="intersection"></a>Průsečíku

`intersection(arg1, arg2, arg3, ...)`

Vrátí jedno pole nebo objekt s běžnými prvky z parametrů.

### <a name="parameters"></a>Parametry

| Parametr | Požaduje se | Typ | Popis |
|:--- |:--- |:--- |:--- |
| arg1 |Ano |pole nebo objekt |První hodnota, která má být používána pro hledání běžných prvků. |
| arg2 |Ano |pole nebo objekt |Druhá hodnota pro hledání společných prvků. |
| další argumenty |Ne |pole nebo objekt |Další hodnoty, které mají být určeny k hledání běžných prvků. |

### <a name="return-value"></a>Návratová hodnota

Pole nebo objekt se společnými prvky.

### <a name="example"></a>Příklad

Následující [ukázková šablona](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/intersection.json) ukazuje, jak používat průsečík s maticemi a objekty:

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "firstObject": {
            "type": "object",
            "defaultValue": {"one": "a", "two": "b", "three": "c"}
        },
        "secondObject": {
            "type": "object",
            "defaultValue": {"one": "a", "two": "z", "three": "c"}
        },
        "firstArray": {
            "type": "array",
            "defaultValue": ["one", "two", "three"]
        },
        "secondArray": {
            "type": "array",
            "defaultValue": ["two", "three"]
        }
    },
    "resources": [
    ],
    "outputs": {
        "objectOutput": {
            "type": "object",
            "value": "[intersection(parameters('firstObject'), parameters('secondObject'))]"
        },
        "arrayOutput": {
            "type": "array",
            "value": "[intersection(parameters('firstArray'), parameters('secondArray'))]"
        }
    }
}
```

Výstup z předchozího příkladu s výchozími hodnotami je:

| Name (Název) | Typ | Hodnota |
| ---- | ---- | ----- |
| objectOutput | Objekt | {"one": "a", "tři": "c"} |
| poleOutput | Pole | ["dva", "tři"] |

Chcete-li nasadit tuto ukázkovou šablonu pomocí příkazového příkazového příkazu k řešení Azure, použijte:

```azurecli-interactive
az deployment group create -g functionexamplegroup --template-uri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/functions/intersection.json
```

Chcete-li nasadit tuto ukázkovou šablonu pomocí PowerShellu, použijte:

```azurepowershell-interactive
New-AzResourceGroupDeployment -ResourceGroupName functionexamplegroup -TemplateUri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/functions/intersection.json
```

## <a name="json"></a>json

`json(arg1)`

Vrátí objekt JSON.

### <a name="parameters"></a>Parametry

| Parametr | Požaduje se | Typ | Popis |
|:--- |:--- |:--- |:--- |
| arg1 |Ano |řetězec |Hodnota převést na JSON. |

### <a name="return-value"></a>Návratová hodnota

JSON objekt ze zadaného řetězce nebo prázdný objekt, pokud je zadán **a null.**

### <a name="remarks"></a>Poznámky

Pokud potřebujete zahrnout hodnotu parametru nebo proměnnou do objektu JSON, vytvořte řetězec, který této funkci předáte, pomocí funkce [concat.](template-functions-string.md#concat)

### <a name="example"></a>Příklad

Následující [ukázková šablona](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/json.json) ukazuje, jak používat funkci json s maticemi a objekty:

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "testValue": {
            "type": "string",
            "defaultValue": "demo value"
        }
    },
    "resources": [
    ],
    "outputs": {
        "jsonOutput": {
            "type": "object",
            "value": "[json('{\"a\": \"b\"}')]"
        },
        "nullOutput": {
            "type": "bool",
            "value": "[empty(json('null'))]"
        },
        "paramOutput": {
            "type": "object",
            "value": "[json(concat('{\"a\": \"', parameters('testValue'), '\"}'))]"
        }
    }
}
```

Výstup z předchozího příkladu s výchozími hodnotami je:

| Name (Název) | Typ | Hodnota |
| ---- | ---- | ----- |
| jsonOutput | Objekt | {"a": "b"} |
| nullOutput | Logická hodnota | True |
| paramOutput | Objekt | {"a": "demo hodnota"}

Chcete-li nasadit tuto ukázkovou šablonu pomocí příkazového příkazového příkazu k řešení Azure, použijte:

```azurecli-interactive
az deployment group create -g functionexamplegroup --template-uri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/functions/json.json
```

Chcete-li nasadit tuto ukázkovou šablonu pomocí PowerShellu, použijte:

```azurepowershell-interactive
New-AzResourceGroupDeployment -ResourceGroupName functionexamplegroup -TemplateUri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/functions/json.json
```

## <a name="last"></a>poslední

`last (arg1)`

Vrátí poslední prvek pole nebo poslední znak řetězce.

### <a name="parameters"></a>Parametry

| Parametr | Požaduje se | Typ | Popis |
|:--- |:--- |:--- |:--- |
| arg1 |Ano |pole nebo řetězec |Hodnota pro načtení posledního prvku nebo znaku. |

### <a name="return-value"></a>Návratová hodnota

Typ (řetězec, int, matice nebo objekt) posledního prvku v poli nebo poslední znak řetězce.

### <a name="example"></a>Příklad

Následující [ukázková šablona](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/last.json) ukazuje, jak používat poslední funkci s polem a řetězcem.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "arrayToTest": {
            "type": "array",
            "defaultValue": ["one", "two", "three"]
        }
    },
    "resources": [
    ],
    "outputs": {
        "arrayOutput": {
            "type": "string",
            "value": "[last(parameters('arrayToTest'))]"
        },
        "stringOutput": {
            "type": "string",
            "value": "[last('One Two Three')]"
        }
    }
}
```

Výstup z předchozího příkladu s výchozími hodnotami je:

| Name (Název) | Typ | Hodnota |
| ---- | ---- | ----- |
| poleOutput | Řetězec | Tři |
| stringOutput | Řetězec | e |

Chcete-li nasadit tuto ukázkovou šablonu pomocí příkazového příkazového příkazu k řešení Azure, použijte:

```azurecli-interactive
az deployment group create -g functionexamplegroup --template-uri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/functions/last.json
```

Chcete-li nasadit tuto ukázkovou šablonu pomocí PowerShellu, použijte:

```azurepowershell-interactive
New-AzResourceGroupDeployment -ResourceGroupName functionexamplegroup -TemplateUri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/functions/last.json
```

## <a name="length"></a>length

`length(arg1)`

Vrátí počet prvků v poli, znaky v řetězci nebo vlastnosti kořenové úrovně v objektu.

### <a name="parameters"></a>Parametry

| Parametr | Požaduje se | Typ | Popis |
|:--- |:--- |:--- |:--- |
| arg1 |Ano |pole, řetězec nebo objekt |Pole, které chcete použít pro získání počtu prvků, řetězec pro získání počtu znaků nebo objekt, který chcete použít pro získání počtu vlastností na úrovni kořenového adresáře. |

### <a name="return-value"></a>Návratová hodnota

Int.

### <a name="example"></a>Příklad

Následující [ukázková šablona](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/length.json) ukazuje, jak používat délku s polem a řetězcem:

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "arrayToTest": {
            "type": "array",
            "defaultValue": [
                "one",
                "two",
                "three"
            ]
        },
        "stringToTest": {
            "type": "string",
            "defaultValue": "One Two Three"
        },
        "objectToTest": {
            "type": "object",
            "defaultValue": {
                "propA": "one",
                "propB": "two",
                "propC": "three",
                "propD": {
                    "propD-1": "sub",
                    "propD-2": "sub"
                }
            }
        }
    },
    "resources": [],
    "outputs": {
        "arrayLength": {
            "type": "int",
            "value": "[length(parameters('arrayToTest'))]"
        },
        "stringLength": {
            "type": "int",
            "value": "[length(parameters('stringToTest'))]"
        },
        "objectLength": {
            "type": "int",
            "value": "[length(parameters('objectToTest'))]"
        }
    }
}
```

Výstup z předchozího příkladu s výchozími hodnotami je:

| Name (Název) | Typ | Hodnota |
| ---- | ---- | ----- |
| poleDélka | Int | 3 |
| stringLength | Int | 13 |
| objekt objectLength | Int | 4 |

Chcete-li nasadit tuto ukázkovou šablonu pomocí příkazového příkazového příkazu k řešení Azure, použijte:

```azurecli-interactive
az deployment group create -g functionexamplegroup --template-uri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/functions/length.json
```

Chcete-li nasadit tuto ukázkovou šablonu pomocí PowerShellu, použijte:

```azurepowershell-interactive
New-AzResourceGroupDeployment -ResourceGroupName functionexamplegroup -TemplateUri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/functions/length.json
```

Tuto funkci můžete použít s polem k určení počtu iterací při vytváření prostředků. V následujícím příkladu by parametr **siteNames** odkazoval na pole názvů, které se mají použít při vytváření webových serverů.

```json
"copy": {
    "name": "websitescopy",
    "count": "[length(parameters('siteNames'))]"
}
```

Další informace o použití této funkce s polem najdete v tématu [Vytvoření více instancí prostředků ve Správci prostředků Azure](copy-resources.md).

## <a name="max"></a>max

`max(arg1)`

Vrátí maximální hodnotu z pole celá čísla nebo čárka odděleného seznamu celá čísla.

### <a name="parameters"></a>Parametry

| Parametr | Požaduje se | Typ | Popis |
|:--- |:--- |:--- |:--- |
| arg1 |Ano |pole celá čísla nebo seznam celočísel oddělených čárkami |Kolekce získat maximální hodnotu. |

### <a name="return-value"></a>Návratová hodnota

Int představující maximální hodnotu.

### <a name="example"></a>Příklad

Následující [ukázková šablona](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/max.json) ukazuje, jak používat max s polem a seznamem celá čísla:

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "arrayToTest": {
            "type": "array",
            "defaultValue": [0,3,2,5,4]
        }
    },
    "resources": [],
    "outputs": {
        "arrayOutput": {
            "type": "int",
            "value": "[max(parameters('arrayToTest'))]"
        },
        "intOutput": {
            "type": "int",
            "value": "[max(0,3,2,5,4)]"
        }
    }
}
```

Výstup z předchozího příkladu s výchozími hodnotami je:

| Name (Název) | Typ | Hodnota |
| ---- | ---- | ----- |
| poleOutput | Int | 5 |
| intOutput | Int | 5 |

Chcete-li nasadit tuto ukázkovou šablonu pomocí příkazového příkazového příkazu k řešení Azure, použijte:

```azurecli-interactive
az deployment group create -g functionexamplegroup --template-uri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/functions/max.json
```

Chcete-li nasadit tuto ukázkovou šablonu pomocí PowerShellu, použijte:

```azurepowershell-interactive
New-AzResourceGroupDeployment -ResourceGroupName functionexamplegroup -TemplateUri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/functions/max.json
```

## <a name="min"></a>min

`min(arg1)`

Vrátí minimální hodnotu z pole celá čísla nebo čárka odděleného seznamu celá čísla.

### <a name="parameters"></a>Parametry

| Parametr | Požaduje se | Typ | Popis |
|:--- |:--- |:--- |:--- |
| arg1 |Ano |pole celá čísla nebo seznam celočísel oddělených čárkami |Kolekce získat minimální hodnotu. |

### <a name="return-value"></a>Návratová hodnota

Int představující minimální hodnotu.

### <a name="example"></a>Příklad

Následující [ukázková šablona](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/min.json) ukazuje, jak používat min s polem a seznamem celá čísla:

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "arrayToTest": {
            "type": "array",
            "defaultValue": [0,3,2,5,4]
        }
    },
    "resources": [],
    "outputs": {
        "arrayOutput": {
            "type": "int",
            "value": "[min(parameters('arrayToTest'))]"
        },
        "intOutput": {
            "type": "int",
            "value": "[min(0,3,2,5,4)]"
        }
    }
}
```

Výstup z předchozího příkladu s výchozími hodnotami je:

| Name (Název) | Typ | Hodnota |
| ---- | ---- | ----- |
| poleOutput | Int | 0 |
| intOutput | Int | 0 |

Chcete-li nasadit tuto ukázkovou šablonu pomocí příkazového příkazového příkazu k řešení Azure, použijte:

```azurecli-interactive
az deployment group create -g functionexamplegroup --template-uri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/functions/min.json
```

Chcete-li nasadit tuto ukázkovou šablonu pomocí PowerShellu, použijte:

```azurepowershell-interactive
New-AzResourceGroupDeployment -ResourceGroupName functionexamplegroup -TemplateUri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/functions/min.json
```

## <a name="range"></a>range

`range(startIndex, count)`

Vytvoří pole celá čísla z počáteční celé číslo a obsahující počet položek.

### <a name="parameters"></a>Parametry

| Parametr | Požaduje se | Typ | Popis |
|:--- |:--- |:--- |:--- |
| Startindex |Ano |int |První celé číslo v poli. Součet startIndex a count nesmí být větší než 2147483647. |
| count |Ano |int |Počet celá čísla v poli. Musí být nezáporné celé číslo do 10000. |

### <a name="return-value"></a>Návratová hodnota

Pole celá čísla.

### <a name="example"></a>Příklad

Následující [ukázková šablona](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/range.json) ukazuje, jak používat funkci rozsahu:

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "startingInt": {
            "type": "int",
            "defaultValue": 5
        },
        "numberOfElements": {
            "type": "int",
            "defaultValue": 3
        }
    },
    "resources": [],
    "outputs": {
        "rangeOutput": {
            "type": "array",
            "value": "[range(parameters('startingInt'),parameters('numberOfElements'))]"
        }
    }
}
```

Výstup z předchozího příkladu s výchozími hodnotami je:

| Name (Název) | Typ | Hodnota |
| ---- | ---- | ----- |
| rangeOutput | Pole | [5, 6, 7] |

Chcete-li nasadit tuto ukázkovou šablonu pomocí příkazového příkazového příkazu k řešení Azure, použijte:

```azurecli-interactive
az deployment group create -g functionexamplegroup --template-uri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/functions/range.json
```

Chcete-li nasadit tuto ukázkovou šablonu pomocí PowerShellu, použijte:

```azurepowershell-interactive
New-AzResourceGroupDeployment -ResourceGroupName functionexamplegroup -TemplateUri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/functions/range.json
```

## <a name="skip"></a>Přeskočit

`skip(originalValue, numberToSkip)`

Vrátí pole se všemi prvky za zadané číslo v poli nebo vrátí řetězec se všemi znaky za zadané číslo v řetězci.

### <a name="parameters"></a>Parametry

| Parametr | Požaduje se | Typ | Popis |
|:--- |:--- |:--- |:--- |
| originalValue |Ano |pole nebo řetězec |Pole nebo řetězec, který se má použít pro přeskočení. |
| čísloToPřeskočit |Ano |int |Počet prvků nebo znaků přeskočit. Pokud je tato hodnota 0 nebo méně, jsou vráceny všechny prvky nebo znaky v hodnotě. Pokud je větší než délka pole nebo řetězce, je vráceno prázdné pole nebo řetězec. |

### <a name="return-value"></a>Návratová hodnota

Pole nebo řetězec.

### <a name="example"></a>Příklad

Následující [ukázková šablona](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/skip.json) přeskočí zadaný počet prvků v poli a zadaný počet znaků v řetězci.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "testArray": {
            "type": "array",
            "defaultValue": [
                "one",
                "two",
                "three"
            ]
        },
        "elementsToSkip": {
            "type": "int",
            "defaultValue": 2
        },
        "testString": {
            "type": "string",
            "defaultValue": "one two three"
        },
        "charactersToSkip": {
            "type": "int",
            "defaultValue": 4
        }
    },
    "resources": [],
    "outputs": {
        "arrayOutput": {
            "type": "array",
            "value": "[skip(parameters('testArray'),parameters('elementsToSkip'))]"
        },
        "stringOutput": {
            "type": "string",
            "value": "[skip(parameters('testString'),parameters('charactersToSkip'))]"
        }
    }
}
```

Výstup z předchozího příkladu s výchozími hodnotami je:

| Name (Název) | Typ | Hodnota |
| ---- | ---- | ----- |
| poleOutput | Pole | ["tři"] |
| stringOutput | Řetězec | dvě tři |

Chcete-li nasadit tuto ukázkovou šablonu pomocí příkazového příkazového příkazu k řešení Azure, použijte:

```azurecli-interactive
az deployment group create -g functionexamplegroup --template-uri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/functions/skip.json
```

Chcete-li nasadit tuto ukázkovou šablonu pomocí PowerShellu, použijte:

```azurepowershell-interactive
New-AzResourceGroupDeployment -ResourceGroupName functionexamplegroup -TemplateUri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/functions/skip.json
```

## <a name="take"></a>vzít

`take(originalValue, numberToTake)`

Vrátí pole se zadaným počtem prvků od začátku pole nebo řetězec se zadaným počtem znaků od začátku řetězce.

### <a name="parameters"></a>Parametry

| Parametr | Požaduje se | Typ | Popis |
|:--- |:--- |:--- |:--- |
| originalValue |Ano |pole nebo řetězec |Pole nebo řetězec, ze kterých mají být prvky odebrány. |
| numberToTake |Ano |int |Počet prvků nebo znaků, které chcete vzít. Pokud je tato hodnota 0 nebo méně, je vráceno prázdné pole nebo řetězec. Pokud je větší než délka daného pole nebo řetězce, jsou vráceny všechny prvky v poli nebo řetězci. |

### <a name="return-value"></a>Návratová hodnota

Pole nebo řetězec.

### <a name="example"></a>Příklad

Následující [ukázková šablona](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/take.json) přebírá zadaný počet prvků z pole a znaky z řetězce.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "testArray": {
            "type": "array",
            "defaultValue": [
                "one",
                "two",
                "three"
            ]
        },
        "elementsToTake": {
            "type": "int",
            "defaultValue": 2
        },
        "testString": {
            "type": "string",
            "defaultValue": "one two three"
        },
        "charactersToTake": {
            "type": "int",
            "defaultValue": 2
        }
    },
    "resources": [],
    "outputs": {
        "arrayOutput": {
            "type": "array",
            "value": "[take(parameters('testArray'),parameters('elementsToTake'))]"
        },
        "stringOutput": {
            "type": "string",
            "value": "[take(parameters('testString'),parameters('charactersToTake'))]"
        }
    }
}
```

Výstup z předchozího příkladu s výchozími hodnotami je:

| Name (Název) | Typ | Hodnota |
| ---- | ---- | ----- |
| poleOutput | Pole | ["jedna", "dva"] |
| stringOutput | Řetězec | on |

Chcete-li nasadit tuto ukázkovou šablonu pomocí příkazového příkazového příkazu k řešení Azure, použijte:

```azurecli-interactive
az deployment group create -g functionexamplegroup --template-uri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/functions/take.json
```

Chcete-li nasadit tuto ukázkovou šablonu pomocí PowerShellu, použijte:

```azurepowershell-interactive
New-AzResourceGroupDeployment -ResourceGroupName functionexamplegroup -TemplateUri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/functions/take.json
```

## <a name="union"></a>sjednocení

`union(arg1, arg2, arg3, ...)`

Vrátí jedno pole nebo objekt se všemi prvky z parametrů. Duplicitní hodnoty nebo klíče jsou zahrnuty pouze jednou.

### <a name="parameters"></a>Parametry

| Parametr | Požaduje se | Typ | Popis |
|:--- |:--- |:--- |:--- |
| arg1 |Ano |pole nebo objekt |První hodnota, která má být pro spojování prvků. |
| arg2 |Ano |pole nebo objekt |Druhá hodnota, která má být použita pro spojování prvků. |
| další argumenty |Ne |pole nebo objekt |Další hodnoty, které mají být určeny pro spojování prvků. |

### <a name="return-value"></a>Návratová hodnota

Pole nebo objekt.

### <a name="example"></a>Příklad

Následující [ukázková šablona](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/union.json) ukazuje, jak používat sjednocení s maticemi a objekty:

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "firstObject": {
            "type": "object",
            "defaultValue": {"one": "a", "two": "b", "three": "c1"}
        },
        "secondObject": {
            "type": "object",
            "defaultValue": {"three": "c2", "four": "d", "five": "e"}
        },
        "firstArray": {
            "type": "array",
            "defaultValue": ["one", "two", "three"]
        },
        "secondArray": {
            "type": "array",
            "defaultValue": ["three", "four"]
        }
    },
    "resources": [
    ],
    "outputs": {
        "objectOutput": {
            "type": "object",
            "value": "[union(parameters('firstObject'), parameters('secondObject'))]"
        },
        "arrayOutput": {
            "type": "array",
            "value": "[union(parameters('firstArray'), parameters('secondArray'))]"
        }
    }
}
```

Výstup z předchozího příkladu s výchozími hodnotami je:

| Name (Název) | Typ | Hodnota |
| ---- | ---- | ----- |
| objectOutput | Objekt | {"one": "a", "dva": "b", "tři": "c2", "čtyři": "d", "pět": "e"} |
| poleOutput | Pole | ["jedna", "dva", "tři", "čtyři"] |

Chcete-li nasadit tuto ukázkovou šablonu pomocí příkazového příkazového příkazu k řešení Azure, použijte:

```azurecli-interactive
az deployment group create -g functionexamplegroup --template-uri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/functions/union.json
```

Chcete-li nasadit tuto ukázkovou šablonu pomocí PowerShellu, použijte:

```azurepowershell-interactive
New-AzResourceGroupDeployment -ResourceGroupName functionexamplegroup -TemplateUri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/functions/union.json
```

## <a name="next-steps"></a>Další kroky

* Popis oddílů v šabloně Azure Resource Manager u najdete v tématu [Vytváření šablon Azure Resource Manageru](template-syntax.md).
* Pokud chcete sloučit víc šablon, přečtěte si informace [o použití propojených šablon ve Správci prostředků Azure](linked-templates.md).
* Chcete-li itrerate zadaný počet časů při vytváření typu prostředku, najdete v [tématu vytvoření více instancí prostředků ve Správci prostředků Azure](copy-resources.md).
* Informace o tom, jak nasadit vytvořenou šablonu, najdete [v tématu Nasazení aplikace pomocí šablony Azure Resource Manageru](deploy-powershell.md).

