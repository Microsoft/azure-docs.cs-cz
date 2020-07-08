---
title: Nasazení Azure cache pro Redis s Azure Resource Manager
description: Naučte se používat šablonu Azure Resource Manager k nasazení mezipaměti Azure pro prostředek Redis. Šablony jsou k dispozici pro běžné scénáře.
author: yegu-ms
ms.author: yegu
ms.service: cache
ms.topic: conceptual
ms.date: 01/23/2017
ms.openlocfilehash: 0dbcbd173ce0a7c4c6a123f0644b870aa3cec2f5
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/02/2020
ms.locfileid: "85829886"
---
# <a name="create-an-azure-cache-for-redis-using-a-template"></a>Vytvoření mezipaměti Azure pro Redis pomocí šablony

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

V tomto tématu se dozvíte, jak vytvořit šablonu Azure Resource Manager, která nasadí mezipaměť Azure pro Redis. Mezipaměť lze použít s existujícím účtem úložiště pro zachování diagnostických dat. Naučíte se také, jak definovat, které prostředky jsou nasazeny a jak definovat parametry, které jsou zadány při spuštění nasazení. Tuto šablonu můžete použít pro vlastní nasazení nebo ji upravit, aby splňovala vaše požadavky.

V současné době se nastavení diagnostiky sdílí pro všechny mezipaměti ve stejné oblasti pro předplatné. Aktualizace jedné mezipaměti v oblasti má vliv na všechny ostatní mezipaměti v oblasti.

Další informace o vytváření šablon najdete v tématu [vytváření šablon Azure Resource Manager](../azure-resource-manager/templates/template-syntax.md). Další informace o syntaxi a vlastnostech JSON pro typy prostředků mezipaměti najdete v tématu [typy prostředků Microsoft. cache](/azure/templates/microsoft.cache/allversions).

Úplnou šablonu najdete v tématu [Šablona Azure cache pro Redis](https://github.com/Azure/azure-quickstart-templates/blob/master/101-redis-cache/azuredeploy.json).

> [!NOTE]
> K dispozici jsou šablony Správce prostředků pro novou [úroveň Premium](cache-premium-tier-intro.md) . 
> 
> * [Vytvoření mezipaměti Azure Premium pro Redis s clusteringem](https://azure.microsoft.com/resources/templates/201-redis-premium-cluster-diagnostics/)
> * [Vytvoření mezipaměti Azure Premium pro Redis s Trvalost dat](https://azure.microsoft.com/resources/templates/201-redis-premium-persistence/)
> * [Vytvoření Redis Cache úrovně Premium nasazených do Virtual Network](https://azure.microsoft.com/resources/templates/201-redis-premium-vnet/)
> 
> Pokud chcete vyhledat nejnovější šablony, přečtěte si téma [šablony pro rychlý Start Azure](https://azure.microsoft.com/documentation/templates/) a vyhledejte `Azure Cache for Redis` .
> 
> 

## <a name="what-you-will-deploy"></a>Co budete nasazovat
V této šabloně nasadíte mezipaměť Azure pro Redis, která pro diagnostická data používá existující účet úložiště.

Pokud chcete nasazení spustit automaticky, klikněte na následující tlačítko:

[![Nasazení do Azure](./media/cache-redis-cache-arm-provision/deploybutton.png)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-redis-cache%2Fazuredeploy.json)

## <a name="parameters"></a>Parametry
Pomocí Azure Resource Manageru definujete parametry pro hodnoty, které chcete zadat při nasazení šablony. Šablona obsahuje oddíl s názvem parametry, který obsahuje všechny hodnoty parametrů.
Parametr byste měli definovat pro hodnoty, které se mění v závislosti na nasazovaném projektu nebo prostředí, do kterého nasazujete. Nedefinujte parametry pro hodnoty, které jsou vždy stejné. Každá hodnota parametru se v šabloně použije k definování nasazovaných prostředků. 

[!INCLUDE [app-service-web-deploy-redis-parameters](../../includes/cache-deploy-parameters.md)]

### <a name="rediscachelocation"></a>redisCacheLocation
Umístění mezipaměti Azure pro Redis. Nejlepšího výkonu dosáhnete, když použijete stejné umístění jako aplikace, která se má použít spolu s mezipamětí.

```json
  "redisCacheLocation": {
    "type": "string"
  }
```

### <a name="existingdiagnosticsstorageaccountname"></a>existingDiagnosticsStorageAccountName
Název existujícího účtu úložiště, který se má použít pro diagnostiku. 

```json
  "existingDiagnosticsStorageAccountName": {
    "type": "string"
  }
```

### <a name="enablenonsslport"></a>enableNonSslPort
Logická hodnota, která označuje, jestli se má povolený přístup přes porty bez SSL.

```json
  "enableNonSslPort": {
    "type": "bool"
  }
```

### <a name="diagnosticsstatus"></a>diagnosticsStatus
Hodnota, která určuje, zda je povolena Diagnostika. Použijte ZAPNUTo nebo vypnuto.

```json
  "diagnosticsStatus": {
    "type": "string",
    "defaultValue": "ON",
    "allowedValues": [
          "ON",
          "OFF"
      ]
  }
```

## <a name="resources-to-deploy"></a>Prostředky k nasazení
### <a name="azure-cache-for-redis"></a>Azure Cache for Redis
Vytvoří službu Azure cache pro Redis.

```json
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
```

## <a name="commands-to-run-deployment"></a>Příkazy pro spuštění nasazení
[!INCLUDE [app-service-deploy-commands](../../includes/app-service-deploy-commands.md)]

### <a name="powershell"></a>PowerShell

```azurepowershell
    New-AzResourceGroupDeployment -TemplateUri https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-redis-cache/azuredeploy.json -ResourceGroupName ExampleDeployGroup -redisCacheName ExampleCache
```

### <a name="azure-cli"></a>Azure CLI

```azurecli
    azure group deployment create --template-uri https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-redis-cache/azuredeploy.json -g ExampleDeployGroup
```
