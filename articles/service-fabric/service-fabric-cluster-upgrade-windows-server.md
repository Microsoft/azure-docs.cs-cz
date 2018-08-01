---
title: Upgrade samostatného clusteru Azure Service Fabric ve Windows serveru | Dokumentace Microsoftu
description: Upgrade kódu Azure Service Fabric a/nebo konfigurace, na kterém běží samostatného clusteru Service Fabric, včetně nastavení režimu aktualizace clusteru.
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
ms.date: 10/15/2017
ms.author: dekapur
ms.openlocfilehash: 9c2534644a0627bac9765621691cbba6ffccfe35
ms.sourcegitcommit: e3d5de6d784eb6a8268bd6d51f10b265e0619e47
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/01/2018
ms.locfileid: "39391307"
---
# <a name="upgrade-your-standalone-azure-service-fabric-cluster-on-windows-server"></a>Upgrade samostatného clusteru Azure Service Fabric na Windows serveru 
> [!div class="op_single_selector"]
> * [Azure cluster](service-fabric-cluster-upgrade.md)
> * [Samostatný cluster](service-fabric-cluster-upgrade-windows-server.md)
>
>

Možnosti upgradu pro moderního systému, je klíčem k dlouhodobý úspěch vašeho produktu. Cluster Azure Service Fabric je prostředek, který vlastníte. Tento článek popisuje, jak zajistit, že v clusteru vždy běží podporované verze kódu Service Fabric a konfigurace.

## <a name="control-the-service-fabric-version-that-runs-on-your-cluster"></a>Ovládací prvek, na kterém běží ve vašem clusteru Service Fabric verze
Nastavení clusteru můžete stáhnout aktualizace, Service Fabric, když společnost Microsoft vydává nové verze, nastavte konfiguraci clusteru fabricClusterAutoupgradeEnabled *true*. Vyberte podporovanou verzi Service Fabric, který chcete, aby váš cluster na hodnotu konfigurace clusteru fabricClusterAutoupgradeEnabled *false*.

