---
title: Azure Service Fabric samostatný Cluster nasazení přípravy | Microsoft Docs
description: Dokumentace týkající se přípravy prostředí a vytváření konfigurace clusteru, aby byla považována za před nasazením clusteru s podporou určený pro produkční zatížení zpracování.
services: service-fabric
documentationcenter: .net
author: dkkapur
manager: timlt
editor: ''
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 9/12/2017
ms.author: dekapur;maburlik;aljo
ms.openlocfilehash: 62673025f5c597f6ed958ad523190d937a52c912
ms.sourcegitcommit: 9cdd83256b82e664bd36991d78f87ea1e56827cd
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/16/2018
---
<a id="preparemachines"></a>

# <a name="plan-and-prepare-your-service-fabric-standalone-cluster-deployment"></a>Plánování a příprava nasazení clusteru Service Fabric Standalone
Před vytvořením clusteru, proveďte následující kroky.

## <a name="plan-your-cluster-infrastructure"></a>Plánování vaší infrastruktury clusteru
Chystáte se vytvořit cluster Service Fabric na počítačích, které jste "vlastní", abyste se mohli rozhodnout, jaké druhy chyb má cluster situaci zvládnout. Například potřebujete samostatné řádky napájení nebo připojení k Internetu zadaný do těchto počítačů? Kromě toho vezměte v úvahu fyzické zabezpečení těchto počítačů. Kde se nacházejí na počítače, a který potřebuje přístup k nim? Po provedení těchto rozhodnutí, můžete namapovat na počítače logicky na různých domén selhání (viz další krok). Plánování pro produkční clustery infrastruktury je složitější než pro testovacích clusterů.

## <a name="determine-the-number-of-fault-domains-and-upgrade-domains"></a>Určit počet domén selhání a upgradu domény
A [ *doména selhání* (FD)](service-fabric-cluster-resource-manager-cluster-description.md) je fyzická jednotka selhání a přímo souvisí s fyzické infrastruktuře v datových centrech. Domény selhání se skládá z hardwarové součásti (počítače, přepínače, sítí a více), které sdílejí jediný bod selhání. I když neexistuje mapování 1:1 mezi domén selhání a stojany, volně platí, že každý rack lze považovat za domény selhání.

Když zadáte FDs v souboru ClusterConfig.json, můžete název pro každý FD. Service Fabric podporuje hierarchické FDs, takže můžete zrcadlit topologie infrastruktury v nich.  Například následující FDs jsou platné:

* "faultDomain": "fd: / Room1/Rack1/Počítač1"
* "faultDomain": "fd: / FD1"
* "faultDomain": "fd: / Room1/Rack1/PDU1/M1"

*Upgradovací doméně* (UD) je logickou jednotku uzlů. Během Service Fabric orchestrovat upgradů (upgrade aplikace nebo upgrade clusteru) jsou všechny uzly v UD provádět upgrade, zatímco uzly v jiných UDs nadále k obsluze požadavků ukončena. Upgrady firmwaru provést na vašich počítačích nerespektují UDs, takže je nutné je provést některou počítače najednou.

Nejjednodušší způsob, jak vezměte v úvahu tyto koncepty je vzít v úvahu FDs jako jednotky neplánovaném selhání a UDs jako jednotka plánované údržby.

Když zadáte UDs v souboru ClusterConfig.json, můžete název pro každý UD. Například jsou platné tyto názvy:

* "upgradeDomain": "UD0"
* "upgradeDomain": "UD1A"
* "upgradeDomain": "DomainRed"
* "upgradeDomain": "Blue"

Podrobné informace o FDs a UDs, najdete v části [popisující cluster Service Fabric](service-fabric-cluster-resource-manager-cluster-description.md).

Cluster v produkčním prostředí by měly obsahovat alespoň tři FDs, aby byla podporována v produkčním prostředí, pokud máte plnou kontrolu nad údržby a správy uzlů, to znamená, jste zodpovědní za aktualizace a nahrazení počítače. Pro clustery spuštěná v prostředích (to znamená, Amazon Web Services VM instance), kde nemáte plnou kontrolu nad na počítače měli byste mít minimálně pět FDs v clusteru. Každý FD může mít jeden nebo více uzlů. Toto je zabránit problémů způsobených počítač upgrady a aktualizace, které v závislosti na jejich časování může narušovat běh aplikace a služby v clusterech.

## <a name="determine-the-initial-cluster-size"></a>Určit velikost počáteční clusteru

Obecně je počet uzlů v clusteru je určen podle obchodních potřeb, které je, kolik služeb a kontejnery bude spuštěna v clusteru a kolik prostředky potřebujete tolerovat vašich úloh. Produkčních clusterů doporučujeme mít alespoň pět uzlů v clusteru, 5 FDs pokrývání uzlů. Ale jak je popsáno výše, pokud máte plnou kontrolu nad uzly a můžou sahat tři FDs, pak tři uzly vhodné provést úlohu.

