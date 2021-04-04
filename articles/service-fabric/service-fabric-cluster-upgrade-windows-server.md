---
title: Upgrade verze samostatného clusteru
description: Upgradujte kód Azure Service Fabric, který spouští samostatný cluster Service Fabric.
ms.topic: conceptual
ms.date: 11/09/2018
ms.openlocfilehash: 5831fcffe718e4966c3444a29d35c5bbd55e5f57
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/29/2021
ms.locfileid: "91842933"
---
# <a name="upgrade-the-service-fabric-version-that-runs-on-your-cluster"></a>Upgrade verze Service Fabric, která běží na vašem clusteru 

Pro všechny moderní systémy je možnost upgradu klíčem na dlouhodobou úspěšnost vašeho produktu. Cluster Azure Service Fabric je prostředek, který vlastníte. Tento článek popisuje, jak upgradovat verzi Service Fabric běžícího na samostatném clusteru.

> [!NOTE]
> Ujistěte se, že cluster vždy používá podporovanou verzi Service Fabric. Když společnost Microsoft oznamuje vydání nové verze Service Fabric, bude předchozí verze označena pro konec podpory po nejméně 60 dní od data oznámení. Nové verze jsou oznámeny [na blogu týmu Service Fabric](https://techcommunity.microsoft.com/t5/azure-service-fabric/bg-p/Service-Fabric). Nová verze je dostupná k výběru v tomto okamžiku.
>
>

Cluster můžete upgradovat na novou verzi pouze v případě, že používáte konfiguraci uzlu ve stylu produkčního prostředí, kde je každý Service Fabric uzel přidělen na samostatném fyzickém nebo virtuálním počítači. Pokud máte vývojový cluster, ve kterém je více než jeden Service Fabric uzel na jednom fyzickém nebo virtuálním počítači, musíte cluster znovu vytvořit s novou verzí.

Dva různé pracovní postupy mohou upgradovat cluster na nejnovější verzi nebo na podporovanou verzi Service Fabric. Jeden pracovní postup je pro clustery, které mají připojení k automatickému stažení nejnovější verze. Druhý pracovní postup je pro clustery, které nemají připojení ke stažení nejnovější verze Service Fabric.

## <a name="enable-auto-upgrade-of-the-service-fabric-version-of-your-cluster"></a>Povolení automatického upgradu Service Fabric verze clusteru
Chcete-li nastavit cluster tak, aby stahoval aktualizace Service Fabric, když společnost Microsoft vydává novou verzi, nastavte `fabricClusterAutoupgradeEnabled` konfiguraci clusteru na *hodnotu true*. Chcete-li ručně vybrat podporovanou verzi Service Fabric, na které má být cluster zapnutý, nastavte `fabricClusterAutoupgradeEnabled` konfiguraci clusteru na *hodnotu NEPRAVDA*.

## <a name="upgrade-clusters-that-have-connectivity-to-download-the-latest-code-and-configuration"></a>Upgradujte clustery, které mají připojení ke stažení nejnovějšího kódu a konfigurace.
Pomocí těchto kroků můžete cluster upgradovat na podporovanou verzi, pokud uzly clusteru mají připojení k Internetu na webu [služby Stažení softwaru](https://download.microsoft.com).

V případě clusterů, které mají připojení ke službě [Stažení softwaru](https://download.microsoft.com), společnost Microsoft pravidelně kontroluje dostupnost nových verzí Service Fabric.

Pokud je k dispozici nová verze Service Fabric, balíček se stáhne místně do clusteru a zřídí pro upgrade. Kromě toho, aby systém informoval zákazníky s touto novou verzí, systém zobrazuje explicitní upozornění na stav clusteru, které je podobné následujícímu:

"Aktuální verze clusteru [verze #] končí na konci [datum]."

Když je v clusteru spuštěná nejnovější verze, upozornění zmizí.

Po zobrazení upozornění na stav clusteru upgradujte cluster:

1. Připojte se ke clusteru z libovolného počítače, který má oprávnění správce ke všem počítačům uvedeným jako uzly v clusteru. Počítač, na kterém je tento skript spuštěný, nemusí být součástí clusteru.

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

2. Získejte seznam verzí Service Fabric, na které můžete upgradovat.

    ```powershell
    ###### Get the list of available Service Fabric versions
    Get-ServiceFabricRegisteredClusterCodeVersion
    ```

    Měli byste získat výstup podobný tomuto:

    ![Získat Service Fabric verze][getfabversions]
3. Spusťte upgrade clusteru na dostupnou verzi pomocí příkazu [Start-ServiceFabricClusterUpgrade](/powershell/module/servicefabric/start-servicefabricclusterupgrade) prostředí Windows PowerShell.

    ```powershell
    Start-ServiceFabricClusterUpgrade -Code -CodePackageVersion <codeversion#> -Monitored -FailureAction Rollback

    ###### Here is a filled-out example

    Start-ServiceFabricClusterUpgrade -Code -CodePackageVersion 5.3.301.9590 -Monitored -FailureAction Rollback
    ```
   Chcete-li monitorovat průběh upgradu, můžete použít Service Fabric Explorer nebo spustit následující příkaz prostředí PowerShell:

    ```powershell
    Get-ServiceFabricClusterUpgrade
    ```

    Pokud zásady stavu clusteru nejsou splněné, upgrade se vrátí zpět. Pokud chcete zadat vlastní zásady stavu pro příkaz Start-ServiceFabricClusterUpgrade, přečtěte si dokumentaci ke službě [Start-ServiceFabricClusterUpgrade](/powershell/module/servicefabric/start-servicefabricclusterupgrade).

    Po vyřešení problémů, které vedly k vrácení zpět, znovu spusťte upgrade pomocí stejných kroků, jak je popsáno výše.

## <a name="upgrade-clusters-that-have-no-connectivity-to-download-the-latest-code-and-configuration"></a>Upgradujte clustery, které nemají *žádné připojení* ke stažení nejnovějšího kódu a konfigurace.
Pomocí těchto kroků můžete cluster upgradovat na podporovanou verzi, pokud uzly clusteru nemají připojení k Internetu na webu [služby Stažení softwaru](https://download.microsoft.com).

> [!NOTE]
> Pokud používáte cluster, který není připojený k Internetu, musíte sledovat [Blog týmu Service Fabric](https://techcommunity.microsoft.com/t5/azure-service-fabric/bg-p/Service-Fabric) , abyste se dozvěděli o nových verzích. Systém nezobrazuje upozornění na stav clusteru, aby vás upozornil na nové verze.  
>
>

### <a name="auto-provisioning-vs-manual-provisioning"></a>Automatické zřizování vs. ruční zřizování
Pokud chcete povolit automatické stahování a registraci pro nejnovější verzi kódu, nastavte službu Service Fabric Update. Pokyny najdete v tématu *Tools\ServiceFabricUpdateService.zip\Readme_InstructionsAndHowTos.txt* v [samostatném balíčku](service-fabric-cluster-standalone-package-contents.md).

Při ručním zpracování postupujte podle těchto pokynů.

Než začnete s upgradem konfigurace, upravte konfiguraci clusteru tak, aby pro následující vlastnost byla nastavena *hodnota false* :

```json
"fabricClusterAutoupgradeEnabled": false,
```

Podrobnosti o využití najdete v příkazu PowerShellu [Start-ServiceFabricClusterConfigurationUpgrade](/powershell/module/servicefabric/start-servicefabricclusterconfigurationupgrade) . Před zahájením upgradu konfigurace nezapomeňte ve svém formátu JSON aktualizovat ' clusterConfigurationVersion '.

```powershell
    Start-ServiceFabricClusterConfigurationUpgrade -ClusterConfigPath <Path to Configuration File>
```

### <a name="cluster-upgrade-workflow"></a>Pracovní postup upgradu clusteru

1. Spusťte rutinu [Get-ServiceFabricClusterUpgrade](/powershell/module/servicefabric/get-servicefabricclusterupgrade) z jednoho z uzlů v clusteru a poznamenejte si *TargetCodeVersion*.

2. Spusťte následující příkaz z počítače připojeného k Internetu a seznam všech verzí kompatibilních s upgradem s aktuální verzí a Stáhněte si odpovídající balíček z přidružených odkazů ke stažení:

    ```powershell
    ###### Get list of all upgrade compatible packages  
    Get-ServiceFabricRuntimeUpgradeVersion -BaseVersion <TargetCodeVersion as noted in Step 1> 
    ```

3. Připojte se ke clusteru z libovolného počítače, který má oprávnění správce ke všem počítačům uvedeným jako uzly v clusteru. Počítač, na kterém je tento skript spuštěný, nemusí být součástí clusteru.

    ```powershell
    ###### Get the list of available Service Fabric versions
    Copy-ServiceFabricClusterPackage -Code -CodePackagePath <name of the .cab file including the path to it> -ImageStoreConnectionString "fabric:ImageStore"

    ###### Here is a filled-out example
    Copy-ServiceFabricClusterPackage -Code -CodePackagePath .\MicrosoftAzureServiceFabric.5.3.301.9590.cab -ImageStoreConnectionString "fabric:ImageStore"
    ```
4. Zkopírujte stažený balíček do úložiště imagí clusteru.

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
    Průběh upgradu můžete monitorovat na Service Fabric Explorer, nebo můžete spustit následující příkaz prostředí PowerShell:

    ```powershell
    Get-ServiceFabricClusterUpgrade
    ```

    Pokud zásady stavu clusteru nejsou splněné, upgrade se vrátí zpět. Pokud chcete zadat vlastní zásady stavu pro příkaz Start-ServiceFabricClusterUpgrade, přečtěte si dokumentaci ke službě [Start-ServiceFabricClusterUpgrade](/powershell/module/servicefabric/start-servicefabricclusterupgrade).

    Po vyřešení problémů, které vedly k vrácení zpět, znovu spusťte upgrade pomocí stejných kroků, jak je popsáno výše.

## <a name="next-steps"></a>Další kroky
* [Upgrade konfigurace samostatného clusteru](service-fabric-cluster-config-upgrade-windows-server.md)
* Přizpůsobení některých [Service Fabric nastavení clusteru](service-fabric-cluster-fabric-settings.md).
* Horizontální navýšení [kapacity clusteru a jejich škálování](service-fabric-cluster-scale-in-out.md).

<!--Image references-->
[getfabversions]: ./media/service-fabric-cluster-upgrade-windows-server/getfabversions.PNG