> [!NOTE]
> Ujistěte se, že cluster běží vždy podporovanou verzi Service Fabric. Když Microsoft oznamuje vydání nové verze Service Fabric, předchozí verze budou označena k ukončení podpory po minimálně 60 dní od data oznámení. Oznámení nových vydaných verzích [na blog týmu Service Fabric](https://blogs.msdn.microsoft.com/azureservicefabric/). Si můžete vybrat v tomto okamžiku je nová verze.
>
>

Cluster můžete upgradovat na novou verzi pouze v případě, že používáte produkční – vizuální styl konfigurace uzlu, kde každý uzel Service Fabric je přidělena na samostatné fyzické nebo virtuální počítač. Pokud máte vývojový cluster, kde je více než jeden uzel Service Fabric na jeden fyzický nebo virtuální počítač, budete muset znovu vytvořit cluster v nové verzi.

Dva různé pracovní postupy svůj cluster můžete upgradovat na nejnovější verzi nebo s podporovanou verzí Service Fabric. Jeden pracovní postup je pro clustery, které jste připojení k nejnovější verzi stáhnete automaticky. Jiný pracovní postup je pro clustery, které nemají připojení k stažení nejnovější verze Service Fabric.

### <a name="upgrade-clusters-that-have-connectivity-to-download-the-latest-code-and-configuration"></a>Upgradovat clustery, které jste připojení ke stažení nejnovější kódu a konfigurace
Tyto kroky použijte cluster upgradovat na podporovanou verzi, pokud máte připojení k Internetu na uzly clusteru [Microsoft Download Center](http://download.microsoft.com).

Pro clustery, které jste připojení k [Microsoft Download Center](http://download.microsoft.com), Microsoft pravidelně kontroluje dostupnost nových verzí Service Fabric.

Když je dostupná nová verze Service Fabric, je balíček stáhli do clusteru a zřídit pro upgrade. Kromě toho k informování zákazníků tuto novou verzi, systém zobrazí upozornění stavu explicitní clusteru, který je podobný následujícímu:

"Aktuální verzi clusteru [verze č] ukončení podpory už [date]."

Jakmile je cluster používá nejnovější verze, upozornění zmizí.

#### <a name="cluster-upgrade-workflow"></a>Pracovní postup upgradu clusteru
Když se zobrazí upozornění stavu clusteru, postupujte takto:

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

    ```Powershell

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

### <a name="upgrade-clusters-that-have-no-connectivity-to-download-the-latest-code-and-configuration"></a>Upgradovat clustery, které mají *bez připojení k* si chcete stáhnout nejnovější kód a konfigurace
Tyto kroky použijte cluster upgradovat na podporovanou verzi, pokud uzly clusteru nemají připojení k Internetu na [Microsoft Download Center](http://download.microsoft.com).

> [!NOTE]
> Pokud máte spuštěný cluster, který není připojený k Internetu, budete muset monitorovat Týmový blog Service Fabric se dozvíte o nových verzích. Systém nezobrazí upozornění stavu clusteru vás upozorní na nové verze.  
>
>

#### <a name="auto-provisioning-vs-manual-provisioning"></a>Automatické zřizování a ruční zřizování
Pokud chcete povolit automatické stahování a registrace pro nejnovější verzi kódu, nastavte aktualizace služba Service Fabric. Pokyny najdete v tématu Tools\ServiceFabricUpdateService.zip\Readme_InstructionsAndHowTos.txt uvnitř [samostatného balíčku](service-fabric-cluster-standalone-package-contents.md).
Proces ručního nastavení postupujte podle těchto pokynů.

Upravit konfiguraci clusteru a nastavte následující vlastnost *false* před zahájením upgradu konfigurace:

        "fabricClusterAutoupgradeEnabled": false,

Podrobnosti o použití najdete v článku [Start ServiceFabricClusterConfigurationUpgrade](https://docs.microsoft.com/powershell/module/servicefabric/start-servicefabricclusterconfigurationupgrade) příkaz prostředí PowerShell. Nezapomeňte aktualizovat "clusterConfigurationVersion" v JSON před zahájením upgradu configuration.

```powershell

    Start-ServiceFabricClusterConfigurationUpgrade -ClusterConfigPath <Path to Configuration File>

```

#### <a name="cluster-upgrade-workflow"></a>Pracovní postup upgradu clusteru

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
    Register-ServiceFabricClusterPackage -Code -CodePackagePath MicrosoftAzureServiceFabric.5.3.301.9590.cab

     ```
6. Spusťte upgrade clusteru na dostupné verzi.

    ```Powershell

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


## <a name="upgrade-the-cluster-configuration"></a>Upgradovat konfiguraci clusteru
Než zahájíte configuration upgrade, můžete otestovat novou konfiguraci clusteru JSON spuštěním následujícího skriptu prostředí PowerShell v samostatném balíčku:

```powershell

    TestConfiguration.ps1 -ClusterConfigFilePath <Path to the new Configuration File> -OldClusterConfigFilePath <Path to the old Configuration File>

```
Nebo použijte tento skript:

```powershell

    TestConfiguration.ps1 -ClusterConfigFilePath <Path to the new Configuration File> -OldClusterConfigFilePath <Path to the old Configuration File> -FabricRuntimePackagePath <Path to the .cab file which you want to test the configuration against>

```

Některé konfigurace se nedá upgradovat, jako jsou koncové body, název clusteru, IP adresu uzlu, atd. Novou konfiguraci clusteru JSON je testován oproti původní a vyvolá chyby v okně Powershellu, pokud se vyskytl problém.

Chcete-li upgradovat konfigurace upgradu clusteru, spusťte [Start ServiceFabricClusterConfigurationUpgrade](https://docs.microsoft.com/powershell/module/servicefabric/start-servicefabricclusterconfigurationupgrade). Konfigurace upgradu je zpracovaných upgradovací doména podle upgradovací domény.

```powershell

    Start-ServiceFabricClusterConfigurationUpgrade -ClusterConfigPath <Path to Configuration File>

```

### <a name="cluster-certificate-config-upgrade"></a>Upgrade konfigurace certifikátů clusteru  
Certifikát clusteru se používá k ověřování mezi uzly clusteru. Certifikáty vyměnit by měl provést další opatrně, protože selhání blokuje komunikaci mezi uzly clusteru.

Technicky vzato jsou podporovány čtyř možností:  

* Jeden certifikát upgrade: způsob upgradu je certifikát (primární) -> certifikát B (primární) -> C certifikát (primární) ->...

* Double upgradu certifikátu: způsob upgradu je certifikát (primární) -> certifikátu (primární) a B (sekundární) -> certifikát B (primární) -> certifikát B (primární) a jazyka C (sekundární) -> C certifikát (primární) ->...

* Typ upgradu certifikátu: Konfigurace certifikátu na základě CommonName configuration <> – na základě kryptografického otisku certifikátu. Například kryptografický otisk certifikátu (primární) a kryptografický otisk B (sekundární) -> certifikát CommonName C.

* Upgrade kryptografický otisk vystavitele certifikátu: způsob upgradu je CN certifikát = A, IssuerThumbprint = IT1 (primární) -> certifikátů CN = A, IssuerThumbprint = IT1 IT2 (primární) -> CN certifikát = A, IssuerThumbprint = IT2 (primární).


## <a name="next-steps"></a>Další postup
* Zjistěte, jak přizpůsobit některé [nastavení clusteru Service Fabric](service-fabric-cluster-fabric-settings.md).
* Zjistěte, jak [škálování clusteru dovnitř a ven](service-fabric-cluster-scale-up-down.md).
* Další informace o [upgrady aplikací](service-fabric-application-upgrade.md).

<!--Image references-->
[getfabversions]: ./media/service-fabric-cluster-upgrade-windows-server/getfabversions.PNG
