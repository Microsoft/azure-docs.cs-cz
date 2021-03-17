---
title: Seznamte se s Service Fabric terminologie Azure
description: Přečtěte si klíčové Service Fabric terminologie a koncepty používané ve zbývající části dokumentace.
author: masnider
ms.topic: conceptual
ms.date: 09/17/2018
ms.author: masnider
ms.openlocfilehash: 2ac4b81a284ed8c38bc9cefccd08db5afa51d600
ms.sourcegitcommit: 16c7fd8fe944ece07b6cf42a9c0e82b057900662
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/03/2020
ms.locfileid: "96575937"
---
# <a name="service-fabric-terminology-overview"></a>Přehled terminologie Service Fabric

Azure Service Fabric je platforma distribuovaných systémů usnadňující balení, nasazování a spravování škálovatelných a spolehlivých mikroslužeb.  [Clustery Service Fabric můžete hostovat kdekoli](service-fabric-deploy-anywhere.md): Azure, v místním datovém centru nebo v jakémkoli poskytovateli cloudu.  Service Fabric je produkt Orchestrator, který využívá [síť Azure Service Fabric](../service-fabric-mesh/index.yml). Můžete použít libovolné rozhraní k zápisu služeb a zvolit, kde spustit aplikaci z více možností prostředí. Tento článek podrobně popisuje terminologii, kterou používá Service Fabric k pochopení podmínek používaných v dokumentaci.

## <a name="infrastructure-concepts"></a>Koncepty infrastruktury

**Cluster**: sada virtuálních nebo fyzických počítačů připojených k síti, do kterých se vaše mikroslužby nasazují a spravují.  Clustery je možné škálovat na tisíce počítačů.

**Uzel**: počítač nebo virtuální počítač, který je součástí clusteru, se označuje jako *uzel*. Každému uzlu je přiřazen název uzlu (řetězec). Uzly mají charakteristiky, jako jsou vlastnosti umístění. Každý počítač nebo virtuální počítač má automaticky spuštěnou službu systému Windows, `FabricHost.exe` která při spuštění spustí spuštění a pak spustí dva spustitelné soubory: `Fabric.exe` a `FabricGateway.exe` . Tyto dva spustitelné soubory tvoří uzel. V případě testovacích scénářů můžete hostovat více uzlů na jednom počítači nebo VIRTUÁLNÍm počítači spuštěním více instancí `Fabric.exe` a `FabricGateway.exe` .

## <a name="application-and-service-concepts"></a>Koncepce aplikací a služeb

**Service Fabric aplikace sítě**: aplikace Service Fabric mřížku jsou popsány v modelu prostředků (soubory prostředků YAML a JSON) a lze je nasadit do jakéhokoli prostředí, kde Service Fabric běží.

**Service Fabric nativní aplikace**: Service Fabric nativní aplikace jsou popsány v rámci nativního aplikačního modelu (aplikace založené na jazyce XML a manifesty služeb).  Service Fabric nativní aplikace nelze spouštět v Service Fabric sítě.

### <a name="service-fabric-mesh-application-concepts"></a>Koncepty použití Service Fabric sítě

**Aplikace**: aplikace je jednotka nasazení, správy verzí a životnosti aplikace sítě. Životní cyklus jednotlivých instancí aplikace je možné spravovat nezávisle.  Aplikace se skládají z jednoho nebo více balíčků kódu služby a nastavení. Aplikace je definovaná pomocí schématu Azure Resource model (RM).  Služby jsou popsány jako vlastnosti prostředku aplikace v šabloně RM.  Odkazy na sítě a svazky používané aplikací jsou odkazovány v aplikaci.  Při vytváření aplikace jsou aplikace, služby (y), sítě a svazky modelovány pomocí modelu Service Fabric prostředků.

**Služba**: služba v aplikaci představuje mikroslužbu a provede úplnou a samostatnou funkci. Každá služba se skládá z jednoho nebo více balíčků kódu, které popisují vše potřebné ke spuštění image kontejneru přidružené k balíčku kódu.  Počet služeb v aplikaci lze škálovat nahoru a dolů.

