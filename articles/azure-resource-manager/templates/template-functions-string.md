---
title: Funkce šablony - řetězec
description: Popisuje funkce, které se mají použít v šabloně Azure Resource Manager pro práci s řetězci.
ms.topic: conceptual
ms.date: 04/08/2020
ms.openlocfilehash: c0517375b273384f263e8ba421995d4afb6c193b
ms.sourcegitcommit: 7d8158fcdcc25107dfda98a355bf4ee6343c0f5c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/09/2020
ms.locfileid: "80982410"
---
# <a name="string-functions-for-arm-templates"></a>Řetězcové funkce pro šablony ARM

Správce prostředků poskytuje následující funkce pro práci s řetězci v šablonách Správce prostředků Azure (ARM):

* [base64](#base64)
* [base64ToJson](#base64tojson)
* [base64ToString](#base64tostring)
* [Concat](#concat)
* [Obsahuje](#contains)
* [dataUri](#datauri)
* [dataUriToString](#datauritostring)
* [empty](#empty)
* [Endswith](#endswith)
* [První](#first)
* [Formát](#format)
* [Identifikátor guid](#guid)
* [Indexof](#indexof)
* [Poslední](#last)
* [Lastindexof](#lastindexof)
* [Délka](#length)
* [newGuid](#newguid)
* [oložek vlevo](#padleft)
* [Nahradit](#replace)
* [Přeskočit](#skip)
* [Rozdělit](#split)
* [Startswith](#startswith)
* [Řetězec](#string)
* [Podřetězec](#substring)
* [vzít](#take)
* [Tolower](#tolower)
* [Toupper](#toupper)
* [Trim](#trim)
* [uniqueString](#uniquestring)
* [Uri](#uri)
* [uriComponent](#uricomponent)
* [uriComponentToString](#uricomponenttostring)

## <a name="base64"></a>base64

`base64(inputString)`

Vrátí reprezentaci vstupního řetězce base64.

### <a name="parameters"></a>Parametry

| Parametr | Požaduje se | Typ | Popis |
|:--- |:--- |:--- |:--- |
| inputString |Ano |řetězec |Hodnota, která má být vrácena jako reprezentace base64. |

### <a name="return-value"></a>Návratová hodnota

Řetězec obsahující reprezentaci base64.

### <a name="examples"></a>Příklady

Následující [ukázková šablona](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/base64.json) ukazuje, jak používat funkci base64.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "stringData": {
            "type": "string",
            "defaultValue": "one, two, three"
        },
        "jsonFormattedData": {
            "type": "string",
            "defaultValue": "{'one': 'a', 'two': 'b'}"
        }
    },
    "variables": {
        "base64String": "[base64(parameters('stringData'))]",
        "base64Object": "[base64(parameters('jsonFormattedData'))]"
    },
    "resources": [
    ],
    "outputs": {
        "base64Output": {
            "type": "string",
            "value": "[variables('base64String')]"
        },
        "toStringOutput": {
            "type": "string",
            "value": "[base64ToString(variables('base64String'))]"
        },
        "toJsonOutput": {
            "type": "object",
            "value": "[base64ToJson(variables('base64Object'))]"
        }
    }
}
```

Výstup z předchozího příkladu s výchozími hodnotami je:

| Name (Název) | Typ | Hodnota |
| ---- | ---- | ----- |
| base64Výstup | Řetězec | b25lLCB0d28sIHRocmVl |
| toStringOutput | Řetězec | Jedna dva tři |
| toJsonOutput | Objekt | {"one": "a", "dva": "b"} |

## <a name="base64tojson"></a>base64ToJson

`base64tojson`

Převede reprezentaci base64 na objekt JSON.

### <a name="parameters"></a>Parametry

| Parametr | Požaduje se | Typ | Popis |
|:--- |:--- |:--- |:--- |
| base64Hodnota |Ano |řetězec |Base64 reprezentace převést na objekt JSON. |

### <a name="return-value"></a>Návratová hodnota

A JSON objekt.

### <a name="examples"></a>Příklady

Následující [ukázková šablona](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/base64.json) používá funkci base64ToJson k převodu hodnoty base64:

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "stringData": {
            "type": "string",
            "defaultValue": "one, two, three"
        },
        "jsonFormattedData": {
            "type": "string",
            "defaultValue": "{'one': 'a', 'two': 'b'}"
        }
    },
    "variables": {
        "base64String": "[base64(parameters('stringData'))]",
        "base64Object": "[base64(parameters('jsonFormattedData'))]"
    },
    "resources": [
    ],
    "outputs": {
        "base64Output": {
            "type": "string",
            "value": "[variables('base64String')]"
        },
        "toStringOutput": {
            "type": "string",
            "value": "[base64ToString(variables('base64String'))]"
        },
        "toJsonOutput": {
            "type": "object",
            "value": "[base64ToJson(variables('base64Object'))]"
        }
    }
}
```

Výstup z předchozího příkladu s výchozími hodnotami je:

| Name (Název) | Typ | Hodnota |
| ---- | ---- | ----- |
| base64Výstup | Řetězec | b25lLCB0d28sIHRocmVl |
| toStringOutput | Řetězec | Jedna dva tři |
| toJsonOutput | Objekt | {"one": "a", "dva": "b"} |

## <a name="base64tostring"></a>base64ToString

`base64ToString(base64Value)`

Převede reprezentaci base64 na řetězec.

### <a name="parameters"></a>Parametry

| Parametr | Požaduje se | Typ | Popis |
|:--- |:--- |:--- |:--- |
| base64Hodnota |Ano |řetězec |Base64 reprezentace převést na řetězec. |

### <a name="return-value"></a>Návratová hodnota

Řetězec převedené hodnoty base64.

### <a name="examples"></a>Příklady

Následující [ukázková šablona](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/base64.json) používá funkci base64ToString k převodu hodnoty base64:

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "stringData": {
            "type": "string",
            "defaultValue": "one, two, three"
        },
        "jsonFormattedData": {
            "type": "string",
            "defaultValue": "{'one': 'a', 'two': 'b'}"
        }
    },
    "variables": {
        "base64String": "[base64(parameters('stringData'))]",
        "base64Object": "[base64(parameters('jsonFormattedData'))]"
    },
    "resources": [
    ],
    "outputs": {
        "base64Output": {
            "type": "string",
            "value": "[variables('base64String')]"
        },
        "toStringOutput": {
            "type": "string",
            "value": "[base64ToString(variables('base64String'))]"
        },
        "toJsonOutput": {
            "type": "object",
            "value": "[base64ToJson(variables('base64Object'))]"
        }
    }
}
```

Výstup z předchozího příkladu s výchozími hodnotami je:

| Name (Název) | Typ | Hodnota |
| ---- | ---- | ----- |
| base64Výstup | Řetězec | b25lLCB0d28sIHRocmVl |
| toStringOutput | Řetězec | Jedna dva tři |
| toJsonOutput | Objekt | {"one": "a", "dva": "b"} |

## <a name="concat"></a>concat

`concat (arg1, arg2, arg3, ...)`

Zkombinuje více řetězcových hodnot a vrátí zřetězený řetězec nebo zkombinuje více polí a vrátí zřetězené pole.

### <a name="parameters"></a>Parametry

| Parametr | Požaduje se | Typ | Popis |
|:--- |:--- |:--- |:--- |
| arg1 |Ano |řetězec nebo pole |První řetězec nebo pole pro zřetězení. |
| další argumenty |Ne |řetězec nebo pole |Další řetězce nebo pole v sekvenčním pořadí pro zřetězení. |

Tato funkce může trvat libovolný počet argumentů a může přijmout řetězce nebo pole pro parametry. Však nelze zadat pole a řetězce pro parametry. Řetězce jsou pouze zřetězené s jinými řetězci.

### <a name="return-value"></a>Návratová hodnota

Řetězec nebo pole zřetězených hodnot.

### <a name="examples"></a>Příklady

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

## <a name="contains"></a>obsahuje

`contains (container, itemToFind)`

Zkontroluje, zda pole obsahuje hodnotu, objekt obsahuje klíč nebo řetězec obsahuje podřetězec. Porovnání řetězců rozlišuje malá a velká písmena. Však při testování, pokud objekt obsahuje klíč, porovnání je malá a velká písmena.

### <a name="parameters"></a>Parametry

| Parametr | Požaduje se | Typ | Popis |
|:--- |:--- |:--- |:--- |
| kontejner |Ano |pole, objekt nebo řetězec |Hodnota, která obsahuje hodnotu najít. |
| položkaHledání |Ano |řetězec nebo int |Hodnota najít. |

### <a name="return-value"></a>Návratová hodnota

**True,** pokud je položka nalezena; jinak **False**.

### <a name="examples"></a>Příklady

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

## <a name="datauri"></a>dataUri

`dataUri(stringToConvert)`

Převede hodnotu na identifikátor URI dat.

### <a name="parameters"></a>Parametry

| Parametr | Požaduje se | Typ | Popis |
|:--- |:--- |:--- |:--- |
| stringChcete-li převést |Ano |řetězec |Hodnota, kterou chcete převést na identifikátor URI dat. |

### <a name="return-value"></a>Návratová hodnota

Řetězec formátovaný jako identifikátor URI dat.

### <a name="examples"></a>Příklady

Následující [ukázková šablona](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/datauri.json) převede hodnotu na identifikátor URI dat a převede identifikátor URI dat na řetězec:

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "stringToTest": {
            "type": "string",
            "defaultValue": "Hello"
        },
        "dataFormattedString": {
            "type": "string",
            "defaultValue": "data:;base64,SGVsbG8sIFdvcmxkIQ=="
        }
    },
    "resources": [],
    "outputs": {
        "dataUriOutput": {
            "value": "[dataUri(parameters('stringToTest'))]",
            "type" : "string"
        },
        "toStringOutput": {
            "type": "string",
            "value": "[dataUriToString(parameters('dataFormattedString'))]"
        }
    }
}
```

Výstup z předchozího příkladu s výchozími hodnotami je:

| Name (Název) | Typ | Hodnota |
| ---- | ---- | ----- |
| dataUriOutput | Řetězec | data:text/plain;charset=utf8;base64,SGVsbG8= |
| toStringOutput | Řetězec | Ahoj světe! |

## <a name="datauritostring"></a>dataUriToString

`dataUriToString(dataUriToConvert)`

Převede hodnotu formátovanou identifikátorem URI dat na řetězec.

### <a name="parameters"></a>Parametry

| Parametr | Požaduje se | Typ | Popis |
|:--- |:--- |:--- |:--- |
| dataUriToConvert |Ano |řetězec |Hodnota identifikátoru URI dat, který chcete převést. |

### <a name="return-value"></a>Návratová hodnota

Řetězec obsahující převedenou hodnotu.

### <a name="examples"></a>Příklady

Následující [ukázková šablona](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/datauri.json) převede hodnotu na identifikátor URI dat a převede identifikátor URI dat na řetězec:

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "stringToTest": {
            "type": "string",
            "defaultValue": "Hello"
        },
        "dataFormattedString": {
            "type": "string",
            "defaultValue": "data:;base64,SGVsbG8sIFdvcmxkIQ=="
        }
    },
    "resources": [],
    "outputs": {
        "dataUriOutput": {
            "value": "[dataUri(parameters('stringToTest'))]",
            "type" : "string"
        },
        "toStringOutput": {
            "type": "string",
            "value": "[dataUriToString(parameters('dataFormattedString'))]"
        }
    }
}
```

Výstup z předchozího příkladu s výchozími hodnotami je:

| Name (Název) | Typ | Hodnota |
| ---- | ---- | ----- |
| dataUriOutput | Řetězec | data:text/plain;charset=utf8;base64,SGVsbG8= |
| toStringOutput | Řetězec | Ahoj světe! |

## <a name="empty"></a>empty

`empty(itemToTest)`

Určuje, zda je pole, objekt nebo řetězec prázdný.

### <a name="parameters"></a>Parametry

| Parametr | Požaduje se | Typ | Popis |
|:--- |:--- |:--- |:--- |
| položkaChcete-totest |Ano |pole, objekt nebo řetězec |Hodnota zkontrolovat, zda je prázdný. |

### <a name="return-value"></a>Návratová hodnota

Vrátí **hodnotu True,** pokud je hodnota prázdná. jinak **False**.

### <a name="examples"></a>Příklady

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

## <a name="endswith"></a>endsWith

`endsWith(stringToSearch, stringToFind)`

Určuje, zda řetězec končí hodnotou. Porovnání je malá a velká písmena.

### <a name="parameters"></a>Parametry

| Parametr | Požaduje se | Typ | Popis |
|:--- |:--- |:--- |:--- |
| stringToSearch |Ano |řetězec |Hodnota, která obsahuje položku, která má být vyhledání. |
| stringChcete-li najít |Ano |řetězec |Hodnota najít. |

### <a name="return-value"></a>Návratová hodnota

**True,** pokud poslední znak nebo znaky řetězce odpovídají hodnotě; jinak **False**.

### <a name="examples"></a>Příklady

Následující [příklad šablony](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/startsendswith.json) ukazuje, jak používat startsWith a endsWith funkce:

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "resources": [],
    "outputs": {
        "startsTrue": {
            "value": "[startsWith('abcdef', 'ab')]",
            "type" : "bool"
        },
        "startsCapTrue": {
            "value": "[startsWith('abcdef', 'A')]",
            "type" : "bool"
        },
        "startsFalse": {
            "value": "[startsWith('abcdef', 'e')]",
            "type" : "bool"
        },
        "endsTrue": {
            "value": "[endsWith('abcdef', 'ef')]",
            "type" : "bool"
        },
        "endsCapTrue": {
            "value": "[endsWith('abcdef', 'F')]",
            "type" : "bool"
        },
        "endsFalse": {
            "value": "[endsWith('abcdef', 'e')]",
            "type" : "bool"
        }
    }
}
```

Výstup z předchozího příkladu s výchozími hodnotami je:

| Name (Název) | Typ | Hodnota |
| ---- | ---- | ----- |
| startsTrue | Logická hodnota | True |
| startsCapTrue | Logická hodnota | True |
| startsFalse | Logická hodnota | False |
| endsTrue | Logická hodnota | True |
| endsCapTrue | Logická hodnota | True |
| endsFalse | Logická hodnota | False |

## <a name="first"></a>První

`first(arg1)`

Vrátí první znak řetězce nebo první prvek pole.

### <a name="parameters"></a>Parametry

| Parametr | Požaduje se | Typ | Popis |
|:--- |:--- |:--- |:--- |
| arg1 |Ano |pole nebo řetězec |Hodnota načíst první prvek nebo znak. |

### <a name="return-value"></a>Návratová hodnota

Řetězec prvního znaku nebo typu (řetězec, int, matice nebo objekt) prvního prvku v poli.

### <a name="examples"></a>Příklady

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

## <a name="format"></a>formát

`format(formatString, arg1, arg2, ...)`

Vytvoří formátovaný řetězec ze vstupních hodnot.

### <a name="parameters"></a>Parametry

| Parametr | Požaduje se | Typ | Popis |
|:--- |:--- |:--- |:--- |
| formatString | Ano | řetězec | Složený formátovací řetězec. |
| arg1 | Ano | řetězec, celé číslo nebo logická hodnota | Hodnota, která má být zahrnuta do formátovaného řetězce. |
| další argumenty | Ne | řetězec, celé číslo nebo logická hodnota | Další hodnoty, které mají být zahrnuty do formátovaného řetězce. |

### <a name="remarks"></a>Poznámky

Tato funkce slouží k formátování řetězce v šabloně. Používá stejné možnosti formátování jako metoda [System.String.Format](/dotnet/api/system.string.format) v rozhraní .NET.

### <a name="examples"></a>Příklady

Následující ukázková šablona ukazuje, jak používat funkci formátu.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "greeting": {
            "type": "string",
            "defaultValue": "Hello"
        },
        "name": {
            "type": "string",
            "defaultValue": "User"
        },
        "numberToFormat": {
            "type": "int",
            "defaultValue": 8175133
        }
    },
    "resources": [
    ],
    "outputs": {
        "formatTest": {
            "type": "string",
            "value": "[format('{0}, {1}. Formatted number: {2:N0}', parameters('greeting'), parameters('name'), parameters('numberToFormat'))]"
        }
    }
}
```

Výstup z předchozího příkladu s výchozími hodnotami je:

| Name (Název) | Typ | Hodnota |
| ---- | ---- | ----- |
| formatTest | Řetězec | Dobrý den, uživateli. Formátované číslo: 8 175 133 |

## <a name="guid"></a>Identifikátor guid

`guid(baseString, ...)`

Vytvoří hodnotu ve formátu globálně jedinečného identifikátoru na základě hodnot poskytnutých jako parametry.

### <a name="parameters"></a>Parametry

| Parametr | Požaduje se | Typ | Popis |
|:--- |:--- |:--- |:--- |
| baseString |Ano |řetězec |Hodnota použitá ve funkci hash k vytvoření identifikátoru GUID. |
| další parametry podle potřeby |Ne |řetězec |Můžete přidat tolik řetězců, kolik je potřeba k vytvoření hodnoty, která určuje úroveň jedinečnosti. |

### <a name="remarks"></a>Poznámky

Tato funkce je užitečná, když potřebujete vytvořit hodnotu ve formátu globálně jedinečného identifikátoru. Zadáte hodnoty parametrů, které omezují rozsah jedinečnosti pro výsledek. Můžete určit, zda je název jedinečný až do předplatného, skupiny prostředků nebo nasazení.

Vrácená hodnota není náhodný řetězec, ale spíše výsledek funkce hash na parametry. Vrácená hodnota je dlouhá 36 znaků. Není to celosvětově jedinečné. Chcete-li vytvořit nový identifikátor GUID, který není založen na této hodnotě hash parametrů, použijte funkci [newGuid.](#newguid)

Následující příklady ukazují, jak pomocí identifikátoru GUID vytvořit jedinečnou hodnotu pro běžně používané úrovně.

Jedinečný rozsah předplatného

```json
"[guid(subscription().subscriptionId)]"
```

Jedinečný obor pro skupinu prostředků

```json
"[guid(resourceGroup().id)]"
```

Jedinečný obor pro nasazení pro skupinu prostředků

```json
"[guid(resourceGroup().id, deployment().name)]"
```

### <a name="return-value"></a>Návratová hodnota

Řetězec obsahující 36 znaků ve formátu globálně jedinečný identifikátor.

### <a name="examples"></a>Příklady

Následující [ukázková šablona](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/guid.json) vrátí výsledky z guid:

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {},
    "variables": {},
    "resources": [],
    "outputs": {
        "guidPerSubscription": {
            "value": "[guid(subscription().subscriptionId)]",
            "type": "string"
        },
        "guidPerResourceGroup": {
            "value": "[guid(resourceGroup().id)]",
            "type": "string"
        },
        "guidPerDeployment": {
            "value": "[guid(resourceGroup().id, deployment().name)]",
            "type": "string"
        }
    }
}
```

## <a name="indexof"></a>Indexof

`indexOf(stringToSearch, stringToFind)`

Vrátí první pozici hodnoty v řetězci. Porovnání je malá a velká písmena.

### <a name="parameters"></a>Parametry

| Parametr | Požaduje se | Typ | Popis |
|:--- |:--- |:--- |:--- |
| stringToSearch |Ano |řetězec |Hodnota, která obsahuje položku, která má být vyhledání. |
| stringChcete-li najít |Ano |řetězec |Hodnota najít. |

### <a name="return-value"></a>Návratová hodnota

Celé číslo, které představuje pozici položky, kterou chcete najít. Hodnota je od nuly. Pokud položka není nalezena, -1 je vrácena.

### <a name="examples"></a>Příklady

Následující [ukázka šablony](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/indexof.json) ukazuje, jak používat funkce indexOf a lastIndexOf:

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "resources": [],
    "outputs": {
        "firstT": {
            "value": "[indexOf('test', 't')]",
            "type" : "int"
        },
        "lastT": {
            "value": "[lastIndexOf('test', 't')]",
            "type" : "int"
        },
        "firstString": {
            "value": "[indexOf('abcdef', 'CD')]",
            "type" : "int"
        },
        "lastString": {
            "value": "[lastIndexOf('abcdef', 'AB')]",
            "type" : "int"
        },
        "notFound": {
            "value": "[indexOf('abcdef', 'z')]",
            "type" : "int"
        }
    }
}
```

Výstup z předchozího příkladu s výchozími hodnotami je:

| Name (Název) | Typ | Hodnota |
| ---- | ---- | ----- |
| firstT | Int | 0 |
| lastT | Int | 3 |
| firstString | Int | 2 |
| lastString | Int | 0 |
| notFound | Int | -1 |

## <a name="last"></a>poslední

`last (arg1)`

Vrátí poslední znak řetězce nebo poslední prvek pole.

### <a name="parameters"></a>Parametry

| Parametr | Požaduje se | Typ | Popis |
|:--- |:--- |:--- |:--- |
| arg1 |Ano |pole nebo řetězec |Hodnota pro načtení posledního prvku nebo znaku. |

### <a name="return-value"></a>Návratová hodnota

Řetězec posledního znaku nebo typu (řetězec, int, matice nebo objekt) posledního prvku v poli.

### <a name="examples"></a>Příklady

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

## <a name="lastindexof"></a>Lastindexof

`lastIndexOf(stringToSearch, stringToFind)`

Vrátí poslední pozici hodnoty v řetězci. Porovnání je malá a velká písmena.

### <a name="parameters"></a>Parametry

| Parametr | Požaduje se | Typ | Popis |
|:--- |:--- |:--- |:--- |
| stringToSearch |Ano |řetězec |Hodnota, která obsahuje položku, která má být vyhledání. |
| stringChcete-li najít |Ano |řetězec |Hodnota najít. |

### <a name="return-value"></a>Návratová hodnota

Celé číslo, které představuje poslední pozici položky, kterou chcete najít. Hodnota je od nuly. Pokud položka není nalezena, -1 je vrácena.

### <a name="examples"></a>Příklady

Následující [ukázka šablony](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/indexof.json) ukazuje, jak používat funkce indexOf a lastIndexOf:

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "resources": [],
    "outputs": {
        "firstT": {
            "value": "[indexOf('test', 't')]",
            "type" : "int"
        },
        "lastT": {
            "value": "[lastIndexOf('test', 't')]",
            "type" : "int"
        },
        "firstString": {
            "value": "[indexOf('abcdef', 'CD')]",
            "type" : "int"
        },
        "lastString": {
            "value": "[lastIndexOf('abcdef', 'AB')]",
            "type" : "int"
        },
        "notFound": {
            "value": "[indexOf('abcdef', 'z')]",
            "type" : "int"
        }
    }
}
```

Výstup z předchozího příkladu s výchozími hodnotami je:

| Name (Název) | Typ | Hodnota |
| ---- | ---- | ----- |
| firstT | Int | 0 |
| lastT | Int | 3 |
| firstString | Int | 2 |
| lastString | Int | 0 |
| notFound | Int | -1 |

## <a name="length"></a>length

`length(string)`

Vrátí počet znaků v řetězci, prvky v poli nebo vlastnosti kořenové úrovně v objektu.

### <a name="parameters"></a>Parametry

| Parametr | Požaduje se | Typ | Popis |
|:--- |:--- |:--- |:--- |
| arg1 |Ano |pole, řetězec nebo objekt |Pole, které chcete použít pro získání počtu prvků, řetězec pro získání počtu znaků nebo objekt, který chcete použít pro získání počtu vlastností na úrovni kořenového adresáře. |

### <a name="return-value"></a>Návratová hodnota

Int. 

### <a name="examples"></a>Příklady

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

## <a name="newguid"></a>newGuid

`newGuid()`

Vrátí hodnotu ve formátu globálně jedinečného identifikátoru. **Tuto funkci lze použít pouze ve výchozí hodnotě parametru.**

### <a name="remarks"></a>Poznámky

Tuto funkci lze použít pouze ve výrazu pro výchozí hodnotu parametru. Použití této funkce kdekoli jinde v šabloně vrátí chybu. Funkce není povolena v jiných částech šablony, protože při každém volání vrátí jinou hodnotu. Nasazení stejné šablony se stejnými parametry by spolehlivě nepřineslo stejné výsledky.

Funkce newGuid se liší od funkce [guid,](#guid) protože nepřevezme žádné parametry. Při volání guid se stejným parametrem vrátí stejný identifikátor pokaždé. Guid použijte, když potřebujete spolehlivě generovat stejný identifikátor GUID pro určité prostředí. NewGuid použijte, když potřebujete pokaždé jiný identifikátor, jako je například nasazení prostředků do testovacího prostředí.

Funkce newGuid používá [guid strukturu](/dotnet/api/system.guid) v rozhraní .NET Framework ke generování globálně jedinečný identifikátor.

Pokud použijete [možnost znovu nasadit dřívější úspěšné nasazení](rollback-on-error.md)a dřívější nasazení obsahuje parametr, který používá newGuid, parametr není znovu vyhodnocen. Místo toho hodnota parametru z předchozího nasazení je automaticky znovu použita v nasazení vrácení zpět.

V testovacím prostředí může být nutné opakovaně nasazovat prostředky, které jsou aktivní pouze krátkou dobu. Spíše než vytváření jedinečných názvů, můžete použít newGuid s [uniqueString](#uniquestring) k vytvoření jedinečných názvů.

Buďte opatrní opětovné nasazení šablony, která závisí na funkci newGuid pro výchozí hodnotu. Při opětovném nasazení a neposkytují hodnotu parametru, funkce je přehodnocena. Pokud chcete aktualizovat existující prostředek spíše než vytvořit nový, předat hodnotu parametru z předchozího nasazení.

### <a name="return-value"></a>Návratová hodnota

Řetězec obsahující 36 znaků ve formátu globálně jedinečný identifikátor.

### <a name="examples"></a>Příklady

Následující ukázková šablona zobrazuje parametr s novým identifikátorem.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "guidValue": {
            "type": "string",
            "defaultValue": "[newGuid()]"
        }
    },
    "resources": [
    ],
    "outputs": {
        "guidOutput": {
            "type": "string",
            "value": "[parameters('guidValue')]"
        }
    }
}
```

Výstup z předchozího příkladu se liší pro každé nasazení, ale bude podobný:

| Name (Název) | Typ | Hodnota |
| ---- | ---- | ----- |
| výstup guidOutput | řetězec | b76a51fc-bd72-4a77-b9a2-3c29e7d2e551 |

Následující příklad používá funkci newGuid k vytvoření jedinečného názvu pro účet úložiště. Tato šablona může fungovat pro testovací prostředí, kde účet úložiště existuje krátkou dobu a není znovu nasazen.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "guidValue": {
            "type": "string",
            "defaultValue": "[newGuid()]"
        }
    },
    "variables": {
        "storageName": "[concat('storage', uniqueString(parameters('guidValue')))]"
    },
    "resources": [
        {
            "type": "Microsoft.Storage/storageAccounts",
            "name": "[variables('storageName')]",
            "location": "West US",
            "apiVersion": "2018-07-01",
            "sku":{
                "name": "Standard_LRS"
            },
            "kind": "StorageV2",
            "properties": {}
        }
    ],
    "outputs": {
        "nameOutput": {
            "type": "string",
            "value": "[variables('storageName')]"
        }
    }
}
```

Výstup z předchozího příkladu se liší pro každé nasazení, ale bude podobný:

| Name (Název) | Typ | Hodnota |
| ---- | ---- | ----- |
| názevVýstup | řetězec | storagenziwvyru7uxie |


## <a name="padleft"></a>oložek vlevo

`padLeft(valueToPad, totalLength, paddingCharacter)`

Vrátí řetězec zarovnaný doprava přidáním znaků doleva, dokud nedosáhne celkové zadané délky.

### <a name="parameters"></a>Parametry

| Parametr | Požaduje se | Typ | Popis |
|:--- |:--- |:--- |:--- |
| valueToPad |Ano |řetězec nebo int |Hodnota zarovnat doprava. |
| totalDélka |Ano |int |Celkový počet znaků ve vráceném řetězci. |
| odsazeníZnak |Ne |jeden znak |Znak, který se má použít pro levé odsazení, dokud není dosaženo celkové délky. Výchozí hodnota je mezera. |

Pokud je původní řetězec delší než počet znaků, které mají být pad, jsou přidány žádné znaky.

### <a name="return-value"></a>Návratová hodnota

Řetězec s alespoň počtem zadaných znaků.

### <a name="examples"></a>Příklady

Následující [ukázková šablona](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/padleft.json) ukazuje, jak vyložce uživatelem zadané hodnoty parametru přidáním nulového znaku, dokud nedosáhne celkového počtu znaků. 

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "testString": {
            "type": "string",
            "defaultValue": "123"
        }
    },
    "resources": [],
    "outputs": {
        "stringOutput": {
            "type": "string",
            "value": "[padLeft(parameters('testString'),10,'0')]"
        }
    }
}
```

Výstup z předchozího příkladu s výchozími hodnotami je:

| Name (Název) | Typ | Hodnota |
| ---- | ---- | ----- |
| stringOutput | Řetězec | 0000000123 |

## <a name="replace"></a>Nahradit

`replace(originalString, oldString, newString)`

Vrátí nový řetězec se všemi instancemi jednoho řetězce nahrazenjiným řetězcem.

### <a name="parameters"></a>Parametry

| Parametr | Požaduje se | Typ | Popis |
|:--- |:--- |:--- |:--- |
| Originalstring |Ano |řetězec |Hodnota, která má všechny instance jednoho řetězce nahrazena jiným řetězcem. |
| starý řetězec |Ano |řetězec |Řetězec, který má být odebrán z původního řetězce. |
| newString |Ano |řetězec |Řetězec přidat místo odebraného řetězce. |

### <a name="return-value"></a>Návratová hodnota

Řetězec s nahrazenými znaky.

### <a name="examples"></a>Příklady

Následující [ukázková šablona](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/replace.json) ukazuje, jak odebrat všechny pomlčky z řetězce poskytnutého uživatelem a jak nahradit část řetězce jiným řetězcem.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "testString": {
            "type": "string",
            "defaultValue": "123-123-1234"
        }
    },
    "resources": [],
    "outputs": {
        "firstOutput": {
            "type": "string",
            "value": "[replace(parameters('testString'),'-', '')]"
        },
        "secondOutput": {
            "type": "string",
            "value": "[replace(parameters('testString'),'1234', 'xxxx')]"
        }
    }
}
```

Výstup z předchozího příkladu s výchozími hodnotami je:

| Name (Název) | Typ | Hodnota |
| ---- | ---- | ----- |
| firstOutput | Řetězec | 1231231234 |
| secondOutput | Řetězec | 123-123-xxxx |

## <a name="skip"></a>Přeskočit

`skip(originalValue, numberToSkip)`

Vrátí řetězec se všemi znaky za zadaný počet znaků nebo pole se všemi prvky za zadaný počet prvků.

### <a name="parameters"></a>Parametry

| Parametr | Požaduje se | Typ | Popis |
|:--- |:--- |:--- |:--- |
| originalValue |Ano |pole nebo řetězec |Pole nebo řetězec, který se má použít pro přeskočení. |
| čísloToPřeskočit |Ano |int |Počet prvků nebo znaků přeskočit. Pokud je tato hodnota 0 nebo méně, jsou vráceny všechny prvky nebo znaky v hodnotě. Pokud je větší než délka pole nebo řetězce, je vráceno prázdné pole nebo řetězec. |

### <a name="return-value"></a>Návratová hodnota

Pole nebo řetězec.

### <a name="examples"></a>Příklady

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

## <a name="split"></a>split

`split(inputString, delimiter)`

Vrátí pole řetězců, které obsahuje podřetězce vstupního řetězce, které jsou odděleny zadanými oddělovači.

### <a name="parameters"></a>Parametry

| Parametr | Požaduje se | Typ | Popis |
|:--- |:--- |:--- |:--- |
| inputString |Ano |řetězec |Řetězec rozdělit. |
| delimiter |Ano |řetězec nebo pole řetězců |Oddělovač použít pro rozdělení řetězce. |

### <a name="return-value"></a>Návratová hodnota

Pole řetězců.

### <a name="examples"></a>Příklady

Následující [ukázková šablona](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/split.json) rozdělí vstupní řetězec čárkou a čárkou nebo středníkem.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "firstString": {
            "type": "string",
            "defaultValue": "one,two,three"
        },
        "secondString": {
            "type": "string",
            "defaultValue": "one;two,three"
        }
    },
    "variables": {
        "delimiters": [ ",", ";" ]
    },
    "resources": [],
    "outputs": {
        "firstOutput": {
            "type": "array",
            "value": "[split(parameters('firstString'),',')]"
        },
        "secondOutput": {
            "type": "array",
            "value": "[split(parameters('secondString'),variables('delimiters'))]"
        }
    }
}
```

Výstup z předchozího příkladu s výchozími hodnotami je:

| Name (Název) | Typ | Hodnota |
| ---- | ---- | ----- |
| firstOutput | Pole | ["jedna", "dva", "tři"] |
| secondOutput | Pole | ["jedna", "dva", "tři"] |

## <a name="startswith"></a>startsWith

`startsWith(stringToSearch, stringToFind)`

Určuje, zda řetězec začíná hodnotou. Porovnání je malá a velká písmena.

### <a name="parameters"></a>Parametry

| Parametr | Požaduje se | Typ | Popis |
|:--- |:--- |:--- |:--- |
| stringToSearch |Ano |řetězec |Hodnota, která obsahuje položku, která má být vyhledání. |
| stringChcete-li najít |Ano |řetězec |Hodnota najít. |

### <a name="return-value"></a>Návratová hodnota

**True,** pokud první znak nebo znaky řetězce odpovídají hodnotě; jinak **False**.

### <a name="examples"></a>Příklady

Následující [příklad šablony](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/startsendswith.json) ukazuje, jak používat startsWith a endsWith funkce:

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "resources": [],
    "outputs": {
        "startsTrue": {
            "value": "[startsWith('abcdef', 'ab')]",
            "type" : "bool"
        },
        "startsCapTrue": {
            "value": "[startsWith('abcdef', 'A')]",
            "type" : "bool"
        },
        "startsFalse": {
            "value": "[startsWith('abcdef', 'e')]",
            "type" : "bool"
        },
        "endsTrue": {
            "value": "[endsWith('abcdef', 'ef')]",
            "type" : "bool"
        },
        "endsCapTrue": {
            "value": "[endsWith('abcdef', 'F')]",
            "type" : "bool"
        },
        "endsFalse": {
            "value": "[endsWith('abcdef', 'e')]",
            "type" : "bool"
        }
    }
}
```

Výstup z předchozího příkladu s výchozími hodnotami je:

| Name (Název) | Typ | Hodnota |
| ---- | ---- | ----- |
| startsTrue | Logická hodnota | True |
| startsCapTrue | Logická hodnota | True |
| startsFalse | Logická hodnota | False |
| endsTrue | Logická hodnota | True |
| endsCapTrue | Logická hodnota | True |
| endsFalse | Logická hodnota | False |

## <a name="string"></a>řetězec

`string(valueToConvert)`

Převede zadanou hodnotu na řetězec.

### <a name="parameters"></a>Parametry

| Parametr | Požaduje se | Typ | Popis |
|:--- |:--- |:--- |:--- |
| valueToConvert |Ano | Všechny |Hodnota převést na řetězec. Lze převést libovolný typ hodnoty, včetně objektů a polí. |

### <a name="return-value"></a>Návratová hodnota

Řetězec převedené hodnoty.

### <a name="examples"></a>Příklady

Následující [ukázková šablona](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/string.json) ukazuje, jak převést různé typy hodnot na řetězce:

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "testObject": {
            "type": "object",
            "defaultValue": {
                "valueA": 10,
                "valueB": "Example Text"
            }
        },
        "testArray": {
            "type": "array",
            "defaultValue": [
                "a",
                "b",
                "c"
            ]
        },
        "testInt": {
            "type": "int",
            "defaultValue": 5
        }
    },
    "resources": [],
    "outputs": {
        "objectOutput": {
            "type": "string",
            "value": "[string(parameters('testObject'))]"
        },
        "arrayOutput": {
            "type": "string",
            "value": "[string(parameters('testArray'))]"
        },
        "intOutput": {
            "type": "string",
            "value": "[string(parameters('testInt'))]"
        }
    }
}
```

Výstup z předchozího příkladu s výchozími hodnotami je:

| Name (Název) | Typ | Hodnota |
| ---- | ---- | ----- |
| objectOutput | Řetězec | {"valueA":10,"valueB":"Příklad textu"} |
| poleOutput | Řetězec | ["a","b","c"] |
| intOutput | Řetězec | 5 |

## <a name="substring"></a>Podřetězec

`substring(stringToParse, startIndex, length)`

Vrátí podřetězec, který začíná na zadané pozici znaku a obsahuje zadaný počet znaků.

### <a name="parameters"></a>Parametry

| Parametr | Požaduje se | Typ | Popis |
|:--- |:--- |:--- |:--- |
| stringToParse |Ano |řetězec |Původní řetězec, ze kterého je extrahován podřetězec. |
| Startindex |Ne |int |Pozice počátečního znaku na základě nuly pro podřetězec. |
| length |Ne |int |Počet znaků pro podřetězec. Musí odkazovat na umístění v řetězci. Musí být nula nebo větší. |

### <a name="return-value"></a>Návratová hodnota

Podřetězec. Nebo prázdný řetězec, pokud je délka nula.

### <a name="remarks"></a>Poznámky

Funkce se nezdaří, pokud podřetězec přesahuje konec řetězce nebo pokud je délka menší než nula. Následující příklad se nezdaří s chybou "Index a délka parametry musí odkazovat na umístění v řetězci. Parametr indexu: '0', parametr length: '11', délka parametru řetězce: '10'.".

```json
"parameters": {
    "inputString": { "type": "string", "value": "1234567890" }
},
"variables": { 
    "prefix": "[substring(parameters('inputString'), 0, 11)]"
}
```

### <a name="examples"></a>Příklady

Následující [příklad šablony](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/substring.json) extrahuje podřetězec z parametru.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "testString": {
            "type": "string",
            "defaultValue": "one two three"
        }
    },
    "resources": [],
    "outputs": {
        "substringOutput": {
            "value": "[substring(parameters('testString'), 4, 3)]",
            "type": "string"
        }
    }
}
```

Výstup z předchozího příkladu s výchozími hodnotami je:

| Name (Název) | Typ | Hodnota |
| ---- | ---- | ----- |
| substringOutput | Řetězec | dva |

## <a name="take"></a>vzít

`take(originalValue, numberToTake)`

Vrátí řetězec se zadaným počtem znaků od začátku řetězce nebo pole se zadaným počtem prvků od začátku pole.

### <a name="parameters"></a>Parametry

| Parametr | Požaduje se | Typ | Popis |
|:--- |:--- |:--- |:--- |
| originalValue |Ano |pole nebo řetězec |Pole nebo řetězec, ze kterých mají být prvky odebrány. |
| numberToTake |Ano |int |Počet prvků nebo znaků, které chcete vzít. Pokud je tato hodnota 0 nebo méně, je vráceno prázdné pole nebo řetězec. Pokud je větší než délka daného pole nebo řetězce, jsou vráceny všechny prvky v poli nebo řetězci. |

### <a name="return-value"></a>Návratová hodnota

Pole nebo řetězec.

### <a name="examples"></a>Příklady

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

## <a name="tolower"></a>Tolower

`toLower(stringToChange)`

Převede zadaný řetězec na malá písmena.

### <a name="parameters"></a>Parametry

| Parametr | Požaduje se | Typ | Popis |
|:--- |:--- |:--- |:--- |
| stringChcete-li změnit |Ano |řetězec |Hodnota převést na malá písmena. |

### <a name="return-value"></a>Návratová hodnota

Řetězec převeden na malá písmena.

### <a name="examples"></a>Příklady

Následující [ukázková šablona](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/tolower.json) převede hodnotu parametru na malá písmena a velká písmena.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "testString": {
            "type": "string",
            "defaultValue": "One Two Three"
        }
    },
    "resources": [],
    "outputs": {
        "toLowerOutput": {
            "value": "[toLower(parameters('testString'))]",
            "type": "string"
        },
        "toUpperOutput": {
            "type": "string",
            "value": "[toUpper(parameters('testString'))]"
        }
    }
}
```

Výstup z předchozího příkladu s výchozími hodnotami je:

| Name (Název) | Typ | Hodnota |
| ---- | ---- | ----- |
| toLowerOutput | Řetězec | Jedna dva tři |
| toUpperOutput | Řetězec | Jedna dva tři |

## <a name="toupper"></a>Toupper

`toUpper(stringToChange)`

Převede zadaný řetězec na velká písmena.

### <a name="parameters"></a>Parametry

| Parametr | Požaduje se | Typ | Popis |
|:--- |:--- |:--- |:--- |
| stringChcete-li změnit |Ano |řetězec |Hodnota převést na velká písmena. |

### <a name="return-value"></a>Návratová hodnota

Řetězec převeden na velká písmena.

### <a name="examples"></a>Příklady

Následující [ukázková šablona](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/tolower.json) převede hodnotu parametru na malá písmena a velká písmena.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "testString": {
            "type": "string",
            "defaultValue": "One Two Three"
        }
    },
    "resources": [],
    "outputs": {
        "toLowerOutput": {
            "value": "[toLower(parameters('testString'))]",
            "type": "string"
        },
        "toUpperOutput": {
            "type": "string",
            "value": "[toUpper(parameters('testString'))]"
        }
    }
}
```

Výstup z předchozího příkladu s výchozími hodnotami je:

| Name (Název) | Typ | Hodnota |
| ---- | ---- | ----- |
| toLowerOutput | Řetězec | Jedna dva tři |
| toUpperOutput | Řetězec | Jedna dva tři |

## <a name="trim"></a>Trim

`trim (stringToTrim)`

Odebere ze zadaného řetězce všechny úvodní a koncové prázdné znaky.

### <a name="parameters"></a>Parametry

| Parametr | Požaduje se | Typ | Popis |
|:--- |:--- |:--- |:--- |
| stringToTrim |Ano |řetězec |Hodnota, která má být oříznuta. |

### <a name="return-value"></a>Návratová hodnota

Řetězec bez úvodních a koncových znaků mezer.

### <a name="examples"></a>Příklady

Následující [ukázková šablona](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/trim.json) ořízne prázdné znaky z parametru.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "testString": {
            "type": "string",
            "defaultValue": "    one two three   "
        }
    },
    "resources": [],
    "outputs": {
        "return": {
            "type": "string",
            "value": "[trim(parameters('testString'))]"
        }
    }
}
```

Výstup z předchozího příkladu s výchozími hodnotami je:

| Name (Název) | Typ | Hodnota |
| ---- | ---- | ----- |
| return | Řetězec | Jedna dva tři |

## <a name="uniquestring"></a>uniqueString

`uniqueString (baseString, ...)`

Vytvoří deterministický řetězec hash na základě hodnot poskytnutých jako parametry. 

### <a name="parameters"></a>Parametry

| Parametr | Požaduje se | Typ | Popis |
|:--- |:--- |:--- |:--- |
| baseString |Ano |řetězec |Hodnota použitá ve funkci hash k vytvoření jedinečného řetězce. |
| další parametry podle potřeby |Ne |řetězec |Můžete přidat tolik řetězců, kolik je potřeba k vytvoření hodnoty, která určuje úroveň jedinečnosti. |

### <a name="remarks"></a>Poznámky

Tato funkce je užitečná, když potřebujete vytvořit jedinečný název prostředku. Zadáte hodnoty parametrů, které omezují rozsah jedinečnosti pro výsledek. Můžete určit, zda je název jedinečný až do předplatného, skupiny prostředků nebo nasazení. 

Vrácená hodnota není náhodný řetězec, ale spíše výsledek funkce hash. Vrácená hodnota je dlouhá 13 znaků. Není to celosvětově jedinečné. Můžete chtít kombinovat hodnotu s předponou z konvence pojmenování vytvořit název, který je smysluplný. Následující příklad ukazuje formát vrácené hodnoty. Skutečná hodnota se liší podle zadaných parametrů.

    tcvhiyu5h2o5o

Následující příklady ukazují, jak pomocí uniqueString vytvořit jedinečnou hodnotu pro běžně používané úrovně.

Jedinečný rozsah předplatného

```json
"[uniqueString(subscription().subscriptionId)]"
```

Jedinečný obor pro skupinu prostředků

```json
"[uniqueString(resourceGroup().id)]"
```

Jedinečný obor pro nasazení pro skupinu prostředků

```json
"[uniqueString(resourceGroup().id, deployment().name)]"
```

Následující příklad ukazuje, jak vytvořit jedinečný název pro účet úložiště na základě vaší skupiny prostředků. Uvnitř skupiny prostředků název není jedinečný, pokud je vytvořen stejným způsobem.

```json
"resources": [{ 
    "name": "[concat('storage', uniqueString(resourceGroup().id))]", 
    "type": "Microsoft.Storage/storageAccounts", 
    ...
```

Pokud potřebujete vytvořit nový jedinečný název při každém nasazení šablony a nemáte v úmyslu aktualizovat prostředek, můžete použít funkci [utcNow](template-functions-date.md#utcnow) s uniqueString. Tento přístup můžete použít v testovacím prostředí. Příklad naleznete [v tématu utcNow](template-functions-date.md#utcnow).

### <a name="return-value"></a>Návratová hodnota

Řetězec obsahující 13 znaků.

### <a name="examples"></a>Příklady

Následující [ukázková šablona](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/uniquestring.json) vrátí výsledky z řetězce uniquestring:

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "resources": [],
    "outputs": {
        "uniqueRG": {
            "value": "[uniqueString(resourceGroup().id)]",
            "type" : "string"
        },
        "uniqueDeploy": {
            "value": "[uniqueString(resourceGroup().id, deployment().name)]",
            "type" : "string"
        }
    }
}
```

## <a name="uri"></a>Uri

`uri (baseUri, relativeUri)`

Vytvoří absolutní IDENTIFIKÁTOR URI kombinací baseUri a relativeUri řetězec.

### <a name="parameters"></a>Parametry

| Parametr | Požaduje se | Typ | Popis |
|:--- |:--- |:--- |:--- |
| Baseuri |Ano |řetězec |Základní řetězec uri. Dbejte na to, abyste sledovali chování týkající se zpracování koncového lomítka ('/'), jak je popsáno v této tabulce.  |
| Relativeuri |Ano |řetězec |Relativní uri řetězec přidat do základního řetězce uri. |

* Pokud **baseUri** končí v koncové lomítko, výsledek je jednoduše **baseUri** následuje **relativeUri**.

* Pokud **baseUri** nekončí v koncové lomítko jedna ze dvou věcí se stane.  

   * Pokud **baseUri** nemá žádné lomítka vůbec (kromě "//" v blízkosti přední) výsledek je jednoduše **baseUri** následuje **relativeUri**.

   * Pokud **baseUri** má některé lomítka, ale nekončí lomítko, vše od poslednílomítko dále je odebránz **baseUri** a výsledkem je **baseUri** následuje **relativeUri**.
     
Zde je několik příkladů:

```
uri('http://contoso.org/firstpath', 'myscript.sh') -> http://contoso.org/myscript.sh
uri('http://contoso.org/firstpath/', 'myscript.sh') -> http://contoso.org/firstpath/myscript.sh
uri('http://contoso.org/firstpath/azuredeploy.json', 'myscript.sh') -> http://contoso.org/firstpath/myscript.sh
uri('http://contoso.org/firstpath/azuredeploy.json/', 'myscript.sh') -> http://contoso.org/firstpath/azuredeploy.json/myscript.sh
```
Úplné podrobnosti **baseUri** a **relativeUri** parametry jsou vyřešeny podle specifikace V [RFC 3986, oddíl 5](https://tools.ietf.org/html/rfc3986#section-5).

### <a name="return-value"></a>Návratová hodnota

Řetězec představující absolutní identifikátor URI pro základní a relativní hodnoty.

### <a name="examples"></a>Příklady

Následující příklad ukazuje, jak vytvořit odkaz na vnořenou šablonu na základě hodnoty nadřazené šablony.

```json
"templateLink": "[uri(deployment().properties.templateLink.uri, 'nested/azuredeploy.json')]"
```

Následující [ukázková šablona](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/uri.json) ukazuje, jak používat uri, uriComponent a uriComponentToString:

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "variables": {
        "uriFormat": "[uri('http://contoso.com/resources/', 'nested/azuredeploy.json')]",
        "uriEncoded": "[uriComponent(variables('uriFormat'))]" 
    },
    "resources": [
    ],
    "outputs": {
        "uriOutput": {
            "type": "string",
            "value": "[variables('uriFormat')]"
        },
        "componentOutput": {
            "type": "string",
            "value": "[variables('uriEncoded')]"
        },
        "toStringOutput": {
            "type": "string",
            "value": "[uriComponentToString(variables('uriEncoded'))]"
        }
    }
}
```

Výstup z předchozího příkladu s výchozími hodnotami je:

| Name (Název) | Typ | Hodnota |
| ---- | ---- | ----- |
| uriOutput | Řetězec | `http://contoso.com/resources/nested/azuredeploy.json` |
| componentOutput | Řetězec | `http%3A%2F%2Fcontoso.com%2Fresources%2Fnested%2Fazuredeploy.json` |
| toStringOutput | Řetězec | `http://contoso.com/resources/nested/azuredeploy.json` |

## <a name="uricomponent"></a>uriComponent

`uricomponent(stringToEncode)`

Zakóduje identifikátor URI.

### <a name="parameters"></a>Parametry

| Parametr | Požaduje se | Typ | Popis |
|:--- |:--- |:--- |:--- |
| stringToEncode |Ano |řetězec |Hodnota kódování. |

### <a name="return-value"></a>Návratová hodnota

Řetězec hodnoty kódované identifikátorem URI.

### <a name="examples"></a>Příklady

Následující [ukázková šablona](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/uri.json) ukazuje, jak používat uri, uriComponent a uriComponentToString:

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "variables": {
        "uriFormat": "[uri('http://contoso.com/resources/', 'nested/azuredeploy.json')]",
        "uriEncoded": "[uriComponent(variables('uriFormat'))]" 
    },
    "resources": [
    ],
    "outputs": {
        "uriOutput": {
            "type": "string",
            "value": "[variables('uriFormat')]"
        },
        "componentOutput": {
            "type": "string",
            "value": "[variables('uriEncoded')]"
        },
        "toStringOutput": {
            "type": "string",
            "value": "[uriComponentToString(variables('uriEncoded'))]"
        }
    }
}
```

Výstup z předchozího příkladu s výchozími hodnotami je:

| Name (Název) | Typ | Hodnota |
| ---- | ---- | ----- |
| uriOutput | Řetězec | `http://contoso.com/resources/nested/azuredeploy.json` |
| componentOutput | Řetězec | `http%3A%2F%2Fcontoso.com%2Fresources%2Fnested%2Fazuredeploy.json` |
| toStringOutput | Řetězec | `http://contoso.com/resources/nested/azuredeploy.json` |

## <a name="uricomponenttostring"></a>uriComponentToString

`uriComponentToString(uriEncodedString)`

Vrátí řetězec hodnoty kódované identifikátorem URI.

### <a name="parameters"></a>Parametry

| Parametr | Požaduje se | Typ | Popis |
|:--- |:--- |:--- |:--- |
| uriEncodedString |Ano |řetězec |Hodnota kódovaná identifikátorem URI, který chcete převést na řetězec. |

### <a name="return-value"></a>Návratová hodnota

Dekódovaný řetězec kódované hodnoty URI.

### <a name="examples"></a>Příklady

Následující [ukázková šablona](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/uri.json) ukazuje, jak používat uri, uriComponent a uriComponentToString:

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "variables": {
        "uriFormat": "[uri('http://contoso.com/resources/', 'nested/azuredeploy.json')]",
        "uriEncoded": "[uriComponent(variables('uriFormat'))]" 
    },
    "resources": [
    ],
    "outputs": {
        "uriOutput": {
            "type": "string",
            "value": "[variables('uriFormat')]"
        },
        "componentOutput": {
            "type": "string",
            "value": "[variables('uriEncoded')]"
        },
        "toStringOutput": {
            "type": "string",
            "value": "[uriComponentToString(variables('uriEncoded'))]"
        }
    }
}
```

Výstup z předchozího příkladu s výchozími hodnotami je:

| Name (Název) | Typ | Hodnota |
| ---- | ---- | ----- |
| uriOutput | Řetězec | `http://contoso.com/resources/nested/azuredeploy.json` |
| componentOutput | Řetězec | `http%3A%2F%2Fcontoso.com%2Fresources%2Fnested%2Fazuredeploy.json` |
| toStringOutput | Řetězec | `http://contoso.com/resources/nested/azuredeploy.json` |

## <a name="next-steps"></a>Další kroky
* Popis oddílů v šabloně Azure Resource Manager u najdete v tématu [Vytváření šablon Azure Resource Manageru](template-syntax.md).
* Pokud chcete sloučit víc šablon, přečtěte si informace [o použití propojených šablon ve Správci prostředků Azure](linked-templates.md).
* Chcete-li itrerate zadaný počet časů při vytváření typu prostředku, najdete v [tématu vytvoření více instancí prostředků ve Správci prostředků Azure](copy-resources.md).
* Informace o tom, jak nasadit vytvořenou šablonu, najdete [v tématu Nasazení aplikace pomocí šablony Azure Resource Manageru](deploy-powershell.md).

