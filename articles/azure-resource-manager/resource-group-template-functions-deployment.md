---
title: Funkce šablon – nasazení
description: Popisuje funkce, které se použijí v šabloně Azure Resource Manager k načtení informací o nasazení.
ms.topic: conceptual
ms.date: 09/13/2019
ms.openlocfilehash: 17caf78fb77e330685bb45ab03aaeed611900ba0
ms.sourcegitcommit: 5cfe977783f02cd045023a1645ac42b8d82223bd
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/17/2019
ms.locfileid: "74149636"
---
# <a name="deployment-functions-for-azure-resource-manager-templates"></a>Funkce nasazení pro šablony Azure Resource Manager 

Správce prostředků poskytuje následující funkce pro získání hodnot z částí šablony a hodnot souvisejících s nasazením:

* [prostředí](#deployment)
* [parameters](#parameters)
* [variables](#variables)

Pokud chcete získat hodnoty z prostředků, skupin prostředků nebo předplatných, přečtěte si téma [funkce prostředků](resource-group-template-functions-resource.md).

<a id="deployment" />

## <a name="deployment"></a>deployment
`deployment()`

Vrátí informace o aktuální operaci nasazení.

### <a name="return-value"></a>Návratová hodnota

Tato funkce vrací objekt, který je předán během nasazování. Vlastnosti ve vráceném objektu se liší v závislosti na tom, zda je objekt nasazení předán jako odkaz nebo jako vložený objekt. Když je objekt nasazení předán do řádku, například při použití parametru **-TemplateFile** v Azure PowerShell k odkazování na místní soubor, vrácený objekt má následující formát:

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

Pokud je objekt předán jako odkaz, například při použití parametru **-TemplateUri** k nasměrování na vzdálený objekt, je objekt vrácen v následujícím formátu: 

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

Když [nasadíte do předplatného Azure](deploy-to-subscription.md)místo skupiny prostředků, návratový objekt zahrnuje vlastnost `location`. Vlastnost Location je obsažena při nasazení místní šablony nebo externí šablony.

### <a name="remarks"></a>Poznámky

Nasazení () můžete použít k propojení na jinou šablonu na základě identifikátoru URI nadřazené šablony.

```json
"variables": {  
    "sharedTemplateUrl": "[uri(deployment().properties.templateLink.uri, 'shared-resources.json')]"  
}
```  

Pokud šablonu znovu nasadíte z historie nasazení na portálu, šablona bude nasazena jako místní soubor. Vlastnost `templateLink` není ve funkci nasazení vrácena. Pokud vaše šablona spoléhá na `templateLink` vytvoření odkazu na jinou šablonu, nepoužívejte portál k opětovnému nasazení. Místo toho použijte příkazy, které jste použili k původnímu nasazení šablony.

### <a name="example"></a>Příklad

Následující [příklad šablony](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/deployment.json) vrátí objekt nasazení:

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

Předchozí příklad vrátí následující objekt:

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

Šablonu na úrovni předplatného, která používá funkci nasazení, najdete v tématu [funkce nasazení předplatného](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/deploymentsubscription.json). Je nasazena buď pomocí příkazů `az deployment create`, nebo `New-AzDeployment`.

<a id="parameters" />

## <a name="parameters"></a>parameters
`parameters(parameterName)`

Vrátí hodnotu parametru. Zadaný název parametru musí být definován v oddílu Parameters této šablony.

### <a name="parameters"></a>Parametry

| Parametr | Požaduje se | Typ | Popis |
|:--- |:--- |:--- |:--- |
| parameterName |Ano |řetězec |Název parametru, který se má vrátit. |

### <a name="return-value"></a>Návratová hodnota

Hodnota zadaného parametru.

### <a name="remarks"></a>Poznámky

Obvykle použijete parametry pro nastavení hodnot prostředků. Následující příklad nastaví název webu na hodnotu parametru předaného během nasazení.

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

### <a name="example"></a>Příklad

Následující [příklad šablony](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/parameters.json) ukazuje zjednodušené použití funkce Parameters.

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
| stringOutput | Řetězec | Možnost 1 |
| intOutput | Int | 1 |
| objectOutput | Objekt | {"One": "a", "Two": "b"} |
| arrayOutput | Pole | [1, 2, 3] |
| crossOutput | Řetězec | Možnost 1 |

Další informace o použití parametrů naleznete v tématu [Parameters in Azure Resource Manager Template](template-parameters.md).

<a id="variables" />

## <a name="variables"></a>proměnné
`variables(variableName)`

Vrátí hodnotu proměnné. Zadaný název proměnné musí být definován v oddílu Variables šablony.

### <a name="parameters"></a>Parametry

| Parametr | Požaduje se | Typ | Popis |
|:--- |:--- |:--- |:--- |
| variableName |Ano |Řetězec |Název proměnné, která se má vrátit. |

### <a name="return-value"></a>Návratová hodnota

Hodnota zadané proměnné.

### <a name="remarks"></a>Poznámky

Obvykle používáte proměnné pro zjednodušení šablony vytvořením složitých hodnot pouze jednou. Následující příklad vytvoří jedinečný název pro účet úložiště.

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

### <a name="example"></a>Příklad

Následující [příklad šablony](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/variables.json) vrací různé hodnoty proměnných.

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
| exampleOutput1 | Řetězec | MojePromenna |
| exampleOutput2 | Pole | [1, 2, 3, 4] |
| exampleOutput3 | Řetězec | MojePromenna |
| exampleOutput4 |  Objekt | {"vlastnost1": "hodnota1"; "Vlastnost2": "hodnota2"} |

Další informace o použití proměnných naleznete v tématu [proměnné v šabloně Azure Resource Manager](template-variables.md).

## <a name="next-steps"></a>Další kroky
* Popis části šablony Azure Resource Manageru najdete v tématu [šablon pro vytváření Azure Resource Manageru](resource-group-authoring-templates.md).
* Chcete-li sloučit několik šablon, přečtěte si téma [použití propojených šablon s Azure Resource Manager](resource-group-linked-templates.md).
* K iteraci zadaného počtu opakování při vytváření konkrétní typ prostředku, naleznete v tématu [vytvořit více instancí prostředku v Azure Resource Manageru](resource-group-create-multiple.md).
* Postup nasazení šablony, které jste vytvořili, najdete v sekci [nasazení aplikace pomocí šablony Azure Resource Manageru](resource-group-template-deploy.md).