**Síť**: síťový prostředek vytvoří privátní síť pro vaše aplikace a nezávisí na aplikacích nebo službách, které se na ni mohou odkazovat. Několik služeb z různých aplikací může být součástí stejné sítě. Sítě jsou nasazené prostředky, na které odkazují aplikace.

**Balíček kódu**: balíčky kódu popisují vše potřebné ke spuštění image kontejneru přidružené k balíčku kódu, včetně následujících:

* Název, verze a registr kontejneru
* Prostředky procesoru a paměti vyžadované pro každý kontejner
* Síťové koncové body
* Svazky, které se mají připojit do kontejneru a odkazují na samostatný prostředek svazku.

Všechny balíčky kódu definované jako součást prostředku aplikace jsou nasazeny a aktivovány společně jako skupina.

**Svazek**: svazky jsou adresáře, které se připevní do instancí kontejnerů, které můžete použít k trvalému stavu. Ovladač svazku souborů Azure připojuje sdílenou složku Azure Files do kontejneru a poskytuje spolehlivé úložiště dat prostřednictvím rozhraní API, které podporuje úložiště souborů. Svazky jsou nasaditelné prostředky, na které odkazují aplikace.

### <a name="service-fabric-native-application-concepts"></a>Service Fabric koncepce nativních aplikací

**Aplikace**: aplikace je kolekcí základních služeb, které provádějí určitou funkci nebo funkce. Životní cyklus jednotlivých instancí aplikace je možné spravovat nezávisle.

**Služba**: služba provádí úplnou a samostatnou funkci a může spustit a spustit nezávisle na jiných službách. Služba se skládá z kódu, konfigurace a dat. U každé služby se kód skládá ze spustitelných binárních souborů, konfigurace se skládá z nastavení služby, které lze načíst za běhu, a data se skládají z libovolných statických dat, která má služba spotřebovat.

**Typ aplikace**: název/verze přiřazená kolekci typů služeb. Je definovaný v `ApplicationManifest.xml` souboru a vložený v adresáři balíčku aplikace. Adresář se pak zkopíruje do úložiště imagí clusteru Service Fabric. Pak můžete vytvořit pojmenovanou aplikaci z tohoto typu aplikace v rámci clusteru.

Další informace najdete v článku o [modelu aplikace](service-fabric-application-model.md) .

**Balíček aplikace**: adresář disku obsahující soubor typu aplikace `ApplicationManifest.xml` . Odkazuje na balíčky služeb pro každý typ služby, který tvoří typ aplikace. Soubory v adresáři balíčku aplikace se zkopírují do úložiště imagí Service Fabricho clusteru. Například balíček aplikace pro typ e-mailové aplikace může obsahovat odkazy na balíček front-Service, balíček front-Service a balíček databázových služeb.

**Pojmenovaná aplikace**: po zkopírování balíčku aplikace do úložiště imagí vytvoříte instanci aplikace v rámci clusteru. Instanci vytvoříte při určení typu aplikace balíčku aplikace pomocí jejího názvu nebo verze. Každé instanci typu aplikace je přiřazen název identifikátoru URI (Uniform Resource Identifier), který vypadá takto: `"fabric:/MyNamedApp"` . V rámci clusteru můžete vytvořit více pojmenovaných aplikací z jednoho typu aplikace. Můžete také vytvořit pojmenované aplikace z různých typů aplikací. Každá pojmenovaná aplikace je spravovaná a má nezávisle.

**Typ služby**: název/verze přiřazená k balíčkům kódu služby, datovým balíčkům a konfiguračním balíčkům. Typ služby je definovaný v `ServiceManifest.xml` souboru a vložený v adresáři balíčku služby. Na adresář balíčku služby se pak odkazuje v souboru balíčku aplikace `ApplicationManifest.xml` . V rámci clusteru můžete po vytvoření pojmenované aplikace vytvořit pojmenovanou službu z některého z typů služeb typu aplikace. Soubor typu služby `ServiceManifest.xml` Popisuje službu.

Další informace najdete v článku o [modelu aplikace](service-fabric-application-model.md) .

Existují dva typy služeb:

