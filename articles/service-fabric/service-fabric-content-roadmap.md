---
title: Další informace o Azure Service Fabric
description: Seznamte se s hlavními koncepty a hlavními oblastmi Azure Service Fabric. Poskytuje rozšířený přehled Service Fabric a jak vytvořit mikroslužeb.
ms.topic: conceptual
ms.date: 12/08/2017
ms.openlocfilehash: 4e6e21f5f9ebfeddb5292e00dc8a929341e77372
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "75458144"
---
# <a name="so-you-want-to-learn-about-service-fabric"></a>Takže se chcete dozvědět o Service Fabric?
Azure Service Fabric je platforma distribuovaných systémů usnadňující balení, nasazování a spravování škálovatelných a spolehlivých mikroslužeb.  Service Fabric má však velkou plochu a je toho hodně, co se můžete naučit.  Tento článek obsahuje přehled service fabric a popisuje základní koncepty, programovací modely, životní cyklus aplikace, testování, clustery a monitorování stavu. Přečtěte si [přehled](service-fabric-overview.md) a [co jsou mikroslužby?](service-fabric-overview-microservices.md) pro úvod a jak Service Fabric lze použít k vytvoření mikroslužeb. Tento článek neobsahuje úplný seznam obsahu, ale odkazuje na přehled a články Začínáme pro každou oblast service fabric. 

## <a name="core-concepts"></a>Základní koncepty
[Terminologie Service Fabric](service-fabric-technical-overview.md), [Aplikační model](service-fabric-application-model.md)a [Podporované programovací modely](service-fabric-choose-framework.md) poskytují další koncepty a popisy, ale zde jsou základy.

### <a name="design-time-service-type-service-package-and-manifest-application-type-application-package-and-manifest"></a>Čas návrhu: typ služby, balíček služeb a manifest, typ aplikace, balíček aplikace a manifest
Typ služby je název/verze přiřazená balíčkům kódu služby, balíčkům dat a konfiguračním balíčkům. To je definováno v souboru ServiceManifest.xml. Typ služby se skládá z spustitelného kódu a nastavení konfigurace služby, které jsou načteny za běhu, a statických dat, která je spotřebována službou.

Balíček služeb je diskový adresář obsahující soubor ServiceManifest.xml typu služby, který odkazuje na kód, statická data a konfigurační balíčky pro daný typ služby. Balíček služeb může například odkazovat na kód, statická data a konfigurační balíčky, které tvoří databázovou službu.

Typ aplikace je název/verze přiřazená kolekci typů služeb. To je definováno v souboru ApplicationManifest.xml.

![Typy aplikací Service Fabric a typy služeb][cluster-imagestore-apptypes]

Balíček aplikace je diskový adresář, který obsahuje soubor ApplicationManifest.xml typu aplikace, který odkazuje na balíčky služeb pro každý typ služby, který tvoří typ aplikace. Balíček aplikace pro typ e-mailové aplikace může například obsahovat odkazy na balíček služby fronty, balíček front-endové služby a balíček služby databáze.  

Soubory v adresáři balíčků aplikace jsou zkopírovány do úložiště bitových obrázků clusteru Service Fabric. Potom můžete vytvořit pojmenovanou aplikaci z tohoto typu aplikace, která pak běží v rámci clusteru. Po vytvoření pojmenované aplikace můžete vytvořit pojmenovanou službu z jednoho z typů služeb typu aplikace. 

### <a name="run-time-clusters-and-nodes-named-applications-named-services-partitions-and-replicas"></a>Doba běhu: clustery a uzly, pojmenované aplikace, pojmenované služby, oddíly a repliky
[Cluster Service Fabric](service-fabric-deploy-anywhere.md) je síťová sada virtuálních nebo fyzických počítačů, do kterých jsou vaše mikroslužby nasazeny a spravovány. Clustery je možné škálovat na tisíce počítačů.

