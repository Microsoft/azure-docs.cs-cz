---
title: Azure Resource Manageru šablony funkce – logické | Dokumentace Microsoftu
description: Popisuje funkce pro použití v šabloně Azure Resource Manageru k určení logické hodnoty.
services: azure-resource-manager
documentationcenter: na
author: tfitzmac
ms.assetid: ''
ms.service: azure-resource-manager
ms.devlang: na
ms.topic: reference
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 04/15/2019
ms.author: tomfitz
ms.openlocfilehash: 2ccdd337d5c01a0ac0253fe1d1e131fa4e6d51a7
ms.sourcegitcommit: 5f348bf7d6cf8e074576c73055e17d7036982ddb
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/16/2019
ms.locfileid: "59608908"
---
# <a name="logical-functions-for-azure-resource-manager-templates"></a>Logické funkce pro šablony Azure Resource Manageru

Resource Manager poskytuje několik funkcí pro provádění porovnání v šablonách.

* [a](#and)
* [BOOL](#bool)
* [if](#if)
* [Not](#not)
* [nebo](#or)

## <a name="and"></a>a

`and(arg1, arg2, ...)`

Kontroluje, zda jsou splněny všechny hodnoty parametrů.

### <a name="parameters"></a>Parametry

| Parametr | Požaduje se | Typ | Popis |
|:--- |:--- |:--- |:--- |
| arg1 |Ano |Boolean |První hodnota ke kontrole, jestli má hodnotu true. |
| arg2 |Ano |Boolean |Druhá hodnota ke kontrole, jestli má hodnotu true. |
| Další argumenty |Ne |Boolean |Další argumenty, které chcete zkontrolovat, zda jsou splněny. |

### <a name="return-value"></a>Návratová hodnota

Vrátí **True** Pokud jsou všechny hodnoty true; v opačném případě **False**.

### <a name="examples"></a>Příklady

Následující [Ukázková šablona](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/andornot.json) ukazuje, jak pomocí logické funkce.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
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

Výstup z předchozího příkladu je:

| Název | Typ | Hodnota |
| ---- | ---- | ----- |
| andExampleOutput | Bool | False |
| orExampleOutput | Bool | True |
| notExampleOutput | Bool | False |

## <a name="bool"></a>bool

`bool(arg1)`

Převede parametr na logickou hodnotu.

### <a name="parameters"></a>Parametry

| Parametr | Požaduje se | Typ | Popis |
|:--- |:--- |:--- |:--- |
| arg1 |Ano |řetězec nebo int |Hodnota pro převod na logickou hodnotu. |

### <a name="return-value"></a>Návratová hodnota
Logická hodnota převedená hodnota.

### <a name="examples"></a>Příklady

Následující [Ukázková šablona](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/bool.json) ukazuje, jak pomocí řetězce nebo celočíselná bool.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
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
| trueString | Bool | True |
| falseString | Bool | False |
| trueInt | Bool | True |
| falseInt | Bool | False |

## <a name="if"></a>if

`if(condition, trueValue, falseValue)`

Vrátí hodnotu podle toho, jestli je podmínka true nebo false.

### <a name="parameters"></a>Parametry

| Parametr | Požaduje se | Typ | Popis |
|:--- |:--- |:--- |:--- |
| condition |Ano |Boolean |Hodnota ke kontrole, jestli je true nebo false. |
| trueValue |Ano | řetězec, int, objekt nebo pole |Hodnota má vrátit, pokud je podmínka pravdivá. |
| falseValue |Ano | řetězec, int, objekt nebo pole |Hodnota má vrátit, pokud podmínka není splněna. |

### <a name="return-value"></a>Návratová hodnota

Vrátí druhý parametr, pokud je první parametr **True**; v opačném případě vrátí třetí parametr.

### <a name="remarks"></a>Poznámky

Pokud je podmínka **True**, je vyhodnocen pouze hodnotu true. Pokud je podmínka **False**, je vyhodnocen pouze hodnota false. S **Pokud** funkce, může obsahovat výrazy, které jsou pouze podmíněně platné. Například můžete odkazovat na prostředek, který existuje v rámci jedné podmínky, ale nikoli v rámci jiné podmínky. V následující části je uveden příklad podmíněně vyhodnocení výrazů.

### <a name="examples"></a>Příklady

Následující [Ukázková šablona](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/if.json) ukazuje způsob použití `if` funkce.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
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

Výstup z předchozího příkladu je:

| Název | Typ | Hodnota |
| ---- | ---- | ----- |
| yesOutput | String | ano |
| noOutput | String | ne |
| objectOutput | Objekt | {"test": "hodnota1"} |

Následující [Ukázková šablona](https://github.com/krnese/AzureDeploy/blob/master/ARM/deployments/conditionWithReference.json) ukazuje způsob použití této funkce s výrazy, které jsou pouze podmíněně platné.

```json
{
    "$schema": "http://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
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

## <a name="not"></a>ne

`not(arg1)`

Logická hodnota se převede na opačnou hodnotu.

### <a name="parameters"></a>Parametry

| Parametr | Požaduje se | Typ | Popis |
|:--- |:--- |:--- |:--- |
| arg1 |Ano |Boolean |Hodnota k převedení. |

### <a name="return-value"></a>Návratová hodnota

Vrátí **True** po parametru **False**. Vrátí **False** po parametru **True**.

### <a name="examples"></a>Příklady

Následující [Ukázková šablona](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/andornot.json) ukazuje, jak pomocí logické funkce.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
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

Výstup z předchozího příkladu je:

| Název | Typ | Hodnota |
| ---- | ---- | ----- |
| andExampleOutput | Bool | False |
| orExampleOutput | Bool | True |
| notExampleOutput | Bool | False |

Následující [Ukázková šablona](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/not-equals.json) používá **není** s [rovná](resource-group-template-functions-comparison.md#equals).

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "resources": [
    ],
    "outputs": {
        "checkNotEquals": {
            "type": "bool",
            "value": "[not(equals(1, 2))]"
        }
    }
```

Výstup z předchozího příkladu je:

| Název | Typ | Hodnota |
| ---- | ---- | ----- |
| checkNotEquals | Bool | True |

## <a name="or"></a>nebo

`or(arg1, arg2, ...)`

Kontroluje, zda je true kterákoli hodnota parametru.

### <a name="parameters"></a>Parametry

| Parametr | Požaduje se | Typ | Popis |
|:--- |:--- |:--- |:--- |
| arg1 |Ano |Boolean |První hodnota ke kontrole, jestli má hodnotu true. |
| arg2 |Ano |Boolean |Druhá hodnota ke kontrole, jestli má hodnotu true. |
| Další argumenty |Ne |Boolean |Další argumenty, které chcete zkontrolovat, zda jsou splněny. |

### <a name="return-value"></a>Návratová hodnota

Vrátí **True** Pokud libovolná hodnota je true; v opačném případě **False**.

### <a name="examples"></a>Příklady

Následující [Ukázková šablona](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/andornot.json) ukazuje, jak pomocí logické funkce.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
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

Výstup z předchozího příkladu je:

| Název | Typ | Hodnota |
| ---- | ---- | ----- |
| andExampleOutput | Bool | False |
| orExampleOutput | Bool | True |
| notExampleOutput | Bool | False |

## <a name="next-steps"></a>Další postup

* Popis části šablony Azure Resource Manageru najdete v tématu [šablon pro vytváření Azure Resource Manageru](resource-group-authoring-templates.md).
* Chcete-li sloučit několik šablon, přečtěte si téma [použití propojených šablon s Azure Resource Managerem](resource-group-linked-templates.md).
* K iteraci zadaného počtu opakování při vytváření konkrétní typ prostředku, naleznete v tématu [vytvořit více instancí prostředku v Azure Resource Manageru](resource-group-create-multiple.md).
* Postup nasazení šablony, které jste vytvořili, najdete v sekci [nasazení aplikace pomocí šablony Azure Resource Manageru](resource-group-template-deploy.md).

