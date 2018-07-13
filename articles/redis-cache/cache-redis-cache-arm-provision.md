---
title: Zřízení mezipaměti redis Cache pomocí Azure Resource Manageru | Dokumentace Microsoftu
description: K nasazení Azure Redis Cache pomocí šablony Azure Resource Manageru.
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
ms.sourcegitcommit: 0a84b090d4c2fb57af3876c26a1f97aac12015c5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/11/2018
ms.locfileid: "38452579"
---
# <a name="create-a-redis-cache-using-a-template"></a>Vytvoření mezipaměti Redis Cache pomocí šablony
V tomto tématu se dozvíte, jak vytvořit šablonu Azure Resource Manageru, který nasadí Azure Redis Cache. Mezipaměti můžete použít s existující účet úložiště k uložení diagnostických dat. Také zjistíte, jak definovat prostředků, které jsou nasazené a tom, jak definovat parametry, které jsou zadané při spuštění nasazení. Tuto šablonu můžete použít pro vlastní nasazení nebo ji upravit, aby splňovala vaše požadavky.

V současné době jsou sdíleny nastavení diagnostiky pro všechny mezipaměti ve stejné oblasti pro odběr. Aktualizuje se mezipaměť jeden v oblasti má vliv na všechny mezipaměti v oblasti.

Další informace o vytváření šablon najdete v tématu [vytváření šablon Azure Resource Manageru](../azure-resource-manager/resource-group-authoring-templates.md).

Úplnou šablonu najdete v části [šablony služby Redis Cache](https://github.com/Azure/azure-quickstart-templates/blob/master/101-redis-cache/azuredeploy.json).

> [!NOTE]
> Šablony Resource Manageru pro nové [úroveň Premium](cache-premium-tier-intro.md) jsou k dispozici. 
> 
> * [Vytvoření mezipaměti redis Cache Premium s clusteringem](https://azure.microsoft.com/documentation/templates/201-redis-premium-cluster-diagnostics/)
> * [Vytvoření mezipaměti redis Cache Premium s trvalostí dat](https://azure.microsoft.com/documentation/templates/201-redis-premium-persistence/)
> * [Vytvoření mezipaměti redis Cache Premium s virtuální sítí a volitelné clustering](https://azure.microsoft.com/documentation/templates/201-redis-premium-vnet-cluster-diagnostics/)
> 
> Pro nejnovější šablony, najdete v části [šablony pro rychlý start Azure](https://azure.microsoft.com/documentation/templates/) a vyhledejte `Redis Cache`.
> 
> 

## <a name="what-you-will-deploy"></a>Co budete nasazovat
V této šabloně nasadíte Azure Redis Cache, která používá existující účet úložiště pro diagnostická data.

Pokud chcete nasazení spustit automaticky, klikněte na následující tlačítko:

[![Nasazení do Azure](./media/cache-redis-cache-arm-provision/deploybutton.png)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-redis-cache%2Fazuredeploy.json)

## <a name="parameters"></a>Parametry
Pomocí Azure Resource Manageru definujete parametry pro hodnoty, které chcete zadat při nasazení šablony. Šablona obsahuje část s názvem parametry, které obsahuje všechny hodnoty parametrů.
Parametr byste měli definovat pro hodnoty, které se mění v závislosti na nasazovaném projektu nebo prostředí, do kterého nasazujete. Nedefinujte parametry pro hodnoty, které jsou vždy stejné. Každá hodnota parametru se v šabloně použije k definování nasazovaných prostředků. 

[!INCLUDE [app-service-web-deploy-redis-parameters](../../includes/cache-deploy-parameters.md)]

### <a name="rediscachelocation"></a>redisCacheLocation
Umístění mezipaměti redis Cache. Pro zajištění nejlepšího výkonu použijte stejné umístění jako aplikace pro použití s mezipamětí.

    "redisCacheLocation": {
      "type": "string"
    }

### <a name="existingdiagnosticsstorageaccountname"></a>existingDiagnosticsStorageAccountName
Název existující účet úložiště pro účely diagnostiky. 

    "existingDiagnosticsStorageAccountName": {
      "type": "string"
    }

### <a name="enablenonsslport"></a>enableNonSslPort
Logická hodnota, která určuje, jestli se má povolit přístup přes porty jiného typu než SSL.

    "enableNonSslPort": {
      "type": "bool"
    }

### <a name="diagnosticsstatus"></a>diagnosticsStatus
Hodnota, která určuje, zda je povoleno diagnostiky. Použití zapnuto nebo vypnuto.

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
Vytvoří Azure Redis Cache.

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


