---
title: Upgrade verze samostatného clusteru
description: Upgradujte kód Azure Service Fabric, který spouští samostatný cluster Service Fabric.
author: dkkapur
ms.topic: conceptual
ms.date: 11/09/2018
ms.author: dekapur
ms.openlocfilehash: 489a90180454e2b4a9dad34730fbd3c4f235a2ad
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "77598098"
---
# <a name="upgrade-the-service-fabric-version-that-runs-on-your-cluster"></a>Upgrade verze Service Fabric, která běží v clusteru 

Pro každý moderní systém je schopnost upgradu klíčem k dlouhodobému úspěchu vašeho produktu. Cluster Azure Service Fabric je prostředek, který vlastníte. Tento článek popisuje, jak upgradovat verzi Service Fabric spuštěnou v samostatném clusteru.

> [!NOTE]
> Ujistěte se, že váš cluster vždy běží podporovanou verzi Service Fabric. Když společnost Microsoft oznámí vydání nové verze Service Fabric, předchozí verze je označena pro ukončení podpory po minimálně 60 dnů od data oznámení. Nové verze jsou oznámeny [na blogu týmu Service Fabric](https://blogs.msdn.microsoft.com/azureservicefabric/). Nová verze je k dispozici na výběr v tomto bodě.
>
>

Cluster můžete upgradovat na novou verzi pouze v případě, že používáte konfiguraci uzlu ve stylu výroby, kde je každý uzel Service Fabric přidělen na samostatném fyzickém nebo virtuálním počítači. Pokud máte vývojový cluster, kde je více než jeden uzel Service Fabric na jednom fyzickém nebo virtuálním počítači, musíte znovu vytvořit cluster s novou verzí.

Dva odlišné pracovní postupy můžete upgradovat clusteru na nejnovější verzi nebo podporovanou verzi Service Fabric. Jeden pracovní postup je určen pro clustery, které mají připojení ke stažení nejnovější verze automaticky. Druhý pracovní postup je pro clustery, které nemají připojení ke stažení nejnovější verze Service Fabric.

## <a name="enable-auto-upgrade-of-the-service-fabric-version-of-your-cluster"></a>Povolení automatického upgradu verze service fabric clusteru
Chcete-li nastavit cluster tak, aby stahoval aktualizace service `fabricClusterAutoupgradeEnabled` fabricu v případě, že společnost Microsoft vydá novou verzi, nastavte konfiguraci clusteru na *hodnotu true*. Chcete-li ručně vybrat podporovanou verzi service fabric, ve které `fabricClusterAutoupgradeEnabled` chcete, aby byl cluster zapnutý, nastavte konfiguraci clusteru na *hodnotu false*.

## <a name="upgrade-clusters-that-have-connectivity-to-download-the-latest-code-and-configuration"></a>Upgrade clusterů, které mají připojení ke stažení nejnovějšího kódu a konfigurace
Pomocí těchto kroků můžete cluster upgradovat na podporovanou verzi, pokud mají uzly clusteru připojení k internetu ke [službě Stažení softwaru](https://download.microsoft.com).

U clusterů, které jsou připojeny k [webu Stažení softwaru](https://download.microsoft.com), společnost Microsoft pravidelně kontroluje dostupnost nových verzí service fabric.

Když je k dispozici nová verze Service Fabric, balíček se stáhne místně do clusteru a zřídí pro upgrade. Kromě toho informovat zákazníka o této nové verzi, systém zobrazuje explicitní upozornění na stav clusteru, který je podobný následující:

"Aktuální verze clusteru [verze #] podpora končí [datum]."

Po clusteru je spuštěna nejnovější verze, upozornění zmizí.

Když se zobrazí upozornění na stav clusteru, upgradujte cluster:

1. Připojte se ke clusteru z libovolného počítače, který má přístup správce ke všem počítačům, které jsou uvedeny jako uzly v clusteru. Počítač, na který je tento skript spuštěn, nemusí být součástí clusteru.

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

2. Získejte seznam verzí service fabric, které můžete upgradovat na.

    ```powershell
    ###### Get the list of available Service Fabric versions
    Get-ServiceFabricRegisteredClusterCodeVersion
    ```

    Měli byste získat výstup podobný tomuto:

    ![Získat verze service fabric][getfabversions]
3. Spusťte upgrade clusteru na dostupnou verzi pomocí příkazu [Start-ServiceFabricClusterUpgrade](https://docs.microsoft.com/powershell/module/servicefabric/start-servicefabricclusterupgrade) Windows PowerShell.

    ```powershell
    Start-ServiceFabricClusterUpgrade -Code -CodePackageVersion <codeversion#> -Monitored -FailureAction Rollback

    ###### Here is a filled-out example

    Start-ServiceFabricClusterUpgrade -Code -CodePackageVersion 5.3.301.9590 -Monitored -FailureAction Rollback
    ```
   Chcete-li sledovat průběh upgradu, můžete použít aplikaci Service Fabric Explorer nebo spustit následující příkaz prostředí PowerShell:

    ```powershell
    Get-ServiceFabricClusterUpgrade
    ```

    Pokud nejsou splněny zásady stavu clusteru, upgrade se vrátí zpět. Chcete-li zadat vlastní zásady stavu pro příkaz Start-ServiceFabricClusterUpgrade, naleznete v dokumentaci k [webu Start-ServiceFabricClusterUpgrade](https://docs.microsoft.com/powershell/module/servicefabric/start-servicefabricclusterupgrade).

    Po opravě problémů, které vedly k vrácení zpět, inicializovat upgrade znovu podle stejných kroků, jak je popsáno výše.

## <a name="upgrade-clusters-that-have-no-connectivity-to-download-the-latest-code-and-configuration"></a>Upgrade clusterů, které *nemají připojení* ke stažení nejnovějšího kódu a konfigurace
Pomocí těchto kroků můžete cluster upgradovat na podporovanou verzi, pokud uzly clusteru nemají připojení k Internetu ke [službě Stažení softwaru](https://download.microsoft.com).

> [!NOTE]
> Pokud používáte cluster, který není připojený k internetu, budete muset sledovat [service fabric tým blog](https://blogs.msdn.microsoft.com/azureservicefabric/) se dozvíte o nových verzích. Systém nezobrazuje upozornění na stav clusteru, které by vás upozornilo na nové verze.  
>
>

### <a name="auto-provisioning-vs-manual-provisioning"></a>Automatické zřizování vs. ruční zřizování
Chcete-li povolit automatické stahování a registraci pro nejnovější verzi kódu, nastavte službu Service Fabric Update Service Service. Pokyny naleznete v *tématu Tools\ServiceFabricUpdateService.zip\Readme_InstructionsAndHowTos.txt* v [samostatném balíčku](service-fabric-cluster-standalone-package-contents.md).

Ruční proces postupujte podle těchto pokynů.

Před zahájením upgradu konfigurace upravte konfiguraci clusteru tak, aby byla následující vlastnost nastavena na *hodnotu false:*

```json
"fabricClusterAutoupgradeEnabled": false,
```

Podrobnosti o použití naleznete v příkazu [Start-ServiceFabricClusterConfigurationUpgrade](https://docs.microsoft.com/powershell/module/servicefabric/start-servicefabricclusterconfigurationupgrade) PowerShell. Před spuštěním upgradu konfigurace nezapomeňte aktualizovat 'clusterConfigurationVersion' ve vašem JSON.

```powershell
    Start-ServiceFabricClusterConfigurationUpgrade -ClusterConfigPath <Path to Configuration File>
```

### <a name="cluster-upgrade-workflow"></a>Pracovní postup upgradu clusteru

1. [Spusťte Get-ServiceFabricClusterUpgrade](https://docs.microsoft.com/powershell/module/servicefabric/get-servicefabricclusterupgrade) z jednoho z uzlů v clusteru a poznamenejte si *TargetCodeVersion*.

2. Spusťte následující z počítače připojeného k internetu a seznam všech verzí kompatibilních s upgradem s aktuální verzí a stáhněte si odpovídající balíček z přidružených odkazů ke stažení:

    ```powershell
    ###### Get list of all upgrade compatible packages  
    Get-ServiceFabricRuntimeUpgradeVersion -BaseVersion <TargetCodeVersion as noted in Step 1> 
    ```

3. Připojte se ke clusteru z libovolného počítače, který má přístup správce ke všem počítačům, které jsou uvedeny jako uzly v clusteru. Počítač, na který je tento skript spuštěn, nemusí být součástí clusteru.

    ```powershell
    ###### Get the list of available Service Fabric versions
    Copy-ServiceFabricClusterPackage -Code -CodePackagePath <name of the .cab file including the path to it> -ImageStoreConnectionString "fabric:ImageStore"

    ###### Here is a filled-out example
    Copy-ServiceFabricClusterPackage -Code -CodePackagePath .\MicrosoftAzureServiceFabric.5.3.301.9590.cab -ImageStoreConnectionString "fabric:ImageStore"
    ```
4. Zkopírujte stažený balíček do úložiště bitových kopií clusteru.

5. Zaregistrujte zkopírovaný balíček.

    ```powershell
    ###### Get the list of available Service Fabric versions
    Register-ServiceFabricClusterPackage -Code -CodePackagePath <name of the .cab file>

    ###### Here is a filled-out example
    Register-ServiceFabricClusterPackage -Code -CodePackagePath MicrosoftAzureServiceFabric.5.3.301.9590.cab
    ```
6. Spusťte upgrade clusteru na dostupnou verzi.

    ```powershell
    Start-ServiceFabricClusterUpgrade -Code -CodePackageVersion <codeversion#> -Monitored -FailureAction Rollback

    ###### Here is a filled-out example
    Start-ServiceFabricClusterUpgrade -Code -CodePackageVersion 5.3.301.9590 -Monitored -FailureAction Rollback
    ```
    Průběh upgradu můžete sledovat v aplikaci Service Fabric Explorer nebo můžete spustit následující příkaz prostředí PowerShell:

    ```powershell
    Get-ServiceFabricClusterUpgrade
    ```

    Pokud nejsou splněny zásady stavu clusteru, upgrade se vrátí zpět. Chcete-li zadat vlastní zásady stavu pro příkaz Start-ServiceFabricClusterUpgrade, naleznete v dokumentaci k [webu Start-ServiceFabricClusterUpgrade](https://docs.microsoft.com/powershell/module/servicefabric/start-servicefabricclusterupgrade).

    Po opravě problémů, které vedly k vrácení zpět, inicializovat upgrade znovu podle stejných kroků, jak je popsáno výše.

## <a name="next-steps"></a>Další kroky
* [Upgrade konfigurace samostatného clusteru](service-fabric-cluster-config-upgrade-windows-server.md)
* Přizpůsobte některá [nastavení clusteru Service Fabric](service-fabric-cluster-fabric-settings.md).
* [Škálujte cluster zapínání a vyzínání clusteru](service-fabric-cluster-scale-up-down.md).

<!--Image references-->
[getfabversions]: ./media/service-fabric-cluster-upgrade-windows-server/getfabversions.PNG
