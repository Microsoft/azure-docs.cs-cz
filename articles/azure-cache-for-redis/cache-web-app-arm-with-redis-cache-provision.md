---
title: Zřízení webové aplikace s využitím mezipaměti Azure pro Redis
description: Použijte šablonu Azure Resource Manager k nasazení webové aplikace s mezipamětí Azure cache pro Redis.
services: app-service
author: yegu-ms
ms.service: app-service
ms.topic: conceptual
ms.date: 01/06/2017
ms.author: yegu
ms.openlocfilehash: ec8d4f5611425734974d07ae6ee7008b10b9b406
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "85833770"
---
# <a name="create-a-web-app-plus-azure-cache-for-redis-using-a-template"></a>Vytvoření webové aplikace a mezipaměti Azure pro Redis pomocí šablony

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

V tomto tématu se dozvíte, jak vytvořit šablonu Azure Resource Manager, která nasadí webovou aplikaci Azure s mezipamětí Azure cache pro Redis. Naučíte se, jak definovat, které prostředky se nasazují a jak definovat parametry, které jsou zadané při spuštění nasazení. Tuto šablonu můžete použít pro vlastní nasazení nebo ji upravit, aby splňovala vaše požadavky.

Další informace o vytváření šablon najdete v tématu [vytváření šablon Azure Resource Manager](../azure-resource-manager/templates/template-syntax.md). Další informace o syntaxi a vlastnostech JSON pro typy prostředků mezipaměti najdete v tématu [typy prostředků Microsoft. cache](/azure/templates/microsoft.cache/allversions).

Úplnou šablonu najdete v tématu [Webová aplikace s využitím šablony Azure cache pro Redis](https://github.com/Azure/azure-quickstart-templates/blob/master/201-web-app-with-redis-cache/azuredeploy.json).

## <a name="what-you-will-deploy"></a>Co budete nasazovat
V této šabloně nasadíte:

* Webová aplikace Azure
* Azure Cache for Redis

Pokud chcete nasazení spustit automaticky, klikněte na následující tlačítko:

[![Nasazení do Azure](./media/cache-web-app-arm-with-redis-cache-provision/deploybutton.png)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F201-web-app-with-redis-cache%2Fazuredeploy.json)

## <a name="parameters-to-specify"></a>Parametry, které se mají zadat
[!INCLUDE [app-service-web-deploy-web-parameters](../../includes/app-service-web-deploy-web-parameters.md)]

[!INCLUDE [cache-deploy-parameters](../../includes/cache-deploy-parameters.md)]

## <a name="variables-for-names"></a>Proměnné pro názvy
Tato šablona používá proměnné pro konstrukci názvů prostředků. Pomocí funkce [uniqueString](../azure-resource-manager/templates/template-functions-string.md#uniquestring) vytvoří hodnotu na základě ID skupiny prostředků.

```json
"variables": {
  "hostingPlanName": "[concat('hostingplan', uniqueString(resourceGroup().id))]",
  "webSiteName": "[concat('webSite', uniqueString(resourceGroup().id))]",
  "cacheName": "[concat('cache', uniqueString(resourceGroup().id))]"
},
```


## <a name="resources-to-deploy"></a>Prostředky k nasazení
[!INCLUDE [app-service-web-deploy-web-host](../../includes/app-service-web-deploy-web-host.md)]

### <a name="azure-cache-for-redis"></a>Azure Cache for Redis
Vytvoří službu Azure cache pro Redis, která se používá s webovou aplikací. Název mezipaměti je zadaný v proměnné **cache** .

Šablona vytvoří mezipaměť ve stejném umístění jako skupina prostředků.

```json
{
  "name": "[variables('cacheName')]",
  "type": "Microsoft.Cache/Redis",
  "location": "[resourceGroup().location]",
  "apiVersion": "2015-08-01",
  "dependsOn": [ ],
  "tags": {
    "displayName": "cache"
  },
  "properties": {
    "sku": {
      "name": "[parameters('cacheSKUName')]",
      "family": "[parameters('cacheSKUFamily')]",
      "capacity": "[parameters('cacheSKUCapacity')]"
    }
  }
}
```


### <a name="web-app"></a>Webová aplikace
Vytvoří webovou aplikaci s názvem zadaným v proměnné **webpracoviště** .

Všimněte si, že webová aplikace je nakonfigurovaná s vlastnostmi nastavení aplikace, které jim umožňují pracovat s mezipamětí Azure pro Redis. Tato nastavení aplikace se dynamicky vytvářejí na základě hodnot poskytovaných během nasazování.

```json
{
  "apiVersion": "2015-08-01",
  "name": "[variables('webSiteName')]",
  "type": "Microsoft.Web/sites",
  "location": "[resourceGroup().location]",
  "dependsOn": [
    "[concat('Microsoft.Web/serverFarms/', variables('hostingPlanName'))]",
    "[concat('Microsoft.Cache/Redis/', variables('cacheName'))]"
  ],
  "tags": {
    "[concat('hidden-related:', resourceGroup().id, '/providers/Microsoft.Web/serverfarms/', variables('hostingPlanName'))]": "empty",
    "displayName": "Website"
  },
  "properties": {
    "name": "[variables('webSiteName')]",
    "serverFarmId": "[resourceId('Microsoft.Web/serverfarms', variables('hostingPlanName'))]"
  },
  "resources": [
    {
      "apiVersion": "2015-08-01",
      "type": "config",
      "name": "appsettings",
      "dependsOn": [
        "[concat('Microsoft.Web/Sites/', variables('webSiteName'))]",
        "[concat('Microsoft.Cache/Redis/', variables('cacheName'))]"
      ],
      "properties": {
       "CacheConnection": "[concat(variables('cacheName'),'.redis.cache.windows.net,abortConnect=false,ssl=true,password=', listKeys(resourceId('Microsoft.Cache/Redis', variables('cacheName')), '2015-08-01').primaryKey)]"
      }
    }
  ]
}
```

## <a name="commands-to-run-deployment"></a>Příkazy pro spuštění nasazení
[!INCLUDE [app-service-deploy-commands](../../includes/app-service-deploy-commands.md)]

### <a name="powershell"></a>PowerShell

```azurepowershell
New-AzResourceGroupDeployment -TemplateUri https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/201-web-app-with-redis-cache/azuredeploy.json -ResourceGroupName ExampleDeployGroup
```

### <a name="azure-cli"></a>Azure CLI

```azurecli
azure group deployment create --template-uri https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/201-web-app-with-redis-cache/azuredeploy.json -g ExampleDeployGroup
```
