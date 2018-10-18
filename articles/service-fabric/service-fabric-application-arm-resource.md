---
title: Nasazení a upgrade aplikace a služby pomocí Azure Resource Manageru | Dokumentace Microsoftu
description: Zjistěte, jak nasadit aplikace a služby do clusteru Service Fabric pomocí šablony Azure Resource Manageru.
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
ms.openlocfilehash: feb9d0a01cbba75fc9868f5a603d494c5c09ae2e
ms.sourcegitcommit: f20e43e436bfeafd333da75754cd32d405903b07
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/17/2018
ms.locfileid: "49386293"
---
# <a name="manage-applications-and-services-as-azure-resource-manager-resources"></a>Spravovat aplikace a služby jako prostředky Azure Resource Manageru

Do vašeho clusteru Service Fabric pomocí Azure Resource Manageru můžete nasadit aplikace a služby. To znamená, že namísto nasazení a správě aplikací prostřednictvím Powershellu nebo rozhraní příkazového řádku po čekání z clusteru bude připravené, můžete nyní express, aplikace a služby ve formátu JSON a nasazení v šabloně Resource Manageru jako cluster. Proces registrace aplikace, zřizování a nasazování všechny se stane v jednom kroku.

Toto je doporučený postup pro můžete nasadit libovolné instalační program, zásady správného řízení nebo aplikace pro správu clusteru, které potřebujete ve vašem clusteru. Jedná se o [aplikace orchestraci oprav](service-fabric-patch-orchestration-application.md), Watchdogs nebo aplikací, které je potřeba ve vašem clusteru spuštěná před samotným nasazením jinými aplikacemi nebo službami. 

V případě potřeby spravujte jako prostředky Resource Manageru pro zlepšení vaší aplikace:
* Záznam pro audit: Resource Manager Audituje každé operace a udržuje na konkrétní *protokolu aktivit* , který vám pomůže sledovat všechny změny provedené v těchto aplikací a clusteru.
* Řízení přístupu na základě role (RBAC): Správa přístupu k clustery, jakož i aplikace nasazené na clusteru, můžete to udělat pomocí stejné šablony Resource Manageru.
* Azure Resource Manageru (prostřednictvím webu Azure portal) se změní na jeden, kde za správu clusteru a nasazení důležitých aplikací.

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


## <a name="add-a-new-application-to-your-resource-manager-template"></a>Přidání nové aplikace do šablony Resource Manageru

1. Příprava pro nasazení šablony Resource Manageru pro váš cluster. Zobrazit [vytvořit cluster Service Fabric pomocí Azure Resource Manageru](service-fabric-cluster-creation-via-arm.md) pro další informace o tomto.
2. Představte si některé z aplikací, které plánujete nasadit v clusteru. Existují všechny, které budou vždycky spouštět, jiné aplikace může trvat závislosti? Budete k nasazení všech zásad správného řízení clusteru nebo instalační program aplikace? Tyto druhy aplikací se nejlépe spravují pomocí šablony Resource Manageru, jak je popsáno výše. 
3. Jakmile máte naplánujete jaké aplikace mají být nasazeny tímto způsobem, aplikace mít zabaleny, ZIP a umístěte do sdílené. Sdílené složky musí být přístupné přes koncový bod REST pro Azure Resource Manager využívat během nasazení.
4. V šabloně Resource Manageru, následující deklarace vašeho clusteru popisují vlastnosti jednotlivých aplikací. Tyto vlastnosti zahrnují počet replik nebo instancí a všechny řetězy závislostí mezi prostředky (jinými aplikacemi nebo službami). Seznam komplexní vlastnosti najdete v tématu [specifikace Swagger rozhraní API REST](https://aka.ms/sfrpswaggerspec). Všimněte si, že tato metoda nenahrazuje aplikace nebo služba manifesty, ale místo toho obsahuje také popis některých co je v nich jako součást šablony Resource Manageru clusteru. Tady je ukázka šablony, která zahrnuje nasazení bezstavovou službu *Service1* a stavové služby *Service2* jako součást *Application1*:

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
  > *ApiVersion* musí být nastaveno na `"2017-07-01-preview"`. Tuto šablonu můžete také nasadit nezávisle na clusteru, za předpokladu, cluster již byla nasazena.

5. Nasaďte! 

## <a name="manage-an-existing-application-via-resource-manager"></a>Spravovat existující aplikace prostřednictvím Resource Manageru

Pokud váš cluster běží již a získat některé aplikace, že chcete spravovat jako Resource Manageru prostředky jsou už nasazené, místo aby odebrala aplikace a znovu je nasadit, můžete použít voláním PUT pomocí stejného rozhraní API pro aplikace potvrzení jako prostředky Resource Manageru. 

> [!NOTE]
> Chcete-li povolit upgradu clusteru ignorovat aplikace není v pořádku, zákazník zadat "maxPercentUnhealthyApplications: 100" v části "upgradeDescription/healthPolicy"; podrobný popis pro všechna nastavení jsou v [dokumentace ke službě Service Fabric REST API clusteru zásady upgradu](https://docs.microsoft.com/rest/api/servicefabric/sfrp-model-clusterupgradepolicy).

## <a name="next-steps"></a>Další postup

* Použití [Service Fabric CLI](service-fabric-cli.md) nebo [Powershellu](service-fabric-deploy-remove-applications.md) při nasazování dalších aplikací do clusteru. 
* [Upgrade clusteru Service Fabric](service-fabric-cluster-upgrade.md)

