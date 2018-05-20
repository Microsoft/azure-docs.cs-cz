---
title: Další informace o Azure Service Fabric | Microsoft Docs
description: Další informace o klíčových konceptech a hlavními oblastmi Azure Service Fabric. Poskytuje přehled rozšířené Service Fabric a postup vytvoření mikroslužeb.
services: service-fabric
documentationcenter: .net
author: rwike77
manager: timlt
editor: ''
ms.assetid: ''
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 12/08/2017
ms.author: ryanwi
ms.openlocfilehash: 1c3ea5b041cf2a961ef57bc168ae86b83412e044
ms.sourcegitcommit: eb75f177fc59d90b1b667afcfe64ac51936e2638
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/16/2018
---
# <a name="so-you-want-to-learn-about-service-fabric"></a>Proto chcete dozvědět o Service Fabric?
Azure Service Fabric je platforma distribuovaných systémů usnadňující balení, nasazování a spravování škálovatelných a spolehlivých mikroslužeb.  Service Fabric má rozlehlých, ale a je mnoha Další.  Tento článek obsahuje souhrn Service Fabric a popisuje základní koncepty, programovací modely, životního cyklu aplikací, testování, clustery a sledování stavu. Pro čtení [přehled](service-fabric-overview.md) a [co jsou mikroslužeb?](service-fabric-overview-microservices.md) úvod a jak Service Fabric slouží k vytvoření mikroslužeb. Tento článek neobsahuje kompletní seznam obsahu, ale propojit přehled a získávání Začínáme články pro každou oblast Service Fabric. 

## <a name="core-concepts"></a>Základní koncepty
[Terminologie Service Fabric](service-fabric-technical-overview.md), [aplikačního modelu](service-fabric-application-model.md), a [podporované programovací modely](service-fabric-choose-framework.md) poskytují další koncepty a popisy, ale zde jsou základní informace.

<table><tr><th>Základní koncepty</th><th>V době návrhu</th><th>Za běhu</th></tr>
<tr><td><a target="_blank" href="https://mva.microsoft.com/en-US/training-courses/building-microservices-applications-on-azure-service-fabric-16747?l=tbuZM46yC_5206218965">
<img src="./media/service-fabric-content-roadmap/CoreConceptsVid.png" WIDTH="240" HEIGHT="162"></a></td>
<td><a target="_blank" href="https://mva.microsoft.com/en-US/training-courses/building-microservices-applications-on-azure-service-fabric-16747?l=tlkI046yC_2906218965"><img src="./media/service-fabric-content-roadmap/RunTimeVid.png" WIDTH="240" HEIGHT="162"></a></td>
<td><a target="_blank" href="https://mva.microsoft.com/en-US/training-courses/building-microservices-applications-on-azure-service-fabric-16747?l=x7CVH56yC_1406218965">
<img src="./media/service-fabric-content-roadmap/RunTimeVid.png" WIDTH="240" HEIGHT="162"></a></td></tr>
</table>

### <a name="design-time-application-type-service-type-application-package-and-manifest-service-package-and-manifest"></a>Návrh čas: typ aplikace, typ služby, balíček aplikace a manifest, balíček služby a manifestu
Typ aplikace je název a verzi, která je přiřazena ke kolekci typů služeb. To je definována v *ApplicationManifest.xml* souboru, která je integrována v adresáři balíčku aplikace. Balíček aplikace se pak zkopíruje do úložiště bitové kopie cluster Service Fabric. Potom můžete vytvořit s názvem aplikace z tohoto typu aplikace, které pak spustí v rámci clusteru. 

Typ služby je název a verzi, která je přiřazená kód balíčky, data balíčky a balíčky konfigurace služby. To je definována v souboru ServiceManifest.xml, která je integrována v adresáři balíčku služby. Adresář balíčku služby se pak odkazuje balíček aplikace *ApplicationManifest.xml* souboru. V rámci clusteru po vytvoření pojmenované aplikace, můžete vytvořit s názvem služby z jednoho z typů služeb typ aplikace. Typ služby je popsán v jeho *ServiceManifest.xml* souboru. Typ služby se skládá z nastavení konfigurace služby spustitelného kódu, které se načtou za běhu, a statických dat, která se využívá služba.

![Typy aplikací Service Fabric a typů služeb][cluster-imagestore-apptypes]

