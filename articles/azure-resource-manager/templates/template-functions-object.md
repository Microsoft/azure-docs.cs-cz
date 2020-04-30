---
title: Funkce šablon – objekty
description: Popisuje funkce, které se použijí v šabloně Azure Resource Manager pro práci s objekty.
ms.topic: conceptual
ms.date: 04/27/2020
ms.openlocfilehash: 2f77cd85802a2ecb3670cfc6d6b36e5e852fb2a6
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/28/2020
ms.locfileid: "82231318"
---
# <a name="object-functions-for-arm-templates"></a>Funkce objektů pro šablony ARM

Správce prostředků poskytuje několik funkcí pro práci s objekty v šabloně Azure Resource Manager (ARM).

* [zobrazí](#contains)
* [empty](#empty)
* [průnik](#intersection)
* [JSON](#json)
* [časový](#length)
* [sjednocovací](#union)

## <a name="contains"></a>obsahuje

`contains(container, itemToFind)`

Kontroluje, zda pole obsahuje hodnotu, objekt obsahuje klíč, nebo řetězec obsahuje podřetězec. Porovnávání řetězců rozlišuje velká a malá písmena. Při testování, zda objekt obsahuje klíč, však porovnání nerozlišuje malá a velká písmena.

### <a name="parameters"></a>Parametry

| Parametr | Požaduje se | Typ | Popis |
|:--- |:--- |:--- |:--- |
| kontejner |Ano |pole, objekt nebo řetězec |Hodnota, která obsahuje hodnotu, která se má najít. |
| itemToFind |Ano |řetězec nebo int |Hodnota, která se má najít |

### <a name="return-value"></a>Návratová hodnota

**True** , pokud je položka nalezena; v opačném případě **false**.

### <a name="example"></a>Příklad

Následující [příklad šablony](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/contains.json) ukazuje, jak použít Contains s různými typy:

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

| Název | Typ | Hodnota |
| ---- | ---- | ----- |
| stringTrue | Logická hodnota | True |
| stringFalse | Logická hodnota | False |
| objectTrue | Logická hodnota | True |
| objectFalse | Logická hodnota | False |
| arrayTrue | Logická hodnota | True |
| arrayFalse | Logická hodnota | False |

## <a name="empty"></a>empty

`empty(itemToTest)`

Určuje, zda je pole, objekt nebo řetězec prázdný.

### <a name="parameters"></a>Parametry

| Parametr | Požaduje se | Typ | Popis |
|:--- |:--- |:--- |:--- |
| itemToTest |Ano |pole, objekt nebo řetězec |Hodnota, která zkontroluje, jestli je prázdná |

### <a name="return-value"></a>Návratová hodnota

Vrátí **hodnotu true** , pokud je hodnota prázdná. v opačném případě **false**.

### <a name="example"></a>Příklad

Následující [příklad šablony](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/empty.json) kontroluje, zda pole, objekt a řetězec jsou prázdné.

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

| Název | Typ | Hodnota |
| ---- | ---- | ----- |
| arrayEmpty | Logická hodnota | True |
| objectEmpty | Logická hodnota | True |
| stringEmpty | Logická hodnota | True |

## <a name="intersection"></a>průnik

`intersection(arg1, arg2, arg3, ...)`

Vrátí jedno pole nebo objekt se společnými prvky z parametrů.

### <a name="parameters"></a>Parametry

| Parametr | Požaduje se | Typ | Popis |
|:--- |:--- |:--- |:--- |
| arg1 |Ano |pole nebo objekt |První hodnota, která má být použita pro hledání běžných prvků. |
| arg2 |Ano |pole nebo objekt |Druhá hodnota, která má být použita pro vyhledání běžných prvků. |
| Další argumenty |Ne |pole nebo objekt |Další hodnoty, které se mají použít pro hledání běžných prvků. |

### <a name="return-value"></a>Návratová hodnota

Pole nebo objekt se společnými prvky.

### <a name="example"></a>Příklad

Následující [příklad šablony](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/intersection.json) ukazuje, jak použít průnik s poli a objekty:

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

| Název | Typ | Hodnota |
| ---- | ---- | ----- |
| objectOutput | Objekt | {"One": "a", "tři": "c"} |
| arrayOutput | Pole | ["Two", "tři"] |

## <a name="json"></a>json

`json(arg1)`

Vrátí objekt JSON.

### <a name="parameters"></a>Parametry

| Parametr | Požaduje se | Typ | Popis |
|:--- |:--- |:--- |:--- |
| arg1 |Ano |řetězec |Hodnota, která má být převedena do formátu JSON. |

### <a name="return-value"></a>Návratová hodnota

Objekt JSON ze zadaného řetězce nebo prázdný objekt, je-li zadána **hodnota null** .

### <a name="remarks"></a>Poznámky

Pokud potřebujete do objektu JSON zahrnout hodnotu parametru nebo proměnnou, použijte funkci [Concat](template-functions-string.md#concat) k vytvoření řetězce, který předáte do funkce.

### <a name="example"></a>Příklad

Následující [příklad šablony](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/json.json) ukazuje, jak používat funkci JSON. Všimněte si, že můžete buď předat řetězec, který představuje objekt, nebo použít **null** , pokud není potřeba žádná hodnota.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "jsonObject1": {
            "type": "string",
            "defaultValue": "null"
        },
        "jsonObject2": {
            "type": "string",
            "defaultValue": "{\"a\": \"b\"}"
        },
        "testValue": {
            "type": "string",
            "defaultValue": "demo value"
        }
    },
    "resources": [
    ],
    "outputs": {
        "jsonOutput1": {
            "type": "bool",
            "value": "[empty(json(parameters('jsonObject1')))]"
        },
        "jsonOutput2": {
            "type": "object",
            "value": "[json(parameters('jsonObject2'))]"
        },
        "paramOutput": {
            "type": "object",
            "value": "[json(concat('{\"a\": \"', parameters('testValue'), '\"}'))]"
        }
    }
}
```

Výstup z předchozího příkladu s výchozími hodnotami je:

| Název | Typ | Hodnota |
| ---- | ---- | ----- |
| jsonOutput1 | Logická hodnota | True |
| jsonOutput2 | Objekt | {"a": "b"} |
| paramOutput | Objekt | {"a": "demo hodnota"}

## <a name="length"></a>length

`length(arg1)`

Vrátí počet prvků v poli, znaky v řetězci nebo vlastnosti na kořenové úrovni objektu.

### <a name="parameters"></a>Parametry

| Parametr | Požaduje se | Typ | Popis |
|:--- |:--- |:--- |:--- |
| arg1 |Ano |pole, řetězec nebo objekt |Pole, které se má použít pro získání počtu prvků, řetězec, který se má použít pro získání počtu znaků, nebo objekt, který se má použít pro získání počtu vlastností na úrovni root. |

### <a name="return-value"></a>Návratová hodnota

Int.

### <a name="example"></a>Příklad

Následující [příklad šablony](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/length.json) ukazuje, jak použít délku s polem a řetězcem:

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

| Název | Typ | Hodnota |
| ---- | ---- | ----- |
| arrayLength | Int | 3 |
| stringLength | Int | 13 |
| objectLength | Int | 4 |

## <a name="union"></a>sjednocení

`union(arg1, arg2, arg3, ...)`

Vrátí jedno pole nebo objekt se všemi prvky z parametrů. Duplicitní hodnoty nebo klíče jsou zahrnuté jenom jednou.

### <a name="parameters"></a>Parametry

| Parametr | Požaduje se | Typ | Popis |
|:--- |:--- |:--- |:--- |
| arg1 |Ano |pole nebo objekt |První hodnota, která se má použít pro spojování prvků. |
| arg2 |Ano |pole nebo objekt |Druhá hodnota, která se má použít pro spojování prvků. |
| Další argumenty |Ne |pole nebo objekt |Další hodnoty, které se mají použít pro spojování prvků. |

### <a name="return-value"></a>Návratová hodnota

Pole nebo objekt.

### <a name="example"></a>Příklad

Následující [příklad šablony](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/union.json) ukazuje, jak použít sjednocení s poli a objekty:

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

| Název | Typ | Hodnota |
| ---- | ---- | ----- |
| objectOutput | Objekt | {"One": "a", "Two": "b", "tři": "C2", "čtyři": "d", "5": "e"} |
| arrayOutput | Pole | ["One", "Two", "tři", "čtyři"] |

## <a name="next-steps"></a>Další kroky

* Popis sekcí v šabloně Azure Resource Manager najdete v tématu [pochopení struktury a syntaxe šablon ARM](template-syntax.md).
