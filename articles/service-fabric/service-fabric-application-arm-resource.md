---
title: Nasazení a upgrade pomocí Azure Resource Manageru
description: Zjistěte, jak nasadit aplikace a služby do clusteru Service Fabric pomocí šablony Azure Resource Manager.
ms.topic: conceptual
ms.date: 12/06/2017
ms.openlocfilehash: a2dfe54bf2c6b4fa8814f10c10576a73727a7417
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "75610246"
---
# <a name="manage-applications-and-services-as-azure-resource-manager-resources"></a>Správa aplikací a služeb jako prostředků Azure Resource Manageru

Aplikace a služby můžete nasadit do clusteru Service Fabric prostřednictvím Správce prostředků Azure. To znamená, že namísto nasazení a správy aplikací prostřednictvím prostředí PowerShell nebo CLI poté, co budete muset čekat na připravenost clusteru, můžete nyní vyjádřit aplikace a služby v JSON a nasadit je ve stejné šabloně Správce prostředků jako váš cluster. Proces registrace aplikace, zřizování a nasazení vše probíhá v jednom kroku.

Toto je doporučený způsob nasazení všech instalačních, zásadových řízení nebo aplikací pro správu clusteru, které požadujete v clusteru. To zahrnuje [patch Orchestrace aplikace](service-fabric-patch-orchestration-application.md), Watchdogs nebo všechny aplikace, které je třeba spustit v clusteru před nasazením jiných aplikací nebo služeb. 

Pokud je to možné, spravujte aplikace jako prostředky Správce prostředků, abyste je mohli zlepšit:
* Záznam auditu: Správce prostředků audituje každou operaci a uchovává podrobný *protokol aktivit,* který vám pomůže sledovat všechny změny provedené v těchto aplikacích a v clusteru.
* Řízení přístupu na základě rolí (RBAC): Správa přístupu ke clusterům i aplikacím nasazeným v clusteru lze provést prostřednictvím stejné šablony Správce prostředků.
* Azure Resource Manager (přes portál Azure) se stává one-stop-shop pro správu clusteru a nasazení důležitých aplikací.

Následující úryvek zobrazuje různé druhy prostředků, které lze spravovat pomocí šablony:

```json
{
    "apiVersion": "2019-03-01",
    "type": "Microsoft.ServiceFabric/clusters/applicationTypes",
    "name": "[concat(parameters('clusterName'), '/', parameters('applicationTypeName'))]",
    "location": "[variables('clusterLocation')]",
},
{
    "apiVersion": "2019-03-01",
    "type": "Microsoft.ServiceFabric/clusters/applicationTypes/versions",
    "name": "[concat(parameters('clusterName'), '/', parameters('applicationTypeName'), '/', parameters('applicationTypeVersion'))]",
    "location": "[variables('clusterLocation')]",
},
{
    "apiVersion": "2019-03-01",
    "type": "Microsoft.ServiceFabric/clusters/applications",
    "name": "[concat(parameters('clusterName'), '/', parameters('applicationName'))]",
    "location": "[variables('clusterLocation')]",
},
{
    "apiVersion": "2019-03-01",
    "type": "Microsoft.ServiceFabric/clusters/applications/services",
    "name": "[concat(parameters('clusterName'), '/', parameters('applicationName'), '/', parameters('serviceName'))]",
    "location": "[variables('clusterLocation')]"
}
```


## <a name="add-a-new-application-to-your-resource-manager-template"></a>Přidání nové aplikace do šablony Správce prostředků