Počítač nebo virtuální počítač, který je součástí clusteru, se nazývá uzel. Každému uzlu je přiřazen název uzlu (řetězec). Uzly mají určité charakteristiky, například vlastnosti umístění. Každý počítač nebo virtuální počítač má službu windows s automatickým spuštěním , `FabricHost.exe` `Fabric.exe` která `FabricGateway.exe`se spustí při spuštění a spustí dva spustitelné soubory: a . Tyto dva spustitelné soubory tvoří uzel. Pro vývoj nebo testování scénářů můžete hostovat více uzlů na jednom počítači nebo virtuálním počítači spuštěním více instancí `Fabric.exe` a `FabricGateway.exe`.

Pojmenovaná aplikace je kolekce pojmenovaných služeb, která provádí určitou funkci nebo funkce. Služba provádí kompletní a samostatnou funkci (lze spustit a spustit nezávisle na jiných službách) a skládá se z kódu, konfigurace a dat. Po zkopírování balíčku aplikace do úložiště bitových obrázků vytvoříte instanci aplikace v rámci clusteru zadáním typu aplikace balíčku aplikace (pomocí jeho názvu/verze). Každé instanci typu aplikace je přiřazen název identifikátoru URI, který vypadá jako *fabric:/MyNamedApp*. V rámci clusteru můžete vytvořit více pojmenovaných aplikací z jednoho typu aplikace. Můžete také vytvořit pojmenované aplikace z různých typů aplikací. Každá pojmenovaná aplikace je spravována a verzí nezávisle.

Po vytvoření pojmenované aplikace můžete vytvořit instanci jednoho z jejích typů služeb (pojmenovanou službu) v rámci clusteru zadáním typu služby (pomocí jejího názvu/verze). Každé instanci typu služby je přiřazen název identifikátoru URI s rozsahem pod identifikátorem URI pojmenované aplikace. Například pokud vytvoříte "MyDatabase" s názvem služby v rámci "MyNamedApp" s názvem aplikace, identifikátor URI vypadá takto: *fabric:/MyNamedApp/MyDatabase*. V rámci pojmenované aplikace můžete vytvořit jednu nebo více pojmenovaných služeb. Každá pojmenovaná služba může mít své vlastní schéma oddílů a počty instancí/replik. 

Existují dva typy služeb: bezstavové a stavové. Bezstavové služby neukládají stav v rámci služby. Bezstavové služby nemají trvalé úložiště vůbec nebo ukládat trvalý stav v externí službě úložiště, jako je Azure Storage, Azure SQL Database nebo Azure Cosmos DB. Stavová služba ukládá stav v rámci služby a používá spolehlivé kolekce nebo spolehlivé actors programovací modely pro správu stavu. 

Při vytváření pojmenované služby zadáte schéma oddílů. Služby s velkým množstvím stavu rozdělit data mezi oddíly. Každý oddíl je zodpovědný za část úplného stavu služby, která je rozložena mezi uzly clusteru.  

Následující diagram znázorňuje vztah mezi aplikacemi a instancemi služby, oddíly a replikami.

![Oddíly a repliky v rámci služby][cluster-application-instances]

### <a name="partitioning-scaling-and-availability"></a>Dělení, škálování a dostupnost
[Dělení](service-fabric-concepts-partitioning.md) není jedinečné pro service fabric. Dobře známá forma dělení je dělení dat nebo dělení. Stavové služby s velkým množstvím stavu rozdělit data mezi oddíly. Každý oddíl je zodpovědný za část úplného stavu služby. 

Repliky každého oddílu jsou rozloženy mezi uzly clusteru, což umožňuje škálování stavu pojmenované [služby](service-fabric-concepts-scalability.md). Jak se rostou potřeby dat, oddíly rostou a Service Fabric znovu vyvažuje oddíly mezi uzly, aby bylo možné efektivně využívat hardwarové prostředky. Pokud do clusteru přidáte nové uzly, service fabric znovu vyvažuje repliky oddílů napříč zvýšeným počtem uzlů. Celkový výkon aplikace zlepšuje a tvrzení o přístupu k paměti snižuje. Pokud uzly v clusteru nejsou používány efektivně, můžete snížit počet uzlů v clusteru. Service Fabric znovu vyvažuje repliky oddílů přes snížený počet uzlů, aby bylo možné lépe využít hardware v každém uzlu.

