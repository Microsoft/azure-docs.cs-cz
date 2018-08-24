---
title: Vytvoření samostatného clusteru Azure Service Fabric | Dokumentace Microsoftu
description: Vytvoření clusteru Azure Service Fabric na jakýkoli počítač (fyzický nebo virtuální) s Windows serverem, ať už jde o místní nebo v libovolném cloudu.
services: service-fabric
documentationcenter: .net
author: dkkapur
manager: timlt
editor: ''
ms.assetid: 31349169-de19-4be6-8742-ca20ac41eb9e
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 8/10/2017
ms.author: dekapur
ms.openlocfilehash: 3ce47d631e8a2ec7daf96ef95200001e5d4f8327
ms.sourcegitcommit: 58c5cd866ade5aac4354ea1fe8705cee2b50ba9f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/24/2018
ms.locfileid: "42818577"
---
# <a name="create-a-standalone-cluster-running-on-windows-server"></a>Vytvoření samostatného clusteru běžící na Windows serveru
Azure Service Fabric můžete použít k vytvoření clusterů Service Fabric na všechny virtuální počítače nebo počítače se systémem Windows Server. To znamená, že můžete nasadit a spouštět aplikace Service Fabric v jakémkoli prostředí, které obsahuje sadu propojených počítačů Windows serveru, už to jsou v místním prostředí nebo jakýkoli jiný poskytovatel cloudu. Service Fabric nabízí instalační balíček pro vytváření clusterů Service Fabric volá samostatného balíčku Windows serveru.

Tento článek vás provede kroky pro vytvoření samostatného clusteru Service Fabric.

