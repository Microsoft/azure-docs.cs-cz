---
title: Upgradovat samostatné clusteru Azure Service Fabric na Windows serveru | Microsoft Docs
description: Upgrade Azure Service Fabric kód nebo konfigurace, který spouští samostatný cluster Service Fabric, včetně nastavení režimu aktualizace clusteru.
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
ms.openlocfilehash: 20526c1ddd55671f815dc39b3e03c4f9b2f91788
ms.sourcegitcommit: eb75f177fc59d90b1b667afcfe64ac51936e2638
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/16/2018
ms.locfileid: "34208647"
---
# <a name="upgrade-your-standalone-azure-service-fabric-cluster-on-windows-server"></a>Upgradovat samostatné Azure Service Fabric clusteru v systému Windows Server 
> [!div class="op_single_selector"]
> * [Azure clusteru](service-fabric-cluster-upgrade.md)
> * [Samostatný cluster](service-fabric-cluster-upgrade-windows-server.md)
>
>

Možnosti upgradu pro libovolný systém moderní je klíčová pro dlouhodobý úspěch vašeho produktu. Cluster služby Azure Service Fabric je prostředek, který vlastníte. Tento článek popisuje, jak můžete zkontrolovat, že cluster vždy běží podporované verze kódu Service Fabric a konfigurace.

## <a name="control-the-service-fabric-version-that-runs-on-your-cluster"></a>Ovládací prvek verze Service Fabric, která běží na clusteru
Stahovat aktualizace Service Fabric, když společnost Microsoft vydá nové verze s vaším clusterem, nastavit konfiguraci clusteru fabricClusterAutoupgradeEnabled *true*. Pokud chcete vybrat podporovanou verzi Service Fabric, které chcete v clusteru, nastavte konfiguraci clusteru fabricClusterAutoupgradeEnabled na *false*.

