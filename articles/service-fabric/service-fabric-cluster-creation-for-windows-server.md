---
title: Vytvoření clusteru s podporou Azure Service Fabric samostatné | Microsoft Docs
description: Vytvoření clusteru služby Azure Service Fabric z jakéhokoli počítače (fyzické nebo virtuální) s Windows serverem, ať už místní nebo v jakékoli cloudu.
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
ms.openlocfilehash: efa48aa90806b45c99237404af24cb8aba762d15
ms.sourcegitcommit: eb75f177fc59d90b1b667afcfe64ac51936e2638
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/16/2018
---
# <a name="create-a-standalone-cluster-running-on-windows-server"></a>Vytvoření clusteru s podporou samostatné systémem Windows Server
Azure Service Fabric můžete použít k vytvoření clusterů Service Fabric na všechny virtuální počítače nebo počítače se systémem Windows Server. To znamená, můžete nasazovat a spouštět aplikace Service Fabric v jakémkoli prostředí, které obsahuje sadu vzájemně propojena počítačů Windows serveru, je-li jej v místním nebo se všechny poskytovatele cloudových služeb. Service Fabric nabízí instalační balíček k vytvoření clusterů Service Fabric se nazývá samostatný balíček Windows Server.

Tento článek vás provede kroky pro vytvoření samostatné clusteru Service Fabric.

