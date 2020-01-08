---
title: Funkce šablon – porovnání
description: Popisuje funkce, které se použijí v šabloně Azure Resource Manager k porovnání hodnot.
ms.topic: conceptual
ms.date: 09/05/2017
ms.openlocfilehash: 3f21066ae5882f51ef1e01343752eea725fece1d
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/25/2019
ms.locfileid: "75484048"
---
# <a name="comparison-functions-for-azure-resource-manager-templates"></a>Funkce porovnání pro šablony Azure Resource Manager

Správce prostředků poskytuje několik funkcí pro porovnávání v šablonách.

* [equals](#equals)
* [greater](#greater)
* [greaterOrEquals](#greaterorequals)
* [less](#less)
* [lessOrEquals](#lessorequals)

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

## <a name="equals"></a>rovná se
`equals(arg1, arg2)`

Kontroluje, zda jsou dvě hodnoty vzájemně stejné.

### <a name="parameters"></a>Parametry

| Parametr | Požaduje se | Typ | Popis |
|:--- |:--- |:--- |:--- |
| arg1 |Ano |int, string, array nebo object |První hodnota pro kontrolu rovnosti. |
| arg2 |Ano |int, string, array nebo object |Druhá hodnota pro kontrolu rovnosti. |

### <a name="return-value"></a>Návratová hodnota

Vrátí **hodnotu true** , pokud jsou hodnoty stejné; v opačném případě **false**.

### <a name="remarks"></a>Poznámky

Funkce Equals se často používá s prvkem `condition` k otestování, jestli je prostředek nasazený.

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

Následující [příklad šablony](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/equals.json) kontroluje různé typy hodnot pro rovnost. Všechny výchozí hodnoty vrátí hodnotu true.

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

| Name (Název) | Typ | Hodnota |
| ---- | ---- | ----- |
| checkInts | Bool | Pravda |
| checkStrings | Bool | Pravda |
| checkArrays | Bool | Pravda |
| checkObjects | Bool | Pravda |

Pokud chcete nasadit šablonu tento příklad pomocí Azure CLI, použijte:

```azurecli-interactive
az group deployment create -g functionexamplegroup --template-uri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/functions/equals.json
```

Pokud chcete nasadit tento příklad šablony pomocí prostředí PowerShell, použijte:

```powershell
New-AzResourceGroupDeployment -ResourceGroupName functionexamplegroup -TemplateUri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/functions/equals.json 
```

Následující [příklad šablony](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/not-equals.json) používá znaménko [Not](template-functions-logical.md#not) a **Equals**.

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

Výstup z předchozího příkladu:

| Name (Název) | Typ | Hodnota |
| ---- | ---- | ----- |
| checkNotEquals | Bool | Pravda |

Pokud chcete nasadit šablonu tento příklad pomocí Azure CLI, použijte:

```azurecli-interactive
az group deployment create -g functionexamplegroup --template-uri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/functions/not-equals.json
```

Pokud chcete nasadit tento příklad šablony pomocí prostředí PowerShell, použijte:

```powershell
New-AzResourceGroupDeployment -ResourceGroupName functionexamplegroup -TemplateUri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/functions/not-equals.json 
```

## <a name="greater"></a>greater
`greater(arg1, arg2)`

Kontroluje, zda je první hodnota větší než druhá hodnota.

### <a name="parameters"></a>Parametry

| Parametr | Požaduje se | Typ | Popis |
|:--- |:--- |:--- |:--- |
| arg1 |Ano |int nebo String |První hodnota pro lepší porovnání. |
| arg2 |Ano |int nebo String |Druhá hodnota pro lepší porovnání. |

### <a name="return-value"></a>Návratová hodnota

Vrátí **hodnotu true** , pokud je první hodnota větší než druhá hodnota. v opačném případě **false**.

### <a name="example"></a>Příklad:

Následující [příklad šablony](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/greater.json) kontroluje, zda je jedna hodnota větší než druhá.

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

| Name (Název) | Typ | Hodnota |
| ---- | ---- | ----- |
| checkInts | Bool | Nepravda |
| checkStrings | Bool | Pravda |

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

Kontroluje, zda je první hodnota větší než nebo rovna druhé hodnotě.

### <a name="parameters"></a>Parametry

| Parametr | Požaduje se | Typ | Popis |
|:--- |:--- |:--- |:--- |
| arg1 |Ano |int nebo String |První hodnota pro porovnání větší nebo rovno. |
| arg2 |Ano |int nebo String |Druhá hodnota pro vyšší nebo stejné porovnání. |

### <a name="return-value"></a>Návratová hodnota

Vrátí **hodnotu true** , pokud je první hodnota větší než nebo rovna druhé hodnotě; v opačném případě **false**.

### <a name="example"></a>Příklad:

Následující [příklad šablony](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/greaterorequals.json) kontroluje, zda je jedna hodnota větší než nebo rovna druhému.

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

| Name (Název) | Typ | Hodnota |
| ---- | ---- | ----- |
| checkInts | Bool | Nepravda |
| checkStrings | Bool | Pravda |

Pokud chcete nasadit šablonu tento příklad pomocí Azure CLI, použijte:

```azurecli-interactive
az group deployment create -g functionexamplegroup --template-uri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/functions/greaterorequals.json
```

Pokud chcete nasadit tento příklad šablony pomocí prostředí PowerShell, použijte:

```powershell
New-AzResourceGroupDeployment -ResourceGroupName functionexamplegroup -TemplateUri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/functions/greaterorequals.json 
```

## <a name="less"></a>less
`less(arg1, arg2)`

Kontroluje, zda je první hodnota menší než druhá hodnota.

### <a name="parameters"></a>Parametry

| Parametr | Požaduje se | Typ | Popis |
|:--- |:--- |:--- |:--- |
| arg1 |Ano |int nebo String |První hodnota pro méně porovnání. |
| arg2 |Ano |int nebo String |Druhá hodnota pro méně porovnání. |

### <a name="return-value"></a>Návratová hodnota

Vrátí **hodnotu true** , pokud je první hodnota menší než druhá hodnota. v opačném případě **false**.

### <a name="example"></a>Příklad:

Následující [příklad šablony](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/less.json) kontroluje, zda je jedna hodnota menší než druhá.

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

| Name (Název) | Typ | Hodnota |
| ---- | ---- | ----- |
| checkInts | Bool | Pravda |
| checkStrings | Bool | Nepravda |

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

Kontroluje, zda je první hodnota menší nebo rovna druhé hodnotě.

### <a name="parameters"></a>Parametry

| Parametr | Požaduje se | Typ | Popis |
|:--- |:--- |:--- |:--- |
| arg1 |Ano |int nebo String |První hodnota pro porovnání menší nebo rovno. |
| arg2 |Ano |int nebo String |Druhá hodnota pro porovnání menší nebo rovno. |

### <a name="return-value"></a>Návratová hodnota

Vrátí **hodnotu true** , pokud je první hodnota menší nebo rovna druhé hodnotě; v opačném případě **false**.

### <a name="example"></a>Příklad:

Následující [příklad šablony](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/lessorequals.json) kontroluje, zda je jedna hodnota menší nebo rovna druhému.

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

| Name (Název) | Typ | Hodnota |
| ---- | ---- | ----- |
| checkInts | Bool | Pravda |
| checkStrings | Bool | Nepravda |

Pokud chcete nasadit šablonu tento příklad pomocí Azure CLI, použijte:

```azurecli-interactive
az group deployment create -g functionexamplegroup --template-uri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/functions/lessorequals.json
```

Pokud chcete nasadit tento příklad šablony pomocí prostředí PowerShell, použijte:

```powershell
New-AzResourceGroupDeployment -ResourceGroupName functionexamplegroup -TemplateUri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/functions/lessorequals.json 
```

## <a name="next-steps"></a>Další kroky
* Popis části šablony Azure Resource Manageru najdete v tématu [šablon pro vytváření Azure Resource Manageru](template-syntax.md).
* Chcete-li sloučit několik šablon, přečtěte si téma [použití propojených šablon s Azure Resource Managerem](linked-templates.md).
* K iteraci zadaného počtu opakování při vytváření konkrétní typ prostředku, naleznete v tématu [vytvořit více instancí prostředku v Azure Resource Manageru](create-multiple-instances.md).
* Pokud chcete zjistit, jak nasadit šablonu, kterou jste vytvořili, přečtěte si téma [nasazení aplikace pomocí šablony Azure Resource Manager](deploy-powershell.md).

