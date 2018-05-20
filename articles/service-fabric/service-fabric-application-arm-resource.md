---
title: Nasazení a upgrade aplikace a služby pomocí Azure Resource Manageru | Microsoft Docs
description: Zjistěte, jak nasadit aplikace a služby pro cluster Service Fabric pomocí šablony Azure Resource Manager.
services: service-fabric
documentationcenter: .net
author: dkkapur
manager: timlt
editor: ''
ms.assetid: ''
ms.service: service-fabric
ms.devlang: dotNet
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 12/06/2017
ms.author: dekapur
ms.openlocfilehash: 8c8ee30a603d439c0fadd0c1569813762bdf4351
ms.sourcegitcommit: eb75f177fc59d90b1b667afcfe64ac51936e2638
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/16/2018
---
# <a name="manage-applications-and-services-as-azure-resource-manager-resources"></a>Spravovat aplikace a služby jako prostředky Azure Resource Manager

Aplikace a služby, můžete nasadit na cluster Service Fabric pomocí Azure Resource Manager. To znamená, že místo nasazení a Správa aplikací pomocí prostředí PowerShell nebo rozhraní příkazového řádku po museli čekat na clusteru bude připravená, teď můžete express aplikace a služby ve formátu JSON a nasadit je do stejné šablony správce prostředků jako cluster. Proces registrace aplikace, zřizování a nasazování všechny se stane v jednom kroku.

Toto je doporučený způsob můžete nasadit všechny instalační program, zásady správného řízení nebo aplikace pro správu clusteru, které vyžadují v clusteru. To zahrnuje [použití opravy Orchestration](service-fabric-patch-orchestration-application.md), Watchdogs nebo jakékoliv aplikace, které musí být před nasazením další služby nebo aplikace běžící v clusteru. 

V případě potřeby spravujte jako prostředky Resource Manageru ke zlepšení aplikace:
* Záznam pro audit: Resource Manager audity každé operace a udržuje podrobné *protokol aktivit* který vám pomůže sledovat všechny změny provedené v těchto aplikacích a cluster.
* Řízení přístupu na základě role (RBAC): Správa přístupu k clustery, jakož i aplikace nasazené na clusteru, můžete to udělat pomocí stejné šablony Resource Manageru.
* Azure Resource Manager (prostřednictvím portálu Azure) se změní jeden praktický pro správu clusteru a nasazení důležitých aplikací.

Následující fragment kódu ukazuje různé druhy prostředků, které je možné spravovat prostřednictvím šablony:

```json
{
    "apiVersion": "2017-07-01-preview",
    "type": "Microsoft.ServiceFabric/clusters/applicationTypes",
    "name": "[concat(parameters('clusterName'), '/', parameters('applicationTypeName'))]",
    "location": "[variables('clusterLocation')]",
},
{
    "apiVersion": "2017-07-01-preview",
    "type": "Microsoft.ServiceFabric/clusters/applicationTypes/versions",
    "name": "[concat(parameters('clusterName'), '/', parameters('applicationTypeName'), '/', parameters('applicationTypeVersion'))]",
    "location": "[variables('clusterLocation')]",
},
{
    "apiVersion": "2017-07-01-preview",
    "type": "Microsoft.ServiceFabric/clusters/applications",
    "name": "[concat(parameters('clusterName'), '/', parameters('applicationName'))]",
    "location": "[variables('clusterLocation')]",
},
{
    "apiVersion": "2017-07-01-preview",
    "type": "Microsoft.ServiceFabric/clusters/applications/services",
    "name": "[concat(parameters('clusterName'), '/', parameters('applicationName'), '/', parameters('serviceName'))]",
    "location": "[variables('clusterLocation')]"
}
```


## <a name="add-a-new-application-to-your-resource-manager-template"></a>Přidejte novou aplikaci do šablony Resource Manageru

