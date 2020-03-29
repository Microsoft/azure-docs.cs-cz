---
title: Funkce šablony – nasazení
description: Popisuje funkce, které se mají použít v šabloně Azure Resource Manager k načtení informací o nasazení.
ms.topic: conceptual
ms.date: 11/27/2019
ms.openlocfilehash: 86a1d3d7e05fedacd7a3c044ecab241ca9d059c5
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "80156323"
---
# <a name="deployment-functions-for-arm-templates"></a>Funkce nasazení pro šablony ARM 

Správce prostředků poskytuje následující funkce pro získávání hodnot souvisejících s aktuálním nasazením šablony Správce prostředků Azure (ARM):

* [Nasazení](#deployment)
* [environment](#environment)
* [Parametry](#parameters)
* [Proměnné](#variables)

Informace o získání hodnot z prostředků, skupin prostředků nebo předplatných naleznete v [tématu Funkce prostředků](template-functions-resource.md).

## <a name="deployment"></a>nasazení

`deployment()`

Vrátí informace o aktuální operaci nasazení.

### <a name="return-value"></a>Návratová hodnota

Tato funkce vrátí objekt, který je předán během nasazení. Vlastnosti v vráceném objektu se liší v závislosti na tom, zda je objekt nasazení předán jako odkaz nebo jako in-line objekt. Když je objekt nasazení předán in-line, například při použití parametru **-TemplateFile** v prostředí Azure PowerShell k přejděte na místní soubor, má vrácený objekt následující formát:

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

Když je objekt předán jako odkaz, například při použití parametru **-TemplateUri** k přemísťuje na vzdálený objekt, je objekt vrácen v následujícím formátu: 

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

Při [nasazení do předplatného Azure](deploy-to-subscription.md), namísto skupiny prostředků, návratový objekt obsahuje `location` vlastnost. Vlastnost umístění je zahrnuta při nasazování místní šablony nebo externí šablony.

### <a name="remarks"></a>Poznámky

Nasazení() můžete použít k propojení s jinou šablonou na základě identifikátoru URI nadřazené šablony.

```json
"variables": {  
    "sharedTemplateUrl": "[uri(deployment().properties.templateLink.uri, 'shared-resources.json')]"  
}
```  

Pokud znovu nasadíte šablonu z historie nasazení na portálu, šablona se nasadí jako místní soubor. Vlastnost `templateLink` není vrácena ve funkci nasazení. Pokud vaše šablona `templateLink` závisí na vytvoření odkazu na jinou šablonu, nepoužívejte portál k opětovnému nasazení. Místo toho použijte příkazy, které jste použili k původnímu nasazení šablony.

### <a name="example"></a>Příklad

Následující [šablona příkladu](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/deployment.json) vrátí objekt nasazení:

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

Šablona na úrovni předplatného, která používá funkci nasazení, najdete v [tématu funkce nasazení předplatného](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/deploymentsubscription.json). Je nasazen s jedním `az deployment create` `New-AzDeployment` nebo příkazy.

## <a name="environment"></a>environment

`environment()`

Vrátí informace o prostředí Azure používané pro nasazení.

### <a name="return-value"></a>Návratová hodnota

Tato funkce vrátí vlastnosti pro aktuální prostředí Azure. Následující příklad ukazuje vlastnosti pro globální Azure. Suverénní mraky mohou vracet mírně odlišné vlastnosti.

```json
{
  "name": "",
  "gallery": "",
  "graph": "",
  "portal": "",
  "graphAudience": "",
  "activeDirectoryDataLake": "",
  "batch": "",
  "media": "",
  "sqlManagement": "",
  "vmImageAliasDoc": "",
  "resourceManager": "",
  "authentication": {
    "loginEndpoint": "",
    "audiences": [
      "",
      ""
    ],
    "tenant": "",
    "identityProvider": ""
  },
  "suffixes": {
    "acrLoginServer": "",
    "azureDatalakeAnalyticsCatalogAndJob": "",
    "azureDatalakeStoreFileSystem": "",
    "azureFrontDoorEndpointSuffix": "",
    "keyvaultDns": "",
    "sqlServerHostname": "",
    "storage": ""
  }
}
```

### <a name="example"></a>Příklad

Následující ukázková šablona vrátí objekt prostředí.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "resources": [],
    "outputs": {
        "environmentOutput": {
            "value": "[environment()]",
            "type" : "object"
        }
    }
}
```

Předchozí příklad vrátí následující objekt při nasazení do globálního Azure:

```json
{
  "name": "AzureCloud",
  "gallery": "https://gallery.azure.com/",
  "graph": "https://graph.windows.net/",
  "portal": "https://portal.azure.com",
  "graphAudience": "https://graph.windows.net/",
  "activeDirectoryDataLake": "https://datalake.azure.net/",
  "batch": "https://batch.core.windows.net/",
  "media": "https://rest.media.azure.net",
  "sqlManagement": "https://management.core.windows.net:8443/",
  "vmImageAliasDoc": "https://raw.githubusercontent.com/Azure/azure-rest-api-specs/master/arm-compute/quickstart-templates/aliases.json",
  "resourceManager": "https://management.azure.com/",
  "authentication": {
    "loginEndpoint": "https://login.windows.net/",
    "audiences": [
      "https://management.core.windows.net/",
      "https://management.azure.com/"
    ],
    "tenant": "common",
    "identityProvider": "AAD"
  },
  "suffixes": {
    "acrLoginServer": ".azurecr.io",
    "azureDatalakeAnalyticsCatalogAndJob": "azuredatalakeanalytics.net",
    "azureDatalakeStoreFileSystem": "azuredatalakestore.net",
    "azureFrontDoorEndpointSuffix": "azurefd.net",
    "keyvaultDns": ".vault.azure.net",
    "sqlServerHostname": ".database.windows.net",
    "storage": "core.windows.net"
  }
}
```

## <a name="parameters"></a>parameters

`parameters(parameterName)`

Vrátí hodnotu parametru. Zadaný název parametru musí být definován v části parametry šablony.

### <a name="parameters"></a>Parametry

| Parametr | Požaduje se | Typ | Popis |
|:--- |:--- |:--- |:--- |
| Parametername |Ano |řetězec |Název parametru, který chcete vrátit. |

### <a name="return-value"></a>Návratová hodnota

Hodnota zadaného parametru.

### <a name="remarks"></a>Poznámky

Obvykle se parametry používají k nastavení hodnot prostředků. Následující příklad nastaví název webu na hodnotu parametru předanou během nasazení.

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

Následující [ukázková šablona](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/parameters.json) ukazuje zjednodušené použití funkce parametrů.

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

| Name (Název) | Typ | Hodnota |
| ---- | ---- | ----- |
| stringOutput | Řetězec | možnost 1 |
| intOutput | Int | 1 |
| objectOutput | Objekt | {"one": "a", "dva": "b"} |
| poleOutput | Pole | [1, 2, 3] |
| crossOutput | Řetězec | možnost 1 |

Další informace o použití parametrů najdete [v tématu Parametry v šabloně Správce prostředků Azure](template-parameters.md).

## <a name="variables"></a>Proměnné

`variables(variableName)`

Vrátí hodnotu proměnné. Zadaný název proměnné musí být definován v části proměnné šablony.

### <a name="parameters"></a>Parametry

| Parametr | Požaduje se | Typ | Popis |
|:--- |:--- |:--- |:--- |
| variableName |Ano |Řetězec |Název proměnné vrátit. |

### <a name="return-value"></a>Návratová hodnota

Hodnota zadané proměnné.

### <a name="remarks"></a>Poznámky

Proměnné se obvykle používají ke zjednodušení šablony vytvořením složitých hodnot pouze jednou. Následující příklad vytvoří jedinečný název pro účet úložiště.

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

Následující [ukázková šablona](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/variables.json) vrátí různé hodnoty proměnných.

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

| Name (Název) | Typ | Hodnota |
| ---- | ---- | ----- |
| exampleOutput1 | Řetězec | myVariable |
| exampleOutput2 | Pole | [1, 2, 3, 4] |
| exampleOutput3 | Řetězec | myVariable |
| exampleOutput4 |  Objekt | {"property1": "value1", "property2": "value2"} |

Další informace o používání proměnných najdete [v tématu Proměnné v šabloně Správce prostředků Azure](template-variables.md).

## <a name="next-steps"></a>Další kroky
* Popis oddílů v šabloně Azure Resource Manager u najdete v tématu [Vytváření šablon Azure Resource Manageru](template-syntax.md).
* Pokud chcete sloučit několik šablon, přečtěte si informace [o použití propojených šablon ve Správci prostředků Azure](linked-templates.md).
* Chcete-li itrerate zadaný počet časů při vytváření typu prostředku, najdete v [tématu vytvoření více instancí prostředků ve Správci prostředků Azure](copy-resources.md).
* Informace o tom, jak nasadit vytvořenou šablonu, najdete [v tématu Nasazení aplikace pomocí šablony Azure Resource Manageru](deploy-powershell.md).

