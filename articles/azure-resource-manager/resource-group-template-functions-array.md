---
title: Šablona Azure Resource Manageru funkce – pole a objekty | Dokumentace Microsoftu
description: Popisuje funkce pro použití v šabloně Azure Resource Manageru pro práci s poli a objekty.
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
ms.date: 09/24/2018
ms.author: tomfitz
ms.openlocfilehash: e0269e17a419c6b611d72a7d00668fe9c9519894
ms.sourcegitcommit: 51a1476c85ca518a6d8b4cc35aed7a76b33e130f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/25/2018
ms.locfileid: "47166177"
---
# <a name="array-and-object-functions-for-azure-resource-manager-templates"></a>Pole a objektu funkce pro šablony Azure Resource Manageru 

Resource Manager poskytuje několik funkcí pro práci s poli a objekty.

* [Pole](#array)
* [sloučení](#coalesce)
* [concat](#concat)
* [Obsahuje](#contains)
* [createArray](#createarray)
* [prázdný](#empty)
* [první](#first)
* [Průnik](#intersection)
* [json](#json)
* [poslední](#last)
* [Délka](#length)
* [max](#max)
* [min](#min)
* [rozsah](#range)
* [Přeskočit](#skip)
* [Take](#take)
* [sjednocení](#union)

K získání řetězcové hodnoty oddělené hodnotu pole, naleznete v tématu [rozdělit](resource-group-template-functions-string.md#split).

<a id="array" />

## <a name="array"></a>pole
`array(convertToArray)`

Převede hodnotu na pole.

### <a name="parameters"></a>Parametry

| Parametr | Požaduje se | Typ | Popis |
|:--- |:--- |:--- |:--- |
| convertToArray |Ano |int, string, pole nebo objekt |Hodnota k převedení na pole. |

### <a name="return-value"></a>Návratová hodnota

Pole.

### <a name="example"></a>Příklad:

Následující [Ukázková šablona](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/array.json) ukazuje, jak použít funkci pole s různými typy.

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

| Název | Typ | Hodnota |
| ---- | ---- | ----- |
| intOutput | Pole | [1] |
| stringOutput | Pole | ["efgh"] |
| objectOutput | Pole | [{"a": "b", "c": "d"}] |

Pokud chcete nasadit šablonu tento příklad pomocí Azure CLI, použijte:

```azurecli-interactive
az group deployment create -g functionexamplegroup --template-uri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/functions/array.json
```

Pokud chcete nasadit tento příklad šablony pomocí prostředí PowerShell, použijte:

```powershell
New-AzureRmResourceGroupDeployment -ResourceGroupName functionexamplegroup -TemplateUri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/functions/array.json
```

<a id="coalesce" />

## <a name="coalesce"></a>sloučení
`coalesce(arg1, arg2, arg3, ...)`

Vrátí první hodnotu než null z parametrů. Prázdné řetězce, pole prázdné a prázdné objekty nejsou null.

### <a name="parameters"></a>Parametry

| Parametr | Požaduje se | Typ | Popis |
|:--- |:--- |:--- |:--- |
| arg1 |Ano |int, string, pole nebo objekt |První hodnota pro test pro hodnotu null. |
| Další argumenty |Ne |int, string, pole nebo objekt |Další hodnoty pro testování hodnotu null. |

### <a name="return-value"></a>Návratová hodnota

Hodnota první parametry jinou hodnotu než null, které mohou být řetězec, int, pole nebo objekt. Hodnota Null, pokud všechny parametry mají hodnotu null. 

### <a name="example"></a>Příklad:

Následující [Ukázková šablona](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/coalesce.json) zobrazuje výstup z různých používá coalesce.

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
| objectOutput | Objekt | {"first": "Výchozí"} |
| arrayOutput | Pole | [1] |
| emptyOutput | BOOL | True |

Pokud chcete nasadit šablonu tento příklad pomocí Azure CLI, použijte:

```azurecli-interactive
az group deployment create -g functionexamplegroup --template-uri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/functions/coalesce.json
```

Pokud chcete nasadit tento příklad šablony pomocí prostředí PowerShell, použijte:

```powershell
New-AzureRmResourceGroupDeployment -ResourceGroupName functionexamplegroup -TemplateUri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/functions/coalesce.json
```

<a id="concat" />

## <a name="concat"></a>concat
`concat(arg1, arg2, arg3, ...)`

Kombinuje několik polí a vrátí pole zřetězených nebo kombinuje více řetězcových hodnot a vrací spojený řetězec. 

### <a name="parameters"></a>Parametry

| Parametr | Požaduje se | Typ | Popis |
|:--- |:--- |:--- |:--- |
| arg1 |Ano |poli nebo řetězci. |První pole nebo řetězec pro zřetězení. |
| Další argumenty |Ne |poli nebo řetězci. |Další pole nebo řetězce v postupném pořadí pro zřetězení. |

Tato funkce může přijmout libovolný počet argumentů a může přijmout řetězce nebo pole parametrů.

### <a name="return-value"></a>Návratová hodnota
Řetězec nebo pole zřetězených hodnot.

### <a name="example"></a>Příklad:

Následující [Ukázková šablona](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/concat-array.json) ukazuje, jak kombinace dvou polí.

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

Pokud chcete nasadit šablonu tento příklad pomocí Azure CLI, použijte:

```azurecli-interactive
az group deployment create -g functionexamplegroup --template-uri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/functions/concat-array.json
```

Pokud chcete nasadit tento příklad šablony pomocí prostředí PowerShell, použijte:

```powershell
New-AzureRmResourceGroupDeployment -ResourceGroupName functionexamplegroup -TemplateUri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/functions/concat-array.json
```

Následující [Ukázková šablona](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/concat-string.json) ukazuje, jak kombinovat dva řetězcové hodnoty a vrátí zřetězených řetězců.

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

Pokud chcete nasadit šablonu tento příklad pomocí Azure CLI, použijte:

```azurecli-interactive
az group deployment create -g functionexamplegroup --template-uri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/functions/concat-string.json
```

Pokud chcete nasadit tento příklad šablony pomocí prostředí PowerShell, použijte:

```powershell
New-AzureRmResourceGroupDeployment -ResourceGroupName functionexamplegroup -TemplateUri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/functions/concat-string.json
```

<a id="contains" />

## <a name="contains"></a>obsahuje
`contains(container, itemToFind)`

Kontroluje, zda pole obsahuje hodnotu, objekt obsahuje klíč nebo řetězec obsahuje daný podřetězec.

### <a name="parameters"></a>Parametry

| Parametr | Požaduje se | Typ | Popis |
|:--- |:--- |:--- |:--- |
| kontejner |Ano |pole, objekt nebo řetězec |Hodnota, která obsahuje hodnotu k vyhledání. |
| itemToFind |Ano |řetězec nebo int |Hodnota k vyhledání. |

### <a name="return-value"></a>Návratová hodnota

**Hodnota TRUE** Pokud byla položka nalezena; v opačném případě **False**.

### <a name="example"></a>Příklad:

Následující [Ukázková šablona](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/contains.json) ukazuje, jak používat obsahuje s různými typy:

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

Pokud chcete nasadit šablonu tento příklad pomocí Azure CLI, použijte:

```azurecli-interactive
az group deployment create -g functionexamplegroup --template-uri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/functions/contains.json
```

Pokud chcete nasadit tento příklad šablony pomocí prostředí PowerShell, použijte:

```powershell
New-AzureRmResourceGroupDeployment -ResourceGroupName functionexamplegroup -TemplateUri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/functions/contains.json
```

<a id="createarray" />

## <a name="createarray"></a>createarray
`createArray (arg1, arg2, arg3, ...)`

Vytvoří z parametrů pole.

### <a name="parameters"></a>Parametry

| Parametr | Požaduje se | Typ | Popis |
|:--- |:--- |:--- |:--- |
| arg1 |Ano |Řetězec, celé číslo, pole nebo objekt |První hodnota v poli. |
| Další argumenty |Ne |Řetězec, celé číslo, pole nebo objekt |Další hodnoty v poli. |

### <a name="return-value"></a>Návratová hodnota

Pole.

### <a name="example"></a>Příklad:

Následující [Ukázková šablona](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/createarray.json) ukazuje způsob použití createArray s různými typy:

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
| objectArray | Pole | [{"jedna": "a", "2": "b", "tři": "c"}] |
| arrayArray | Pole | [["jedna", "dvě", "tři"]] |

Pokud chcete nasadit šablonu tento příklad pomocí Azure CLI, použijte:

```azurecli-interactive
az group deployment create -g functionexamplegroup --template-uri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/functions/createarray.json
```

Pokud chcete nasadit tento příklad šablony pomocí prostředí PowerShell, použijte:

```powershell
New-AzureRmResourceGroupDeployment -ResourceGroupName functionexamplegroup -TemplateUri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/functions/createarray.json
```

<a id="empty" />

## <a name="empty"></a>prázdný

`empty(itemToTest)`

Určuje, zda je pole, objekt nebo řetězec prázdný.

### <a name="parameters"></a>Parametry

| Parametr | Požaduje se | Typ | Popis |
|:--- |:--- |:--- |:--- |
| itemToTest |Ano |pole, objekt nebo řetězec |Hodnota ke kontrole, jestli je prázdný. |

### <a name="return-value"></a>Návratová hodnota

Vrátí **True** Pokud prázdný; v opačném případě je hodnota **False**.

### <a name="example"></a>Příklad:

Následující [Ukázková šablona](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/empty.json) ověří, zda polí, objektu a řetězec jsou prázdné.

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

Pokud chcete nasadit šablonu tento příklad pomocí Azure CLI, použijte:

```azurecli-interactive
az group deployment create -g functionexamplegroup --template-uri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/functions/empty.json
```

Pokud chcete nasadit tento příklad šablony pomocí prostředí PowerShell, použijte:

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
| arg1 |Ano |poli nebo řetězci. |Hodnota načíst první prvek nebo znak. |

### <a name="return-value"></a>Návratová hodnota

Typ (řetězec, int, pole nebo objekt) první prvek v poli, nebo první znak řetězce.

### <a name="example"></a>Příklad:

Následující [Ukázková šablona](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/first.json) ukazuje, jak použít první funkce pomocí řetězec a pole.

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

Pokud chcete nasadit šablonu tento příklad pomocí Azure CLI, použijte:

```azurecli-interactive
az group deployment create -g functionexamplegroup --template-uri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/functions/first.json
```

Pokud chcete nasadit tento příklad šablony pomocí prostředí PowerShell, použijte:

```powershell
New-AzureRmResourceGroupDeployment -ResourceGroupName functionexamplegroup -TemplateUri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/functions/first.json
```

<a id="intersection" />

## <a name="intersection"></a>Průnik
`intersection(arg1, arg2, arg3, ...)`

Vrátí jedno pole nebo objekt s společné prvky z parametrů.

### <a name="parameters"></a>Parametry

| Parametr | Požaduje se | Typ | Popis |
|:--- |:--- |:--- |:--- |
| arg1 |Ano |pole nebo objekt |První hodnota pro hledání společné prvky. |
| arg2 |Ano |pole nebo objekt |Druhá hodnota pro hledání společné prvky. |
| Další argumenty |Ne |pole nebo objekt |Další hodnoty pro hledání společné prvky. |

### <a name="return-value"></a>Návratová hodnota

Pole nebo objekt s společné prvky.

### <a name="example"></a>Příklad:

Následující [Ukázková šablona](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/intersection.json) ukazuje způsob použití průnik s poli a objekty:

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
| objectOutput | Objekt | {"jedna": "a", "tři": "c"} |
| arrayOutput | Pole | ["dvě", "tři"] |

Pokud chcete nasadit šablonu tento příklad pomocí Azure CLI, použijte:

```azurecli-interactive
az group deployment create -g functionexamplegroup --template-uri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/functions/intersection.json
```

Pokud chcete nasadit tento příklad šablony pomocí prostředí PowerShell, použijte:

```powershell
New-AzureRmResourceGroupDeployment -ResourceGroupName functionexamplegroup -TemplateUri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/functions/intersection.json
```

## <a name="json"></a>json
`json(arg1)`

Vrátí objekt JSON.

### <a name="parameters"></a>Parametry

| Parametr | Požaduje se | Typ | Popis |
|:--- |:--- |:--- |:--- |
| arg1 |Ano |řetězec |Hodnota pro převod do formátu JSON. |


### <a name="return-value"></a>Návratová hodnota

Objekt JSON z zadaný řetězec nebo prázdný objekt při **null** je zadán.

### <a name="remarks"></a>Poznámky

Pokud je potřeba zahrnout parametr nebo proměnná v objektu JSON, použijte [concat](resource-group-template-functions-string.md#concat) funkci, která vytvoří řetězec, který můžete předat do funkce.

### <a name="example"></a>Příklad:

Následující [Ukázková šablona](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/json.json) ukazuje, jak pomocí pole a objekty funkce json:

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

| Název | Typ | Hodnota |
| ---- | ---- | ----- |
| jsonOutput | Objekt | {"a": "b"} |
| nullOutput | Logická hodnota | True |
| paramOutput | Objekt | {"a": "ukázka hodnotu"}

Pokud chcete nasadit šablonu tento příklad pomocí Azure CLI, použijte:

```azurecli-interactive
az group deployment create -g functionexamplegroup --template-uri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/functions/json.json
```

Pokud chcete nasadit tento příklad šablony pomocí prostředí PowerShell, použijte:

```powershell
New-AzureRmResourceGroupDeployment -ResourceGroupName functionexamplegroup -TemplateUri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/functions/json.json
```

<a id="last" />

## <a name="last"></a>poslední
`last (arg1)`

Vrátí poslední prvek pole nebo poslední znak řetězce.

### <a name="parameters"></a>Parametry

| Parametr | Požaduje se | Typ | Popis |
|:--- |:--- |:--- |:--- |
| arg1 |Ano |poli nebo řetězci. |Hodnota určená k načtení poslední prvek nebo znak. |

### <a name="return-value"></a>Návratová hodnota

Typ (řetězec, int, pole nebo objekt) po posledním prvku v poli, nebo poslední znak řetězce.

### <a name="example"></a>Příklad:

Následující [Ukázková šablona](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/last.json) ukazuje, jak použít funkci poslední pole a řetězce.

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
| stringOutput | Řetězec | e |

Pokud chcete nasadit šablonu tento příklad pomocí Azure CLI, použijte:

```azurecli-interactive
az group deployment create -g functionexamplegroup --template-uri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/functions/last.json
```

Pokud chcete nasadit tento příklad šablony pomocí prostředí PowerShell, použijte:

```powershell
New-AzureRmResourceGroupDeployment -ResourceGroupName functionexamplegroup -TemplateUri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/functions/last.json
```

<a id="length" />

## <a name="length"></a>Délka
`length(arg1)`

Vrátí počet prvků v poli nebo znaků v řetězci.

### <a name="parameters"></a>Parametry

| Parametr | Požaduje se | Typ | Popis |
|:--- |:--- |:--- |:--- |
| arg1 |Ano |poli nebo řetězci. |Pole, které chcete použít pro získání počet prvků, nebo řetězec určený pro maximální počet znaků. |

### <a name="return-value"></a>Návratová hodnota

Celé číslo 

### <a name="example"></a>Příklad:

Následující [Ukázková šablona](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/length.json) ukazuje způsob použití délku pole a řetězec:

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

Pokud chcete nasadit šablonu tento příklad pomocí Azure CLI, použijte:

```azurecli-interactive
az group deployment create -g functionexamplegroup --template-uri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/functions/length.json
```

Pokud chcete nasadit tento příklad šablony pomocí prostředí PowerShell, použijte:

```powershell
New-AzureRmResourceGroupDeployment -ResourceGroupName functionexamplegroup -TemplateUri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/functions/length.json
```

Tato funkce s polem slouží k určení počtu iterací při vytváření prostředků. V následujícím příkladu se parametr **siteNames** by odkazovat na pole názvů, které lze použít při vytváření webové stránky.

```json
"copy": {
    "name": "websitescopy",
    "count": "[length(parameters('siteNames'))]"
}
```

Další informace o použití této funkce s pole najdete v tématu [vytvořit více instancí prostředku v Azure Resource Manageru](resource-group-create-multiple.md).

<a id="max" />

## <a name="max"></a>max
`max(arg1)`

Vrátí maximální hodnotu z pole celých čísel nebo čárkami oddělený seznam celých čísel.

### <a name="parameters"></a>Parametry

| Parametr | Požaduje se | Typ | Popis |
|:--- |:--- |:--- |:--- |
| arg1 |Ano |pole celých čísel nebo čárkami oddělený seznam celých čísel |Kolekce, kterou chcete získat maximální hodnotu. |

### <a name="return-value"></a>Návratová hodnota

Celé číslo představující maximální hodnotu.

### <a name="example"></a>Příklad:

Následující [Ukázková šablona](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/max.json) ukazuje, jak použít maximum pole a seznamu celých čísel:

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

Pokud chcete nasadit šablonu tento příklad pomocí Azure CLI, použijte:

```azurecli-interactive
az group deployment create -g functionexamplegroup --template-uri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/functions/max.json
```

Pokud chcete nasadit tento příklad šablony pomocí prostředí PowerShell, použijte:

```powershell
New-AzureRmResourceGroupDeployment -ResourceGroupName functionexamplegroup -TemplateUri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/functions/max.json
```

<a id="min" />

## <a name="min"></a>min
`min(arg1)`

Vrátí minimální hodnotu z pole celých čísel nebo čárkami oddělený seznam celých čísel.

### <a name="parameters"></a>Parametry

| Parametr | Požaduje se | Typ | Popis |
|:--- |:--- |:--- |:--- |
| arg1 |Ano |pole celých čísel nebo čárkami oddělený seznam celých čísel |Kolekce, kterou chcete získat minimální hodnotu. |

### <a name="return-value"></a>Návratová hodnota

Celé číslo představující minimální hodnotu.

### <a name="example"></a>Příklad:

Následující [Ukázková šablona](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/min.json) ukazuje způsob použití min pole a seznamu celých čísel:

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

Pokud chcete nasadit šablonu tento příklad pomocí Azure CLI, použijte:

```azurecli-interactive
az group deployment create -g functionexamplegroup --template-uri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/functions/min.json
```

Pokud chcete nasadit tento příklad šablony pomocí prostředí PowerShell, použijte:

```powershell
New-AzureRmResourceGroupDeployment -ResourceGroupName functionexamplegroup -TemplateUri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/functions/min.json
```

<a id="range" />

## <a name="range"></a>rozsah
`range(startingInteger, numberOfElements)`

Z od celé číslo a který obsahuje počet položek, které vytvoří pole celých čísel.

### <a name="parameters"></a>Parametry

| Parametr | Požaduje se | Typ | Popis |
|:--- |:--- |:--- |:--- |
| startingInteger |Ano |int |První celé číslo v poli. |
| numberofElements |Ano |int |Počet celých čísel v poli. |

### <a name="return-value"></a>Návratová hodnota

Pole celých čísel.

### <a name="example"></a>Příklad:

Následující [Ukázková šablona](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/range.json) ukazuje, jak použít funkci rozsahu:

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

Pokud chcete nasadit šablonu tento příklad pomocí Azure CLI, použijte:

```azurecli-interactive
az group deployment create -g functionexamplegroup --template-uri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/functions/range.json
```

Pokud chcete nasadit tento příklad šablony pomocí prostředí PowerShell, použijte:

```powershell
New-AzureRmResourceGroupDeployment -ResourceGroupName functionexamplegroup -TemplateUri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/functions/range.json
```

<a id="skip" />

## <a name="skip"></a>Přeskočit
`skip(originalValue, numberToSkip)`

Vrátí pole obsahující všechny prvky po číslo zadané v poli, nebo vrátí řetězec, všechny znaky za číslo zadané v řetězci.

### <a name="parameters"></a>Parametry

| Parametr | Požaduje se | Typ | Popis |
|:--- |:--- |:--- |:--- |
| původní hodnota |Ano |poli nebo řetězci. |Pole nebo řetězec použitý pro přeskočení. |
| numberToSkip |Ano |int |Počet elementů nebo znaků, které mají přeskočit. Pokud tato hodnota je 0 nebo méně, budou vráceny všechny elementy nebo znaků v hodnotě. Pokud je větší než délka pole nebo řetězec, vrátí se prázdná pole nebo řetězec. |

### <a name="return-value"></a>Návratová hodnota

Pole nebo řetězec.

### <a name="example"></a>Příklad:

Následující [Ukázková šablona](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/skip.json) vynechá zadaný počet prvků v poli a zadaný počet znaků v řetězci.

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
| stringOutput | Řetězec | dva tři |

Pokud chcete nasadit šablonu tento příklad pomocí Azure CLI, použijte:

```azurecli-interactive
az group deployment create -g functionexamplegroup --template-uri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/functions/skip.json
```

Pokud chcete nasadit tento příklad šablony pomocí prostředí PowerShell, použijte:

```powershell
New-AzureRmResourceGroupDeployment -ResourceGroupName functionexamplegroup -TemplateUri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/functions/skip.json
```

<a id="take" />

## <a name="take"></a>Take
`take(originalValue, numberToTake)`

Vrátí pole obsahující zadaný počet prvků od začátku pole nebo řetězec s zadaný počet znaků od začátku řetězce.

### <a name="parameters"></a>Parametry

| Parametr | Požaduje se | Typ | Popis |
|:--- |:--- |:--- |:--- |
| původní hodnota |Ano |poli nebo řetězci. |Pole nebo řetězec, se prvky ze. |
| numberToTake |Ano |int |Počet elementů nebo znaků, které mají provést. Pokud tato hodnota je 0 nebo méně, je vrácena prázdná pole nebo řetězec. Pokud je větší než délka daného pole nebo řetězec, budou vráceny všechny prvky v poli nebo řetězci. |

### <a name="return-value"></a>Návratová hodnota

Pole nebo řetězec.

### <a name="example"></a>Příklad:

Následující [Ukázková šablona](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/take.json) má zadaný počet prvků v poli a znaků z řetězce.

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
| arrayOutput | Pole | ["1", "dvě"] |
| stringOutput | Řetězec | zapnuté |

Pokud chcete nasadit šablonu tento příklad pomocí Azure CLI, použijte:

```azurecli-interactive
az group deployment create -g functionexamplegroup --template-uri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/functions/take.json
```

Pokud chcete nasadit tento příklad šablony pomocí prostředí PowerShell, použijte:

```powershell
New-AzureRmResourceGroupDeployment -ResourceGroupName functionexamplegroup -TemplateUri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/functions/take.json
```

<a id="union" />

## <a name="union"></a>sjednocení
`union(arg1, arg2, arg3, ...)`

Vrátí jedno pole nebo objekt se všechny prvky z parametrů. Duplicitní hodnoty nebo klíče jsou pouze jednou zahrnuté.

### <a name="parameters"></a>Parametry

| Parametr | Požaduje se | Typ | Popis |
|:--- |:--- |:--- |:--- |
| arg1 |Ano |pole nebo objekt |První hodnota pro propojení elementů. |
| arg2 |Ano |pole nebo objekt |Druhá hodnota pro propojení elementů. |
| Další argumenty |Ne |pole nebo objekt |Další hodnoty se mají použít pro připojení k elementy. |

### <a name="return-value"></a>Návratová hodnota

Pole nebo objekt.

### <a name="example"></a>Příklad:

Následující [Ukázková šablona](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/union.json) ukazuje způsob použití sjednocení s poli a objekty:

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
| objectOutput | Objekt | {"jedna": "a", "2": "b", "tři": "c2", "čtyři": "d", "5": "e"} |
| arrayOutput | Pole | ["jedna", "dvě", "tři", "čtyři"] |

Pokud chcete nasadit šablonu tento příklad pomocí Azure CLI, použijte:

```azurecli-interactive
az group deployment create -g functionexamplegroup --template-uri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/functions/union.json
```

Pokud chcete nasadit tento příklad šablony pomocí prostředí PowerShell, použijte:

```powershell
New-AzureRmResourceGroupDeployment -ResourceGroupName functionexamplegroup -TemplateUri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/functions/union.json
```

## <a name="next-steps"></a>Další postup
* Popis části šablony Azure Resource Manageru najdete v tématu [šablon pro vytváření Azure Resource Manageru](resource-group-authoring-templates.md).
* Chcete-li sloučit několik šablon, přečtěte si téma [použití propojených šablon s Azure Resource Managerem](resource-group-linked-templates.md).
* K iteraci zadaného počtu opakování při vytváření konkrétní typ prostředku, naleznete v tématu [vytvořit více instancí prostředku v Azure Resource Manageru](resource-group-create-multiple.md).
* Postup nasazení šablony, které jste vytvořili, najdete v sekci [nasazení aplikace pomocí šablony Azure Resource Manageru](resource-group-template-deploy.md).