V rámci oddílu bezstavové pojmenované služby mají instance, zatímco stavové pojmenované služby mají repliky. Bezstavové pojmenované služby mají obvykle pouze jeden oddíl, protože nemají žádný vnitřní stav. Instance oddílu poskytují [dostupnost](service-fabric-availability-services.md). Pokud jedna instance selže, ostatní instance nadále fungovat normálně a pak Service Fabric vytvoří novou instanci. Stavové pojmenované služby udržují svůj stav v replikách a každý oddíl má vlastní sadu replik. Operace čtení a zápisu se provádějí v jedné replice (nazývané Primární). Změny stavu z operací zápisu jsou replikovány do více dalších replik (nazývané aktivní sekundární databáze). Pokud replika selže, Service Fabric vytvoří novou repliku z existujících replik.

## <a name="stateless-and-stateful-microservices-for-service-fabric"></a>Bezstavové a stavové mikroslužby pro Service Fabric
Service Fabric umožňuje sestavovat aplikace, které se skládají z mikroslužeb nebo kontejnerů. Bezstavové mikroslužby (například brány protokolů a webové proxy) si mimo požadavek a odpověď ze služby neudržují měnitelný stav. Role pracovních procesů služby Azure Cloud Services jsou příkladem stavové služby. Stavové mikroslužby (například uživatelské účty, databáze, zařízení, nákupní košíky a fronty) si udržují měnitelný a autoritativní stav i mimo požadavek a odpověď. Dnešní aplikace v internetovém měřítku se skládají z kombinace bezstavových a stavových mikroslužeb. 

Klíčové rozlišení service fabric je jeho silné zaměření na vytváření stavových služeb, buď s [vestavěnými programovacími modely](service-fabric-choose-framework.md) nebo s kontejnerovými stavovými službami. [Scénáře aplikací](service-fabric-application-scenarios.md) popisují scénáře, ve kterých se používají stavové služby.

Proč mají stavové mikroslužby spolu s bezstavové ty? Dva hlavní důvody jsou:

* Můžete vytvářet služby online zpracování transakcí (OLTP) s vysokou propustností, nízkou latencí a selháním udržováním kódu a dat v blízkosti stejného počítače. Některé příklady jsou interaktivní výkladní skříně, vyhledávání, internet věcí (IoT) systémy, obchodní systémy, zpracování kreditních karet a systémy odhalování podvodů, a osobní záznam řízení.
* Návrh aplikace můžete zjednodušit. Stavové mikroslužby odeberou potřebu dalších front a mezipamětí, které jsou tradičně vyžadovány k řešení požadavků na dostupnost a latenci čistě bezstavové aplikace. Stavové služby jsou přirozeně vysoká dostupnost a nízká latence, což snižuje počet pohyblivých částí pro správu ve vaší aplikaci jako celku.

## <a name="supported-programming-models"></a>Podporované programovací modely
Service Fabric nabízí několik způsobů, jak psát a spravovat své služby. Služby můžete použít rozhraní API Service Fabric plně využít funkce platformy a rozhraní aplikace. Služby mohou být také libovolný kompilovaný spustitelný program napsaný v libovolném jazyce a hostované v clusteru Service Fabric. Další informace naleznete v [tématu Podporované programovací modely](service-fabric-choose-framework.md).

### <a name="containers"></a>Kontejnery
Service Fabric ve výchozím nastavení nasazuje a aktivuje služby jako procesy. Service Fabric můžete také nasadit služby v [kontejnerech](service-fabric-containers-overview.md). Důležité je, že můžete kombinovat služby v procesech a služby v kontejnerech ve stejné aplikaci. Service Fabric podporuje nasazení linuxových kontejnerů a kontejnerů Windows na Windows Server 2016. Můžete nasadit existující aplikace, bezstavové služby nebo stavové služby v kontejnerech. 

