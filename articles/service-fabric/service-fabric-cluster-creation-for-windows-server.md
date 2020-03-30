---
title: Vytvoření samostatného clusteru Azure Service Fabric
description: Vytvořte cluster Azure Service Fabric na libovolném počítači (fyzickém nebo virtuálním) se systémem Windows Server, ať už je místní nebo v jakémkoli cloudu.
author: dkkapur
ms.topic: conceptual
ms.date: 2/21/2019
ms.author: dekapur
ms.openlocfilehash: 461d6021a201ca1fa5722bb44c427baca2a7728e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "79258821"
---
# <a name="create-a-standalone-cluster-running-on-windows-server"></a>Vytvoření samostatného clusteru běžícího na Windows Serveru
Azure Service Fabric můžete použít k vytvoření clusterů Service Fabric na všech virtuálních počítačích nebo počítačích se systémem Windows Server. To znamená, že můžete nasadit a spustit aplikace Service Fabric v jakémkoli prostředí, které obsahuje sadu propojených počítačů se systémem Windows Server, ať už místně nebo s jakýmkoli poskytovatelem cloudu. Service Fabric poskytuje instalační balíček pro vytvoření clusterů Service Fabric nazývaných samostatný balíček Windows Server. Tradiční clustery Service Fabric v Azure jsou k dispozici jako spravovaná služba, zatímco samostatné clustery Service Fabric jsou samoobslužné. Další informace o rozdílech najdete v [tématu Porovnání clusterů Azure a samostatné service fabric](./service-fabric-deploy-anywhere.md).

Tento článek vás provede kroky pro vytvoření samostatného clusteru Service Fabric.

