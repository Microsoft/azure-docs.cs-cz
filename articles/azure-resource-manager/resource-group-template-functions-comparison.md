---
title: Funkce šablon Azure Resource Manageru – porovnání | Dokumentace Microsoftu
description: Popisuje funkce pro použití v šabloně Azure Resource Manageru pro porovnání hodnot.
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
ms.openlocfilehash: 6591a75577670f5bb50c1275af7e51e9b8328d50
ms.sourcegitcommit: 5978d82c619762ac05b19668379a37a40ba5755b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/31/2019
ms.locfileid: "55492850"
---
# <a name="comparison-functions-for-azure-resource-manager-templates"></a>Porovnání funkcí šablon Azure Resource Manageru

Resource Manager poskytuje několik funkcí pro provádění porovnání v šablonách.

* [rovná se](#equals)
* [greater](#greater)
* [greaterOrEquals](#greaterorequals)
* [méně](#less)
* [lessOrEquals](#lessorequals)

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="equals"></a>rovná se
`equals(arg1, arg2)`

Kontroluje, zda dvě hodnoty rovnají mezi sebou.

### <a name="parameters"></a>Parametry

| Parametr | Požaduje se | Typ | Popis |
|:--- |:--- |:--- |:--- |
| arg1 |Ano |int, string, pole nebo objekt |První hodnota ke kontrole rovnosti. |
| arg2 |Ano |int, string, pole nebo objekt |Druhá hodnota pro kontrolu rovnosti. |

### <a name="return-value"></a>Návratová hodnota

Vrátí **True** Pokud jsou hodnoty stejné, jinak **False**.

### <a name="remarks"></a>Poznámky

Funkce rovná se často používá s `condition` prvek, který chcete otestovat, jestli je prostředek nasazený.

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

### <a name="example"></a>Příklad:

Následující [Ukázková šablona](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/equals.json) kontroluje různého typu hodnoty na rovnost. Všechny výchozí hodnoty vrátí hodnotu True.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
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
            "defaultValue": ["a", "b"]
        },
        "secondArray": {
            "type": "array",
            "defaultValue": ["a", "b"]
        },
        "firstObject": {
            "type": "object",
            "defaultValue": {"a": "b"}
        },
        "secondObject": {
            "type": "object",
            "defaultValue": {"a": "b"}
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

Výstup z předchozího příkladu s výchozími hodnotami je:

| Název | Typ | Hodnota |
| ---- | ---- | ----- |
| checkInts | Bool | True |
| checkStrings | Bool | True |
| checkArrays | Bool | True |
| checkObjects | Bool | True |

Pokud chcete nasadit šablonu tento příklad pomocí Azure CLI, použijte:

```azurecli-interactive
az group deployment create -g functionexamplegroup --template-uri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/functions/equals.json
```

Pokud chcete nasadit tento příklad šablony pomocí prostředí PowerShell, použijte:

```powershell
New-AzResourceGroupDeployment -ResourceGroupName functionexamplegroup -TemplateUri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/functions/equals.json 
```

Následující [Ukázková šablona](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/not-equals.json) používá [není](resource-group-template-functions-logical.md#not) s **rovná**.

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
}
```

Výstup z předchozího příkladu je:

| Název | Typ | Hodnota |
| ---- | ---- | ----- |
| checkNotEquals | Bool | True |

Pokud chcete nasadit šablonu tento příklad pomocí Azure CLI, použijte:

```azurecli-interactive
az group deployment create -g functionexamplegroup --template-uri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/functions/not-equals.json
```

Pokud chcete nasadit tento příklad šablony pomocí prostředí PowerShell, použijte:

```powershell
New-AzResourceGroupDeployment -ResourceGroupName functionexamplegroup -TemplateUri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/functions/not-equals.json 
```

## <a name="greater"></a>větší
`greater(arg1, arg2)`

Kontroluje, zda je první hodnota větší než druhá hodnota.

### <a name="parameters"></a>Parametry

| Parametr | Požaduje se | Typ | Popis |
|:--- |:--- |:--- |:--- |
| arg1 |Ano |int nebo string |První hodnota pro porovnání větší. |
| arg2 |Ano |int nebo string |Druhá hodnota pro porovnání větší. |

### <a name="return-value"></a>Návratová hodnota

Vrátí **True** Pokud první hodnota je větší než druhá hodnota; v opačném případě **False**.

### <a name="example"></a>Příklad:

Následující [Ukázková šablona](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/greater.json) zkontroluje, jestli jedna hodnota je větší než druhý.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
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

Výstup z předchozího příkladu s výchozími hodnotami je:

| Název | Typ | Hodnota |
| ---- | ---- | ----- |
| checkInts | Bool | False |
| checkStrings | Bool | True |

Pokud chcete nasadit šablonu tento příklad pomocí Azure CLI, použijte:

```azurecli-interactive
az group deployment create -g functionexamplegroup --template-uri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/functions/greater.json
```

Pokud chcete nasadit tento příklad šablony pomocí prostředí PowerShell, použijte:

```powershell
New-AzResourceGroupDeployment -ResourceGroupName functionexamplegroup -TemplateUri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/functions/greater.json 
```

## <a name="greaterorequals"></a>greaterOrEquals
`greaterOrEquals(arg1, arg2)`

Kontroluje, zda je první hodnota větší než nebo rovna hodnotě druhá hodnota.

### <a name="parameters"></a>Parametry

| Parametr | Požaduje se | Typ | Popis |
|:--- |:--- |:--- |:--- |
| arg1 |Ano |int nebo string |První hodnota pro porovnání větší nebo rovno. |
| arg2 |Ano |int nebo string |Druhá hodnota pro porovnání větší nebo rovno. |

### <a name="return-value"></a>Návratová hodnota

Vrátí **True** Pokud první hodnota je větší než nebo rovna hodnotě druhý; v opačném případě **False**.

### <a name="example"></a>Příklad:

Následující [Ukázková šablona](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/greaterorequals.json) zkontroluje, jestli jedna hodnota je větší než nebo rovna hodnotě druhé.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
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

Výstup z předchozího příkladu s výchozími hodnotami je:

| Název | Typ | Hodnota |
| ---- | ---- | ----- |
| checkInts | Bool | False |
| checkStrings | Bool | True |

Pokud chcete nasadit šablonu tento příklad pomocí Azure CLI, použijte:

```azurecli-interactive
az group deployment create -g functionexamplegroup --template-uri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/functions/greaterorequals.json
```

Pokud chcete nasadit tento příklad šablony pomocí prostředí PowerShell, použijte:

```powershell
New-AzResourceGroupDeployment -ResourceGroupName functionexamplegroup -TemplateUri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/functions/greaterorequals.json 
```

## <a name="less"></a>méně
`less(arg1, arg2)`

Kontroluje, zda je první hodnota menší než druhá hodnota.

### <a name="parameters"></a>Parametry

| Parametr | Požaduje se | Typ | Popis |
|:--- |:--- |:--- |:--- |
| arg1 |Ano |int nebo string |První hodnota menší porovnání. |
| arg2 |Ano |int nebo string |Druhá hodnota pro porovnání méně. |

### <a name="return-value"></a>Návratová hodnota

Vrátí **True** Pokud je první hodnota menší než druhá hodnota; v opačném případě **False**.

### <a name="example"></a>Příklad:

Následující [Ukázková šablona](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/less.json) zkontroluje, jestli jedna hodnota je menší než ten druhý.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
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

Výstup z předchozího příkladu s výchozími hodnotami je:

| Název | Typ | Hodnota |
| ---- | ---- | ----- |
| checkInts | Bool | True |
| checkStrings | Bool | False |

Pokud chcete nasadit šablonu tento příklad pomocí Azure CLI, použijte:

```azurecli-interactive
az group deployment create -g functionexamplegroup --template-uri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/functions/less.json
```

Pokud chcete nasadit tento příklad šablony pomocí prostředí PowerShell, použijte:

```powershell
New-AzResourceGroupDeployment -ResourceGroupName functionexamplegroup -TemplateUri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/functions/less.json 
```

## <a name="lessorequals"></a>lessOrEquals
`lessOrEquals(arg1, arg2)`

Kontroluje, zda je první hodnota menší než druhá hodnota.

### <a name="parameters"></a>Parametry

| Parametr | Požaduje se | Typ | Popis |
|:--- |:--- |:--- |:--- |
| arg1 |Ano |int nebo string |První hodnota je menší nebo rovná porovnání. |
| arg2 |Ano |int nebo string |Druhá hodnota pro menší nebo rovná porovnání. |

### <a name="return-value"></a>Návratová hodnota

Vrátí **True** Pokud první hodnota je menší než nebo rovna hodnotě druhý; v opačném případě **False**.

### <a name="example"></a>Příklad:

Následující [Ukázková šablona](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/lessorequals.json) zkontroluje, jestli jedna hodnota je menší nebo rovna na druhý.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
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

Výstup z předchozího příkladu s výchozími hodnotami je:

| Název | Typ | Hodnota |
| ---- | ---- | ----- |
| checkInts | Bool | True |
| checkStrings | Bool | False |

Pokud chcete nasadit šablonu tento příklad pomocí Azure CLI, použijte:

```azurecli-interactive
az group deployment create -g functionexamplegroup --template-uri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/functions/lessorequals.json
```

Pokud chcete nasadit tento příklad šablony pomocí prostředí PowerShell, použijte:

```powershell
New-AzResourceGroupDeployment -ResourceGroupName functionexamplegroup -TemplateUri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/functions/lessorequals.json 
```

## <a name="next-steps"></a>Další postup
* Popis části šablony Azure Resource Manageru najdete v tématu [šablon pro vytváření Azure Resource Manageru](resource-group-authoring-templates.md).
* Chcete-li sloučit několik šablon, přečtěte si téma [použití propojených šablon s Azure Resource Managerem](resource-group-linked-templates.md).
* K iteraci zadaného počtu opakování při vytváření konkrétní typ prostředku, naleznete v tématu [vytvořit více instancí prostředku v Azure Resource Manageru](resource-group-create-multiple.md).
* Postup nasazení šablony, které jste vytvořili, najdete v sekci [nasazení aplikace pomocí šablony Azure Resource Manageru](resource-group-template-deploy.md).