Balíček aplikace je adresář disk obsahující typ aplikace *ApplicationManifest.xml* souboru, který odkazuje na balíčky služeb pro každý typ služby, která vytváří typ aplikace. Balíček aplikace pro typ e-mailové aplikace může například obsahovat odkazy na balíček služby fronty, front-endové služby balíčku a balíček služby databáze. Soubory v adresáři balíčku aplikace se zkopírují do úložiště bitové kopie cluster Service Fabric. 

Balíček služby je adresář disk obsahující typ služby *ServiceManifest.xml* souboru, který odkazuje na kód, statických dat a balíčky konfigurace pro typ služby. Soubory v adresáři balíčku služby odkazuje typ aplikace *ApplicationManifest.xml* souboru. Například může naleznete v kódu, statických dat a konfigurace balíčky, které tvoří služba databáze balíčku služby.

### <a name="run-time-clusters-and-nodes-named-applications-named-services-partitions-and-replicas"></a>Čas spuštění: clustery a uzly, s názvem aplikace, s názvem služby, oddíly a repliky
[Cluster Service Fabric](service-fabric-deploy-anywhere.md) je síťově propojená sada virtuálních nebo fyzických počítačů, ve které se nasazují a spravují mikroslužby. Clustery je možné škálovat na tisíce počítačů.

Počítač nebo virtuální počítač, který je součástí clusteru, se nazývá uzel. Každému uzlu je přiřazen název uzlu (řetězec). Uzly mají určité charakteristiky, například vlastnosti umístění. Každý počítač nebo virtuální počítač má služby systému Windows automaticky spouštěná `FabricHost.exe`, který spuštění při spuštění a pak spustí dvě spustitelné soubory: `Fabric.exe` a `FabricGateway.exe`. Tyto dvě spustitelné soubory tvoří uzlu. Pro vývoj nebo testování scénáře, může hostovat více uzlů na jeden počítač nebo virtuální počítač spuštěním několika instancí `Fabric.exe` a `FabricGateway.exe`.

Pojmenované aplikace je kolekce s názvem služby, které provádí některé funkce nebo funkce. Služba provede kompletní a samostatné funkce (ho můžete spustit a spustit nezávisle na jiných služeb) a se skládá z kódu, konfigurace a data. Po zkopírování balíčku aplikace je úložiště image store, vytvoření instance aplikace v rámci clusteru zadáním typu aplikace balíček aplikace (pomocí jeho název a verzi). Každá instance typu aplikace je přiřazen název identifikátor URI, který vypadá jako *fabric: / MyNamedApp*. V rámci clusteru můžete vytvořit více aplikací s názvem z typu jednu aplikaci. Můžete také vytvořit s názvem aplikace z typů jinou aplikaci. Každé pojmenované aplikace je spravovaná a verzí nezávisle.

Po vytvoření pojmenované aplikace, můžete vytvořit instanci jednoho z jeho typů služeb (s názvem service) v rámci clusteru zadáním typu služby (pomocí jeho název a verzi). Každá instance typu služby je přiřazen název identifikátor URI oboru v identifikátoru URI s názvem aplikace. Například pokud vytvoříte "Databáze" s názvem služby v rámci "MyNamedApp" s názvem aplikace, identifikátor URI bude vypadat takto: *fabric: / MyNamedApp/databáze*. V rámci pojmenované aplikace můžete vytvořit jednu nebo více služeb s názvem. Počty instancí nebo repliky a každé pojmenované služby může mít svůj vlastní schéma oddílu. 

Existují dva typy služeb: bezstavové a stavové. Bezstavové služby můžete uložit trvalý stav služby externího úložiště, jako je například Azure Storage, Azure SQL Database nebo Azure Cosmos DB. Bezstavové služby použijte, pokud služba používá žádné trvalé úložiště vůbec. Stavové služby Service Fabric používá ke správě služby stavu přes jeho spolehlivé kolekce nebo programovací modely Reliable Actors. 

Při vytváření pojmenovaného služby, zadejte schéma oddílu. Služby s velkým množstvím stavu rozdělit data do oddílů. Každý oddíl je zodpovědná za část dokončení stav služby, která je rozdělena mezi uzly clusteru.  

Následující diagram znázorňuje vztah mezi aplikací a instance služby, oddíly a repliky.

![Oddíly a repliky v rámci služby][cluster-application-instances]

### <a name="partitioning-scaling-and-availability"></a>Rozdělení do oddílů, škálování a dostupnosti
[Vytváření oddílů](service-fabric-concepts-partitioning.md) není jedinečný pro Service Fabric. Dobře známé formulář oddíly je vytvoření oddílů dat nebo horizontálního dělení. Stavové služby s velkým množstvím stavu rozdělit data do oddílů. Každý oddíl je zodpovědná za část dokončení stav služby. 