* **Bezstavová** služba: Pokud je trvalý stav služby uložený v externí službě úložiště, například Azure Storage, Azure SQL Database nebo Azure Cosmos DB, použijte bezstavovou službu. Bezstavová služba se používá v případě, že služba nemá trvalé úložiště. Například u služby kalkulačky, kde jsou hodnoty předány službě, se provede výpočet, který tyto hodnoty používá, a pak se vrátí výsledek.
* **Stavová** služba: pokud chcete Service Fabric spravovat stav služby prostřednictvím svých spolehlivých kolekcí nebo Reliable Actors programovacích modelů, použijte stavovou službu. Při vytváření pojmenované služby určete, kolik oddílů chcete rozšířit svůj stav a škálovatelnost. Také určete, kolikrát má být stav replikace mezi uzly, aby byla spolehlivost. Každá pojmenovaná služba má jednu primární repliku a několik sekundárních replik. Stav pojmenované služby upravíte při zápisu do primární repliky. Service Fabric pak tento stav replikuje do všech sekundárních replik, aby byl váš stav synchronizovaný. Service Fabric automaticky detekuje, kdy se primární replika nezdařila, a propaguje stávající sekundární repliku na primární repliku. Service Fabric pak vytvoří novou sekundární repliku.  

**Repliky nebo instance** odkazují na kód (a stav pro stavové služby) služby, která je nasazená a spuštěná. Viz [repliky a instance](service-fabric-concepts-replica-lifecycle.md).

Změna **Konfigurace** odkazuje na proces jakékoli změny v sadě replik služby. Viz [rekonfigurace](service-fabric-concepts-reconfiguration.md).

**Balíček služby**: adresář disku, který obsahuje soubor typu služby `ServiceManifest.xml` . Tento soubor odkazuje na kód, statická data a konfigurační balíčky pro typ služby. Soubory v adresáři balíčku služby jsou odkazovány souborem typu aplikace `ApplicationManifest.xml` . Balíček služby může například odkazovat na kód, statická data a konfigurační balíčky, které tvoří databázovou službu.

**Pojmenovaná služba**: po vytvoření pojmenované aplikace můžete vytvořit instanci jednoho z jeho typů služeb v rámci clusteru. Typ služby určíte pomocí jejího názvu nebo verze. Každá instance typu služby má přiřazený název identifikátoru URI v oboru názvů IDENTIFIKÁTORu URI pojmenované aplikace. Například pokud vytvoříte "MyDatabase" pojmenovanou službu v rámci "MyNamedApp" pojmenované aplikace, identifikátor URI vypadá takto: `"fabric:/MyNamedApp/MyDatabase"` . V rámci pojmenované aplikace můžete vytvořit několik pojmenovaných služeb. Každá pojmenovaná služba může mít vlastní schéma oddílů a instance nebo počet replik.

**Balíček kódu**: adresář disku obsahující spustitelné soubory typu služby, obvykle soubory exe/dll. Soubory v adresáři balíčku kódu jsou odkazovány ze souboru typu služby `ServiceManifest.xml` . Při vytváření pojmenované služby je balíček kódu zkopírován do uzlu nebo uzlů, které jsou vybrány pro spuštění pojmenované služby. Pak se kód začne spouštět. Existují dva typy spustitelných souborů balíčku kódu:

* **Spustitelné soubory hosta**: spustitelné soubory, které běží jako – jsou v hostitelském operačním systému (Windows nebo Linux). Tyto spustitelné soubory neodkazují na ani neodkazují na žádné soubory Service Fabric runtime, a proto nepoužívají žádné Service Fabric programovací modely. Tyto spustitelné soubory nemůžou používat některé funkce Service Fabric, jako je třeba služba pojmenování pro zjišťování koncových bodů. Spustitelné soubory hosta nemůžou sestavovat metriky zatížení, které jsou specifické pro jednotlivé instance služby.
* **Spustitelné soubory hostitele služby**: spustitelné soubory, které používají Service Fabric programovacích modelů propojením s Service Fabric běhových souborů a povolením funkcí Service Fabric. Například pojmenovaná instance služby může registrovat koncové body pomocí Naming Service Service Fabric a může také nahlásit metriky zatížení.