### <a name="reliable-services"></a>Reliable Services
[Spolehlivé služby](service-fabric-reliable-services-introduction.md) je lehký rámec pro psaní služeb, které se integrují s platformou Service Fabric a těžit z celé sady funkcí platformy. Spolehlivé služby mohou být bezstavové (podobně jako většina platforem služeb, jako jsou webové servery nebo role pracovních míst v Azure Cloud Services), kde se stav udál v externím řešení, jako je Azure DB nebo Azure Table Storage. Spolehlivé služby může být také stavové, kde stav je trvalý přímo ve službě samotné pomocí spolehlivé kolekce. Stav je [vysoce dostupný](service-fabric-availability-services.md) prostřednictvím replikace a distribuován prostřednictvím [dělení](service-fabric-concepts-partitioning.md), vše automaticky spravované service fabric.

### <a name="reliable-actors"></a>Reliable Actors
Postaveno na nad rámec spolehlivé služby, [architektura reliable actor](service-fabric-reliable-actors-introduction.md) je rozhraní aplikace, které implementuje virtuální objekt actor vzor, na základě vzoru návrhu objektu actor. Architektura Reliable Actor používá nezávislé jednotky výpočetních prostředků a stavu s jednovláknovým spuštěním nazývaným objekty actor. Architektura reliable actor poskytuje integrovanou komunikaci pro objekty actor a přednastavené konfigurace trvalosti stavu a horizontálního navýšení kapacity.

### <a name="aspnet-core"></a>ASP.NET Core
Service Fabric integruje s [ASP.NET Core](service-fabric-reliable-services-communication-aspnetcore.md) jako prvotřídní programovací model pro vytváření webových a API aplikací.  ASP.NET Core lze použít dvěma různými způsoby v Service Fabric:

- Hostováno jako spustitelný soubor hosta. Používá se především ke spuštění existujících ASP.NET základních aplikací v service fabric bez změn kódu.
- Spusťte spolehlivou službu. To umožňuje lepší integraci s service fabric runtime a umožňuje stavové ASP.NET základní služby.

### <a name="guest-executables"></a>Spustitelné soubory typu Host
[Spustitelný soubor hosta](service-fabric-guest-executables-introduction.md) je existující libovolný spustitelný soubor (napsaný v libovolném jazyce) hostovaný v clusteru Service Fabric spolu s dalšími službami. Spustitelné soubory hosta se neintegrují přímo s api service fabric. Ale stále těžit z funkcí platformy nabízí, jako je například vlastní stav a zatížení vykazování a služby zjistitelnost voláním REST API. Mají také plnou podporu životního cyklu aplikace. 

## <a name="application-lifecycle"></a>Životní cyklus aplikace
Stejně jako u jiných platforem aplikace na Service Fabric obvykle prochází následující fáze: návrh, vývoj, testování, nasazení, upgrade, údržbu a odebrání. Service Fabric poskytuje prvotřídní podporu pro celý životní cyklus aplikací cloudových aplikací, od vývoje přes nasazení, každodenní správu a údržbu až po případné vyřazení z provozu. Model služby umožňuje několik různých rolí k účasti nezávisle na životním cyklu aplikace. [Životní cyklus aplikace Service Fabric](service-fabric-application-lifecycle.md) poskytuje přehled rozhraní API a jak jsou používány různými rolemi v průběhu fází životního cyklu aplikace Service Fabric. 

