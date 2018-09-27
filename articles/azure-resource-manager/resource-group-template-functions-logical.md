---
title: Azure Resource Manageru šablony funkce – logické | Dokumentace Microsoftu
description: Popisuje funkce pro použití v šabloně Azure Resource Manageru k určení logické hodnoty.
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
ms.openlocfilehash: 8745519f1a0fdda7a5feb6ffb3f61e5250bb260a
ms.sourcegitcommit: 51a1476c85ca518a6d8b4cc35aed7a76b33e130f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/25/2018
ms.locfileid: "47164783"
---
# <a name="logical-functions-for-azure-resource-manager-templates"></a>Logické funkce pro šablony Azure Resource Manageru

Resource Manager poskytuje několik funkcí pro provádění porovnání v šablonách.

* [a](#and)
* [BOOL](#bool)
* [if](#if)
* [Not](#not)
* [Nebo](#or)

## <a name="and"></a>a
`and(arg1, arg2)`

Kontroluje, zda jsou splněny obě hodnoty parametru.

### <a name="parameters"></a>Parametry

| Parametr | Požaduje se | Typ | Popis |
|:--- |:--- |:--- |:--- |
| arg1 |Ano |Boolean |První hodnota ke kontrole, jestli má hodnotu true. |
| arg2 |Ano |Boolean |Druhá hodnota ke kontrole, jestli má hodnotu true. |

### <a name="return-value"></a>Návratová hodnota

Vrátí **True** Pokud jsou obě hodnoty true; v opačném případě **False**.

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
| andExampleOutput | BOOL | False |
| orExampleOutput | BOOL | True |
| notExampleOutput | BOOL | False |

Pokud chcete nasadit šablonu tento příklad pomocí Azure CLI, použijte:

```azurecli-interactive
az group deployment create -g functionexamplegroup --template-uri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/functions/andornot.json
```

Pokud chcete nasadit tento příklad šablony pomocí prostředí PowerShell, použijte:

```powershell
New-AzureRmResourceGroupDeployment -ResourceGroupName functionexamplegroup -TemplateUri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/functions/andornot.json
```

## <a name="bool"></a>BOOL
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
| trueString | BOOL | True |
| falseString | BOOL | False |
| trueInt | BOOL | True |
| falseInt | BOOL | False |

Pokud chcete nasadit šablonu tento příklad pomocí Azure CLI, použijte:

```azurecli-interactive
az group deployment create -g functionexamplegroup --template-uri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/functions/bool.json
```

Pokud chcete nasadit tento příklad šablony pomocí prostředí PowerShell, použijte:

```powershell
New-AzureRmResourceGroupDeployment -ResourceGroupName functionexamplegroup -TemplateUri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/functions/bool.json
```

## <a name="if"></a>If
`if(condition, trueValue, falseValue)`

Vrátí hodnotu podle toho, jestli je podmínka true nebo false.

### <a name="parameters"></a>Parametry

| Parametr | Požaduje se | Typ | Popis |
|:--- |:--- |:--- |:--- |
| condition |Ano |Boolean |Hodnota ke kontrole, jestli je hodnota true. |
| trueValue |Ano | řetězec, int, objekt nebo pole |Hodnota má vrátit, pokud je podmínka pravdivá. |
| falseValue |Ano | řetězec, int, objekt nebo pole |Hodnota má vrátit, pokud podmínka není splněna. |

### <a name="return-value"></a>Návratová hodnota

Vrátí druhý parametr, pokud je první parametr **True**; v opačném případě vrátí třetí parametr.

### <a name="remarks"></a>Poznámky

Tato funkce slouží k podmíněně nastavit vlastnost prostředku. V následujícím příkladu není kompletní šablonu, ale ukazuje příslušné části pro podmíněně nastavení dostupnosti.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        ...
        "availabilitySet": {
            "type": "string",
            "allowedValues": [
                "yes",
                "no"
            ]
        }
    },
    "variables": {
        ...
        "availabilitySetName": "availabilitySet1",
        "availabilitySet": {
            "id": "[resourceId('Microsoft.Compute/availabilitySets',variables('availabilitySetName'))]"
        }
    },
    "resources": [
        {
            "condition": "[equals(parameters('availabilitySet'),'yes')]",
            "type": "Microsoft.Compute/availabilitySets",
            "name": "[variables('availabilitySetName')]",
            ...
        },
        {
            "apiVersion": "2016-03-30",
            "type": "Microsoft.Compute/virtualMachines",
            "properties": {
                "availabilitySet": "[if(equals(parameters('availabilitySet'),'yes'), variables('availabilitySet'), json('null'))]",
                ...
            }
        },
        ...
    ],
    ...
}
```

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
| yesOutput | Řetězec | ano |
| noOutput | Řetězec | ne |
| objectOutput | Objekt | {"test": "hodnota1"} |

Pokud chcete nasadit šablonu tento příklad pomocí Azure CLI, použijte:

```azurecli-interactive
az group deployment create -g functionexamplegroup --template-uri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/functions/if.json
```

Pokud chcete nasadit tento příklad šablony pomocí prostředí PowerShell, použijte:

```powershell
New-AzureRmResourceGroupDeployment -ResourceGroupName functionexamplegroup -TemplateUri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/functions/if.json
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
| andExampleOutput | BOOL | False |
| orExampleOutput | BOOL | True |
| notExampleOutput | BOOL | False |

Pokud chcete nasadit šablonu tento příklad pomocí Azure CLI, použijte:

```azurecli-interactive
az group deployment create -g functionexamplegroup --template-uri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/functions/andornot.json
```

Pokud chcete nasadit tento příklad šablony pomocí prostředí PowerShell, použijte:

```powershell
New-AzureRmResourceGroupDeployment -ResourceGroupName functionexamplegroup -TemplateUri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/functions/andornot.json
```

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
| checkNotEquals | BOOL | True |

Pokud chcete nasadit šablonu tento příklad pomocí Azure CLI, použijte:

```azurecli-interactive
az group deployment create -g functionexamplegroup --template-uri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/functions/not-equals.json
```

Pokud chcete nasadit tento příklad šablony pomocí prostředí PowerShell, použijte:

```powershell
New-AzureRmResourceGroupDeployment -ResourceGroupName functionexamplegroup -TemplateUri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/functions/not-equals.json
```

## <a name="or"></a>nebo
`or(arg1, arg2)`

Kontroluje, zda je buď parametr hodnotu true.

### <a name="parameters"></a>Parametry

| Parametr | Požaduje se | Typ | Popis |
|:--- |:--- |:--- |:--- |
| arg1 |Ano |Boolean |První hodnota ke kontrole, jestli má hodnotu true. |
| arg2 |Ano |Boolean |Druhá hodnota ke kontrole, jestli má hodnotu true. |

### <a name="return-value"></a>Návratová hodnota

Vrátí **True** Pokud buď hodnotu true; v opačném případě **False**.

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
| andExampleOutput | BOOL | False |
| orExampleOutput | BOOL | True |
| notExampleOutput | BOOL | False |

Pokud chcete nasadit šablonu tento příklad pomocí Azure CLI, použijte:

```azurecli-interactive
az group deployment create -g functionexamplegroup --template-uri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/functions/andornot.json
```

Pokud chcete nasadit tento příklad šablony pomocí prostředí PowerShell, použijte:

```powershell
New-AzureRmResourceGroupDeployment -ResourceGroupName functionexamplegroup -TemplateUri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/functions/andornot.json
```

## <a name="next-steps"></a>Další postup
* Popis části šablony Azure Resource Manageru najdete v tématu [šablon pro vytváření Azure Resource Manageru](resource-group-authoring-templates.md).
* Chcete-li sloučit několik šablon, přečtěte si téma [použití propojených šablon s Azure Resource Managerem](resource-group-linked-templates.md).
* K iteraci zadaného počtu opakování při vytváření konkrétní typ prostředku, naleznete v tématu [vytvořit více instancí prostředku v Azure Resource Manageru](resource-group-create-multiple.md).
* Postup nasazení šablony, které jste vytvořili, najdete v sekci [nasazení aplikace pomocí šablony Azure Resource Manageru](resource-group-template-deploy.md).

