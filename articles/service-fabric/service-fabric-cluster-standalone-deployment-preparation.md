---
title: Azure Service Fabric samostatného clusteru nasazení přípravy | Dokumentace Microsoftu
description: Dokumentace týkající se přípravy prostředí a vytváření konfigurace clusteru, aby bylo považováno za před nasazením clusteru určená pro zpracování a produkční úlohy.
services: service-fabric
documentationcenter: .net
author: dkkapur
manager: timlt
editor: ''
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 9/11/2018
ms.author: dekapur
ms.openlocfilehash: 0b6fae59fbe0fa86cb16b176eb1df47e031d04f1
ms.sourcegitcommit: eb9dd01614b8e95ebc06139c72fa563b25dc6d13
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/12/2018
ms.locfileid: "53317182"
---
# <a name="plan-and-prepare-your-service-fabric-standalone-cluster-deployment"></a>Plánování a příprava vašeho nasazení clusteru Service Fabric Standalone

<a id="preparemachines"></a>Před vytvořením clusteru, proveďte následující kroky.

## <a name="plan-your-cluster-infrastructure"></a>Plánování infrastruktury clusteru
Chystáte se vytvořit cluster Service Fabric na počítačích "vlastníte", abyste se mohli rozhodnout, jaké druhy chyb, které má cluster k překonání. Například potřebujete samostatné řádky napájení nebo zadaný k těmto počítačům připojení k Internetu? Kromě toho zvážit fyzické zabezpečení těchto počítačů. Kde se počítače nacházejí a kdo potřebuje přístup k nim? Po těchto rozhodnutí, můžete namapovat na počítače logicky do různých domén selhání (viz dál). Plánování pro produkční clustery infrastruktury je složitější než pro testovací clustery.

## <a name="determine-the-number-of-fault-domains-and-upgrade-domains"></a>Určení počtu domén selhání a upgradovací domény
A [ *doména selhání* (FD)](service-fabric-cluster-resource-manager-cluster-description.md) je fyzická jednotka, selhání a souvisí s fyzickou infrastrukturou v datových centrech. Doména selhání se skládá z hardwarové součásti (počítače, přepínače, sítě a další), které mají společný bod selhání. Ačkoli neexistuje žádné mapování 1:1 mezi doménami selhání a stojany, volně vzato každý rack lze považovat za doménu selhání.

Když zadáte ClusterConfig.json doménami selhání, můžete název pro každý FD. Service Fabric podporuje hierarchické doménami selhání, takže topologii vaší infrastruktury v nich můžete sledovat.  Například následující doménami selhání jsou platné:

* "faultDomain": "fd: / Room1/Rack1/Počítač1"
* "faultDomain": "fd: / FD1"
* "faultDomain": "fd: / Room1/Rack1/PDU1/M1"

*Upgradovací doména* (UD) je logická jednotka z uzlů. Během Service Fabric orchestrované upgradů (upgrade aplikace nebo upgradu clusteru) jsou všechny uzly v UD odstavit k provedení upgradu nadále k dispozici pro požadavků, uzly v jiných aktualizačními doménami. Upgrady firmwaru provádět na počítačích, takže je nutné je provést některou nerespektují aktualizačními doménami, počítači současně.

Nejjednodušší způsob, jak přemýšlejte o těchto konceptech je doménami selhání je třeba brát jako jednotka neplánovaných selhání a aktualizačními doménami jako jednotka plánované údržby.

Když zadáte aktualizačními doménami v ClusterConfig.json, můžete název pro každý UD. Platné jsou například následující názvy:

* "upgradeDomain": "UD0"
* "upgradeDomain": "UD1A"
* "upgradeDomain": "DomainRed"
* "upgradeDomain": "Blue"

Podrobnější informace o doménami selhání a aktualizačními doménami, najdete v článku [popisující cluster Service Fabric](service-fabric-cluster-resource-manager-cluster-description.md).

Cluster v produkčním prostředí by měl zahrnovat aspoň tři doménami selhání, aby byla podporována v produkčním prostředí, pokud máte plnou kontrolu nad údržbu a správu uzlů, to znamená, budete muset pro aktualizaci nebo nahrazení počítače. Pro clustery spuštěné v prostředí (to znamená, instance virtuálního počítače Amazon Web Services), kde nemáte plnou kontrolu nad na počítače měli byste mít minimálně pěti doménami selhání ve vašem clusteru. Každý FD může mít jeden nebo více uzlů. Tím se brání potíže způsobené službou machine upgrady a aktualizace, které v závislosti na jejich načasování, může vést k potížím s běžící aplikací a služeb v clusterech.

## <a name="determine-the-initial-cluster-size"></a>Určit velikost počáteční clusteru

