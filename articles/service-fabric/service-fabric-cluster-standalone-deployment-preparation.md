---
title: Příprava nasazení samostatného clusteru
description: Dokumentace týkající se přípravy prostředí a vytvoření konfigurace clusteru, které mají být považovány před nasazením clusteru určeného pro zpracování produkčního zatížení.
author: dkkapur
ms.topic: conceptual
ms.date: 9/11/2018
ms.author: dekapur
ms.openlocfilehash: 6a00b7d1b72d594c08021982b2448de6275414c8
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "75610059"
---
# <a name="plan-and-prepare-your-service-fabric-standalone-cluster-deployment"></a>Plánování a příprava nasazení samostatného clusteru Service Fabric

<a id="preparemachines"></a>Před vytvořením clusteru proveďte následující kroky.

## <a name="plan-your-cluster-infrastructure"></a>Plánování infrastruktury clusteru
Chystáte se vytvořit cluster Service Fabric na počítačích, které "vlastníte", takže se můžete rozhodnout, jaké druhy selhání chcete clusteru přežít. Potřebujete například samostatné elektrické vedení nebo připojení k Internetu dodávané do těchto strojů? Kromě toho zvažte fyzické zabezpečení těchto strojů. Kde se stroje nacházejí a kdo k nim potřebuje přístup? Po těchto rozhodnutích můžete logicky mapovat počítače na různé domény selhání (viz další krok). Plánování infrastruktury pro produkční clustery je více než pro testovací clustery.

## <a name="determine-the-number-of-fault-domains-and-upgrade-domains"></a>Určení počtu domén selhání a upgradovacích domén
[ *Doména selhání* (FD)](service-fabric-cluster-resource-manager-cluster-description.md) je fyzická jednotka selhání a přímo souvisí s fyzickou infrastrukturou v datových centrech. Doména selhání se skládá z hardwarových součástí (počítače, přepínače, sítě a další), které sdílejí jeden bod selhání. Přestože neexistuje žádné mapování 1:1 mezi doménami selhání a racky, volně řečeno, každý rack může být považován za doménu selhání.

Když zadáte FDs v ClusterConfig.json, můžete zvolit název pro každý FD. Service Fabric podporuje hierarchické FDs, takže můžete odrážet topologii infrastruktury v nich.  Například následující fds jsou platné:

* "faultDomain": "fd:/Room1/Rack1/Machine1"
* "faultDomain": "fd:/FD1"
* "faultDomain": "fd:/Room1/Rack1/PDU1/M1"

*Inovace domény* (UD) je logická jednotka uzlů. Během upgradu řízeného service fabric (upgrade aplikace nebo upgrade clusteru) jsou všechny uzly v ud odebrány k provedení upgradu, zatímco uzly v jiných udů zůstávají k dispozici pro obsluhu požadavků. Upgrady firmwaru, které provádíte na svých počítačích, nerespektují udů, takže je musíte provádět po jednom počítači.

Nejjednodušší způsob, jak přemýšlet o těchto konceptech, je považovat fds jako jednotku neplánovaného selhání a UDs jako jednotku plánované údržby.

Když zadáte uds v ClusterConfig.json, můžete zvolit název pro každý UD. Například následující názvy jsou platné:

* "upgradeDomain": "UD0"
* "upgradeDomain": "UD1A"
* "upgradeDomain": "DomainRed"
* "upgradeDomain": "Modrá"

Podrobnější informace o fds a UDs, naleznete [v tématu popisující service fabric clusteru](service-fabric-cluster-resource-manager-cluster-description.md).

Cluster v produkčním prostředí by měl span alespoň tři FDs, aby byly podporovány v produkčním prostředí, pokud máte plnou kontrolu nad údržbou a správou uzlů, to znamená, že jste zodpovědní za aktualizaci a výměnu počítačů. Pro clustery spuštěné v prostředích (to znamená, Amazon Web Services VM instance), kde nemáte úplnou kontrolu nad počítači, měli byste mít minimálně pět FDs v clusteru. Každý FD může mít jeden nebo více uzlů. To to je zabránit problémům způsobeným upgrady a aktualizace počítače, které v závislosti na jejich načasování, může narušit spouštění aplikací a služeb v clusterech.

## <a name="determine-the-initial-cluster-size"></a>Určení počáteční velikosti clusteru

Obecně platí, že počet uzlů ve vašem clusteru je určen na základě vašich obchodních potřeb, to znamená, kolik služeb a kontejnerů bude spuštěno v clusteru a kolik prostředků potřebujete k udržení vašich úloh. Pro produkční clustery doporučujeme mít v clusteru alespoň pět uzlů, které pokrývají 5 fds. Nicméně, jak je popsáno výše, pokud máte plnou kontrolu nad uzly a může span tři FDs, pak tři uzly by měl také dělat práci.

