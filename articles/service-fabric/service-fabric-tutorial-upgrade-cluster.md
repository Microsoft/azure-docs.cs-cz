---
title: Upgrade modulu runtime Service Fabric v Azure
description: V tomto kurzu se dozvíte, jak pomocí PowerShellu upgradovat modul runtime clusteru Service Fabric hostovaného v Azure.
ms.topic: tutorial
ms.date: 07/22/2019
ms.custom: mvc
ms.openlocfilehash: 23b3aabf8e991e512ef9a5c07d725c3084ea7f83
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2021
ms.locfileid: "86244731"
---
# <a name="tutorial-upgrade-the-runtime-of-a-service-fabric-cluster-in-azure"></a>Kurz: Upgrade modulu runtime clusteru Service Fabric v Azure

Tento kurz je čtvrtou částí série a ukazuje, jak upgradovat modul runtime Service Fabric v clusteru Azure Service Fabric. Tato část kurzu je napsaná pro Service Fabric clusterů běžících na Azure a nevztahuje se na samostatné clustery Service Fabric.

> [!WARNING]
> Tato část kurzu vyžaduje PowerShell. Nástroje Azure CLI ještě upgradování modulu runtime clusteru nepodporují. Alternativně je možné cluster upgradovat na portálu. Další informace najdete v tématu [Upgrade clusteru Azure Service Fabric](service-fabric-cluster-upgrade.md).

Pokud váš cluster už používá nejnovější Service Fabric runtime, nemusíte tento krok provádět. Tento článek však můžete použít k instalaci jakéhokoli podporovaného modulu runtime v clusteru Azure Service Fabric.

V tomto kurzu se naučíte:

> [!div class="checklist"]
> * Načtení verze clusteru
> * Nastavení verze clusteru

V této sérii kurzů se naučíte:
> [!div class="checklist"]
> * Vytvoření zabezpečeného [clusteru s Windows](service-fabric-tutorial-create-vnet-and-windows-cluster.md) v Azure pomocí šablony
> * [Monitorování clusteru](service-fabric-tutorial-monitor-cluster.md)
> * [Horizontální snížení nebo navýšení kapacity clusteru](service-fabric-tutorial-scale-cluster.md)
> * Upgrade modulu runtime clusteru
> * [Odstranění clusteru](service-fabric-tutorial-delete-cluster.md)


[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="prerequisites"></a>Předpoklady

Než začnete s tímto kurzem:

* Pokud nemáte předplatné Azure, vytvořte si [bezplatný účet](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) .
* Nainstalujte [Azure PowerShell](/powershell/azure/install-az-ps) nebo rozhraní příkazového [řádku Azure](/cli/azure/install-azure-cli).
* Vytvoření zabezpečeného [clusteru s Windows](service-fabric-tutorial-create-vnet-and-windows-cluster.md) v Azure
* Nastavte vývojové prostředí systému Windows. Nainstalujte [Visual Studio 2019](https://www.visualstudio.com) a vývojové úlohy pro vývoj pro **Azure**, **ASP.NET a** vývoj pro web a **.NET Core pro různé platformy** .  Potom nastavte [vývojové prostředí .NET](service-fabric-get-started.md).

### <a name="sign-in-to-azure"></a>Přihlášení k Azure

Před spouštěním příkazů Azure se přihlaste ke svému účtu Azure a vyberte své předplatné.

```powershell
Connect-AzAccount
Get-AzSubscription
Set-AzContext -SubscriptionId <guid>
```

## <a name="get-the-runtime-version"></a>Získání verze modulu runtime

Po připojení k Azure vyberte předplatné, které obsahuje Cluster Service Fabric, a můžete získat běhovou verzi clusteru.

```powershell
Get-AzServiceFabricCluster -ResourceGroupName SFCLUSTERTUTORIALGROUP -Name aztestcluster `
    | Select-Object ClusterCodeVersion
```

Nebo stačí získat seznam všech clusterů v rámci vašeho předplatného s následujícím příkladem:

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

Nejdřív se připojte ke clusteru pomocí certifikátu TLS/SSL vytvořeného v první části tohoto kurzu. Použijte k tomu rutinu `Connect-ServiceFabricCluster` nebo `sfctl cluster upgrade-status`.

```powershell
$endpoint = "<mycluster>.southcentralus.cloudapp.azure.com:19000"
$thumbprint = "63EB5BA4BC2A3BADC42CA6F93D6F45E5AD98A1E4"

Connect-ServiceFabricCluster -ConnectionEndpoint $endpoint `
                             -KeepAliveIntervalInSec 10 `
                             -X509Credential -ServerCertThumbprint $thumbprint `
                             -FindType FindByThumbprint -FindValue $thumbprint `
                             -StoreLocation CurrentUser -StoreName My
```

```console
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

```console
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