Obecně platí počet uzlů v clusteru je určen podle potřeb firmy, které je, kolik služby a kontejnery poběží v clusteru a kolik prostředků je třeba udržovat vaše úlohy. Pro produkční clustery doporučujeme, abyste s alespoň pět uzlů v clusteru, pokrývání uzlů 5 doménami selhání. Ale jak bylo popsáno výše, pokud budete mít plnou kontrolu nad uzly, které můžou využívat tři doménami selhání, pak tři uzly by měl také provést úlohy.

Testovací clustery se systémem zatížení se sdílením stavu by měla mít tři uzly, že testovacích clusterů pouze spuštěná Bezstavová zatížení jenom jeden uzel. Také je potřeba poznamenat, že pro účely vývoje můžete mít více než jeden uzel na daném počítači. V produkčním prostředí ale Service Fabric podporuje pouze jeden uzel na fyzický nebo virtuální počítač.

## <a name="prepare-the-machines-that-will-serve-as-nodes"></a>Příprava počítačů, které bude sloužit jako uzly

Tady jsou některé doporučené specifikace pro každý počítač, který chcete přidat do clusteru:

* Minimálně 16 GB paměti RAM
* Minimálně 40 GB volného místa na disku
* 4 jádra nebo větší využití procesoru
* Připojení k zabezpečené sítě nebo sítě pro všechny počítače
* Nainstalovaný operační systém Windows Server (platných verzí: 2012 R2, 2016, 1709 nebo 1803)
* [Rozhraní .NET framework 4.5.1 nebo novější](https://www.microsoft.com/download/details.aspx?id=40773), úplné instalace
* [Prostředí Windows PowerShell 3.0](https://msdn.microsoft.com/powershell/scripting/setup/installing-windows-powershell)
* [RemoteRegistry služby](https://technet.microsoft.com/library/cc754820) by měla být spuštěná na všech počítačích

Správce clusteru, nasazení a konfigurace clusteru, musí mít [oprávnění správce](https://social.technet.microsoft.com/wiki/contents/articles/13436.windows-server-2012-how-to-add-an-account-to-a-local-administrator-group.aspx) na všech počítačích. Service Fabric nelze nainstalovat na řadič domény.

## <a name="download-the-service-fabric-standalone-package-for-windows-server"></a>Stáhněte si samostatného balíčku Service Fabric pro Windows Server
[Stáhněte si odkaz - samostatného balíčku Service Fabric – Windows Server](https://go.microsoft.com/fwlink/?LinkId=730690) a rozbalte balíček k počítači nasazení, který není součástí clusteru nebo do jednoho počítače, které budou součástí clusteru.

## <a name="modify-cluster-configuration"></a>Upravit konfigurace clusteru
K vytvoření samostatného clusteru je nutné vytvořit samostatný cluster konfigurace ClusterConfig.json soubor, který popisuje specifikace clusteru. Můžete založit na šablony v konfiguračním souboru odkaz níže. <br>
[Konfigurace samostatného clusteru](https://github.com/Azure-Samples/service-fabric-dotnet-standalone-cluster-configuration/tree/master/Samples)

Podrobnosti v části v tomto souboru najdete v tématu [nastavení konfigurace pro samostatný cluster Windows](service-fabric-cluster-manifest.md).

Otevřete jednu z ClusterConfig.json soubory z balíčku, který jste stáhli a upravte následující nastavení:

| **Nastavení konfigurace** | **Popis** |
| --- | --- |
| **NodeTypes** |Typy uzlů umožňují rozdělit uzly clusteru do různých skupin. Cluster musí mít aspoň jeden typ NodeType. Všechny uzly ve skupině mají následující běžné vlastnosti: <br> **Název** – Toto je název typu uzlu. <br>**Porty koncových bodů** – tyto pojmenovány různé koncové body (porty), které jsou spojeny s tímto typem uzlu. Můžete použít libovolné číslo portu, který chcete, za předpokladu, že nejsou v konfliktu se nic jiného v manifestu a nejsou již používá jiná aplikace, na počítači nebo virtuálním počítači běží. <br> **Vlastnosti umístění** -popisují tyto vlastnosti pro tento typ uzlu, který používáte jako omezení umístění pro služby systému nebo služby. Tyto vlastnosti jsou páry klíč/hodnota definovaný uživatelem, které poskytují další metadata pro daný uzel. Příkladem vlastnosti uzlu by se určuje, zda má uzel pevný disk nebo grafickou kartu, počet diskových jednotek v jeho pevného disku, počet jader a další fyzické vlastnosti. <br> **Kapacity** -kapacity uzlů zadat název a množství určitý prostředek, že konkrétní uzel k dispozici pro použití. Uzlem může například definovat, že má kapacitu pro metriku s názvem "MemoryInMb" a že má 2 048 MB k dispozici ve výchozím nastavení. Tyto kapacity se používají v době běhu k zajištění, že služby, které vyžadují určité množství prostředků umísťují na uzly, které byly jejich prostředky, které jsou k dispozici požadované množství.<br>**IsPrimary** – Pokud máte více než jeden NodeType definované Ujistěte se, že pouze jeden je nastaven na primární hodnotou *true*, což je, kde systém služeb spustit. Všechny ostatní typy uzlů musí být nastaveno na hodnotu *false* |
| **Uzly** |Toto jsou podrobnosti pro každý z uzlů, které jsou součástí clusteru (typ uzlu, název uzlu, IP adresa, doména selhání a upgradovací doména uzlu). Na počítačích, které má cluster má být vytvořena na potřeba zde uvedené pomocí jejich IP adresy. <br> Pokud používáte stejnou IP adresu pro všechny uzly, pak políčko jeden cluster je vytvořený, který můžete použít pro účely testování. Nepoužívejte One-box clustery pro nasazení úlohy v produkčním prostředí. |

Po konfiguraci clusteru byla všechna nastavení nakonfigurovaná tak, aby prostředí, můžete testovat v prostředí clusteru (krok 7).

<a id="environmentsetup"></a>

## <a name="environment-setup"></a>Nastavení prostředí

Při konfiguraci Správce clusteru samostatného clusteru Service Fabric, prostředí, musí být nastavení s následujícími kritérii: <br>
1. Uživatel vytvářející cluster by měl mít oprávnění zabezpečení na úrovni správce na všech počítačích, které jsou uvedeny jako uzly v clusteru konfigurační soubor.
2. Počítač, ze kterého se cluster vytvoří, jakož i každého počítače uzlu clusteru musí:
   * Jste odinstalovali sadu Service Fabric SDK
   * Mají odinstalovat modul runtime Service Fabric 
   * Jste povolili službu Windows Firewall (mpssvc)
   * Povolíte služby Remote Registry (vzdálený registr)
   * Soubor, který povoleno sdílení (SMB)
   * Mají potřebné porty otevřít, založené na portech konfigurace clusteru
   * Mít nezbytné porty otevřené pro Windows, SMB a Remote Registry service: 135, 137, 138, 139 a 445
   * Mít síťové připojení mezi sebou
3. Žádná z počítače uzlu clusteru by měl být řadičem domény.
4. Pokud je cluster k nasazení zabezpečeného clusteru, ověřte nezbytné požadavky jsou v umístění a správně nakonfigurované pro konfiguraci zabezpečení.
5. Pokud cluster počítače nejsou přístupné z Internetu, nastavte v konfiguraci clusteru následující:
   * Zakážete telemetrická data: V části *vlastnosti* nastavit *"enableTelemetry": false*
   * Zakážete automatické stahování verze prostředků infrastruktury a oznámení, že aktuální verze clusteru se blíží konec podpory: V části *vlastnosti* nastavit *"fabricClusterAutoupgradeEnabled": false*
   * Případně, pokud přístup k síti internet, je omezený na uvedené prázdné domény, domény níže jsou požadovány pro automatický upgrade: go.microsoft.com download.microsoft.com

6. Nastavit příslušné výjimky antivirové ochrany v Service Fabric:

| **Antivirové Vyloučené adresáře** |
| --- |
| Program Files\Microsoft Service Fabric |
| FabricDataRoot (od konfigurace clusteru) |
| FabricLogRoot (od konfigurace clusteru) |

| **Antivirové vyloučené procesy** |
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

## <a name="validate-environment-using-testconfiguration-script"></a>Ověření prostředí pomocí TestConfiguration skriptu
TestConfiguration.ps1 skript nachází v samostatném balíčku. Slouží jako Analyzátor osvědčených postupů k ověření některé z výše uvedených kritérií a by měla sloužit jako kontrolu správnosti k ověření, jestli je možné cluster nasadit do daného prostředí. Pokud je jakékoli neúspěchy, podívejte se na seznam v části [prostředí](service-fabric-cluster-standalone-deployment-preparation.md) pro řešení potíží. 

Tento skript můžete spustit na jakýkoli počítač, který má přístup správce pro všechny počítače, které jsou označeny jako uzly v clusteru konfigurační soubor. Počítače, ve kterém se skript spouští na nemusí být součástí clusteru.

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

Tento modul testování konfigurace aktuálně neověřuje konfiguraci zabezpečení, tak to je třeba provést nezávisle na sobě.  

> [!NOTE]
> Průběžně provádíme vylepšení robustnější, aby tento modul, pokud je poškozené nebo chybějící případu, což si myslíte, že není aktuálně zachycuje se prostřednictvím TestConfiguration, dejte nám vědět prostřednictvím našich [podporují kanály](https://docs.microsoft.com/azure/service-fabric/service-fabric-support).   
> 
> 

## <a name="next-steps"></a>Další postup
* [Vytvoření samostatného clusteru běžící na Windows serveru](service-fabric-cluster-creation-for-windows-server.md)