**Datový balíček**: adresář disku, který obsahuje statické datové soubory typu, které jsou jen pro čtení, obvykle fotky, zvuk a videosoubory. Soubory v adresáři datových balíčků jsou odkazovány ze souboru typu služby `ServiceManifest.xml` . Při vytváření pojmenované služby je balíček dat zkopírován do uzlu nebo uzlů vybraných pro spuštění pojmenované služby. Kód začíná běžet a teď může přistupovat k datovým souborům.

**Konfigurační balíček**: adresář disku, který obsahuje statické konfigurační soubory typu služby jen pro čtení, obvykle textové soubory. Soubory v adresáři konfiguračního balíčku jsou odkazovány ze souboru typu služby `ServiceManifest.xml` . Při vytváření pojmenované služby se soubory v konfiguračním balíčku zkopírují do jednoho nebo více uzlů vybraných pro spuštění pojmenované služby. Pak se kód začne spouštět a teď může přistupovat ke konfiguračním souborům.

**Kontejnery**: ve výchozím nastavení Service Fabric nasadí a aktivuje služby jako procesy. Service Fabric můžou nasazovat i služby v imagích kontejnerů. Kontejnery jsou virtualizační technologie, která z aplikací vyabstrakce základní operační systém. Aplikace a její modul runtime, závislosti a systémové knihovny se spouští uvnitř kontejneru. Kontejner má plný privátní přístup k vlastnímu izolovanému zobrazení kontejneru konstrukcí operačního systému. Service Fabric podporuje kontejnery Windows serveru a kontejnery Docker v systému Linux. Další informace najdete v tématu [Service Fabric a kontejnery](service-fabric-containers-overview.md).

**Schéma oddílu**: při vytváření pojmenované služby určíte schéma oddílu. Služby s podstatnými stavy rozdělí data napříč oddíly, které šíří stav napříč uzly clusteru. Rozdělením dat mezi oddíly se může škálovat stav vaší pojmenované služby. V rámci oddílu mají instance bez příslušnosti instance, zatímco stav s názvem služby má repliky. Obvykle pojmenované služby s názvem bez stavů mají pouze jeden oddíl, protože nemají žádný interní stav. Instance oddílu poskytují dostupnost. Pokud dojde k chybě jedné instance, ostatní instance budou fungovat normálně a pak Service Fabric vytvoří novou instanci. Stavové služby uchovávají jejich stav v rámci replik a každý oddíl má vlastní sadu replik, takže stav je udržován v synchronizaci. Pokud by se replika nezdařila, Service Fabric vytvoří novou repliku z existujících replik.

Další informace najdete v článku věnovaném [Service Fabric Reliable Services pro oddíl](service-fabric-concepts-partitioning.md) .

## <a name="system-services"></a>Systémové služby

V každém clusteru se vytvářejí systémové služby, které poskytují možnosti platformy Service Fabric.

**Naming Service**: každý cluster Service Fabric obsahuje Naming Service, což překládá názvy služeb na umístění v clusteru. Můžete spravovat názvy a vlastnosti služby, jako je třeba Internet Domain Name System (DNS) pro cluster. Klienti zabezpečeně komunikují s jakýmkoli uzlem v clusteru pomocí Naming Service k překladu názvu služby a jejího umístění. Aplikace se pohybují v rámci clusteru. Může to být například kvůli chybám, vyrovnávání prostředků nebo změně velikosti clusteru. Můžete vyvíjet služby a klienty, kteří vyřeší aktuální umístění v síti. Klienti získají skutečnou IP adresu počítače a port, na kterém je aktuálně spuštěná.

Další informace o rozhraních API pro komunikaci klientů a služeb, která fungují s Naming Service, najdete v tématu [komunikace se službami](service-fabric-connect-and-communicate-with-services.md) .

**Služba Image Store**: každý cluster Service Fabric obsahuje službu Image Store, ve které je nasazená verze balíčků aplikací. Zkopírujte balíček aplikace do Image Store a pak zaregistrujte typ aplikace obsažený v rámci tohoto balíčku aplikace. Po zřízení typu aplikace se z něj vytvoří pojmenovaná aplikace. Můžete zrušit registraci typu aplikace od služby Image Store po odstranění všech jejích pojmenovaných aplikací.

Další informace o Image Store službě najdete [v tématu Principy nastavení ImageStoreConnectionString](service-fabric-image-store-connection-string.md) .

