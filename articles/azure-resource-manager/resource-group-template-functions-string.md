---
title: Funkce šablon Azure Resource Manager – String | Microsoft Docs
description: Popisuje funkce, které se použijí v šabloně Azure Resource Manager pro práci s řetězci.
author: tfitzmac
ms.service: azure-resource-manager
ms.topic: conceptual
ms.date: 07/31/2019
ms.author: tomfitz
ms.openlocfilehash: 93f17ea9d2ffa33d1dca9da3eb60f75165e8ed61
ms.sourcegitcommit: c2e7595a2966e84dc10afb9a22b74400c4b500ed
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/05/2019
ms.locfileid: "71973339"
---
# <a name="string-functions-for-azure-resource-manager-templates"></a>Řetězcové funkce pro šablony Azure Resource Manager

Správce prostředků poskytuje následující funkce pro práci s řetězci:

* [Base](#base64)
* [base64ToJson](#base64tojson)
* [base64ToString](#base64tostring)
* [spojuje](#concat)
* [zobrazí](#contains)
* [dataUri](#datauri)
* [dataUriToString](#datauritostring)
* [obsahovat](#empty)
* [endsWith](#endswith)
* [první](#first)
* [formátovat](#format)
* [hlavních](#guid)
* [indexOf](#indexof)
* [posledního](#last)
* [lastIndexOf](#lastindexof)
* [časový](#length)
* [newGuid](#newguid)
* [padLeft](#padleft)
* [náhrady](#replace)
* [přímo](#skip)
* [split](#split)
* [startsWith](#startswith)
* [řetezce](#string)
* [podřetězec](#substring)
* [nezbytná](#take)
* [toLower](#tolower)
* [toUpper](#toupper)
* [sklon](#trim)
* [uniqueString](#uniquestring)
* [identifikátor URI](#uri)
* [uriComponent](#uricomponent)
* [uriComponentToString](#uricomponenttostring)
* [utcNow](#utcnow)

## <a name="base64"></a>Base

`base64(inputString)`

Vrátí reprezentaci se vstupním řetězcem ve formátu base64.

### <a name="parameters"></a>Parametry

| Parametr | Požaduje se | Typ | Popis |
|:--- |:--- |:--- |:--- |
| inputString |Ano |string |Hodnota, která se má vrátit jako reprezentace v kódování Base64. |

### <a name="return-value"></a>Návratová hodnota

Řetězec obsahující reprezentaci base64.

### <a name="examples"></a>Příklady

Následující [příklad šablony](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/base64.json) ukazuje, jak používat funkci base64.

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
| base64Output | Řetězec | b25lLCB0d28sIHRocmVl |
| toStringOutput | Řetězec | Jedna dva tři |
| toJsonOutput | Objekt | {"One": "a", "Two": "b"} |

## <a name="base64tojson"></a>base64ToJson

`base64tojson`

Převede reprezentaci Base64 na objekt JSON.

### <a name="parameters"></a>Parametry

| Parametr | Požaduje se | Typ | Popis |
|:--- |:--- |:--- |:--- |
| base64Value |Ano |string |Reprezentace Base64 pro převod na objekt JSON. |

### <a name="return-value"></a>Návratová hodnota

Objekt JSON.

### <a name="examples"></a>Příklady

Následující [příklad šablony](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/base64.json) používá funkci base64ToJson k převodu hodnoty Base64:

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
| base64Output | Řetězec | b25lLCB0d28sIHRocmVl |
| toStringOutput | Řetězec | Jedna dva tři |
| toJsonOutput | Objekt | {"One": "a", "Two": "b"} |

## <a name="base64tostring"></a>base64ToString

`base64ToString(base64Value)`

Převede reprezentaci Base64 na řetězec.

### <a name="parameters"></a>Parametry

| Parametr | Požaduje se | Typ | Popis |
|:--- |:--- |:--- |:--- |
| base64Value |Ano |string |Reprezentace Base64 pro převod na řetězec. |

### <a name="return-value"></a>Návratová hodnota

Řetězec převedené hodnoty base64.

### <a name="examples"></a>Příklady

Následující [příklad šablony](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/base64.json) používá funkci base64ToString k převodu hodnoty Base64:

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
| base64Output | Řetězec | b25lLCB0d28sIHRocmVl |
| toStringOutput | Řetězec | Jedna dva tři |
| toJsonOutput | Objekt | {"One": "a", "Two": "b"} |

## <a name="concat"></a>spojuje

`concat (arg1, arg2, arg3, ...)`

Kombinuje více řetězcových hodnot a vrátí zřetězený řetězec nebo zkombinuje více polí a vrátí zřetězené pole.

### <a name="parameters"></a>Parametry

| Parametr | Požaduje se | Typ | Popis |
|:--- |:--- |:--- |:--- |
| arg1 |Ano |řetězec nebo pole |První hodnota pro zřetězení |
| Další argumenty |Ne |string |Další hodnoty v sekvenčním pořadí pro zřetězení. |

### <a name="return-value"></a>Návratová hodnota
Řetězec nebo pole zřetězených hodnot.

### <a name="examples"></a>Příklady

Následující [příklad šablony](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/concat-string.json) ukazuje, jak kombinovat dvě řetězcové hodnoty a vracet zřetězený řetězec.

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
| concatOutput | Řetězec | prefix – 5yj4yjf5mbg72 |

Následující [příklad šablony](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/concat-array.json) ukazuje, jak kombinovat dvě pole.

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
| vrátit | Pole | ["1-1", "1-2", "1-3", "2-1", "2-2", "2-3"] |

## <a name="contains"></a>zobrazí

`contains (container, itemToFind)`

Kontroluje, zda pole obsahuje hodnotu, objekt obsahuje klíč, nebo řetězec obsahuje podřetězec. Porovnávání řetězců rozlišuje velká a malá písmena. Při testování, zda objekt obsahuje klíč, však porovnání nerozlišuje malá a velká písmena.

### <a name="parameters"></a>Parametry

| Parametr | Požaduje se | Typ | Popis |
|:--- |:--- |:--- |:--- |
| kontejner |Ano |pole, objekt nebo řetězec |Hodnota, která obsahuje hodnotu, která se má najít. |
| itemToFind |Ano |řetězec nebo int |Hodnota, která se má najít |

### <a name="return-value"></a>Návratová hodnota

**True** , pokud je položka nalezena; v opačném případě **false**.

### <a name="examples"></a>Příklady

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

| Name (Název) | Typ | Hodnota |
| ---- | ---- | ----- |
| stringTrue | Logick | True |
| stringFalse | Logick | False |
| objectTrue | Logick | True |
| objectFalse | Logick | False |
| arrayTrue | Logick | True |
| arrayFalse | Logick | False |

## <a name="datauri"></a>dataUri

`dataUri(stringToConvert)`

Převede hodnotu na identifikátor URI dat.

### <a name="parameters"></a>Parametry

| Parametr | Požaduje se | Typ | Popis |
|:--- |:--- |:--- |:--- |
| stringToConvert |Ano |string |Hodnota, která má být převedena na identifikátor URI dat. |

### <a name="return-value"></a>Návratová hodnota

Řetězec formátovaný jako identifikátor URI dat.

### <a name="examples"></a>Příklady

Následující [příklad šablony](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/datauri.json) převede hodnotu na identifikátor URI dat a převede identifikátor URI dat na řetězec:

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
| dataUriOutput | Řetězec | data: text/prostý; charset = UTF8; Base64, SGVsbG8 = |
| toStringOutput | Řetězec | Ahoj světe! |

## <a name="datauritostring"></a>dataUriToString

`dataUriToString(dataUriToConvert)`

Převede hodnotu v identifikátoru URI dat na řetězec.

### <a name="parameters"></a>Parametry

| Parametr | Požaduje se | Typ | Popis |
|:--- |:--- |:--- |:--- |
| dataUriToConvert |Ano |string |Hodnota identifikátoru URI dat, která se má převést. |

### <a name="return-value"></a>Návratová hodnota

Řetězec obsahující převedenou hodnotu.

### <a name="examples"></a>Příklady

Následující [příklad šablony](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/datauri.json) převede hodnotu na identifikátor URI dat a převede identifikátor URI dat na řetězec:

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
| dataUriOutput | Řetězec | data: text/prostý; charset = UTF8; Base64, SGVsbG8 = |
| toStringOutput | Řetězec | Ahoj světe! |

## <a name="empty"></a>obsahovat

`empty(itemToTest)`

Určuje, zda je pole, objekt nebo řetězec prázdný.

### <a name="parameters"></a>Parametry

| Parametr | Požaduje se | Typ | Popis |
|:--- |:--- |:--- |:--- |
| itemToTest |Ano |pole, objekt nebo řetězec |Hodnota, která zkontroluje, jestli je prázdná |

### <a name="return-value"></a>Návratová hodnota

Vrátí **hodnotu true** , pokud je hodnota prázdná. v opačném případě **false**.

### <a name="examples"></a>Příklady

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

| Name (Název) | Typ | Hodnota |
| ---- | ---- | ----- |
| arrayEmpty | Logick | True |
| objectEmpty | Logick | True |
| stringEmpty | Logick | True |

## <a name="endswith"></a>endsWith

`endsWith(stringToSearch, stringToFind)`

Určuje, zda řetězec končí hodnotou. V porovnání se nerozlišují malá a velká písmena.

### <a name="parameters"></a>Parametry

| Parametr | Požaduje se | Typ | Popis |
|:--- |:--- |:--- |:--- |
| stringToSearch |Ano |string |Hodnota, která obsahuje položku, kterou chcete najít. |
| stringToFind |Ano |string |Hodnota, která se má najít |

### <a name="return-value"></a>Návratová hodnota

**True** , pokud se poslední znak nebo znaky řetězce shodují s hodnotou; v opačném případě **false**.

### <a name="examples"></a>Příklady

Následující [příklad šablony](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/startsendswith.json) ukazuje, jak používat funkce StartsWith a EndsWith:

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
| startsTrue | Logick | True |
| startsCapTrue | Logick | True |
| startsFalse | Logick | False |
| endsTrue | Logick | True |
| endsCapTrue | Logick | True |
| endsFalse | Logick | False |

## <a name="first"></a>první

`first(arg1)`

Vrátí první znak řetězce nebo první prvek pole.

### <a name="parameters"></a>Parametry

| Parametr | Požaduje se | Typ | Popis |
|:--- |:--- |:--- |:--- |
| arg1 |Ano |pole nebo řetězec |Hodnota pro načtení prvního prvku nebo znaku. |

### <a name="return-value"></a>Návratová hodnota

Řetězec prvního znaku nebo typ (řetězec, int, Array nebo Object) prvního prvku v poli.

### <a name="examples"></a>Příklady

Následující [příklad šablony](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/first.json) ukazuje, jak použít první funkci s polem a řetězcem.

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
| arrayOutput | Řetězec | jeden |
| stringOutput | Řetězec | Zápis |

## <a name="format"></a>formátovat

`format(formatString, arg1, arg2, ...)`

Vytvoří formátovaný řetězec ze vstupních hodnot.

### <a name="parameters"></a>Parametry

| Parametr | Požaduje se | Typ | Popis |
|:--- |:--- |:--- |:--- |
| formatString | Ano | string | Složený řetězec formátu. |
| arg1 | Ano | řetězec, celé číslo nebo logická hodnota | Hodnota, která má být zahrnuta do formátovaného řetězce. |
| Další argumenty | Ne | řetězec, celé číslo nebo logická hodnota | Další hodnoty, které mají být zahrnuty do formátovaného řetězce. |

### <a name="remarks"></a>Poznámky

Tuto funkci použijte k formátování řetězce v šabloně. Používá stejné možnosti formátování jako metoda [System. String. Format](/dotnet/api/system.string.format) v rozhraní .NET.

### <a name="examples"></a>Příklady

Následující příklad šablony ukazuje, jak použít funkci Format.

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
| formatTest | Řetězec | Hello, User. Naformátované číslo: 8 175 133 |

## <a name="guid"></a>hlavních

`guid(baseString, ...)`

Vytvoří hodnotu ve formátu globálně jedinečného identifikátoru na základě hodnot poskytnutých jako parametry.

### <a name="parameters"></a>Parametry

| Parametr | Požaduje se | Typ | Popis |
|:--- |:--- |:--- |:--- |
| baseString |Ano |string |Hodnota použitá ve funkci hash k vytvoření GUID. |
| Další parametry podle potřeby |Ne |string |Můžete přidat tolik řetězců, kolik jich je potřeba, a vytvořit tak hodnotu, která určuje úroveň jedinečnosti. |

### <a name="remarks"></a>Poznámky

Tato funkce je užitečná v případě, že potřebujete vytvořit hodnotu ve formátu globálně jedinečného identifikátoru. Poskytnete hodnoty parametrů, které omezují rozsah jedinečnosti pro výsledek. Můžete určit, jestli je název jedinečný pro předplatné, skupinu prostředků nebo nasazení.

Vrácená hodnota není náhodný řetězec, ale místo toho je výsledkem funkce hash u parametrů. Vrácená hodnota je 36 znaků. Není globálně jedinečný. Chcete-li vytvořit nový identifikátor GUID, který není založen na této hodnotě hash parametrů, použijte funkci [newGuid](#newguid) .

Následující příklady ukazují, jak pomocí GUID vytvořit jedinečnou hodnotu pro běžně používané úrovně.

Jedinečný obor pro předplatné

```json
"[guid(subscription().subscriptionId)]"
```

Jedinečný obor pro skupinu prostředků

```json
"[guid(resourceGroup().id)]"
```

Jedinečné v oboru nasazení pro skupinu prostředků

```json
"[guid(resourceGroup().id, deployment().name)]"
```

### <a name="return-value"></a>Návratová hodnota

Řetězec obsahující 36 znaků ve formátu globálně jedinečného identifikátoru.

### <a name="examples"></a>Příklady

Následující [příklad šablony](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/guid.json) vrátí výsledky z identifikátoru GUID:

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

## <a name="indexof"></a>indexOf

`indexOf(stringToSearch, stringToFind)`

Vrátí první pozici hodnoty v rámci řetězce. V porovnání se nerozlišují malá a velká písmena.

### <a name="parameters"></a>Parametry

| Parametr | Požaduje se | Typ | Popis |
|:--- |:--- |:--- |:--- |
| stringToSearch |Ano |string |Hodnota, která obsahuje položku, kterou chcete najít. |
| stringToFind |Ano |string |Hodnota, která se má najít |

### <a name="return-value"></a>Návratová hodnota

Celé číslo představující pozici hledané položky. Hodnota je počítána od nuly. Pokud se položka nenajde, vrátí se – 1.

### <a name="examples"></a>Příklady

Následující [příklad šablony](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/indexof.json) ukazuje, jak používat funkce IndexOf a LastIndexOf:

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
| první | Hmot | 0 |
| Poslední | Hmot | 3 |
| firstString | Hmot | 2 |
| lastString | Hmot | 0 |
| notFound | Hmot | – 1 |

## <a name="last"></a>posledního

`last (arg1)`

Vrátí poslední znak řetězce nebo poslední prvek pole.

### <a name="parameters"></a>Parametry

| Parametr | Požaduje se | Typ | Popis |
|:--- |:--- |:--- |:--- |
| arg1 |Ano |pole nebo řetězec |Hodnota, která načte poslední prvek nebo znak. |

### <a name="return-value"></a>Návratová hodnota

Řetězec posledního znaku nebo typ (řetězec, int, Array nebo Object) posledního prvku v poli.

### <a name="examples"></a>Příklady

Následující [příklad šablony](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/last.json) ukazuje, jak použít poslední funkci s polem a řetězcem.

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
| arrayOutput | Řetězec | tři |
| stringOutput | Řetězec | cerebrální |

## <a name="lastindexof"></a>lastIndexOf

`lastIndexOf(stringToSearch, stringToFind)`

Vrátí poslední pozici hodnoty v rámci řetězce. V porovnání se nerozlišují malá a velká písmena.

### <a name="parameters"></a>Parametry

| Parametr | Požaduje se | Typ | Popis |
|:--- |:--- |:--- |:--- |
| stringToSearch |Ano |string |Hodnota, která obsahuje položku, kterou chcete najít. |
| stringToFind |Ano |string |Hodnota, která se má najít |

### <a name="return-value"></a>Návratová hodnota

Celé číslo představující poslední pozici položky, která se má najít Hodnota je počítána od nuly. Pokud se položka nenajde, vrátí se – 1.

### <a name="examples"></a>Příklady

Následující [příklad šablony](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/indexof.json) ukazuje, jak používat funkce IndexOf a LastIndexOf:

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
| první | Hmot | 0 |
| Poslední | Hmot | 3 |
| firstString | Hmot | 2 |
| lastString | Hmot | 0 |
| notFound | Hmot | – 1 |

## <a name="length"></a>časový

`length(string)`

Vrátí počet znaků v řetězci, prvky v poli nebo vlastnosti na úrovni root v objektu.

### <a name="parameters"></a>Parametry

| Parametr | Požaduje se | Typ | Popis |
|:--- |:--- |:--- |:--- |
| arg1 |Ano |pole, řetězec nebo objekt |Pole, které se má použít pro získání počtu prvků, řetězec, který se má použít pro získání počtu znaků, nebo objekt, který se má použít pro získání počtu vlastností na úrovni root. |

### <a name="return-value"></a>Návratová hodnota

Int. 

### <a name="examples"></a>Příklady

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

| Name (Název) | Typ | Hodnota |
| ---- | ---- | ----- |
| arrayLength | Hmot | 3 |
| stringLength | Hmot | 13 |
| objectLength | Hmot | 4 |

## <a name="newguid"></a>newGuid

`newGuid()`

Vrátí hodnotu ve formátu globálně jedinečného identifikátoru. **Tato funkce se dá použít jenom ve výchozí hodnotě pro parametr.**

### <a name="remarks"></a>Poznámky

Tuto funkci lze použít pouze v rámci výrazu pro výchozí hodnotu parametru. Použití této funkce kdekoli jinde v šabloně vrátí chybu. Funkce není povolena v ostatních částech šablony, protože vrací jinou hodnotu pokaždé, když je volána. Nasazení stejné šablony se stejnými parametry by nespolehlivě přineslo stejné výsledky.

Funkce newGuid se liší od funkce [GUID](#guid) , protože nepřijímá žádné parametry. Když zavoláte GUID se stejným parametrem, vrátí se vždy stejný identifikátor. Identifikátor GUID použijte, pokud potřebujete spolehlivě vygenerovat stejný identifikátor GUID pro konkrétní prostředí. Použijte newGuid, pokud potřebujete jiný identifikátor pokaždé, jako je například nasazení prostředků do testovacího prostředí.

Použijete-li [možnost pro opětovné nasazení dřívějšího úspěšného nasazení](rollback-on-error.md)a předchozí nasazení zahrnuje parametr, který používá newGuid, parametr není znovu vyhodnocen. Místo toho je hodnota parametru z dřívějšího nasazení automaticky znovu použita při nasazení zpět.

V testovacím prostředí možná budete muset opakovaně nasazovat prostředky, které jsou po krátkou dobu živé. Místo vytváření jedinečných názvů můžete k vytváření jedinečných názvů použít newGuid s [uniqueString](#uniquestring) .

Buďte opatrní při opětovném nasazení šablony, která spoléhá na funkci newGuid, na výchozí hodnotu. Když znovu nasadíte a nezadáte hodnotu parametru, funkce se znovu vyhodnotí. Pokud chcete aktualizovat existující prostředek místo vytvoření nového prostředku, předejte hodnotu parametru z dřívějšího nasazení.

### <a name="return-value"></a>Návratová hodnota

Řetězec obsahující 36 znaků ve formátu globálně jedinečného identifikátoru.

### <a name="examples"></a>Příklady

Následující příklad šablony ukazuje parametr s novým identifikátorem.

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

Výstup z výše uvedeného příkladu se u každého nasazení liší, ale bude vypadat přibližně takto:

| Name (Název) | Typ | Hodnota |
| ---- | ---- | ----- |
| guidOutput | string | b76a51fc-bd72-4a77-b9a2-3c29e7d2e551 |

Následující příklad používá funkci newGuid k vytvoření jedinečného názvu pro účet úložiště. Tato šablona může fungovat pro testovací prostředí, ve kterém existuje účet úložiště po krátkou dobu a který se znovu neimplementuje.

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

Výstup z výše uvedeného příkladu se u každého nasazení liší, ale bude vypadat přibližně takto:

| Name (Název) | Typ | Hodnota |
| ---- | ---- | ----- |
| nameOutput | string | storagenziwvyru7uxie |


## <a name="padleft"></a>padLeft

`padLeft(valueToPad, totalLength, paddingCharacter)`

Vrátí řetězec zarovnaný doprava přidáním znaků vlevo, dokud nedosáhnete celkové zadané délky.

### <a name="parameters"></a>Parametry

| Parametr | Požaduje se | Typ | Popis |
|:--- |:--- |:--- |:--- |
| valueToPad |Ano |řetězec nebo int |Hodnota, která se má zarovnat vpravo |
| totalLength |Ano |int |Celkový počet znaků ve vráceném řetězci. |
| paddingCharacter |Ne |jeden znak |Znak, který se má použít pro levé odsazení, dokud se nedosáhne celkové délky Výchozí hodnota je mezera. |

Pokud je původní řetězec delší než počet znaků, které mají být vloženy, nejsou přidány žádné znaky.

### <a name="return-value"></a>Návratová hodnota

Řetězec s minimálním počtem zadaných znaků.

### <a name="examples"></a>Příklady

Následující [příklad šablony](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/padleft.json) ukazuje, jak doplnit hodnotu zadaného parametru uživatelem přidáním nulového znaku, dokud nedosáhne celkového počtu znaků. 

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

## <a name="replace"></a>náhrady

`replace(originalString, oldString, newString)`

Vrátí nový řetězec se všemi instancemi jednoho řetězce nahrazeného jiným řetězcem.

### <a name="parameters"></a>Parametry

| Parametr | Požaduje se | Typ | Popis |
|:--- |:--- |:--- |:--- |
| originalString |Ano |string |Hodnota, která má všechny instance jednoho řetězce nahrazena jiným řetězcem. |
| Starý řetězec |Ano |string |Řetězec, který má být odebrán z původního řetězce. |
| Nový řetězec |Ano |string |Řetězec, který má být přidán místo odebraného řetězce. |

### <a name="return-value"></a>Návratová hodnota

Řetězec s nahrazenými znaky.

### <a name="examples"></a>Příklady

Následující [příklad šablony](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/replace.json) ukazuje, jak odebrat všechny pomlčky z uživatelsky zadaného řetězce a jak nahradit část řetězce jiným řetězcem.

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
| secondOutput | Řetězec | 123-123 – xxxx |

## <a name="skip"></a>přímo

`skip(originalValue, numberToSkip)`

Vrátí řetězec se všemi znaky po zadaném počtu znaků nebo pole se všemi prvky po zadaném počtu prvků.

### <a name="parameters"></a>Parametry

| Parametr | Požaduje se | Typ | Popis |
|:--- |:--- |:--- |:--- |
| Původní |Ano |pole nebo řetězec |Pole nebo řetězec, který se má použít pro přeskočení. |
| numberToSkip |Ano |int |Počet prvků nebo znaků, které mají být přeskočeny. Pokud je tato hodnota 0 nebo méně, vrátí se všechny prvky nebo znaky v hodnotě. Pokud je větší než délka pole nebo řetězce, je vráceno prázdné pole nebo řetězec. |

### <a name="return-value"></a>Návratová hodnota

Pole nebo řetězec.

### <a name="examples"></a>Příklady

Následující [příklad šablony](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/skip.json) přeskočí zadaný počet prvků v poli a zadaný počet znaků v řetězci.

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
| arrayOutput | Pole | ["tři"] |
| stringOutput | Řetězec | 2 3 |

## <a name="split"></a>split

`split(inputString, delimiter)`

Vrátí pole řetězců, které obsahují podřetězce vstupního řetězce, které jsou odděleny zadanými oddělovači.

### <a name="parameters"></a>Parametry

| Parametr | Požaduje se | Typ | Popis |
|:--- |:--- |:--- |:--- |
| inputString |Ano |string |Řetězec, který má být rozdělen. |
| oddělovač |Ano |řetězec nebo pole řetězců |Oddělovač, který se má použít pro rozdělení řetězce. |

### <a name="return-value"></a>Návratová hodnota

Pole řetězců.

### <a name="examples"></a>Příklady

Následující [příklad šablony](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/split.json) rozdělí vstupní řetězec čárkou a buď čárkou, nebo středníkem.

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
| firstOutput | Pole | ["One"; "Two"; "tři"] |
| secondOutput | Pole | ["One"; "Two"; "tři"] |

## <a name="startswith"></a>startsWith

`startsWith(stringToSearch, stringToFind)`

Určuje, zda řetězec začíná hodnotou. V porovnání se nerozlišují malá a velká písmena.

### <a name="parameters"></a>Parametry

| Parametr | Požaduje se | Typ | Popis |
|:--- |:--- |:--- |:--- |
| stringToSearch |Ano |string |Hodnota, která obsahuje položku, kterou chcete najít. |
| stringToFind |Ano |string |Hodnota, která se má najít |

### <a name="return-value"></a>Návratová hodnota

**True** , pokud se první znak nebo znaky řetězce shodují s hodnotou; v opačném případě **false**.

### <a name="examples"></a>Příklady

Následující [příklad šablony](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/startsendswith.json) ukazuje, jak používat funkce StartsWith a EndsWith:

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
| startsTrue | Logick | True |
| startsCapTrue | Logick | True |
| startsFalse | Logick | False |
| endsTrue | Logick | True |
| endsCapTrue | Logick | True |
| endsFalse | Logick | False |

## <a name="string"></a>string

`string(valueToConvert)`

Převede zadanou hodnotu na řetězec.

### <a name="parameters"></a>Parametry

| Parametr | Požaduje se | Typ | Popis |
|:--- |:--- |:--- |:--- |
| valueToConvert |Ano | Všechny |Hodnota, která má být převedena na řetězec. Jakýkoli typ hodnoty lze převést, včetně objektů a polí. |

### <a name="return-value"></a>Návratová hodnota

Řetězec převedené hodnoty.

### <a name="examples"></a>Příklady

Následující [příklad šablony](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/string.json) ukazuje, jak převést různé typy hodnot na řetězce:

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
| objectOutput | Řetězec | {"valuet": 10, "Hodnotab": "example text"} |
| arrayOutput | Řetězec | ["a", "b", "c"] |
| intOutput | Řetězec | 5 |

## <a name="substring"></a>podřetězec

`substring(stringToParse, startIndex, length)`

Vrátí podřetězec, který začíná na zadané pozici znaku a obsahuje zadaný počet znaků.

### <a name="parameters"></a>Parametry

| Parametr | Požaduje se | Typ | Popis |
|:--- |:--- |:--- |:--- |
| stringToParse |Ano |string |Původní řetězec, ze kterého je dílčí řetězec extrahován. |
| Indexu |Ne |int |Počáteční pozice znaku na základě nuly pro podřetězec. |
| časový |Ne |int |Počet znaků podřetězce. Musí odkazovat na umístění v rámci řetězce. Musí být nula nebo větší. |

### <a name="return-value"></a>Návratová hodnota

Podřetězec. Nebo prázdný řetězec, pokud je délka nula.

### <a name="remarks"></a>Poznámky

Tato funkce se nezdařila, pokud podřetězec překračuje konec řetězce, nebo pokud je délka menší než nula. Následující příklad se nezdařil s chybou "parametry index a Length musí odkazovat na umístění v rámci řetězce. Parametr indexu: ' 0 ', parametr length: ' 11 ', délka řetězcového parametru: ' 10 '.

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

## <a name="take"></a>nezbytná

`take(originalValue, numberToTake)`

Vrátí řetězec, který má zadaný počet znaků od začátku řetězce, nebo pole se zadaným počtem prvků od začátku pole.

### <a name="parameters"></a>Parametry

| Parametr | Požaduje se | Typ | Popis |
|:--- |:--- |:--- |:--- |
| Původní |Ano |pole nebo řetězec |Pole nebo řetězec, ze kterého mají být přebírat prvky. |
| numberToTake |Ano |int |Počet prvků nebo znaků, které mají být přebírat. Pokud je tato hodnota 0 nebo méně, vrátí se prázdné pole nebo řetězec. Pokud je větší než délka daného pole nebo řetězce, vrátí se všechny prvky v poli nebo řetězci. |

### <a name="return-value"></a>Návratová hodnota

Pole nebo řetězec.

### <a name="examples"></a>Příklady

Následující [příklad šablony](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/take.json) přebírá zadaný počet prvků z pole a znaky z řetězce.

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
| arrayOutput | Pole | ["One"; "Two"] |
| stringOutput | Řetězec | pnete |

## <a name="tolower"></a>toLower

`toLower(stringToChange)`

Převede zadaný řetězec na malá písmena.

### <a name="parameters"></a>Parametry

| Parametr | Požaduje se | Typ | Popis |
|:--- |:--- |:--- |:--- |
| stringToChange |Ano |string |Hodnota, která se má převést na malá písmena. |

### <a name="return-value"></a>Návratová hodnota

Řetězec převedený na malá písmena.

### <a name="examples"></a>Příklady

Následující [příklad šablony](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/tolower.json) převede hodnotu parametru na malá písmena a na velká písmena.

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

## <a name="toupper"></a>toUpper

`toUpper(stringToChange)`

Převede zadaný řetězec na velká písmena.

### <a name="parameters"></a>Parametry

| Parametr | Požaduje se | Typ | Popis |
|:--- |:--- |:--- |:--- |
| stringToChange |Ano |string |Hodnota, která má být převedena na velká písmena. |

### <a name="return-value"></a>Návratová hodnota

Řetězec převedený na velká písmena.

### <a name="examples"></a>Příklady

Následující [příklad šablony](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/tolower.json) převede hodnotu parametru na malá písmena a na velká písmena.

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

## <a name="trim"></a>sklon

`trim (stringToTrim)`

Odebere všechny úvodní a koncové prázdné znaky ze zadaného řetězce.

### <a name="parameters"></a>Parametry

| Parametr | Požaduje se | Typ | Popis |
|:--- |:--- |:--- |:--- |
| stringToTrim |Ano |string |Hodnota, která má být oříznuta. |

### <a name="return-value"></a>Návratová hodnota

Řetězec bez počátečních a koncových prázdných znaků.

### <a name="examples"></a>Příklady

Následující [příklad šablony](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/trim.json) ořízne prázdné znaky z parametru.

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
| vrátit | Řetězec | Jedna dva tři |

## <a name="uniquestring"></a>uniqueString

`uniqueString (baseString, ...)`

Vytvoří deterministický řetězec hash založený na hodnotách poskytnutých jako parametry. 

### <a name="parameters"></a>Parametry

| Parametr | Požaduje se | Typ | Popis |
|:--- |:--- |:--- |:--- |
| baseString |Ano |string |Hodnota použitá ve funkci hash k vytvoření jedinečného řetězce. |
| Další parametry podle potřeby |Ne |string |Můžete přidat tolik řetězců, kolik jich je potřeba, a vytvořit tak hodnotu, která určuje úroveň jedinečnosti. |

### <a name="remarks"></a>Poznámky

Tato funkce je užitečná v případě, že potřebujete vytvořit jedinečný název pro určitý prostředek. Poskytnete hodnoty parametrů, které omezují rozsah jedinečnosti pro výsledek. Můžete určit, jestli je název jedinečný pro předplatné, skupinu prostředků nebo nasazení. 

Vrácená hodnota není náhodný řetězec, ale místo toho je výsledkem funkce hash. Vrácená hodnota je 13 znaků. Není globálně jedinečný. Je možné, že budete chtít kombinovat hodnotu s předponou z konvence vytváření názvů a vytvořit smysluplný název. Následující příklad ukazuje formát vrácené hodnoty. Skutečná hodnota se liší podle poskytnutých parametrů.

    tcvhiyu5h2o5o

Následující příklady ukazují, jak používat uniqueString k vytvoření jedinečné hodnoty pro běžně používané úrovně.

Jedinečný obor pro předplatné

```json
"[uniqueString(subscription().subscriptionId)]"
```

Jedinečný obor pro skupinu prostředků

```json
"[uniqueString(resourceGroup().id)]"
```

Jedinečné v oboru nasazení pro skupinu prostředků

```json
"[uniqueString(resourceGroup().id, deployment().name)]"
```

Následující příklad ukazuje, jak vytvořit jedinečný název pro účet úložiště na základě vaší skupiny prostředků. V rámci skupiny prostředků není název jedinečný, pokud je konstruován stejným způsobem.

```json
"resources": [{ 
    "name": "[concat('storage', uniqueString(resourceGroup().id))]", 
    "type": "Microsoft.Storage/storageAccounts", 
    ...
```

Pokud potřebujete vytvořit nový jedinečný název pokaždé, když nasadíte šablonu, a neplánujete aktualizovat prostředek, můžete použít funkci [UtcNow](#utcnow) s uniqueString. Tento postup můžete použít v testovacím prostředí. Příklad naleznete v tématu [UtcNow](#utcnow).

### <a name="return-value"></a>Návratová hodnota

Řetězec obsahující 13 znaků.

### <a name="examples"></a>Příklady

Následující [příklad šablony](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/uniquestring.json) vrátí výsledky z uniquestring:

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

## <a name="uri"></a>identifikátor URI

`uri (baseUri, relativeUri)`

Vytvoří absolutní identifikátor URI kombinováním řetězce baseUri a relativeUri.

### <a name="parameters"></a>Parametry

| Parametr | Požaduje se | Typ | Popis |
|:--- |:--- |:--- |:--- |
| Identifikátor |Ano |string |Základní řetězec identifikátoru URI. Pečlivě Sledujte chování týkající se zpracování koncového lomítka ('/'), jak je popsáno v následující tabulce.  |
| relativeUri |Ano |string |Relativní řetězec identifikátoru URI, který se má přidat do základního řetězce identifikátoru URI. |

* Pokud **BaseUri** končí na koncovém lomítku, výsledek je jednoduše **BaseUri** následovaný **relativeUri**.

* Pokud **BaseUri** nekončí na koncovém lomítku, stane se jedna z těchto dvou věcí.  

   * Pokud **BaseUri** nemá žádné lomítko (kromě znaku "//" poblíž začátku), výsledek je jednoduše **BaseUri** následovaný **relativeUri**.

   * Pokud má **BaseUri** lomítka, ale nemá na konci lomítka, vše od posledního lomítka kromě je odebráno z **BaseUri** a výsledek je **BaseUri** , za nímž následuje **relativeUri**.
     
Zde je několik příkladů:

```
uri('http://contoso.org/firstpath', 'myscript.sh') -> http://contoso.org/myscript.sh
uri('http://contoso.org/firstpath/', 'myscript.sh') -> http://contoso.org/firstpath/myscript.sh
uri('http://contoso.org/firstpath/azuredeploy.json', 'myscript.sh') -> http://contoso.org/firstpath/myscript.sh
uri('http://contoso.org/firstpath/azuredeploy.json/', 'myscript.sh') -> http://contoso.org/firstpath/azuredeploy.json/myscript.sh
```
Pro úplné podrobnosti se přeloží parametry **BaseUri** a **relativeUri** , jak je uvedeno v [dokumentu RFC 3986, oddíle 5](https://tools.ietf.org/html/rfc3986#section-5).

### <a name="return-value"></a>Návratová hodnota

Řetězec představující absolutní identifikátor URI pro základní a relativní hodnoty.

### <a name="examples"></a>Příklady

Následující příklad ukazuje, jak vytvořit odkaz na vnořenou šablonu na základě hodnoty nadřazené šablony.

```json
"templateLink": "[uri(deployment().properties.templateLink.uri, 'nested/azuredeploy.json')]"
```

Následující [příklad šablony](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/uri.json) ukazuje, jak použít identifikátory URI, UriComponent a uriComponentToString:

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
| uriOutput | Řetězec | http://contoso.com/resources/nested/azuredeploy.json |
| componentOutput | Řetězec | http% 3 A% 2 F% 2 F contoso. com% 2Fresources% 2Fnested% 2Fazuredeploy. JSON |
| toStringOutput | Řetězec | http://contoso.com/resources/nested/azuredeploy.json |

## <a name="uricomponent"></a>uriComponent

`uricomponent(stringToEncode)`

Zakóduje identifikátor URI.

### <a name="parameters"></a>Parametry

| Parametr | Požaduje se | Typ | Popis |
|:--- |:--- |:--- |:--- |
| stringToEncode |Ano |string |Hodnota, která se má zakódovat |

### <a name="return-value"></a>Návratová hodnota

Řetězec hodnoty kódované identifikátorem URI.

### <a name="examples"></a>Příklady

Následující [příklad šablony](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/uri.json) ukazuje, jak použít identifikátory URI, UriComponent a uriComponentToString:

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
| uriOutput | Řetězec | http://contoso.com/resources/nested/azuredeploy.json |
| componentOutput | Řetězec | http% 3 A% 2 F% 2 F contoso. com% 2Fresources% 2Fnested% 2Fazuredeploy. JSON |
| toStringOutput | Řetězec | http://contoso.com/resources/nested/azuredeploy.json |

## <a name="uricomponenttostring"></a>uriComponentToString

`uriComponentToString(uriEncodedString)`

Vrátí řetězec hodnoty kódované identifikátorem URI.

### <a name="parameters"></a>Parametry

| Parametr | Požaduje se | Typ | Popis |
|:--- |:--- |:--- |:--- |
| uriEncodedString |Ano |string |Hodnota zakódovaná identifikátorem URI, která má být převedena na řetězec. |

### <a name="return-value"></a>Návratová hodnota

Dekódovaný řetězec hodnoty kódované identifikátorem URI.

### <a name="examples"></a>Příklady

Následující [příklad šablony](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/uri.json) ukazuje, jak použít identifikátory URI, UriComponent a uriComponentToString:

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
| uriOutput | Řetězec | http://contoso.com/resources/nested/azuredeploy.json |
| componentOutput | Řetězec | http% 3 A% 2 F% 2 F contoso. com% 2Fresources% 2Fnested% 2Fazuredeploy. JSON |
| toStringOutput | Řetězec | http://contoso.com/resources/nested/azuredeploy.json |

## <a name="utcnow"></a>utcNow

`utcNow(format)`

Vrátí aktuální hodnotu DateTime (UTC) v zadaném formátu. Pokud není zadán žádný formát, bude použit formát ISO 8601 (yyyyMMddTHHmmssZ). **Tato funkce se dá použít jenom ve výchozí hodnotě pro parametr.**

### <a name="parameters"></a>Parametry

| Parametr | Požaduje se | Typ | Popis |
|:--- |:--- |:--- |:--- |
| formátovat |Ne |string |Hodnota zakódovaná identifikátorem URI, která má být převedena na řetězec. Použijte buď [standardní formátovací řetězce](https://docs.microsoft.com/dotnet/standard/base-types/standard-date-and-time-format-strings) , nebo [Vlastní řetězce formátu](https://docs.microsoft.com/dotnet/standard/base-types/custom-date-and-time-format-strings). |

### <a name="remarks"></a>Poznámky

Tuto funkci lze použít pouze v rámci výrazu pro výchozí hodnotu parametru. Použití této funkce kdekoli jinde v šabloně vrátí chybu. Funkce není povolena v ostatních částech šablony, protože vrací jinou hodnotu pokaždé, když je volána. Nasazení stejné šablony se stejnými parametry by nespolehlivě přineslo stejné výsledky.

Použijete-li [možnost pro opětovné nasazení dřívějšího úspěšného nasazení](rollback-on-error.md)a předchozí nasazení zahrnuje parametr, který používá UtcNow, parametr není znovu vyhodnocen. Místo toho je hodnota parametru z dřívějšího nasazení automaticky znovu použita při nasazení zpět.

Buďte opatrní při opětovném nasazení šablony, která spoléhá na funkci utcNow, na výchozí hodnotu. Když znovu nasadíte a nezadáte hodnotu parametru, funkce se znovu vyhodnotí. Pokud chcete aktualizovat existující prostředek místo vytvoření nového prostředku, předejte hodnotu parametru z dřívějšího nasazení.

### <a name="return-value"></a>Návratová hodnota

Aktuální hodnota DateTime UTC.

### <a name="examples"></a>Příklady

Následující příklad šablony ukazuje různé formáty hodnoty data a času.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "utcValue": {
            "type": "string",
            "defaultValue": "[utcNow()]"
        },
        "utcShortValue": {
            "type": "string",
            "defaultValue": "[utcNow('d')]"
        },
        "utcCustomValue": {
            "type": "string",
            "defaultValue": "[utcNow('M d')]"
        }
    },
    "resources": [
    ],
    "outputs": {
        "utcOutput": {
            "type": "string",
            "value": "[parameters('utcValue')]"
        },
        "utcShortOutput": {
            "type": "string",
            "value": "[parameters('utcShortValue')]"
        },
        "utcCustomOutput": {
            "type": "string",
            "value": "[parameters('utcCustomValue')]"
        }
    }
}
```

Výstup z výše uvedeného příkladu se u každého nasazení liší, ale bude vypadat přibližně takto:

| Name (Název) | Typ | Hodnota |
| ---- | ---- | ----- |
| utcOutput | string | 20190305T175318Z |
| utcShortOutput | string | 03/05/2019 |
| utcCustomOutput | string | 3 5 |

Další příklad ukazuje, jak použít hodnotu z funkce při nastavení hodnoty značky.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2018-05-01/subscriptionDeploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "utcShort": {
            "type": "string",
            "defaultValue": "[utcNow('d')]"
        },
        "rgName": {
            "type": "string"
        }
    },
    "resources": [
        {
            "type": "Microsoft.Resources/resourceGroups",
            "apiVersion": "2018-05-01",
            "name": "[parameters('rgName')]",
            "location": "westeurope",
            "tags":{
                "createdDate": "[parameters('utcShort')]"
            },
            "properties":{}
        }
    ],
    "outputs": {
        "utcShort": {
            "type": "string",
            "value": "[parameters('utcShort')]"
        }
    }
}
```

## <a name="next-steps"></a>Další kroky
* Popis sekcí v šabloně Azure Resource Manager najdete v tématu [vytváření šablon Azure Resource Manager](resource-group-authoring-templates.md).
* Chcete-li sloučit více šablon, přečtěte si téma [použití propojených šablon s Azure Resource Manager](resource-group-linked-templates.md).
* Informace o iteraci zadaného počtu výskytů při vytváření typu prostředku najdete v tématu [vytvoření více instancí prostředků v Azure Resource Manager](resource-group-create-multiple.md).
* Pokud chcete zjistit, jak nasadit šablonu, kterou jste vytvořili, přečtěte si téma [nasazení aplikace pomocí šablony Azure Resource Manager](resource-group-template-deploy.md).

