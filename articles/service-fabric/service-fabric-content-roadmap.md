---
title: Další informace o Azure Service Fabric | Dokumentace Microsoftu
description: Další informace o klíčových konceptech a hlavní oblasti Azure Service Fabric. Přehled rozšířené Service Fabric a jak vytvářet mikroslužby.
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
ms.openlocfilehash: c55e67e24c8f5ff6e243c67b614592874b2cc459
ms.sourcegitcommit: 0bb8db9fe3369ee90f4a5973a69c26bff43eae00
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/08/2018
ms.locfileid: "48870651"
---
# <a name="so-you-want-to-learn-about-service-fabric"></a>Proto chcete se dozvědět o službě Service Fabric?
Azure Service Fabric je platforma distribuovaných systémů usnadňující balení, nasazování a spravování škálovatelných a spolehlivých mikroslužeb.  Service Fabric má velký plochy, ale a existuje mnoho dalších informací.  Tento článek poskytuje stručný Service Fabric a popisuje základní koncepty programovacích modelů životního cyklu aplikací, testování, clustery a sledování stavu. Čtení [přehled](service-fabric-overview.md) a [co jsou mikroslužby?](service-fabric-overview-microservices.md) úvod a jak Service Fabric umožňuje vytvářet mikroslužby. Tento článek obsahuje úplný seznam obsahu, ale propojit pro přehled a získávání Začínáme články pro každou oblast Service Fabric. 

## <a name="core-concepts"></a>Základní koncepty
[Terminologie Service Fabric](service-fabric-technical-overview.md), [aplikační model](service-fabric-application-model.md), a [podporované programovací modely](service-fabric-choose-framework.md) poskytují další koncepty a popisy, ale zde jsou základní informace.

<table><tr><th>Základní koncepty</th><th>Doby návrhu</th><th>Za běhu</th></tr>
<tr><td><a target="_blank" href="https://mva.microsoft.com/en-US/training-courses/building-microservices-applications-on-azure-service-fabric-16747?l=tbuZM46yC_5206218965">
<img src="./media/service-fabric-content-roadmap/CoreConceptsVid.png" WIDTH="240" HEIGHT="162"></a></td>
<td><a target="_blank" href="https://mva.microsoft.com/en-US/training-courses/building-microservices-applications-on-azure-service-fabric-16747?l=tlkI046yC_2906218965"><img src="./media/service-fabric-content-roadmap/RunTimeVid.png" WIDTH="240" HEIGHT="162"></a></td>
<td><a target="_blank" href="https://mva.microsoft.com/en-US/training-courses/building-microservices-applications-on-azure-service-fabric-16747?l=x7CVH56yC_1406218965">
<img src="./media/service-fabric-content-roadmap/RunTimeVid.png" WIDTH="240" HEIGHT="162"></a></td></tr>
</table>

### <a name="design-time-application-type-service-type-application-package-and-manifest-service-package-and-manifest"></a>Návrhu: typ aplikace, typ služby, balíček aplikace a manifest, balíček služby a manifest
Typ aplikace je název/verze, která je přiřazena ke kolekci typů služeb. Toto je definováno v *ApplicationManifest.xml* soubor, který je vložený v adresáři balíčku aplikace. Balíček aplikace se pak zkopíruje do úložiště imagí clusteru Service Fabric. Pojmenované aplikace si můžete vytvořit z tohoto typu aplikace, které pak spustí v rámci clusteru. 

Typ služby je název/verze přiřadit balíčky kódu, data balíčky a balíčky pro konfiguraci služby. Toto je definováno v souboru ServiceManifest.xml, která je integrována do adresáře balíčku služby. Adresář balíčku service se pak odkazuje balíčku aplikace *ApplicationManifest.xml* souboru. V rámci clusteru po vytvoření aplikace s názvem, můžete vytvořit pojmenovanou službu z jednoho z typů služeb typu aplikace. Typ služby je popsán v jeho *ServiceManifest.xml* souboru. Typ služby se skládá z nastavení konfigurace služby spustitelného kódu, které jsou načteny v době běhu, a statická data, která je využívána služba.

![Typy aplikací Service Fabric a typy služeb][cluster-imagestore-apptypes]

Balíček aplikace je disk adresáře, který obsahuje typ aplikace *ApplicationManifest.xml* soubor, který odkazuje na balíčky služeb pro každý typ služby, který vytvoří typ aplikace. Balíček aplikace pro typ e-mailové aplikace může například obsahovat odkazy na balíček služby front, balíček služby front-endu a balíček služby databáze. Soubory v adresáři balíčku aplikace se zkopírují do úložiště imagí clusteru Service Fabric. 

