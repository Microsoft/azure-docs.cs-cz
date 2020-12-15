---
title: Příprava nasazení samostatného clusteru
description: Dokumentace týkající se přípravy prostředí a vytvoření konfigurace clusteru, která se má zvážit před nasazením clusteru určeného pro zpracování produkčního zatížení.
ms.topic: conceptual
ms.date: 9/11/2018
ms.openlocfilehash: 277c7e047815b3b4171f7cced203ecbe5b68b155
ms.sourcegitcommit: 63d0621404375d4ac64055f1df4177dfad3d6de6
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/15/2020
ms.locfileid: "97509168"
---
# <a name="plan-and-prepare-your-service-fabric-standalone-cluster-deployment"></a>Plánování a příprava nasazení samostatného clusteru Service Fabric

<a id="preparemachines"></a>Před vytvořením clusteru proveďte následující kroky.

## <a name="plan-your-cluster-infrastructure"></a>Plánování infrastruktury clusteru
Chystáte se vytvořit cluster Service Fabric v počítačích, které vlastníte, abyste se mohli rozhodnout, jaké druhy selhání má cluster zachován. Například potřebujete samostatné napájení nebo připojení k Internetu, které jsou k těmto počítačům dodávány? Zvažte také fyzické zabezpečení těchto počítačů. Kde se nacházejí počítače a kdo k nim potřebuje přístup? Po provedení těchto rozhodnutí můžete logicky namapovat počítače na různé domény selhání (viz další krok). Plánování infrastruktury pro produkční clustery je více zapojeno než u testovacích clusterů.

## <a name="determine-the-number-of-fault-domains-and-upgrade-domains"></a>Určení počtu domén selhání a upgradovacích domén
[ *Doména* selhání (FD)](service-fabric-cluster-resource-manager-cluster-description.md) je fyzická jednotka selhání a přímo se vztahuje k fyzické infrastruktuře v datových centrech. Doména selhání se skládá z hardwarových komponent (počítačů, přepínačů, sítí a dalších), které sdílejí jediný bod selhání. Přestože neexistuje žádné mapování 1:1 mezi doménami selhání a racky, volně řečeno, každý stojan se může považovat za doménu selhání.

Když zadáte doménami selhání v ClusterConfig.jsna, můžete zvolit název pro každé FD. Service Fabric podporuje hierarchické doménami selhání, takže můžete v nich odrážet topologii infrastruktury.  Například následující doménami selhání jsou platné:

* "faultDomain": "FD:/Room1/Rack1/Machine1"
* "faultDomain": "FD:/FD1"
* "faultDomain": "FD:/Room1/Rack1/PDU1/M1"

*Upgradovací doména* (UD) je logická jednotka uzlů. Během Service Fabric orchestrace upgradu (buď upgrade aplikace nebo upgrade clusteru), jsou všechny uzly v UD vyřízeny k provedení upgradu, zatímco uzly v jiné UDs zůstávají k dispozici pro poskytování požadavků. Upgrady firmwaru, které na vašich počítačích provedete, se neshodují, takže je třeba provést jeden počítač po jednom.

Nejjednodušší způsob, jak se zamyslet na tyto koncepce, je zvážit doménami selhání jako jednotku neplánovaných selhání a UDs jako jednotka plánované údržby.

Když zadáte UDs v ClusterConfig.jszapnuto, můžete zvolit název pro každý UD. Platné jsou například následující názvy:

* "upgradeDomain": "UD0"
* "upgradeDomain": "UD1A"
* "upgradeDomain": "DomainRed"
* "upgradeDomain": "Blue"

Podrobnější informace o doménami selhání a UDs najdete v tématu [popisujícím cluster Service Fabric](service-fabric-cluster-resource-manager-cluster-description.md).

Cluster v produkčním prostředí by měl mít alespoň tři doménami selhání, aby se podporoval v produkčním prostředí, pokud máte plnou kontrolu nad údržbou a správou uzlů, tedy zodpovídáte za aktualizaci a nahrazování počítačů. Pro clustery spuštěné v prostředích (tj. Amazon Web Services instancích virtuálních počítačů), ve kterých nemáte úplnou kontrolu nad počítači, byste měli mít ve svém clusteru minimálně pět doménami selhání. Každé FD může mít jeden nebo více uzlů. K tomu je potřeba zabránit problémům způsobeným upgrady počítačů a aktualizacemi, které v závislosti na jejich načasování můžou kolidovat s běžícími aplikacemi a službami v clusterech.

## <a name="determine-the-initial-cluster-size"></a>Určení počáteční velikosti clusteru

Obecně platí, že počet uzlů v clusteru je založený na vašich obchodních potřebách, to znamená, kolik služeb a kontejnerů bude spuštěno v clusteru a kolik prostředků potřebujete k udržení svých úloh. V produkčních clusterech doporučujeme mít v clusteru aspoň pět uzlů, což pokrývá 5 doménami selhání. Jak je popsáno výše, pokud máte plnou kontrolu nad uzly a může zasahovat do tří doménami selhání, pak by tato úloha měla také tři uzly.

