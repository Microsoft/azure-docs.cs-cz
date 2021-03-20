---
title: Rozdíly v Azure Service Fabric mezi systémy Linux a Windows
description: Rozdíly mezi Azure Service Fabric v Linuxu a Azure Service Fabric ve Windows.
ms.topic: conceptual
ms.date: 2/23/2018
ms.openlocfilehash: 4adae60ded804450c25809faa0edca6bb058adf7
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2021
ms.locfileid: "96574492"
---
# <a name="differences-between-service-fabric-on-linux-and-windows"></a>Rozdíly mezi Service Fabric v Linuxu a ve Windows

Existují určité funkce, které jsou ve Windows podporované, ale v Linuxu zatím ne. S každou vydanou verzí se rozdíly mezi funkcemi budou zmenšovat, až si nakonec sady funkcí budou odpovídat. Mezi nejnovějšími dostupnými verzemi existují následující rozdíly.

* Zástupné (reverzní proxy) je ve verzi Preview v systému Linux
* Samostatný instalační program pro Linux ještě není v Linuxu k dispozici.
* Přesměrování konzoly (nepodporuje se v produkčních clusterech s Linuxem ani s Windows).
* Fault Analysis Service (FAS) v Linuxu.
* Služba DNS pro služby Service Fabric (služba DNS je podporována pro kontejnery v Linuxu).
* Ekvivalenty příkazů rozhraní příkazového řádku pro určité příkazy PowerShellu (seznam je níže; většina se vztahuje pouze na samostatné clustery).
* [Rozdíly v implementaci protokolu, které mohou mít vliv na škálovatelnost](service-fabric-concepts-scalability.md#choosing-a-platform)

## <a name="powershell-cmdlets-that-do-not-work-against-a-linux-service-fabric-cluster"></a>Rutiny PowerShellu, které nefungují proti linuxovému clusteru Service Fabric

* Invoke-ServiceFabricChaosTestScenario
* Invoke-ServiceFabricFailoverTestScenario
* Invoke-ServiceFabricPartitionDataLoss
* Invoke-ServiceFabricPartitionQuorumLoss
* Restart-ServiceFabricPartition
* Start-ServiceFabricNode
* Stop-ServiceFabricNode
* Get-ServiceFabricImageStoreContent
* Get-ServiceFabricChaosReport
* Get-ServiceFabricNodeTransitionProgress
* Get-ServiceFabricPartitionDataLossProgress
* Get-ServiceFabricPartitionQuorumLossProgress
* Get-ServiceFabricPartitionRestartProgress
* Get-ServiceFabricTestCommandStatusList
* Remove-ServiceFabricTestState
* Start-ServiceFabricChaos
* Start-ServiceFabricNodeTransition
* Start-ServiceFabricPartitionDataLoss
* Start-ServiceFabricPartitionQuorumLoss
* Start-ServiceFabricPartitionRestart
* Stop-ServiceFabricChaos
* Stop-ServiceFabricTestCommand
* Get-ServiceFabricNodeConfiguration
* Get-ServiceFabricClusterConfiguration
* Get-ServiceFabricClusterConfigurationUpgradeStatus
* Get-ServiceFabricPackageDebugParameters
* New-ServiceFabricPackageDebugParameter
* New-ServiceFabricPackageSharingPolicy
* Add-ServiceFabricNode
* Copy-ServiceFabricClusterPackage
* Get-ServiceFabricRuntimeSupportedVersion
* Get-ServiceFabricRuntimeUpgradeVersion
* New-ServiceFabricCluster
* New-ServiceFabricNodeConfiguration
* Remove-ServiceFabricCluster
* Remove-ServiceFabricClusterPackage
* Remove-ServiceFabricNodeConfiguration
* Test-ServiceFabricClusterManifest
* Test-ServiceFabricConfiguration
* Update-ServiceFabricNodeConfiguration
* Approve-ServiceFabricRepairTask
* Complete-ServiceFabricRepairTask
* Get-ServiceFabricRepairTask
* Invoke-ServiceFabricDecryptText
* Invoke-ServiceFabricEncryptSecret
* Invoke-ServiceFabricEncryptText
* Invoke-ServiceFabricInfrastructureCommand
* Invoke-ServiceFabricInfrastructureQuery
* Remove-ServiceFabricRepairTask
* Start-ServiceFabricRepairTask
* Stop-ServiceFabricRepairTask
* Update-ServiceFabricRepairTaskHealthPolicy

## <a name="next-steps"></a>Další kroky

* [Příprava vývojového prostředí v Linuxu](service-fabric-get-started-linux.md)
* [Příprava vývojového prostředí v OSX](service-fabric-get-started-mac.md)
* [Vytvoření a nasazení první aplikace Service Fabric v Javě v Linuxu pomocí Yeomana](service-fabric-create-your-first-linux-application-with-java.md)
* [Vytvoření a nasazení první aplikace Service Fabric Java v systému Linux pomocí Service Fabric modulu plug-in pro zatmění](service-fabric-get-started-eclipse.md)
* [Vytvoření první aplikace v CSharp v Linuxu](service-fabric-create-your-first-linux-application-with-csharp.md)
* [Správa aplikací pomocí Service Fabric CLI](service-fabric-application-lifecycle-sfctl.md)