Repliky každý oddíl jsou rozprostřeny mezi uzly clusteru, což umožňuje stavu služby s názvem [škálování](service-fabric-concepts-scalability.md). Jako data potřebuje růst, oddíly růst a Service Fabric oddíly znovu vytvoří rovnováhu mezi uzly k efektivní využívání prostředků hardwaru. Pokud přidáte nové uzly do clusteru, Service Fabric znovu vyvážit repliky oddílu napříč vyšší počet uzlů. Celkové zlepšuje výkon aplikace a snižuje kolize pro přístup do paměti. Pokud uzly v clusteru nejsou používány efektivně, můžete snížit počet uzlů v clusteru. Service Fabric opakujte znovu vytvoří, rovnováhu repliky oddílu napříč ke snížení počtu uzlů zajistit lepší využití hardwaru na každém uzlu.

V rámci oddílu mají bezstavové služby s názvem instance, zatímco stavové služby s názvem mít repliky. Bezstavové služby s názvem mají obvykle, vždy jen jeden oddíl, protože mají žádný vnitřní stav. Instance oddílu přinášejí [dostupnosti](service-fabric-availability-services.md). Pokud se jedna instance nezdaří, ostatní instance i nadále fungovat normálně a pak Service Fabric vytvoří novou instanci. Stateful s názvem služby udržovat, že jejich stavu v rámci repliky a každý oddíl má svou vlastní sady replik. Operace čtení a zápisu jsou prováděny v jedné replice (označovaný jako primární). Změny stavu z zápisu operace se replikují do víc replik (označovaný jako aktivní sekundární databáze). Má replika selhat, Service Fabric vytvoří novou repliku z existujících replik.

## <a name="stateless-and-stateful-microservices-for-service-fabric"></a>Bezstavové a stavové mikroslužby pro Service Fabric
Service Fabric umožňuje sestavovat aplikace, které se skládají z mikroslužeb nebo kontejnerů. Bezstavové mikroslužby (například brány protokolů a webové proxy) si mimo požadavek a odpověď ze služby neudržují měnitelný stav. Role pracovních procesů služby Azure Cloud Services jsou příkladem stavové služby. Stavové mikroslužby (například uživatelské účty, databáze, zařízení, nákupní košíky a fronty) si udržují měnitelný a autoritativní stav i mimo požadavek a odpověď. Dnešní aplikace v internetovém měřítku se skládají z kombinace bezstavových a stavových mikroslužeb. 

Klíče differentation s Service Fabric je silné zaměřuje na budování stavové služby, buď pomocí [integrovaný programovací modely ](service-fabric-choose-framework.md) nebo s kontejnerizované stavové služby. [Scénáře aplikací](service-fabric-application-scenarios.md) popisují scénáře, ve kterých se používají stavové služby.

Proč máte stavová mikroslužeb společně s bezstavové ty? Dva hlavní důvody jsou:

* Můžete vytvořit vysokou propustnost, nízkou latencí, proti chybám online zpracování transakcí (OLTP) služby udržováním kódu a zavření data na stejném počítači. Některé příklady jsou interaktivní obchodní poutače, hledání, systémy Internet věcí (IoT), obchodní systémy, platební karty zpracování a podvod detekce systémů a správu osobní záznamu.
* Návrh aplikace můžete zjednodušit. Stavová mikroslužeb odebrat potřebu další fronty a mezipaměti, které tradičně musí zahrnovat požadavky na dostupnosti a latence čistě bezstavové aplikace. Stavové služby jsou přirozeně vysokou dostupnost a nízkou latencí, což snižuje počet přesunutí částí ke správě v aplikaci jako celek.

## <a name="supported-programming-models"></a>Podporované programovací modely
Service Fabric nabízí několik způsobů, jak zapsat a spravovat vaše služby. Služby můžete použít rozhraní API služby Fabric na plně využít výhod funkcí a architektur aplikací platformu. Služby mohou být také všechny kompilované spustitelný program napsané v libovolném jazyce a hostovaná v clusteru Service Fabric. Další informace najdete v tématu [podporované programovací modely](service-fabric-choose-framework.md).

### <a name="containers"></a>Containers
Ve výchozím nastavení Service Fabric nasadí a aktivuje služby jako procesy. Service Fabric lze také nasadit služby v [kontejnery](service-fabric-containers-overview.md). Je důležité je možné kombinovat služby v procesy a služby v kontejnerech ve stejné aplikaci. Service Fabric podporuje nasazení kontejnerů Linux kontejnery Windows na Windows Server 2016. Můžete nasadit existující aplikace, bezstavové služby nebo stavové služby v kontejnerech. 

