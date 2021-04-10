---
title: Další informace o Azure Service Fabric
description: Přečtěte si o základních konceptech a hlavních oblastech Azure Service Fabric. Poskytuje rozšířený přehled Service Fabric a vytváření mikroslužeb.
ms.topic: conceptual
ms.date: 12/08/2017
ms.openlocfilehash: 6e5a7445706a6e7842822ec784a64555d9226923
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/30/2021
ms.locfileid: "105727650"
---
# <a name="so-you-want-to-learn-about-service-fabric"></a>Chcete se dozvědět o Service Fabric?
Azure Service Fabric je platforma distribuovaných systémů usnadňující balení, nasazování a spravování škálovatelných a spolehlivých mikroslužeb.  Service Fabric má ale velkou oblast povrchu, ale máme spoustu informací.  Tento článek obsahuje stručný přehled Service Fabric a popisuje základní koncepty, programovací modely, životní cyklus aplikací, testování, clustery a monitorování stavu. Přečtěte si [Přehled](service-fabric-overview.md) a [co jsou mikroslužby?](service-fabric-overview-microservices.md) Úvod do Service Fabric, jak se dá použít k vytváření mikroslužeb. Tento článek neobsahuje úplný seznam obsahu, ale obsahuje odkaz na přehled a zahájení práce pro každou oblast Service Fabric. 

## <a name="core-concepts"></a>Základní koncepty
[Service Fabric terminologie](service-fabric-technical-overview.md), [aplikační model](service-fabric-application-model.md)a [podporované programovací modely](service-fabric-choose-framework.md) poskytují více konceptů a popisů, ale tady jsou základy.

### <a name="design-time-service-type-service-package-and-manifest-application-type-application-package-and-manifest"></a>Doba návrhu: typ služby, balíček služby a manifest, typ aplikace, balíček aplikace a manifest
Typ služby je název/verze přiřazená k balíčkům kódu služby, datovým balíčkům a konfiguračním balíčkům. Tato definice je definována v souboru ServiceManifest.xml. Typ služby se skládá ze spustitelného kódu a konfiguračního nastavení služby, které jsou načteny za běhu, a statických dat, která služba spotřebovává.

Balíček služby je adresář disku, který obsahuje ServiceManifest.xml soubor typu služby, který odkazuje na kód, statická data a konfigurační balíčky pro typ služby. Balíček služby může například odkazovat na kód, statická data a konfigurační balíčky, které tvoří databázovou službu.

Typ aplikace je název/verze přiřazená kolekci typů služeb. Tato definice je definována v souboru ApplicationManifest.xml.

![Service Fabric typů aplikací a typů služeb][cluster-imagestore-apptypes]

Balíček aplikace je adresář disku, který obsahuje soubor ApplicationManifest.xml typu aplikace, který odkazuje na balíčky služeb pro každý typ služby, který tvoří typ aplikace. Například balíček aplikace pro typ e-mailové aplikace může obsahovat odkazy na balíček služby front, front-end Service a balíček databázové služby.  

Soubory v adresáři balíčku aplikace se zkopírují do úložiště imagí Service Fabricho clusteru. Pak můžete vytvořit pojmenovanou aplikaci z tohoto typu aplikace, která pak bude spuštěna v rámci clusteru. Po vytvoření pojmenované aplikace můžete vytvořit pojmenovanou službu z některého z typů služeb typu aplikace. 

### <a name="run-time-clusters-and-nodes-named-applications-named-services-partitions-and-replicas"></a>Čas spuštění: clustery a uzly, pojmenované aplikace, pojmenované služby, oddíly a repliky
[Cluster Service Fabric](service-fabric-deploy-anywhere.md) je sada virtuálních nebo fyzických počítačů připojených k síti, do kterých se vaše mikroslužby nasazují a spravují. Clustery je možné škálovat na tisíce počítačů.

Počítač nebo virtuální počítač, který je součástí clusteru, se nazývá uzel. Každému uzlu je přiřazen název uzlu (řetězec). Uzly mají určité charakteristiky, například vlastnosti umístění. Každý počítač nebo virtuální počítač má automaticky spuštěnou službu systému Windows, `FabricHost.exe` která při spuštění spustí spuštění a pak spustí dva spustitelné soubory: `Fabric.exe` a `FabricGateway.exe` . Tyto dva spustitelné soubory tvoří uzel. Pro scénáře vývoje nebo testování můžete hostovat více uzlů na jednom počítači nebo VIRTUÁLNÍm počítači spuštěním několika instancí `Fabric.exe` a `FabricGateway.exe` .

