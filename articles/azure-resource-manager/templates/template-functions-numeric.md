---
title: Funkce šablony - číselné
description: Popisuje funkce, které se mají použít v šabloně Azure Resource Manager pro práci s čísly.
ms.topic: conceptual
ms.date: 11/08/2017
ms.openlocfilehash: 2ca5c539036d002b83b8141132a0ebf2530dc6af
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "80156340"
---
# <a name="numeric-functions-for-arm-templates"></a>Číselné funkce pro šablony ARM

Správce prostředků poskytuje následující funkce pro práci s celáčísla v šabloně Správce prostředků Azure (ARM):

* [add](#add)
* [copyIndex](#copyindex)
* [div](#div)
* [float](#float)
* [int](#int)
* [Max](#max)
* [Min](#min)
* [Mod](#mod)
* [mul](#mul)
* [Dílčí](#sub)

<a id="add" />

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

## <a name="add"></a>add
`add(operand1, operand2)`

Vrátí součet dvou poskytnutých celá čísla.

### <a name="parameters"></a>Parametry

| Parametr | Požaduje se | Typ | Popis |
|:--- |:--- |:--- |:--- |
|operand1 |Ano |int |První číslo přidat. |
|operand2 |Ano |int |Druhé číslo přidat. |

### <a name="return-value"></a>Návratová hodnota

Celé číslo, které obsahuje součet parametrů.

### <a name="example"></a>Příklad

Následující [ukázková šablona](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/add.json) přidá dva parametry.

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

| Name (Název) | Typ | Hodnota |
| ---- | ---- | ----- |
| addResult | Int | 8 |

Chcete-li nasadit tuto ukázkovou šablonu pomocí příkazového příkazového příkazu k řešení Azure, použijte:

```azurecli-interactive
az deployment group create -g functionexamplegroup --template-uri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/functions/add.json
```

Chcete-li nasadit tuto ukázkovou šablonu pomocí PowerShellu, použijte:

```powershell
New-AzResourceGroupDeployment -ResourceGroupName functionexamplegroup -TemplateUri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/functions/add.json
```

<a id="copyindex" />

## <a name="copyindex"></a>copyIndex
`copyIndex(loopName, offset)`

Vrátí index iterace smyčky.

### <a name="parameters"></a>Parametry

| Parametr | Požaduje se | Typ | Popis |
|:--- |:--- |:--- |:--- |
| název smyčky | Ne | řetězec | Název smyčky pro získání iterace. |
| posun |Ne |int |Číslo, které chcete přidat k hodnotě iterace založené na nule. |

### <a name="remarks"></a>Poznámky

Tato funkce se vždy používá s **objektem kopírování.** Pokud není zadána žádná hodnota pro **posun**, je vrácena aktuální hodnota iterace. Hodnota iterace začíná na nule. Iterace smyčky můžete použít při definování buď prostředky nebo proměnné.

Vlastnost **loopName** umožňuje určit, zda copyIndex odkazuje na iteraci prostředku nebo iteraci vlastností. Pokud pro **název loopName**není zadána žádná hodnota , použije se aktuální iterace typu prostředku. Zadejte hodnotu pro **loopName** při iterace na vlastnost.

Úplný popis použití **copyIndex**najdete v tématu [Vytvoření více instancí prostředků ve Správci prostředků Azure](copy-resources.md).

Příklad použití **copyIndex** při definování proměnné naleznete v tématu [Proměnné](template-syntax.md#variables).

### <a name="example"></a>Příklad

Následující příklad ukazuje smyčku kopírování a hodnotu indexu zahrnutou v názvu.

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

Vrátí celé číslo dělení dvou zadaných celá čísla.

### <a name="parameters"></a>Parametry

| Parametr | Požaduje se | Typ | Popis |
|:--- |:--- |:--- |:--- |
| operand1 |Ano |int |Číslo, které se dělí. |
| operand2 |Ano |int |Číslo, které se používá k dělení. Nemůže být 0. |

### <a name="return-value"></a>Návratová hodnota

Celé číslo představující rozdělení.

### <a name="example"></a>Příklad

Následující [ukázková šablona](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/div.json) rozdělí jeden parametr jiným parametrem.

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

| Name (Název) | Typ | Hodnota |
| ---- | ---- | ----- |
| divVýsledek | Int | 2 |

Chcete-li nasadit tuto ukázkovou šablonu pomocí příkazového příkazového příkazu k řešení Azure, použijte:

```azurecli-interactive
az deployment group create -g functionexamplegroup --template-uri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/functions/div.json
```

Chcete-li nasadit tuto ukázkovou šablonu pomocí PowerShellu, použijte:

```powershell
New-AzResourceGroupDeployment -ResourceGroupName functionexamplegroup -TemplateUri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/functions/div.json
```

<a id="float" />

## <a name="float"></a>float
`float(arg1)`

Převede hodnotu na číslo s plovoucí desetinnou tácem. Tuto funkci používáte pouze při předávání vlastníparametry do aplikace, jako je například aplikace logiky.

### <a name="parameters"></a>Parametry

| Parametr | Požaduje se | Typ | Popis |
|:--- |:--- |:--- |:--- |
| arg1 |Ano |řetězec nebo int |Hodnota převést na číslo s plovoucí desetinnou tácek. |

### <a name="return-value"></a>Návratová hodnota
Číslo s plovoucí desetinnou tísní.

### <a name="example"></a>Příklad

Následující příklad ukazuje, jak použít float předat parametry do aplikace logiky:

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

## <a name="int"></a>int
`int(valueToConvert)`

Převede zadanou hodnotu na celé číslo.

### <a name="parameters"></a>Parametry

| Parametr | Požaduje se | Typ | Popis |
|:--- |:--- |:--- |:--- |
| valueToConvert |Ano |řetězec nebo int |Hodnota převést na celé číslo. |

### <a name="return-value"></a>Návratová hodnota

Celé číslo převedené hodnoty.

### <a name="example"></a>Příklad

Následující [ukázková šablona](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/int.json) převede hodnotu parametru zajišťované uživatelem na celé číslo.

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

| Name (Název) | Typ | Hodnota |
| ---- | ---- | ----- |
| intResult | Int | 4 |

Chcete-li nasadit tuto ukázkovou šablonu pomocí příkazového příkazového příkazu k řešení Azure, použijte:

```azurecli-interactive
az deployment group create -g functionexamplegroup --template-uri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/functions/int.json
```

Chcete-li nasadit tuto ukázkovou šablonu pomocí PowerShellu, použijte:

```powershell
New-AzResourceGroupDeployment -ResourceGroupName functionexamplegroup -TemplateUri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/functions/int.json
```

<a id="max" />

## <a name="max"></a>max
`max (arg1)`

Vrátí maximální hodnotu z pole celá čísla nebo čárka odděleného seznamu celá čísla.

### <a name="parameters"></a>Parametry

| Parametr | Požaduje se | Typ | Popis |
|:--- |:--- |:--- |:--- |
| arg1 |Ano |pole celá čísla nebo seznam celočísel oddělených čárkami |Kolekce získat maximální hodnotu. |

### <a name="return-value"></a>Návratová hodnota

Celé číslo představující maximální hodnotu z kolekce.

### <a name="example"></a>Příklad

Následující [ukázková šablona](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/max.json) ukazuje, jak používat max s polem a seznamem celá čísla:

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

| Name (Název) | Typ | Hodnota |
| ---- | ---- | ----- |
| poleOutput | Int | 5 |
| intOutput | Int | 5 |

Chcete-li nasadit tuto ukázkovou šablonu pomocí příkazového příkazového příkazu k řešení Azure, použijte:

```azurecli-interactive
az deployment group create -g functionexamplegroup --template-uri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/functions/max.json
```

Chcete-li nasadit tuto ukázkovou šablonu pomocí PowerShellu, použijte:

```powershell
New-AzResourceGroupDeployment -ResourceGroupName functionexamplegroup -TemplateUri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/functions/max.json
```

<a id="min" />

## <a name="min"></a>min
`min (arg1)`

Vrátí minimální hodnotu z pole celá čísla nebo čárka odděleného seznamu celá čísla.

### <a name="parameters"></a>Parametry

| Parametr | Požaduje se | Typ | Popis |
|:--- |:--- |:--- |:--- |
| arg1 |Ano |pole celá čísla nebo seznam celočísel oddělených čárkami |Kolekce získat minimální hodnotu. |

### <a name="return-value"></a>Návratová hodnota

Celé číslo představující minimální hodnotu z kolekce.

### <a name="example"></a>Příklad

Následující [ukázková šablona](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/min.json) ukazuje, jak používat min s polem a seznamem celá čísla:

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

| Name (Název) | Typ | Hodnota |
| ---- | ---- | ----- |
| poleOutput | Int | 0 |
| intOutput | Int | 0 |

Chcete-li nasadit tuto ukázkovou šablonu pomocí příkazového příkazového příkazu k řešení Azure, použijte:

```azurecli-interactive
az deployment group create -g functionexamplegroup --template-uri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/functions/min.json
```

Chcete-li nasadit tuto ukázkovou šablonu pomocí PowerShellu, použijte:

```powershell
New-AzResourceGroupDeployment -ResourceGroupName functionexamplegroup -TemplateUri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/functions/min.json
```

<a id="mod" />

## <a name="mod"></a>Mod
`mod(operand1, operand2)`

Vrátí zbytek dělení celé číslo pomocí dvou poskytnutých celá čísla.

### <a name="parameters"></a>Parametry

| Parametr | Požaduje se | Typ | Popis |
|:--- |:--- |:--- |:--- |
| operand1 |Ano |int |Číslo, které se dělí. |
| operand2 |Ano |int |Číslo, které se používá k dělení, nelze být 0. |

### <a name="return-value"></a>Návratová hodnota
Celé číslo představující zbytek.

### <a name="example"></a>Příklad

Následující [ukázková šablona](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/mod.json) vrátí zbytek dělení jednoho parametru jiným parametrem.

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

| Name (Název) | Typ | Hodnota |
| ---- | ---- | ----- |
| modVýsledek | Int | 1 |

Chcete-li nasadit tuto ukázkovou šablonu pomocí příkazového příkazového příkazu k řešení Azure, použijte:

```azurecli-interactive
az deployment group create -g functionexamplegroup --template-uri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/functions/mod.json
```

Chcete-li nasadit tuto ukázkovou šablonu pomocí PowerShellu, použijte:

```powershell
New-AzResourceGroupDeployment -ResourceGroupName functionexamplegroup -TemplateUri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/functions/mod.json
```

<a id="mul" />

## <a name="mul"></a>mul
`mul(operand1, operand2)`

Vrátí násobení dvou zadaných celá čísla.

### <a name="parameters"></a>Parametry

| Parametr | Požaduje se | Typ | Popis |
|:--- |:--- |:--- |:--- |
| operand1 |Ano |int |První číslo k násobení. |
| operand2 |Ano |int |Druhé číslo k násobení. |

### <a name="return-value"></a>Návratová hodnota

Celé číslo představující násobení.

### <a name="example"></a>Příklad

Následující [ukázková šablona](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/mul.json) vynásobí jeden parametr jiným parametrem.

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

| Name (Název) | Typ | Hodnota |
| ---- | ---- | ----- |
| mulVýsledek | Int | 15 |

Chcete-li nasadit tuto ukázkovou šablonu pomocí příkazového příkazového příkazu k řešení Azure, použijte:

```azurecli-interactive
az deployment group create -g functionexamplegroup --template-uri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/functions/mul.json
```

Chcete-li nasadit tuto ukázkovou šablonu pomocí PowerShellu, použijte:

```powershell
New-AzResourceGroupDeployment -ResourceGroupName functionexamplegroup -TemplateUri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/functions/mul.json
```

<a id="sub" />

## <a name="sub"></a>Dílčí
`sub(operand1, operand2)`

Vrátí odečtení dvou zadaných celá čísla.

### <a name="parameters"></a>Parametry

| Parametr | Požaduje se | Typ | Popis |
|:--- |:--- |:--- |:--- |
| operand1 |Ano |int |Číslo, které je odečteno od. |
| operand2 |Ano |int |Číslo, které je odečteno. |

### <a name="return-value"></a>Návratová hodnota
Celé číslo představující odčítání.

### <a name="example"></a>Příklad

Následující [ukázková šablona](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/sub.json) odečte jeden parametr od jiného parametru.

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

| Name (Název) | Typ | Hodnota |
| ---- | ---- | ----- |
| dílčí výsledek | Int | 4 |

Chcete-li nasadit tuto ukázkovou šablonu pomocí příkazového příkazového příkazu k řešení Azure, použijte:

```azurecli-interactive
az deployment group create -g functionexamplegroup --template-uri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/functions/sub.json
```

Chcete-li nasadit tuto ukázkovou šablonu pomocí PowerShellu, použijte:

```powershell
New-AzResourceGroupDeployment -ResourceGroupName functionexamplegroup -TemplateUri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/functions/sub.json
```

## <a name="next-steps"></a>Další kroky
* Popis oddílů v šabloně Azure Resource Manager u najdete v tématu [Vytváření šablon Azure Resource Manageru](template-syntax.md).
* Pokud chcete sloučit víc šablon, přečtěte si informace [o použití propojených šablon ve Správci prostředků Azure](linked-templates.md).
* Chcete-li itrerate zadaný počet časů při vytváření typu prostředku, najdete v [tématu vytvoření více instancí prostředků ve Správci prostředků Azure](copy-resources.md).
* Informace o tom, jak nasadit vytvořenou šablonu, najdete [v tématu Nasazení aplikace pomocí šablony Azure Resource Manageru](deploy-powershell.md).

