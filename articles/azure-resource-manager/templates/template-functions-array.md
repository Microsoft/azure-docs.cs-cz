---
title: Funkce šablon – pole
description: Popisuje funkce, které se použijí v šabloně Azure Resource Manager pro práci s poli.
ms.topic: conceptual
ms.date: 10/12/2020
ms.openlocfilehash: a5cf73203cf59a0b9f2b5f49c923d0a077c065fc
ms.sourcegitcommit: d103a93e7ef2dde1298f04e307920378a87e982a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/13/2020
ms.locfileid: "91979134"
---
# <a name="array-functions-for-arm-templates"></a>Funkce pole pro šablony ARM

Správce prostředků poskytuje několik funkcí pro práci s poli v šabloně Azure Resource Manager (ARM).

* [array](#array)
* [spojuje](#concat)
* [zobrazí](#contains)
* [createArray](#createarray)
* [empty](#empty)
* [první](#first)
* [průnik](#intersection)
* [posledního](#last)
* [length](#length)
* [počet](#max)
* [min](#min)
* [oblasti](#range)
* [přímo](#skip)
* [take](#take)
* [sjednocovací](#union)

Chcete-li získat pole řetězcových hodnot oddělených hodnotou, viz [rozdělit](template-functions-string.md#split).

## <a name="array"></a>array

`array(convertToArray)`

Převede hodnotu na pole.

### <a name="parameters"></a>Parametry

| Parametr | Povinné | Typ | Popis |
|:--- |:--- |:--- |:--- |
| convertToArray |Ano |int, String, Array nebo Object |Hodnota, která má být převedena na pole. |

### <a name="return-value"></a>Vrácená hodnota

Pole.

### <a name="example"></a>Příklad

Následující [příklad šablony](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/array.json) ukazuje, jak používat funkci Array s různými typy.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
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

| Název | Typ | Hodnota |
| ---- | ---- | ----- |
| intOutput | Pole |  [1] |
| stringOutput | Pole | ["efgh"] |
| objectOutput | Pole | [{"a": "b", "c": "d"}] |

## <a name="concat"></a>concat

`concat(arg1, arg2, arg3, ...)`

Kombinuje více polí a vrátí zřetězené pole, nebo kombinuje více řetězcových hodnot a vrátí zřetězený řetězec.

### <a name="parameters"></a>Parametry

| Parametr | Povinné | Typ | Popis |
|:--- |:--- |:--- |:--- |
| arg1 |Ano |pole nebo řetězec |První pole nebo řetězec pro zřetězení. |
| Další argumenty |No |pole nebo řetězec |Další pole nebo řetězce v sekvenčním pořadí pro zřetězení. |

Tato funkce může mít libovolný počet argumentů a může přijmout buď řetězce nebo pole pro parametry. Pro parametry však nelze zadat obě pole a řetězce. Pole jsou zřetězena pouze s jinými poli.

### <a name="return-value"></a>Vrácená hodnota

Řetězec nebo pole zřetězených hodnot.

### <a name="example"></a>Příklad

Následující [příklad šablony](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/concat-array.json) ukazuje, jak kombinovat dvě pole.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
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

| Název | Typ | Hodnota |
| ---- | ---- | ----- |
| return | Pole | ["1-1", "1-2", "1-3", "2-1", "2-2", "2-3"] |

Následující [příklad šablony](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/concat-string.json) ukazuje, jak kombinovat dvě řetězcové hodnoty a vracet zřetězený řetězec.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
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

| Název | Typ | Hodnota |
| ---- | ---- | ----- |
| concatOutput | Řetězec | prefix – 5yj4yjf5mbg72 |

## <a name="contains"></a>obsahuje

`contains(container, itemToFind)`

Kontroluje, zda pole obsahuje hodnotu, objekt obsahuje klíč, nebo řetězec obsahuje podřetězec. Porovnávání řetězců rozlišuje velká a malá písmena. Při testování, zda objekt obsahuje klíč, však porovnání nerozlišuje malá a velká písmena.

### <a name="parameters"></a>Parametry

| Parametr | Povinné | Typ | Popis |
|:--- |:--- |:--- |:--- |
| kontejner |Ano |pole, objekt nebo řetězec |Hodnota, která obsahuje hodnotu, která se má najít. |
| itemToFind |Ano |řetězec nebo int |Hodnota, která se má najít |

### <a name="return-value"></a>Vrácená hodnota

**True** , pokud je položka nalezena; v opačném případě **false**.

### <a name="example"></a>Příklad

Následující [příklad šablony](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/contains.json) ukazuje, jak použít Contains s různými typy:

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
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

| Název | Typ | Hodnota |
| ---- | ---- | ----- |
| stringTrue | Logická hodnota | Ano |
| stringFalse | Logická hodnota | Nepravda |
| objectTrue | Logická hodnota | Ano |
| objectFalse | Logická hodnota | Nepravda |
| arrayTrue | Logická hodnota | Ano |
| arrayFalse | Logická hodnota | Nepravda |

## <a name="createarray"></a>createarray

`createArray (arg1, arg2, arg3, ...)`

Vytvoří pole z parametrů.

### <a name="parameters"></a>Parametry

| Parametr | Povinné | Typ | Popis |
|:--- |:--- |:--- |:--- |
| args |No |Řetězec, celé číslo, pole nebo objekt |Hodnoty v poli. |

### <a name="return-value"></a>Vrácená hodnota

Pole. Pokud nejsou zadány žádné parametry, vrátí prázdné pole.

### <a name="example"></a>Příklad

Následující [příklad šablony](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/createarray.json) ukazuje, jak používat createArray s různými typy:

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
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
        },
        "emptyArray": {
            "type": "array",
            "value": "[createArray()]"
        }
    }
}
```

Výstup z předchozího příkladu s výchozími hodnotami je:

| Název | Typ | Hodnota |
| ---- | ---- | ----- |
| stringArray | Pole | ["a", "b", "c"] |
| intArray | Pole | [1, 2, 3] |
| objectArray | Pole | [{"One": "a"; "Two": "b"; "tři": "c"}] |
| arrayArray | Pole | [["One"; "Two"; "tři"]] |
| emptyArray | Pole | [] |

## <a name="empty"></a>empty

`empty(itemToTest)`

Určuje, zda je pole, objekt nebo řetězec prázdný.

### <a name="parameters"></a>Parametry

| Parametr | Povinné | Typ | Popis |
|:--- |:--- |:--- |:--- |
| itemToTest |Ano |pole, objekt nebo řetězec |Hodnota, která zkontroluje, jestli je prázdná |

### <a name="return-value"></a>Vrácená hodnota

Vrátí **hodnotu true** , pokud je hodnota prázdná. v opačném případě **false**.

### <a name="example"></a>Příklad

Následující [příklad šablony](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/empty.json) kontroluje, zda pole, objekt a řetězec jsou prázdné.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
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

| Název | Typ | Hodnota |
| ---- | ---- | ----- |
| arrayEmpty | Logická hodnota | Ano |
| objectEmpty | Logická hodnota | Ano |
| stringEmpty | Logická hodnota | Ano |

## <a name="first"></a>první

`first(arg1)`

Vrátí první prvek pole nebo první znak řetězce.

### <a name="parameters"></a>Parametry

| Parametr | Povinné | Typ | Popis |
|:--- |:--- |:--- |:--- |
| arg1 |Ano |pole nebo řetězec |Hodnota pro načtení prvního prvku nebo znaku. |

### <a name="return-value"></a>Vrácená hodnota

Typ (řetězec, int, Array nebo Object) prvního prvku v poli nebo první znak řetězce.

### <a name="example"></a>Příklad

Následující [příklad šablony](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/first.json) ukazuje, jak použít první funkci s polem a řetězcem.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
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

| Název | Typ | Hodnota |
| ---- | ---- | ----- |
| arrayOutput | Řetězec | jeden |
| stringOutput | Řetězec | O |

## <a name="intersection"></a>průnik

`intersection(arg1, arg2, arg3, ...)`

Vrátí jedno pole nebo objekt se společnými prvky z parametrů.

### <a name="parameters"></a>Parametry

| Parametr | Povinné | Typ | Popis |
|:--- |:--- |:--- |:--- |
| arg1 |Ano |pole nebo objekt |První hodnota, která má být použita pro hledání běžných prvků. |
| arg2 |Ano |pole nebo objekt |Druhá hodnota, která má být použita pro vyhledání běžných prvků. |
| Další argumenty |No |pole nebo objekt |Další hodnoty, které se mají použít pro hledání běžných prvků. |

### <a name="return-value"></a>Vrácená hodnota

Pole nebo objekt se společnými prvky.

### <a name="example"></a>Příklad

Následující [příklad šablony](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/intersection.json) ukazuje, jak použít průnik s poli a objekty:

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
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

| Název | Typ | Hodnota |
| ---- | ---- | ----- |
| objectOutput | Objekt | {"One": "a", "tři": "c"} |
| arrayOutput | Pole | ["Two", "tři"] |

## <a name="last"></a>poslední

`last (arg1)`

Vrátí poslední prvek pole nebo poslední znak řetězce.

### <a name="parameters"></a>Parametry

| Parametr | Povinné | Typ | Popis |
|:--- |:--- |:--- |:--- |
| arg1 |Ano |pole nebo řetězec |Hodnota, která načte poslední prvek nebo znak. |

### <a name="return-value"></a>Vrácená hodnota

Typ (řetězec, int, Array nebo Object) posledního prvku v poli nebo posledního znaku řetězce.

### <a name="example"></a>Příklad

Následující [příklad šablony](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/last.json) ukazuje, jak použít poslední funkci s polem a řetězcem.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
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

| Název | Typ | Hodnota |
| ---- | ---- | ----- |
| arrayOutput | Řetězec | 3 |
| stringOutput | Řetězec | e |

## <a name="length"></a>length

`length(arg1)`

Vrátí počet prvků v poli, znaky v řetězci nebo vlastnosti na kořenové úrovni objektu.

### <a name="parameters"></a>Parametry

| Parametr | Povinné | Typ | Popis |
|:--- |:--- |:--- |:--- |
| arg1 |Ano |pole, řetězec nebo objekt |Pole, které se má použít pro získání počtu prvků, řetězec, který se má použít pro získání počtu znaků, nebo objekt, který se má použít pro získání počtu vlastností na úrovni root. |

### <a name="return-value"></a>Vrácená hodnota

Int.

### <a name="example"></a>Příklad

Následující [příklad šablony](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/length.json) ukazuje, jak použít délku s polem a řetězcem:

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
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

| Název | Typ | Hodnota |
| ---- | ---- | ----- |
| arrayLength | Int | 3 |
| stringLength | Int | 13 |
| objectLength | Int | 4 |

Tuto funkci můžete použít spolu s polem k určení počtu iterací při vytváření prostředků. V následujícím příkladu by parametry **název_webu** odkazovaly na pole názvů, které se má použít při vytváření webů.

```json
"copy": {
    "name": "websitescopy",
    "count": "[length(parameters('siteNames'))]"
}
```

Další informace o použití této funkce s polem najdete v tématu [vytvoření více instancí prostředků v Azure Resource Manager](copy-resources.md).

## <a name="max"></a>max

`max(arg1)`

Vrátí maximální hodnotu z pole celých čísel nebo seznam celých čísel oddělených čárkami.

### <a name="parameters"></a>Parametry

| Parametr | Povinné | Typ | Popis |
|:--- |:--- |:--- |:--- |
| arg1 |Ano |pole celých čísel nebo seznam celých čísel oddělených čárkami |Kolekce, která získá maximální hodnotu |

### <a name="return-value"></a>Vrácená hodnota

Celé číslo představující maximální hodnotu.

### <a name="example"></a>Příklad

Následující [příklad šablony](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/max.json) ukazuje, jak použít Max s polem a seznam celých čísel:

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
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

| Název | Typ | Hodnota |
| ---- | ---- | ----- |
| arrayOutput | Int | 5 |
| intOutput | Int | 5 |

## <a name="min"></a>min

`min(arg1)`

Vrátí minimální hodnotu z pole celých čísel nebo seznam celých čísel oddělených čárkami.

### <a name="parameters"></a>Parametry

| Parametr | Povinné | Typ | Popis |
|:--- |:--- |:--- |:--- |
| arg1 |Ano |pole celých čísel nebo seznam celých čísel oddělených čárkami |Kolekce, která získá minimální hodnotu. |

### <a name="return-value"></a>Vrácená hodnota

Celé číslo představující minimální hodnotu.

### <a name="example"></a>Příklad

Následující [příklad šablony](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/min.json) ukazuje, jak použít minimum s polem a seznam celých čísel:

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
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

| Název | Typ | Hodnota |
| ---- | ---- | ----- |
| arrayOutput | Int | 0 |
| intOutput | Int | 0 |

## <a name="range"></a>range

`range(startIndex, count)`

Vytvoří pole celých čísel od počátečního celého čísla a obsahuje několik položek.

### <a name="parameters"></a>Parametry

| Parametr | Povinné | Typ | Popis |
|:--- |:--- |:--- |:--- |
| Indexu |Ano |int |První celé číslo v poli Součet hodnoty startIndex a Count nesmí být větší než 2147483647. |
| count |Ano |int |Počet celých čísel v poli. Musí být nezáporné celé číslo od do 10000. |

### <a name="return-value"></a>Vrácená hodnota

Pole celých čísel.

### <a name="example"></a>Příklad

Následující [příklad šablony](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/range.json) ukazuje, jak použít funkci Range:

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
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

| Název | Typ | Hodnota |
| ---- | ---- | ----- |
| rangeOutput | Pole | [5, 6, 7] |

## <a name="skip"></a>Přeskočit

`skip(originalValue, numberToSkip)`

Vrátí pole se všemi prvky po zadaném čísle v poli nebo vrátí řetězec se všemi znaky po zadaném čísle v řetězci.

### <a name="parameters"></a>Parametry

| Parametr | Povinné | Typ | Popis |
|:--- |:--- |:--- |:--- |
| Původní |Ano |pole nebo řetězec |Pole nebo řetězec, který se má použít pro přeskočení. |
| numberToSkip |Ano |int |Počet prvků nebo znaků, které mají být přeskočeny. Pokud je tato hodnota 0 nebo méně, vrátí se všechny prvky nebo znaky v hodnotě. Pokud je větší než délka pole nebo řetězce, je vráceno prázdné pole nebo řetězec. |

### <a name="return-value"></a>Vrácená hodnota

Pole nebo řetězec.

### <a name="example"></a>Příklad

Následující [příklad šablony](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/skip.json) přeskočí zadaný počet prvků v poli a zadaný počet znaků v řetězci.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
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

| Název | Typ | Hodnota |
| ---- | ---- | ----- |
| arrayOutput | Pole | ["tři"] |
| stringOutput | Řetězec | 2 3 |

## <a name="take"></a>take

`take(originalValue, numberToTake)`

Vrátí pole se zadaným počtem prvků od začátku pole nebo řetězec, který má zadaný počet znaků od začátku řetězce.

### <a name="parameters"></a>Parametry

| Parametr | Povinné | Typ | Popis |
|:--- |:--- |:--- |:--- |
| Původní |Ano |pole nebo řetězec |Pole nebo řetězec, ze kterého mají být přebírat prvky. |
| numberToTake |Ano |int |Počet prvků nebo znaků, které mají být přebírat. Pokud je tato hodnota 0 nebo méně, vrátí se prázdné pole nebo řetězec. Pokud je větší než délka daného pole nebo řetězce, vrátí se všechny prvky v poli nebo řetězci. |

### <a name="return-value"></a>Vrácená hodnota

Pole nebo řetězec.

### <a name="example"></a>Příklad

Následující [příklad šablony](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/take.json) přebírá zadaný počet prvků z pole a znaky z řetězce.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
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

| Název | Typ | Hodnota |
| ---- | ---- | ----- |
| arrayOutput | Pole | ["One"; "Two"] |
| stringOutput | Řetězec | on |

## <a name="union"></a>sjednocení

`union(arg1, arg2, arg3, ...)`

Vrátí jedno pole nebo objekt se všemi prvky z parametrů. Duplicitní hodnoty nebo klíče jsou zahrnuté jenom jednou.

### <a name="parameters"></a>Parametry

| Parametr | Povinné | Typ | Popis |
|:--- |:--- |:--- |:--- |
| arg1 |Ano |pole nebo objekt |První hodnota, která se má použít pro spojování prvků. |
| arg2 |Ano |pole nebo objekt |Druhá hodnota, která se má použít pro spojování prvků. |
| Další argumenty |No |pole nebo objekt |Další hodnoty, které se mají použít pro spojování prvků. |

### <a name="return-value"></a>Vrácená hodnota

Pole nebo objekt.

### <a name="example"></a>Příklad

Následující [příklad šablony](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/union.json) ukazuje, jak použít sjednocení s poli a objekty:

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
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

| Název | Typ | Hodnota |
| ---- | ---- | ----- |
| objectOutput | Objekt | {"One": "a", "Two": "b", "tři": "C2", "čtyři": "d", "5": "e"} |
| arrayOutput | Pole | ["One", "Two", "tři", "čtyři"] |

## <a name="next-steps"></a>Další kroky

* Popis sekcí v šabloně Azure Resource Manager najdete v tématu [pochopení struktury a syntaxe šablon ARM](template-syntax.md).