> [!NOTE]
> Tento samostatný balíček Windows serveru je komerčně dostupný a mohou být použity pro nasazení v produkčním prostředí. Tento balíček může obsahovat nové funkce Service Fabric, které jsou v "Verze Preview". Přejděte dolů k položce "[součástí tohoto balíčku funkce ve verzi Preview](#previewfeatures_anchor)." oddíl seznamu funkce ve verzi preview. Je možné [stáhnout kopii této smlouvy EULA](http://go.microsoft.com/fwlink/?LinkID=733084) nyní.
> 
> 

<a id="getsupport"></a>

## <a name="get-support-for-the-service-fabric-for-windows-server-package"></a>Získat podporu pro balíček aplikace Service Fabric pro Windows Server
* Zeptejte se komunity o samostatného balíčku Service Fabric pro Windows Server v [fórum pro Azure Service Fabric](https://social.msdn.microsoft.com/Forums/azure/en-US/home?forum=AzureServiceFabric?).
* Vytvořit lístek pro [profesionální podpory pro Service Fabric](http://support.microsoft.com/oas/default.aspx?prid=16146).  Další informace o profesionální podpory od Microsoftu [tady](https://support.microsoft.com/en-us/gp/offerprophone?wa=wsignin1.0).
* Můžete také získat podporu pro tento balíček v rámci [programem Microsoft Premier Support](https://support.microsoft.com/en-us/premier).
* Další podrobnosti najdete v tématu [možnosti podpory Azure Service Fabric](https://docs.microsoft.com/azure/service-fabric/service-fabric-support).
* Chcete-li shromažďovat protokoly pro účely podpory, spusťte [kolektoru protokolů samostatné Service Fabric](service-fabric-cluster-standalone-package-contents.md).

<a id="downloadpackage"></a>

## <a name="download-the-service-fabric-for-windows-server-package"></a>Stažení balíčku Service Fabric pro Windows Server
K vytvoření clusteru použijte balíček aplikace Service Fabric pro Windows Server (Windows Server 2012 R2 a novější) najdete tady: <br>
[Stáhněte si odkaz - samostatného balíčku Service Fabric – Windows Server](http://go.microsoft.com/fwlink/?LinkId=730690)

Podrobnosti najít v obsahu balíčku [tady](service-fabric-cluster-standalone-package-contents.md).

Balíček modulu runtime Service Fabric se automaticky stáhnou v době vytváření clusteru. Pokud nasazení z jiného počítače, které nejsou připojené k Internetu, stáhněte si prosím balíček modulu runtime mimo pásmo z tohoto: <br>
[Stáhněte si odkaz – modul Runtime Service Fabric – Windows Server](https://go.microsoft.com/fwlink/?linkid=839354)

V těchto ukázkách samostatné konfigurace clusteru: <br>
[Ukázky konfigurace samostatného clusteru](https://github.com/Azure-Samples/service-fabric-dotnet-standalone-cluster-configuration/tree/master/Samples)

<a id="createcluster"></a>

## <a name="create-the-cluster"></a>Vytvoření clusteru
Několik ukázkových konfiguračních souborů clusteru se nainstaluje spolu s instalačním balíčkem. Soubor *ClusterConfig.Unsecure.DevCluster.json* představuje nejjednodušší konfiguraci clusteru: nezabezpečený cluster se třemi uzly spuštěnými v jednom počítači.  Další konfigurační soubory popisují clustery s jedním nebo více počítači zabezpečené pomocí certifikátů X.509 nebo s použitím zabezpečení systému Windows.  Nemusíte upravovat žádná výchozí nastavení konfigurace pro tento kurz, ale projděte si konfigurační soubor a seznamte se s nastavením.  Část **nodes** popisuje tři uzly v clusteru: název, IP adresa, [typ uzlu, doména selhání a upgradovací doména](service-fabric-cluster-manifest.md#nodes-on-the-cluster).  Část **properties** definuje [zabezpečení, úroveň spolehlivosti, shromažďování diagnostických dat a typy uzlů](service-fabric-cluster-manifest.md#cluster-properties) pro cluster.

V rámci tohoto článku vytvořili cluster není zabezpečený.  Každý se může anonymně připojit a provádět operace správy, proto by produkční clustery vždy měly být zabezpečené pomocí certifikátů X.509 nebo zabezpečení systému Windows.  Zabezpečení se konfiguruje jenom při vytváření clusteru a není možné povolit zabezpečení po vytvoření clusteru. Aktualizace konfiguračního souboru povolit [certifikátu zabezpečení](service-fabric-windows-cluster-x509-security.md) nebo [zabezpečení Windows](service-fabric-windows-cluster-windows-security.md). V článku [Zabezpečení clusteru](service-fabric-cluster-security.md) najdete další informace o zabezpečení clusteru Service Fabric.

### <a name="step-1a-create-an-unsecured-local-development-cluster"></a>Krok 1A: vytvořit nezabezpečený místního vývojového clusteru
Service Fabric je nasadit na jeden počítač vývojový cluster pomocí *ClusterConfig.Unsecure.DevCluster.json* souboru zahrnutém v [ukázky](https://github.com/Azure-Samples/service-fabric-dotnet-standalone-cluster-configuration/tree/master/Samples).

Rozbalit samostatného balíčku do počítače, zkopírovat ukázkový konfigurační soubor do místního počítače a potom spusťte *CreateServiceFabricCluster.ps1* skriptu prostřednictvím relaci prostředí PowerShell správce ze složky samostatného balíčku .

```powershell
.\CreateServiceFabricCluster.ps1 -ClusterConfigFilePath .\ClusterConfig.Unsecure.DevCluster.json -AcceptEULA
```

V části nastavení prostředí v [plánování a příprava vašeho nasazení clusteru](service-fabric-cluster-standalone-deployment-preparation.md) pro podrobnosti o řešení problémů.

Pokud dokončíte spuštěné vývojové scénáře, můžete odebrat cluster Service Fabric z počítače pomocí kroků v části "[odebrat cluster](#removecluster_anchor)". 

### <a name="step-1b-create-a-multi-machine-cluster"></a>Krok 1B: vytvoření clusteru s více počítači
Poté, co jste prošli plánování a podrobné kroky přípravy na [plánování a příprava vašeho nasazení clusteru](service-fabric-cluster-standalone-deployment-preparation.md), jste připraveni vytvořit cluster produkčním prostředí pomocí souboru konfigurace clusteru.

Správce clusteru, který cluster nasazuje a konfiguruje, musí mít v příslušném počítači oprávnění správce. Service Fabric nelze nainstalovat na řadič domény.

1. Skript *TestConfiguration.ps1* v samostatném balíčku se používá jako analyzátor s osvědčenými postupy pro ověření, jestli je možné cluster nasadit do daného prostředí. V článku [Příprava nasazení](service-fabric-cluster-standalone-deployment-preparation.md) jsou vypsány předpoklady a požadavky prostředí. Spusťte skript a ověřte, jestli můžete vývojový cluster vytvořit:  

    ```powershell
    .\TestConfiguration.ps1 -ClusterConfigFilePath .\ClusterConfig.json
    ```

    Byste měli vidět výstup podobný následujícímu. Pokud pole dolní "Předán" se vrátí jako "PRAVDA", vhodnosti kontroly proběhly a cluster vypadá jako nasaditelný podle konfigurace vstupu.

    ```powershell
    Trace folder already exists. Traces will be written to existing trace folder: C:\temp\Microsoft.Azure.ServiceFabric.WindowsServer\DeploymentTraces
    Running Best Practices Analyzer...
    Best Practices Analyzer completed successfully.
    
    LocalAdminPrivilege        : True
    IsJsonValid                : True
    IsCabValid                 : True
    RequiredPortsOpen          : True
    RemoteRegistryAvailable    : True
    FirewallAvailable          : True
    RpcCheckPassed             : True
    NoConflictingInstallations : True
    FabricInstallable          : True
    Passed                     : True
    ```

2. Vytvoření clusteru: Spusťte *CreateServiceFabricCluster.ps1* skript k nasazení clusteru Service Fabric v každém počítači v konfiguraci. 
    ```powershell
    .\CreateServiceFabricCluster.ps1 -ClusterConfigFilePath .\ClusterConfig.json -AcceptEULA
    ```

> [!NOTE]
> Trasování nasazení se zapisují do virtuálního počítače nebo počítače, ve kterém jste spustili skript prostředí PowerShell CreateServiceFabricCluster.ps1. Najít je můžete v podsložce DeploymentTraces v rámci adresáře, ze kterého se skript spustil. Pokud chcete zjistit, jestli nasazení Service Fabric do počítače proběhlo úspěšně, vyhledejte nainstalované soubory v adresáři FabricDataRoot podle popisu v části FabricSettings konfiguračního souboru clusteru (ve výchozím nastavení c:\ProgramData\SF). Kromě toho jsou ve Správci úloh vidět spuštěné procesy FabricHost.exe a Fabric.exe.
> 
> 

### <a name="step-1c-create-an-offline-internet-disconnected-cluster"></a>Krok 1C: vytvořit cluster v režimu offline (internet odpojen)
Při vytváření clusteru se automaticky stáhne balíček modulu runtime Service Fabric. Při nasazování clusteru na počítače bez připojení k Internetu, je potřeba stáhnout balíček modulu runtime Service Fabric samostatně a zadejte cestu k němu při vytváření clusteru.
Balíček modulu runtime se dají stáhnout samostatně, z jiného počítače připojené k Internetu, na [stáhnout odkaz – modul Runtime Service Fabric – Windows Server](https://go.microsoft.com/fwlink/?linkid=839354). Pokud nasazujete offline clusteru ze a vytvořte cluster spuštěním zkopírujte balíček modulu runtime `CreateServiceFabricCluster.ps1` s `-FabricRuntimePackagePath` parametr zahrnuty, jak je znázorněno v tomto příkladu: 

```powershell
.\CreateServiceFabricCluster.ps1 -ClusterConfigFilePath .\ClusterConfig.json -FabricRuntimePackagePath .\MicrosoftAzureServiceFabric.cab
```

*.\ClusterConfig.JSON* a *.\MicrosoftAzureServiceFabric.cab* jsou cesty ke konfiguraci clusteru a modul runtime soubor CAB v uvedeném pořadí.

### <a name="step-2-connect-to-the-cluster"></a>Krok 2: Připojení ke clusteru
Připojte se ke clusteru ověřte, zda že je cluster běží a je k dispozici. Modul PowerShell ServiceFabric se instaluje spolu s modulem runtime.  Na jednom z uzlů clusteru nebo ze vzdáleného počítače pomocí modulu runtime Service Fabric můžete připojit ke clusteru.  Rutina [Connect-ServiceFabricCluster](/powershell/module/servicefabric/connect-servicefabriccluster?view=azureservicefabricps) vytvoří připojení ke clusteru.

Pro připojení k nezabezpečenému clusteru, spusťte následující příkaz Powershellu:

```powershell
Connect-ServiceFabricCluster -ConnectionEndpoint <*IPAddressofaMachine*>:<Client connection end point port>
```

Příklad:
```powershell
Connect-ServiceFabricCluster -ConnectionEndpoint 192.13.123.2345:19000
```

Další příklady připojení ke clusteru najdete v článku [Připojení k zabezpečenému clusteru](service-fabric-connect-to-secure-cluster.md). Po připojení ke clusteru zobrazte pomocí rutiny [Get-ServiceFabricNode](/powershell/module/servicefabric/get-servicefabricnode?view=azureservicefabricps) seznam uzlů v clusteru a stavové informace pro každý uzel. Položka **HealthState** by měla mít pro každý uzel hodnotu *OK*.

```powershell
PS C:\temp\Microsoft.Azure.ServiceFabric.WindowsServer> Get-ServiceFabricNode |Format-Table

NodeDeactivationInfo NodeName IpAddressOrFQDN NodeType  CodeVersion  ConfigVersion NodeStatus NodeUpTime NodeDownTime HealthState
-------------------- -------- --------------- --------  -----------  ------------- ---------- ---------- ------------ -----------
                     vm2      localhost       NodeType2 5.6.220.9494 0                     Up 00:03:38   00:00:00              OK
                     vm1      localhost       NodeType1 5.6.220.9494 0                     Up 00:03:38   00:00:00              OK
                     vm0      localhost       NodeType0 5.6.220.9494 0                     Up 00:02:43   00:00:00              OK
```

### <a name="step-3-visualize-the-cluster-using-service-fabric-explorer"></a>Krok 3: Vizualizace clusteru pomocí Service Fabric explorer
[Service Fabric Explorer](service-fabric-visualizing-your-cluster.md) je nástroj vhodný pro vizualizaci clusteru a správu aplikací.  Service Fabric Explorer je služba, která se spustí v clusteru, které máte přístup prostřednictvím prohlížeče, že přejdete na [ http://localhost:19080/Explorer ](http://localhost:19080/Explorer).

Řídicí panel clusteru poskytuje přehled o clusteru včetně souhrnu stavu aplikací a uzlů. Zobrazení uzlu obsahuje fyzické rozložení clusteru. Pro daný uzel můžete zjistit, které aplikace mají v uzlu nasazený kód.

![Service Fabric Explorer][service-fabric-explorer]

## <a name="add-and-remove-nodes"></a>Přidávání a odebírání uzlů
Podle toho, jak se vaše obchodní potřeby mění, můžete uzly do samostatného clusteru Service Fabric přidávat nebo je z něj odebírat. Podrobné kroky najdete v tématu [Přidávání uzlů do samostatného clusteru Service Fabric nebo jejich odebírání](service-fabric-cluster-windows-server-add-remove-nodes.md).

<a id="removecluster" name="removecluster_anchor"></a>
## <a name="remove-a-cluster"></a>Odebrání clusteru
Chcete-li cluster odebrat, spusťte skript *RemoveServiceFabricCluster.ps1* prostředí PowerShell ze složky balíčku a předejte mu cestu ke konfiguračnímu souboru JSON. Volitelně můžete určit umístění pro protokol odstranění.

Tento skript můžete spustit na jakýkoli počítač, který má přístup správce pro všechny počítače, které jsou označeny jako uzly v clusteru konfigurační soubor. Počítače, ve kterém se skript spouští na nemusí být součástí clusteru.

```powershell
# Removes Service Fabric from each machine in the configuration
.\RemoveServiceFabricCluster.ps1 -ClusterConfigFilePath .\ClusterConfig.json -Force
```

```powershell
# Removes Service Fabric from the current machine
.\CleanFabric.ps1
```

<a id="telemetry"></a>

## <a name="telemetry-data-collected-and-how-to-opt-out-of-it"></a>Mezi shromažďovaná telemetrická data a jak se odhlásit z ní
Jako výchozí produkt shromažďuje telemetrii využití Service Fabric k vylepšení produktu. Analyzátor osvědčených postupů, která se spouští jako součást instalace zkontroluje připojení ke [ https://vortex.data.microsoft.com/collect/v1 ](https://vortex.data.microsoft.com/collect/v1). Pokud není dostupný, instalace se nezdaří, pokud zakážete telemetrická data.

1. Telemetrická data profilace se pokusí odeslat následující data, která mají [ https://vortex.data.microsoft.com/collect/v1 ](https://vortex.data.microsoft.com/collect/v1) jednou denně. Je nahrání best effort a nemá žádný vliv na funkčnost clusteru. Telemetrická data se odesílají z uzlu, který běží převzetí služeb při selhání primární správce. Žádné uzly odesílat telemetrická data.
2. Telemetrická data se skládá z následujících akcí:

* Počet služeb
* Počet ServiceType
* Počet aplikací
* Počet ApplicationUpgrades
* Počet jednotek
* Počet InBuildFailoverUnits
* Počet UnhealthyFailoverUnits
* Počet replik
* Počet InBuildReplicas
* Počet StandByReplicas
* Počet OfflineReplicas
* CommonQueueLength
* QueryQueueLength
* FailoverUnitQueueLength
* CommitQueueLength
* Počet uzlů
* IsContextComplete: True nebo False
* ClusterId: Toto je identifikátor GUID pro každý cluster náhodně generované
* ServiceFabricVersion
* IP adresu virtuálního počítače nebo počítače, ze kterého se nahraje telemetrická data

Chcete-li zakázat telemetrii, přidejte následující text do *vlastnosti* v konfiguraci clusteru: *enableTelemetry: false*.

<a id="previewfeatures" name="previewfeatures_anchor"></a>

## <a name="preview-features-included-in-this-package"></a>Funkce ve verzi Preview, které jsou součástí tohoto balíčku
Žádné.


> [!NOTE]
> Spouští se s novými [verzi samostatného clusteru pro systém Windows Server (verze 5.3.204.x)](https://azure.microsoft.com/blog/azure-service-fabric-for-windows-server-now-ga/), svůj cluster můžete upgradovat na budoucí verze, ručně nebo automaticky. Odkazovat na [upgradovat samostatnou verzi clusteru Service Fabric](service-fabric-cluster-upgrade-windows-server.md) dokumentu podrobnosti.
> 
> 

## <a name="next-steps"></a>Další postup
* [Nasazení a odebírat aplikace pomocí Powershellu](service-fabric-deploy-remove-applications.md)
* [Nastavení konfigurace pro samostatný cluster Windows](service-fabric-cluster-manifest.md)
* [Přidávání a odebírání uzlů do samostatného clusteru Service Fabric](service-fabric-cluster-windows-server-add-remove-nodes.md)
* [Upgradovat samostatnou verzi clusteru Service Fabric](service-fabric-cluster-upgrade-windows-server.md)
* [Vytvoření samostatného clusteru Service Fabric pomocí virtuálních počítačů Azure s Windows](service-fabric-cluster-creation-with-windows-azure-vms.md)
* [Zabezpečení samostatného clusteru ve Windows pomocí zabezpečení Windows](service-fabric-windows-cluster-windows-security.md)
* [Zabezpečení samostatného clusteru ve Windows pomocí X509 certifikáty](service-fabric-windows-cluster-x509-security.md)

<!--Image references-->
[Trusted Zone]: ./media/service-fabric-cluster-creation-for-windows-server/TrustedZone.png
[service-fabric-explorer]: ./media/service-fabric-cluster-creation-for-windows-server/sfx.png