1. Příprava pro nasazení šablony Resource Manageru vašeho clusteru. V tématu [vytvořit cluster Service Fabric pomocí Azure Resource Manager](service-fabric-cluster-creation-via-arm.md) pro další informace o tomto.
2. Vezměte v úvahu některé z aplikací, které plánujete nasadit v clusteru. Dochází k žádnému počítači, který bude vždy spuštěna, jiné aplikace může trvat, než se závislostí? Plánování pro nasazení žádné zásady správného řízení clusteru nebo instalační program aplikace? Tyto řazení aplikací se nejlépe spravují pomocí šablony Resource Manageru, jak je popsáno výše. 
3. Jakmile budete mít započítáno na jaké aplikace mají být nasazeny tímto způsobem, aplikace nemusí být zabalené, metoda ZIP a put ve sdílené složce. Sdílené složky musí být přístupné přes koncový bod REST pro Azure Resource Manager využívat během nasazení.
4. V šabloně Resource Manager, níže deklarace vašeho clusteru popisují vlastnosti pro každou aplikaci. Tyto vlastnosti zahrnují počet replik nebo instancí a všechny řetězy závislosti mezi prostředky (jinými aplikacemi nebo službami). Seznam vlastností, komplexní najdete v tématu [specifikace Swagger rozhraní API REST](https://aka.ms/sfrpswaggerspec). Poznámka: Tato metoda nenahrazuje aplikace nebo služba manifesty, ale spíš popisuje některé co je v nich jako součást clusteru šablony Resource Manageru. Zde je ukázka šablony, která zahrnuje nasazení bezstavové služby *Service1* a stavové služby *Service2* jako součást *Application1*:

  ```json
  {
    "$schema": "http://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json",
    "contentVersion": "1.0.0.0",
    "parameters": {
      "clusterName": {
        "type": "string",
        "defaultValue": "Cluster",
        "metadata": {
          "description": "Name of your cluster - Between 3 and 23 characters. Letters and numbers only."
        }
      },
      "applicationTypeName": {
        "type": "string",
        "defaultValue": "ApplicationType",
        "metadata": {
          "description": "The application type name."
        }
      },
      "applicationTypeVersion": {
        "type": "string",
        "defaultValue": "1",
        "metadata": {
          "description": "The application type version."
        }
      },
      "appPackageUrl": {
        "type": "string",
        "metadata": {
          "description": "The URL to the application package sfpkg file."
        }
      },
      "applicationName": {
        "type": "string",
        "defaultValue": "Application1",
        "metadata": {
          "description": "The name of the application resource."
        }
      },
      "serviceName": {
        "type": "string",
        "defaultValue": "Application1~Service1",
        "metadata": {
          "description": "The name of the service resource in the format of {applicationName}~{serviceName}."
        }
      },
      "serviceTypeName": {
        "type": "string",
        "defaultValue": "Service1Type",
        "metadata": {
          "description": "The name of the service type."
        }
      },
      "serviceName2": {
        "type": "string",
        "defaultValue": "Application1~Service2",
        "metadata": {
          "description": "The name of the service resource in the format of {applicationName}~{serviceName}."
        }
      },
      "serviceTypeName2": {
        "type": "string",
        "defaultValue": "Service2Type",
        "metadata": {
          "description": "The name of the service type."
        }
      }
    },
    "variables": {
      "clusterLocation": "[resourcegroup().location]"
    },
    "resources": [
      {
        "apiVersion": "2017-07-01-preview",
        "type": "Microsoft.ServiceFabric/clusters/applicationTypes",
        "name": "[concat(parameters('clusterName'), '/', parameters('applicationTypeName'))]",
        "location": "[variables('clusterLocation')]",
        "dependsOn": [],
        "properties": {
          "provisioningState": "Default"
        }
      },
      {
        "apiVersion": "2017-07-01-preview",
        "type": "Microsoft.ServiceFabric/clusters/applicationTypes/versions",
        "name": "[concat(parameters('clusterName'), '/', parameters('applicationTypeName'), '/', parameters('applicationTypeVersion'))]",
        "location": "[variables('clusterLocation')]",
        "dependsOn": [
          "[concat('Microsoft.ServiceFabric/clusters/', parameters('clusterName'), '/applicationTypes/', parameters('applicationTypeName'))]"
        ],
        "properties": {
          "provisioningState": "Default",
          "appPackageUrl": "[parameters('appPackageUrl')]"
        }
      },
      {
        "apiVersion": "2017-07-01-preview",
        "type": "Microsoft.ServiceFabric/clusters/applications",
        "name": "[concat(parameters('clusterName'), '/', parameters('applicationName'))]",
        "location": "[variables('clusterLocation')]",
        "dependsOn": [
          "[concat('Microsoft.ServiceFabric/clusters/', parameters('clusterName'), '/applicationTypes/', parameters('applicationTypeName'), '/versions/', parameters('applicationTypeVersion'))]"
        ],
        "properties": {
          "provisioningState": "Default",
          "typeName": "[parameters('applicationTypeName')]",
          "typeVersion": "[parameters('applicationTypeVersion')]",
          "parameters": {},
          "upgradePolicy": {
            "upgradeReplicaSetCheckTimeout": "01:00:00.0",
            "forceRestart": "false",
            "rollingUpgradeMonitoringPolicy": {
              "healthCheckWaitDuration": "00:02:00.0",
              "healthCheckStableDuration": "00:05:00.0",
              "healthCheckRetryTimeout": "00:10:00.0",
              "upgradeTimeout": "01:00:00.0",
              "upgradeDomainTimeout": "00:20:00.0"
            },
            "applicationHealthPolicy": {
              "considerWarningAsError": "false",
              "maxPercentUnhealthyDeployedApplications": "50",
              "defaultServiceTypeHealthPolicy": {
                "maxPercentUnhealthyServices": "50",
                "maxPercentUnhealthyPartitionsPerService": "50",
                "maxPercentUnhealthyReplicasPerPartition": "50"
              }
            }
          }
        }
      },
      {
        "apiVersion": "2017-07-01-preview",
        "type": "Microsoft.ServiceFabric/clusters/applications/services",
        "name": "[concat(parameters('clusterName'), '/', parameters('applicationName'), '/', parameters('serviceName'))]",
        "location": "[variables('clusterLocation')]",
        "dependsOn": [
          "[concat('Microsoft.ServiceFabric/clusters/', parameters('clusterName'), '/applications/', parameters('applicationName'))]"
        ],
        "properties": {
          "provisioningState": "Default",
          "serviceKind": "Stateless",
          "serviceTypeName": "[parameters('serviceTypeName')]",
          "instanceCount": "-1",
          "partitionDescription": {
            "partitionScheme": "Singleton"
          },
          "correlationScheme": [],
          "serviceLoadMetrics": [],
          "servicePlacementPolicies": []
        }
      },
      {
        "apiVersion": "2017-07-01-preview",
        "type": "Microsoft.ServiceFabric/clusters/applications/services",
        "name": "[concat(parameters('clusterName'), '/', parameters('applicationName'), '/', parameters('serviceName2'))]",
        "location": "[variables('clusterLocation')]",
        "dependsOn": [
          "[concat('Microsoft.ServiceFabric/clusters/', parameters('clusterName'), '/applications/', parameters('applicationName'))]"
        ],
        "properties": {
          "provisioningState": "Default",
          "serviceKind": "Stateful",
          "serviceTypeName": "[parameters('serviceTypeName2')]",
          "targetReplicaSetSize": "3",
          "minReplicaSetSize": "2",
          "replicaRestartWaitDuration": "00:01:00.0",
          "quorumLossWaitDuration": "00:02:00.0",
          "standByReplicaKeepDuration": "00:00:30.0",
          "partitionDescription": {
            "partitionScheme": "UniformInt64Range",
            "count": "5",
            "lowKey": "1",
            "highKey": "5"
          },
          "hasPersistedState": "true",
          "correlationScheme": [],
          "serviceLoadMetrics": [],
          "servicePlacementPolicies": [],
          "defaultMoveCost": "Low"
        }
      }
    ]
  }
  ```

  > [!NOTE] 
  > *ApiVersion* musí být nastavena na `"2017-07-01-preview"`. Tuto šablonu můžete také nasadit nezávisle na clusteru, dokud nasazení clusteru.

5. Nasaďte! 

## <a name="manage-an-existing-application-via-resource-manager"></a>Spravovat prostřednictvím Resource Manager stávající aplikaci

Pokud váš cluster je již v provozu a získat některé aplikace, zda chcete spravovat jako správce prostředků prostředky jsou už nasazené, místo odebrání aplikací a jejich opětovného nasazení, můžete použít PUT volání pomocí stejné rozhraní API do mají aplikace potvrdí jako prostředky Resource Manager. 


## <a name="next-steps"></a>Další postup

* Použití [Service Fabric rozhraní příkazového řádku](service-fabric-cli.md) nebo [prostředí PowerShell](service-fabric-deploy-remove-applications.md) nasazení dalších aplikací do clusteru. 
* [Upgrade clusteru Service Fabric](service-fabric-cluster-upgrade.md)

