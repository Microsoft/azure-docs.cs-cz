---
title: Upgrade na verzi samostatného clusteru Azure Service Fabric | Dokumentace Microsoftu
description: Upgradujte Azure Service Fabric kód, který spouští samostatný cluster Service Fabric.
services: service-fabric
documentationcenter: .net
author: dkkapur
manager: timlt
editor: ''
ms.assetid: 66296cc6-9524-4c6a-b0a6-57c253bdf67e
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/09/2018
ms.author: dekapur
ms.openlocfilehash: cac0b620ae0361ba6903599799f80adb9a917fc7
ms.sourcegitcommit: 7804131dbe9599f7f7afa59cacc2babd19e1e4b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/17/2018
ms.locfileid: "51853111"
---
# <a name="upgrade-the-service-fabric-version-that-runs-on-your-cluster"></a>Upgrade, na kterém běží ve vašem clusteru Service Fabric verze 

Možnosti upgradu pro moderního systému, je klíčem k dlouhodobý úspěch vašeho produktu. Cluster Azure Service Fabric je prostředek, který vlastníte. Tento článek popisuje, jak upgradovat na verzi běžící na samostatný cluster Service Fabric.

> [!NOTE]
> Ujistěte se, že cluster běží vždy podporovanou verzi Service Fabric. Když Microsoft oznamuje vydání nové verze Service Fabric, předchozí verze budou označena k ukončení podpory po minimálně 60 dní od data oznámení. Oznámení nových vydaných verzích [na blog týmu Service Fabric](https://blogs.msdn.microsoft.com/azureservicefabric/). Si můžete vybrat v tomto okamžiku je nová verze.
>
>

Cluster můžete upgradovat na novou verzi pouze v případě, že používáte produkční – vizuální styl konfigurace uzlu, kde každý uzel Service Fabric je přidělena na samostatné fyzické nebo virtuální počítač. Pokud máte vývojový cluster, kde je více než jeden uzel Service Fabric na jeden fyzický nebo virtuální počítač, budete muset znovu vytvořit cluster v nové verzi.

Dva různé pracovní postupy svůj cluster můžete upgradovat na nejnovější verzi nebo s podporovanou verzí Service Fabric. Jeden pracovní postup je pro clustery, které jste připojení k nejnovější verzi stáhnete automaticky. Jiný pracovní postup je pro clustery, které nemají připojení k stažení nejnovější verze Service Fabric.

## <a name="enable-auto-upgrade-of-the-service-fabric-version-of-your-cluster"></a>Povolit automatický upgrade na verzi clusteru Service Fabric
Chcete-li nastavit cluster můžete stáhnout aktualizace, Service Fabric, když společnost Microsoft vydává nové verze, nastavte `fabricClusterAutoupgradeEnabled` konfiguraci clusteru, aby *true*. Chcete-li ručně vyberte podporovanou verzi Service Fabric, který chcete, aby váš cluster na, nastavte `fabricClusterAutoupgradeEnabled` konfiguraci clusteru, aby *false*.

## <a name="upgrade-clusters-that-have-connectivity-to-download-the-latest-code-and-configuration"></a>Upgradovat clustery, které jste připojení ke stažení nejnovější kódu a konfigurace
Tyto kroky použijte cluster upgradovat na podporovanou verzi, pokud máte připojení k Internetu na uzly clusteru [Microsoft Download Center](https://download.microsoft.com).

Pro clustery, které jste připojení k [Microsoft Download Center](https://download.microsoft.com), Microsoft pravidelně kontroluje dostupnost nových verzí Service Fabric.

Když je dostupná nová verze Service Fabric, je balíček stáhli do clusteru a zřídit pro upgrade. Kromě toho k informování zákazníků tuto novou verzi, systém zobrazí upozornění stavu explicitní clusteru, který je podobný následujícímu:

"Aktuální verzi clusteru [verze č] ukončení podpory už [date]."

Jakmile je cluster používá nejnovější verze, upozornění zmizí.

Když se zobrazí upozornění stavu clusteru, upgrade clusteru:

1. Připojte se ke clusteru z libovolného počítače, který má přístup správce pro všechny počítače, které jsou označeny jako uzly v clusteru. Počítače, ve kterém se skript spouští na nemusí být součástí clusteru.

    ```powershell
    ###### connect to the secure cluster using certs
    $ClusterName= "mysecurecluster.something.com:19000"
    $CertThumbprint= "70EF5E22ADB649799DA3C8B6A6BF7FG2D630F8F3"
    Connect-serviceFabricCluster -ConnectionEndpoint $ClusterName -KeepAliveIntervalInSec 10 `
        -X509Credential `
        -ServerCertThumbprint $CertThumbprint  `
        -FindType FindByThumbprint `
        -FindValue $CertThumbprint `
        -StoreLocation CurrentUser `
        -StoreName My
    ```

2. Získá seznam verzí Service Fabric, které můžete provést upgrade.

    ```powershell
    ###### Get the list of available Service Fabric versions
    Get-ServiceFabricRegisteredClusterCodeVersion
    ```

    Měl zobrazit výstup podobný tomuto:

    ![Získání verzí Service Fabric][getfabversions]
3. Spuštění upgradu clusteru dostupnou verzi pomocí [Start ServiceFabricClusterUpgrade](https://docs.microsoft.com/powershell/module/servicefabric/start-servicefabricclusterupgrade) příkazu Windows Powershellu.

    ```powershell
    Start-ServiceFabricClusterUpgrade -Code -CodePackageVersion <codeversion#> -Monitored -FailureAction Rollback

    ###### Here is a filled-out example

    Start-ServiceFabricClusterUpgrade -Code -CodePackageVersion 5.3.301.9590 -Monitored -FailureAction Rollback
    ```
   Můžete sledovat průběh upgradu můžete použít Service Fabric Exploreru nebo pomocí následujícího příkazu Powershellu:

    ```powershell
    Get-ServiceFabricClusterUpgrade
    ```

    Pokud zásady stavu clusteru nejsou splněny, upgrade se vrátí zpět. Pokud chcete zadat zásady vlastní stavu pro příkaz Start-ServiceFabricClusterUpgrade, naleznete v dokumentaci k [Start ServiceFabricClusterUpgrade](https://docs.microsoft.com/powershell/module/servicefabric/start-servicefabricclusterupgrade).

    Po vyřešení problémů, z kterých vzniklo vrácení změn zahájení upgradu znovu podle stejných kroků jako výše popsaným.

## <a name="upgrade-clusters-that-have-no-connectivity-to-download-the-latest-code-and-configuration"></a>Upgradovat clustery, které mají *bez připojení k* si chcete stáhnout nejnovější kód a konfigurace
Tyto kroky použijte cluster upgradovat na podporovanou verzi, pokud uzly clusteru nemají připojení k Internetu na [Microsoft Download Center](https://download.microsoft.com).

> [!NOTE]
> Pokud máte spuštěný cluster, který není připojený k Internetu, musíte monitorovat [blog týmu Service Fabric](https://blogs.msdn.microsoft.com/azureservicefabric/) Další informace o nových verzích. Systém nezobrazí upozornění stavu clusteru vás upozorní na nové verze.  
>
>

### <a name="auto-provisioning-vs-manual-provisioning"></a>Automatické zřizování a ruční zřizování
Pokud chcete povolit automatické stahování a registrace pro nejnovější verzi kódu, nastavte aktualizace služba Service Fabric. Pokyny najdete v tématu *Tools\ServiceFabricUpdateService.zip\Readme_InstructionsAndHowTos.txt* v [samostatného balíčku](service-fabric-cluster-standalone-package-contents.md).

Proces ručního nastavení postupujte podle těchto pokynů.

Upravit konfiguraci clusteru a nastavte následující vlastnost *false* před zahájením upgradu konfigurace:

```json
"fabricClusterAutoupgradeEnabled": false,
```

Podrobnosti o použití najdete v článku [Start ServiceFabricClusterConfigurationUpgrade](https://docs.microsoft.com/powershell/module/servicefabric/start-servicefabricclusterconfigurationupgrade) příkaz prostředí PowerShell. Nezapomeňte aktualizovat "clusterConfigurationVersion" v JSON před zahájením upgradu configuration.

```powershell
    Start-ServiceFabricClusterConfigurationUpgrade -ClusterConfigPath <Path to Configuration File>
```

### <a name="cluster-upgrade-workflow"></a>Pracovní postup upgradu clusteru

1. Spustit [Get-ServiceFabricClusterUpgrade](https://docs.microsoft.com/powershell/module/servicefabric/get-servicefabricclusterupgrade) na jednom z uzlů v clusteru a Všimněte si *TargetCodeVersion*.

2. Spuštěním následujícího příkazu z počítače připojeného k Internetu zobrazí seznam všech verzí upgrade kompatibilní s aktuální verzí a stáhněte si odpovídající balíček z odkazů přidružené ke stažení:

    ```powershell
    ###### Get list of all upgrade compatible packages  
    Get-ServiceFabricRuntimeUpgradeVersion -BaseVersion <TargetCodeVersion as noted in Step 1> 
    ```

3. Připojte se ke clusteru z libovolného počítače, který má přístup správce pro všechny počítače, které jsou označeny jako uzly v clusteru. Počítače, ve kterém se skript spouští na nemusí být součástí clusteru.

    ```powershell
    ###### Get the list of available Service Fabric versions
    Copy-ServiceFabricClusterPackage -Code -CodePackagePath <name of the .cab file including the path to it> -ImageStoreConnectionString "fabric:ImageStore"

    ###### Here is a filled-out example
    Copy-ServiceFabricClusterPackage -Code -CodePackagePath .\MicrosoftAzureServiceFabric.5.3.301.9590.cab -ImageStoreConnectionString "fabric:ImageStore"
    ```
4. Zkopírujte stažený balíček do úložiště imagí clusteru.

5. Zaregistrujte zkopírovaný balíčku.

    ```powershell
    ###### Get the list of available Service Fabric versions
    Register-ServiceFabricClusterPackage -Code -CodePackagePath <name of the .cab file>

    ###### Here is a filled-out example
    Register-ServiceFabricClusterPackage -Code -CodePackagePath .\MicrosoftAzureServiceFabric.5.3.301.9590.cab
    ```
6. Spusťte upgrade clusteru na dostupné verzi.

    ```powershell
    Start-ServiceFabricClusterUpgrade -Code -CodePackageVersion <codeversion#> -Monitored -FailureAction Rollback

    ###### Here is a filled-out example
    Start-ServiceFabricClusterUpgrade -Code -CodePackageVersion 5.3.301.9590 -Monitored -FailureAction Rollback
    ```
    Můžete sledovat průběh upgradu v Service Fabric Explorer, nebo spuštěním následujícího příkazu Powershellu:

    ```powershell
    Get-ServiceFabricClusterUpgrade
    ```

    Pokud zásady stavu clusteru nejsou splněny, upgrade se vrátí zpět. Pokud chcete zadat zásady vlastní stavu pro příkaz Start-ServiceFabricClusterUpgrade, naleznete v dokumentaci k [Start ServiceFabricClusterUpgrade](https://docs.microsoft.com/powershell/module/servicefabric/start-servicefabricclusterupgrade).

    Po vyřešení problémů, z kterých vzniklo vrácení změn zahájení upgradu znovu podle stejných kroků jako výše popsaným.

## <a name="next-steps"></a>Další postup
* [Upgradovat konfiguraci samostatného clusteru](service-fabric-cluster-config-upgrade-windows-server.md)
* Přizpůsobit některé [nastavení clusteru Service Fabric](service-fabric-cluster-fabric-settings.md).
* [Škálování clusteru dovnitř a ven](service-fabric-cluster-scale-up-down.md).

<!--Image references-->
[getfabversions]: ./media/service-fabric-cluster-upgrade-windows-server/getfabversions.PNG