Další informace o nasazování aplikací do služby Image Store najdete v článku [nasazení aplikace](service-fabric-deploy-remove-applications.md) .

**Služba Správce převzetí služeb při selhání**: každý cluster Service Fabric má správce převzetí služeb při selhání službu, která zodpovídá za následující akce:

 - Provádí funkce související s vysokou dostupností a konzistencí služeb.
 - Orchestruje upgrady aplikací a clusterů.
 - Spolupracuje s dalšími součástmi systému.

**Služba Repair Manager**: Jedná se o volitelnou systémovou službu, která umožňuje provádět akce oprav v clusteru způsobem, který je bezpečný, automatizovaný a transparentní. Správce oprav se používá v:

   - Provádění oprav v rámci údržby Azure u clusterů Azure Service Fabric v [stříbrné a zlaté odolnosti](service-fabric-cluster-capacity.md#durability-characteristics-of-the-cluster)
   - Provádění opravných akcí pro [aplikaci orchestrace oprav](service-fabric-patch-orchestration-application.md)

## <a name="deployment-and-application-models"></a>Modely nasazení a aplikace

Chcete-li nasadit své služby, je třeba popsat, jak by měly být spuštěny. Service Fabric podporuje tři různé modely nasazení:

### <a name="resource-model-preview"></a>Model prostředků (Preview)

Prostředky Service Fabric jsou cokoli, co se dá nasadit jednotlivě do Service Fabric; včetně aplikací, služeb, sítí a svazků. Prostředky se definují pomocí souboru JSON, který se dá nasadit na koncový bod clusteru.  V případě Service Fabric sítě se používá schéma modelu Azure Resource model. Schéma souborů YAML lze také použít k snadnějšímu vytváření definičních souborů. Prostředky je možné nasadit kdekoli Service Fabric spuštění. Model prostředků představuje nejjednodušší způsob, jak popsání Service Fabricch aplikací. Hlavním fokusem je jednoduché nasazení a Správa kontejnerových služeb. Pokud se chcete dozvědět víc, přečtěte si [Úvod do modelu prostředků Service Fabric](../service-fabric-mesh/service-fabric-mesh-service-fabric-resources.md).

### <a name="native-model"></a>Nativní model

Nativní aplikační model poskytuje vašim aplikacím úplný přístup na nízké úrovni k Service Fabric. Aplikace a služby jsou definovány jako registrované typy v souborech manifestu XML.

Nativní model podporuje rozhraní Reliable Services a Reliable Actors, která poskytují přístup k rozhraním API Service Fabric modulu runtime a rozhraním API pro správu clusteru v jazycích C# a Java. Nativní model podporuje také libovolné kontejnery a spustitelné soubory. Nativní model není podporován v [prostředí Service Fabric sítě](../service-fabric-mesh/service-fabric-mesh-overview.md).

**Reliable Services**: rozhraní API pro sestavení bezstavových a stavových služeb. Stavové služby ukládají jejich stav do spolehlivých kolekcí, jako je slovník nebo fronta. Můžete také připojit různé komunikační zásobníky, například webové rozhraní API a Windows Communication Foundation (WCF).

**Reliable Actors**: rozhraní API pro sestavení bezstavových a stavových objektů pomocí programovacího modelu Virtual actor. Tento model je užitečný v případě, že máte spoustu nezávislých jednotek výpočtu nebo stavu. Tento model používá model vláken založený na založení, takže je nejvhodnější vyhnout se kódu, který se volá na jiné objekty actor nebo Services, protože samostatný objekt actor nemůže zpracovat jiné příchozí požadavky, dokud nebudou dokončeny všechny jeho odchozí žádosti.

Můžete také spouštět stávající aplikace na Service Fabric:

**Kontejnery**: Service Fabric podporuje nasazení kontejnerů Docker v kontejnerech se systémy Linux a Windows Server v systému windows Server 2016 společně s podporou režimu izolace technologie Hyper-V. V [modelu aplikace](service-fabric-application-model.md)Service Fabric kontejner představuje hostitele aplikace, ve kterém jsou umístěny více replik služby. Service Fabric může spustit libovolné kontejnery a scénář je podobný scénáři hostující spustitelný soubor, kde zabalíte existující aplikaci do kontejneru. Kromě toho můžete také [spouštět Service Fabric služby uvnitř kontejnerů](service-fabric-services-inside-containers.md) .

**Spustitelné soubory hosta**: v Azure můžete spustit libovolný typ kódu, jako je například Node.js, Python, Java nebo C++, Service Fabric jako služba. Service Fabric odkazuje na tyto typy služeb jako spustitelné soubory hosta, které jsou považovány za bezstavové služby. Výhodou spuštění hostovaného spustitelného souboru v clusteru Service Fabric zahrnuje vysokou dostupnost, monitorování stavu, správu životního cyklu aplikací, vysokou hustotu a zjistitelnost.

Další informace najdete v článku [Volba programovacího modelu pro vaši službu](service-fabric-choose-framework.md) .

### <a name="docker-compose"></a>Docker Compose 

[Docker Compose](https://docs.docker.com/compose/) je součástí projektu Docker. Service Fabric poskytuje omezené podpory pro [nasazení aplikací pomocí modelu Docker Compose](service-fabric-docker-compose.md).

## <a name="environments"></a>Prostředí

Service Fabric je open source technologie pro platformu, na které jsou založené různé služby a produkty. Společnost Microsoft poskytuje následující možnosti:

 - **Síť Azure Service Fabric**: plně spravovaná služba pro spouštění Service Fabric aplikací v Microsoft Azure.
 - **Azure Service Fabric**: Nabídka Azure hosted Service Fabric clusteru. Poskytuje integraci mezi Service Fabric a infrastrukturou Azure spolu se správou aktualizací a konfigurací Service Fabricch clusterů.
 - **Service Fabric Standalone**: sada nástrojů pro instalaci a konfiguraci pro [nasazení Service Fabricch clusterů kdekoli](./service-fabric-deploy-anywhere.md) (místně nebo v jakémkoli poskytovateli cloudu). Nespravuje se v Azure.
 - **Service Fabric vývojový cluster**: poskytuje místní vývojové prostředí v systému Windows, Linux nebo Mac pro vývoj aplikací Service Fabric.

## <a name="environment-framework-and-deployment-model-support-matrix"></a>Matice podpory pro prostředí, architekturu a model nasazení

Různá prostředí mají různé úrovně podpory pro architektury a modely nasazení. V následující tabulce jsou popsány podporované kombinace rozhraní a modelu nasazení.

| Typ aplikace | Popisuje | Síť Azure Service Fabric | Clustery Azure Service Fabric (libovolný operační systém)| Místní cluster | Samostatný cluster |
|---|---|---|---|---|---|
| Aplikace Service Fabric sítě | Model prostředků (YAML & JSON) | Podporováno |Nepodporováno | Windows – podporováno, Linux a Mac – nepodporováno | Windows – nepodporováno |
|Service Fabric nativních aplikací | Nativní aplikační model (XML) | Nepodporuje se| Podporováno|Podporováno|Windows – podporováno|

V následující tabulce jsou popsány různé modely aplikací a nástroje, které pro ně existují pro Service Fabric.

| Typ aplikace | Popisuje | Visual Studio | Eclipse | SFCTL | AZ CLI | PowerShell|
|---|---|---|---|---|---|---|
| Aplikace Service Fabric sítě | Model prostředků (YAML & JSON) | VS 2017 |Nepodporováno |Nepodporováno | Podporované – jenom pro prostředí sítě | Nepodporuje se|
|Service Fabric nativních aplikací | Nativní aplikační model (XML) | VS 2017 a VS 2015| Podporováno|Podporováno|Podporováno|Podporováno|

## <a name="next-steps"></a>Další kroky

Další informace o Service Fabric:

* [Přehled služby Service Fabric](service-fabric-overview.md)
* [Proč při sestavování aplikací zvolit přístup založený na mikroslužbách?](service-fabric-overview-microservices.md)
* [Scénáře aplikací](service-fabric-application-scenarios.md)

Další informace o Service Fabric sítě:

* [Přehled Service Fabric sítě](../service-fabric-mesh/service-fabric-mesh-overview.md)