Balíček služby je disk adresáře, který obsahuje typ služby *ServiceManifest.xml* soubor, který odkazuje na kód, statických dat a balíčky pro konfiguraci pro typ služby. Soubory v adresáři balíčku služby odkazuje typ aplikace *ApplicationManifest.xml* souboru. Například může odkazovat na kód, statických dat a balíčky pro konfiguraci, které tvoří databázová služba balíčku služby.

### <a name="run-time-clusters-and-nodes-named-applications-named-services-partitions-and-replicas"></a>Čas spuštění: clustery a uzlů s názvem aplikace s názvem služby, oddíly a repliky
[Cluster Service Fabric](service-fabric-deploy-anywhere.md) je síťově propojená sada virtuálních nebo fyzických počítačů, ve které se nasazují a spravují mikroslužby. Clustery je možné škálovat na tisíce počítačů.

Počítač nebo virtuální počítač, který je součástí clusteru, se nazývá uzel. Každému uzlu je přiřazen název uzlu (řetězec). Uzly mají určité charakteristiky, například vlastnosti umístění. Každý počítač nebo virtuální počítač má Windows automatické spouštění služby `FabricHost.exe`, která se spustí při spuštění a spustí dvě spustitelné soubory: `Fabric.exe` a `FabricGateway.exe`. Tyto dvě spustitelné soubory tvoří uzlu. Pro vývoj nebo testování scénářů může hostovat více uzlů na jeden počítač nebo virtuální počítač spuštěním několika instancí `Fabric.exe` a `FabricGateway.exe`.

Pojmenované aplikace je kolekce s názvem služby, která provádí určitou funkci nebo funkce. Služba provádí kompletní a samostatné funkce (ji můžete spustit a spustit nezávisle na ostatních službách) a se skládá z kódu, konfigurace a data. Po zkopírování balíčku aplikace do úložiště imagí, vytvoříte instanci aplikace v rámci clusteru tak, že zadáte typ aplikace balíčku aplikace (pomocí jeho název/verze). Každá instance typu aplikace je přiřazen název identifikátoru URI, který může vypadat *fabric: / MyNamedApp*. V rámci clusteru můžete vytvořit více pojmenované aplikací z jedné aplikace typu. Můžete také vytvořit pojmenované aplikace z aplikace různé typy. Jednotlivé pojmenované aplikace je spravované a verzuje nezávisle na sobě.

Po vytvoření aplikace s názvem, můžete vytvořit instanci jednoho z jeho typy služeb (pojmenovanou službu) v rámci clusteru tak, že zadáte typ služby (s použitím jeho název/verze). Každá instance typu služby je přiřazen název identifikátoru URI oboru v identifikátoru URI s názvem aplikace. Například pokud vytvoříte "Databáze" s názvem služby v rámci "MyNamedApp" s názvem aplikace, identifikátor URI vypadá: *fabric: / MyNamedApp/databáze*. V rámci aplikace s názvem můžete vytvořit jednu nebo víc služeb s názvem. Počty instancí/replik a každou pojmenovanou službu může mít svůj vlastní schéma oddílu. 

Existují dva typy služeb: bezstavových a stavových. Bezstavové služby neukládejte stav ve službě. Bezstavové služby mít žádné trvalého úložiště na všech nebo ukládání trvalý stav ve službě externí úložiště, jako je například Azure Storage, Azure SQL Database nebo Azure Cosmos DB. Stavová služba ukládá stav ve službě a používá ke správě stavu Reliable Collections nebo Reliable Actors programovacích modelů. 

Při vytváření pojmenovanou službu, zadat schéma oddílu. Služby s velkým množstvím stavu rozdělení dat napříč oddíly. Každý oddíl je zodpovědná za část úplný stavu služby, která se pak rozdělí mezi uzly clusteru.  

Následující diagram znázorňuje vztah mezi aplikacemi a instancemi služby, oddíly a repliky.

![Oddílů a replik v rámci služby][cluster-application-instances]

### <a name="partitioning-scaling-and-availability"></a>Dělení, škálování a dostupnost
[Dělení](service-fabric-concepts-partitioning.md) není jedinečný pro Service Fabric. Dobře známé formě a dělení je dělení dat nebo horizontálního dělení. Stavové služby s velkým množstvím stavu rozdělení dat napříč oddíly. Každý oddíl je zodpovědná za část úplný stav služby. 