Pojmenovaná aplikace je kolekce pojmenovaných služeb, které provádějí určitou funkci nebo funkce. Služba provádí úplnou a samostatnou funkci (může spustit a spustit nezávisle na jiných službách) a skládá se z kódu, konfigurace a dat. Po zkopírování balíčku aplikace do úložiště imagí vytvoříte instanci aplikace v rámci clusteru tak, že zadáte typ aplikace v balíčku aplikace (pomocí jejího názvu nebo verze). Každé instanci typu aplikace je přiřazen název identifikátoru URI, který vypadá jako *Fabric:/MyNamedApp*. V rámci clusteru můžete vytvořit více pojmenovaných aplikací z jednoho typu aplikace. Můžete také vytvořit pojmenované aplikace z různých typů aplikací. Každá pojmenovaná aplikace je spravovaná a má nezávisle.

Po vytvoření pojmenované aplikace můžete v clusteru vytvořit instanci jednoho z jejích typů služeb (pojmenovaná služba), a to zadáním typu služby (pomocí jejího názvu nebo verze). Každá instance typu služby má přiřazený název identifikátoru URI v oboru názvů IDENTIFIKÁTORu URI pojmenované aplikace. Například pokud vytvoříte "MyDatabase" pojmenovanou službu v rámci "MyNamedApp" pojmenované aplikace, identifikátor URI vypadá jako: *Fabric:/MyNamedApp/MyDatabase*. V rámci pojmenované aplikace můžete vytvořit jednu nebo více pojmenovaných služeb. Každá pojmenovaná služba může mít vlastní schéma oddílů a počty instancí nebo replik. 

Existují dva typy služeb: bezstavové a stavové. Bezstavové služby neukládají ve službě stav. Bezstavové služby neobsahují trvalé úložiště vůbec nebo ukládají trvalý stav do externí služby úložiště, jako je Azure Storage, Azure SQL Database nebo Azure Cosmos DB. Stavová služba ukládá stav v rámci služby a používá spolehlivé kolekce nebo Reliable Actors programovacích modelů pro správu stavu. 

Při vytváření pojmenované služby určíte schéma oddílu. Služby s velkým množstvím stavu rozdělí data napříč oddíly. Každý oddíl zodpovídá za část kompletního stavu služby, která je rozdělená mezi uzly clusteru.  

Následující diagram znázorňuje vztah mezi aplikacemi a instancemi služby, oddíly a replikami.

![Oddíly a repliky v rámci služby][cluster-application-instances]

### <a name="partitioning-scaling-and-availability"></a>Dělení, škálování a dostupnost
[Dělení](service-fabric-concepts-partitioning.md) není pro Service Fabric jedinečné. Dobře známá forma dělení je data partitions nebo horizontálního dělení. Stavové služby s velkým objemem stavu rozdělí data napříč oddíly. Každý oddíl zodpovídá za část kompletního stavu služby. 

Repliky jednotlivých oddílů jsou rozloženy v uzlech clusteru, což umožňuje [škálovat](service-fabric-concepts-scalability.md)stav pojmenované služby. Vzhledem k rostoucímu množství dat, zvětšování oddílů a Service Fabric přerovnává oddíly mezi uzly, aby bylo možné efektivně využívat hardwarové prostředky. Pokud do clusteru přidáte nové uzly, Service Fabric bude znovu vyrovnávat repliky oddílů v rámci většího počtu uzlů. Celkový výkon aplikace vylepšuje a kolizí pro přístup k snížení velikosti paměti. Pokud se uzly v clusteru nepoužívají efektivně, můžete snížit počet uzlů v clusteru. Service Fabric znovu vyrovnává repliky oddílů napříč sníženým počtem uzlů, aby bylo možné lépe využívat hardware na každém uzlu.

