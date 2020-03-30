---
title: Nasazení mezipaměti Azure pro Redis pomocí Správce prostředků Azure
description: Zjistěte, jak použít šablonu Azure Resource Managerka k nasazení prostředku Azure Cache for Redis. Šablony jsou k dispozici pro běžné scénáře.
author: yegu-ms
ms.author: yegu
ms.service: cache
ms.topic: conceptual
ms.date: 01/23/2017
ms.openlocfilehash: 787edf662aa3a34e167db61b0a89dfc5c2944219
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "75412410"
---
# <a name="create-an-azure-cache-for-redis-using-a-template"></a>Vytvoření mezipaměti Azure pro Redis pomocí šablony

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

V tomto tématu se dozvíte, jak vytvořit šablonu Azure Resource Manager, která nasazuje Azure Cache pro Redis. Mezipaměť lze použít s existujícím účtem úložiště k uchování diagnostických dat. Dozvíte se také, jak definovat, které prostředky jsou nasazeny a jak definovat parametry, které jsou určeny při spuštění nasazení. Tuto šablonu můžete použít pro vlastní nasazení nebo ji upravit, aby splňovala vaše požadavky.

V současné době jsou diagnostická nastavení sdílena pro všechny mezipaměti ve stejné oblasti pro odběr. Aktualizace jedné mezipaměti v oblasti ovlivní všechny ostatní mezipaměti v oblasti.

Další informace o vytváření šablon najdete v [tématu Vytváření šablon Azure Resource Manageru](../azure-resource-manager/templates/template-syntax.md). Informace o syntaxi JSON a vlastnostech pro typy prostředků mezipaměti naleznete v [tématu Microsoft.Cache typy prostředků](/azure/templates/microsoft.cache/allversions).

Kompletní šablonu najdete v tématu [Azure Cache for Redis template](https://github.com/Azure/azure-quickstart-templates/blob/master/101-redis-cache/azuredeploy.json).

> [!NOTE]
> Šablony Správce prostředků pro novou [úroveň Premium](cache-premium-tier-intro.md) jsou k dispozici. 
> 
> * [Vytvoření prémiové mezipaměti Azure pro redis pomocí clusteringu](https://azure.microsoft.com/resources/templates/201-redis-premium-cluster-diagnostics/)
> * [Vytvoření prémiové mezipaměti Azure pro redis s trvalostí dat](https://azure.microsoft.com/resources/templates/201-redis-premium-persistence/)
> * [Vytvoření mezipaměti Premium Redis nasazené do virtuální sítě](https://azure.microsoft.com/resources/templates/201-redis-premium-vnet/)
> 
> Nejnovější šablony najdete v [tématu Šablony rychlého](https://azure.microsoft.com/documentation/templates/) `Azure Cache for Redis`spuštění Azure a vyhledejte .
> 
> 

## <a name="what-you-will-deploy"></a>Co nasadíte
V této šabloně nasadíte Azure Cache pro Redis, který používá existující účet úložiště pro diagnostická data.

Pokud chcete nasazení spustit automaticky, klikněte na následující tlačítko:

[![Nasazení do Azure](./media/cache-redis-cache-arm-provision/deploybutton.png)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-redis-cache%2Fazuredeploy.json)

## <a name="parameters"></a>Parametry
Pomocí Azure Resource Manageru definujete parametry pro hodnoty, které chcete zadat při nasazení šablony. Šablona obsahuje oddíl s názvem Parametry, který obsahuje všechny hodnoty parametrů.
Parametr byste měli definovat pro hodnoty, které se mění v závislosti na nasazovaném projektu nebo prostředí, do kterého nasazujete. Nedefinujte parametry pro hodnoty, které jsou vždy stejné. Každá hodnota parametru se v šabloně použije k definování nasazovaných prostředků. 

[!INCLUDE [app-service-web-deploy-redis-parameters](../../includes/cache-deploy-parameters.md)]

### <a name="rediscachelocation"></a>redisCacheLocation
Umístění azure mezipaměti pro Redis. Chcete-li dosáhnout nejlepšího výkonu, použijte stejné umístění jako aplikace, která se má používat s mezipamětí.

    "redisCacheLocation": {
      "type": "string"
    }

### <a name="existingdiagnosticsstorageaccountname"></a>existingDiagnosticsStorageAccountName
Název existujícího účtu úložiště, který se má použít pro diagnostiku. 

    "existingDiagnosticsStorageAccountName": {
      "type": "string"
    }

### <a name="enablenonsslport"></a>enableNonSslPort
Logická hodnota, která označuje, zda má být povolen přístup prostřednictvím portů bez SSL.

    "enableNonSslPort": {
      "type": "bool"
    }

### <a name="diagnosticsstatus"></a>diagnosticsStav
Hodnota, která označuje, zda je povolena diagnostika. Použijte ZAPNUTO nebo VYPNUTO.

    "diagnosticsStatus": {
      "type": "string",
      "defaultValue": "ON",
      "allowedValues": [
            "ON",
            "OFF"
        ]
    }

## <a name="resources-to-deploy"></a>Prostředky k nasazení
### <a name="azure-cache-for-redis"></a>Azure Cache for Redis
Vytvoří mezipaměť Azure pro Redis.

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
          "apiVersion": "2017-05-01-preview",
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

    New-AzResourceGroupDeployment -TemplateUri https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-redis-cache/azuredeploy.json -ResourceGroupName ExampleDeployGroup -redisCacheName ExampleCache

### <a name="azure-cli"></a>Azure CLI
    azure group deployment create --template-uri https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-redis-cache/azuredeploy.json -g ExampleDeployGroup
