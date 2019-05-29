---
title: Upgrade modulu runtime Service Fabric v Azure | Microsoft Docs
description: V tomto kurzu se dozvíte, jak pomocí PowerShellu upgradovat modul runtime clusteru Service Fabric hostovaného v Azure.
services: service-fabric
documentationcenter: .net
author: aljo-microsoft
manager: chackdan
editor: ''
ms.assetid: ''
ms.service: service-fabric
ms.devlang: dotNet
ms.topic: tutorial
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 11/28/2017
ms.author: aljo
ms.custom: mvc
ms.openlocfilehash: 8bb8a635c3699828376390c489697b6315030937
ms.sourcegitcommit: 009334a842d08b1c83ee183b5830092e067f4374
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/29/2019
ms.locfileid: "66306680"
---
# <a name="tutorial-upgrade-the-runtime-of-a-service-fabric-cluster-in-azure"></a>Kurz: Upgrade modulu runtime clusteru Service Fabric v Azure

Tento kurz je čtvrtou částí série a ukazuje, jak upgradovat modul runtime Service Fabric v clusteru Azure Service Fabric. Tato část kurzu je určené pro clustery Service Fabric běžící v Azure a neplatí pro Service Fabric samostatné clustery.

> [!WARNING]
> Tato část kurzu vyžaduje PowerShell. Nástroje Azure CLI ještě upgradování modulu runtime clusteru nepodporují. Alternativně je možné cluster upgradovat na portálu. Další informace najdete v tématu [Upgrade clusteru Azure Service Fabric](service-fabric-cluster-upgrade.md).

Pokud váš cluster již využívá nejnovější modul runtime Service Fabric, nemusíte tento krok. Tento článek však můžete použít k instalaci jakéhokoli podporovaného modulu runtime v clusteru Azure Service Fabric.

V tomto kurzu se naučíte:

> [!div class="checklist"]
> * Načtení verze clusteru
> * Nastavení verze clusteru

V této sérii kurzů se naučíte:
> [!div class="checklist"]
> * Vytvoření zabezpečeného [clusteru Windows](service-fabric-tutorial-create-vnet-and-windows-cluster.md) v Azure pomocí šablony
> * [Monitorování clusteru](service-fabric-tutorial-monitor-cluster.md)
> * [Horizontální snížení nebo navýšení kapacity clusteru](service-fabric-tutorial-scale-cluster.md)
> * Upgrade modulu runtime clusteru
> * [Odstranění clusteru](service-fabric-tutorial-delete-cluster.md)


[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="prerequisites"></a>Požadavky

Než začnete s tímto kurzem:

* Pokud ještě nemáte předplatné Azure, vytvořte si [bezplatný účet](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
* Nainstalujte [prostředí Azure Powershell](https://docs.microsoft.com/powershell/azure/install-Az-ps) nebo [rozhraní příkazového řádku Azure](/cli/azure/install-azure-cli).
* Vytvoření zabezpečeného [clusteru Windows](service-fabric-tutorial-create-vnet-and-windows-cluster.md) v Azure
* Nastavení vývojového prostředí Windows. Nainstalujte [Visual Studio 2019](https://www.visualstudio.com) a **vývoj pro Azure**, **vývoj pro ASP.NET a web**, a **vývoj pro různé platformy .NET Core**úlohy.  Potom nastavte [vývojové prostředí .NET](service-fabric-get-started.md).

### <a name="sign-in-to-azure"></a>Přihlásit se k Azure

Před spouštěním příkazů Azure se přihlaste ke svému účtu Azure a vyberte své předplatné.

```powershell
Connect-AzAccount
Get-AzSubscription
Set-AzContext -SubscriptionId <guid>
```

## <a name="get-the-runtime-version"></a>Získání verze modulu runtime

Po připojení k Azure, vybrané předplatné obsahuje cluster Service Fabric, můžete získat verzi modulu runtime clusteru.

```powershell
Get-AzServiceFabricCluster -ResourceGroupName SFCLUSTERTUTORIALGROUP -Name aztestcluster `
    | Select-Object ClusterCodeVersion
```

Případně stačí získat seznam všech clusterů ve vašem předplatném pomocí následujícího příkladu:

```powershell
Get-AzServiceFabricCluster | Select-Object Name, ClusterCodeVersion
```

Poznamenejte si hodnotu **ClusterCodeVersion**. Tuto hodnotu použijete v další části.

## <a name="upgrade-the-runtime"></a>Upgrade modulu runtime

Pomocí hodnoty **ClusterCodeVersion** z předchozí části a rutiny `Get-ServiceFabricRuntimeUpgradeVersion` zjistěte, jaké verze jsou pro upgrade k dispozici. Tuto rutinu je možné spustit pouze z počítače připojeného k internetu. Pokud například chcete zobrazit, na jaké verze modulu runtime můžete upgradovat z verze `5.7.198.9494`, použijte následující příkaz:

```powershell
Get-ServiceFabricRuntimeUpgradeVersion -BaseVersion "5.7.198.9494"
```

Když máte seznam verzí, můžete clusteru Azure Service Fabric sdělit, aby provedl upgrade na novější modul runtime. Pokud je například pro upgrade k dispozici verze `6.0.219.9494`, můžete cluster upgradovat pomocí následujícího příkazu.

```powershell
Set-AzServiceFabricUpgradeType -ResourceGroupName SFCLUSTERTUTORIALGROUP `
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

Přejděte k dalšímu kurzu:

> [!div class="nextstepaction"]
> [Odstranění clusteru](service-fabric-tutorial-delete-cluster.md)