Repliky každý oddíl jsou rozděleny mezi uzly clusteru, který umožňuje pojmenovanou službu stavu na [škálování](service-fabric-concepts-scalability.md). Jak rostou potřeby data, oddíly růst a Service Fabric znovu vytvoří rovnováhu oddílů mezi uzly, aby efektivně využívat hardwarových prostředků. Pokud přidáte nové uzly do clusteru Service Fabric obnovit rovnováhu replik oddílů napříč zvýšeného počtu uzlů. Celkově zlepšuje výkon aplikací a snižuje kolize pro přístup k paměti. Pokud uzly v clusteru nejsou používány efektivně, můžete snížit počet uzlů v clusteru. Service Fabric znovu znovu vytvoří, rovnováhu replik oddílů napříč snížený počet uzlů, abyste měli lepší využití hardwaru na každém uzlu.

V rámci oddílu bezstavové služby s názvem mají instance stavové služby s názvem mají repliky. Bezstavové služby s názvem mají obvykle, vždy jen jeden oddíl, protože nemají žádné vnitřní stav. Instance oddílu poskytují pro [dostupnosti](service-fabric-availability-services.md). Pokud jedna instance selže, ostatní instance fungovat normálně dál a vytvoří novou instanci Service Fabric. Stavová s názvem služby spravovat, že jejich stav v rámci repliky a každý oddíl má svůj vlastní sady replik. Operace čtení a zápisu jsou prováděny jedna replika (označované jako primární). Změny stavu z zápisu operace se replikují do více replik (označované jako aktivní sekundární databáze). By měl replika selže, Service Fabric vytvoří novou repliku z existujících replik.

## <a name="stateless-and-stateful-microservices-for-service-fabric"></a>Bezstavové a stavové mikroslužby pro Service Fabric
Service Fabric umožňuje sestavovat aplikace, které se skládají z mikroslužeb nebo kontejnerů. Bezstavové mikroslužby (například brány protokolů a webové proxy) si mimo požadavek a odpověď ze služby neudržují měnitelný stav. Role pracovních procesů služby Azure Cloud Services jsou příkladem stavové služby. Stavové mikroslužby (například uživatelské účty, databáze, zařízení, nákupní košíky a fronty) si udržují měnitelný a autoritativní stav i mimo požadavek a odpověď. Dnešní aplikace v internetovém měřítku se skládají z kombinace bezstavových a stavových mikroslužeb. 

Odlišuje Service Fabric je silným zaměřením na sestavování stavových služeb, buď pomocí [integrovaným programovací modely ](service-fabric-choose-framework.md) nebo kontejnerizovaných stavových služeb. [Scénáře aplikací](service-fabric-application-scenarios.md) popisují scénáře, ve kterých se používají stavové služby.

Proč jste stavových mikroslužeb spolu s bezstavové ty? Dva hlavní důvody jsou:

* Můžete vytvářet vysokou propustnost, nízkou latenci, proti chybám online zpracování transakcí (OLTP) služby udržováním kód a data blízko sebe na stejném počítači. Některé příklady jsou interaktivní prodejní místa, vyhledávání, systémy Internet of Things (IoT), obchodní systémy, systémech zjišťování možných podvodů a zpracování platební karty a Správa osobních záznamů.
* Můžete zjednodušit návrh aplikace. Stavových mikroslužeb zbavují uživatele nutnosti pro další fronty a mezipamětí, které jsou obvykle požadovány pro splnění požadavků na dostupnost a latenci čistě bezstavové aplikace. Stavové služby jsou přirozeně vysokou dostupností a nízkou latencí, což snižuje počet pohyblivých částí pro správu ve vaší aplikaci jako celek.

## <a name="supported-programming-models"></a>Podporované programovací modely
Service Fabric nabízí několik způsobů, jak zapisovat a spravovat vaše služby. Služby můžou používat rozhraní API služby Service Fabric plně využít funkce platformy a aplikačních architektur. Služby mohou být také všechny zkompilovaný spustitelný program napsané v libovolném jazyce a hostovaná v clusteru Service Fabric. Další informace najdete v tématu [podporované programovací modely](service-fabric-choose-framework.md).

### <a name="containers"></a>Containers
Ve výchozím nastavení Service Fabric nasadí a aktivuje služby jako procesy. Service Fabric dokáže nasadit také služby v [kontejnery](service-fabric-containers-overview.md). Co je důležité můžete kombinovat služby v procesech a služby v kontejnerech ve stejné aplikaci. Service Fabric podporuje nasazování kontejnerů Linuxu a kontejnery Windows ve Windows serveru 2016. Můžete nasadit aplikace, bezstavové služby nebo stavové služby v kontejnerech. 

