---
title: Funkce šablon – logická
description: Popisuje funkce, které lze použít v šabloně Azure Resource Manager k určení logických hodnot.
ms.topic: conceptual
ms.date: 04/27/2020
ms.openlocfilehash: 8fe1c00240fc24c3c1454b118f9e0d9a9d54fe4e
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "84677385"
---
# <a name="logical-functions-for-arm-templates"></a>Logické funkce pro šablony ARM

Správce prostředků poskytuje několik funkcí pro porovnávání šablon Azure Resource Manager (ARM).

* [and](#and)
* [bool](#bool)
* [if](#if)
* [mění](#not)
* [nebo](#or)

## <a name="and"></a>a

`and(arg1, arg2, ...)`

Kontroluje, zda jsou všechny hodnoty parametrů pravdivé.

### <a name="parameters"></a>Parametry

| Parametr | Požaduje se | Typ | Description |
|:--- |:--- |:--- |:--- |
| arg1 |Yes |boolean |První hodnota, která ověří, zda je hodnota true. |
| arg2 |Yes |boolean |Druhá hodnota, která ověří, zda je hodnota true. |
| Další argumenty |No |boolean |Další argumenty pro kontrolu, zda jsou pravdivé. |

### <a name="return-value"></a>Vrácená hodnota

Vrátí **hodnotu true** , pokud jsou všechny hodnoty true; v opačném případě **false**.

### <a name="examples"></a>Příklady

Následující [příklad šablony](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/andornot.json) ukazuje, jak používat logické funkce.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "resources": [ ],
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

Výstup z předchozího příkladu:

| Název | Typ | Hodnota |
| ---- | ---- | ----- |
| andExampleOutput | Logická hodnota | Nepravda |
| orExampleOutput | Logická hodnota | Ano |
| notExampleOutput | Logická hodnota | Nepravda |

## <a name="bool"></a>bool

`bool(arg1)`

Převede parametr na logickou hodnotu.

### <a name="parameters"></a>Parametry

| Parametr | Požaduje se | Typ | Description |
|:--- |:--- |:--- |:--- |
| arg1 |Yes |řetězec nebo int |Hodnota, která má být převedena na logickou hodnotu. |

### <a name="return-value"></a>Vrácená hodnota
Logická hodnota převedené hodnoty.

### <a name="examples"></a>Příklady

Následující [příklad šablony](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/bool.json) ukazuje, jak použít bool s řetězcem nebo celým číslem.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "resources": [],
    "outputs": {
        "trueString": {
            "value": "[bool('true')]",
            "type" : "bool"
        },
        "falseString": {
            "value": "[bool('false')]",
            "type" : "bool"
        },
        "trueInt": {
            "value": "[bool(1)]",
            "type" : "bool"
        },
        "falseInt": {
            "value": "[bool(0)]",
            "type" : "bool"
        }
    }
}
```

Výstup z předchozího příkladu s výchozími hodnotami je:

| Název | Typ | Hodnota |
| ---- | ---- | ----- |
| trueString | Logická hodnota | Ano |
| falseString | Logická hodnota | Nepravda |
| trueInt | Logická hodnota | Ano |
| falseInt | Logická hodnota | Nepravda |

## <a name="if"></a>if

`if(condition, trueValue, falseValue)`

Vrátí hodnotu na základě toho, zda je podmínka pravdivá, nebo false.

### <a name="parameters"></a>Parametry

| Parametr | Požaduje se | Typ | Description |
|:--- |:--- |:--- |:--- |
| pomocné |Yes |boolean |Hodnota, která ověří, zda je true nebo false. |
| trueValue |Yes | řetězec, int, objekt nebo pole |Hodnota, která se má vrátit, pokud je podmínka pravdivá. |
| falseValue |Yes | řetězec, int, objekt nebo pole |Hodnota, která se má vrátit, pokud je podmínka nepravdivá |

### <a name="return-value"></a>Vrácená hodnota

Vrací druhý parametr, pokud je první parametr **true**; v opačném případě vrátí třetí parametr.

### <a name="remarks"></a>Poznámky

Pokud je podmínka **pravdivá**, vyhodnotí se jenom skutečná hodnota. Pokud je podmínka **nepravdivá**, vyhodnotí se jenom hodnota false. Pomocí funkce **if** můžete zahrnout výrazy, které jsou pouze podmíněně platné. Můžete například vytvořit odkaz na prostředek, který existuje v rámci jedné podmínky, ale ne pod druhou podmínkou. Příklad podmíněného vyhodnocování výrazů je uveden v následující části.

### <a name="examples"></a>Příklady

Následující [příklad šablony](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/if.json) ukazuje, jak používat `if` funkci.

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

Výstup z předchozího příkladu:

| Název | Typ | Hodnota |
| ---- | ---- | ----- |
| yesOutput | Řetězec | ano |
| Výstup | Řetězec | ne |
| objectOutput | Objekt | {"test": "hodnota1"} |

Následující [příklad šablony](https://github.com/krnese/AzureDeploy/blob/master/ARM/deployments/conditionWithReference.json) ukazuje, jak použít tuto funkci s výrazy, které jsou pouze podmíněně platné.

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
            "name": "[concat(parameters('vmName'),'/omsOnboarding')]",
            "type": "Microsoft.Compute/virtualMachines/extensions",
            "location": "[parameters('location')]",
            "apiVersion": "2017-03-30",
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

## <a name="not"></a>not

`not(arg1)`

Převede logickou hodnotu na její opačnou hodnotu.

### <a name="parameters"></a>Parametry

| Parametr | Požaduje se | Typ | Description |
|:--- |:--- |:--- |:--- |
| arg1 |Yes |boolean |Hodnota, kterou chcete převést. |

### <a name="return-value"></a>Vrácená hodnota

Vrátí **hodnotu pravda** , pokud má parametr **hodnotu false**. Vrátí **hodnotu false** , pokud má parametr **hodnotu true**.

### <a name="examples"></a>Příklady

Následující [příklad šablony](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/andornot.json) ukazuje, jak používat logické funkce.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "resources": [ ],
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

Výstup z předchozího příkladu:

| Název | Typ | Hodnota |
| ---- | ---- | ----- |
| andExampleOutput | Logická hodnota | Nepravda |
| orExampleOutput | Logická hodnota | Ano |
| notExampleOutput | Logická hodnota | Nepravda |

Následující [příklad šablony](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/not-equals.json) používá znaménko **Not** a [Equals](template-functions-comparison.md#equals).

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

Výstup z předchozího příkladu:

| Název | Typ | Hodnota |
| ---- | ---- | ----- |
| checkNotEquals | Logická hodnota | Ano |

## <a name="or"></a>nebo

`or(arg1, arg2, ...)`

Kontroluje, zda je hodnota parametru pravdivá.

### <a name="parameters"></a>Parametry

| Parametr | Požaduje se | Typ | Description |
|:--- |:--- |:--- |:--- |
| arg1 |Yes |boolean |První hodnota, která ověří, zda je hodnota true. |
| arg2 |Yes |boolean |Druhá hodnota, která ověří, zda je hodnota true. |
| Další argumenty |No |boolean |Další argumenty pro kontrolu, zda jsou pravdivé. |

### <a name="return-value"></a>Vrácená hodnota

Vrátí **hodnotu true** , pokud je libovolná hodnota true; v opačném případě **false**.

### <a name="examples"></a>Příklady

Následující [příklad šablony](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/andornot.json) ukazuje, jak používat logické funkce.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "resources": [ ],
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

Výstup z předchozího příkladu:

| Název | Typ | Hodnota |
| ---- | ---- | ----- |
| andExampleOutput | Logická hodnota | Nepravda |
| orExampleOutput | Logická hodnota | Ano |
| notExampleOutput | Logická hodnota | Nepravda |

## <a name="next-steps"></a>Další kroky

* Popis sekcí v šabloně Azure Resource Manager najdete v tématu [pochopení struktury a syntaxe šablon ARM](template-syntax.md).