V rámci oddílu mají instance bez příslušnosti instance, zatímco stavová služba má repliky. Obvykle pouze bezstavové služby s názvem mají vždy jeden oddíl, protože nemají žádný interní stav, i když [existují výjimky](./service-fabric-concepts-partitioning.md#partition-service-fabric-stateless-services). Instance oddílu poskytují [dostupnost](service-fabric-availability-services.md). Pokud dojde k chybě jedné instance, ostatní instance budou fungovat normálně a pak Service Fabric vytvoří novou instanci. Stavové pojmenované služby udržují stav v rámci replik a každý oddíl má vlastní sadu replik. Operace čtení a zápisu se provádějí v jedné replice (označované jako primární). Změny stavu z operací zápisu se replikují do více dalších replik (označované jako aktivní sekundární). Pokud by se replika nezdařila, Service Fabric vytvoří novou repliku z existujících replik.

## <a name="stateless-and-stateful-microservices-for-service-fabric"></a>Bezstavové a stavové mikroslužby pro Service Fabric
Service Fabric umožňuje sestavovat aplikace, které se skládají z mikroslužeb nebo kontejnerů. Bezstavové mikroslužby (například brány protokolů a webové proxy) si mimo požadavek a odpověď ze služby neudržují měnitelný stav. Role pracovních procesů služby Azure Cloud Services jsou příkladem stavové služby. Stavové mikroslužby (například uživatelské účty, databáze, zařízení, nákupní košíky a fronty) si udržují měnitelný a autoritativní stav i mimo požadavek a odpověď. Dnešní aplikace v internetovém měřítku se skládají z kombinace bezstavových a stavových mikroslužeb. 

Klíčovým rozlišením Service Fabric je jeho silný fokus při vytváření stavových služeb, a to buď pomocí [vestavěných programovacích modelů](service-fabric-choose-framework.md) , nebo s využitím kontejnerových stavových služeb. [Scénáře aplikací](service-fabric-application-scenarios.md) popisují scénáře, ve kterých se používají stavové služby.

Proč mají stavové mikroslužby spolu s bezstavovým mikroslužbami? Existují dva hlavní důvody:

* Můžete vytvořit vysoce propustnost, nízkou latenci, neOLTPé služby online zpracování transakcí (), a to tak, že zachováte kód a data blízko na stejném počítači. Některé příklady jsou interaktivní prodejní místa, hledání, Internet věcí (IoT) systémy, obchodní systémy, zpracování kreditních karet a systémy pro detekci podvodů a správa osobních záznamů.
* Můžete zjednodušit návrh aplikace. Stavové mikroslužby odstraňují nutnost dalších front a mezipamětí, které jsou tradičně nutné pro řešení požadavků na dostupnost a latenci čistě bezstavových aplikací. Stavové služby jsou přirozeně vysokou dostupností a s nízkou latencí, což snižuje počet přesouvaných částí, které se mají ve vaší aplikaci spravovat jako celek.

## <a name="supported-programming-models"></a>Podporované programovací modely
Service Fabric nabízí několik způsobů, jak psát a spravovat vaše služby. Služby mohou používat rozhraní Service Fabric API k plnému využití funkcí platformy a architektur aplikací. Služby také mohou být všechny zkompilované spustitelné programy napsané v jakémkoli jazyce a jsou hostovány v clusteru Service Fabric. Další informace najdete v tématu [podporované programovací modely](service-fabric-choose-framework.md).

### <a name="containers"></a>Kontejnery
Ve výchozím nastavení Service Fabric nasadí a aktivuje služby jako procesy. Service Fabric mohou také nasazovat služby v [kontejnerech](service-fabric-containers-overview.md). Důležité je, že můžete kombinovat služby v procesech a službách v kontejnerech ve stejné aplikaci. Service Fabric podporuje nasazení kontejnerů Linux a kontejnerů Windows v systému Windows Server 2016. V kontejnerech můžete nasadit existující aplikace, bezstavové služby nebo stavové služby. 

### <a name="reliable-services"></a>Reliable Services
[Reliable Services](service-fabric-reliable-services-introduction.md) je zjednodušená architektura pro psaní služeb, které se integrují s Service Fabricou platformou a využívají kompletní sadu funkcí platformy. Reliable Services můžou být Bezstavová (podobně jako u většiny platforem služeb, jako jsou webové servery nebo role pracovního procesu ve službě Azure Cloud Services), kde je stav uložený v externím řešení, jako je Azure DB nebo Azure Table Storage. Reliable Services může být také stavová, kde je stav uložen přímo ve službě samotné pomocí spolehlivých kolekcí. Stav je [vysoce dostupný](service-fabric-availability-services.md) prostřednictvím replikace a distribuuje se prostřednictvím [dělení](service-fabric-concepts-partitioning.md), a to vše spravované automaticky pomocí Service Fabric.

### <a name="reliable-actors"></a>Reliable Actors
Technologie [Reliable Actors](service-fabric-reliable-actors-introduction.md) , která je postavená na Reliable Services, je rozhraní aplikace, které implementuje vzor virtuálního objektu actor na základě vzoru návrhu objektu actor. Rozhraní Reliable actor používá nezávislé jednotky COMPUTE a State s jedním vláknovým spouštěním nazvaným Actors. Rozhraní Reliable Actors poskytuje vestavěnou komunikaci pro objekty actor a trvalá nastavení trvalých stavů a škálování na více systémů.

### <a name="aspnet-core"></a>ASP.NET Core
Service Fabric se integruje s [ASP.NET Core](service-fabric-reliable-services-communication-aspnetcore.md) jako první třída programovací model pro vytváření webových aplikací a aplikací API.  ASP.NET Core lze použít dvěma různými způsoby v Service Fabric:

- Hostuje se jako spustitelný soubor hosta. Používá se hlavně ke spouštění existujících aplikací ASP.NET Core v Service Fabric bez jakýchkoli změn kódu.
- Spouštějte v rámci spolehlivé služby. To umožňuje lepší integraci s Service Fabric runtime a umožňuje stavové služby ASP.NET Core.

### <a name="guest-executables"></a>Spustitelné soubory typu Host
[Spustitelný soubor hosta](service-fabric-guest-executables-introduction.md) je existující libovolný spustitelný soubor (napsaný v jakémkoli jazyce) hostovaný na Service Fabricm clusteru spolu s dalšími službami. Spustitelné soubory hosta se neintegrují přímo s Service Fabric rozhraní API. Stále však využívají funkce, které nabízí platforma, jako je například vlastní stav, generování sestav a zjistitelnost služeb voláním rozhraní REST API. Mají také úplnou podporu životního cyklu aplikací. 

## <a name="application-lifecycle"></a>Životní cyklus aplikace
Stejně jako u jiných platforem aplikace na Service Fabric obvykle prochází následujícími fázemi: návrh, vývoj, testování, nasazení, upgrade, údržba a odebrání. Service Fabric poskytuje prvotřídní podporu pro plný životní cyklus aplikací cloudových aplikací, od vývoje prostřednictvím nasazení, každodenní správy a údržby až po případné vyřazení z provozu. Model služby umožňuje, aby se v životním cyklu aplikace nezávisle účastnilo několik různých rolí. [Service Fabric životní cyklus aplikace](service-fabric-application-lifecycle.md) poskytuje přehled rozhraní API a způsob jejich používání různými rolemi v průběhu fáze životního cyklu aplikace Service Fabric. 

Celý životní cyklus aplikace se dá spravovat pomocí [rutin PowerShellu](/powershell/module/servicefabric/?preserve-view=true&view=azureservicefabricps), [příkazů CLI](service-fabric-sfctl.md), [rozhraní API jazyka C#](/dotnet/api/system.fabric.fabricclient.applicationmanagementclient), [rozhraní API Java](/java/api/overview/azure/servicefabric)a [rozhraní REST API](/rest/api/servicefabric/). Kanály průběžné integrace a průběžného nasazování můžete také nastavit pomocí nástrojů, jako je [Azure Pipelines](./service-fabric-tutorial-deploy-app-with-cicd-vsts.md) nebo [Jenkinse](/azure/developer/jenkins/deploy-to-service-fabric-cluster).

## <a name="test-applications-and-services"></a>Testování aplikací a služeb
Aby bylo možné vytvářet vysoce škálovatelné služby, je důležité ověřit, že vaše aplikace a služby můžou naodolat chybám reálného světa. Služba analýzy chyb je navržena pro testování služeb, které jsou postaveny na Service Fabric. Pomocí [služby analýzy chyb](service-fabric-testability-overview.md)můžete navolávat smysluplné chyby a spouštět kompletní testovací scénáře pro vaše aplikace. Tyto chyby a scénáře cvičení a ověřují množství stavů a přechodů, ke kterým dojde v průběhu své životnosti, a to vše v kontrolovaném, bezpečném a konzistentním způsobem.

[Akce](service-fabric-testability-actions.md) cílí na službu pro testování pomocí jednotlivých chyb. Vývojář služby může tyto prvky použít jako stavební bloky pro psaní složitých scénářů. Příklady simulovaných chyb:

* Restartujte uzel, aby se simuloval libovolný počet situací, kdy se počítač nebo virtuální počítač restartuje.
* Přesuňte repliku stavové služby pro simulaci vyrovnávání zatížení, převzetí služeb při selhání nebo upgradu aplikace.
* Vyvolat ztrátu kvora pro stavovou službu a vytvořit situaci, kdy operace zápisu nemohou pokračovat, protože pro příjem nových dat nejsou k dispozici dostatek záložních a sekundárních replik.
* Vyvolat ztrátu dat u stavové služby, která vytvoří situaci, kdy je stav veškerého stavu v paměti zcela vymazán.

[Scénáře](service-fabric-testability-scenarios.md) jsou komplexní operace složené z jedné nebo více akcí. Služba analýzy chyb nabízí dva předdefinované scénáře:

* [Scénář chaos](service-fabric-controlled-chaos.md)– simuluje nepřetržité, prokládané chyby (jak v rámci celého clusteru, tak i nekonečno) po delší dobu.
* [Scénář převzetí služeb při selhání](service-fabric-testability-scenarios.md#failover-test)– verze scénáře testu chaos, která cílí na konkrétní oddíl služby a zároveň ponechává jiné služby, které neovlivní.

## <a name="clusters"></a>Clustery
[Cluster Service Fabric](service-fabric-deploy-anywhere.md) je sada virtuálních nebo fyzických počítačů připojených k síti, do kterých se vaše mikroslužby nasazují a spravují. Clustery je možné škálovat na tisíce počítačů. Počítač nebo virtuální počítač, který je součástí clusteru, se označuje jako uzel clusteru. Každému uzlu je přiřazen název uzlu (řetězec). Uzly mají určité charakteristiky, například vlastnosti umístění. Každý počítač nebo virtuální počítač má automaticky spuštěnou službu, `FabricHost.exe` která při spuštění spustí spuštění, a potom spustí dva spustitelné soubory: Fabric.exe a FabricGateway.exe. Tyto dva spustitelné soubory tvoří uzel. V případě testovacích scénářů můžete hostovat více uzlů na jednom počítači nebo VIRTUÁLNÍm počítači spuštěním více instancí `Fabric.exe` a `FabricGateway.exe` .

Clustery Service Fabric lze vytvořit na virtuálních nebo fyzických počítačích se systémem Windows Server nebo Linux. Můžete nasazovat a spouštět aplikace Service Fabric v jakémkoli prostředí, kde máte připojenou sadu počítačů se systémem Windows Server nebo Linux, které jsou propojeny: místně, v Microsoft Azure nebo na jakémkoli poskytovateli cloudu.

### <a name="clusters-on-azure"></a>Clustery v Azure
Spuštění Service Fabric clusterů v Azure zajišťuje integraci s dalšími funkcemi a službami Azure, což usnadňuje a spolehlivuje provoz a správu clusteru. Cluster je prostředek Azure Resource Manager, takže můžete modelovat clustery stejně jako jakékoli jiné prostředky v Azure. Správce prostředků taky umožňuje snadnou správu všech prostředků používaných clusterem jako jedné jednotky. Clustery v Azure jsou integrované s protokoly diagnostiky a Azure Monitor Azure. Typy uzlů clusteru jsou [sady škálování virtuálních počítačů](../virtual-machine-scale-sets/index.yml), takže funkce automatického škálování je integrovaná.

Cluster v Azure můžete vytvořit prostřednictvím [Azure Portal](service-fabric-cluster-creation-via-portal.md), ze [šablony](service-fabric-cluster-creation-via-arm.md)nebo ze sady [Visual Studio](./service-fabric-cluster-creation-via-arm.md).

Service Fabric v systému Linux vám umožňuje sestavovat, nasazovat a spravovat vysoce dostupné a vysoce škálovatelné aplikace na platformě Linux stejným způsobem jako ve Windows. Rozhraní Service Fabric (Reliable Services a Reliable Actors) jsou kromě C# (.NET Core) k dispozici v jazyce Java v systému Linux. Můžete také vytvořit [hostované služby hosta](service-fabric-guest-executables-introduction.md) s libovolným jazykem nebo architekturou. Podporují se taky orchestrace kontejnerů Docker. Kontejnery Docker můžou spouštět spustitelné soubory hosta nebo nativní Service Fabric služby, které používají Service Fabric architektury. Další informace najdete v článku o [Service Fabric v systému Linux](service-fabric-deploy-anywhere.md).

Existují některé funkce, které jsou ve Windows podporované, ale ne v systému Linux. Pokud se chcete dozvědět víc, přečtěte si [rozdíly mezi Service Fabric v systémech Linux a Windows](service-fabric-linux-windows-differences.md).

### <a name="standalone-clusters"></a>Samostatné clustery
Service Fabric poskytuje instalační balíček, který vám umožní vytvořit samostatné Service Fabric clustery v místním prostředí nebo v jakémkoli poskytovateli cloudu. Samostatné clustery poskytují volnost hostování clusteru kdekoli, kde chcete. Pokud jsou vaše data předmětem dodržování předpisů nebo regulativních omezení nebo chcete zachovat místní data, můžete hostovat svůj vlastní cluster a aplikace. Aplikace Service Fabric mohou běžet v několika hostitelských prostředích bez jakýchkoli změn, takže vaše znalosti sestavování aplikací přecházejí z jednoho hostitelského prostředí do jiného. 

[Vytvoření vašeho prvního samostatného clusteru Service Fabric](service-fabric-cluster-creation-for-windows-server.md)

Samostatné clustery se systémem Linux zatím nejsou podporovány.

### <a name="cluster-security"></a>Zabezpečení clusteru
Clustery musí být zabezpečené, aby se zabránilo neautorizovaným uživatelům v připojení ke clusteru, zejména v případě, že na něm běží provozní zatížení. I když je možné vytvořit nezabezpečený cluster, umožní vám to anonymním uživatelům připojovat se k němu, pokud jsou koncové body správy zpřístupněny veřejnému Internetu. Nelze později povolit zabezpečení u nezabezpečeného clusteru: v době vytváření clusteru je povoleno zabezpečení clusteru.

Scénáře zabezpečení clusteru jsou tyto:
* Zabezpečení mezi uzly
* Zabezpečení klient-uzel
* Service Fabric řízení přístupu na základě role

Další informace najdete v článku [zabezpečení clusteru](service-fabric-cluster-security.md).

### <a name="scaling"></a>Škálování
Pokud do clusteru přidáte nové uzly, Service Fabric znovu vyrovnává repliky oddílů a instance napříč rostoucím počtem uzlů. Celkový výkon aplikace vylepšuje a kolizí pro přístup k snížení velikosti paměti. Pokud se uzly v clusteru nepoužívají efektivně, můžete snížit počet uzlů v clusteru. Service Fabric znovu vyrovnává repliky oddílů a instance napříč sníženým počtem uzlů, aby bylo možné lépe využívat hardware na jednotlivých uzlech. Clustery v Azure můžete škálovat buď [ručně](service-fabric-cluster-scale-in-out.md) , nebo [prostřednictvím kódu programu](service-fabric-cluster-programmatic-scaling.md). Samostatné clustery je možné škálovat [ručně](service-fabric-cluster-windows-server-add-remove-nodes.md).

### <a name="cluster-upgrades"></a>Upgrady clusteru
Nové verze modulu runtime Service Fabric jsou pravidelně vydávány. Proveďte za běhu nebo prostředků infrastruktury, upgrady v clusteru, abyste vždy používali [podporovanou verzi](service-fabric-support.md). Kromě upgradů prostředků infrastruktury můžete také aktualizovat konfiguraci clusteru, jako jsou certifikáty nebo porty aplikací.

Cluster Service Fabric je prostředek, který vlastníte, ale částečně ho spravuje Microsoft. Společnost Microsoft zodpovídá za opravy základního operačního systému a provádění upgradů prostředků infrastruktury ve vašem clusteru. Cluster můžete nastavit tak, aby přijímal automatické upgrady prostředků infrastruktury, když společnost Microsoft vydává novou verzi, nebo zvolit podporovanou verzi prostředků infrastruktury, kterou požadujete. Upgrady infrastruktury a konfigurace je možné nastavit prostřednictvím Azure Portal nebo prostřednictvím Správce prostředků. Další informace najdete v článku [Upgrade Service Fabric clusteru](service-fabric-cluster-upgrade.md). 

Samostatný cluster je prostředek, který zcela vlastníte. Zodpovídáte za opravy základního operačního systému a zahájení upgradu prostředků infrastruktury. Pokud se váš cluster může připojit k [https://www.microsoft.com/download](https://www.microsoft.com/download) , můžete nastavit svůj cluster tak, aby automaticky stahoval a zřídil nový balíček Service Fabric runtime. Pak byste měli zahájit upgrade. Pokud váš cluster nemá přístup [https://www.microsoft.com/download](https://www.microsoft.com/download) , můžete nový balíček modulu runtime stáhnout ručně z počítače připojeného k Internetu a pak zahájit upgrade. Pokud chcete získat další informace, přečtěte si téma [upgrade samostatného Service Fabricho clusteru](service-fabric-cluster-upgrade-windows-server.md).

## <a name="health-monitoring"></a>Monitorování stavu
Service Fabric zavádí [model stavu](service-fabric-health-introduction.md) navržený tak, aby na konkrétní entity (například uzly clusteru a repliky služeb) nemohly označovat podmínky pro clustery, které nejsou v pořádku. Model stavu používá sestavy stavu (systémové součásti a sledovací zařízení). Cílem je snadno a rychle diagnostikovat a opravit. Zapisovače služeb se musí představit předem ohledně stavu a [navrhovat vytváření sestav o stavu](service-fabric-report-health.md#design-health-reporting). Všechny podmínky, které mohou ovlivnit stav, by měly být hlášeny, zejména v případě, že mohou přispět k potížím s příznakem blízko ke kořenu. Informace o stavu mohou ušetřit čas a úsilí při ladění a vyšetřování, jakmile je služba v provozu a v produkčním měřítku probíhá.

Service Fabric sestavy monitorují identifikované podmínky zájmu. Vykazují tyto podmínky na základě jejich místního zobrazení. [Health Store](service-fabric-health-introduction.md#health-store) agreguje data o stavu odesílaná všemi sestavami, aby zjistila, jestli jsou entity globálně v pořádku. Model má být bohatě, flexibilní a snadno použitelný. Kvalita sestav stavu určuje přesnost zobrazení stavu clusteru. Falešné kladné hodnoty, které nesprávně ukazují problémy ve špatném stavu, můžou mít negativní dopad na upgrady nebo jiné služby, které používají data o stavu. Příklady takových služeb jsou opravné služby a mechanismy upozorňování. Proto je nutné, aby bylo možné poskytovat sestavy, které zachytí podmínky zájmu co nejlepším možným způsobem.

Vytváření sestav se dá provést z těchto:
* Sledovaná replika služby Service Fabric nebo instance.
* Interní sledovací zařízení nasazená jako služba Service Fabric (například Service Fabric Bezstavová služba, která monitoruje stavy a hlášení problémů). Sledovací zařízení je možné nasadit na všech uzlech nebo je můžete spřažené do monitorované služby.
* Interní sledovací zařízení, která běží na Service Fabricch uzlech, ale nejsou implementovaná jako Service Fabric služby.
* Externí sledovací zařízení, která procházejí prostředkem mimo Service Fabric cluster (například monitorovací služba jako Gomez).

Mimo box Service Fabric komponenty hlásí stav u všech entit v clusteru. [Sestavy o stavu systému](service-fabric-understand-and-troubleshoot-with-system-health-reports.md) poskytují přehled o funkcích clusteru a aplikací a označují problémy prostřednictvím stavu. Pro aplikace a služby se v sestavách stavu systému ověřují, jestli jsou entity implementované a že se chovají správně z perspektivy Service Fabric runtime. Sestavy neposkytují žádné monitorování stavu obchodní logiky služby nebo zjišťují procesy, které přestaly odpovídat. K přidání informací o stavu, které jsou specifické pro vaši logiku služby, implementujte ve svých službách [vlastní vytváření sestav o stavu](service-fabric-report-health.md) .

Service Fabric poskytuje více způsobů [zobrazení sestav stavu](service-fabric-view-entities-aggregated-health.md) agregovaných v Health Store:
* [Service Fabric Explorer](service-fabric-visualizing-your-cluster.md) nebo jiné nástroje pro vizualizaci.
* Dotazy na stav (prostřednictvím [PowerShellu](/powershell/module/servicefabric/?preserve-view=true&view=azureservicefabricps), [CLI](service-fabric-sfctl.md), rozhraní API pro [C# FabricClient](/dotnet/api/system.fabric.fabricclient.healthclient) a rozhraní API [Java FabricClient](/java/api/system.fabric)nebo [rozhraní REST API](/rest/api/servicefabric)).
* Obecné dotazy, které vracejí seznam entit, které mají stav jako jednu z vlastností (prostřednictvím PowerShellu, CLI, rozhraní API nebo REST).

## <a name="monitoring-and-diagnostics"></a>Monitorování a diagnostika
[Monitorování a diagnostika](service-fabric-diagnostics-overview.md) jsou zásadní pro vývoj, testování a nasazení aplikací a služeb v jakémkoli prostředí. Řešení Service Fabric fungují nejlépe při plánování a implementaci monitorování a diagnostiky, které vám pomůžou zajistit, aby aplikace a služby fungovaly podle očekávání v místním vývojovém prostředí nebo v produkčním prostředí.

Mezi hlavní cíle monitorování a diagnostiky patří:

- Zjišťování a Diagnostika problémů s hardwarem a infrastrukturou
- Zjištění problémů softwaru a aplikací, omezení výpadků služeb
- Pochopení využití prostředků a usnadnění rozhodování o operacích
- Optimalizace výkonu aplikací, služeb a infrastruktury
- Generování obchodních přehledů a určení oblastí zlepšování
 
Celkový pracovní postup monitorování a diagnostiky se skládá ze tří kroků:

1. Generování událostí: sem patří události (protokoly, trasování, vlastní události) na úrovni infrastruktury (clusteru), platformy a aplikace nebo služby.
2. Agregace událostí: vygenerované události je potřeba shromáždit a agregovat předtím, než se dají zobrazit.
3. Analýza: události musí být vizuální a přístupné v některém formátu, aby bylo možné analyzovat a zobrazovat podle potřeby.

K dispozici je více produktů, které pokrývají tyto tři oblasti, a pro každou z nich si můžete vybrat různé technologie. Další informace najdete v článku [monitorování a Diagnostika pro Azure Service Fabric](service-fabric-diagnostics-overview.md).

## <a name="next-steps"></a>Další kroky
* Naučte se vytvořit [cluster v Azure](service-fabric-cluster-creation-via-portal.md) nebo [samostatný clusteru ve Windows](service-fabric-cluster-creation-for-windows-server.md).
* Zkuste si vytvořit službu pomocí programovacího modelu [Reliable Services](service-fabric-reliable-services-quick-start.md) nebo [Reliable Actors](service-fabric-reliable-actors-get-started.md).
* Naučte se [migrovat z Cloud Services](service-fabric-cloud-services-migration-differences.md).
* Naučte se [monitorovat a diagnostikovat služby](service-fabric-diagnostics-how-to-monitor-and-diagnose-services-locally.md). 
* Naučte se [testovat své aplikace a služby](service-fabric-testability-overview.md).
* Naučte se [Spravovat a orchestrovat prostředky clusteru](service-fabric-cluster-resource-manager-introduction.md).
* Prohlédněte si [ukázky Service Fabric](/samples/browse/?products=azure).
* Přečtěte si o [možnostech podpory Service Fabric](service-fabric-support.md).
* Články a oznámení si můžete přečíst na [blogu týmu](https://techcommunity.microsoft.com/t5/azure-service-fabric/bg-p/Service-Fabric) .


[cluster-application-instances]: media/service-fabric-content-roadmap/cluster-application-instances.png
[cluster-imagestore-apptypes]: ./media/service-fabric-content-roadmap/cluster-imagestore-apptypes.png