### <a name="reliable-services"></a>Reliable Services
[Reliable Services](service-fabric-reliable-services-introduction.md) je jednoduché rozhraní pro zápis služby, které integrace s platformou Service Fabric a využívat výhod celé sady funkcí platformy. Reliable Services může být bezstavové (podobně jako na většině platforem služby, jako jsou webové servery nebo role pracovních procesů v Azure Cloud Services), kde je trvalý stav ve externí řešení, jako je Azure DB nebo Azure Table Storage. Reliable Services může být stavový, kde je stav ukládaný přímo v samotné použitím spolehlivých kolekcí služby. Stav se provádí [s vysokou dostupností](service-fabric-availability-services.md) prostřednictvím replikace a distribuovaných prostřednictvím [dělení](service-fabric-concepts-partitioning.md), všechny spravované automaticky pomocí Service Fabric.

### <a name="reliable-actors"></a>Reliable Actors
Postavené na Reliable Services [Reliable Actors](service-fabric-reliable-actors-introduction.md) framework je aplikační rozhraní, která implementuje vzoru virtuálního objektu Actor, podle návrhu vzor objektu actor. Reliable Actors rozhraní framework používá nezávislých jednotek výpočetního výkonu a stavu s názvem actors spuštění s jedním vláknem. Reliable Actors, které poskytuje rozhraní framework integrovaným komunikace pro objekty actor a předem nastavení trvalosti a horizontální navýšení kapacity konfigurací stavu.

### <a name="aspnet-core"></a>Jádro ASP.NET
Service Fabric se integruje s [ASP.NET Core](service-fabric-reliable-services-communication-aspnetcore.md) jako první třídy programovací model pro vytváření webových a aplikacích API.  ASP.NET Core lze použít v Service Fabric dvěma různými způsoby:

- Hostovaný jako spustitelný soubor typu Host. To se používá především k provozování existujících aplikací ASP.NET Core v Service Fabric s žádnými změnami kódu.
- Spouští se uvnitř spolehlivé služby. To umožňuje lepší integraci s modulem runtime Service Fabric a stavové služby ASP.NET Core services.

### <a name="guest-executables"></a>Spustitelné soubory typu Host
A [spustitelný soubor typu Host](service-fabric-guest-executables-introduction.md) je existující, libovolný spustitelný soubor (napsané v libovolném jazyce) hostovaná v clusteru Service Fabric společně s další služby. Spustitelné soubory hosta není integraci přímo s rozhraními API pro Service Fabric. Ale jsou stále využívat funkce, které platforma nabízí, například vlastní stav a načítání, vytváření sestav a služby zjistitelnost pomocí volání rozhraní REST API. Mají také podporu životního cyklu celou aplikaci. 

## <a name="application-lifecycle"></a>Životní cyklus aplikace
Podle jiných platforem, aplikace v Service Fabric obvykle prochází následující fáze: návrh, vývoj, testování, nasazení, upgrade, údržbu a odebírání. Service Fabric poskytuje prvotřídní podporu pro celou aplikaci životního cyklu cloudových aplikací, od vývoje až po nasazení, každodenní správu a údržbu na případné vyřazení z provozu. Model služby umožňuje několik různých rolí se účastnit životního cyklu aplikací nezávisle na sobě. [Životní cyklus aplikace Service Fabric](service-fabric-application-lifecycle.md) obsahuje základní informace o rozhraní API a jak se používají v různých rolích v průběhu fáze životního cyklu aplikací Service Fabric. 