Celý životní cyklus aplikace lze spravovat pomocí [rutin prostředí PowerShell](/powershell/module/ServiceFabric/), [příkazů příkazu CLI](service-fabric-sfctl.md), [c# API](/dotnet/api/system.fabric.fabricclient.applicationmanagementclient), java [api](/java/api/overview/azure/servicefabric)a [rest API](/rest/api/servicefabric/). Můžete také nastavit průběžné integrace nebo průběžné nasazení kanály pomocí nástrojů, jako je [Azure Pipelines](service-fabric-set-up-continuous-integration.md) nebo [Jenkins](service-fabric-cicd-your-linux-applications-with-jenkins.md).

## <a name="test-applications-and-services"></a>Testování aplikací a služeb
Chcete-li vytvořit skutečně cloudové služby, je důležité ověřit, zda vaše aplikace a služby vydrží selhání v reálném světě. Služba analýzy chyb je určena pro testování služeb, které jsou postaveny na service fabric. Pomocí [služby Fault Analysis Service](service-fabric-testability-overview.md)můžete vyvolat smysluplné chyby a spustit kompletní testovací scénáře proti vašim aplikacím. Tyto chyby a scénáře cvičení a ověřit četné stavy a přechody, které služba bude zažívat po celou dobu své životnosti, to vše řízeným, bezpečným a konzistentním způsobem.

[Akce](service-fabric-testability-actions.md) cílí na službu pro testování pomocí jednotlivých chyb. Vývojář služby můžete použít jako stavební bloky psát složité scénáře. Příklady simulovaných poruch jsou:

* Restartujte uzel, abyste simulovali libovolný počet situací, kdy je počítač nebo virtuální počítač restartován.
* Přesuňte repliku stavové služby a simulujte vyrovnávání zatížení, převzetí služeb při selhání nebo upgrade aplikace.
* Vyvolat ztrátu kvora na stavové služby k vytvoření situace, kdy operace zápisu nemůže pokračovat, protože není dostatek "zálohování" nebo "sekundární" repliky přijímat nová data.
* Vyvolat ztrátu dat na stavové služby k vytvoření situace, kdy je zcela vymazány všechny v paměti stavu.

[Scénáře](service-fabric-testability-scenarios.md) jsou složité operace složené z jedné nebo více akcí. Služba analýzy poruch poskytuje dva předdefinované úplné scénáře:

* [Scénář chaosu](service-fabric-controlled-chaos.md)- simuluje souvislé, proložené chyby (ladné i ungraceful) v celém clusteru po delší časové období.
* [Scénář převzetí služeb při selhání](service-fabric-testability-scenarios.md#failover-test)– verze scénáře chaos test, který se zaměřuje na konkrétní oddíl služby při ponechání dalších služeb bez e-ovlivnil.

## <a name="clusters"></a>Clustery
[Cluster Service Fabric](service-fabric-deploy-anywhere.md) je síťová sada virtuálních nebo fyzických počítačů, do kterých jsou vaše mikroslužby nasazeny a spravovány. Clustery je možné škálovat na tisíce počítačů. Počítač nebo virtuální počítač, který je součástí clusteru, se nazývá uzel clusteru. Každému uzlu je přiřazen název uzlu (řetězec). Uzly mají určité charakteristiky, například vlastnosti umístění. Každý počítač nebo virtuální počítač má `FabricHost.exe`službu automatického spuštění , která se spustí při spuštění a potom spustí dva spustitelné soubory: Fabric.exe a FabricGateway.exe. Tyto dva spustitelné soubory tvoří uzel. Pro testovací scénáře můžete hostovat více uzlů na jednom počítači nebo `Fabric.exe` `FabricGateway.exe`virtuálním počítači spuštěním více instancí a .

Clustery Service Fabric lze vytvářet na virtuálních nebo fyzických počítačích se systémem Windows Server nebo Linux. Můžete nasadit a spustit service fabric aplikace v jakémkoli prostředí, kde máte sadu počítačů se systémem Windows Server nebo Linux, které jsou vzájemně propojené: místní, v Microsoft Azure nebo na libovolném poskytovateli cloudu.

### <a name="clusters-on-azure"></a>Clustery v Azure
Spuštění clusterů Service Fabric v Azure poskytuje integraci s dalšími funkcemi a službami Azure, což usnadňuje a zspolehlivuje provoz a správu clusteru. Cluster je prostředek Azure Resource Manager, takže můžete modelovat clustery jako všechny ostatní prostředky v Azure. Správce prostředků také poskytuje snadnou správu všech prostředků používaných clusterem jako jedné jednotky. Clustery v Azure jsou integrované s diagnostikou Azure a protokoly Azure Monitoru. Typy uzlů clusteru jsou [škálovací sady virtuálních počítačů](/azure/virtual-machine-scale-sets/index), takže je vestavěná funkce automatického škálování.

Cluster můžete vytvořit v Azure prostřednictvím [portálu Azure](service-fabric-cluster-creation-via-portal.md), ze [šablony](service-fabric-cluster-creation-via-arm.md)nebo z [Visual Studia](service-fabric-cluster-creation-via-visual-studio.md).

Service Fabric na Linuxu umožňuje vytvářet, nasazovat a spravovat vysoce dostupné, vysoce škálovatelné aplikace na Linuxu stejně jako v systému Windows. Service Fabric rozhraní (spolehlivé služby a spolehlivé objekty actor) jsou k dispozici v Jazyce Java na Linuxu, kromě C# (.NET Core). Můžete také vytvořit [host spustitelné služby](service-fabric-guest-executables-introduction.md) s libovolným jazykem nebo rámci. Orchestrating Docker kontejnery je také podporována. Kontejnery Dockeru můžete spustit spustitelné soubory hosta nebo nativní služby Service Fabric, které používají rozhraní Service Fabric. Další informace naleznete v informacích o [službě Service Fabric na Linuxu](service-fabric-deploy-anywhere.md).

Existují některé funkce, které jsou podporovány v systému Windows, ale ne na Linuxu. Další informace najdete v článek [Rozdíly mezi service fabricem v Systémech Linux a Windows](service-fabric-linux-windows-differences.md).

### <a name="standalone-clusters"></a>Samostatné clustery
Service Fabric poskytuje instalační balíček pro vytvoření samostatných clusterů Service Fabric místně nebo na libovolném poskytovateli cloudu. Samostatné clustery vám dávají svobodu hostovat cluster kdekoli chcete. Pokud vaše data podléhají dodržování předpisů nebo regulačním omezením nebo chcete zachovat místní data, můžete hostovat vlastní cluster a aplikace. Aplikace Service Fabric lze spustit ve více hostitelských prostředích bez evidenčních prostředí, takže vaše znalosti o vytváření aplikací se přenáší z jednoho hostitelského prostředí do druhého. 

[Vytvoření vašeho prvního samostatného clusteru Service Fabric](service-fabric-cluster-creation-for-windows-server.md)

Linux samostatné clustery ještě nejsou podporovány.

### <a name="cluster-security"></a>Zabezpečení clusteru
Clustery musí být zabezpečené, aby se zabránilo neoprávněným uživatelům v připojení ke clusteru, zejména v případě, že má produkční úlohy spuštěné na něm. I když je možné vytvořit nezabezpečený cluster, umožňuje to anonymním uživatelům připojit se k němu, pokud jsou koncové body správy vystaveny veřejnému internetu. Později není možné povolit zabezpečení nezabezpečeného clusteru: zabezpečení clusteru je povoleno v době vytvoření clusteru.

Scénáře zabezpečení clusteru jsou:
* Zabezpečení mezi uzlužinami
* Zabezpečení klient-uzel
* Řízení přístupu na základě role (RBAC)

Další informace naleznete v článek [Zabezpečení clusteru](service-fabric-cluster-security.md).

### <a name="scaling"></a>Škálování
Pokud do clusteru přidáte nové uzly, service fabric znovu vyvažuje repliky oddílů a instance napříč zvýšeným počtem uzlů. Celkový výkon aplikace zlepšuje a tvrzení o přístupu k paměti snižuje. Pokud uzly v clusteru nejsou používány efektivně, můžete snížit počet uzlů v clusteru. Service Fabric znovu vyvažuje repliky oddílů a instance přes snížený počet uzlů, aby bylo možné lépe využít hardware v každém uzlu. Clustery v Azure můžete škálovat [ručně](service-fabric-cluster-scale-up-down.md) nebo [programově](service-fabric-cluster-programmatic-scaling.md). Samostatné clustery lze škálovat [ručně](service-fabric-cluster-windows-server-add-remove-nodes.md).

### <a name="cluster-upgrades"></a>Upgrady clusteru
Pravidelně jsou uvolněny nové verze service fabric runtime jsou uvolněny. Proveďte inovace za běhu nebo prostředků infrastruktury v clusteru, takže vždy používáte [podporovanou verzi](service-fabric-support.md). Kromě upgradů prostředků infrastruktury můžete také aktualizovat konfiguraci clusteru, jako jsou certifikáty nebo porty aplikace.

Cluster Service Fabric je prostředek, který vlastníte, ale je částečně spravován společností Microsoft. Společnost Microsoft je zodpovědná za opravu základního operačního systému a provádění upgradů prostředků infrastruktury v clusteru. Cluster můžete nastavit tak, aby přijímali automatické upgrady prostředků infrastruktury, když společnost Microsoft vydá novou verzi, nebo zvolit výběr požadované podporované verze prostředků infrastruktury. Upgrady prostředků infrastruktury a konfigurace lze nastavit prostřednictvím portálu Azure nebo prostřednictvím Správce prostředků. Další informace naleznete [v článek Upgrade clusteru Service Fabric](service-fabric-cluster-upgrade.md). 

Samostatný cluster je prostředek, který zcela vlastníte. Jste zodpovědní za opravu základníoperační systém a zahájení upgrady prostředků infrastruktury. Pokud se váš [https://www.microsoft.com/download](https://www.microsoft.com/download)cluster může připojit k aplikaci , můžete nastavit, aby se cluster automaticky stahoval a zřašoval nový runtime balíček Service Fabric. Potom byste zahájit upgrade. Pokud váš cluster nemá [https://www.microsoft.com/download](https://www.microsoft.com/download)přístup , můžete ručně stáhnout nový balíček runtime z počítače připojeného k internetu a potom zahájit upgrade. Další informace naleznete [v článek Upgrade samostatného clusteru Service Fabric](service-fabric-cluster-upgrade-windows-server.md).

## <a name="health-monitoring"></a>Monitorování stavu
Service Fabric zavádí [model stavu](service-fabric-health-introduction.md) určené k označení není v pořádku clusteru a podmínky aplikace na konkrétní entity (například uzly clusteru a repliky služeb). Model stavu používá zdravotní reportéři (součásti systému a watchdogs). Cílem je snadná a rychlá diagnostika a oprava. Autoři služeb musí předem přemýšlet o stavu a o tom, jak [navrhnout zprávy o stavu](service-fabric-report-health.md#design-health-reporting). Všechny podmínky, které mohou mít vliv na zdraví by měly být hlášeny na, zejména v případě, že může pomoci příznak problémy v blízkosti kořene. Informace o stavu může ušetřit čas a úsilí na ladění a vyšetřování, jakmile je služba v provozu ve velkém měřítku v produkčním prostředí.

Reportéři Service Fabric monitorují zjištěné podmínky zájmu. Podávají o těchto podmínkách zprávy na základě svého místního pohledu. [Úložiště stavu](service-fabric-health-introduction.md#health-store) agreguje data stavu odeslaná všemi reportéry k určení, zda entity jsou globálně v pořádku. Model je určen k bohatému, flexibilnímu a snadno použitelnému. Kvalita zpráv o stavu určuje přesnost zobrazení stavu clusteru. Falešně pozitivní výsledky, které nesprávně zobrazit problémy není v pořádku může negativně ovlivnit upgrady nebo jiné služby, které používají data stavu. Příkladem takových služeb jsou opravárenské služby a upozorňující mechanismy. Proto je zapotřebí některé myšlenky, aby zprávy, které zachycují podmínky zájmu v nejlepším možným způsobem.

Vykazování lze provést z:
* Replika nebo instance služby Service Fabric monitorované služby.
* Interní watchdogs nasazené jako služba Service Fabric (například služba Service Fabric bezstavové, která monitoruje podmínky a problémy sestavy). Watchdogs lze nasadit na všech uzlech nebo může být spřažené do monitorované služby.
* Interní watchdogs, které běží na uzly Service Fabric, ale nejsou implementovány jako služby Service Fabric.
* Externí watchdogs, které sondují prostředek mimo cluster Service Fabric (například monitorovací služby jako Gomez).

Po vybalení součásti Service Fabric sestavy stavu na všechny entity v clusteru. [Sestavy stavu systému](service-fabric-understand-and-troubleshoot-with-system-health-reports.md) poskytují přehled o funkcích clusteru a aplikací a problémy s příznakem prostřednictvím stavu. U aplikací a služeb sestavy stavu systému ověřují, že entity jsou implementovány a chovají se správně z hlediska runtime Service Fabric. Sestavy neposkytují žádné monitorování stavu obchodní logiky služby ani nezjišťují procesy, které přestaly reagovat. Chcete-li přidat informace o stavu specifické pro logiku vaší služby, [implementujte vlastní sestavy stavu](service-fabric-report-health.md) ve vašich službách.

Service Fabric poskytuje několik [způsobů,](service-fabric-view-entities-aggregated-health.md) jak zobrazit sestavy stavu agregované v úložišti stavu:
* [Service Fabric Explorer](service-fabric-visualizing-your-cluster.md) nebo jiné vizualizační nástroje.
* Dotazy na stav (prostřednictvím [prostředí PowerShell](/powershell/module/ServiceFabric/), [CLI](service-fabric-sfctl.md), [c# fabricclient API](/dotnet/api/system.fabric.fabricclient.healthclient) a [java fabricclient api](/java/api/system.fabric)nebo rest [api).](/rest/api/servicefabric)
* Obecné dotazy, které vracejí seznam entit, které mají stav jako jednu z vlastností (prostřednictvím prostředí PowerShell, CLI, rozhraní API nebo REST).

## <a name="monitoring-and-diagnostics"></a>Monitorování a diagnostika
[Monitorování a diagnostika](service-fabric-diagnostics-overview.md) jsou důležité pro vývoj, testování a nasazení aplikací a služeb v jakémkoli prostředí. Service Fabric řešení fungují nejlépe, když plánujete a implementujete monitorování a diagnostiku, které pomáhají zajistit, že aplikace a služby fungují podle očekávání v prostředí místního vývoje nebo v produkčním prostředí.

Hlavními cíli monitorování a diagnostiky jsou:

- Zjišťování a diagnostika problémů s hardwarem a infrastrukturou
- Detekce problémů se softwarem a aplikacemi, snížení prostoji služeb
- Pochopení spotřeby prostředků a pomoc při rozhodování o operacích
- Optimalizace výkonu aplikací, služeb a infrastruktury
- Vytvářejte obchodní přehledy a identifikujte oblasti zlepšení
 
Celkový pracovní postup monitorování a diagnostiky se skládá ze tří kroků:

1. Generování událostí: to zahrnuje události (protokoly, trasování, vlastní události) na infrastruktuře (clusteru), platformě a na úrovni aplikace / služby
2. Agregace událostí: generované události je třeba shromažďovat a agregovat před jejich zobrazením
3. Analýza: události musí být vizualizovány a přístupné v nějakém formátu, aby bylo možné provést analýzu a zobrazení podle potřeby

K dispozici je více produktů, které pokrývají tyto tři oblasti, a můžete si pro každou z nich vybrat různé technologie. Další informace načtete [sledování a diagnostiku prostředků Azure Service Fabric](service-fabric-diagnostics-overview.md).

## <a name="next-steps"></a>Další kroky
* Naučte se vytvořit [cluster v Azure](service-fabric-cluster-creation-via-portal.md) nebo [samostatný clusteru ve Windows](service-fabric-cluster-creation-for-windows-server.md).
* Zkuste si vytvořit službu pomocí programovacího modelu [Reliable Services](service-fabric-reliable-services-quick-start.md) nebo [Reliable Actors](service-fabric-reliable-actors-get-started.md).
* Přečtěte si, jak [migrovat z cloudových služeb](service-fabric-cloud-services-migration-differences.md).
* Naučte se [sledovat a diagnostikovat služby](service-fabric-diagnostics-how-to-monitor-and-diagnose-services-locally.md). 
* Naučte se [testovat aplikace a služby](service-fabric-testability-overview.md).
* Naučte se [spravovat a organizovat prostředky clusteru](service-fabric-cluster-resource-manager-introduction.md).
* Prohlédněte si [ukázky service fabric](https://aka.ms/servicefabricsamples).
* Další informace o [možnostech podpory service fabric](service-fabric-support.md).
* Přečtěte si [týmový blog](https://blogs.msdn.microsoft.com/azureservicefabric/) pro články a oznámení.


[cluster-application-instances]: media/service-fabric-content-roadmap/cluster-application-instances.png
[cluster-imagestore-apptypes]: ./media/service-fabric-content-roadmap/cluster-imagestore-apptypes.png