> [!NOTE]
> Ujistěte se, že váš cluster vždy s podporovanou verzí Service Fabric. Když Microsoft oznamuje vydání nové verze Service Fabric, předchozí verze je označena k ukončení podpory po minimálně 60 dní od data oznámení. Nové verze není zmínka [na blogu týmu Service Fabric](https://blogs.msdn.microsoft.com/azureservicefabric/). Nové verze je možné zvolit v daném okamžiku.
>
>

Clusteru můžete upgradovat na novou verzi, pouze pokud používáte produkční style konfigurace uzlu, kde je každý uzel Service Fabric přidělena na samostatný fyzický nebo virtuální počítač. Pokud máte cluster vývoj, které je více než jeden uzel Service Fabric na jeden fyzický nebo virtuální počítač, budete muset znovu vytvořit cluster s novou verzí.

Dvou různých pracovních postupů můžete upgradovat clusteru na nejnovější verzi nebo podporovanou verzi Service Fabric. Jeden pracovní postup je pro clustery, které mají připojení k automaticky stáhnout nejnovější verzi. Jiné pracovní postup je pro clustery, které nemají připojení k stáhnout nejnovější verzi Service Fabric.

### <a name="upgrade-clusters-that-have-connectivity-to-download-the-latest-code-and-configuration"></a>Upgrade clustery, které mají připojení k stáhnout nejnovější kódu a konfigurace
Tyto kroky použijte k upgradu clusteru na podporovanou verzi, pokud máte připojení k Internetu pro uzly clusteru [Microsoft Download Center](http://download.microsoft.com).

Pro clustery, které mají připojení k [Microsoft Download Center](http://download.microsoft.com), Microsoft pravidelně kontroluje dostupnost nové verze Service Fabric.

Pokud je dostupná nová verze Service Fabric, balíček je stažen místně do clusteru a zřídit pro upgrade. Navíc k informování zákazník tuto novou verzi, systém zobrazí upozornění stavu explicitní clusteru, který je podobný následujícímu:

"Aktuální verze clusteru [verze #] [datum] ukončení podpory."

Po v clusteru je spuštěn na nejnovější verzi, upozornění Vyčkat.

#### <a name="cluster-upgrade-workflow"></a>Pracovní postup upgradu clusteru
Když se zobrazí upozornění na stav clusteru, postupujte takto:

1. Připojte se ke clusteru z libovolného počítače, který má práva správce pro všechny počítače, které jsou označeny jako uzly v clusteru. Na počítač, který tento skript je spuštěn na nemusí být součástí clusteru.

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

2. Získáte seznam verzí Service Fabric, které můžete upgradovat.

    ```powershell

    ###### Get the list of available Service Fabric versions
    Get-ServiceFabricRegisteredClusterCodeVersion
    ```

    Měli byste obdržet výstup podobná této:

    ![Získání verze Service Fabric][getfabversions]
3. Spusťte upgrade clusteru k dispozici verzi pomocí [Start-ServiceFabricClusterUpgrade](https://msdn.microsoft.com/library/mt125872.aspx) příkazu prostředí Windows PowerShell.

    ```Powershell

    Start-ServiceFabricClusterUpgrade -Code -CodePackageVersion <codeversion#> -Monitored -FailureAction Rollback

    ###### Here is a filled-out example

    Start-ServiceFabricClusterUpgrade -Code -CodePackageVersion 5.3.301.9590 -Monitored -FailureAction Rollback

    ```
   Pokud chcete sledovat průběh upgradu, můžete použít Service Fabric Explorer nebo spusťte následující příkaz prostředí PowerShell:

    ```powershell

    Get-ServiceFabricClusterUpgrade
    ```

    Pokud zásady stavu clusteru nejsou splněny, upgrade je vrácena zpět. Pokud chcete nastavit zásady vlastní stavu pro příkaz Start-ServiceFabricClusterUpgrade, naleznete v dokumentaci k [Start-ServiceFabricClusterUpgrade](https://msdn.microsoft.com/library/mt125872.aspx).

Po vyřešení problémů, jejichž výsledkem vrácení zpět zahájit upgrade znovu podle následujících kroků stejná jako bylo popsáno dříve.

### <a name="upgrade-clusters-that-have-no-connectivity-to-download-the-latest-code-and-configuration"></a>Upgrade clustery, které mají *žádná připojení* stáhnout nejnovější kódu a konfigurace
Tyto kroky použijte pro upgrade clusteru na podporovanou verzi, pokud uzly clusteru nemají připojení k Internetu k [Microsoft Download Center](http://download.microsoft.com).

> [!NOTE]
> Pokud používáte cluster, který není připojený k Internetu, máte k monitorování blog týmu Service Fabric Další informace o nové verze. Systém nezobrazí upozornění stavu clusteru vás upozorní na nové verze.  
>
>

#### <a name="auto-provisioning-vs-manual-provisioning"></a>Automatické zřizování oproti ručního zřizování
Povolit automatické stahování a registrace na nejnovější verzi kódu, nastavte aktualizace služby prostředků infrastruktury. Pokyny najdete v tématu Tools\ServiceFabricUpdateService.zip\Readme_InstructionsAndHowTos.txt uvnitř [samostatný balíček](service-fabric-cluster-standalone-package-contents.md).
Proces ručního nastavení postupujte podle těchto pokynů.

Upravte konfiguraci clusteru a nastavte následující vlastnost na *false* před zahájením upgradu konfigurace:

        "fabricClusterAutoupgradeEnabled": false,

Podrobnosti využití najdete v tématu [příkaz Start-ServiceFabricClusterConfigurationUpgrade Powershellu](https://msdn.microsoft.com/library/mt788302.aspx). Ujistěte se, že jste před zahájením upgradu configuration aktualizujte 'clusterConfigurationVersion' ve vašem formátu JSON.

```powershell

    Start-ServiceFabricClusterConfigurationUpgrade -ClusterConfigPath <Path to Configuration File>

```

#### <a name="cluster-upgrade-workflow"></a>Pracovní postup upgradu clusteru

1. Get-ServiceFabricClusterUpgrade spustit na jednom z uzlů v clusteru a poznamenejte si TargetCodeVersion.

2. Spusťte následující příkazy z počítače připojeného k Internetu k seznamu všech verzí upgrade kompatibilní s aktuální verzí a stáhněte si odpovídající balíček z odkazů přidružené ke stažení:

    ```powershell

    ###### Get list of all upgrade compatible packages  
    Get-ServiceFabricRuntimeUpgradeVersion -BaseVersion <TargetCodeVersion as noted in Step 1> 
    ```

3. Připojte se ke clusteru z libovolného počítače, který má práva správce pro všechny počítače, které jsou označeny jako uzly v clusteru. Na počítač, který tento skript je spuštěn na nemusí být součástí clusteru.

    ```powershell

   ###### Get the list of available Service Fabric versions
    Copy-ServiceFabricClusterPackage -Code -CodePackagePath <name of the .cab file including the path to it> -ImageStoreConnectionString "fabric:ImageStore"

   ###### Here is a filled-out example
    Copy-ServiceFabricClusterPackage -Code -CodePackagePath .\MicrosoftAzureServiceFabric.5.3.301.9590.cab -ImageStoreConnectionString "fabric:ImageStore"

    ```
4. Zkopírujte stažený balíček do úložiště bitových kopií clusteru.

5. Zaregistrujte zkopírovaný balíčku.

    ```powershell

    ###### Get the list of available Service Fabric versions
    Register-ServiceFabricClusterPackage -Code -CodePackagePath <name of the .cab file>

    ###### Here is a filled-out example
    Register-ServiceFabricClusterPackage -Code -CodePackagePath MicrosoftAzureServiceFabric.5.3.301.9590.cab

     ```
6. Spusťte upgrade clusteru dostupnou verzi.

    ```Powershell

    Start-ServiceFabricClusterUpgrade -Code -CodePackageVersion <codeversion#> -Monitored -FailureAction Rollback

    ###### Here is a filled-out example
    Start-ServiceFabricClusterUpgrade -Code -CodePackageVersion 5.3.301.9590 -Monitored -FailureAction Rollback

    ```
   Můžete sledovat průběh upgradu v Service Fabric Explorer nebo spuštěním následujícího příkazu Powershellu:

    ```powershell

    Get-ServiceFabricClusterUpgrade
    ```

    Pokud zásady stavu clusteru nejsou splněny, upgrade je vrácena zpět. Pokud chcete nastavit zásady vlastní stavu pro příkaz Start-ServiceFabricClusterUpgrade, najdete v dokumentaci k [Start-ServiceFabricClusterUpgrade](https://msdn.microsoft.com/library/mt125872.aspx).

Po vyřešení problémů, jejichž výsledkem vrácení zpět zahájit upgrade znovu podle následujících kroků stejná jako bylo popsáno dříve.


## <a name="upgrade-the-cluster-configuration"></a>Upgradovat konfiguraci clusteru
Než zahájíte upgrade konfigurace, můžete otestovat novou konfiguraci clusteru JSON spuštěním následujícího skriptu prostředí PowerShell v samostatné balíčku:

```powershell

    TestConfiguration.ps1 -ClusterConfigFilePath <Path to the new Configuration File> -OldClusterConfigFilePath <Path to the old Configuration File>

```
Nebo použijte tento skript:

```powershell

    TestConfiguration.ps1 -ClusterConfigFilePath <Path to the new Configuration File> -OldClusterConfigFilePath <Path to the old Configuration File> -FabricRuntimePackagePath <Path to the .cab file which you want to test the configuration against>

```

Některé konfigurace nelze upgradovat, jako je například koncových bodů, název clusteru, uzel IP adresy, atd. Novou konfiguraci clusteru JSON je testován vůči starý a vyvolá chyby v okně prostředí PowerShell, pokud se vyskytl problém.

Upgradovat konfiguraci upgrade clusteru, spusťte ServiceFabricClusterConfigurationUpgrade spuštění. Konfigurace upgradu je zpracování doména upgradu podle upgradovací doméně.

```powershell

    Start-ServiceFabricClusterConfigurationUpgrade -ClusterConfigPath <Path to Configuration File>

```

### <a name="cluster-certificate-config-upgrade"></a>Aktualizace konfigurace certifikátu clusteru  
Certifikát clusteru se používá pro ověřování mezi uzly clusteru. Změna certifikátu by měl provést navíc opatrně, protože selhání blokuje komunikaci mezi uzly clusteru.

Technicky jsou podporovány čtyř možností:  

* Jeden certifikát upgrade: způsob upgradu je certifikát (primární) -> certifikát B (primární) -> C certifikátu (primární) ->...

* Double certifikátu upgrade: způsob upgradu je certifikát (primární) -> certifikátu (primární) a B (sekundární) -> certifikát B (primární) -> certifikát B (primární) a C (sekundární) -> C certifikátu (primární) ->...

* Certifikát typ upgradu: Konfigurace certifikátu na základě CommonName configuration <> – na základě kryptografický otisk certifikátu. Například kryptografický otisk certifikátu (primární) a kryptografický otisk B (sekundární) -> certifikát CommonName C.

* Upgrade kryptografický otisk vystavitelů certifikátů: CN certifikátu je cesta k upgradu = A, IssuerThumbprint = IT1 (primární) -> certifikátů CN = A, IssuerThumbprint = IT1 IT2 (primární) -> CN certifikátu = A, IssuerThumbprint = IT2 (primární).


## <a name="next-steps"></a>Další postup
* Zjistěte, jak přizpůsobit některé [nastavení clusteru Service Fabric](service-fabric-cluster-fabric-settings.md).
* Zjistěte, jak [škálování vašeho clusteru a odhlašování](service-fabric-cluster-scale-up-down.md).
* Další informace o [upgradů aplikací](service-fabric-application-upgrade.md).

<!--Image references-->
[getfabversions]: ./media/service-fabric-cluster-upgrade-windows-server/getfabversions.PNG