### <a name="reliable-services"></a>Reliable Services
[Spolehlivé služby](service-fabric-reliable-services-introduction.md) je jednoduché rozhraní pro zápis služby, které integrovat platformy Service Fabric a využívat úplnou sadu funkcí platformy. Spolehlivé služby může být bezstavové (podobně jako na většině platforem služby, jako jsou webové servery nebo rolí pracovního procesu ve službě Azure Cloud Services), kde je uložen stav externí řešení, například Azure DB nebo úložiště tabulek Azure. Spolehlivé služby může být také stateful, kde je uložen stav přímo v samotné pomocí spolehlivé kolekcí služby. Stav se provádí [vysokou dostupností](service-fabric-availability-services.md) prostřednictvím replikace a distribuované prostřednictvím [dělení](service-fabric-concepts-partitioning.md), všechny spravované automaticky pomocí Service Fabric.

### <a name="reliable-actors"></a>Reliable Actors
Postavená na spolehlivé služby, [spolehlivé objektu Actor](service-fabric-reliable-actors-introduction.md) framework je aplikační rozhraní, který implementuje vzor virtuální objektu Actor, na základě vzoru návrhu objektu actor. Rozhraní objektu Actor spolehlivé používá nezávislé jednotky výpočetních operací a stavu s jedním podprocesem provádění názvem aktéři. Spolehlivé objektu Actor framework poskytuje součástí komunikace pro aktéři a předem nastavit stav konfigurace trvalosti a Škálováním na více systémů.

### <a name="aspnet-core"></a>Jádro ASP.NET
Service Fabric se integruje s [ASP.NET Core](service-fabric-reliable-services-communication-aspnetcore.md) jako první třídy programovací model pro vytváření webových a aplikací API.  ASP.NET Core lze použít dvěma různými způsoby v Service Fabric:

- Hostovaný jako spustitelný soubor hosta. To slouží především ke spouštění existujících aplikací ASP.NET Core v Service Fabric beze změn kódu.
- Spusťte uvnitř spolehlivě. To umožňuje lepší integraci s modulem runtime Service Fabric a umožňuje stavová ASP.NET Core services.

### <a name="guest-executables"></a>Spustitelné soubory typu Host
A [spustitelný soubor hosta](service-fabric-guest-executables-introduction.md) je existující, libovolného spustitelného souboru (napsané v libovolném jazyce) hostovaná v clusteru Service Fabric souběžně s jinými službami. Spustitelné soubory hosta není integrovat přímo s rozhraními API služby prostředků infrastruktury. Ale budou i nadále využívat funkce nabízí platformu, například vlastní stav a spouštění sestav a volání rozhraní REST API služby možnosti rozpoznání. Mají také celou aplikaci životní cyklus podpory. 

## <a name="application-lifecycle"></a>Životní cyklus aplikace
Jako s jinými platformami, v Service Fabric aplikace obvykle projde má tyto fáze: návrh, vývoj, testování, nasazení, upgrade, údržbu a odebírání. Service Fabric nabízí prvotřídní podporu pro celou aplikaci životního cyklu aplikací cloudu, od vývoje přes nasazení, každodenní správu a údržbu na případné vyřazení z provozu. Model služby umožňuje několik různých rolí se zúčastnit nezávisle v průběhu životního cyklu aplikace. [Životní cyklus aplikace Service Fabric](service-fabric-application-lifecycle.md) poskytuje přehled o rozhraní API a jak se používají různé role v průběhu fáze životního cyklu aplikace Service Fabric. 

