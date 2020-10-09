---
title: Vytvoření samostatného clusteru Azure Service Fabric
description: Vytvořte cluster Azure Service Fabric na jakémkoli počítači (fyzickém nebo virtuálním) s Windows serverem, ať už je to místní nebo v jakémkoli cloudu.
ms.topic: conceptual
ms.date: 2/21/2019
ms.openlocfilehash: 36883f2c8b09fa3f8f013e0267dafa2a8220e5d2
ms.sourcegitcommit: b87c7796c66ded500df42f707bdccf468519943c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/08/2020
ms.locfileid: "91843188"
---
# <a name="create-a-standalone-cluster-running-on-windows-server"></a>Vytvoření samostatného clusteru běžícího na Windows Serveru
Azure Service Fabric můžete použít k vytvoření clusterů Service Fabric na všech virtuálních počítačích nebo počítačích se systémem Windows Server. To znamená, že můžete nasadit a spouštět aplikace Service Fabric v jakémkoli prostředí, které obsahuje sadu propojených počítačů se systémem Windows Server, je místní nebo s jakýmkoli poskytovatelem cloudu. Service Fabric poskytuje instalační balíček pro vytváření clusterů Service Fabric s názvem samostatný balíček Windows serveru. Tradiční Service Fabric clustery v Azure jsou k dispozici jako spravovaná služba, zatímco samostatné clustery Service Fabric jsou samoobslužné. Další informace o rozdílech najdete v tématu [porovnání clusterů Azure a samostatných Service Fabric](./service-fabric-deploy-anywhere.md).

Tento článek vás provede kroky pro vytvoření samostatného clusteru Service Fabric.

