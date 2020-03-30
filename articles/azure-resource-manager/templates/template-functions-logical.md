---
title: Funkce šablony - logické
description: Popisuje funkce, které se mají použít v šabloně Azure Resource Manager k určení logických hodnot.
ms.topic: conceptual
ms.date: 04/15/2019
ms.openlocfilehash: f058baa32e5f93a4177913287a5e9873fa7a9acb
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "80156306"
---
# <a name="logical-functions-for-arm-templates"></a>Logické funkce pro šablony ARM

Správce prostředků poskytuje několik funkcí pro porovnávání v šablonách Azure Resource Manager (ARM).

* [A](#and)
* [bool](#bool)
* [if](#if)
* [Ne](#not)
* [Nebo](#or)

## <a name="and"></a>a

`and(arg1, arg2, ...)`

Zkontroluje, zda jsou všechny hodnoty parametrů pravdivé.

### <a name="parameters"></a>Parametry

| Parametr | Požaduje se | Typ | Popis |
|:--- |:--- |:--- |:--- |
| arg1 |Ano |Boolean |První hodnota pro kontrolu, zda je true. |
| arg2 |Ano |Boolean |Druhá hodnota pro kontrolu, zda je true. |
| další argumenty |Ne |Boolean |Další argumenty ke kontrole, zda jsou true. |

### <a name="return-value"></a>Návratová hodnota

Vrátí **hodnotu True,** pokud jsou všechny hodnoty true; jinak **False**.

### <a name="examples"></a>Příklady

Následující [ukázková šablona](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/andornot.json) ukazuje, jak používat logické funkce.

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

| Name (Název) | Typ | Hodnota |
| ---- | ---- | ----- |
| aExampleOutput | Logická hodnota | False |
| neboExampleOutput | Logická hodnota | True |
| notExampleOutput | Logická hodnota | False |

## <a name="bool"></a>bool

`bool(arg1)`

Převede parametr na logickou hodnotu.

### <a name="parameters"></a>Parametry

| Parametr | Požaduje se | Typ | Popis |
|:--- |:--- |:--- |:--- |
| arg1 |Ano |řetězec nebo int |Hodnota, která má být převedena na logickou hodnotu. |

### <a name="return-value"></a>Návratová hodnota
Logická hodnota převedené hodnoty.

### <a name="examples"></a>Příklady

Následující [ukázková šablona](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/bool.json) ukazuje, jak používat bool s řetězcem nebo celé číslo.

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

| Name (Název) | Typ | Hodnota |
| ---- | ---- | ----- |
| trueString | Logická hodnota | True |
| Falsestring | Logická hodnota | False |
| trueInt | Logická hodnota | True |
| falseInt | Logická hodnota | False |

## <a name="if"></a>if

`if(condition, trueValue, falseValue)`

Vrátí hodnotu na základě toho, zda je podmínka pravdivá nebo nepravdivá.

### <a name="parameters"></a>Parametry

| Parametr | Požaduje se | Typ | Popis |
|:--- |:--- |:--- |:--- |
| Podmínka |Ano |Boolean |Hodnota zkontrolovat, zda je true nebo false. |
| Truevalue |Ano | řetězec, int, objekt nebo pole |Hodnota vrátit, když je podmínka true. |
| Falsevalue |Ano | řetězec, int, objekt nebo pole |Hodnota vrátit, pokud je podmínka false. |

### <a name="return-value"></a>Návratová hodnota

Vrátí druhý parametr, pokud je první parametr **True**; v opačném případě vrátí třetí parametr.

### <a name="remarks"></a>Poznámky

Pokud je podmínka **True**, je vyhodnocena pouze skutečná hodnota. Pokud je podmínka **False**, je vyhodnocena pouze hodnota false. Pomocí funkce **if** můžete zahrnout výrazy, které jsou pouze podmíněně platné. Můžete například odkazovat na prostředek, který existuje pod jednou podmínkou, ale ne pod druhou podmínkou. Příklad podmíněného vyhodnocení výrazů je uveden v následující části.

### <a name="examples"></a>Příklady

Následující [ukázková šablona](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/if.json) ukazuje, jak používat `if` funkci.

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

| Name (Název) | Typ | Hodnota |
| ---- | ---- | ----- |
| yesOutput | Řetězec | ano |
| noVýstup | Řetězec | ne |
| objectOutput | Objekt | { "test": "value1" } |

Následující [ukázková šablona](https://github.com/krnese/AzureDeploy/blob/master/ARM/deployments/conditionWithReference.json) ukazuje, jak používat tuto funkci s výrazy, které jsou pouze podmíněně platné.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
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

| Parametr | Požaduje se | Typ | Popis |
|:--- |:--- |:--- |:--- |
| arg1 |Ano |Boolean |Hodnota, kterou chcete převést. |

### <a name="return-value"></a>Návratová hodnota

Vrátí **hodnotu True,** pokud je parametr **False**. Vrátí **hodnotu Hodnotit hodnotu Hodnotit hodnotu** **True,** pokud je parametr True .

### <a name="examples"></a>Příklady

Následující [ukázková šablona](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/andornot.json) ukazuje, jak používat logické funkce.

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

| Name (Název) | Typ | Hodnota |
| ---- | ---- | ----- |
| aExampleOutput | Logická hodnota | False |
| neboExampleOutput | Logická hodnota | True |
| notExampleOutput | Logická hodnota | False |

Následující [ukázková šablona](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/not-equals.json) **nepoužívá** [rovná se](template-functions-comparison.md#equals).

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

| Name (Název) | Typ | Hodnota |
| ---- | ---- | ----- |
| checkNotEquals | Logická hodnota | True |

## <a name="or"></a>– nebo –

`or(arg1, arg2, ...)`

Zkontroluje, zda je hodnota parametru true.

### <a name="parameters"></a>Parametry

| Parametr | Požaduje se | Typ | Popis |
|:--- |:--- |:--- |:--- |
| arg1 |Ano |Boolean |První hodnota pro kontrolu, zda je true. |
| arg2 |Ano |Boolean |Druhá hodnota pro kontrolu, zda je true. |
| další argumenty |Ne |Boolean |Další argumenty ke kontrole, zda jsou true. |

### <a name="return-value"></a>Návratová hodnota

Vrátí **hodnotu True,** pokud je nějaká hodnota true; jinak **False**.

### <a name="examples"></a>Příklady

Následující [ukázková šablona](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/andornot.json) ukazuje, jak používat logické funkce.

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

| Name (Název) | Typ | Hodnota |
| ---- | ---- | ----- |
| aExampleOutput | Logická hodnota | False |
| neboExampleOutput | Logická hodnota | True |
| notExampleOutput | Logická hodnota | False |

## <a name="next-steps"></a>Další kroky

* Popis oddílů v šabloně Azure Resource Manager u najdete v tématu [Vytváření šablon Azure Resource Manageru](template-syntax.md).
* Pokud chcete sloučit víc šablon, přečtěte si informace [o použití propojených šablon ve Správci prostředků Azure](linked-templates.md).
* Chcete-li itrerate zadaný počet časů při vytváření typu prostředku, najdete v [tématu vytvoření více instancí prostředků ve Správci prostředků Azure](copy-resources.md).
* Informace o tom, jak nasadit vytvořenou šablonu, najdete [v tématu Nasazení aplikace pomocí šablony Azure Resource Manageru](deploy-powershell.md).