Testovací clustery, na kterých běží stavová zatížení, by měly mít tři uzly, zatímco testovací clustery jenom spouštějící bezstavové úlohy potřebují jenom jeden uzel. Měli byste si také všimnout, že pro účely vývoje můžete mít v daném počítači více než jeden uzel. V produkčním prostředí ale Service Fabric podporuje jenom jeden uzel na fyzický nebo virtuální počítač.

## <a name="prepare-the-machines-that-will-serve-as-nodes"></a>Příprava počítačů, které budou sloužit jako uzly

Tady jsou doporučené specifikace pro počítače ve Service Fabricm clusteru:

* Minimálně 16 GB paměti RAM
* Minimálně 40 GB volného místa na disku
* PROCESOR s 4 jádry nebo vyšším
* Připojení k zabezpečené síti nebo sítím pro všechny počítače
* Nainstalovaný operační systém Windows Server (platné verze: 2012 R2, 2016, 1709 nebo 1803). Verze Service Fabric 6.4.654.9590 a novější také podporuje servery 2019 a 1809.
* Úplná instalace [.NET Framework 4.5.1 nebo vyšší](https://www.microsoft.com/download/details.aspx?id=40773)verze
* [Windows PowerShell 3.0](/powershell/scripting/windows-powershell/install/installing-windows-powershell?view=powershell-7)
* [Služba RemoteRegistry](/previous-versions/windows/it-pro/windows-server-2008-R2-and-2008/cc754820(v=ws.11)) by měla běžet na všech počítačích
* **Instalační jednotka Service Fabric musí být systém souborů NTFS.**
* ***Protokoly výkonu* služeb systému Windows & výstrahy a *protokol událostí systému Windows* musí [být povoleny](/previous-versions/windows/it-pro/windows-server-2008-r2-and-2008/cc755249(v=ws.11))**.

> [!IMPORTANT]
> Cluster, který nasazuje a konfiguruje správce clusteru, musí mít na každém z těchto počítačů [oprávnění správce](https://social.technet.microsoft.com/wiki/contents/articles/13436.windows-server-2012-how-to-add-an-account-to-a-local-administrator-group.aspx) . Service Fabric nelze nainstalovat na řadič domény.

## <a name="download-the-service-fabric-standalone-package-for-windows-server"></a>Stažení samostatného balíčku Service Fabric pro Windows Server
[Stažení odkazu-Service Fabric samostatný balíček – Windows Server](https://go.microsoft.com/fwlink/?LinkId=730690) a rozbalení balíčku, buď na počítač pro nasazení, který není součástí clusteru, nebo na jeden z počítačů, které budou součástí clusteru.

## <a name="modify-cluster-configuration"></a>Úprava konfigurace clusteru
Pokud chcete vytvořit samostatný cluster, musíte vytvořit samostatnou konfiguraci clusteru ClusterConfig.jsv souboru, která popisuje specifikace clusteru. Konfigurační soubor můžete založit na šablonách, které najdete v odkazu níže. <br>
[Samostatné konfigurace clusteru](https://github.com/Azure-Samples/service-fabric-dotnet-standalone-cluster-configuration/tree/master/Samples)

Podrobnosti o oddílech v tomto souboru najdete v tématu [nastavení konfigurace pro samostatný cluster Windows](service-fabric-cluster-manifest.md).

Otevřete některý z ClusterConfig.jssouborů z balíčku, který jste stáhli, a upravte následující nastavení:

| **Nastavení konfigurace** | **Popis** |
| --- | --- |
| **NodeTypes** |Typy uzlů umožňují oddělit uzly clusteru do různých skupin. Cluster musí mít alespoň jeden uzel NodeType. Všechny uzly ve skupině mají tyto společné charakteristiky: <br> **Název** – jedná se o název typu uzlu. <br>**Porty koncového bodu** – jedná se o různé pojmenované koncové body (porty), které jsou přidružené k tomuto typu uzlu. Můžete použít libovolné číslo portu, které chcete, pokud nekoliduje s jakýmkoli jiným v tomto manifestu a nejsou již používány žádnou jinou aplikací spuštěnou v počítači nebo VIRTUÁLNÍm počítači. <br> **Vlastnosti umístění** – tyto popisují vlastnosti tohoto typu uzlu, které používáte jako omezení umístění pro systémové služby nebo služby. Tyto vlastnosti jsou uživatelsky definované páry klíč/hodnota, které poskytují dodatečné meta data pro daný uzel. Mezi příklady vlastností uzlu patří to, jestli má uzel pevný disk nebo grafickou kartu, počet disků v jeho pevném disku, jádrech a dalších fyzických vlastnostech. <br> **Kapacity – kapacity** uzlů definují název a velikost konkrétního prostředku, ke kterému má určitý uzel k dispozici pro spotřebu. Uzel může například definovat, že má kapacitu pro metriku nazvanou "MemoryInMb" a že má ve výchozím nastavení k dispozici 2048 MB. Tyto kapacity se používají za běhu, aby se zajistilo, že služby, které vyžadují konkrétní objemy prostředků, jsou umístěné na uzlech, které mají tyto prostředky k dispozici v požadovaných částkách.<br>**Primární** – Pokud máte definován více než jeden typ NodeType, zajistěte, aby byla vlastnost Primary nastavena na primární s hodnotou *true*, což je, kde jsou spuštěny systémové služby. Všechny ostatní typy uzlů by měly být nastavené na hodnotu *false* . |
| **Sortiment** |Toto jsou podrobnosti pro každý uzel, který je součástí clusteru (typ uzlu, název uzlu, IP adresa, doména selhání a upgradovací doména uzlu). Počítače, na kterých chcete cluster vytvořit, musí být uvedené tady se svými IP adresami. <br> Pokud pro všechny uzly použijete stejnou IP adresu, vytvoří se cluster s jedním box, který můžete použít pro účely testování. Nepoužívejte clustery s jedním box pro nasazení produkčních úloh. |

Po konfiguraci clusteru se všechna nastavení nakonfigurují pro prostředí, která se dají testovat v prostředí clusteru (krok 7).

<a id="environmentsetup"></a>

## <a name="environment-setup"></a>Nastavení prostředí

Když správce clusteru nakonfiguruje samostatný cluster Service Fabric, je potřeba nastavit toto prostředí s následujícími kritérii: <br>
1. Uživatel, který vytváří cluster, musí mít oprávnění zabezpečení na úrovni správce pro všechny počítače, které jsou uvedené jako uzly v konfiguračním souboru clusteru.
2. Počítač, ze kterého se cluster vytváří, a každý počítač uzlu clusteru musí:
   * Odinstalování sady Service Fabric SDK
   * Odinstalování modulu runtime Service Fabric
   * Má povolenou službu Brána firewall systému Windows (MpsSvc)
   * Mít povolenou službu Remote Registry Service (Remote Registry)
   * Má povolené sdílení souborů (SMB)
   * Musí být otevřené potřebné porty na základě portů konfigurace clusteru.
   * Musí být otevřené potřebné porty pro službu Windows SMB a vzdálený registr: 135, 137, 138, 139 a 445.
   * Připojení k síti mezi sebou
3. Žádný z počítačů uzlů clusteru by neměl být řadičem domény.
4. Pokud má být cluster nasazen zabezpečeným clusterem, ověřte, zda jsou splněny nezbytné požadavky na zabezpečení a zda jsou správně nakonfigurovány proti konfiguraci.
5. Pokud počítače clusteru nejsou přístupné z Internetu, nastavte v konfiguraci clusteru následující:
   * Zakázat telemetrii: v části *vlastnosti* nastavené na *konfiguračního EnableTelemetry: false*
   * Zakázat automatické stažení verze prostředků infrastruktury & oznámením, že aktuální verze clusteru končí na konci podpory: v části *vlastnosti* nastavené na *"fabricClusterAutoupgradeEnabled": false*
   * Případně platí, že pokud je síťový přístup k Internetu omezený na allowlisted domény, vyžadují se následující domény pro automatický upgrade: go.microsoft.com download.microsoft.com

6. Nastavte vhodná Service Fabric vyloučení antivirové ochrany:

| **Vyloučené adresáře antivirové ochrany** |
| --- |
| Program Files\Microsoft Service Fabric |
| FabricDataRoot (z konfigurace clusteru) |
| FabricLogRoot (z konfigurace clusteru) |

| **Vyloučené procesy antivirové ochrany** |
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

## <a name="validate-environment-using-testconfiguration-script"></a>Ověřit prostředí pomocí skriptu TestConfiguration
Skript TestConfiguration.ps1 najdete v samostatném balíčku. Slouží jako Analyzátor osvědčených postupů k ověření některého z výše uvedených kritérií a měla by se používat jako správnosti kontrola k ověření, jestli cluster může být nasazený v daném prostředí. Pokud dojde k nějaké chybě, přečtěte si seznam v části [nastavení prostředí](service-fabric-cluster-standalone-deployment-preparation.md) pro řešení potíží.

Tento skript se dá spustit na jakémkoli počítači, který má oprávnění správce ke všem počítačům, které jsou uvedené jako uzly v konfiguračním souboru clusteru. Počítač, ve kterém je tento skript spuštěn, nemusí být součástí clusteru.

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

V současné době tento modul testování konfigurace neověřuje konfiguraci zabezpečení, takže je třeba provést nezávisle.

> [!NOTE]
> Neustále provádíme vylepšení, aby byl tento modul robustnější, takže pokud dojde k chybnému nebo chybějícímu případu, který se domníváte, že v tuto chvíli nejste zachytili TestConfiguration, dejte nám prosím informace prostřednictvím našich [kanálů podpory](./service-fabric-support.md).
>
>

## <a name="next-steps"></a>Další kroky
* [Vytvoření samostatného clusteru běžícího na Windows Serveru](service-fabric-cluster-creation-for-windows-server.md)
