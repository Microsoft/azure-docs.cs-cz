---
title: Zřídit Redis Cache pomocí Azure Resource Manager | Microsoft Docs
description: Šablona Azure Resource Manager k nasazení Azure Redis Cache.
services: app-service
documentationcenter: ''
author: wesmc7777
manager: cfowler
editor: ''
ms.assetid: ce6f5372-7038-4655-b1c5-108f7c148282
ms.service: cache
ms.workload: web
ms.tgt_pltfrm: cache-redis
ms.devlang: na
ms.topic: article
ms.date: 01/23/2017
ms.author: wesmc
ms.openlocfilehash: b26116b974abbfe410b0a6ebc0186d73f4eea1bf
ms.sourcegitcommit: 2a70752d0987585d480f374c3e2dba0cd5097880
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/19/2018
ms.locfileid: "27910737"
---
# <a name="create-a-redis-cache-using-a-template"></a>Vytvoření mezipaměti Redis Cache pomocí šablony
V tomto tématu se dozvíte, jak vytvořit šablonu Azure Resource Manager, která nasazuje Azure Redis Cache. Mezipaměti umožňuje s existujícím účtem úložiště zachovat diagnostická data. Také zjistíte, jak definovat prostředky, ke kterým nasazených a jak definovat parametry, které jsou zadané, když se spustí nasazení. Tuto šablonu můžete použít pro vlastní nasazení nebo ji upravit, aby splňovala vaše požadavky.

Nastavení diagnostiky se v současné době jsou sdíleny pro všechny mezipaměti ve stejné oblasti pro předplatné. Aktualizace mezipaměti jeden v oblasti ovlivňuje všechny mezipaměti v oblasti.

Další informace o vytváření šablon najdete v tématu [vytváření šablon Azure Resource Manager](../azure-resource-manager/resource-group-authoring-templates.md).

Pro úplnou šablonu, najdete v části [Redis Cache šablony](https://github.com/Azure/azure-quickstart-templates/blob/master/101-redis-cache/azuredeploy.json).

> [!NOTE]
> Šablony Resource Manageru pro nový [úroveň Premium](cache-premium-tier-intro.md) jsou k dispozici. 
> 
> * [Vytvořit Premium Redis Cache s clusteringem](https://azure.microsoft.com/documentation/templates/201-redis-premium-cluster-diagnostics/)
> * [Vytvoření pomocí trvalosti dat Premium Redis Cache](https://azure.microsoft.com/documentation/templates/201-redis-premium-persistence/)
> * [Vytvořit Premium Redis Cache s virtuální sítí a volitelné clustering](https://azure.microsoft.com/documentation/templates/201-redis-premium-vnet-cluster-diagnostics/)
> 
> Vyhledat nejnovější šablony, najdete v tématu [šablon Azure rychlý Start](https://azure.microsoft.com/documentation/templates/) a vyhledejte řetězec `Redis Cache`.
> 
> 

## <a name="what-you-will-deploy"></a>Co budete nasazovat
V této šabloně nasadíte Azure Redis Cache, který používá stávající účet úložiště pro diagnostická data.

Pokud chcete nasazení spustit automaticky, klikněte na následující tlačítko:

[![Nasazení do Azure](./media/cache-redis-cache-arm-provision/deploybutton.png)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-redis-cache%2Fazuredeploy.json)

## <a name="parameters"></a>Parametry
Pomocí Azure Resource Manageru definujete parametry pro hodnoty, které chcete zadat při nasazení šablony. Šablona obsahuje oddíl s názvem parametry obsahující všechny hodnoty parametru.
Parametr byste měli definovat pro hodnoty, které se mění v závislosti na nasazovaném projektu nebo prostředí, do kterého nasazujete. Nedefinujte parametry pro hodnoty, které jsou vždy stejné. Každá hodnota parametru se v šabloně použije k definování nasazovaných prostředků. 

[!INCLUDE [app-service-web-deploy-redis-parameters](../../includes/cache-deploy-parameters.md)]

### <a name="rediscachelocation"></a>redisCacheLocation
Umístění mezipaměti Redis. Pro zajištění nejlepšího výkonu používalo stejné umístění jako aplikace pro použití s mezipamětí.

    "redisCacheLocation": {
      "type": "string"
    }

### <a name="existingdiagnosticsstorageaccountname"></a>existingDiagnosticsStorageAccountName
Název existující účet úložiště pro diagnostiku. 

    "existingDiagnosticsStorageAccountName": {
      "type": "string"
    }

### <a name="enablenonsslport"></a>enableNonSslPort
Logická hodnota, která určuje, jestli se má povolit přístup přes porty bez SSL.

    "enableNonSslPort": {
      "type": "bool"
    }

### <a name="diagnosticsstatus"></a>diagnosticsStatus
Hodnota, která určuje, zda je povoleno diagnostiky. Použití ON nebo OFF.

    "diagnosticsStatus": {
      "type": "string",
      "defaultValue": "ON",
      "allowedValues": [
            "ON",
            "OFF"
        ]
    }

## <a name="resources-to-deploy"></a>Prostředky k nasazení
### <a name="redis-cache"></a>Redis Cache
Vytvoří mezipaměť Redis systému Azure.

    {
      "apiVersion": "2015-08-01",
      "name": "[parameters('redisCacheName')]",
      "type": "Microsoft.Cache/Redis",
      "location": "[parameters('redisCacheLocation')]",
      "properties": {
        "enableNonSslPort": "[parameters('enableNonSslPort')]",
        "sku": {
          "capacity": "[parameters('redisCacheCapacity')]",
          "family": "[parameters('redisCacheFamily')]",
          "name": "[parameters('redisCacheSKU')]"
        }
      },
      "resources": [
        {
          "apiVersion": "2015-07-01",
          "type": "Microsoft.Cache/redis/providers/diagnosticsettings",
          "name": "[concat(parameters('redisCacheName'), '/Microsoft.Insights/service')]",
          "location": "[parameters('redisCacheLocation')]",
          "dependsOn": [
            "[concat('Microsoft.Cache/Redis/', parameters('redisCacheName'))]"
          ],
          "properties": {
            "status": "[parameters('diagnosticsStatus')]",
            "storageAccountName": "[parameters('existingDiagnosticsStorageAccountName')]"
          }
        }
      ]
    }



## <a name="commands-to-run-deployment"></a>Příkazy pro spuštění nasazení
[!INCLUDE [app-service-deploy-commands](../../includes/app-service-deploy-commands.md)]

### <a name="powershell"></a>PowerShell
    New-AzureRmResourceGroupDeployment -TemplateUri https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-redis-cache/azuredeploy.json -ResourceGroupName ExampleDeployGroup -redisCacheName ExampleCache

### <a name="azure-cli"></a>Azure CLI
    azure group deployment create --template-uri https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-redis-cache/azuredeploy.json -g ExampleDeployGroup