> [!NOTE]
> Tento samostatný balíček Windows serveru je komerčně dostupný zdarma a může se používat pro produkční nasazení. Tento balíček může obsahovat nové funkce Service Fabric, které jsou ve verzi Preview. Posuňte se dolů k[funkcím verze Preview, které jsou součástí tohoto balíčku](#previewfeatures_anchor). v části pro seznam funkcí verze Preview. [Kopii smlouvy EULA](https://go.microsoft.com/fwlink/?LinkID=733084) si můžete stáhnout hned teď.
> 
> 

<a id="getsupport"></a>

## <a name="get-support-for-the-service-fabric-for-windows-server-package"></a>Získání podpory pro balíček Service Fabric pro Windows Server
* Zeptejte se komunity na balíček Service Fabric Standalone pro Windows Server na [stránce s dotazem na Microsoft Q&pro Azure Service Fabric](/answers/topics/azure-service-fabric.html).
* Otevřete lístek pro [profesionální podporu pro Service Fabric](https://support.microsoft.com/oas/default.aspx?prid=16146).  Další informace o odborné podpoře od Microsoftu [najdete tady](https://support.microsoft.com/en-us/gp/offerprophone?wa=wsignin1.0).
* Podporu tohoto balíčku můžete také získat jako součást [Microsoft Premier Support](https://support.microsoft.com/en-us/premier).
* Další podrobnosti najdete v tématu [Možnosti podpory pro Azure Service Fabric](./service-fabric-support.md).
* Pro shromažďování protokolů pro účely podpory spusťte [Service Fabric samostatný kolektor protokolů](service-fabric-cluster-standalone-package-contents.md).

<a id="downloadpackage"></a>

## <a name="download-the-service-fabric-for-windows-server-package"></a>Stažení balíčku Service Fabric pro Windows Server
Pokud chcete vytvořit cluster, použijte Service Fabric pro balíček Windows serveru (Windows Server 2012 R2 a novější), který najdete tady: <br>
[Stažení odkazu-Service Fabric samostatný balíček – Windows Server](https://go.microsoft.com/fwlink/?LinkId=730690)

[Tady](service-fabric-cluster-standalone-package-contents.md)najdete podrobnosti o obsahu balíčku.

Balíček Service Fabric runtime se automaticky stáhne v době vytváření clusteru. Pokud nasazujete z počítače, který není připojený k Internetu, Stáhněte si prosím z tohoto místa běhový balíček mimo pásmo: <br>
[Stažení odkazu-Service Fabric runtime – Windows Server](https://go.microsoft.com/fwlink/?linkid=839354)

Najít samostatné ukázky konfigurace clusteru v: <br>
[Samostatné ukázky konfigurace clusteru](https://github.com/Azure-Samples/service-fabric-dotnet-standalone-cluster-configuration/tree/master/Samples)

<a id="createcluster"></a>

## <a name="create-the-cluster"></a>Vytvoření clusteru
Několik ukázkových konfiguračních souborů clusteru se nainstaluje spolu s instalačním balíčkem. Soubor *ClusterConfig.Unsecure.DevCluster.json* představuje nejjednodušší konfiguraci clusteru: nezabezpečený cluster se třemi uzly spuštěnými v jednom počítači.  Další konfigurační soubory popisují clustery s jedním nebo více počítači zabezpečené pomocí certifikátů X.509 nebo s použitím zabezpečení systému Windows.  Nemusíte upravovat žádná výchozí nastavení konfigurace pro tento kurz, ale projděte si konfigurační soubor a seznamte se s nastavením.  Část **nodes** popisuje tři uzly v clusteru: název, IP adresa, [typ uzlu, doména selhání a upgradovací doména](service-fabric-cluster-manifest.md#nodes-on-the-cluster).  Část **properties** definuje [zabezpečení, úroveň spolehlivosti, shromažďování diagnostických dat a typy uzlů](service-fabric-cluster-manifest.md#cluster-properties) pro cluster.

Cluster vytvořený v tomto článku je nezabezpečený.  Každý se může anonymně připojit a provádět operace správy, proto by produkční clustery vždy měly být zabezpečené pomocí certifikátů X.509 nebo zabezpečení systému Windows.  Zabezpečení se konfiguruje jenom při vytváření clusteru a není možné povolit zabezpečení po vytvoření clusteru. Aktualizujte konfigurační soubor povolení [zabezpečení certifikátů](service-fabric-windows-cluster-x509-security.md) nebo [zabezpečení systému Windows](service-fabric-windows-cluster-windows-security.md). V článku [Zabezpečení clusteru](service-fabric-cluster-security.md) najdete další informace o zabezpečení clusteru Service Fabric.

### <a name="step-1-create-the-cluster"></a>Krok 1: Vytvoření clusteru

#### <a name="scenario-a-create-an-unsecured-local-development-cluster"></a>Scénář A: vytvoření nezabezpečeného místního vývojového clusteru
Service Fabric lze nasadit do vývojového clusteru v jednom počítači pomocí *ClusterConfig.Unsecure.DevCluster.jsv* souboru zahrnutém v [ukázkách](https://github.com/Azure-Samples/service-fabric-dotnet-standalone-cluster-configuration/tree/master/Samples).

Rozbalení samostatného balíčku do počítače, zkopírujte Ukázkový konfigurační soubor do místního počítače a potom spusťte skript *CreateServiceFabricCluster.ps1* pomocí relace PowerShellu Správce ze samostatné složky balíčku.

```powershell
.\CreateServiceFabricCluster.ps1 -ClusterConfigFilePath .\ClusterConfig.Unsecure.DevCluster.json -AcceptEULA
```

Podrobnosti o řešení potíží najdete v oddílu nastavení prostředí v tématu [Naplánování a příprava nasazení clusteru](service-fabric-cluster-standalone-deployment-preparation.md) .

Pokud jste dokončili vývojové scénáře, můžete z počítače odebrat Cluster Service Fabric odkazem na kroky v oddílu "[Odebrání clusteru](#removecluster_anchor)". 

#### <a name="scenario-b-create-a-multi-machine-cluster"></a>Scénář B: Vytvoření clusteru s více počítači
Po dokončení kroků pro plánování a přípravu, které jsou podrobně popsané v části plánování [a příprava nasazení clusteru](service-fabric-cluster-standalone-deployment-preparation.md), jste připraveni vytvořit produkční cluster pomocí konfiguračního souboru clusteru.

Správce clusteru, který cluster nasazuje a konfiguruje, musí mít v příslušném počítači oprávnění správce. Service Fabric nelze nainstalovat na řadič domény.

1. Skript *TestConfiguration.ps1* v samostatném balíčku se používá jako analyzátor s osvědčenými postupy pro ověření, jestli je možné cluster nasadit do daného prostředí. V článku [Příprava nasazení](service-fabric-cluster-standalone-deployment-preparation.md) jsou vypsány předpoklady a požadavky prostředí. Spusťte skript a ověřte, jestli můžete vývojový cluster vytvořit:  

    ```powershell
    .\TestConfiguration.ps1 -ClusterConfigFilePath .\ClusterConfig.json
    ```

    Výstup by se měl podobat následujícímu. Pokud je dolní pole "Pass" vráceno jako "true", kontroly správnosti byly úspěšné a cluster bude pravděpodobně možné nasadit na základě konfigurace vstupu.

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

2. Vytvoření clusteru: spuštěním skriptu *CreateServiceFabricCluster.ps1* nasaďte Service Fabric cluster napříč jednotlivými počítači v konfiguraci. 
    ```powershell
    .\CreateServiceFabricCluster.ps1 -ClusterConfigFilePath .\ClusterConfig.json -AcceptEULA
    ```

> [!NOTE]
> Trasování nasazení se zapisují do virtuálního počítače nebo počítače, ve kterém jste spustili skript prostředí PowerShell CreateServiceFabricCluster.ps1. Najít je můžete v podsložce DeploymentTraces v rámci adresáře, ze kterého se skript spustil. Pokud chcete zjistit, jestli nasazení Service Fabric do počítače proběhlo úspěšně, vyhledejte nainstalované soubory v adresáři FabricDataRoot podle popisu v části FabricSettings konfiguračního souboru clusteru (ve výchozím nastavení c:\ProgramData\SF). Kromě toho jsou ve Správci úloh vidět spuštěné procesy FabricHost.exe a Fabric.exe.
> 
> 

#### <a name="scenario-c-create-an-offline-internet-disconnected-cluster"></a>Scénář C: Vytvoření clusteru v režimu offline (Internet-odpojeno)
Balíček Service Fabric runtime se automaticky stáhne při vytváření clusteru. Když nasadíte cluster do počítačů, které nejsou připojené k Internetu, budete muset stáhnout balíček Service Fabric runtime samostatně a zadat jeho cestu při vytváření clusteru.
Balíček modulu runtime lze stáhnout samostatně, z jiného počítače připojeného k Internetu, a to při [stažení odkazu-Service Fabric modul runtime – Windows Server](https://go.microsoft.com/fwlink/?linkid=839354). Zkopírujte běhový balíček do umístění, ze kterého chcete cluster nasadit, a vytvořte cluster spuštěním `CreateServiceFabricCluster.ps1` s `-FabricRuntimePackagePath` parametrem, jak je znázorněno v následujícím příkladu: 

```powershell
.\CreateServiceFabricCluster.ps1 -ClusterConfigFilePath .\ClusterConfig.json -FabricRuntimePackagePath .\MicrosoftAzureServiceFabric.cab
```

*.\ClusterConfig.jsv* a *.\MicrosoftAzureServiceFabric.cab* jsou cesty ke konfiguraci clusteru a souboru runtime. cab v uvedeném pořadí.

### <a name="step-2-connect-to-the-cluster"></a>Krok 2: připojení ke clusteru
Připojte se ke clusteru a ověřte, jestli je cluster spuštěný a dostupný. Modul PowerShell ServiceFabric se instaluje spolu s modulem runtime.  Ke clusteru se můžete připojit z jednoho z uzlů clusteru nebo ze vzdáleného počítače s modulem runtime Service Fabric.  Rutina [Connect-ServiceFabricCluster](/powershell/module/servicefabric/connect-servicefabriccluster?view=azureservicefabricps) vytvoří připojení ke clusteru.

Pokud se chcete připojit k nezabezpečenému clusteru, spusťte následující příkaz prostředí PowerShell:

```powershell
Connect-ServiceFabricCluster -ConnectionEndpoint <*IPAddressofaMachine*>:<Client connection end point port>
```

Příklad:
```powershell
Connect-ServiceFabricCluster -ConnectionEndpoint 192.13.123.234:19000
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

### <a name="step-3-visualize-the-cluster-using-service-fabric-explorer"></a>Krok 3: Vizualizujte cluster pomocí Service Fabric Exploreru
[Service Fabric Explorer](service-fabric-visualizing-your-cluster.md) je nástroj vhodný pro vizualizaci clusteru a správu aplikací.  Service Fabric Explorer je služba, která běží v clusteru, ke kterému přistupujete pomocí prohlížeče, a to tak, že přejdete na `http://localhost:19080/Explorer` .

Řídicí panel clusteru poskytuje přehled o clusteru včetně souhrnu stavu aplikací a uzlů. Zobrazení uzlu obsahuje fyzické rozložení clusteru. Pro daný uzel můžete zjistit, které aplikace mají v uzlu nasazený kód.

![Service Fabric Explorer][service-fabric-explorer]

## <a name="add-and-remove-nodes"></a>Přidávání a odebírání uzlů
Podle toho, jak se vaše obchodní potřeby mění, můžete uzly do samostatného clusteru Service Fabric přidávat nebo je z něj odebírat. Podrobné kroky najdete v tématu [Přidávání uzlů do samostatného clusteru Service Fabric nebo jejich odebírání](service-fabric-cluster-windows-server-add-remove-nodes.md).

<a id="removecluster" name="removecluster_anchor"></a>
## <a name="remove-a-cluster"></a>Odebrat cluster
Chcete-li cluster odebrat, spusťte skript *RemoveServiceFabricCluster.ps1* prostředí PowerShell ze složky balíčku a předejte mu cestu ke konfiguračnímu souboru JSON. Volitelně můžete určit umístění pro protokol odstranění.

Tento skript se dá spustit na jakémkoli počítači, který má oprávnění správce ke všem počítačům, které jsou uvedené jako uzly v konfiguračním souboru clusteru. Počítač, ve kterém je tento skript spuštěn, nemusí být součástí clusteru.

```powershell
# Removes Service Fabric from each machine in the configuration
.\RemoveServiceFabricCluster.ps1 -ClusterConfigFilePath .\ClusterConfig.json -Force
```

```powershell
# Removes Service Fabric from the current machine
.\CleanFabric.ps1
```

<a id="telemetry"></a>

## <a name="telemetry-data-collected-and-how-to-opt-out-of-it"></a>Shromážděná data telemetrie a její odhlášení
Ve výchozím nastavení produkt shromažďuje telemetrii o využití Service Fabric k vylepšení produktu. Analyzátor osvědčených postupů, který je spuštěn jako součást instalačního programu, kontroluje dostupnost připojení [https://vortex.data.microsoft.com/collect/v1](https://vortex.data.microsoft.com/collect/v1) . Pokud není dosažitelný, instalace se nezdařila, pokud nebudete odhlásit z telemetrie.

1. Kanál telemetrie se pokusí odeslat následující data do [https://vortex.data.microsoft.com/collect/v1](https://vortex.data.microsoft.com/collect/v1) každého dne. Je to nejlepší nahrávání a nemá žádný vliv na funkce clusteru. Telemetrie se odesílá jenom z uzlu, na kterém běží primární správce převzetí služeb při selhání. Žádné další uzly neodesílají telemetrii.
2. Telemetrii se skládá z těchto možností:

* Počet služeb
* Počet ServiceTypeí
* Počet aplikací
* Počet ApplicationUpgrades
* Počet PLB
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
* IsContextComplete: true nebo false
* ClusterId: Toto je náhodně generovaný identifikátor GUID pro každý cluster.
* ServiceFabricVersion
* IP adresa virtuálního počítače nebo počítače, ze kterého se má telemetrie nahrát

Pro vypnutí telemetrie přidejte do *vlastností* konfigurace clusteru následující: *konfiguračního EnableTelemetry: false*.

<a id="previewfeatures" name="previewfeatures_anchor"></a>

## <a name="preview-features-included-in-this-package"></a>Funkce ve verzi Preview, které jsou součástí tohoto balíčku
Žádné


> [!NOTE]
> Počínaje novou [verzí GA samostatného clusteru pro Windows Server (verze 5.3.204. x)](https://azure.microsoft.com/blog/azure-service-fabric-for-windows-server-now-ga/)můžete cluster upgradovat na budoucí verze, a to ručně nebo automaticky. Podrobnosti najdete v tématu [upgrade samostatného dokumentu verze Service Fabric clusteru](service-fabric-cluster-upgrade-windows-server.md) .
> 
> 

## <a name="next-steps"></a>Další kroky
* [Nasazení a odebrání aplikací pomocí PowerShellu](service-fabric-deploy-remove-applications.md)
* [Nastavení konfigurace pro samostatný cluster Windows](service-fabric-cluster-manifest.md)
* [Přidání nebo odebrání uzlů do samostatného Service Fabric clusteru](service-fabric-cluster-windows-server-add-remove-nodes.md)
* [Upgrade samostatné verze Service Fabric clusteru](service-fabric-cluster-upgrade-windows-server.md)
* [Vytvoření samostatného Service Fabricho clusteru s virtuálními počítači Azure s Windows](./service-fabric-cluster-creation-via-arm.md)
* [Zabezpečení samostatného clusteru ve Windows pomocí zabezpečení systému Windows](service-fabric-windows-cluster-windows-security.md)
* [Zabezpečení samostatného clusteru ve Windows pomocí certifikátů x509](service-fabric-windows-cluster-x509-security.md)

<!--Image references-->
[Trusted Zone]: ./media/service-fabric-cluster-creation-for-windows-server/TrustedZone.png
[service-fabric-explorer]: ./media/service-fabric-cluster-creation-for-windows-server/sfx.png
