---
title: Funkce šablony - porovnání
description: Popisuje funkce, které se mají použít v šabloně Azure Resource Manager k porovnání hodnot.
ms.topic: conceptual
ms.date: 09/05/2017
ms.openlocfilehash: 42009e8543e307f2d3e4643ddaa79f492f9bdfee
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "80156357"
---
# <a name="comparison-functions-for-arm-templates"></a>Funkce porovnání pro šablony ARM

Správce prostředků poskytuje několik funkcí pro porovnávání v šablonách Azure Resource Manager (ARM).

* [equals](#equals)
* [greater](#greater)
* [greaterOrEquals](#greaterorequals)
* [less](#less)
* [lessOrEquals](#lessorequals)

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

## <a name="equals"></a>equals
`equals(arg1, arg2)`

Zkontroluje, zda se dvě hodnoty vzájemně rovnají.

### <a name="parameters"></a>Parametry

| Parametr | Požaduje se | Typ | Popis |
|:--- |:--- |:--- |:--- |
| arg1 |Ano |int, řetězec, pole nebo objekt |První hodnota pro kontrolu rovnosti. |
| arg2 |Ano |int, řetězec, pole nebo objekt |Druhá hodnota pro kontrolu rovnosti. |

### <a name="return-value"></a>Návratová hodnota

Vrátí **hodnotu True,** pokud jsou hodnoty stejné; jinak **False**.

### <a name="remarks"></a>Poznámky

Equals funkce se často `condition` používá s elementem k testování, zda je nasazen prostředek.

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

### <a name="example"></a>Příklad

Následující [ukázková šablona](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/equals.json) kontroluje různé typy hodnot rovnosti. Všechny výchozí hodnoty vrátí hodnotu True.

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
| checkInts | Logická hodnota | True |
| kontrolní řetězce | Logická hodnota | True |
| checkArrays | Logická hodnota | True |
| checkObjects | Logická hodnota | True |

Chcete-li nasadit tuto ukázkovou šablonu pomocí příkazového příkazového příkazu k řešení Azure, použijte:

```azurecli-interactive
az deployment group create -g functionexamplegroup --template-uri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/functions/equals.json
```

Chcete-li nasadit tuto ukázkovou šablonu pomocí PowerShellu, použijte:

```powershell
New-AzResourceGroupDeployment -ResourceGroupName functionexamplegroup -TemplateUri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/functions/equals.json
```

Následující [ukázková šablona](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/not-equals.json) [nepoužívá](template-functions-logical.md#not) **rovná se**.

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

| Name (Název) | Typ | Hodnota |
| ---- | ---- | ----- |
| checkNotEquals | Logická hodnota | True |

Chcete-li nasadit tuto ukázkovou šablonu pomocí příkazového příkazového příkazu k řešení Azure, použijte:

```azurecli-interactive
az deployment group create -g functionexamplegroup --template-uri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/functions/not-equals.json
```

Chcete-li nasadit tuto ukázkovou šablonu pomocí PowerShellu, použijte:

```powershell
New-AzResourceGroupDeployment -ResourceGroupName functionexamplegroup -TemplateUri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/functions/not-equals.json
```

## <a name="greater"></a>greater
`greater(arg1, arg2)`

Zkontroluje, zda je první hodnota větší než druhá hodnota.

### <a name="parameters"></a>Parametry

| Parametr | Požaduje se | Typ | Popis |
|:--- |:--- |:--- |:--- |
| arg1 |Ano |int nebo řetězec |První hodnota pro větší porovnání. |
| arg2 |Ano |int nebo řetězec |Druhá hodnota pro větší porovnání. |

### <a name="return-value"></a>Návratová hodnota

Vrátí **hodnotu True,** pokud je první hodnota větší než druhá hodnota. jinak **False**.

### <a name="example"></a>Příklad

Následující [ukázková šablona](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/greater.json) kontroluje, zda je jedna hodnota větší než druhá.

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
| checkInts | Logická hodnota | False |
| kontrolní řetězce | Logická hodnota | True |

Chcete-li nasadit tuto ukázkovou šablonu pomocí příkazového příkazového příkazu k řešení Azure, použijte:

```azurecli-interactive
az deployment group create -g functionexamplegroup --template-uri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/functions/greater.json
```

Chcete-li nasadit tuto ukázkovou šablonu pomocí PowerShellu, použijte:

```powershell
New-AzResourceGroupDeployment -ResourceGroupName functionexamplegroup -TemplateUri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/functions/greater.json
```

## <a name="greaterorequals"></a>greaterOrEquals
`greaterOrEquals(arg1, arg2)`

Zkontroluje, zda je první hodnota větší nebo rovna druhé hodnotě.

### <a name="parameters"></a>Parametry

| Parametr | Požaduje se | Typ | Popis |
|:--- |:--- |:--- |:--- |
| arg1 |Ano |int nebo řetězec |První hodnota pro větší nebo rovno porovnání. |
| arg2 |Ano |int nebo řetězec |Druhá hodnota pro porovnání větší nebo rovna. |

### <a name="return-value"></a>Návratová hodnota

Vrátí **hodnotu True,** pokud je první hodnota větší nebo rovna druhé hodnotě. jinak **False**.

### <a name="example"></a>Příklad

Následující [ukázková šablona](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/greaterorequals.json) kontroluje, zda je jedna hodnota větší nebo rovna druhé.

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
| checkInts | Logická hodnota | False |
| kontrolní řetězce | Logická hodnota | True |

Chcete-li nasadit tuto ukázkovou šablonu pomocí příkazového příkazového příkazu k řešení Azure, použijte:

```azurecli-interactive
az deployment group create -g functionexamplegroup --template-uri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/functions/greaterorequals.json
```

Chcete-li nasadit tuto ukázkovou šablonu pomocí PowerShellu, použijte:

```powershell
New-AzResourceGroupDeployment -ResourceGroupName functionexamplegroup -TemplateUri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/functions/greaterorequals.json
```

## <a name="less"></a>less
`less(arg1, arg2)`

Zkontroluje, zda je první hodnota menší než druhá hodnota.

### <a name="parameters"></a>Parametry

| Parametr | Požaduje se | Typ | Popis |
|:--- |:--- |:--- |:--- |
| arg1 |Ano |int nebo řetězec |První hodnota pro méně porovnání. |
| arg2 |Ano |int nebo řetězec |Druhá hodnota pro méně porovnání. |

### <a name="return-value"></a>Návratová hodnota

Vrátí **hodnotu True,** pokud je první hodnota menší než druhá hodnota. jinak **False**.

### <a name="example"></a>Příklad

Následující [ukázková šablona](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/less.json) kontroluje, zda je jedna hodnota menší než druhá.

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
| checkInts | Logická hodnota | True |
| kontrolní řetězce | Logická hodnota | False |

Chcete-li nasadit tuto ukázkovou šablonu pomocí příkazového příkazového příkazu k řešení Azure, použijte:

```azurecli-interactive
az deployment group create -g functionexamplegroup --template-uri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/functions/less.json
```

Chcete-li nasadit tuto ukázkovou šablonu pomocí PowerShellu, použijte:

```powershell
New-AzResourceGroupDeployment -ResourceGroupName functionexamplegroup -TemplateUri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/functions/less.json
```

## <a name="lessorequals"></a>lessOrEquals
`lessOrEquals(arg1, arg2)`

Zkontroluje, zda je první hodnota menší nebo rovna druhé hodnotě.

### <a name="parameters"></a>Parametry

| Parametr | Požaduje se | Typ | Popis |
|:--- |:--- |:--- |:--- |
| arg1 |Ano |int nebo řetězec |První hodnota pro porovnání menší nebo rovná se. |
| arg2 |Ano |int nebo řetězec |Druhá hodnota pro porovnání menší nebo rovná se. |

### <a name="return-value"></a>Návratová hodnota

Vrátí **hodnotu True,** pokud je první hodnota menší nebo rovna druhé hodnotě. jinak **False**.

### <a name="example"></a>Příklad

Následující [ukázková šablona](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/lessorequals.json) kontroluje, zda je jedna hodnota menší nebo rovna druhé.

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
| checkInts | Logická hodnota | True |
| kontrolní řetězce | Logická hodnota | False |

Chcete-li nasadit tuto ukázkovou šablonu pomocí příkazového příkazového příkazu k řešení Azure, použijte:

```azurecli-interactive
az deployment group create -g functionexamplegroup --template-uri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/functions/lessorequals.json
```

Chcete-li nasadit tuto ukázkovou šablonu pomocí PowerShellu, použijte:

```powershell
New-AzResourceGroupDeployment -ResourceGroupName functionexamplegroup -TemplateUri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/functions/lessorequals.json
```

## <a name="next-steps"></a>Další kroky
* Popis oddílů v šabloně Azure Resource Manager u najdete v tématu [Vytváření šablon Azure Resource Manageru](template-syntax.md).
* Pokud chcete sloučit víc šablon, přečtěte si informace [o použití propojených šablon ve Správci prostředků Azure](linked-templates.md).
* Chcete-li itrerate zadaný počet časů při vytváření typu prostředku, najdete v [tématu vytvoření více instancí prostředků ve Správci prostředků Azure](copy-resources.md).
* Informace o tom, jak nasadit vytvořenou šablonu, najdete [v tématu Nasazení aplikace pomocí šablony Azure Resource Manageru](deploy-powershell.md).

