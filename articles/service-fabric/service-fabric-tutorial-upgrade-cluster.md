---
title: Upgrade modulu runtime Service Fabric v Azure | Microsoft Docs
description: V tomto kurzu se dozvíte, jak pomocí PowerShellu upgradovat modul runtime clusteru Service Fabric hostovaného v Azure.
services: service-fabric
documentationcenter: .net
author: Thraka
manager: timlt
editor: ''
ms.assetid: ''
ms.service: service-fabric
ms.devlang: dotNet
ms.topic: tutorial
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 11/28/2017
ms.author: adegeo
ms.custom: mvc
ms.openlocfilehash: 7e7304b259931c5196a4865383cf0b4ace4c4398
ms.sourcegitcommit: 5a7f13ac706264a45538f6baeb8cf8f30c662f8f
ms.translationtype: HT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/29/2018
ms.locfileid: "37109762"
---
# <a name="tutorial-upgrade-the-runtime-of-a-service-fabric-cluster-in-azure"></a>Kurz: Upgrade modulu runtime clusteru Service Fabric v Azure

V tomto kurzu, který je třetí částí série, se dozvíte, jak upgradovat modul runtime Service Fabric v clusteru Azure Service Fabric. Tato část kurzu je určená pro clustery Service Fabric běžící v Azure, a nevztahuje se na samostatné clustery Service Fabric.

> [!WARNING]
> Tato část kurzu vyžaduje PowerShell. Nástroje Azure CLI ještě upgradování modulu runtime clusteru nepodporují. Alternativně je možné cluster upgradovat na portálu. Další informace najdete v tématu [Upgrade clusteru Azure Service Fabric](service-fabric-cluster-upgrade.md).

Pokud váš cluster již využívá nejnovější modul runtime Service Fabric, nemusíte tento krok provádět. Tento článek však můžete použít k instalaci jakéhokoli podporovaného modulu runtime v clusteru Azure Service Fabric.

V tomto kurzu se naučíte:

> [!div class="checklist"]
> * Načtení verze clusteru
> * Nastavení verze clusteru

V této sérii kurzů se naučíte:
> [!div class="checklist"]
> * Vytvoření zabezpečeného [clusteru s Windows](service-fabric-tutorial-create-vnet-and-windows-cluster.md) nebo [clusteru s Linuxem](service-fabric-tutorial-create-vnet-and-linux-cluster.md) v Azure pomocí šablony
> * [Horizontální snížení nebo navýšení kapacity clusteru](service-fabric-tutorial-scale-cluster.md)
> * Upgrade modulu runtime clusteru
> * [Nasazení služby API Management s využitím Service Fabric](service-fabric-tutorial-deploy-api-management.md)

## <a name="prerequisites"></a>Požadavky

Než začnete s tímto kurzem:

* Pokud ještě nemáte předplatné Azure, vytvořte si [bezplatný účet](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
* Nainstalujte [modul Azure PowerShellu verze 4.1 nebo novější ](https://docs.microsoft.com/powershell/azure/install-azurerm-ps) nebo [Azure CLI 2.0](/cli/azure/install-azure-cli).
* Vytvořte zabezpečený [cluster s Windows](service-fabric-tutorial-create-vnet-and-windows-cluster.md) nebo [cluster s Linuxem](service-fabric-tutorial-create-vnet-and-linux-cluster.md) v Azure.
* Pokud nasadíte cluster s Windows, nastavte vývojové prostředí ve Windows. Nainstalujte sadu [Visual Studio 2017](http://www.visualstudio.com) a sady funkcí **Vývoj pro Azure**, **Vývoj pro ASP.NET a web** a **Vývoj multiplatformních aplikací pomocí rozhraní .NET Core**.  Potom nastavte [vývojové prostředí .NET](service-fabric-get-started.md).
* Pokud nasadíte cluster s Linuxem, nastavte vývojové prostředí Java v [Linuxu](service-fabric-get-started-linux.md) nebo [MacOS](service-fabric-get-started-mac.md).  Nainstalujte [Service Fabric CLI](service-fabric-cli.md).

### <a name="sign-in-to-azure"></a>Přihlášení k Azure

Před spouštěním příkazů Azure se přihlaste ke svému účtu Azure a vyberte své předplatné.

```powershell
Connect-AzureRmAccount
Get-AzureRmSubscription
Set-AzureRmContext -SubscriptionId <guid>
```

## <a name="get-the-runtime-version"></a>Získání verze modulu runtime

Po připojení k Azure a výběru předplatného, které obsahuje cluster Service Fabric, můžete získat verzi modulu runtime clusteru.

```powershell
Get-AzureRmServiceFabricCluster -ResourceGroupName SFCLUSTERTUTORIALGROUP -Name aztestcluster `
    | Select-Object ClusterCodeVersion
```

Případně stačí získat seznam všech clusterů ve vašem předplatném pomocí následujícího příkazu:

```powershell
Get-AzureRmServiceFabricCluster | Select-Object Name, ClusterCodeVersion
```

Poznamenejte si hodnotu **ClusterCodeVersion**. Tuto hodnotu použijete v další části.

## <a name="upgrade-the-runtime"></a>Upgrade modulu runtime

Pomocí hodnoty **ClusterCodeVersion** z předchozí části a rutiny `Get-ServiceFabricRuntimeUpgradeVersion` zjistěte, jaké verze jsou pro upgrade k dispozici. Tuto rutinu je možné spustit pouze z počítače připojeného k internetu. Pokud například chcete zobrazit, na jaké verze modulu runtime můžete upgradovat z verze `5.7.198.9494`, použijte následující příkaz:

```powershell
Get-ServiceFabricRuntimeUpgradeVersion -BaseVersion "5.7.198.9494"
```

Když máte seznam verzí, můžete clusteru Azure Service Fabric sdělit, aby provedl upgrade na novější modul runtime. Pokud je například pro upgrade k dispozici verze `6.0.219.9494`, můžete cluster upgradovat pomocí následujícího příkazu.

```powershell
Set-AzureRmServiceFabricUpgradeType -ResourceGroupName SFCLUSTERTUTORIALGROUP `
                                    -Name aztestcluster `
                                    -UpgradeMode Manual `
                                    -Version "6.0.219.9494"
```

> [!IMPORTANT]
> Dokončení upgradu modulu runtime může trvat delší dobu. PowerShell je v průběhu upgradu blokovaný. Ke kontrole stavu upgradu můžete použít jinou relaci PowerShellu.

Stav upgradu můžete monitorovat pomocí PowerShellu nebo Azure Service Fabric CLI (sfctl).

Nejprve se ke clusteru připojte pomocí certifikátu SSL vytvořeného v první části kurzu. Použijte k tomu rutinu `Connect-ServiceFabricCluster` nebo `sfctl cluster upgrade-status`.

```powershell
$endpoint = "<mycluster>.southcentralus.cloudapp.azure.com:19000"
$thumbprint = "63EB5BA4BC2A3BADC42CA6F93D6F45E5AD98A1E4"

Connect-ServiceFabricCluster -ConnectionEndpoint $endpoint `
                             -KeepAliveIntervalInSec 10 `
                             -X509Credential -ServerCertThumbprint $thumbprint `
                             -FindType FindByThumbprint -FindValue $thumbprint `
                             -StoreLocation CurrentUser -StoreName My
```

```azurecli
sfctl cluster select --endpoint https://aztestcluster.southcentralus.cloudapp.azure.com:19080 \
--pem ./aztestcluster201709151446.pem --no-verify
```

Pak pomocí rutiny `Get-ServiceFabricClusterUpgrade` nebo `sfctl cluster upgrade-status` zobrazte stav. Zobrazí se výsledek podobný následujícímu příkladu.

```powershell
Get-ServiceFabricClusterUpgrade

TargetCodeVersion                          : 6.0.219.9494
TargetConfigVersion                        : 3
StartTimestampUtc                          : 11/28/2017 3:09:48 AM
UpgradeState                               : RollingForwardPending
UpgradeDuration                            : 00:09:00
CurrentUpgradeDomainDuration               : 00:09:00
NextUpgradeDomain                          : 1
UpgradeDomainsStatus                       : { "0" = "Completed";
                                             "1" = "Pending";
                                             "2" = "Pending";
                                             "3" = "Pending";
                                             "4" = "Pending" }
UpgradeKind                                : Rolling
RollingUpgradeMode                         : Monitored
FailureAction                              : Rollback
ForceRestart                               : False
UpgradeReplicaSetCheckTimeout              : 37201.09:59:01
HealthCheckWaitDuration                    : 00:05:00
HealthCheckStableDuration                  : 00:05:00
HealthCheckRetryTimeout                    : 00:45:00
UpgradeDomainTimeout                       : 02:00:00
UpgradeTimeout                             : 12:00:00
ConsiderWarningAsError                     : False
MaxPercentUnhealthyApplications            : 0
MaxPercentUnhealthyNodes                   : 100
ApplicationTypeHealthPolicyMap             : {}
EnableDeltaHealthEvaluation                : True
MaxPercentDeltaUnhealthyNodes              : 0
MaxPercentUpgradeDomainDeltaUnhealthyNodes : 0
ApplicationHealthPolicyMap                 : {}
```

```azurecli
sfctl cluster upgrade-status

{
  "codeVersion": "6.0.219.9494",
  "configVersion": "3",

... item cut to save space ...

  },
  "upgradeDomains": [
    {
      "name": "0",
      "state": "Completed"
    },
    {
      "name": "1",
      "state": "Pending"
    },
    {
      "name": "2",
      "state": "Pending"
    },
    {
      "name": "3",
      "state": "Pending"
    },
    {
      "name": "4",
      "state": "Pending"
    }
  ],
  "upgradeDurationInMilliseconds": "PT1H2M4.63889S",
  "upgradeState": "RollingForwardPending"
}
```

## <a name="next-steps"></a>Další kroky

V tomto kurzu jste se naučili:

> [!div class="checklist"]
> * Získání verze modulu runtime clusteru
> * Upgrade modulu runtime clusteru
> * Monitorování upgradu

Teď přejděte k následujícímu kurzu, kde se dozvíte, jak nasadit službu API Management s využitím clusteru Service Fabric.
> [!div class="nextstepaction"]
> [Nasazení služby API Management s využitím Service Fabric](service-fabric-tutorial-deploy-api-management.md)
