---
title: Nasazení a upgrade pomocí Azure Resource Manager
description: Naučte se, jak nasadit aplikace a služby do clusteru Service Fabric pomocí šablony Azure Resource Manager.
ms.topic: conceptual
ms.date: 12/06/2017
ms.openlocfilehash: ed6bc7d96cb3ea0934929e6543c5e637a9f42c1f
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2021
ms.locfileid: "97930833"
---
# <a name="manage-applications-and-services-as-azure-resource-manager-resources"></a>Správa aplikací a služeb jako Azure Resource Managerch prostředků

Do Service Fabric clusteru můžete pomocí Azure Resource Manager nasadit aplikace a služby. To znamená, že místo nasazení a správy aplikací prostřednictvím PowerShellu nebo rozhraní příkazového řádku po chvíli, kdy bude nutné počkat na dokončení clusteru, teď můžete ve službě JSON aplikace a služby ve formátu JSON a nasadit je do stejné šablony Správce prostředků jako svůj cluster. Proces registrace, zřizování a nasazení aplikace proběhne v jednom kroku.

Toto je doporučený způsob, jak nasadit jakékoli aplikace pro správu, zásady správného řízení nebo správu clusteru, které v clusteru požadujete. Patří sem [aplikace orchestrace oprav](service-fabric-patch-orchestration-application.md), sledovacích zařízení nebo jakékoli aplikace, které musí být spuštěny ve vašem clusteru před nasazením dalších aplikací nebo služeb. 

V případě potřeby můžete své aplikace spravovat jako Správce prostředků prostředky pro zlepšení:
* Záznam pro audit: Správce prostředků Audituje každou operaci a udržuje podrobný *Protokol aktivit* , který vám může pomáhat sledovat změny provedené v těchto aplikacích a clusteru.
* Řízení přístupu na základě role v Azure (Azure RBAC): Správa přístupu ke clusterům i aplikacím nasazeným v clusteru je možné provádět prostřednictvím stejné Správce prostředků šablony.
* Azure Resource Manager (přes Azure Portal) se stane jedním zazastavením pro správu clusteru a důležitých nasazení aplikací.

Následující fragment kódu ukazuje různé druhy prostředků, které lze spravovat pomocí šablony:

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

1. Připravte šablonu Správce prostředků clusteru pro nasazení. Další informace najdete v tématu [Vytvoření clusteru Service Fabric pomocí Azure Resource Manager](service-fabric-cluster-creation-via-arm.md) .
2. Zamyslete se nad některými aplikacemi, které plánujete nasadit v clusteru. Existují nějaké součásti, které budou vždycky běžet, na kterých se můžou pracovat jiné aplikace? Plánujete nasazení jakýchkoli zásad správného řízení clusteru nebo instalačních aplikací? Tyto typy aplikací se nejlépe spravují pomocí šablony Správce prostředků, jak je popsáno výše. 
3. Po zjištění toho, které aplikace chcete nasadit tímto způsobem, musí být aplikace zabaleny, zip a umístěny do sdílené složky úložiště. Sdílená složka musí být přístupná prostřednictvím koncového bodu REST, aby bylo možné Azure Resource Manager spotřebovat během nasazení. Podrobnosti najdete v tématu [Vytvoření účtu úložiště](service-fabric-concept-resource-model.md#create-a-storage-account) .
4. V šabloně Správce prostředků pod deklarací clusteru popište vlastnosti jednotlivých aplikací. Mezi tyto vlastnosti patří replika nebo počet instancí a všechny řetězy závislostí mezi prostředky (jiné aplikace nebo služby). Všimněte si, že toto nenahrazuje manifesty aplikace nebo služby, ale místo toho popisuje některé z nich, co je v rámci šablony Správce prostředků clusteru. Tady je Ukázková šablona, která zahrnuje nasazení bezstavové služby *Service1* a *jazyka2* stavové služby jako součást *application1*:

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
   > Informace o využití a podrobnostech jednotlivých vlastností šablony najdete v [referenčních informacích](/azure/templates/microsoft.servicefabric/clusters/applicationtypes) k Service Fabric Azure Resource Manager.

5. Nasazení! 

## <a name="remove-service-fabric-resource-provider-application-resource"></a>Odebrat prostředek aplikace Service Fabric Resource Provider
Následující příkaz aktivuje balíček aplikace, aby se nezřídil z clusteru, a vyčistí místo na disku, které se používá:
```powershell
Get-AzureRmResource -ResourceId /subscriptions/{sid}/resourceGroups/{rg}/providers/Microsoft.ServiceFabric/clusters/{cluster}/applicationTypes/{apptType}/versions/{version} -ApiVersion "2019-03-01" | Remove-AzureRmResource -Force -ApiVersion "2017-07-01-preview"
```
Pouhým odebráním Microsoft. ServiceFabric/Clusters/Application ze šablony ARM nedojde k zrušení zřízení aplikace.

>[!NOTE]
> Po dokončení odebrání by se už neměla zobrazovat verze balíčku v SFX nebo ARM. Nelze odstranit prostředek verze typu aplikace, se kterou aplikace běží. Tato akce zabrání ARM/SFRP. Pokud se pokusíte zrušit zřízení běžícího balíčku, modul runtime SF ho zabrání.


## <a name="manage-an-existing-application-via-resource-manager"></a>Správa existující aplikace pomocí Správce prostředků

Pokud je váš cluster už v provozu a některé aplikace, které byste chtěli spravovat jako Správce prostředků prostředky, jsou už nasazené, neodstraňujte aplikace a znovu je nasadíte. k tomu, aby se aplikace získaly jako Správce prostředků prostředky, můžete použít volání PUT pomocí stejných rozhraní API. Další informace najdete [v tématu Co je model prostředků aplikace Service Fabric?](./service-fabric-concept-resource-model.md)

> [!NOTE]
> Aby mohl upgrade clusteru ignorovat aplikace, které nemají stav v pořádku, může zákazník v části upgradeDescription/healthPolicy zadat "maxPercentUnhealthyApplications: 100"; Podrobné popisy všech nastavení jsou v [dokumentaci Service fabric REST API zásadách upgradu clusteru](/rest/api/servicefabric/sfrp-model-clusterupgradepolicy).

## <a name="next-steps"></a>Další kroky

* K nasazení dalších aplikací do clusteru použijte [Service Fabric CLI](service-fabric-cli.md) nebo [PowerShell](service-fabric-deploy-remove-applications.md) . 
* [Upgrade clusteru Service Fabric](service-fabric-cluster-upgrade.md)
