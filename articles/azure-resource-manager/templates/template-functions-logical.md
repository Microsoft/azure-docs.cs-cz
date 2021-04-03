---
title: Funkce šablon – logická
description: Popisuje funkce, které se použijí v šabloně Azure Resource Manager (šablona ARM) k určení logických hodnot.
ms.topic: conceptual
ms.date: 11/18/2020
ms.openlocfilehash: 27d94f10374daf0b9a351469579a5eb659cf5445
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/29/2021
ms.locfileid: "96920465"
---
# <a name="logical-functions-for-arm-templates"></a>Logické funkce pro šablony ARM

Správce prostředků poskytuje několik funkcí pro porovnávání v šabloně Azure Resource Manager (šablona ARM):

* [and](#and)
* [bool](#bool)
* [chybné](#false)
* [if](#if)
* [mění](#not)
* [nebo](#or)
* [podmínka](#true)

[!INCLUDE [Bicep preview](../../../includes/resource-manager-bicep-preview.md)]

## <a name="and"></a>a

`and(arg1, arg2, ...)`

Kontroluje, zda jsou všechny hodnoty parametrů pravdivé. `and`Funkce není v bicep podporována. `&&`Místo toho použijte operátor.

### <a name="parameters"></a>Parametry

| Parametr | Povinné | Typ | Description |
|:--- |:--- |:--- |:--- |
| arg1 |Yes |boolean |První hodnota, která ověří, zda je hodnota true. |
| arg2 |Yes |boolean |Druhá hodnota, která ověří, zda je hodnota true. |
| Další argumenty |No |boolean |Další argumenty pro kontrolu, zda jsou pravdivé. |

### <a name="return-value"></a>Vrácená hodnota

Vrátí **hodnotu true** , pokud jsou všechny hodnoty true; v opačném případě **false**.

### <a name="examples"></a>Příklady

Následující [příklad šablony](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/andornot.json) ukazuje, jak používat logické funkce.

# <a name="json"></a>[JSON](#tab/json)

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "resources": [],
  "outputs": {
    "andExampleOutput": {
      "type": "bool",
      "value": "[and(bool('true'), bool('false'))]"
    },
    "orExampleOutput": {
      "type": "bool",
      "value": "[or(bool('true'), bool('false'))]"
    },
    "notExampleOutput": {
      "type": "bool",
      "value": "[not(bool('true'))]"
    }
  }
}
```

# <a name="bicep"></a>[Bicep](#tab/bicep)

```bicep
output andExampleOutput bool = bool('true') && bool('false')
output orExampleOutput bool = bool('true') || bool('false')
output notExampleOutput bool = !(bool('true'))
```

---

Výstup z předchozího příkladu:

| Název | Typ | Hodnota |
| ---- | ---- | ----- |
| andExampleOutput | Logická hodnota | Ne |
| orExampleOutput | Logická hodnota | Ano |
| notExampleOutput | Logická hodnota | Ne |

## <a name="bool"></a>bool

`bool(arg1)`

Převede parametr na logickou hodnotu.

### <a name="parameters"></a>Parametry

| Parametr | Povinné | Typ | Description |
|:--- |:--- |:--- |:--- |
| arg1 |Yes |řetězec nebo int |Hodnota, která má být převedena na logickou hodnotu. |

### <a name="return-value"></a>Vrácená hodnota

Logická hodnota převedené hodnoty.

### <a name="remarks"></a>Poznámky

K získání logických hodnot můžete použít také hodnoty [true ()](#true) a [false ()](#false) .

### <a name="examples"></a>Příklady

Následující [příklad šablony](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/bool.json) ukazuje, jak použít bool s řetězcem nebo celým číslem.

# <a name="json"></a>[JSON](#tab/json)

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "resources": [],
  "outputs": {
    "trueString": {
      "type": "bool",
      "value": "[bool('true')]",
    },
    "falseString": {
      "type": "bool",
      "value": "[bool('false')]"
    },
    "trueInt": {
      "type": "bool",
      "value": "[bool(1)]"
    },
    "falseInt": {
      "type": "bool",
      "value": "[bool(0)]"
    }
  }
}
```

# <a name="bicep"></a>[Bicep](#tab/bicep)

```bicep
output trueString bool = bool('true')
output falseString bool = bool('false')
output trueInt bool = bool(1)
output falseInt bool = bool(0)
```

---
Výstup z předchozího příkladu s výchozími hodnotami je:

| Název | Typ | Hodnota |
| ---- | ---- | ----- |
| trueString | Logická hodnota | Ano |
| falseString | Logická hodnota | Ne |
| trueInt | Logická hodnota | Ano |
| falseInt | Logická hodnota | Ne |

## <a name="false"></a>false (nepravda)

`false()`

Vrátí hodnotu false. `false`Funkce není v bicep k dispozici.  `false`Místo toho použijte klíčové slovo.

### <a name="parameters"></a>Parametry

Funkce false nepřijímá žádné parametry.

### <a name="return-value"></a>Vrácená hodnota

Logická hodnota, která je vždy false.

### <a name="example"></a>Příklad

Následující příklad vrátí falešnou výstupní hodnotu.

# <a name="json"></a>[JSON](#tab/json)

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "resources": [],
  "outputs": {
    "falseOutput": {
      "type": "bool",
      "value": "[false()]"
    }
  }
}
```

# <a name="bicep"></a>[Bicep](#tab/bicep)

```bicep
output falseOutput bool = false
```

---

Výstup z předchozího příkladu:

| Název | Typ | Hodnota |
| ---- | ---- | ----- |
| falseOutput | Logická hodnota | Ne |

## <a name="if"></a>if

`if(condition, trueValue, falseValue)`

Vrátí hodnotu na základě toho, zda je podmínka pravdivá, nebo false. `if`Funkce není v bicep podporována. `?:`Místo toho použijte operátor.

### <a name="parameters"></a>Parametry

| Parametr | Povinné | Typ | Description |
|:--- |:--- |:--- |:--- |
| pomocné |Yes |boolean |Hodnota, která ověří, zda je true nebo false. |
| trueValue |Yes | řetězec, int, objekt nebo pole |Hodnota, která se má vrátit, pokud je podmínka pravdivá. |
| falseValue |Yes | řetězec, int, objekt nebo pole |Hodnota, která se má vrátit, pokud je podmínka nepravdivá |

### <a name="return-value"></a>Vrácená hodnota

Vrací druhý parametr, pokud je první parametr **true**; v opačném případě vrátí třetí parametr.

### <a name="remarks"></a>Poznámky

Pokud je podmínka **pravdivá**, vyhodnotí se jenom skutečná hodnota. Pokud je podmínka **nepravdivá**, vyhodnotí se jenom hodnota false. Pomocí `if` funkce můžete zahrnout výrazy, které jsou pouze podmíněně platné. Můžete například vytvořit odkaz na prostředek, který existuje v rámci jedné podmínky, ale ne pod druhou podmínkou. Příklad podmíněného vyhodnocování výrazů je uveden v následující části.

### <a name="examples"></a>Příklady

Následující [příklad šablony](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/if.json) ukazuje, jak používat `if` funkci.

# <a name="json"></a>[JSON](#tab/json)

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "resources": [
  ],
  "outputs": {
    "yesOutput": {
      "type": "string",
      "value": "[if(equals('a', 'a'), 'yes', 'no')]"
    },
    "noOutput": {
      "type": "string",
      "value": "[if(equals('a', 'b'), 'yes', 'no')]"
    },
    "objectOutput": {
      "type": "object",
      "value": "[if(equals('a', 'a'), json('{\"test\": \"value1\"}'), json('null'))]"
    }
  }
}
```

# <a name="bicep"></a>[Bicep](#tab/bicep)

```bicep
output yesOutput string = 'a' == 'a' ? 'yes' : 'no'
output noOutput string = 'a' == 'b' ? 'yes' : 'no'
output objectOutput object = 'a' == 'a' ? json('{"test": "value1"}') : json('null')
```

---

Výstup z předchozího příkladu:

| Název | Typ | Hodnota |
| ---- | ---- | ----- |
| yesOutput | Řetězec | ano |
| Výstup | Řetězec | ne |
| objectOutput | Objekt | {"test": "hodnota1"} |

Následující [příklad šablony](https://github.com/krnese/AzureDeploy/blob/master/ARM/deployments/conditionWithReference.json) ukazuje, jak použít tuto funkci s výrazy, které jsou pouze podmíněně platné.

# <a name="json"></a>[JSON](#tab/json)

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "vmName": {
      "type": "string"
    },
    "location": {
      "type": "string"
    },
    "logAnalytics": {
      "type": "string",
      "defaultValue": ""
    }
  },
  "resources": [
    {
      "condition": "[not(empty(parameters('logAnalytics')))]",
      "type": "Microsoft.Compute/virtualMachines/extensions",
      "apiVersion": "2017-03-30",
      "name": "[concat(parameters('vmName'),'/omsOnboarding')]",
      "location": "[parameters('location')]",
      "properties": {
        "publisher": "Microsoft.EnterpriseCloud.Monitoring",
        "type": "MicrosoftMonitoringAgent",
        "typeHandlerVersion": "1.0",
        "autoUpgradeMinorVersion": true,
        "settings": {
          "workspaceId": "[if(not(empty(parameters('logAnalytics'))), reference(parameters('logAnalytics'), '2015-11-01-preview').customerId, json('null'))]"
        },
        "protectedSettings": {
          "workspaceKey": "[if(not(empty(parameters('logAnalytics'))), listKeys(parameters('logAnalytics'), '2015-11-01-preview').primarySharedKey, json('null'))]"
        }
      }
    }
  ],
  "outputs": {
    "mgmtStatus": {
      "type": "string",
      "value": "[if(not(empty(parameters('logAnalytics'))), 'Enabled monitoring for VM!', 'Nothing to enable')]"
    }
  }
}
```

# <a name="bicep"></a>[Bicep](#tab/bicep)

> [!NOTE]
> `Conditions` ještě nejsou v bicep implementované. Viz [podmínky](https://github.com/Azure/bicep/issues/186).

---

## <a name="not"></a>not

`not(arg1)`

Převede logickou hodnotu na její opačnou hodnotu. `not`Funkce není v bicep podporována. `!`Místo toho použijte operátor.

### <a name="parameters"></a>Parametry

| Parametr | Povinné | Typ | Description |
|:--- |:--- |:--- |:--- |
| arg1 |Yes |boolean |Hodnota, kterou chcete převést. |

### <a name="return-value"></a>Vrácená hodnota

Vrátí **hodnotu pravda** , pokud má parametr **hodnotu false**. Vrátí **hodnotu false** , pokud má parametr **hodnotu true**.

### <a name="examples"></a>Příklady

Následující [příklad šablony](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/andornot.json) ukazuje, jak používat logické funkce.

# <a name="json"></a>[JSON](#tab/json)

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "resources": [],
  "outputs": {
    "andExampleOutput": {
      "type": "bool",
      "value": "[and(bool('true'), bool('false'))]",
    },
    "orExampleOutput": {
      "type": "bool",
      "value": "[or(bool('true'), bool('false'))]"
    },
    "notExampleOutput": {
      "type": "bool",
      "value": "[not(bool('true'))]"
    }
  }
}
```

# <a name="bicep"></a>[Bicep](#tab/bicep)

```bicep
output andExampleOutput bool = bool('true') && bool('false')
output orExampleOutput bool = bool('true') || bool('false')
output notExampleOutput bool = !(bool('true'))
```

---

Výstup z předchozího příkladu:

| Název | Typ | Hodnota |
| ---- | ---- | ----- |
| andExampleOutput | Logická hodnota | Ne |
| orExampleOutput | Logická hodnota | Ano |
| notExampleOutput | Logická hodnota | Ne |

Následující [příklad šablony](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/not-equals.json) používá znaménko **Not** a [Equals](template-functions-comparison.md#equals).

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
output checkNotEquals bool = !(1 == 2)
```

---

Výstup z předchozího příkladu:

| Název | Typ | Hodnota |
| ---- | ---- | ----- |
| checkNotEquals | Logická hodnota | Ano |

## <a name="or"></a>nebo

`or(arg1, arg2, ...)`

Kontroluje, zda je hodnota parametru pravdivá. `or`Funkce není v bicep podporována. `||`Místo toho použijte operátor.

### <a name="parameters"></a>Parametry

| Parametr | Povinné | Typ | Description |
|:--- |:--- |:--- |:--- |
| arg1 |Yes |boolean |První hodnota, která ověří, zda je hodnota true. |
| arg2 |Yes |boolean |Druhá hodnota, která ověří, zda je hodnota true. |
| Další argumenty |No |boolean |Další argumenty pro kontrolu, zda jsou pravdivé. |

### <a name="return-value"></a>Vrácená hodnota

Vrátí **hodnotu true** , pokud je libovolná hodnota true; v opačném případě **false**.

### <a name="examples"></a>Příklady

Následující [příklad šablony](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/andornot.json) ukazuje, jak používat logické funkce.

# <a name="json"></a>[JSON](#tab/json)

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "resources": [],
  "outputs": {
    "andExampleOutput": {
      "value": "[and(bool('true'), bool('false'))]",
      "type": "bool"
    },
    "orExampleOutput": {
      "value": "[or(bool('true'), bool('false'))]",
      "type": "bool"
    },
    "notExampleOutput": {
      "value": "[not(bool('true'))]",
      "type": "bool"
    }
  }
}
```

# <a name="bicep"></a>[Bicep](#tab/bicep)

```bicep
output andExampleOutput bool = bool('true') && bool('false')
output orExampleOutput bool = bool('true') || bool('false')
output notExampleOutput bool = !(bool('true'))
```

---

Výstup z předchozího příkladu:

| Název | Typ | Hodnota |
| ---- | ---- | ----- |
| andExampleOutput | Logická hodnota | Ne |
| orExampleOutput | Logická hodnota | Ano |
| notExampleOutput | Logická hodnota | Ne |

## <a name="true"></a>true

`true()`

Vrátí hodnotu true. `true`Funkce není v bicep k dispozici.  `true`Místo toho použijte klíčové slovo.

### <a name="parameters"></a>Parametry

Funkce true nepřijímá žádné parametry. `true`Funkce není v bicep k dispozici.  `true`Místo toho použijte klíčové slovo.

### <a name="return-value"></a>Vrácená hodnota

Logická hodnota, která je vždycky true.

### <a name="example"></a>Příklad

Následující příklad vrátí hodnotu true Output.

# <a name="json"></a>[JSON](#tab/json)

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "resources": [],
  "outputs": {
    "trueOutput": {
      "type": "bool",
      "value": "[true()]"
    }
  }
}
```

# <a name="bicep"></a>[Bicep](#tab/bicep)

```bicep
output trueOutput bool = true
```

---

Výstup z předchozího příkladu:

| Název | Typ | Hodnota |
| ---- | ---- | ----- |
| trueOutput | Logická hodnota | Ano |

## <a name="next-steps"></a>Další kroky

* Popis sekcí v šabloně ARM najdete v tématu [pochopení struktury a syntaxe šablon ARM](template-syntax.md).