> [!NOTE]
> Tento balíček samostatné systému Windows Server je dostupný a mohou být použity pro nasazení v produkčním prostředí. Tento balíček může obsahovat nové funkce Service Fabric, které jsou v "Náhled". Přejděte dolů k položce "[zobrazit náhled funkce obsažené v tomto balíčku](#previewfeatures_anchor)." části seznamu funkce verze preview. Můžete [stáhnout kopii smlouvy EULA](http://go.microsoft.com/fwlink/?LinkID=733084) nyní.
> 
> 

<a id="getsupport"></a>

## <a name="get-support-for-the-service-fabric-for-windows-server-package"></a>Získat podporu pro balíček prostředků infrastruktury služby pro systém Windows Server
* Zeptejte se komunity pro systém Windows Server v o samostatný balíček Service Fabric [fórum Azure Service Fabric](https://social.msdn.microsoft.com/Forums/azure/en-US/home?forum=AzureServiceFabric?).
* Otevřít lístek pro [Professional podporu pro Service Fabric](http://support.microsoft.com/oas/default.aspx?prid=16146).  Další informace o Professional podporu společnosti Microsoft [zde](https://support.microsoft.com/en-us/gp/offerprophone?wa=wsignin1.0).
* Můžete také získat podporu pro tento balíček jako součást [Microsoft Premier Support](https://support.microsoft.com/en-us/premier).
* Další podrobnosti najdete v tématu [možnosti podpory Azure Service Fabric](https://docs.microsoft.com/azure/service-fabric/service-fabric-support).
* Chcete-li shromažďovat protokoly pro účely podpory, spusťte [kolektor protokolů samostatné Service Fabric](service-fabric-cluster-standalone-package-contents.md).

<a id="downloadpackage"></a>

## <a name="download-the-service-fabric-for-windows-server-package"></a>Stáhněte si balíček prostředků infrastruktury služby pro systém Windows Server
Pokud chcete vytvořit cluster, použít balíček prostředků infrastruktury služby pro systém Windows Server (Windows Server 2012 R2 a novější) nachází tady: <br>
[Stáhněte si odkaz – samostatný balíček prostředků infrastruktury služby - Windows Server](http://go.microsoft.com/fwlink/?LinkId=730690)

Najde podrobnosti o na obsah balíčku [zde](service-fabric-cluster-standalone-package-contents.md).

V době vytváření clusteru automaticky stažení balíčku modulu runtime Service Fabric. Pokud nasazení z počítače není připojený k Internetu, stáhněte balíček modulu runtime vzdálené správy z tohoto umístění: <br>
[Stáhněte si odkaz - Service Fabric Runtime - Windows Server](https://go.microsoft.com/fwlink/?linkid=839354)

Najděte ukázky samostatné konfigurace clusteru na: <br>
[Konfigurace clusteru samostatné – ukázky](https://github.com/Azure-Samples/service-fabric-dotnet-standalone-cluster-configuration/tree/master/Samples)

<a id="createcluster"></a>

## <a name="create-the-cluster"></a>Vytvoření clusteru
Service Fabric lze nasadit do clusteru s podporou vývoj pro jeden počítač pomocí *ClusterConfig.Unsecure.DevCluster.json* zahrnutý v [ukázky](https://github.com/Azure-Samples/service-fabric-dotnet-standalone-cluster-configuration/tree/master/Samples).

Rozbalte balíček samostatné k počítači, zkopírujte vzorový konfigurační soubor do místního počítače, a pak spustit *CreateServiceFabricCluster.ps1* skriptu prostřednictvím relaci prostředí PowerShell správce ze složky balíčku samostatné:
### <a name="step-1a-create-an-unsecured-local-development-cluster"></a>Krok 1A: vytvoření zabezpečená místního vývojového clusteru
```powershell
.\CreateServiceFabricCluster.ps1 -ClusterConfigFilePath .\ClusterConfig.Unsecure.DevCluster.json -AcceptEULA
```

Najdete v části nastavení prostředí na [plánování a příprava nasazení clusteru](service-fabric-cluster-standalone-deployment-preparation.md) pro řešení potíží s podrobnosti.

Pokud dokončíte spuštěné vývojové scénáře, můžete odebrat cluster Service Fabric z počítače tím, že odkazuje na postup v části "[odebrat cluster](#removecluster_anchor)". 

### <a name="step-1b-create-a-multi-machine-cluster"></a>Krok 1B: vytvoření clusteru s více počítači
Poté, co jste prošli plánování a přípravné kroky popsané v následujícím odkazu, budete chtít vytvořit cluster produkční pomocí konfiguračního souboru clusteru. <br>
[Plánování a příprava nasazení clusteru](service-fabric-cluster-standalone-deployment-preparation.md)

1. Ověřit konfigurační soubor, který jste napsali spuštěním *TestConfiguration.ps1* skript ve složce samostatný balíček:  

    ```powershell
    .\TestConfiguration.ps1 -ClusterConfigFilePath .\ClusterConfig.json
    ```

    Zobrazený výstup by měl jako níže. Pokud pole dolní "Předání" se vrátí jako "PRAVDA", mají zkontrolovány správností a cluster vypadá jako nasadit na základě vstupních konfigurace.

    ```
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

2. Vytvoření clusteru: spuštění *CreateServiceFabricCluster.ps1* skriptu nasazení clusteru Service Fabric v rámci každého počítače v konfiguraci. 
    ```powershell
    .\CreateServiceFabricCluster.ps1 -ClusterConfigFilePath .\ClusterConfig.json -AcceptEULA
    ```

> [!NOTE]
> Trasování nasazení se zapisují do virtuálního počítače nebo počítače, na kterém jste spustili skript prostředí PowerShell CreateServiceFabricCluster.ps1. Ty lze najít v podsložce DeploymentTraces, na základě v adresáři, ze kterého se spouštěl skript. Pokud chcete zobrazit, pokud se k počítači správně nasazena Service Fabric, najít nainstalované soubory v adresáři FabricDataRoot adresáři, podle popisu v části FabricSettings clusteru konfigurační soubor (ve výchozím nastavení c:\ProgramData\SF). FabricHost.exe a Fabric.exe procesy je možné taky zobrazit spuštěná ve Správci úloh.
> 
> 

### <a name="step-1c-create-an-offline-internet-disconnected-cluster"></a>Krok 1C: vytvoření clusteru do režimu offline (internet odpojení)
Při vytváření clusteru automaticky stažení balíčku modulu runtime Service Fabric. Při nasazení v clusteru s podporou počítače nejsou připojené k Internetu, musíte stáhnout balíček modulu runtime Service Fabric samostatně a zadat cestu k němu při vytváření clusteru.
Balíček modulu runtime lze stáhnout samostatně z jiného počítače připojené k Internetu, v [stáhnout odkaz - Service Fabric Runtime - Windows Server](https://go.microsoft.com/fwlink/?linkid=839354). Zkopírujte balíček modulu runtime kde nasazujete offline clusteru a vytvoření clusteru spuštěním `CreateServiceFabricCluster.ps1` s `-FabricRuntimePackagePath` parametr zahrnuty, jak je uvedeno níže: 

```powershell
.\CreateServiceFabricCluster.ps1 -ClusterConfigFilePath .\ClusterConfig.json -FabricRuntimePackagePath .\MicrosoftAzureServiceFabric.cab
```
kde `.\ClusterConfig.json` a `.\MicrosoftAzureServiceFabric.cab` jsou cesty k konfiguraci clusteru a soubor .cab do modulu runtime v uvedeném pořadí.


### <a name="step-2-connect-to-the-cluster"></a>Krok 2: Připojte se ke clusteru
Pokud chcete připojit ke clusteru s podporou zabezpečení, najdete v části [ke zabezpečení clusteru Service fabric připojit](service-fabric-connect-to-secure-cluster.md).

Pro připojení k nezabezpečená clusteru, spusťte následující příkaz prostředí PowerShell:

```powershell
Connect-ServiceFabricCluster -ConnectionEndpoint <*IPAddressofaMachine*>:<Client connection end point port>
```
Příklad:
```powershell
Connect-ServiceFabricCluster -ConnectionEndpoint 192.13.123.2345:19000
```
### <a name="step-3-bring-up-service-fabric-explorer"></a>Krok 3: Zprovoznit Service Fabric Exploreru
Teď můžete připojit ke clusteru pomocí Service Fabric Exploreru buď přímo od jednoho z počítačů s http://localhost:19080/Explorer/index.html nebo vzdáleně pomocí http://<*IPAddressofaMachine*>: 19080/Explorer/index.html.

## <a name="add-and-remove-nodes"></a>Přidání a odebrání uzlů
Můžete přidávat nebo odebírat uzly do clusteru Service Fabric samostatné, podle potřeb organizace změnu. V tématu [přidávat nebo odebírat uzly do clusteru Service Fabric samostatné](service-fabric-cluster-windows-server-add-remove-nodes.md) podrobné pokyny.

<a id="removecluster" name="removecluster_anchor"></a>
## <a name="remove-a-cluster"></a>Odebrání clusteru
Chcete-li cluster odebrat, spusťte skript *RemoveServiceFabricCluster.ps1* prostředí PowerShell ze složky balíčku a předejte mu cestu ke konfiguračnímu souboru JSON. Volitelně můžete určit umístění pro protokol odstranění.

Tento skript můžete spustit na jakýkoli počítač, který má práva správce pro všechny počítače, které jsou označeny jako uzly v souboru konfigurace clusteru. Na počítač, který tento skript je spuštěn na nemá být součástí clusteru.

```
# Removes Service Fabric from each machine in the configuration
.\RemoveServiceFabricCluster.ps1 -ClusterConfigFilePath .\ClusterConfig.json -Force
```

```
# Removes Service Fabric from the current machine
.\CleanFabric.ps1
```

<a id="telemetry"></a>

## <a name="telemetry-data-collected-and-how-to-opt-out-of-it"></a>Mezi shromažďovaná telemetrická data a jak pro vyjádření výslovného nesouhlasu se
Ve výchozím produktu shromažďuje telemetrická data na Service Fabric použití k vylepšení produktu. Analyzátor osvědčených postupů, který běží jako součást instalace zkontroluje připojení k [ https://vortex.data.microsoft.com/collect/v1 ](https://vortex.data.microsoft.com/collect/v1). Pokud není dostupný, instalace se nezdaří, pokud vyjádření výslovného nesouhlasu telemetrie.

1. Kanál telemetrie se pokusí odeslat následující data, která mají [ https://vortex.data.microsoft.com/collect/v1 ](https://vortex.data.microsoft.com/collect/v1) jednou denně. Je načtení typu best effort a nemá žádný vliv na fungování clusteru. Telemetrie je odeslán, pouze z uzlu, který běží převzetí služeb při selhání primární správce. Žádné další uzly k odeslání telemetrie.
2. Telemetrie se skládá z následujících akcí:

* Počet služeb
* Počet ServiceTypes
* Počet aplikací
* Počet ApplicationUpgrades
* Počet FailoverUnits
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
* ClusterId: Toto je identifikátor GUID náhodně vygenerované pro každý cluster
* ServiceFabricVersion
* IP adresu virtuálního počítače nebo počítače, ze kterého se nahraje telemetrii

Zakázat telemetrii, přidejte následující *vlastnosti* v souboru config clusteru: *enableTelemetry: false*.

<a id="previewfeatures" name="previewfeatures_anchor"></a>

## <a name="preview-features-included-in-this-package"></a>Funkce Preview obsažené v tomto balíčku
Žádné.


> [!NOTE]
> Počínaje nové [verzí GA samostatné clusteru pro systém Windows Server (verze 5.3.204.x)](https://azure.microsoft.com/blog/azure-service-fabric-for-windows-server-now-ga/), můžete provést upgrade clusteru na budoucích verzích ručně nebo automaticky. Odkazovat na [upgradovat na verzi clusteru Service Fabric samostatné](service-fabric-cluster-upgrade-windows-server.md) v dokumentu.
> 
> 

## <a name="next-steps"></a>Další postup
* [Nasazení a odebírat aplikace pomocí prostředí PowerShell](service-fabric-deploy-remove-applications.md)
* [Nastavení konfigurace pro samostatný cluster Windows](service-fabric-cluster-manifest.md)
* [Přidávat nebo odebírat uzly do clusteru s podporou samostatné Service Fabric](service-fabric-cluster-windows-server-add-remove-nodes.md)
* [Upgrade na verzi clusteru Service Fabric samostatné](service-fabric-cluster-upgrade-windows-server.md)
* [Vytvořit cluster Service Fabric samostatné s virtuálními počítači Azure s Windows](service-fabric-cluster-creation-with-windows-azure-vms.md)
* [Zabezpečení clusteru s podporou samostatné do systému Windows pomocí zabezpečení systému Windows](service-fabric-windows-cluster-windows-security.md)
* [Zabezpečení clusteru s podporou samostatné do systému Windows pomocí X509 certifikáty](service-fabric-windows-cluster-x509-security.md)

<!--Image references-->
[Trusted Zone]: ./media/service-fabric-cluster-creation-for-windows-server/TrustedZone.png
