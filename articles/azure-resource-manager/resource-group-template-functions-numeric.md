---
title: Azure Resource Manager funkce šablon - číselné | Microsoft Docs
description: Popisuje funkce pro použití v šablonu Azure Resource Manageru pro práci s čísla.
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
ms.date: 11/08/2017
ms.author: tomfitz
ms.openlocfilehash: 4fc17b997c44560199e65edb01d20c6a24e49877
ms.sourcegitcommit: b6319f1a87d9316122f96769aab0d92b46a6879a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/20/2018
ms.locfileid: "34359998"
---
# <a name="numeric-functions-for-azure-resource-manager-templates"></a>Numerické funkce pro šablony Azure Resource Manager

Resource Manager poskytuje následující funkce pro práci s celými čísly:

* [Přidat](#add)
* [copyIndex](#copyindex)
* [div](#div)
* [Plovoucí desetinná čárka](#float)
* [celá čísla](#int)
* [max](#max)
* [Min.](#min)
* [MOD](#mod)
* [mul](#mul)
* [sub](#sub)

<a id="add" />

## <a name="add"></a>přidat
`add(operand1, operand2)`

Vrátí součet dvou zadaný celých čísel.

### <a name="parameters"></a>Parametry

| Parametr | Požaduje se | Typ | Popis |
|:--- |:--- |:--- |:--- | 
|operand1 |Ano |celá čísla |První číslo přidat. |
|operand2 |Ano |celá čísla |Druhé číslo, které chcete přidat. |

### <a name="return-value"></a>Návratová hodnota

Celé číslo, které obsahuje součet hodnot parametrů.

### <a name="example"></a>Příklad:

Následující [příklad šablony](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/add.json) přidá dva parametry.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "first": {
            "type": "int",
            "defaultValue": 5,
            "metadata": {
                "description": "First integer to add"
            }
        },
        "second": {
            "type": "int",
            "defaultValue": 3,
            "metadata": {
                "description": "Second integer to add"
            }
        }
    },
    "resources": [
    ],
    "outputs": {
        "addResult": {
            "type": "int",
            "value": "[add(parameters('first'), parameters('second'))]"
        }
    }
}
```

Výstup z předchozího příkladu s výchozími hodnotami je:

| Název | Typ | Hodnota |
| ---- | ---- | ----- |
| addResult | Int | 8 |

Chcete-li nasadit tento příklad šablony pomocí rozhraní příkazového řádku Azure, použijte:

```azurecli-interactive
az group deployment create -g functionexamplegroup --template-uri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/functions/add.json
```

Chcete-li nasadit tento příklad šablony v prostředí PowerShell, použijte:

```powershell
New-AzureRmResourceGroupDeployment -ResourceGroupName functionexamplegroup -TemplateUri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/functions/add.json 
```

<a id="copyindex" />

## <a name="copyindex"></a>copyIndex
`copyIndex(loopName, offset)`

Vrátí index smyčky iterací. 

### <a name="parameters"></a>Parametry

| Parametr | Požaduje se | Typ | Popis |
|:--- |:--- |:--- |:--- |
| loopName | Ne | řetězec | Název smyčky pro získávání iterace. |
| Posun |Ne |celá čísla |Číslo, který se má přidat na nule iterace hodnotu. |

### <a name="remarks"></a>Poznámky

Tato funkce se vždy používá s **kopie** objektu. Pokud není zadána žádná hodnota pro **posun**, je vrácena hodnota aktuální iteraci. Hodnota iterace začíná od nuly. Iterace smyčky můžete použít při definování prostředky nebo proměnné.

**LoopName** vlastnost umožňuje určit, zda copyIndex odkazuje na prostředek iterace nebo vlastnost iterace. Pokud není zadána žádná hodnota pro **loopName**, se používá na aktuální iteraci typ prostředku. Zadejte hodnotu pro **loopName** během iterace u vlastnosti. 
 
Úplný popis jak používat **copyIndex**, najdete v části [vytvořit více instancí prostředků ve službě Správce prostředků Azure](resource-group-create-multiple.md).

Příklad použití **copyIndex** při definování proměnné, najdete v části [proměnné](resource-group-authoring-templates.md#variables).

### <a name="example"></a>Příklad:

Následující příklad ukazuje kopírovací smyčkou a hodnotu indexu, který je součástí názvu. 

```json
"resources": [ 
  { 
    "name": "[concat('examplecopy-', copyIndex())]", 
    "type": "Microsoft.Web/sites", 
    "copy": { 
      "name": "websitescopy", 
      "count": "[parameters('count')]" 
    }, 
    ...
  }
]
```

### <a name="return-value"></a>Návratová hodnota

Celé číslo představující aktuální index iterace.

<a id="div" />

## <a name="div"></a>div
`div(operand1, operand2)`

Vrátí celočíselné dělení dvou zadaný celých čísel.

### <a name="parameters"></a>Parametry

| Parametr | Požaduje se | Typ | Popis |
|:--- |:--- |:--- |:--- |
| operand1 |Ano |celá čísla |Číslo je rozdělen. |
| operand2 |Ano |celá čísla |Číslo, které slouží k rozdělení. Nemůže být 0. |

### <a name="return-value"></a>Návratová hodnota

Celé číslo představující rozdělení.

### <a name="example"></a>Příklad:

Následující [příklad šablony](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/div.json) rozdělí jeden parametr jiné parametrem.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "first": {
            "type": "int",
            "defaultValue": 8,
            "metadata": {
                "description": "Integer being divided"
            }
        },
        "second": {
            "type": "int",
            "defaultValue": 3,
            "metadata": {
                "description": "Integer used to divide"
            }
        }
    },
    "resources": [
    ],
    "outputs": {
        "divResult": {
            "type": "int",
            "value": "[div(parameters('first'), parameters('second'))]"
        }
    }
}
```

Výstup z předchozího příkladu s výchozími hodnotami je:

| Název | Typ | Hodnota |
| ---- | ---- | ----- |
| divResult | Int | 2 |

Chcete-li nasadit tento příklad šablony pomocí rozhraní příkazového řádku Azure, použijte:

```azurecli-interactive
az group deployment create -g functionexamplegroup --template-uri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/functions/div.json
```

Chcete-li nasadit tento příklad šablony v prostředí PowerShell, použijte:

```powershell
New-AzureRmResourceGroupDeployment -ResourceGroupName functionexamplegroup -TemplateUri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/functions/div.json 
```

<a id="float" />

## <a name="float"></a>float
`float(arg1)`

Převede hodnotu na plovoucí bodu číslo. Pouze použijete tuto funkci při předávání vlastních parametrů aplikace, jako je například aplikace logiky.

### <a name="parameters"></a>Parametry

| Parametr | Požaduje se | Typ | Popis |
|:--- |:--- |:--- |:--- |
| arg1 |Ano |řetězec nebo celá čísla |Hodnota, která má převést na plovoucí bodu číslo. |

### <a name="return-value"></a>Návratová hodnota
Plovoucí bodu číslo.

### <a name="example"></a>Příklad:

Následující příklad ukazuje, jak použít float a předat parametry do aplikace logiky:

```json
{
    "type": "Microsoft.Logic/workflows",
    "properties": {
        ...
        "parameters": {
            "custom1": {
                "value": "[float('3.0')]"
            },
            "custom2": {
                "value": "[float(3)]"
            },
```

<a id="int" />

## <a name="int"></a>celá čísla
`int(valueToConvert)`

Převede zadanou hodnotu na celé číslo.

### <a name="parameters"></a>Parametry

| Parametr | Požaduje se | Typ | Popis |
|:--- |:--- |:--- |:--- |
| valueToConvert |Ano |řetězec nebo celá čísla |Hodnota převést na celé číslo. |

### <a name="return-value"></a>Návratová hodnota

Celé číslo převedenou hodnotu.

### <a name="example"></a>Příklad:

Následující [příklad šablony](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/int.json) převede hodnotu parametru zadaný uživatelem na celé číslo.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "stringToConvert": { 
            "type": "string",
            "defaultValue": "4"
        }
    },
    "resources": [
    ],
    "outputs": {
        "intResult": {
            "type": "int",
            "value": "[int(parameters('stringToConvert'))]"
        }
    }
}
```

Výstup z předchozího příkladu s výchozími hodnotami je:

| Název | Typ | Hodnota |
| ---- | ---- | ----- |
| Zavřete | Int | 4 |

Chcete-li nasadit tento příklad šablony pomocí rozhraní příkazového řádku Azure, použijte:

```azurecli-interactive
az group deployment create -g functionexamplegroup --template-uri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/functions/int.json
```

Chcete-li nasadit tento příklad šablony v prostředí PowerShell, použijte:

```powershell
New-AzureRmResourceGroupDeployment -ResourceGroupName functionexamplegroup -TemplateUri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/functions/int.json
```

<a id="max" />

## <a name="max"></a>max
`max (arg1)`

Vrací maximální hodnotu z pole celá čísla nebo seznam celých čísel oddělených čárkami.

### <a name="parameters"></a>Parametry

| Parametr | Požaduje se | Typ | Popis |
|:--- |:--- |:--- |:--- |
| arg1 |Ano |pole celá čísla nebo seznam celých čísel oddělených čárkou |Kolekce získat maximální hodnotu. |

### <a name="return-value"></a>Návratová hodnota

Celé číslo představující maximální hodnotu z kolekce.

### <a name="example"></a>Příklad:

Následující [příklad šablony](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/max.json) ukazuje, jak použít maximum s pole a seznam celých čísel:

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "arrayToTest": {
            "type": "array",
            "defaultValue": [0,3,2,5,4]
        }
    },
    "resources": [],
    "outputs": {
        "arrayOutput": {
            "type": "int",
            "value": "[max(parameters('arrayToTest'))]"
        },
        "intOutput": {
            "type": "int",
            "value": "[max(0,3,2,5,4)]"
        }
    }
}
```

Výstup z předchozího příkladu s výchozími hodnotami je:

| Název | Typ | Hodnota |
| ---- | ---- | ----- |
| arrayOutput | Int | 5 |
| intOutput | Int | 5 |

Chcete-li nasadit tento příklad šablony pomocí rozhraní příkazového řádku Azure, použijte:

```azurecli-interactive
az group deployment create -g functionexamplegroup --template-uri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/functions/max.json
```

Chcete-li nasadit tento příklad šablony v prostředí PowerShell, použijte:

```powershell
New-AzureRmResourceGroupDeployment -ResourceGroupName functionexamplegroup -TemplateUri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/functions/max.json
```

<a id="min" />

## <a name="min"></a>min
`min (arg1)`

Vrátí minimální hodnotu z pole celá čísla nebo seznam celých čísel oddělených čárkami.

### <a name="parameters"></a>Parametry

| Parametr | Požaduje se | Typ | Popis |
|:--- |:--- |:--- |:--- |
| arg1 |Ano |pole celá čísla nebo seznam celých čísel oddělených čárkou |Kolekce získat minimální hodnotu. |

### <a name="return-value"></a>Návratová hodnota

Celé číslo představující minimální hodnota z kolekce.

### <a name="example"></a>Příklad:

Následující [příklad šablony](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/min.json) ukazuje, jak používat min s pole a seznam celých čísel:

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "arrayToTest": {
            "type": "array",
            "defaultValue": [0,3,2,5,4]
        }
    },
    "resources": [],
    "outputs": {
        "arrayOutput": {
            "type": "int",
            "value": "[min(parameters('arrayToTest'))]"
        },
        "intOutput": {
            "type": "int",
            "value": "[min(0,3,2,5,4)]"
        }
    }
}
```

Výstup z předchozího příkladu s výchozími hodnotami je:

| Název | Typ | Hodnota |
| ---- | ---- | ----- |
| arrayOutput | Int | 0 |
| intOutput | Int | 0 |

Chcete-li nasadit tento příklad šablony pomocí rozhraní příkazového řádku Azure, použijte:

```azurecli-interactive
az group deployment create -g functionexamplegroup --template-uri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/functions/min.json
```

Chcete-li nasadit tento příklad šablony v prostředí PowerShell, použijte:

```powershell
New-AzureRmResourceGroupDeployment -ResourceGroupName functionexamplegroup -TemplateUri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/functions/min.json
```

<a id="mod" />

## <a name="mod"></a>MOD
`mod(operand1, operand2)`

Vrátí zbytek celočíselného dělení pomocí dvě zadané celá čísla.

### <a name="parameters"></a>Parametry

| Parametr | Požaduje se | Typ | Popis |
|:--- |:--- |:--- |:--- |
| operand1 |Ano |celá čísla |Číslo je rozdělen. |
| operand2 |Ano |celá čísla |Číslo, které slouží k rozdělení, nemůže být 0. |

### <a name="return-value"></a>Návratová hodnota
Celé číslo představující zbytek.

### <a name="example"></a>Příklad:

Následující [příklad šablony](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/mod.json) Vrátí zbytek po dělení jeden parametr jiné parametrem.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "first": {
            "type": "int",
            "defaultValue": 7,
            "metadata": {
                "description": "Integer being divided"
            }
        },
        "second": {
            "type": "int",
            "defaultValue": 3,
            "metadata": {
                "description": "Integer used to divide"
            }
        }
    },
    "resources": [
    ],
    "outputs": {
        "modResult": {
            "type": "int",
            "value": "[mod(parameters('first'), parameters('second'))]"
        }
    }
}
```

Výstup z předchozího příkladu s výchozími hodnotami je:

| Název | Typ | Hodnota |
| ---- | ---- | ----- |
| modResult | Int | 1 |

Chcete-li nasadit tento příklad šablony pomocí rozhraní příkazového řádku Azure, použijte:

```azurecli-interactive
az group deployment create -g functionexamplegroup --template-uri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/functions/mod.json
```

Chcete-li nasadit tento příklad šablony v prostředí PowerShell, použijte:

```powershell
New-AzureRmResourceGroupDeployment -ResourceGroupName functionexamplegroup -TemplateUri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/functions/mod.json
```

<a id="mul" />

## <a name="mul"></a>mul
`mul(operand1, operand2)`

Vrátí násobení dvě zadané celých čísel.

### <a name="parameters"></a>Parametry

| Parametr | Požaduje se | Typ | Popis |
|:--- |:--- |:--- |:--- |
| operand1 |Ano |celá čísla |První číslo mají vynásobit. |
| operand2 |Ano |celá čísla |Druhé číslo, které mají vynásobit. |

### <a name="return-value"></a>Návratová hodnota

Celé číslo představující násobení.

### <a name="example"></a>Příklad:

Následující [příklad šablony](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/mul.json) vynásobí jeden parametr jiné parametrem.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "first": {
            "type": "int",
            "defaultValue": 5,
            "metadata": {
                "description": "First integer to multiply"
            }
        },
        "second": {
            "type": "int",
            "defaultValue": 3,
            "metadata": {
                "description": "Second integer to multiply"
            }
        }
    },
    "resources": [
    ],
    "outputs": {
        "mulResult": {
            "type": "int",
            "value": "[mul(parameters('first'), parameters('second'))]"
        }
    }
}
```

Výstup z předchozího příkladu s výchozími hodnotami je:

| Název | Typ | Hodnota |
| ---- | ---- | ----- |
| mulResult | Int | 15 |

Chcete-li nasadit tento příklad šablony pomocí rozhraní příkazového řádku Azure, použijte:

```azurecli-interactive
az group deployment create -g functionexamplegroup --template-uri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/functions/mul.json
```

Chcete-li nasadit tento příklad šablony v prostředí PowerShell, použijte:

```powershell
New-AzureRmResourceGroupDeployment -ResourceGroupName functionexamplegroup -TemplateUri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/functions/mul.json
```

<a id="sub" />

## <a name="sub"></a>Sub –
`sub(operand1, operand2)`

Vrátí odčítání dvě zadané celých čísel.

### <a name="parameters"></a>Parametry

| Parametr | Požaduje se | Typ | Popis |
|:--- |:--- |:--- |:--- |
| operand1 |Ano |celá čísla |Číslo, které je odečten od. |
| operand2 |Ano |celá čísla |Číslo, které je odečten. |

### <a name="return-value"></a>Návratová hodnota
Celé číslo představující odčítání.

### <a name="example"></a>Příklad:

Následující [příklad šablony](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/sub.json) odečítá od jiného parametru jeden parametr.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "first": {
            "type": "int",
            "defaultValue": 7,
            "metadata": {
                "description": "Integer subtracted from"
            }
        },
        "second": {
            "type": "int",
            "defaultValue": 3,
            "metadata": {
                "description": "Integer to subtract"
            }
        }
    },
    "resources": [
    ],
    "outputs": {
        "subResult": {
            "type": "int",
            "value": "[sub(parameters('first'), parameters('second'))]"
        }
    }
}
```

Výstup z předchozího příkladu s výchozími hodnotami je:

| Název | Typ | Hodnota |
| ---- | ---- | ----- |
| subResult | Int | 4 |

Chcete-li nasadit tento příklad šablony pomocí rozhraní příkazového řádku Azure, použijte:

```azurecli-interactive
az group deployment create -g functionexamplegroup --template-uri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/functions/sub.json
```

Chcete-li nasadit tento příklad šablony v prostředí PowerShell, použijte:

```powershell
New-AzureRmResourceGroupDeployment -ResourceGroupName functionexamplegroup -TemplateUri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/functions/sub.json
```

## <a name="next-steps"></a>Další postup
* Popis v částech šablonu Azure Resource Manager naleznete v tématu [šablon pro tvorbu Azure Resource Manageru](resource-group-authoring-templates.md).
* Sloučit několik šablon, najdete v části [použití propojených šablon s Azure Resource Manager](resource-group-linked-templates.md).
* K iteraci v zadaného počtu opakování při vytváření typu prostředku, najdete v části [vytvořit více instancí prostředků ve službě Správce prostředků Azure](resource-group-create-multiple.md).
* Postup nasazení šablony, které jste vytvořili, najdete v sekci [nasazení aplikace pomocí šablony Azure Resource Manageru](resource-group-template-deploy.md).