Životní cyklus celé aplikace můžete spravovat pomocí [rutiny prostředí PowerShell](/powershell/module/ServiceFabric/), [rozhraní příkazového řádku](service-fabric-sfctl.md), [rozhraní API jazyka C#](/dotnet/api/system.fabric.fabricclient.applicationmanagementclient), [rozhraní API Java](/java/api/system.fabric._application_management_client), a [ Rozhraní REST API](/rest/api/servicefabric/). Můžete také nastavit nepřetržité integrace/průběžné kanály nasazení pomocí nástrojů, jako [Visual Studio Team Services](service-fabric-set-up-continuous-integration.md) nebo [volaných](service-fabric-cicd-your-linux-applications-with-jenkins.md).

V následujícím videu Microsoft Virtual Academy popisuje, jak spravovat životním cyklu aplikací: <center><a target="_blank" href="https://mva.microsoft.com/en-US/training-courses/building-microservices-applications-on-azure-service-fabric-16747?l=My3Ka56yC_6106218965">
<img src="./media/service-fabric-content-roadmap/AppLifecycleVid.png" WIDTH="360" HEIGHT="244">
</a></center>

## <a name="test-applications-and-services"></a>Testování aplikací a služeb
Pokud chcete vytvořit skutečně cloudové služby, je důležité k ověření, že vašim aplikacím a službám můžete odolat selhání reálného. Služba Analysis Service selhání je určená pro testování služby, které jsou postaveny na Service Fabric. S [selhání Analysis Service](service-fabric-testability-overview.md), můžete vyvolat smysluplný chyb a spouštění scénáře dokončení testování vaší aplikace. Tyto scénáře a chyb vykonává a ověřit množství stavy a přechody, které služba bude mít v průběhu své životnosti, všechny řízené, bezpečné a konzistentním způsobem.

[Akce](service-fabric-testability-actions.md) cíle služby pro testování pomocí jednotlivých chyb. Vývojář služby můžete použít jako stavební bloky k zápisu složité scénáře. Příklady simulované chyb:

* Restartujte uzel k simulaci libovolný počet situace, kdy počítač nebo virtuální počítač restartovat.
* Přesunutí repliky stavové služby k simulaci Vyrovnávání zatížení a převzetí služeb při selhání nebo upgradu aplikace.
* Vyvolání ztrátě kvora na stavové služby, chcete-li vytvořit situaci, kdy operace zápisu nemůže pokračovat, protože nejsou k dispozici dostatek "zálohování" nebo "sekundární" repliky tak, aby přijímal nová data.
* Vyvolání ztrátě dat na stavové služby pro vytvoření situaci, kde všechny stavy v paměti je zcela k vymazání.

[Scénáře](service-fabric-testability-scenarios.md) komplexních operací se skládají z jedné nebo více akcí. Služba Analysis odolnost poskytuje dva předdefinované kompletní scénáře:

* [Scénář Chaos](service-fabric-controlled-chaos.md)-simuluje průběžné, prokládaná chyb (řádně i vynuceném) v rámci clusteru přes dlouhou dobu.
* [Scénář převzetí služeb při selhání](service-fabric-testability-scenarios.md#failover-test)-verzi chaos testovací scénář, který cílí oddíl konkrétní službu a nechat jiných služeb neovlivní.

## <a name="clusters"></a>Clustery
[Cluster Service Fabric](service-fabric-deploy-anywhere.md) je síťově propojená sada virtuálních nebo fyzických počítačů, ve které se nasazují a spravují mikroslužby. Clustery je možné škálovat na tisíce počítačů. Počítač nebo virtuální počítač, který je součástí clusteru, se nazývá uzlem clusteru. Každému uzlu je přiřazen název uzlu (řetězec). Uzly mají určité charakteristiky, například vlastnosti umístění. Každý počítač nebo virtuální počítač se automaticky spouštěná služba, `FabricHost.exe`, který spuštění při spuštění a pak spustí dvě spustitelné soubory: Fabric.exe a FabricGateway.exe. Tyto dvě spustitelné soubory tvoří uzlu. Pro testování scénáře, může hostovat více uzlů na jeden počítač nebo virtuální počítač spuštěním několika instancí `Fabric.exe` a `FabricGateway.exe`.

Clusterů Service Fabric se dají vytvořit na virtuálních nebo fyzických počítačích se systémem Windows Server nebo Linux. Budete moci nasazení a spuštění aplikace Service Fabric v jakémkoli prostředí, kde máte sadu Windows Server nebo Linux počítače, které jsou vzájemně provázány: místně na Microsoft Azure nebo na všechny poskytovatele cloudových služeb.

Následující Microsoft Virtual Academy video popisuje clusterů Service Fabric: <center><a target="_blank" href="https://mva.microsoft.com/en-US/training-courses/building-microservices-applications-on-azure-service-fabric-16747?l=tbuZM46yC_5206218965">
<img src="./media/service-fabric-content-roadmap/ClusterOverview.png" WIDTH="360" HEIGHT="244">
</a></center>

### <a name="clusters-on-azure"></a>Clustery v Azure
Clusterů Service Fabric systémem Azure poskytuje integraci se službou další funkce Azure a služby, takže je operace a Správa clusteru jednodušší a spolehlivější. Cluster je prostředek Azure Resource Manager, abyste mohli clustery podobně jako všechny ostatní prostředky v Azure. Resource Manager také poskytuje snadnou správu všechny prostředky používané clusteru jako na jednu jednotku. Clustery v Azure jsou integrované s Azure diagnostiku a analýzy protokolů. Typy uzlů clusteru jsou [sady škálování virtuálního počítače](/azure/virtual-machine-scale-sets/index), takže je součástí funkce automatického škálování.

Můžete vytvořit cluster v Azure pomocí [portál Azure](service-fabric-cluster-creation-via-portal.md), z [šablony](service-fabric-cluster-creation-via-arm.md), nebo z [Visual Studio](service-fabric-cluster-creation-via-visual-studio.md).

Service Fabric v systému Linux umožňuje vytvářet, nasazovat a spravovat vysoce dostupné a vysoce škálovatelné aplikace v systému Linux, stejně jako v systému Windows. Rozhraní Service Fabric (Reliable Services a Reliable Actors) jsou k dispozici v jazyce Java v systému Linux, kromě C# (.NET Core). Můžete také vytvořit [spustitelný soubor služby hosta](service-fabric-guest-executables-introduction.md) s libovolný jazyk nebo rozhraní. Orchestrace Docker kontejnery je také podporována. Kontejnery docker lze spouštět spustitelné soubory hosta nebo nativní služby, Service Fabric, které používají rozhraní Service Fabric. Další informace najdete v tématu o [Service Fabric v systému Linux](service-fabric-deploy-anywhere.md).

Existují některé funkce, které jsou podporovány v systému Windows, ale není v systému Linux. Další informace, přečtěte si [rozdíly mezi Service Fabric v systému Linux a Windows](service-fabric-linux-windows-differences.md).

### <a name="standalone-clusters"></a>Samostatné clustery
Service Fabric nabízí instalační balíček vytvořit samostatnou Service Fabric clustery místně nebo na všechny poskytovatele cloudových služeb. Samostatné clustery získáte možnost používat tato zařízení k hostování clusteru s podporou požadovaná místa. Pokud vaše data podléhá dodržování předpisů nebo zákonných omezení, nebo chcete zachovat místní data, můžete hostovat vlastní cluster a aplikace. Aplikace Service Fabric můžete spustit v prostředí s více hostitelských beze změn, takže vašich znalostí vytváření aplikace přenáší z jednoho hostování prostředí do druhého. 

[Vytvoření vaší první samostatný cluster Service Fabric](service-fabric-get-started-standalone-cluster.md)

Linux samostatné clustery se ještě nepodporují.

### <a name="cluster-security"></a>Zabezpečení clusteru
Clustery musí být zabezpečená neoprávněným uživatelům zabránit v připojení ke clusteru, zejména v případě, že je v něm spuštěny úlohy v produkčním prostředí. Přestože je možné vytvořit zabezpečená clusteru, tak učiníte, umožníte anonymní uživatelé se k němu připojit, pokud koncové body správy jsou umístěny do veřejného Internetu. Není možné později povolení zabezpečení zabezpečená clusteru: při vytváření clusteru je povoleno zabezpečení clusteru.

Scénáře zabezpečení clusteru jsou:
* Zabezpečení – uzly
* Uzel Klient zabezpečení
* Řízení přístupu na základě role (RBAC)

Další informace najdete v tématu [zabezpečení clusteru](service-fabric-cluster-security.md).

### <a name="scaling"></a>Škálování
Pokud přidáte nové uzly do clusteru, Service Fabric znovu vytvoří rovnováhu repliky oddílu a instance napříč vyšší počet uzlů. Celkové zlepšuje výkon aplikace a snižuje kolize pro přístup do paměti. Pokud uzly v clusteru nejsou používány efektivně, můžete snížit počet uzlů v clusteru. Service Fabric opakujte znovu vytvoří rovnováhu repliky oddílu a instance napříč ke snížení počtu uzlů zajistit lepší využití hardwaru na každém uzlu. Clustery v Azure je možné škálovat buď [ručně](service-fabric-cluster-scale-up-down.md) nebo [prostřednictvím kódu programu](service-fabric-cluster-programmatic-scaling.md). Je možné rozšířit samostatnou clustery [ručně](service-fabric-cluster-windows-server-add-remove-nodes.md).

### <a name="cluster-upgrades"></a>Upgrade clusteru
Pravidelně jsou vydávány nové verze modulu runtime Service Fabric. Provést upgrady modulu runtime, nebo z prostředků infrastruktury, v clusteru tak, aby vždy běží [podporovaná verze](service-fabric-support.md). Kromě upgrady prostředků infrastruktury můžete také aktualizovat konfiguraci clusteru, jako je například certifikáty nebo porty aplikace.

Cluster Service Fabric je na prostředek, který vlastníte, ale je částečně spravováno společností Microsoft. Microsoft je odpovědná za použití dílčích oprav základního operačního systému a provádění upgrady prostředků infrastruktury v clusteru. Můžete nastavit clusteru na aktualizace automatické prostředků infrastruktury, když společnost Microsoft vydá nové verze, nebo zvolte verze podporovaných fabric, který chcete vybrat. Upgrady prostředků infrastruktury a konfigurace jde nastavit prostřednictvím portálu Azure nebo prostřednictvím Resource Manager. Další informace najdete v tématu [Upgrade clusteru Service Fabric](service-fabric-cluster-upgrade.md). 

Cluster s podporou samostatné je prostředek které je zcela vlastní. Jste zodpovědní za použití dílčích oprav základního operačního systému a inicializaci upgrady prostředků infrastruktury. Pokud váš cluster může připojit k [ https://www.microsoft.com/download ](https://www.microsoft.com/download), můžete nastavit cluster automaticky stáhnout a zajištění nového balíčku modulu runtime Service Fabric. By potom zahájit upgrade. Pokud váš cluster nemůže získat přístup k [ https://www.microsoft.com/download ](https://www.microsoft.com/download), můžete ručně stáhnout nový balíček modulu runtime z počítače připojené Internetu a potom zahájit upgrade. Další informace najdete v tématu [Upgrade clusteru Service Fabric samostatné](service-fabric-cluster-upgrade-windows-server.md).

## <a name="health-monitoring"></a>Monitorování stavu
Zavádí Service Fabric [stavu modelu](service-fabric-health-introduction.md) navržený tak, aby příznak není v pořádku, cluster a aplikace podmínek na konkrétní entity (například uzly clusteru a repliky služby). Health model používá stavu reporters (součásti systému a watchdogs). Cílem je rychlé a snadné diagnostiky a opravy. Služba zapisovače muset myslíte o stavu předem a postup [návrhu, vytváření sestav stavu](service-fabric-report-health.md#design-health-reporting). Všechny podmínku, která může mít vliv na stav by měl být zaznamenány na, zejména v případě, že může pomoci příznak problémy blízko kořenu. Informace o stavu můžete ušetřit čas a úsilí na ladění a šetření jednou služba je spuštěná ve velkém měřítku v produkčním prostředí.

Monitorování reporters Service Fabric identifikovat podmínky, které vás zajímají. Sestavy budou tyto podmínky založené na jejich místní zobrazení. [Úložiště stavu](service-fabric-health-introduction.md#health-store) agreguje data o stavu posílá všechny reporters určit, jestli entity jsou globální v pořádku. Model má být bohaté, flexibilní a snadno se používá. Kvalitu sestav stavu určuje přesnost zobrazení stavu clusteru. Falešně pozitivních zjištění, které se nesprávně zobrazí není v pořádku problémy může mít negativní vliv na upgrady nebo jiné služby, které používají data o stavu. Příkladem takové služby jsou opravy služeb a mechanismy pro výstrahy. Proto je některé myšlenku potřebných k poskytování sestavy, které zaznamenat podmínky zájem o optimálně.

Vytváření sestav se dá udělat z:
* Monitorované Service Fabric service replik nebo instancí.
* Interní watchdogs nasazené jako služba Service Fabric (například Service Fabric bezstavové služba, která monitoruje podmínky a vydá sestavy). Watchdogs lze nasadit na všech uzlech, nebo můžete spřažené s monitorované služby.
* Interní watchdogs, které běžet na uzlech Service Fabric, ale nejsou implementované jako služby Service Fabric.
* Externí watchdogs, které probe prostředků z mimo cluster Service Fabric (například monitorování služby jako Gomez).

Předinstalované komponenty Service Fabric sestavy stavu všech entit v clusteru. [Sestav o stavu systému](service-fabric-understand-and-troubleshoot-with-system-health-reports.md) poskytují přehled o clusteru a aplikaci funkce a příznak problémy prostřednictvím stavu. Pro aplikace a služby ověřte sestav o stavu systému entity jsou implementované a chovají správně z perspektivy modulu runtime Service Fabric. Sestavy není zadejte jakékoli sledování stavu obchodní logiky, služby nebo zjistit "zamrzlých" procesů. Přidání konkrétní informace o stavu do vaší služby logiku [implementovat vlastní stavu reporting](service-fabric-report-health.md) v službě.

Service Fabric nabízí několik způsobů, jak [zobrazit sestavy stavu](service-fabric-view-entities-aggregated-health.md) agregován v health store:
* [Service Fabric Explorer](service-fabric-visualizing-your-cluster.md) nebo jiných nástrojů vizualizace.
* Dotazy na stav (prostřednictvím [prostředí PowerShell](/powershell/module/ServiceFabric/), [rozhraní příkazového řádku](service-fabric-sfctl.md), [FabricClient rozhraní API jazyka C#](/dotnet/api/system.fabric.fabricclient.healthclient) a [rozhraní API Java FabricClient](/java/api/system.fabric._health_client), nebo [REST Rozhraní API](/rest/api/servicefabric)).
* Obecné dotazuje to návratový seznam entit, které mají stav jako jedna z vlastností (pomocí prostředí PowerShell, rozhraní příkazového řádku, rozhraní API nebo REST).

V následujícím videu Microsoft Virtual Academy popisuje model stavu Service Fabric a jak se používají: <center><a target="_blank" href="https://mva.microsoft.com/en-US/training-courses/building-microservices-applications-on-azure-service-fabric-16747?l=tevZw56yC_1906218965">
<img src="./media/service-fabric-content-roadmap/HealthIntroVid.png" WIDTH="360" HEIGHT="244">
</a></center>

## <a name="monitoring-and-diagnostics"></a>Monitorování a diagnostika
[Monitorovací a diagnostické](service-fabric-diagnostics-overview.md) jsou důležité pro vývoj, testování a nasazení aplikace a služby v jakémkoli prostředí. Řešení Service Fabric fungují lépe, když plánování a implementace monitorování a diagnostiky, které pomáhají zajistit aplikace a služby jsou funguje podle očekávání v místním vývojovém prostředí, nebo v produkčním prostředí.

Hlavní cíle monitorování a Diagnostika se:

- Najít a diagnostikovat problémy s hardwarem a infrastruktury
- Rozpoznat problémy s softwaru a aplikace, zkrátit dobu prostojů při služby
- Informace k prostředku využívání a nápovědy jednotky operations rozhodování o
- Optimalizace výkonu aplikace, služby a infrastruktury
- Generovat podnikových statistik a identifikujte oblasti zlepšování
 
Celkové pracovní postup monitorování a Diagnostika zahrnuje tři kroky:

1. Generování událostí: Jedná se o událostí (protokoly, trasování, vlastní události) na infrastrukturu (cluster), platformy a na úrovni aplikace / služby
2. Agregace událostí: vygenerovaných událostí je třeba shromažďovat a agregovat předtím, než lze zobrazit
3. Analýza: události musí být vizualizovaných a v některých formátu, aby bylo možné pro analýzu a zobrazit podle potřeby

Více produkty jsou k dispozici, které zahrnují tyto tři oblasti, a můžete vybrat různé technologie pro každou. Další informace najdete v tématu [monitorovací a diagnostické pro Azure Service Fabric](service-fabric-diagnostics-overview.md).

## <a name="next-steps"></a>Další postup
* Naučte se vytvořit [cluster v Azure](service-fabric-cluster-creation-via-portal.md) nebo [samostatný clusteru ve Windows](service-fabric-cluster-creation-for-windows-server.md).
* Zkuste si vytvořit službu pomocí programovacího modelu [Reliable Services](service-fabric-reliable-services-quick-start.md) nebo [Reliable Actors](service-fabric-reliable-actors-get-started.md).
* Zjistěte, jak [migrovat z cloudové služby](service-fabric-cloud-services-migration-differences.md).
* Naučte se [monitorování a Diagnostika služby](service-fabric-diagnostics-how-to-monitor-and-diagnose-services-locally.md). 
* Naučte se [testování vaší aplikace a služby](service-fabric-testability-overview.md).
* Naučte se [spravovat a orchestraci prostředky clusteru](service-fabric-cluster-resource-manager-introduction.md).
* Projděte [Service Fabric ukázky](http://aka.ms/servicefabricsamples).
* Další informace o [možnosti podpory Service Fabric](service-fabric-support.md).
* Pro čtení [blogu týmu](https://blogs.msdn.microsoft.com/azureservicefabric/) pro články a oznámení.


[cluster-application-instances]: media/service-fabric-content-roadmap/cluster-application-instances.png
[cluster-imagestore-apptypes]: ./media/service-fabric-content-roadmap/cluster-imagestore-apptypes.png
