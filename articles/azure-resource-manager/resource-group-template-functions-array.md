---
title: Šablona Azure Resource Manageru funkce – polí a objekty | Microsoft Docs
description: Popisuje funkce pro použití v šablonu Azure Resource Manageru pro práci s pole a objekty.
services: azure-resource-manager
documentationcenter: na
author: tfitzmac
manager: timlt
editor: tysonn
ms.assetid: ''
ms.service: azure-resource-manager
ms.devlang: na
ms.topic: reference
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 09/05/2017
ms.author: tomfitz
ms.openlocfilehash: eb24535956140632da73807364b6f3ff7b91a416
ms.sourcegitcommit: b6319f1a87d9316122f96769aab0d92b46a6879a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/20/2018
ms.locfileid: "34360236"
---
# <a name="array-and-object-functions-for-azure-resource-manager-templates"></a>Pole a objektu funkce pro šablony Azure Resource Manager 

Resource Manager poskytuje několik funkce pro práci s pole a objekty.

* [Pole](#array)
* [sloučení](#coalesce)
* [concat](#concat)
* [Obsahuje](#contains)
* [createArray](#createarray)
* [prázdný](#empty)
* [první](#first)
* [průnik](#intersection)
* [json](#json)
* [poslední](#last)
* [Délka](#length)
* [max](#max)
* [Min.](#min)
* [rozsah](#range)
* [Přeskočit](#skip)
* [proveďte](#take)
* [sjednocení](#union)

Pole hodnot řetězec oddělená hodnotu získáte v tématu [rozdělení](resource-group-template-functions-string.md#split).

<a id="array" />

## <a name="array"></a>pole
`array(convertToArray)`

Převede hodnotu na pole.

### <a name="parameters"></a>Parametry

| Parametr | Požaduje se | Typ | Popis |
|:--- |:--- |:--- |:--- |
| convertToArray |Ano |int, string, pole nebo objekt |Hodnota pro převod na pole. |

### <a name="return-value"></a>Návratová hodnota

Pole.

### <a name="example"></a>Příklad:

Následující [příklad šablony](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/array.json) ukazuje, jak chcete používat funkci pole s různými typy.

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
            "defaultValue": "a"
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
| intOutput | Pole | [1] |
| stringOutput | Pole | ["a"] |
| objectOutput | Pole | [{"a": "b", "c": "d"}] |

Chcete-li nasadit tento příklad šablony pomocí rozhraní příkazového řádku Azure, použijte:

```azurecli-interactive
az group deployment create -g functionexamplegroup --template-uri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/functions/array.json
```

Chcete-li nasadit tento příklad šablony v prostředí PowerShell, použijte:

```powershell
New-AzureRmResourceGroupDeployment -ResourceGroupName functionexamplegroup -TemplateUri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/functions/array.json
```

<a id="coalesce" />

## <a name="coalesce"></a>sloučení
`coalesce(arg1, arg2, arg3, ...)`

Vrátí první hodnotu než null z parametrů. Prázdné řetězce, prázdné pole a prázdné objekty nejsou null.

### <a name="parameters"></a>Parametry

| Parametr | Požaduje se | Typ | Popis |
|:--- |:--- |:--- |:--- |
| arg1 |Ano |int, string, pole nebo objekt |První hodnota k testování pro hodnotu null. |
| Další argumenty |Ne |int, string, pole nebo objekt |Další hodnoty, které chcete otestovat hodnotu null. |

### <a name="return-value"></a>Návratová hodnota

Hodnota první parametry jinou hodnotu než null, což může být řetězec, int, pole nebo objekt. Hodnota Null, pokud jsou všechny parametry hodnotu null. 

### <a name="example"></a>Příklad:

Následující [příklad šablony](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/coalesce.json) se zobrazuje výstup z různých používá funkci coalesce.

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

| Název | Typ | Hodnota |
| ---- | ---- | ----- |
| stringOutput | Řetězec | default |
| intOutput | Int | 1 |
| objectOutput | Objekt | {"první": "Výchozí"} |
| arrayOutput | Pole | [1] |
| emptyOutput | BOOL | True |

Chcete-li nasadit tento příklad šablony pomocí rozhraní příkazového řádku Azure, použijte:

```azurecli-interactive
az group deployment create -g functionexamplegroup --template-uri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/functions/coalesce.json
```

Chcete-li nasadit tento příklad šablony v prostředí PowerShell, použijte:

```powershell
New-AzureRmResourceGroupDeployment -ResourceGroupName functionexamplegroup -TemplateUri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/functions/coalesce.json
```

<a id="concat" />

## <a name="concat"></a>concat
`concat(arg1, arg2, arg3, ...)`

Kombinuje několik polí a vrátí pole zřetězených nebo kombinuje více řetězcové hodnoty a vrací spojený řetězec. 

### <a name="parameters"></a>Parametry

| Parametr | Požaduje se | Typ | Popis |
|:--- |:--- |:--- |:--- |
| arg1 |Ano |pole nebo řetězec |První pole nebo řetězec pro zřetězení. |
| Další argumenty |Ne |pole nebo řetězec |Další pole nebo řetězců v sekvenčním pořadí pro zřetězení. |

Tato funkce může trvat libovolný počet argumentů a může přijímat řetězce nebo pole parametrů.

### <a name="return-value"></a>Návratová hodnota
Řetězec nebo pole zřetězených hodnot.

### <a name="example"></a>Příklad:

Následující [příklad šablony](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/concat-array.json) ukazuje, jak kombinovat dvěma poli.

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

| Název | Typ | Hodnota |
| ---- | ---- | ----- |
| Vrátí | Pole | ["1-1", "1-2", "1-3", "2-1", "2-2", "2-3"] |

Chcete-li nasadit tento příklad šablony pomocí rozhraní příkazového řádku Azure, použijte:

```azurecli-interactive
az group deployment create -g functionexamplegroup --template-uri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/functions/concat-array.json
```

Chcete-li nasadit tento příklad šablony v prostředí PowerShell, použijte:

```powershell
New-AzureRmResourceGroupDeployment -ResourceGroupName functionexamplegroup -TemplateUri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/functions/concat-array.json
```

Následující [příklad šablony](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/concat-string.json) ukazuje, jak kombinovat dvou řetězcových hodnot a vrátí spojený řetězec.

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

| Název | Typ | Hodnota |
| ---- | ---- | ----- |
| concatOutput | Řetězec | Předpona 5yj4yjf5mbg72 |

Chcete-li nasadit tento příklad šablony pomocí rozhraní příkazového řádku Azure, použijte:

```azurecli-interactive
az group deployment create -g functionexamplegroup --template-uri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/functions/concat-string.json
```

Chcete-li nasadit tento příklad šablony v prostředí PowerShell, použijte:

```powershell
New-AzureRmResourceGroupDeployment -ResourceGroupName functionexamplegroup -TemplateUri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/functions/concat-string.json
```

<a id="contains" />

## <a name="contains"></a>obsahuje
`contains(container, itemToFind)`

Kontroluje, zda pole obsahuje hodnotu, objekt obsahuje klíč nebo řetězec obsahuje dílčí řetězec.

### <a name="parameters"></a>Parametry

| Parametr | Požaduje se | Typ | Popis |
|:--- |:--- |:--- |:--- |
| kontejner |Ano |pole, objekt nebo řetězec |Hodnota, která obsahuje hodnotu k vyhledání. |
| itemToFind |Ano |řetězec nebo celá čísla |Hodnota k vyhledání. |

### <a name="return-value"></a>Návratová hodnota

**Hodnota TRUE,** Pokud je položka, jinak hodnota **False**.

### <a name="example"></a>Příklad:

Následující [příklad šablony](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/contains.json) ukazuje, jak používat obsahuje s různými typy:

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
| stringTrue | BOOL | True |
| stringFalse | BOOL | False |
| objectTrue | BOOL | True |
| objectFalse | BOOL | False |
| arrayTrue | BOOL | True |
| arrayFalse | BOOL | False |

Chcete-li nasadit tento příklad šablony pomocí rozhraní příkazového řádku Azure, použijte:

```azurecli-interactive
az group deployment create -g functionexamplegroup --template-uri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/functions/contains.json
```

Chcete-li nasadit tento příklad šablony v prostředí PowerShell, použijte:

```powershell
New-AzureRmResourceGroupDeployment -ResourceGroupName functionexamplegroup -TemplateUri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/functions/contains.json
```

<a id="createarray" />

## <a name="createarray"></a>createarray
`createArray (arg1, arg2, arg3, ...)`

Vytvoří pole parametrů.

### <a name="parameters"></a>Parametry

| Parametr | Požaduje se | Typ | Popis |
|:--- |:--- |:--- |:--- |
| arg1 |Ano |Řetězec, celé číslo, pole nebo objekt |První hodnota v poli. |
| Další argumenty |Ne |Řetězec, celé číslo, pole nebo objekt |Další hodnoty v poli. |

### <a name="return-value"></a>Návratová hodnota

Pole.

### <a name="example"></a>Příklad:

Následující [příklad šablony](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/createarray.json) ukazuje způsob použití createArray s různými typy:

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

| Název | Typ | Hodnota |
| ---- | ---- | ----- |
| stringArray | Pole | ["a", "b", "c"] |
| intArray | Pole | [1, 2, 3] |
| objectArray | Pole | [{"1": "a", "dva": "b", "tři": "c"}] |
| arrayArray | Pole | [["1", "dva", "tři"]] |

Chcete-li nasadit tento příklad šablony pomocí rozhraní příkazového řádku Azure, použijte:

```azurecli-interactive
az group deployment create -g functionexamplegroup --template-uri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/functions/createarray.json
```

Chcete-li nasadit tento příklad šablony v prostředí PowerShell, použijte:

```powershell
New-AzureRmResourceGroupDeployment -ResourceGroupName functionexamplegroup -TemplateUri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/functions/createarray.json
```

<a id="empty" />

## <a name="empty"></a>prázdný

`empty(itemToTest)`

Určuje, zda je prázdný řetězec, objekt nebo pole.

### <a name="parameters"></a>Parametry

| Parametr | Požaduje se | Typ | Popis |
|:--- |:--- |:--- |:--- |
| itemToTest |Ano |pole, objekt nebo řetězec |Hodnota ke kontrole, jestli je prázdný. |

### <a name="return-value"></a>Návratová hodnota

Vrátí **True** Pokud hodnota je prázdný, jinak hodnota **False**.

### <a name="example"></a>Příklad:

Následující [příklad šablony](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/empty.json) ověří, zda jsou řetězec, objekt a pole prázdné.

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
| arrayEmpty | BOOL | True |
| objectEmpty | BOOL | True |
| stringEmpty | BOOL | True |

Chcete-li nasadit tento příklad šablony pomocí rozhraní příkazového řádku Azure, použijte:

```azurecli-interactive
az group deployment create -g functionexamplegroup --template-uri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/functions/empty.json
```

Chcete-li nasadit tento příklad šablony v prostředí PowerShell, použijte:

```powershell
New-AzureRmResourceGroupDeployment -ResourceGroupName functionexamplegroup -TemplateUri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/functions/empty.json
```

<a id="first" />

## <a name="first"></a>první
`first(arg1)`

Vrátí první prvek pole nebo první znak řetězce.

### <a name="parameters"></a>Parametry

| Parametr | Požaduje se | Typ | Popis |
|:--- |:--- |:--- |:--- |
| arg1 |Ano |pole nebo řetězec |Hodnota k načtení první element nebo znak. |

### <a name="return-value"></a>Návratová hodnota

Typ (řetězec, int, pole nebo objekt) první prvek pole nebo první znak řetězce.

### <a name="example"></a>Příklad:

Následující [příklad šablony](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/first.json) ukazuje, jak používat funkci první s řetězec a pole.

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

| Název | Typ | Hodnota |
| ---- | ---- | ----- |
| arrayOutput | Řetězec | jeden |
| stringOutput | Řetězec | O |

Chcete-li nasadit tento příklad šablony pomocí rozhraní příkazového řádku Azure, použijte:

```azurecli-interactive
az group deployment create -g functionexamplegroup --template-uri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/functions/first.json
```

Chcete-li nasadit tento příklad šablony v prostředí PowerShell, použijte:

```powershell
New-AzureRmResourceGroupDeployment -ResourceGroupName functionexamplegroup -TemplateUri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/functions/first.json
```

<a id="intersection" />

## <a name="intersection"></a>průnik
`intersection(arg1, arg2, arg3, ...)`

Vrátí objekt s běžných elementech nebo jednoho pole z parametrů.

### <a name="parameters"></a>Parametry

| Parametr | Požaduje se | Typ | Popis |
|:--- |:--- |:--- |:--- |
| arg1 |Ano |objekt nebo pole |První hodnota, kterou chcete použít pro hledání běžných elementech. |
| arg2 |Ano |objekt nebo pole |Druhá hodnota, kterou chcete použít pro hledání běžných elementech. |
| Další argumenty |Ne |objekt nebo pole |Další hodnoty, které chcete použít pro hledání běžných elementech. |

### <a name="return-value"></a>Návratová hodnota

Pole nebo objekt s běžných elementech.

### <a name="example"></a>Příklad:

Následující [příklad šablony](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/intersection.json) ukazuje, jak používat průnik s pole a objekty:

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
| objectOutput | Objekt | {"1": "a", "tři": "c"} |
| arrayOutput | Pole | ["dva", "tři"] |

Chcete-li nasadit tento příklad šablony pomocí rozhraní příkazového řádku Azure, použijte:

```azurecli-interactive
az group deployment create -g functionexamplegroup --template-uri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/functions/intersection.json
```

Chcete-li nasadit tento příklad šablony v prostředí PowerShell, použijte:

```powershell
New-AzureRmResourceGroupDeployment -ResourceGroupName functionexamplegroup -TemplateUri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/functions/intersection.json
```

## <a name="json"></a>json
`json(arg1)`

Vrátí objekt JSON.

### <a name="parameters"></a>Parametry

| Parametr | Požaduje se | Typ | Popis |
|:--- |:--- |:--- |:--- |
| arg1 |Ano |řetězec |Hodnota k převést na JSON. |


### <a name="return-value"></a>Návratová hodnota

Objekt JSON v zadaný řetězec nebo objekt prázdný při **null** je zadán.

### <a name="example"></a>Příklad:

Následující [příklad šablony](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/json.json) ukazuje, jak používat funkci json s pole a objekty:

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
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
        }
    }
}
```

Výstup z předchozího příkladu s výchozími hodnotami je:

| Název | Typ | Hodnota |
| ---- | ---- | ----- |
| jsonOutput | Objekt | {"a": "b"} |
| nullOutput | Logická hodnota | True |

Chcete-li nasadit tento příklad šablony pomocí rozhraní příkazového řádku Azure, použijte:

```azurecli-interactive
az group deployment create -g functionexamplegroup --template-uri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/functions/json.json
```

Chcete-li nasadit tento příklad šablony v prostředí PowerShell, použijte:

```powershell
New-AzureRmResourceGroupDeployment -ResourceGroupName functionexamplegroup -TemplateUri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/functions/json.json
```

<a id="last" />

## <a name="last"></a>poslední
`last (arg1)`

Vrátí poslední element pole nebo poslední znak řetězce.

### <a name="parameters"></a>Parametry

| Parametr | Požaduje se | Typ | Popis |
|:--- |:--- |:--- |:--- |
| arg1 |Ano |pole nebo řetězec |Hodnota k načtení poslední element nebo znak. |

### <a name="return-value"></a>Návratová hodnota

Typ (řetězec, int, pole nebo objekt) posledním prvkem v pole nebo poslední znak řetězce.

### <a name="example"></a>Příklad:

Následující [příklad šablony](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/last.json) ukazuje, jak používat funkci naposledy s řetězec a pole.

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

| Název | Typ | Hodnota |
| ---- | ---- | ----- |
| arrayOutput | Řetězec | tři |
| stringOutput | Řetězec | E |

Chcete-li nasadit tento příklad šablony pomocí rozhraní příkazového řádku Azure, použijte:

```azurecli-interactive
az group deployment create -g functionexamplegroup --template-uri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/functions/last.json
```

Chcete-li nasadit tento příklad šablony v prostředí PowerShell, použijte:

```powershell
New-AzureRmResourceGroupDeployment -ResourceGroupName functionexamplegroup -TemplateUri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/functions/last.json
```

<a id="length" />

## <a name="length"></a>Délka
`length(arg1)`

Vrátí počet prvků pole nebo znaků v řetězci.

### <a name="parameters"></a>Parametry

| Parametr | Požaduje se | Typ | Popis |
|:--- |:--- |:--- |:--- |
| arg1 |Ano |pole nebo řetězec |Pole na použití pro získání počet elementů nebo řetězec Pokud chcete použít pro maximální počet znaků. |

### <a name="return-value"></a>Návratová hodnota

Typ int. 

### <a name="example"></a>Příklad:

Následující [příklad šablony](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/length.json) ukazuje, jak používat délka s řetězec a pole:

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
        }
    }
}
```

Výstup z předchozího příkladu s výchozími hodnotami je:

| Název | Typ | Hodnota |
| ---- | ---- | ----- |
| arrayLength | Int | 3 |
| stringLength | Int | 13 |

Chcete-li nasadit tento příklad šablony pomocí rozhraní příkazového řádku Azure, použijte:

```azurecli-interactive
az group deployment create -g functionexamplegroup --template-uri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/functions/length.json
```

Chcete-li nasadit tento příklad šablony v prostředí PowerShell, použijte:

```powershell
New-AzureRmResourceGroupDeployment -ResourceGroupName functionexamplegroup -TemplateUri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/functions/length.json
```

Tato funkce se pole vám pomůže při vytváření prostředků zadat počet opakování. V následujícím příkladu, parametr **siteNames** by odkazovat na pole názvů pro použití při vytváření webových serverů.

```json
"copy": {
    "name": "websitescopy",
    "count": "[length(parameters('siteNames'))]"
}
```

Další informace o použití této funkce se pole najdete v tématu [vytvořit více instancí prostředků ve službě Správce prostředků Azure](resource-group-create-multiple.md).

<a id="max" />

## <a name="max"></a>max
`max(arg1)`

Vrací maximální hodnotu z pole celá čísla nebo seznam celých čísel oddělených čárkami.

### <a name="parameters"></a>Parametry

| Parametr | Požaduje se | Typ | Popis |
|:--- |:--- |:--- |:--- |
| arg1 |Ano |pole celá čísla nebo seznam celých čísel oddělených čárkou |Kolekce získat maximální hodnotu. |

### <a name="return-value"></a>Návratová hodnota

Typ int představující maximální hodnotu.

### <a name="example"></a>Příklad:

Následující [příklad šablony](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/max.json) ukazuje, jak použít maximum s pole a seznam celých čísel:

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

| Název | Typ | Hodnota |
| ---- | ---- | ----- |
| arrayOutput | Int | 5 |
| intOutput | Int | 5 |

Chcete-li nasadit tento příklad šablony pomocí rozhraní příkazového řádku Azure, použijte:

```azurecli-interactive
az group deployment create -g functionexamplegroup --template-uri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/functions/max.json
```

Chcete-li nasadit tento příklad šablony v prostředí PowerShell, použijte:

```powershell
New-AzureRmResourceGroupDeployment -ResourceGroupName functionexamplegroup -TemplateUri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/functions/max.json
```

<a id="min" />

## <a name="min"></a>min
`min(arg1)`

Vrátí minimální hodnotu z pole celá čísla nebo seznam celých čísel oddělených čárkami.

### <a name="parameters"></a>Parametry

| Parametr | Požaduje se | Typ | Popis |
|:--- |:--- |:--- |:--- |
| arg1 |Ano |pole celá čísla nebo seznam celých čísel oddělených čárkou |Kolekce získat minimální hodnotu. |

### <a name="return-value"></a>Návratová hodnota

Typ int představující minimální hodnotu.

### <a name="example"></a>Příklad:

Následující [příklad šablony](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/min.json) ukazuje, jak používat min s pole a seznam celých čísel:

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

| Název | Typ | Hodnota |
| ---- | ---- | ----- |
| arrayOutput | Int | 0 |
| intOutput | Int | 0 |

Chcete-li nasadit tento příklad šablony pomocí rozhraní příkazového řádku Azure, použijte:

```azurecli-interactive
az group deployment create -g functionexamplegroup --template-uri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/functions/min.json
```

Chcete-li nasadit tento příklad šablony v prostředí PowerShell, použijte:

```powershell
New-AzureRmResourceGroupDeployment -ResourceGroupName functionexamplegroup -TemplateUri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/functions/min.json
```

<a id="range" />

## <a name="range"></a>rozsah
`range(startingInteger, numberOfElements)`

Vytvoří pole celých čísel od spuštění celé číslo a který obsahuje počet položek.

### <a name="parameters"></a>Parametry

| Parametr | Požaduje se | Typ | Popis |
|:--- |:--- |:--- |:--- |
| startingInteger |Ano |celá čísla |První celé číslo v poli. |
| numberofElements |Ano |celá čísla |Počet celých čísel v poli. |

### <a name="return-value"></a>Návratová hodnota

Pole celých čísel.

### <a name="example"></a>Příklad:

Následující [příklad šablony](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/range.json) ukazuje, jak používat funkci rozsahu:

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

| Název | Typ | Hodnota |
| ---- | ---- | ----- |
| rangeOutput | Pole | [5, 6, 7] |

Chcete-li nasadit tento příklad šablony pomocí rozhraní příkazového řádku Azure, použijte:

```azurecli-interactive
az group deployment create -g functionexamplegroup --template-uri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/functions/range.json
```

Chcete-li nasadit tento příklad šablony v prostředí PowerShell, použijte:

```powershell
New-AzureRmResourceGroupDeployment -ResourceGroupName functionexamplegroup -TemplateUri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/functions/range.json
```

<a id="skip" />

## <a name="skip"></a>Přeskočit
`skip(originalValue, numberToSkip)`

Vrátí pole s všechny elementy po zadaný počet v poli nebo vrátí řetězec s odebranými znaky po zadaný počet v řetězci.

### <a name="parameters"></a>Parametry

| Parametr | Požaduje se | Typ | Popis |
|:--- |:--- |:--- |:--- |
| původní hodnota |Ano |pole nebo řetězec |Pole nebo řetězec, který má používat pro přeskočení. |
| numberToSkip |Ano |celá čísla |Počet elementů nebo znaků, které chcete vynechat. Pokud tato hodnota je 0 nebo menší, vrátí se všechny elementy nebo znaků v hodnotě. Pokud je větší než délka pole nebo řetězec, se vrátí prázdné pole nebo řetězec. |

### <a name="return-value"></a>Návratová hodnota

Pole nebo řetězec.

### <a name="example"></a>Příklad:

Následující [příklad šablony](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/skip.json) přeskočí zadaný počet elementů v poli a zadaný počet znaků v řetězci.

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

| Název | Typ | Hodnota |
| ---- | ---- | ----- |
| arrayOutput | Pole | ["tři"] |
| stringOutput | Řetězec | dvě tři |

Chcete-li nasadit tento příklad šablony pomocí rozhraní příkazového řádku Azure, použijte:

```azurecli-interactive
az group deployment create -g functionexamplegroup --template-uri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/functions/skip.json
```

Chcete-li nasadit tento příklad šablony v prostředí PowerShell, použijte:

```powershell
New-AzureRmResourceGroupDeployment -ResourceGroupName functionexamplegroup -TemplateUri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/functions/skip.json
```

<a id="take" />

## <a name="take"></a>proveďte
`take(originalValue, numberToTake)`

Vrátí pole s zadaný počet elementů od začátku pole nebo řetězec s zadaný počet znaků od začátku řetězce.

### <a name="parameters"></a>Parametry

| Parametr | Požaduje se | Typ | Popis |
|:--- |:--- |:--- |:--- |
| původní hodnota |Ano |pole nebo řetězec |Pole nebo řetězec, který má trvat elementy ze. |
| numberToTake |Ano |celá čísla |Počet elementů nebo znaků, který má trvat. Pokud tato hodnota je 0 nebo menší, se vrátí prázdné pole nebo řetězec. Pokud je větší než délka dané pole nebo řetězec, vrátí se všechny elementy ve pole nebo řetězec. |

### <a name="return-value"></a>Návratová hodnota

Pole nebo řetězec.

### <a name="example"></a>Příklad:

Následující [příklad šablony](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/take.json) provede zadaný počet elementů z pole a znaků z řetězce.

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

| Název | Typ | Hodnota |
| ---- | ---- | ----- |
| arrayOutput | Pole | ["1", "dva"] |
| stringOutput | Řetězec | zapnuté |

Chcete-li nasadit tento příklad šablony pomocí rozhraní příkazového řádku Azure, použijte:

```azurecli-interactive
az group deployment create -g functionexamplegroup --template-uri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/functions/take.json
```

Chcete-li nasadit tento příklad šablony v prostředí PowerShell, použijte:

```powershell
New-AzureRmResourceGroupDeployment -ResourceGroupName functionexamplegroup -TemplateUri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/functions/take.json
```

<a id="union" />

## <a name="union"></a>sjednocení
`union(arg1, arg2, arg3, ...)`

Vrací jednu pole nebo objekt s všechny elementy z parametrů. Duplicitní hodnoty nebo klíče jsou jenom jednou zahrnuty.

### <a name="parameters"></a>Parametry

| Parametr | Požaduje se | Typ | Popis |
|:--- |:--- |:--- |:--- |
| arg1 |Ano |objekt nebo pole |První hodnota má použít pro připojení elementy. |
| arg2 |Ano |objekt nebo pole |Druhá hodnota má použít pro připojení elementy. |
| Další argumenty |Ne |objekt nebo pole |Další hodnoty má použít pro připojení elementy. |

### <a name="return-value"></a>Návratová hodnota

Pole nebo objekt.

### <a name="example"></a>Příklad:

Následující [příklad šablony](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/union.json) ukazuje, jak používat sjednocení s pole a objekty:

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
| objectOutput | Objekt | {"1": "a", "dva": "b", "tři": "c2", "čtyři": "d", "5": "e"} |
| arrayOutput | Pole | ["1", "dva", "tři", "čtyři"] |

Chcete-li nasadit tento příklad šablony pomocí rozhraní příkazového řádku Azure, použijte:

```azurecli-interactive
az group deployment create -g functionexamplegroup --template-uri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/functions/union.json
```

Chcete-li nasadit tento příklad šablony v prostředí PowerShell, použijte:

```powershell
New-AzureRmResourceGroupDeployment -ResourceGroupName functionexamplegroup -TemplateUri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/functions/union.json
```

## <a name="next-steps"></a>Další postup
* Popis v částech šablonu Azure Resource Manager naleznete v tématu [šablon pro tvorbu Azure Resource Manageru](resource-group-authoring-templates.md).
* Sloučit několik šablon, najdete v části [použití propojených šablon s Azure Resource Manager](resource-group-linked-templates.md).
* K iteraci v zadaného počtu opakování při vytváření typu prostředku, najdete v části [vytvořit více instancí prostředků ve službě Správce prostředků Azure](resource-group-create-multiple.md).
* Postup nasazení šablony, které jste vytvořili, najdete v sekci [nasazení aplikace pomocí šablony Azure Resource Manageru](resource-group-template-deploy.md).