> [!NOTE]
> Tento samostatný balíček windows serveru je komerčně dostupný zdarma a může být použit pro produkční nasazení. Tento balíček může obsahovat nové funkce Service Fabric, které jsou v "Náhled". Posuňte se dolů na["Náhled funkcí obsažených v tomto balíčku](#previewfeatures_anchor)". pro seznam funkcí náhledu. Nyní si můžete [stáhnout kopii eula.](https://go.microsoft.com/fwlink/?LinkID=733084)
> 
> 

<a id="getsupport"></a>

## <a name="get-support-for-the-service-fabric-for-windows-server-package"></a>Získání podpory pro service fabric pro balíček Windows Server
* Zeptejte se komunity na samostatný balíček Service Fabric pro Windows Server ve [fóru Azure Service Fabric](https://social.msdn.microsoft.com/Forums/azure/en-US/home?forum=AzureServiceFabric?).
* Otevřete lístek pro [profesionální podporu service fabric](https://support.microsoft.com/oas/default.aspx?prid=16146).  Další informace o odborné podpoře od [microsoftu naleznete zde](https://support.microsoft.com/en-us/gp/offerprophone?wa=wsignin1.0).
* Podporu pro tento balíček můžete získat také jako součást [podpory Microsoft Premier Support](https://support.microsoft.com/en-us/premier).
* Další podrobnosti najdete v [tématu Možnosti podpory Azure Service Fabric](https://docs.microsoft.com/azure/service-fabric/service-fabric-support).
* Chcete-li shromažďovat protokoly pro účely podpory, spusťte [kolektor samostatných protokolů Service Fabric](service-fabric-cluster-standalone-package-contents.md).

<a id="downloadpackage"></a>

## <a name="download-the-service-fabric-for-windows-server-package"></a>Stažení balíčku Service Fabric pro Windows Server
Chcete-li vytvořit cluster, použijte balíček Service Fabric for Windows Server (Windows Server 2012 R2 a novější), který naleznete zde: <br>
[Odkaz ke stažení – samostatný balíček Service Fabric - Windows Server](https://go.microsoft.com/fwlink/?LinkId=730690)

Podrobnosti o obsahu balíčku naleznete [zde](service-fabric-cluster-standalone-package-contents.md).

Balíček runtime Service Fabric se automaticky stáhne v době vytvoření clusteru. Pokud nasazujete ze zařízení, které není připojeno k internetu, stáhněte si runtime balíček mimo pásmo zde: <br>
[Odkaz ke stažení – service fabric runtime – Windows Server](https://go.microsoft.com/fwlink/?linkid=839354)

Najdete ukázky konfigurace samostatného clusteru na adrese: <br>
[Ukázky konfigurace samostatného clusteru](https://github.com/Azure-Samples/service-fabric-dotnet-standalone-cluster-configuration/tree/master/Samples)

<a id="createcluster"></a>

## <a name="create-the-cluster"></a>Vytvoření clusteru
Několik ukázkových konfiguračních souborů clusteru se nainstaluje spolu s instalačním balíčkem. Soubor *ClusterConfig.Unsecure.DevCluster.json* představuje nejjednodušší konfiguraci clusteru: nezabezpečený cluster se třemi uzly spuštěnými v jednom počítači.  Další konfigurační soubory popisují clustery s jedním nebo více počítači zabezpečené pomocí certifikátů X.509 nebo s použitím zabezpečení systému Windows.  Nemusíte upravovat žádná výchozí nastavení konfigurace pro tento kurz, ale projděte si konfigurační soubor a seznamte se s nastavením.  Část **nodes** popisuje tři uzly v clusteru: název, IP adresa, [typ uzlu, doména selhání a upgradovací doména](service-fabric-cluster-manifest.md#nodes-on-the-cluster).  Část **properties** definuje [zabezpečení, úroveň spolehlivosti, shromažďování diagnostických dat a typy uzlů](service-fabric-cluster-manifest.md#cluster-properties) pro cluster.

Cluster vytvořený v tomto článku není zabezpečený.  Každý se může anonymně připojit a provádět operace správy, proto by produkční clustery vždy měly být zabezpečené pomocí certifikátů X.509 nebo zabezpečení systému Windows.  Zabezpečení se konfiguruje jenom při vytváření clusteru a není možné povolit zabezpečení po vytvoření clusteru. Aktualizace konfiguračního souboru povoluje [zabezpečení certifikátů](service-fabric-windows-cluster-x509-security.md) nebo [zabezpečení systému Windows](service-fabric-windows-cluster-windows-security.md). V článku [Zabezpečení clusteru](service-fabric-cluster-security.md) najdete další informace o zabezpečení clusteru Service Fabric.

### <a name="step-1-create-the-cluster"></a>Krok 1: Vytvoření clusteru

#### <a name="scenario-a-create-an-unsecured-local-development-cluster"></a>Scénář A: Vytvoření nezabezpečeného clusteru místního vývoje
Service Fabric lze nasadit do vývojového clusteru s jedním počítačem pomocí souboru *ClusterConfig.Unsecure.DevCluster.json,* který je součástí [ukázek](https://github.com/Azure-Samples/service-fabric-dotnet-standalone-cluster-configuration/tree/master/Samples).

Rozbalte samostatný balíček do počítače, zkopírujte ukázkový konfigurační soubor do místního počítače a spusťte skript *CreateServiceFabricCluster.ps1* prostřednictvím relace správce prostředí PowerShell ze samostatné složky balíčku.

```powershell
.\CreateServiceFabricCluster.ps1 -ClusterConfigFilePath .\ClusterConfig.Unsecure.DevCluster.json -AcceptEULA
```

Podívejte se na část Nastavení prostředí v [plánu a připravte nasazení clusteru](service-fabric-cluster-standalone-deployment-preparation.md) pro podrobnosti o řešení potíží.

Pokud jste dokončili spuštění scénářů vývoje, můžete odebrat cluster Service Fabric z počítače odkazem na kroky v části "[Odebrat cluster](#removecluster_anchor)". 

#### <a name="scenario-b-create-a-multi-machine-cluster"></a>Scénář B: Vytvoření clusteru s více počítači
Po projděte si kroky plánování a přípravy podrobně popsané v [plánu a přípravě nasazení clusteru](service-fabric-cluster-standalone-deployment-preparation.md), jste připraveni vytvořit produkční cluster pomocí konfiguračního souboru clusteru.

Správce clusteru, který cluster nasazuje a konfiguruje, musí mít v příslušném počítači oprávnění správce. Service Fabric nelze nainstalovat na řadič domény.

1. Skript *TestConfiguration.ps1* v samostatném balíčku se používá jako analyzátor s osvědčenými postupy pro ověření, jestli je možné cluster nasadit do daného prostředí. V článku [Příprava nasazení](service-fabric-cluster-standalone-deployment-preparation.md) jsou vypsány předpoklady a požadavky prostředí. Spusťte skript a ověřte, jestli můžete vývojový cluster vytvořit:  

    ```powershell
    .\TestConfiguration.ps1 -ClusterConfigFilePath .\ClusterConfig.json
    ```

    Zobrazený výstup by měl vypadat přibližně takto: Pokud je dolní pole "Předáno" vráceno jako "True", kontroly příčetnosti prošly a cluster vypadá, že je nasaditelný na základě vstupní konfigurace.

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

2. Vytvoření clusteru: Spusťte skript *CreateServiceFabricCluster.ps1* a nasaďte cluster Service Fabric v každém počítači v konfiguraci. 
    ```powershell
    .\CreateServiceFabricCluster.ps1 -ClusterConfigFilePath .\ClusterConfig.json -AcceptEULA
    ```

> [!NOTE]
> Trasování nasazení se zapisují do virtuálního počítače nebo počítače, ve kterém jste spustili skript prostředí PowerShell CreateServiceFabricCluster.ps1. Najít je můžete v podsložce DeploymentTraces v rámci adresáře, ze kterého se skript spustil. Pokud chcete zjistit, jestli nasazení Service Fabric do počítače proběhlo úspěšně, vyhledejte nainstalované soubory v adresáři FabricDataRoot podle popisu v části FabricSettings konfiguračního souboru clusteru (ve výchozím nastavení c:\ProgramData\SF). Kromě toho jsou ve Správci úloh vidět spuštěné procesy FabricHost.exe a Fabric.exe.
> 
> 

#### <a name="scenario-c-create-an-offline-internet-disconnected-cluster"></a>Scénář C: Vytvoření offline (odpojeného z internetu) clusteru
Balíček runtime Service Fabric se automaticky stáhne při vytváření clusteru. Při nasazování clusteru do počítačů, které nejsou připojeny k Internetu, budete muset stáhnout balíček runtime Service Fabric samostatně a poskytnout cestu k němu při vytváření clusteru.
Runtime balíček lze stáhnout samostatně, z jiného počítače připojeného k internetu, na [download link - Service Fabric Runtime - Windows Server](https://go.microsoft.com/fwlink/?linkid=839354). Zkopírujte balíček runtime do místa, odkud nasazujete offline cluster, a vytvořte cluster spuštěním `CreateServiceFabricCluster.ps1` s `-FabricRuntimePackagePath` zahrnutým parametrem, jak je znázorněno v tomto příkladu: 

```powershell
.\CreateServiceFabricCluster.ps1 -ClusterConfigFilePath .\ClusterConfig.json -FabricRuntimePackagePath .\MicrosoftAzureServiceFabric.cab
```

*.\ClusterConfig.json* a *.\MicrosoftAzureServiceFabric.cab* jsou cesty ke konfiguraci clusteru a souboru CAB za běhu.

### <a name="step-2-connect-to-the-cluster"></a>Krok 2: Připojení ke clusteru
Připojte se ke clusteru a ověřte, zda je cluster spuštěný a dostupný. Modul PowerShell ServiceFabric se instaluje spolu s modulem runtime.  Ke clusteru se můžete připojit z jednoho z uzlů clusteru nebo ze vzdáleného počítače pomocí runtime Service Fabric.  Rutina [Connect-ServiceFabricCluster](/powershell/module/servicefabric/connect-servicefabriccluster?view=azureservicefabricps) vytvoří připojení ke clusteru.

Chcete-li se připojit k nezabezpečenému clusteru, spusťte následující příkaz prostředí PowerShell:

```powershell
Connect-ServiceFabricCluster -ConnectionEndpoint <*IPAddressofaMachine*>:<Client connection end point port>
```

Například:
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

### <a name="step-3-visualize-the-cluster-using-service-fabric-explorer"></a>Krok 3: Vizualizace clusteru pomocí průzkumníka Service Fabric
[Service Fabric Explorer](service-fabric-visualizing-your-cluster.md) je nástroj vhodný pro vizualizaci clusteru a správu aplikací.  Service Fabric Explorer je služba spuštěná v clusteru, ke které [http://localhost:19080/Explorer](http://localhost:19080/Explorer)přistupujete pomocí prohlížeče na .

Řídicí panel clusteru poskytuje přehled o clusteru včetně souhrnu stavu aplikací a uzlů. Zobrazení uzlu obsahuje fyzické rozložení clusteru. Pro daný uzel můžete zjistit, které aplikace mají v uzlu nasazený kód.

![Service Fabric Explorer][service-fabric-explorer]

## <a name="add-and-remove-nodes"></a>Přidávání a odebírání uzlů
Podle toho, jak se vaše obchodní potřeby mění, můžete uzly do samostatného clusteru Service Fabric přidávat nebo je z něj odebírat. Podrobné kroky najdete v tématu [Přidávání uzlů do samostatného clusteru Service Fabric nebo jejich odebírání](service-fabric-cluster-windows-server-add-remove-nodes.md).

<a id="removecluster" name="removecluster_anchor"></a>
## <a name="remove-a-cluster"></a>Odebrat cluster
Chcete-li cluster odebrat, spusťte skript *RemoveServiceFabricCluster.ps1* prostředí PowerShell ze složky balíčku a předejte mu cestu ke konfiguračnímu souboru JSON. Volitelně můžete určit umístění pro protokol odstranění.

Tento skript lze spustit v libovolném počítači, který má přístup správce ke všem počítačům, které jsou uvedeny jako uzly v konfiguračním souboru clusteru. Počítač, na který je tento skript spuštěn, nemusí být součástí clusteru.

```powershell
# Removes Service Fabric from each machine in the configuration
.\RemoveServiceFabricCluster.ps1 -ClusterConfigFilePath .\ClusterConfig.json -Force
```

```powershell
# Removes Service Fabric from the current machine
.\CleanFabric.ps1
```

<a id="telemetry"></a>

## <a name="telemetry-data-collected-and-how-to-opt-out-of-it"></a>Shromážděná telemetrická data a způsob, jak se z ně odhlásit
Ve výchozím nastavení produkt shromažďuje telemetrická data na service fabric využití ke zlepšení produktu. Analyzátor osvědčených postupů, který běží jako součást [https://vortex.data.microsoft.com/collect/v1](https://vortex.data.microsoft.com/collect/v1)instalace, kontroluje připojení k aplikaci . Pokud není k zastižení, instalace se nezdaří, pokud se odhlásíte z telemetrie.

1. Kanál telemetrie se pokusí nahrát následující data [https://vortex.data.microsoft.com/collect/v1](https://vortex.data.microsoft.com/collect/v1) jednou denně. Jedná se o nejlepší úsilí nahrát a nemá žádný vliv na funkčnost clusteru. Telemetrie je odesílána pouze z uzlu, který spouští primární správce převzetí služeb při selhání. Žádné jiné uzly nevysílají telemetrii.
2. Telemetrie se skládá z následujících:

* Počet služeb
* Počet typů služeb
* Počet žádostí
* Počet upgradů aplikací
* Počet jednotek s podporou převzetí služeb při selhání
* Počet jednotek InBuildFailoverUnits
* Počet jednotek Nezdravé selhání
* Počet replik
* Počet replik inbuild
* Počet pohotovostních replik
* Počet offlinereplik
* CommonQueueLength
* QueryQueueLength
* Převzetí služeb při selháníUnitQueueLength
* Délka fronty commit
* Počet uzlů
* IsContextComplete: Pravda/Nepravda
* ClusterId: Toto je identifikátor GUID náhodně generovaný pro každý cluster.
* ServiceFabricVersion
* IP adresa virtuálního počítače nebo počítače, ze kterého se telemetrie nahrává

Chcete-li zakázat telemetrii, přidejte do *vlastností* konfigurace clusteru následující vlastnosti: *enableTelemetry: false*.

<a id="previewfeatures" name="previewfeatures_anchor"></a>

## <a name="preview-features-included-in-this-package"></a>Funkce náhledu obsažené v tomto balíčku
Žádné.


> [!NOTE]
> Počínaje novou [verzí samostatného clusteru ga pro windows server (verze 5.3.204.x)](https://azure.microsoft.com/blog/azure-service-fabric-for-windows-server-now-ga/)můžete cluster upgradovat na budoucí verze ručně nebo automaticky. Podrobnosti naleznete [v dokumentu upgrade samostatné verze clusteru Service Fabric.](service-fabric-cluster-upgrade-windows-server.md)
> 
> 

## <a name="next-steps"></a>Další kroky
* [Nasazení a odebrání aplikací pomocí PowerShellu](service-fabric-deploy-remove-applications.md)
* [Nastavení konfigurace samostatného clusteru Windows](service-fabric-cluster-manifest.md)
* [Přidání nebo odebrání uzlů do samostatného clusteru Service Fabric](service-fabric-cluster-windows-server-add-remove-nodes.md)
* [Upgrade verze samostatného clusteru Service Fabric](service-fabric-cluster-upgrade-windows-server.md)
* [Vytvoření samostatného clusteru Service Fabric s virtuálními počítači Azure se systémem Windows](service-fabric-cluster-creation-with-windows-azure-vms.md)
* [Zabezpečení samostatného clusteru v systému Windows pomocí zabezpečení systému Windows](service-fabric-windows-cluster-windows-security.md)
* [Zabezpečení samostatného clusteru v systému Windows pomocí certifikátů X509](service-fabric-windows-cluster-x509-security.md)

<!--Image references-->
[Trusted Zone]: ./media/service-fabric-cluster-creation-for-windows-server/TrustedZone.png
[service-fabric-explorer]: ./media/service-fabric-cluster-creation-for-windows-server/sfx.png
