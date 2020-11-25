---
title: Funkce šablon – porovnání
description: Popisuje funkce, které se použijí v šabloně Azure Resource Manager k porovnání hodnot.
ms.topic: conceptual
ms.date: 11/18/2020
ms.openlocfilehash: c5ffcfe7688935da6ea5602cdb2c66a8b86a8d88
ms.sourcegitcommit: 10d00006fec1f4b69289ce18fdd0452c3458eca5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/21/2020
ms.locfileid: "96004598"
---
# <a name="comparison-functions-for-arm-templates"></a>Funkce porovnání pro šablony ARM

Správce prostředků poskytuje několik funkcí pro porovnávání šablon Azure Resource Manager (ARM).

* [COALESCE](#coalesce)
* [equals](#equals)
* [greater](#greater)
* [greaterOrEquals](#greaterorequals)
* [tolik](#less)
* [lessOrEquals](#lessorequals)

[!INCLUDE [Bicep preview](../../../includes/resource-manager-bicep-preview.md)]

## <a name="coalesce"></a>COALESCE

`coalesce(arg1, arg2, arg3, ...)`

Vrátí první hodnotu, která není null, z parametrů. Prázdné řetězce, prázdné pole a prázdné objekty nejsou null.

### <a name="parameters"></a>Parametry

| Parametr | Povinné | Typ | Description |
|:--- |:--- |:--- |:--- |
| arg1 |Yes |int, String, Array nebo Object |První hodnota, která má být testována na hodnotu null. |
| Další argumenty |No |int, String, Array nebo Object |Další hodnoty, které mají být testovány na hodnotu null. |

### <a name="return-value"></a>Vrácená hodnota

Hodnota prvních parametrů, které nejsou null, což může být řetězec, int, Array nebo Object. Hodnota null, pokud jsou všechny parametry null.

### <a name="example"></a>Příklad

Následující [příklad šablony](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/coalesce.json) ukazuje výstup z různých použití funkce coalesce.

# <a name="json"></a>[JSON](#tab/json)

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "objectToTest": {
      "type": "object",
      "defaultValue": {
        "null1": null,
        "null2": null,
        "string": "default",
        "int": 1,
        "object": { "first": "default" },
        "array": [ 1 ]
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

# <a name="bicep"></a>[Bicep](#tab/bicep)

```bicep
param objectToTest object = {
  'null1': null
  'null2': null
  'string': 'default'
  'int': 1
  'object': {
    'first': 'default'
  }
  'array': [
    1
  ]
}

output stringOutput string = coalesce(objectToTest.null1, objectToTest.null2, objectToTest.string)
output intOutput int = coalesce(objectToTest.null1, objectToTest.null2, objectToTest.int)
output objectOutput object = coalesce(objectToTest.null1, objectToTest.null2, objectToTest.object)
output arrayOutput array = coalesce(objectToTest.null1, objectToTest.null2, objectToTest.array)
output emptyOutput bool =empty(coalesce(objectToTest.null1, objectToTest.null2))
```

---

Výstup z předchozího příkladu s výchozími hodnotami je:

| Název | Typ | Hodnota |
| ---- | ---- | ----- |
| stringOutput | Řetězec | default |
| intOutput | Int | 1 |
| objectOutput | Objekt | {"First": "default"} |
| arrayOutput | Pole |  [1] |
| emptyOutput | Logická hodnota | Ano |

## <a name="equals"></a>equals

`equals(arg1, arg2)`

Kontroluje, zda jsou dvě hodnoty vzájemně stejné. `equals`Funkce není v bicep podporována. `==`Místo toho použijte operátor.

### <a name="parameters"></a>Parametry

| Parametr | Povinné | Typ | Description |
|:--- |:--- |:--- |:--- |
| arg1 |Yes |int, String, Array nebo Object |První hodnota pro kontrolu rovnosti. |
| arg2 |Yes |int, String, Array nebo Object |Druhá hodnota pro kontrolu rovnosti. |

### <a name="return-value"></a>Vrácená hodnota

Vrátí **hodnotu true** , pokud jsou hodnoty stejné; v opačném případě **false**.

### <a name="remarks"></a>Poznámky

Funkce Equals se často používá s `condition` prvkem k otestování, jestli je prostředek nasazený.

# <a name="json"></a>[JSON](#tab/json)

```json
{
  "condition": "[equals(parameters('newOrExisting'),'new')]",
  "type": "Microsoft.Storage/storageAccounts",
  "name": "[variables('storageAccountName')]",
  "apiVersion": "2017-06-01",
  "location": "[resourceGroup().location]",
  "sku": {
    "name": "[variables('storageAccountType')]"
  },
  "kind": "Storage",
  "properties": {}
}
```

# <a name="bicep"></a>[Bicep](#tab/bicep)

> [!NOTE]
> `Conditions` ještě nejsou v bicep implementované. Viz [podmínky](https://github.com/Azure/bicep/issues/186).

---

### <a name="example"></a>Příklad

Následující [příklad šablony](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/equals.json) kontroluje různé typy hodnot pro rovnost. Všechny výchozí hodnoty vrátí hodnotu true.

# <a name="json"></a>[JSON](#tab/json)

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "firstInt": {
      "type": "int",
      "defaultValue": 1
    },
    "secondInt": {
      "type": "int",
      "defaultValue": 1
    },
    "firstString": {
      "type": "string",
      "defaultValue": "a"
    },
    "secondString": {
      "type": "string",
      "defaultValue": "a"
    },
    "firstArray": {
      "type": "array",
      "defaultValue": [ "a", "b" ]
    },
    "secondArray": {
      "type": "array",
      "defaultValue": [ "a", "b" ]
    },
    "firstObject": {
      "type": "object",
      "defaultValue": { "a": "b" }
    },
    "secondObject": {
      "type": "object",
      "defaultValue": { "a": "b" }
    }
  },
  "resources": [
  ],
  "outputs": {
    "checkInts": {
      "type": "bool",
      "value": "[equals(parameters('firstInt'), parameters('secondInt') )]"
    },
    "checkStrings": {
      "type": "bool",
      "value": "[equals(parameters('firstString'), parameters('secondString'))]"
    },
    "checkArrays": {
      "type": "bool",
      "value": "[equals(parameters('firstArray'), parameters('secondArray'))]"
    },
    "checkObjects": {
      "type": "bool",
      "value": "[equals(parameters('firstObject'), parameters('secondObject'))]"
    }
  }
}
```

# <a name="bicep"></a>[Bicep](#tab/bicep)

```bicep
param firstInt int = 1
param secondInt int = 1
param firstString string = 'a'
param secondString string = 'a'
param firstArray array = [
  'a'
  'b'
]
param secondArray array = [
  'a'
  'b'
]
param firstObject object = {
  'a': 'b'
}
param secondObject object = {
  'a': 'b'
}

output checInts bool = firstInt == secondInt
output checkStrings bool = firstString == secondString
output checkArrays bool = firstArray == secondArray
output checkObjects bool = firstObject == secondObject
```

---

Výstup z předchozího příkladu s výchozími hodnotami je:

| Název | Typ | Hodnota |
| ---- | ---- | ----- |
| checkInts | Logická hodnota | Ano |
| checkStrings | Logická hodnota | Ano |
| checkArrays | Logická hodnota | Ano |
| checkObjects | Logická hodnota | Ano |

Následující [příklad šablony](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/not-equals.json) používá znaménko [Not](template-functions-logical.md#not) a **Equals**.

# <a name="json"></a>[JSON](#tab/json)

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "resources": [
  ],
  "outputs": {
    "checkNotEquals": {
      "type": "bool",
      "value": "[not(equals(1, 2))]"
    }
  }
}
```

# <a name="bicep"></a>[Bicep](#tab/bicep)

```bicep
output checkNotEquals bool = ! (1 == 2)
```

---

Výstup z předchozího příkladu:

| Název | Typ | Hodnota |
| ---- | ---- | ----- |
| checkNotEquals | Logická hodnota | Ano |

## <a name="greater"></a>greater

`greater(arg1, arg2)`

Kontroluje, zda je první hodnota větší než druhá hodnota. `greater`Funkce není v bicep podporována. `>`Místo toho použijte operátor.

### <a name="parameters"></a>Parametry

| Parametr | Povinné | Typ | Description |
|:--- |:--- |:--- |:--- |
| arg1 |Yes |int nebo String |První hodnota pro lepší porovnání. |
| arg2 |Yes |int nebo String |Druhá hodnota pro lepší porovnání. |

### <a name="return-value"></a>Vrácená hodnota

Vrátí **hodnotu true** , pokud je první hodnota větší než druhá hodnota. v opačném případě **false**.

### <a name="example"></a>Příklad

Následující [příklad šablony](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/greater.json) kontroluje, zda je jedna hodnota větší než druhá.

# <a name="json"></a>[JSON](#tab/json)

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "firstInt": {
      "type": "int",
      "defaultValue": 1
    },
    "secondInt": {
      "type": "int",
      "defaultValue": 2
    },
    "firstString": {
      "type": "string",
      "defaultValue": "A"
    },
    "secondString": {
      "type": "string",
      "defaultValue": "a"
    }
  },
  "resources": [
  ],
  "outputs": {
    "checkInts": {
      "type": "bool",
      "value": "[greater(parameters('firstInt'), parameters('secondInt') )]"
    },
    "checkStrings": {
      "type": "bool",
      "value": "[greater(parameters('firstString'), parameters('secondString'))]"
    }
  }
}
```

# <a name="bicep"></a>[Bicep](#tab/bicep)

```bicep
param firstInt int = 1
param secondInt int = 2
param firstString string = 'A'
param secondString string = 'a'

output checkInts bool = firstInt > secondInt
output checkStrings bool = firstString > secondString
```

---

Výstup z předchozího příkladu s výchozími hodnotami je:

| Název | Typ | Hodnota |
| ---- | ---- | ----- |
| checkInts | Logická hodnota | Ne |
| checkStrings | Logická hodnota | Ano |

## <a name="greaterorequals"></a>greaterOrEquals

`greaterOrEquals(arg1, arg2)`

Kontroluje, zda je první hodnota větší než nebo rovna druhé hodnotě. `greaterOrEquals`Funkce není v bicep podporována. `>=`Místo toho použijte operátor.

### <a name="parameters"></a>Parametry

| Parametr | Povinné | Typ | Description |
|:--- |:--- |:--- |:--- |
| arg1 |Yes |int nebo String |První hodnota pro porovnání větší nebo rovno. |
| arg2 |Yes |int nebo String |Druhá hodnota pro vyšší nebo stejné porovnání. |

### <a name="return-value"></a>Vrácená hodnota

Vrátí **hodnotu true** , pokud je první hodnota větší než nebo rovna druhé hodnotě; v opačném případě **false**.

### <a name="example"></a>Příklad

Následující [příklad šablony](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/greaterorequals.json) kontroluje, zda je jedna hodnota větší než nebo rovna druhému.

# <a name="json"></a>[JSON](#tab/json)

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "firstInt": {
      "type": "int",
      "defaultValue": 1
    },
    "secondInt": {
      "type": "int",
      "defaultValue": 2
    },
    "firstString": {
      "type": "string",
      "defaultValue": "A"
    },
    "secondString": {
      "type": "string",
      "defaultValue": "a"
    }
  },
  "resources": [
  ],
  "outputs": {
    "checkInts": {
      "type": "bool",
      "value": "[greaterOrEquals(parameters('firstInt'), parameters('secondInt') )]"
    },
    "checkStrings": {
      "type": "bool",
      "value": "[greaterOrEquals(parameters('firstString'), parameters('secondString'))]"
    }
  }
}
```

# <a name="bicep"></a>[Bicep](#tab/bicep)

```bicep
param firstInt int = 1
param secondInt int = 2
param firstString string = 'A'
param secondString string = 'a'

output checkInts bool = firstInt >= secondInt
output checkStrings bool = firstString >= secondString
```

---

Výstup z předchozího příkladu s výchozími hodnotami je:

| Název | Typ | Hodnota |
| ---- | ---- | ----- |
| checkInts | Logická hodnota | Ne |
| checkStrings | Logická hodnota | Ano |

## <a name="less"></a>less

`less(arg1, arg2)`

Kontroluje, zda je první hodnota menší než druhá hodnota. `less`Funkce není v bicep podporována. `<`Místo toho použijte operátor.

### <a name="parameters"></a>Parametry

| Parametr | Povinné | Typ | Description |
|:--- |:--- |:--- |:--- |
| arg1 |Yes |int nebo String |První hodnota pro méně porovnání. |
| arg2 |Yes |int nebo String |Druhá hodnota pro méně porovnání. |

### <a name="return-value"></a>Vrácená hodnota

Vrátí **hodnotu true** , pokud je první hodnota menší než druhá hodnota. v opačném případě **false**.

### <a name="example"></a>Příklad

Následující [příklad šablony](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/less.json) kontroluje, zda je jedna hodnota menší než druhá.

# <a name="json"></a>[JSON](#tab/json)

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "firstInt": {
      "type": "int",
      "defaultValue": 1
    },
    "secondInt": {
      "type": "int",
      "defaultValue": 2
    },
    "firstString": {
      "type": "string",
      "defaultValue": "A"
    },
    "secondString": {
      "type": "string",
      "defaultValue": "a"
    }
  },
  "resources": [
  ],
  "outputs": {
    "checkInts": {
      "type": "bool",
      "value": "[less(parameters('firstInt'), parameters('secondInt') )]"
    },
    "checkStrings": {
      "type": "bool",
      "value": "[less(parameters('firstString'), parameters('secondString'))]"
    }
  }
}
```

# <a name="bicep"></a>[Bicep](#tab/bicep)

```bicep
param firstInt int = 1
param secondInt int = 2
param firstString string = 'A'
param secondString string = 'a'

output checkInts bool = firstInt < secondInt
output checkStrings bool = firstString < secondString
```

---

Výstup z předchozího příkladu s výchozími hodnotami je:

| Název | Typ | Hodnota |
| ---- | ---- | ----- |
| checkInts | Logická hodnota | Ano |
| checkStrings | Logická hodnota | Ne |

## <a name="lessorequals"></a>lessOrEquals

`lessOrEquals(arg1, arg2)`

Kontroluje, zda je první hodnota menší nebo rovna druhé hodnotě. `lessOrEquals`Funkce není v bicep podporována. `<=`Místo toho použijte operátor.

### <a name="parameters"></a>Parametry

| Parametr | Povinné | Typ | Description |
|:--- |:--- |:--- |:--- |
| arg1 |Yes |int nebo String |První hodnota pro porovnání menší nebo rovno. |
| arg2 |Yes |int nebo String |Druhá hodnota pro porovnání menší nebo rovno. |

### <a name="return-value"></a>Vrácená hodnota

Vrátí **hodnotu true** , pokud je první hodnota menší nebo rovna druhé hodnotě; v opačném případě **false**.

### <a name="example"></a>Příklad

Následující [příklad šablony](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/lessorequals.json) kontroluje, zda je jedna hodnota menší nebo rovna druhému.

# <a name="json"></a>[JSON](#tab/json)

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "firstInt": {
      "type": "int",
      "defaultValue": 1
    },
    "secondInt": {
      "type": "int",
      "defaultValue": 2
    },
    "firstString": {
      "type": "string",
      "defaultValue": "A"
    },
    "secondString": {
      "type": "string",
      "defaultValue": "a"
    }
  },
  "resources": [
  ],
  "outputs": {
    "checkInts": {
      "type": "bool",
      "value": "[lessOrEquals(parameters('firstInt'), parameters('secondInt') )]"
    },
    "checkStrings": {
      "type": "bool",
      "value": "[lessOrEquals(parameters('firstString'), parameters('secondString'))]"
    }
  }
}
```

# <a name="bicep"></a>[Bicep](#tab/bicep)

```bicep
param firstInt int = 1
param secondInt int = 2
param firstString string = 'A'
param secondString string = 'a'

output checkInts bool = firstInt <= secondInt
output checkStrings bool = firstString <= secondString
```

---

Výstup z předchozího příkladu s výchozími hodnotami je:

| Název | Typ | Hodnota |
| ---- | ---- | ----- |
| checkInts | Logická hodnota | Ano |
| checkStrings | Logická hodnota | Ne |

## <a name="next-steps"></a>Další kroky

* Popis sekcí v šabloně Azure Resource Manager najdete v tématu [pochopení struktury a syntaxe šablon ARM](template-syntax.md).
