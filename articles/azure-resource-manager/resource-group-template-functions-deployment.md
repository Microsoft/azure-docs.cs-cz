---
title: Funkce šablon Azure Resource Manageru – nasazení | Dokumentace Microsoftu
description: Popisuje funkce pro použití v šabloně Azure Resource Manageru k načtení informací o nasazení.
services: azure-resource-manager
documentationcenter: na
author: tfitzmac
ms.assetid: ''
ms.service: azure-resource-manager
ms.devlang: na
ms.topic: reference
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 01/03/2019
ms.author: tomfitz
ms.openlocfilehash: c5bd40741ec0fe047f98b4b4431819d90e188385
ms.sourcegitcommit: 5978d82c619762ac05b19668379a37a40ba5755b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/31/2019
ms.locfileid: "55491463"
---
# <a name="deployment-functions-for-azure-resource-manager-templates"></a>Nasazení funkce pro šablony Azure Resource Manageru 

Resource Manager poskytuje následující funkce pro načtení hodnot z části šablony a hodnoty související s nasazením:

* [Nasazení](#deployment)
* [parameters](#parameters)
* [Proměnné](#variables)

K získání hodnot z prostředky, skupiny prostředků nebo předplatná, najdete v článku [funkce Resource](resource-group-template-functions-resource.md).

<a id="deployment" />

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="deployment"></a>nasazení
`deployment()`

Vrátí informace o aktuální operace nasazení.

### <a name="return-value"></a>Návratová hodnota

Tato funkce vrací objekt, který je předán během nasazení. Vlastnosti vráceného objektu se liší podle toho, jestli je nasazení objekt předán jako odkazu nebo objektu v řádku. Při nasazení nebude předaný objekt v řádku, jako například při použití **- TemplateFile** parametr v prostředí Azure PowerShell přejděte do místního souboru, vráceného objektu má následující formát:

```json
{
    "name": "",
    "properties": {
        "template": {
            "$schema": "",
            "contentVersion": "",
            "parameters": {},
            "variables": {},
            "resources": [
            ],
            "outputs": {}
        },
        "parameters": {},
        "mode": "",
        "provisioningState": ""
    }
}
```

Pokud objekt je předán jako odkaz, například při použití **- TemplateUri** parametru tak, aby odkazoval na vzdálený objekt, je objekt vrácen v následujícím formátu: 

```json
{
    "name": "",
    "properties": {
        "templateLink": {
            "uri": ""
        },
        "template": {
            "$schema": "",
            "contentVersion": "",
            "parameters": {},
            "variables": {},
            "resources": [],
            "outputs": {}
        },
        "parameters": {},
        "mode": "",
        "provisioningState": ""
    }
}
```

Když můžete [nasazení s předplatným Azure](deploy-to-subscription.md), místo skupiny prostředků, vrácený objekt zahrnuje `location` vlastnost. Vlastnost location je součástí nasazení šablony místní nebo externí šablony.

### <a name="remarks"></a>Poznámky

Deployment() můžete propojit s jinou šablonu podle identifikátoru URI nadřazené šablony.

```json
"variables": {  
    "sharedTemplateUrl": "[uri(deployment().properties.templateLink.uri, 'shared-resources.json')]"  
}
```  

Pokud provádíte opakované nasazení šablony z historie nasazení na portálu, šablony se nasadí jako místní soubor. `templateLink` Nasazení funkcí není vrácena vlastnost. Pokud vaše šablony závisí na `templateLink` Pokud chcete vytvořit odkaz na jinou šablonu, nepoužívejte na portálu znovu. Místo toho použijte příkazy, které jste použili k nasazení šablony původně.

### <a name="example"></a>Příklad:

Následující [Ukázková šablona](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/deployment.json) vrátí objekt nasazení:

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "resources": [],
    "outputs": {
        "subscriptionOutput": {
            "value": "[deployment()]",
            "type" : "object"
        }
    }
}
```

V předchozím příkladu vrátí následující objekt:

```json
{
  "name": "deployment",
  "properties": {
    "template": {
      "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
      "contentVersion": "1.0.0.0",
      "resources": [],
      "outputs": {
        "subscriptionOutput": {
          "type": "Object",
          "value": "[deployment()]"
        }
      }
    },
    "parameters": {},
    "mode": "Incremental",
    "provisioningState": "Accepted"
  }
}
```

Pokud chcete nasadit šablonu tento příklad pomocí Azure CLI, použijte:

```azurecli-interactive
az group deployment create -g functionexamplegroup --template-uri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/functions/deployment.json
```

Pokud chcete nasadit tento příklad šablony pomocí prostředí PowerShell, použijte:

```powershell
New-AzResourceGroupDeployment -ResourceGroupName functionexamplegroup -TemplateUri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/functions/deployment.json
```

Úroveň předplatného šablony, která používá funkci nasazení, najdete v části [předplatné nasazení funkce](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/deploymentsubscription.json). Nasazuje se s oběma `az deployment create` nebo `New-AzDeployment` příkazy.

<a id="parameters" />

## <a name="parameters"></a>parameters
`parameters(parameterName)`

Vrátí hodnotu parametru. Zadaný název parametru musí být definovaný v sekci parametrů šablony.

### <a name="parameters"></a>Parametry

| Parametr | Požaduje se | Typ | Popis |
|:--- |:--- |:--- |:--- |
| Název parametru |Ano |řetězec |Název parametru se vraťte. |

### <a name="return-value"></a>Návratová hodnota

Hodnota zadaná parametru.

### <a name="remarks"></a>Poznámky

Parametry se obvykle používají k nastavení hodnot prostředku. Následující příklad nastaví název webové stránky na hodnotu parametru předaného během nasazení.

```json
"parameters": { 
  "siteName": {
      "type": "string"
  }
},
"resources": [
   {
      "apiVersion": "2016-08-01",
      "name": "[parameters('siteName')]",
      "type": "Microsoft.Web/Sites",
      ...
   }
]
```

### <a name="example"></a>Příklad:

Následující [Ukázková šablona](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/parameters.json) ukazuje zjednodušený použití parametry funkce.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "stringParameter": {
            "type" : "string",
            "defaultValue": "option 1"
        },
        "intParameter": {
            "type": "int",
            "defaultValue": 1
        },
        "objectParameter": {
            "type": "object",
            "defaultValue": {"one": "a", "two": "b"}
        },
        "arrayParameter": {
            "type": "array",
            "defaultValue": [1, 2, 3]
        },
        "crossParameter": {
            "type": "string",
            "defaultValue": "[parameters('stringParameter')]"
        }
    },
    "variables": {},
    "resources": [],
    "outputs": {
        "stringOutput": {
            "value": "[parameters('stringParameter')]",
            "type" : "string"
        },
        "intOutput": {
            "value": "[parameters('intParameter')]",
            "type" : "int"
        },
        "objectOutput": {
            "value": "[parameters('objectParameter')]",
            "type" : "object"
        },
        "arrayOutput": {
            "value": "[parameters('arrayParameter')]",
            "type" : "array"
        },
        "crossOutput": {
            "value": "[parameters('crossParameter')]",
            "type" : "string"
        }
    }
}
```

Výstup z předchozího příkladu s výchozími hodnotami je:

| Název | Typ | Hodnota |
| ---- | ---- | ----- |
| stringOutput | String | Možnost 1 |
| intOutput | Int | 1 |
| objectOutput | Objekt | {"jedna": "a", "2": "b"} |
| arrayOutput | Pole | [1, 2, 3] |
| crossOutput | String | Možnost 1 |

Pokud chcete nasadit šablonu tento příklad pomocí Azure CLI, použijte:

```azurecli-interactive
az group deployment create -g functionexamplegroup --template-uri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/functions/parameters.json
```

Pokud chcete nasadit tento příklad šablony pomocí prostředí PowerShell, použijte:

```powershell
New-AzResourceGroupDeployment -ResourceGroupName functionexamplegroup -TemplateUri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/functions/parameters.json
```

<a id="variables" />

## <a name="variables"></a>Proměnné
`variables(variableName)`

Vrátí hodnotu proměnné. Zadaný název proměnné musí být definovaný v sekci proměnných šablony.

### <a name="parameters"></a>Parametry

| Parametr | Požaduje se | Typ | Popis |
|:--- |:--- |:--- |:--- |
| variableName |Ano |String |Název proměnné, která se vrátí. |

### <a name="return-value"></a>Návratová hodnota

Hodnotu zadanou proměnnou.

### <a name="remarks"></a>Poznámky

Proměnné se obvykle používají pro zjednodušení šablony tak, že vytváří komplexní hodnoty pouze jednou. Následující příklad vytvoří jedinečný název pro účet úložiště.

```json
"variables": {
    "storageName": "[concat('storage', uniqueString(resourceGroup().id))]"
},
"resources": [
    {
        "type": "Microsoft.Storage/storageAccounts",
        "name": "[variables('storageName')]",
        ...
    },
    {
        "type": "Microsoft.Compute/virtualMachines",
        "dependsOn": [
            "[variables('storageName')]"
        ],
        ...
    }
],
```

### <a name="example"></a>Příklad:

Následující [Ukázková šablona](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/variables.json) vrátí různé hodnoty proměnné.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {},
    "variables": {
        "var1": "myVariable",
        "var2": [ 1,2,3,4 ],
        "var3": "[ variables('var1') ]",
        "var4": {
            "property1": "value1",
            "property2": "value2"
        }
    },
    "resources": [],
    "outputs": {
        "exampleOutput1": {
            "value": "[variables('var1')]",
            "type" : "string"
        },
        "exampleOutput2": {
            "value": "[variables('var2')]",
            "type" : "array"
        },
        "exampleOutput3": {
            "value": "[variables('var3')]",
            "type" : "string"
        },
        "exampleOutput4": {
            "value": "[variables('var4')]",
            "type" : "object"
        }
    }
}
```

Výstup z předchozího příkladu s výchozími hodnotami je:

| Název | Typ | Hodnota |
| ---- | ---- | ----- |
| exampleOutput1 | String | myVariable |
| exampleOutput2 | Pole | [1, 2, 3, 4] |
| exampleOutput3 | String | myVariable |
| exampleOutput4 |  Objekt | {"vlastnost1": "hodnota1", "vlastnost2": "hodnota2"} |

Pokud chcete nasadit šablonu tento příklad pomocí Azure CLI, použijte:

```azurecli-interactive
az group deployment create -g functionexamplegroup --template-uri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/functions/variables.json
```

Pokud chcete nasadit tento příklad šablony pomocí prostředí PowerShell, použijte:

```powershell
New-AzResourceGroupDeployment -ResourceGroupName functionexamplegroup -TemplateUri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/functions/variables.json
```

## <a name="next-steps"></a>Další postup
* Popis části šablony Azure Resource Manageru najdete v tématu [šablon pro vytváření Azure Resource Manageru](resource-group-authoring-templates.md).
* Chcete-li sloučit několik šablon, přečtěte si téma [použití propojených šablon s Azure Resource Managerem](resource-group-linked-templates.md).
* K iteraci zadaného počtu opakování při vytváření konkrétní typ prostředku, naleznete v tématu [vytvořit více instancí prostředku v Azure Resource Manageru](resource-group-create-multiple.md).
* Postup nasazení šablony, které jste vytvořili, najdete v sekci [nasazení aplikace pomocí šablony Azure Resource Manageru](resource-group-template-deploy.md).