Životní cyklus celá aplikace je možné spravovat pomocí [rutin prostředí PowerShell](/powershell/module/ServiceFabric/), [příkazy rozhraní příkazového řádku](service-fabric-sfctl.md), [rozhraní API jazyka C#](/dotnet/api/system.fabric.fabricclient.applicationmanagementclient), [rozhraní Java API](/java/api/system.fabric._application_management_client), a [ Rozhraní REST API](/rest/api/servicefabric/). Můžete také nastavit průběžné integrace a nasazování kanálů pomocí nástroje, jako [kanály Azure](service-fabric-set-up-continuous-integration.md) nebo [Jenkins](service-fabric-cicd-your-linux-applications-with-jenkins.md).

Následující video Microsoft Virtual Academy popisuje, jak spravovat životní cyklus vaší aplikace: <center><a target="_blank" href="https://mva.microsoft.com/en-US/training-courses/building-microservices-applications-on-azure-service-fabric-16747?l=My3Ka56yC_6106218965">
<img src="./media/service-fabric-content-roadmap/AppLifecycleVid.png" WIDTH="360" HEIGHT="244">
</a></center>

## <a name="test-applications-and-services"></a>Testování aplikací a služeb
K vytvoření skutečně cloudových služeb, je velmi důležité ověřit, že vaše aplikace a služby dokázal skutečná selhání. Fault Analysis Service je určená pro testování služeb, které jsou vytvořené v Service Fabric. S [Fault Analysis Service](service-fabric-testability-overview.md), můžete zahájit smysluplné chyb a spouštějte scénáře dokončení testování vašich aplikací. Tyto chyby a scénáře výkon a ověřit řadu stavy a přechody, která službu budou moct používat v průběhu svého životního cyklu, všechny řízené, bezpečné a konzistentním způsobem.

[Akce](service-fabric-testability-actions.md) Cílová služba pro testování s využitím jednotlivých chyb. Služba vývojáři mohou využít jako stavební bloky pro zápis složité scénáře. Příklady simulované chyb:

* Restartujte uzel pro simulaci libovolný počet situacích, kdy po restartování počítače nebo virtuálního počítače.
* Přesunutí repliky stavové služby pro simulaci Vyrovnávání zatížení a převzetí služeb při selhání nebo upgrade aplikace.
* Vyvolání ztráty kvora na stavové služby k nastat situace, kdy operace zápisu nemůže pokračovat, protože nejsou k dispozici dostatek "zálohování" nebo "sekundární" repliky tak, aby přijímal nová data.
* Vyvolání ztráty dat na stavové služby k nastat situace, kdy všechny stavy v paměti je zcela dojde k vymazání.

[Scénáře](service-fabric-testability-scenarios.md) složité operace se skládají z jednoho nebo více akcí. Fault Analysis Service poskytuje dvě předdefinované kompletní scénáře:

* [Scénář Chaos](service-fabric-controlled-chaos.md)-simuluje průběžné, prokládané chyby (řádné i vynuceném) v rámci clusteru za dlouhou dobu.
* [Scénáře převzetí služeb při selhání](service-fabric-testability-scenarios.md#failover-test)– verzi chaos testovací scénáře, který cílí na konkrétní službu oddílu a dalším službám ponechání to neovlivní.

## <a name="clusters"></a>Clustery
[Cluster Service Fabric](service-fabric-deploy-anywhere.md) je síťově propojená sada virtuálních nebo fyzických počítačů, ve které se nasazují a spravují mikroslužby. Clustery je možné škálovat na tisíce počítačů. Počítač nebo virtuální počítač, který je součástí clusteru, se nazývá uzel clusteru. Každému uzlu je přiřazen název uzlu (řetězec). Uzly mají určité charakteristiky, například vlastnosti umístění. Každý počítač nebo virtuální počítač má automaticky spouštěná služba, `FabricHost.exe`, která se spustí při spuštění a spustí dvě spustitelné soubory: Fabric.exe a FabricGateway.exe. Tyto dvě spustitelné soubory tvoří uzlu. Pro testovací scénáře, může hostovat více uzlů na jeden počítač nebo virtuální počítač spuštěním několika instancí `Fabric.exe` a `FabricGateway.exe`.

Clustery Service Fabric se dají vytvořit na virtuálních nebo fyzických počítačích s Windows serverem nebo Linuxem. Máte možnost k nasazení a spuštění aplikace Service Fabric v jakémkoli prostředí, kam máte sadu Windows Server nebo Linux počítačů, které jsou propojeny: místně, v Microsoft Azure nebo na jakýkoli jiný poskytovatel cloudu.

Následující video Microsoft Virtual Academy popisuje clustery Service Fabric: <center><a target="_blank" href="https://mva.microsoft.com/en-US/training-courses/building-microservices-applications-on-azure-service-fabric-16747?l=tbuZM46yC_5206218965">
<img src="./media/service-fabric-content-roadmap/ClusterOverview.png" WIDTH="360" HEIGHT="244">
</a></center>

### <a name="clusters-on-azure"></a>Clustery v Azure
Clustery Service Fabric běžící v Azure poskytuje integraci se službou další funkce a služby Azure, takže operace a Správa clusteru jednodušší a spolehlivější. Cluster je prostředek Azure Resource Manageru, takže lze modelovat clustery podobně jako ostatní prostředky v Azure. Resource Manager také poskytuje snadnou správu všechny prostředky používané položkou clusteru jako jeden celek. Clustery v Azure jsou integrované s diagnostikou Azure a službou Log Analytics. Typy uzlů clusteru jsou [škálovací sady virtuálních počítačů](/azure/virtual-machine-scale-sets/index), takže je součástí funkcí automatického škálování.

Můžete vytvořit cluster v Azure prostřednictvím [webu Azure portal](service-fabric-cluster-creation-via-portal.md), z [šablony](service-fabric-cluster-creation-via-arm.md), nebo z [sady Visual Studio](service-fabric-cluster-creation-via-visual-studio.md).

Service Fabric v Linuxu umožňuje vytvářet, nasazovat a spravovat vysoce dostupné a vysoce škálovatelných aplikací v Linuxu, stejně jako ve Windows. Jsou k dispozici v jazyce Java v Linuxu, kromě jazyka C# (.NET Core) rozhraní Service Fabric (Reliable Services a Reliable Actors). Můžete také sestavit [spustitelný soubor služby hosta](service-fabric-guest-executables-introduction.md) s libovolný jazyk nebo rozhraní. Orchestrace kontejnerů Dockeru je také podporována. Kontejnery dockeru můžete spouštět spustitelné soubory hosta nebo nativními službami Service Fabric, které používají rozhraní Service Fabric. Další informace, přečtěte si informace o [Service Fabric v Linuxu](service-fabric-deploy-anywhere.md).

Existují některé funkce, které jsou podporované ve Windows, ale není v Linuxu. Další informace najdete v článku [rozdíly mezi Service Fabric v Linuxu a Windows](service-fabric-linux-windows-differences.md).

### <a name="standalone-clusters"></a>Samostatné clustery
Service Fabric nabízí instalační balíček si můžete vytvořit samostatné Service Fabric clustery v místním nebo jakýkoli jiný poskytovatel cloudu. Samostatné clustery získáte volný k hostování clusteru kdekoli budete chtít. Pokud vaše data jsou v souladu s dodržování předpisů nebo zákonných omezení nebo chcete zachovat místní data, můžete hostovat vlastní cluster a aplikace. Aplikace Service Fabric můžete spustit v prostředí s více hostitelských beze změn, tak svoje znalosti v oblasti vytváření aplikací, se přenesou z jednoho hostování prostředí do druhého. 

[Vytvoření vašeho prvního samostatného clusteru Service Fabric](service-fabric-cluster-creation-for-windows-server.md)

Samostatné clustery s Linuxem se zatím nepodporují.

### <a name="cluster-security"></a>Zabezpečení clusteru
Clustery musí být zabezpečená umožňují zabránit neoprávněným uživatelům v připojení ke clusteru, zejména v případě, že se v něm spuštěné úlohy v produkčním prostředí. I když je možné vytvořit nezabezpečený cluster, tak učiníte, umožníte anonymní uživatelé se k němu připojit, pokud jsou vystaveny koncových bodů správy do veřejného Internetu. Není možné později povolit zabezpečení na nezabezpečenému clusteru: při vytváření clusteru je povoleno zabezpečení clusteru.

Scénáře zabezpečení clusteru jsou:
* Zabezpečení mezi uzly
* Uzel klienta zabezpečení
* Řízení přístupu na základě role (RBAC)

Další informace najdete v článku [zabezpečení clusteru](service-fabric-cluster-security.md).

### <a name="scaling"></a>Škálování
Pokud chcete přidat nové uzly do clusteru, Service Fabric znovu vytvoří rovnováhu replik oddílů a instancí napříč zvýšeného počtu uzlů. Celkově zlepšuje výkon aplikací a snižuje kolize pro přístup k paměti. Pokud uzly v clusteru nejsou používány efektivně, můžete snížit počet uzlů v clusteru. Service Fabric znovu znovu vytvoří rovnováhu replik oddílů a instancí napříč snížený počet uzlů, abyste měli lepší využití hardwaru na každém uzlu. Clustery v Azure můžete škálovat buď [ručně](service-fabric-cluster-scale-up-down.md) nebo [programově](service-fabric-cluster-programmatic-scaling.md). Samostatné clustery je možné škálovat [ručně](service-fabric-cluster-windows-server-add-remove-nodes.md).

### <a name="cluster-upgrades"></a>Upgrade clusteru
Pravidelně se vydávají nové verze modulu runtime Service Fabric. Proveďte upgrade modulu runtime nebo z prostředků infrastruktury, ve vašem clusteru tak, aby vždy běží [podporovaná verze](service-fabric-support.md). Kromě toho upgrady prostředků infrastruktury můžete také aktualizovat konfiguraci clusteru, například certifikáty nebo porty aplikací.

Cluster Service Fabric je prostředek, který vlastníte, ale částečně spravovaného společností Microsoft. Společnost Microsoft zodpovídá za použití dílčích oprav základního operačního systému a provádění upgrady prostředků infrastruktury ve vašem clusteru. Můžete nastavit cluster pro příjem upgradů automatické prostředků infrastruktury, když společnost Microsoft vydává nové verze nebo verze podporovaných prostředků infrastruktury, který chcete vybrat. Upgrady prostředků infrastruktury a konfigurace je možné nastavit na webu Azure portal nebo prostřednictvím Resource Manageru. Další informace najdete v článku [Upgrade clusteru Service Fabric](service-fabric-cluster-upgrade.md). 

Samostatný cluster je prostředek, které zcela vlastní. Zodpovídáte za použití dílčích oprav základního operačního systému a inicializaci upgrady prostředků infrastruktury. Pokud váš cluster může připojit k [ https://www.microsoft.com/download ](https://www.microsoft.com/download), můžete nastavit cluster automaticky stáhnout a zřídit nový balíček modulu runtime Service Fabric. By potom zahájit upgrade. Pokud váš cluster nemá přístup k [ https://www.microsoft.com/download ](https://www.microsoft.com/download), můžete ručně stáhnout nový balíček modulu runtime z počítač připojený k Internetu a potom zahájit upgrade. Další informace najdete v článku [Upgrade samostatného clusteru Service Fabric](service-fabric-cluster-upgrade-windows-server.md).

## <a name="health-monitoring"></a>Monitorování stavu
Service Fabric představuje [modelu stavu](service-fabric-health-introduction.md) navržené tak, aby příznak není v pořádku, cluster a aplikace podmínek u konkrétních entit (jako jsou uzly clusteru a služba repliky). Health model používá reporters stavu (systémové součásti a watchdogs). Cílem je rychlou a snadnou diagnostiku a opravy. Služba zapisovače musí přemýšlet o stavu předem a jak [návrhu sestav stavu](service-fabric-report-health.md#design-health-reporting). Jakoukoli podmínku, která může mít vliv na stav by se měly hlásit, zejména v případě, že může být snazší příznak problémy blízko kořenový adresář. Informace o stavu můžete ušetřit čas a námahu při ladění a šetření jednou služba je spuštěná ve velkém měřítku v produkčním prostředí.

Monitor Service Fabric reporters identifikovat podmínky, které vás zajímají. Tyto sestavy tyto podmínky na základě jejich místní zobrazení. [Health store](service-fabric-health-introduction.md#health-store) agreguje data o stavu odeslaných všechny reporters k určení, zda entity jsou globálně v pořádku. Model je určen jako bohaté, flexibilní a snadno se používá. Určuje kvalitu sestav o stavu přesností zobrazení stavu clusteru. Falešně pozitivních shod nesprávně zobrazit není v pořádku, problémy mohou mít negativní vliv na upgrady nebo jiné služby, které používají data o stavu. Příkladem takové služby jsou opravy služeb a mechanismy upozorňování. Proto je uvažujete potřebných k poskytování sestavy, které zaznamenávají podmínky zájem optimálně.

Generování sestav můžete provést ve:
* Nemonitorované repliku služby Service Fabric nebo instance.
* Interní watchdogs nasadit jako službu Service Fabric (například Service Fabric bezstavovou službu, která monitoruje podmínky a vydá sestavy). Watchdogs se dají nasadit na všechny uzly, nebo můžete spřažené s monitorované služby.
* Interní watchdogs spuštění na uzlech Service Fabric, které nejsou implementované jako služeb Service Fabric.
* Externí watchdogs, které pro zjišťování prostředků z mimo cluster Service Fabric (například monitorování službě, jako je Gomez).

Komponenty Service Fabric hned po spuštění sestavy stavu všech entit v clusteru. [Systémovými stavovými sestavami](service-fabric-understand-and-troubleshoot-with-system-health-reports.md) poskytují vhled do aplikací a clusteru a příznak funkce problémů prostřednictvím služby health. Pro aplikace a služby systémovými stavovými sestavami ověřte, že entity jsou implementovány a chovají správně z hlediska modulu runtime Service Fabric. Sestavy poskytují jakékoli sledování stavu obchodní logiky služby ani detekovat ukončování "zamrzlých" procesů. Chcete-li přidat konkrétní informace o stavu pro vaši službu logic [implementaci vlastních stavových sestav](service-fabric-report-health.md) ve vašich službách.

Service Fabric poskytuje několik způsobů [zobrazení sestav health](service-fabric-view-entities-aggregated-health.md) agregovat v health store:
* [Service Fabric Explorer](service-fabric-visualizing-your-cluster.md) nebo jiné nástroje pro vizualizaci.
* Dotazy na stav (prostřednictvím [PowerShell](/powershell/module/ServiceFabric/), [rozhraní příkazového řádku](service-fabric-sfctl.md), [rozhraní API FabricClient C#](/dotnet/api/system.fabric.fabricclient.healthclient) a [rozhraní API FabricClient Java](/java/api/system.fabric), nebo [REST Rozhraní API](/rest/api/servicefabric)).
* Obecné dotazy to návratový seznam entit, které mají stav jako jedna z vlastností (prostřednictvím prostředí PowerShell, rozhraní příkazového řádku, rozhraní API nebo REST).

Následující video Microsoft Virtual Academy popisuje model stavu Service Fabric a jak se používá: <center><a target="_blank" href="https://mva.microsoft.com/en-US/training-courses/building-microservices-applications-on-azure-service-fabric-16747?l=tevZw56yC_1906218965">
<img src="./media/service-fabric-content-roadmap/HealthIntroVid.png" WIDTH="360" HEIGHT="244">
</a></center>

## <a name="monitoring-and-diagnostics"></a>Monitorování a diagnostika
[Monitorování a Diagnostika](service-fabric-diagnostics-overview.md) jsou důležité pro vývoj, testování a nasazení aplikací a služeb v jakémkoli prostředí. Service Fabric řešení fungují lépe, když plánování a implementace monitorování a diagnostiky, které pomáhají zajistit aplikace a služby fungují podle očekávání v místním vývojovém prostředí nebo v produkčním prostředí.

Je hlavních cílů monitorování a diagnostiku:

- Detekujte a Diagnostikujte problémy hardwaru a infrastruktury
- Rozpoznávání problémů softwaru a aplikace, zkrátit výpadky služby
- Vysvětlení prostředků spotřeby a nápovědu operace rozhodovat
- Optimalizace výkonu aplikace, služby a infrastruktura
- Generovat obchodní přehledy a identifikujte oblasti zlepšování
 
Celkový pracovní postup monitorování a diagnostiku se skládá ze tří kroků:

1. Generování události: Jedná se o událostí (protokoly, trasování, vlastní události) na infrastrukturu (clusteru), platformy a aplikaci nebo úroveň
2. Agregace událostí: vygenerovaných událostí potřeba shromažďovat a agregovat předtím, než je možné zobrazit
3. Analýza: události musí být vizualizovaný a v některých formát pro povolení pro analýzu a zobrazit podle potřeby k dispozici

Více produkty, které jsou k dispozici, která pokrývají tyto tři oblasti, a můžete libovolně vybrat různé technologie pro všechny. Další informace najdete v článku [monitorování a diagnostiky pro Azure Service Fabric](service-fabric-diagnostics-overview.md).

## <a name="next-steps"></a>Další postup
* Naučte se vytvořit [cluster v Azure](service-fabric-cluster-creation-via-portal.md) nebo [samostatný clusteru ve Windows](service-fabric-cluster-creation-for-windows-server.md).
* Zkuste si vytvořit službu pomocí programovacího modelu [Reliable Services](service-fabric-reliable-services-quick-start.md) nebo [Reliable Actors](service-fabric-reliable-actors-get-started.md).
* Zjistěte, jak [migrace ze služby Cloud Services](service-fabric-cloud-services-migration-differences.md).
* Zjistěte, jak [monitorování a Diagnostika služeb](service-fabric-diagnostics-how-to-monitor-and-diagnose-services-locally.md). 
* Zjistěte, jak [testování vašich aplikací a služeb](service-fabric-testability-overview.md).
* Zjistěte, jak [Správa a Orchestrace prostředků clusteru](service-fabric-cluster-resource-manager-introduction.md).
* Projít [ukázek Service Fabric](http://aka.ms/servicefabricsamples).
* Další informace o [možnosti podpory pro Service Fabric](service-fabric-support.md).
* Přečtěte si [blogu týmu](https://blogs.msdn.microsoft.com/azureservicefabric/) pro články a oznámení.


[cluster-application-instances]: media/service-fabric-content-roadmap/cluster-application-instances.png
[cluster-imagestore-apptypes]: ./media/service-fabric-content-roadmap/cluster-imagestore-apptypes.png