1. Připravte šablonu Správce prostředků clusteru pro nasazení. Další informace najdete [v tématu Vytvoření clusteru Service Fabric pomocí Správce prostředků Azure.](service-fabric-cluster-creation-via-arm.md)
2. Přemýšlejte o některých aplikacích, které plánujete nasadit v clusteru. Existují nějaké, které budou vždy spuštěny, že jiné aplikace mohou mít závislosti na? Plánujete nasazení všech aplikací zásad správného řízení nebo nastavení clusteru? Tyto druhy aplikací jsou nejlépe spravovány pomocí šablony Správce prostředků, jak je popsáno výše. 
3. Jakmile zjistíte, jaké aplikace chcete nasadit tímto způsobem, musí být aplikace zabaleny, zipovány a umístěny do sdílené složky. Sdílená sdílená část musí být přístupná prostřednictvím koncového bodu REST, který může Správce prostředků Azure využívat během nasazení.
4. V šabloně Správce prostředků pod deklarací clusteru popište vlastnosti každé aplikace. Tyto vlastnosti zahrnují počet replik nebo instancí a všechny řetězy závislostí mezi prostředky (jiné aplikace nebo služby). Seznam komplexních vlastností naleznete v [tématu REST API Swagger Spec](https://aka.ms/sfrpswaggerspec). Všimněte si, že to nenahrazuje manifesty aplikace nebo služby, ale spíše popisuje některé co je v nich jako součást šablony Správce prostředků clusteru. Zde je ukázková šablona, která zahrnuje nasazení služby bez stavové *služby Service1* a stavové služby *Service2* jako součást *Aplikace1*:

   ```json
   {
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json",
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
        "apiVersion": "2019-03-01",
        "type": "Microsoft.ServiceFabric/clusters/applicationTypes",
        "name": "[concat(parameters('clusterName'), '/', parameters('applicationTypeName'))]",
        "location": "[variables('clusterLocation')]",
        "dependsOn": [],
        "properties": {
          "provisioningState": "Default"
        }
      },
      {
        "apiVersion": "2019-03-01",
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
        "apiVersion": "2019-03-01",
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
        "apiVersion": "2019-03-01",
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
        "apiVersion": "2019-03-01",
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
   > *ApiVersion* musí být `"2019-03-01"`nastavena na . Tuto šablonu lze také nasadit nezávisle na clusteru, pokud již byl nasazen cluster.

5. Nasazení! 

## <a name="remove-service-fabric-resource-provider-application-resource"></a>Odebrat prostředek aplikace zprostředkovatele prostředků zprostředkovatele prostředků zprostředkovatele prostředků služby
Následující aktivuje balíček aplikace, který má být un-provisioned z clusteru, a to bude vyčistit místo na disku:
```powershell
Get-AzureRmResource -ResourceId /subscriptions/{sid}/resourceGroups/{rg}/providers/Microsoft.ServiceFabric/clusters/{cluster}/applicationTypes/{apptType}/versions/{version} -ApiVersion "2019-03-01" | Remove-AzureRmResource -Force -ApiVersion "2017-07-01-preview"
```
Pouhé odebrání microsoft.servicefabric/clusters/aplikace ze šablony ARM neodpojí zřízení aplikace

>[!NOTE]
> Jakmile je odstranění dokončeno, neměli byste vidět verzi balíčku v SFX nebo ARM. Prostředek verze typu aplikace, se kterým je aplikace spuštěna, nelze odstranit. ARM/SFRP tomu zabrání. Pokud se pokusíte zrušit zřízení spuštěný balíček, SF runtime zabrání.


## <a name="manage-an-existing-application-via-resource-manager"></a>Správa existující aplikace prostřednictvím Správce prostředků

Pokud je váš cluster již zapnutý a některé aplikace, které chcete spravovat jako prostředky Správce prostředků, jsou již nasazeny na to, namísto odebrání aplikací a jejich opětovné nasazení, můžete použít volání PUT pomocí stejných api, aby aplikace získat jako zdroje Správce zdrojů. Další informace naleznete v části [Co je model prostředků aplikace Service Fabric?](https://docs.microsoft.com/azure/service-fabric/service-fabric-concept-resource-model)

> [!NOTE]
> Chcete-li povolit upgrade clusteru ignorovat nefunkční aplikace zákazník může zadat "maxPercentUnhealthyApplications: 100" v části "upgradeDescription/healthPolicy"; Podrobné popisy všech nastavení jsou uvedeny v [dokumentaci k zásadám upgradu clusteru REST API služby Service Fabric](https://docs.microsoft.com/rest/api/servicefabric/sfrp-model-clusterupgradepolicy).

## <a name="next-steps"></a>Další kroky

* Pomocí [cli service fabric](service-fabric-cli.md) nebo [PowerShell](service-fabric-deploy-remove-applications.md) nasadit jiné aplikace do clusteru. 
* [Upgrade clusteru Service Fabric](service-fabric-cluster-upgrade.md)