Testovací clustery se stavovými úlohami by měly mít tři uzly, zatímco testovací clustery spouštějí pouze bezstavové úlohy, které potřebují pouze jeden uzel. Je třeba také poznamenat, že pro účely vývoje můžete mít více než jeden uzel na daném počítači. V produkčním prostředí však Service Fabric podporuje pouze jeden uzel na fyzický nebo virtuální počítač.

## <a name="prepare-the-machines-that-will-serve-as-nodes"></a>Příprava strojů, které budou sloužit jako uzly

Zde jsou některé doporučené specifikace pro každý počítač, který chcete přidat do clusteru:

* Minimálně 16 GB paměti RAM
* Minimálně 40 GB volného místa na disku
* Procesor s výkonem 4 jádra nebo vyšší
* Připojení k zabezpečené síti nebo sítím pro všechny počítače
* Operační systém Windows Server nainstalován (platné verze: 2012 R2, 2016, 1709 nebo 1803). Service Fabric verze 6.4.654.9590 a novější také podporuje Server 2019 a 1809.
* [Rozhraní .NET Framework 4.5.1 nebo vyšší](https://www.microsoft.com/download/details.aspx?id=40773), úplná instalace
* [Windows PowerShell 3.0](https://msdn.microsoft.com/powershell/scripting/install/installing-windows-powershell)
* [Služba RemoteRegistry](https://technet.microsoft.com/library/cc754820) by měla být spuštěna na všech počítačích.
* Instalační jednotka Service Fabric musí být systém souborů NTFS.

Správce clusteru, který nasadí a konfiguruje cluster, musí mít [oprávnění správce](https://social.technet.microsoft.com/wiki/contents/articles/13436.windows-server-2012-how-to-add-an-account-to-a-local-administrator-group.aspx) v každém počítači. Service Fabric nelze nainstalovat na řadič domény.

## <a name="download-the-service-fabric-standalone-package-for-windows-server"></a>Stažení samostatného balíčku Service Fabric pro Windows Server
[Odkaz ke stažení – samostatný balíček Service Fabric – Windows Server](https://go.microsoft.com/fwlink/?LinkId=730690) a rozbalte balíček, a to buď do počítače nasazení, který není součástí clusteru, nebo do jednoho z počítačů, které budou součástí clusteru.

## <a name="modify-cluster-configuration"></a>Změna konfigurace clusteru
Chcete-li vytvořit samostatný cluster, musíte vytvořit samostatný konfigurační soubor ClusterConfig.json, který popisuje specifikaci clusteru. Konfigurační soubor můžete založit na šablonách, které se nacházejí na níže uvedeném odkazu. <br>
[Konfigurace samostatného clusteru](https://github.com/Azure-Samples/service-fabric-dotnet-standalone-cluster-configuration/tree/master/Samples)

Podrobnosti o částech v tomto souboru naleznete v [tématu Nastavení konfigurace samostatného clusteru Windows](service-fabric-cluster-manifest.md).

Otevřete jeden ze souborů ClusterConfig.json z staženého balíčku a upravte následující nastavení:

| **Nastavení konfigurace** | **Popis** |
| --- | --- |
| **Typy uzlů** |Typy uzlů umožňují oddělit uzly clusteru do různých skupin. Cluster musí mít alespoň jeden NodeType. Všechny uzly ve skupině mají následující společné charakteristiky: <br> **Název** - Toto je název typu uzlu. <br>**Porty koncového bodu** – jedná se o různé pojmenované koncové body (porty), které jsou přidruženy k tomuto typu uzlu. Můžete použít libovolné číslo portu, které si přejete, pokud nejsou v konfliktu s ničím jiným v tomto manifestu a nejsou již používány jinou aplikací spuštěnou v počítači nebo virtuálním počítači. <br> **Vlastnosti umístění** – popisují vlastnosti pro tento typ uzlu, které používáte jako omezení umístění pro systémové služby nebo služby. Tyto vlastnosti jsou uživatelem definované dvojice klíč/hodnota, které poskytují další meta data pro daný uzel. Příklady vlastností uzlu by bylo, zda uzel má pevný disk nebo grafickou kartu, počet vřeten v jeho pevném disku, jádra a další fyzikální vlastnosti. <br> **Kapacity** - kapacity uzlů definují název a množství konkrétního prostředku, který má konkrétní uzel k dispozici pro spotřebu. Například uzel může definovat, že má kapacitu pro metriku s názvem "MemoryInMb" a že má 2048 MB k dispozici ve výchozím nastavení. Tyto kapacity se používají za běhu k zajištění, že služby, které vyžadují určité množství prostředků jsou umístěny na uzly, které mají tyto prostředky k dispozici v požadovaných částkách.<br>**IsPrimary** - Pokud máte více než jeden NodeType definována zajistit, že pouze jeden je nastaven na primární s hodnotou *true*, což je místo, kde jsou spuštěny systémové služby. Všechny ostatní typy uzlů by měly být nastaveny na hodnotu *false.* |
| **Uzly** |Jedná se o podrobnosti pro každý z uzlů, které jsou součástí clusteru (typ uzlu, název uzlu, IP adresa, doména selhání a upgradovací doména uzlu). Počítače, na kterých má být cluster vytvořen, musí být zde uvedeny s jejich IP adresami. <br> Pokud používáte stejnou adresu IP pro všechny uzly, vytvoří se cluster s jedním polem, který můžete použít pro účely testování. Nepoužívejte one-box clustery pro nasazení produkčních úloh. |

Poté, co konfigurace clusteru má všechna nastavení nakonfigurována pro prostředí, může být testována v prostředí clusteru (krok 7).

<a id="environmentsetup"></a>

## <a name="environment-setup"></a>Nastavení prostředí

Pokud správce clusteru konfiguruje samostatný cluster Service Fabric, je třeba prostředí nastavit s následujícími kritérii: <br>
1. Uživatel vytvářející cluster by měl mít oprávnění zabezpečení na úrovni správce pro všechny počítače, které jsou uvedeny jako uzly v konfiguračním souboru clusteru.
2. Počítač, ze kterého je cluster vytvořen, stejně jako každý počítač uzlu clusteru musí:
   * Nechat odinstalovat sadu Service Fabric SDK
   * Nechat odinstalovat runtime service fabric
   * Byla povolena služba Brána Firewall systému Windows (mpssvc)
   * Byla povolena služba Vzdálená registrace (vzdálený registr)
   * Mít povoleno sdílení souborů (SMB)
   * Otevření potřebných portů na základě konfiguračních portů clusteru
   * Otevření potřebných portů pro službu Windows SMB a Vzdáleného registru: 135, 137, 138, 139 a 445
   * Mít k sobě navzájem připojení k síti
3. Žádný z počítačů uzlů clusteru by neměl být řadičem domény.
4. Pokud je cluster, který má být nasazen, zabezpečený cluster, ověřte, zda jsou na místě nezbytné požadavky zabezpečení, a jsou správně nakonfigurovány podle konfigurace.
5. Pokud počítače clusteru nejsou přístupné internetu, nastavte v konfiguraci clusteru následující:
   * Zakázat telemetrii: Pod *vlastnostmi* nastavit *"enableTelemetry": false*
   * Zakázat automatické stahování verze prostředků fabric & oznámení, že aktuální verze clusteru se blíží ke konci podpory: V *části vlastnosti* nastavit *"fabricClusterAutoupgradeEnabled": false*
   * Pokud je přístup k internetu v síti omezen na domény uvedené na seznamu povolených, jsou pro automatický upgrade vyžadovány níže uvedené domény: go.microsoft.com download.microsoft.com

6. Nastavte příslušná vyloučení antivirového programu Service Fabric:

| **Antivirové vyloučené adresáře** |
| --- |
| Soubory programů\Aplikace Microsoft Service Fabric |
| FabricDataRoot (z konfigurace clusteru) |
| FabricLogRoot (z konfigurace clusteru) |

| **Procesy vyloučené antivirovým programem** |
| --- |
| Fabric.exe |
| FabricHost.exe |
| FabricInstallerService.exe |
| Soubor FabricSetup.exe |
| FabricDeployer.exe |
| ImageBuilder.exe |
| FabricGateway.exe |
| FabricDCA.exe |
| FabricFAS.exe |
| FabricUOS.exe |
| FabricRM.exe |
| Soubor StoreService.exe |

## <a name="validate-environment-using-testconfiguration-script"></a>Ověřit prostředí pomocí skriptu TestConfiguration
Skript TestConfiguration.ps1 naleznete v samostatném balíčku. Používá se jako analyzátor osvědčených postupů k ověření některých výše uvedených kritérií a měl by být použit jako kontrola příčetnosti k ověření, zda lze cluster nasadit v daném prostředí. Pokud dojde k nějaké chybě, naleznete v seznamu v [části Nastavení prostředí](service-fabric-cluster-standalone-deployment-preparation.md) pro řešení potíží.

Tento skript lze spustit v libovolném počítači, který má přístup správce ke všem počítačům, které jsou uvedeny jako uzly v konfiguračním souboru clusteru. Počítač, na který je tento skript spuštěn, nemusí být součástí clusteru.

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

V současné době tento modul testování konfigurace neověřuje konfiguraci zabezpečení, takže to musí být provedeno nezávisle.

> [!NOTE]
> Neustále vylepšujeme, aby byl tento modul robustnější, takže pokud existuje vadný nebo chybějící případ, o kterém se domníváte, že není v současné době zachycen TestConfiguration, dejte nám prosím vědět prostřednictvím našich [kanálů podpory](https://docs.microsoft.com/azure/service-fabric/service-fabric-support).
>
>

## <a name="next-steps"></a>Další kroky
* [Vytvoření samostatného clusteru běžícího na Windows Serveru](service-fabric-cluster-creation-for-windows-server.md)