Test clustery spuštěná stavová zatížení by měl mít tři uzly, zatímco potřebovat testovacích clusterů pouze spuštěný Bezstavová zatížení jenom jeden uzel. Také je potřeba poznamenat, že pro účely vývoje, můžete mít více než jeden uzel v daném počítači. V produkčním prostředí však Service Fabric podporuje jenom jeden uzel na fyzický nebo virtuální počítač.

## <a name="prepare-the-machines-that-will-serve-as-nodes"></a>Příprava počítačů, které bude sloužit jako uzly

Zde jsou některé doporučené specifikace pro každý počítač, který chcete přidat do clusteru:

* Minimálně 16 GB paměti RAM
* Minimálně 40 GB volného místa na disku
* 4 jádra nebo větší využití procesoru
* Připojení k zabezpečené sítě a sítě pro všechny počítače
* Windows Server 2012 R2 nebo Windows Server 2016
* [Rozhraní .NET framework 4.5.1 nebo novější](https://www.microsoft.com/download/details.aspx?id=40773), úplné instalace
* [Prostředí Windows PowerShell 3.0](https://msdn.microsoft.com/powershell/scripting/setup/installing-windows-powershell)
* [RemoteRegistry služby](https://technet.microsoft.com/library/cc754820) by měla být spuštěná na všechny počítače

Správce clusteru, nasazení a konfigurace clusteru musí mít [oprávnění správce](https://social.technet.microsoft.com/wiki/contents/articles/13436.windows-server-2012-how-to-add-an-account-to-a-local-administrator-group.aspx) na každý z těchto počítačů. Service Fabric nelze nainstalovat na řadič domény.

## <a name="download-the-service-fabric-standalone-package-for-windows-server"></a>Stáhněte si balíček Service Fabric samostatné pro systém Windows Server
[Stáhněte si odkaz – samostatný balíček prostředků infrastruktury služby - Windows Server](http://go.microsoft.com/fwlink/?LinkId=730690) a rozbalte balíček pro nasazení počítač, který není součástí clusteru, nebo do jednoho z počítačů, které budou součástí clusteru.

## <a name="modify-cluster-configuration"></a>Změna konfigurace clusteru
K vytvoření clusteru samostatné budete muset vytvořit samostatnou clusteru konfigurace souboru ClusterConfig.json soubor, který popisuje specifikace clusteru. Konfigurační soubor pro šablony nalezený na tom můžete vycházet z následujícím odkazu. <br>
[Konfigurace clusteru samostatné](https://github.com/Azure-Samples/service-fabric-dotnet-standalone-cluster-configuration/tree/master/Samples)

Informace v části v tomto souboru najdete v tématu [nastavení konfigurace pro samostatný cluster Windows](service-fabric-cluster-manifest.md).

Otevřete ho souboru ClusterConfig.json soubory z balíčku, který jste stáhli a upravit následující nastavení:
| **Nastavení konfigurace** | **Popis** |
| --- | --- |
| **NodeTypes** |Typy uzlů umožňují jednotlivé uzly clusteru do různých skupin. Cluster musí mít alespoň jeden typ uzlu. Všechny uzly ve skupině mají společné následující vlastnosti: <br> **Název** -Toto je název typu uzlu. <br>**Porty koncových bodů** – tyto mají různé název koncového bodu (porty), které jsou spojeny s tímto typem uzlu. Můžete použít libovolné číslo portu, který chcete, tak dlouho, dokud nedošlo ke konfliktu s nic jiného v této manifest a nejsou již používá jiná aplikace, počítače nebo virtuálního počítače se systémem. <br> **Vlastnosti umístění** -tyto popisují vlastnosti pro tento typ uzlu, který používáte jako omezení umístění pro služby systému nebo vaše služby. Tyto vlastnosti jsou páry klíč – hodnota definovaný uživatelem, které poskytují další metadata pro daný uzel. Příklady vlastnosti uzlu by, jestli má uzlu na pevný disk nebo grafickou kartu, počet diskových jednotek v jeho pevný disk, jader a další fyzické vlastnosti. <br> **Kapacity** -kapacity uzlu zadat název a množství určitý prostředek, má k dispozici pro využívání konkrétní uzel. Uzlem může můžete například definovat, že má kapacitu pro metriku názvem "MemoryInMb" a že má 2 048 MB k dispozici ve výchozím nastavení. Tyto kapacity se používají v době běhu k zajištění, že služby, které vyžadují určité množství prostředků umísťují na uzly, které mají tyto prostředky, které jsou k dispozici v požadované objemy.<br>**IsPrimary** – Pokud máte více než jeden typ uzlu definována zkontrolujte, že pouze jedna je nastavená na primární s hodnotou *true*, což je, kde systém služeb spustit. Všechny ostatní typy uzlu musí být nastavena na hodnotu *false* |
| **Uzly** |Toto jsou podrobnosti pro každý z uzlů, které jsou součástí clusteru (typ uzlu, název uzlu, IP adresa, doména selhání a upgradu domény uzlu). Na počítače chcete clusteru má být vytvořena na potřeba zde uvedené s jejich IP adresy. <br> Pokud používáte stejnou IP adresu pro všechny uzly, pak políčko jeden cluster je vytvořený, který můžete použít pro účely testování. Nepoužívejte jeden pole clustery pro nasazení v produkčním prostředí. |

Po konfiguraci clusteru zaznamenala všechna nastavení, které jsou nakonfigurované tak, aby prostředí, může být testována v prostředí clusteru (krok 7).

<a id="environmentsetup"></a>

## <a name="environment-setup"></a>Nastavení prostředí

Pokud správce clusteru konfiguruje samostatný cluster Service Fabric, v prostředí musí být nastavení s těmito kritérii: <br>
1. Oprávnění zabezpečení na úrovni správce pro všechny počítače, které jsou označeny jako uzly v clusteru konfigurační soubor musí mít uživatel vytvoření clusteru.
2. Počítač, ze kterého je vytvořen cluster, jakož i každého počítače uzlu clusteru musí být:
* Odinstalovali Service Fabric SDK
* Mít odinstalaci modulu runtime Service Fabric 
* Povolili službu Brána Windows Firewall (mpssvc)
* Povolili služby Remote Registry (vzdálený registr)
* Soubor povoleno sdílení (SMB)
* Máte nezbytné porty otevřít, založené na porty konfigurace clusteru
* Mít nezbytné porty otevřené pro službu Windows SMB a Remote Registry: 135, 137, 138, 139 a 445
* Máte připojení k síti na sebe navzájem
3. Žádné počítače uzlu clusteru musí být řadič domény.
4. Pokud je cluster k nasazení clusteru s podporou zabezpečení, ověřte potřeby zabezpečení, které jsou požadavky umístěte a jsou správně nakonfigurované pro konfiguraci.
5. Pokud počítače clusteru nejsou přístupné z Internetu, nastavte v konfiguraci clusteru následující:
* Zakázat telemetrická data: v části *vlastnosti* nastavit *"enableTelemetry": false*
* Zakázat automatické stahování verze Fabric & oznámení, že aktuální verze clusteru se blíží konec podpory: v části *vlastnosti* nastavit *"fabricClusterAutoupgradeEnabled": false*
* Případně, pokud je omezen na uvedené prázdné domény přístup k síti internet, domén níže jsou požadovány pro automatický upgrade: go.microsoft.com download.microsoft.com

6. Nastavte příslušné Service Fabric antivirový vyloučení:

| **Antivirový Vyloučené adresáře** |
| --- |
| Program Files\Microsoft Service Fabric |
| Proměnná FabricDataRoot (z konfigurace clusteru) |
| Adresáři FabricLogRoot (z konfigurace clusteru) |

| **Antivirový vyloučené procesy** |
| --- |
| Fabric.exe |
| FabricHost.exe |
| FabricInstallerService.exe |
| FabricSetup.exe |
| FabricDeployer.exe |
| ImageBuilder.exe |
| FabricGateway.exe |
| FabricDCA.exe |
| FabricFAS.exe |
| FabricUOS.exe |
| FabricRM.exe |
| FileStoreService.exe |

## <a name="validate-environment-using-testconfiguration-script"></a>Ověření pomocí skriptu TestConfiguration prostředí
Skript TestConfiguration.ps1 naleznete v samostatné balíčku. To slouží jako Analyzátor osvědčených postupů k ověření některé z výše uvedená kritéria a má být použit jako kontrolu správností k ověření, zda cluster lze nasadit v daném prostředí. Pokud je jakákoli chyba, najdete v seznamu v části [nastavení prostředí](service-fabric-cluster-standalone-deployment-preparation.md) pro řešení potíží. 

Tento skript můžete spustit na jakýkoli počítač, který má práva správce pro všechny počítače, které jsou označeny jako uzly v souboru konfigurace clusteru. Na počítač, který tento skript je spuštěn na nemá být součástí clusteru.

```powershell
PS C:\temp\Microsoft.Azure.ServiceFabric.WindowsServer> .\TestConfiguration.ps1 -ClusterConfigFilePath .\ClusterConfig.Unsecure.DevCluster.json
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

Tento modul testování konfigurace aktuálně neověřuje konfigurace zabezpečení, to je nutné provést nezávisle.  

> [!NOTE]
> Průběžně vydáváme zlepšení robustnější, aby tento modul, pokud se nestane poškozené nebo chybí, které budete mít dojem, není aktuálně zachytila TestConfiguration, dejte nám vědět prostřednictvím našich [podporují kanály](https://docs.microsoft.com/azure/service-fabric/service-fabric-support).   
> 
> 

## <a name="next-steps"></a>Další postup
* [Vytvoření clusteru s podporou samostatné systémem Windows Server](service-fabric-cluster-creation-for-windows-server.md)
