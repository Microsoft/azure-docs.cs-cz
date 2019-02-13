---
title: Přečtěte si terminologie pro Azure Service Fabric | Dokumentace Microsoftu
description: Přehled terminologie Service Fabric. Pojednává klíčovou terminologií konceptů a termínů používaných ve zbývající části dokumentace.
services: service-fabric
documentationcenter: .net
author: rwike77
manager: timlt
editor: chackdan;subramar
ms.assetid: 3a970679-e19e-43b3-9be8-71773f307c57
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 09/17/2018
ms.author: ryanwi
ms.openlocfilehash: 787a25a822e4ee7e13a2f1429bacd8d5cd2139ca
ms.sourcegitcommit: 301128ea7d883d432720c64238b0d28ebe9aed59
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/13/2019
ms.locfileid: "56196820"
---
# <a name="service-fabric-terminology-overview"></a>Přehled terminologie Service Fabric
Azure Service Fabric je platforma distribuovaných systémů usnadňující balení, nasazování a spravování škálovatelných a spolehlivých mikroslužeb.  Je možné [clustery hostitelů Service Fabric, kdekoli](service-fabric-deploy-anywhere.md): Azure, v místním datovém centru, nebo jakýkoli jiný poskytovatel cloudu.  Service Fabric je orchestrátor, která je základem [Azure Service Fabric mřížky](/azure/service-fabric-mesh). K psaní služeb a rozhodnete, jak spustit aplikaci z několika možností prostředí můžete použít libovolné architektury. Tento článek obsahuje podrobnosti o terminologii používané k pochopení termínů používaných v dokumentaci k Service Fabric.

## <a name="infrastructure-concepts"></a>Koncepty infrastruktury
**Cluster**: Síťově propojená sada virtuálních nebo fyzických počítačů, do které se nasazují a spravují mikroslužby.  Clustery je možné škálovat na tisíce počítačů.

**Uzel**: Je volána počítač nebo virtuální počítač, který je součástí clusteru *uzel*. Každému uzlu je přiřazen název uzlu (řetězec). Uzly mají určité charakteristiky, jako je například vlastnosti umístění. Každý počítač nebo virtuální počítač má Windows automatické spouštění služby `FabricHost.exe`, která se spustí při spuštění a spustí dvě spustitelné soubory: `Fabric.exe` a `FabricGateway.exe`. Tyto dvě spustitelné soubory tvoří uzlu. Pro testovací scénáře, může hostovat více uzlů na jeden počítač nebo virtuální počítač spuštěním několika instancí `Fabric.exe` a `FabricGateway.exe`.

## <a name="application-and-service-concepts"></a>Aplikace a koncepty služby

**Aplikace Service Fabric sítě**: Aplikace Service Fabric sítě jsou popsány pomocí modelu Resource (zdrojové soubory pro YAML a JSON) a je možné nasadit do libovolného prostředí, kde běží Service Fabric.

**Aplikace Service Fabric nativní**: Nativní aplikace Service Fabric jsou popsány modelem nativní aplikace (založený na formátu XML manifestů aplikace a služby).  Nativní aplikace Service Fabric nelze spustit v Service Fabric mřížky.

### <a name="service-fabric-mesh-application-concepts"></a>Koncepty mřížky aplikace Service Fabric

**Aplikace**: Aplikace je jednotka nasazení, správu verzí a životního cyklu aplikace sítě. Životní cyklus jednotlivých instancí aplikací se dají spravovat nezávisle.  Aplikace se skládají z jednoho nebo více balíčků kódu služby a nastavení. Aplikace je definována pomocí schématu modelu prostředků Azure (SV).  Služby jsou popsány jako vlastnosti prostředku aplikace do šablony správce prostředků.  Sítě a svazky, které používají aplikace se odkazuje aplikace.  Při vytváření aplikací, aplikací, služeb, sítě a svazky jsou modelovány pomocí modelu prostředků služby Service Fabric.

**Služba**: Službu v aplikaci představuje mikroslužby a provádí kompletní a samostatné funkce. Každá služba se skládá z jednoho nebo více, balíčky kódu, které popisují vše potřebné pro spuštění image kontejneru, který je přidružený k balíčku kódu.  Počet služeb v aplikaci je možné škálovat nahoru a dolů.

**Síť**: Síťový prostředek vytvoří privátní sítě pro vaše aplikace a je nezávislá aplikace nebo služby, které může na něj odkazovat. Více služeb z různých aplikací může být součástí stejné sítě. Sítě jsou nasadit prostředky, které se příslušná aplikace odkazuje.

**Balíček kódu**: Balíčky kódu popisují vše potřebné pro spuštění image kontejneru, který je přidružený k balíčku kódu, včetně následujících:

* Název kontejneru, verzi a registru
* Prostředky procesoru a paměti vyžadované pro každý kontejner
* Koncové body sítě
* Svazky v kontejneru, odkazující na samostatný svazek prostředek připojení.

Všechny balíčky kódu definované jako součást prostředek aplikace se nasazují a aktivují společně jako skupina.

**Svazek**: Svazky jsou adresáře, které se připojit k uvnitř instancí kontejneru, které můžete použít k uložení stavu. Ovladač Azure Files svazek připojí do sdílené složky Azure Files do kontejneru a poskytuje spolehlivé úložiště dat prostřednictvím jakéhokoli rozhraní API, která podporuje úložiště file. Svazky jsou nasadit prostředky, které se příslušná aplikace odkazuje.

### <a name="service-fabric-native-application-concepts"></a>Koncepty nativní aplikace Service Fabric

**Aplikace**: Aplikace je kolekce základních služeb, které provádějí určité funkce nebo funkce. Životní cyklus jednotlivých instancí aplikací se dají spravovat nezávisle.

**Služba**: Služba provádí kompletní a samostatné funkce a můžete spustit a spustit nezávisle na ostatních službách. Služba se skládá z kódu, konfigurace a data. Pro každou službu kód se skládá ze spustitelného souboru binární soubory, konfigurace se skládá z nastavení služby, které načtené být za běhu a data se skládají z libovolného statických dat, který se má používat služba.

**Typ aplikace**: Název/verze přiřazena ke kolekci typů služeb. Je definován v `ApplicationManifest.xml` souboru a vložit v adresáři balíčku aplikace. Adresář se pak zkopíruje do úložiště imagí clusteru Service Fabric. Pojmenované aplikace si můžete vytvořit z tohoto typu aplikace v rámci clusteru.

Přečtěte si [aplikační model](service-fabric-application-model.md) najdete další informace.

**Balíček aplikace**: Disk adresáře, který obsahuje typ aplikace `ApplicationManifest.xml` souboru. Odkazuje na balíčky služeb pro každý typ služby, který vytvoří typ aplikace. Soubory v adresáři balíčku aplikace se zkopírují do úložiště imagí clusteru Service Fabric. Balíček aplikace pro typ e-mailové aplikace může například obsahovat odkazy na balíček služby front, balíček front-endové služby a databáze služby balíčku.

**Aplikace s názvem**: Po zkopírování balíčku aplikace do úložiště imagí můžete vytvořit instanci aplikace v rámci clusteru. Vytvoření instance při zadávání typu aplikace balíčku aplikace, pomocí jeho názvu nebo verze. Každá instance typu aplikace je přiřazen název URI identifikátor URI, který může vypadat: `"fabric:/MyNamedApp"`. V rámci clusteru můžete vytvořit více pojmenované aplikací z jedné aplikace typu. Můžete také vytvořit pojmenované aplikace z aplikace různé typy. Jednotlivé pojmenované aplikace je spravované a verzuje nezávisle na sobě.

**Typ služby**: Název/verze přiřadit balíčky kódu, data balíčky a balíčky pro konfiguraci služby. Typ služby je definována v `ServiceManifest.xml` souboru a v adresáři balíčku service vložené. Adresář balíčku service se pak odkazuje balíčku aplikace `ApplicationManifest.xml` souboru. V rámci clusteru po vytvoření aplikace s názvem, můžete vytvořit pojmenovanou službu z jednoho z typů služeb typu aplikace. Typ služby `ServiceManifest.xml` soubor popisuje službu.

Přečtěte si [aplikační model](service-fabric-application-model.md) najdete další informace.

Existují dva typy služeb:

* **Bezstavové**: Nestavovou službu použijte, když trvalý stav služby je uložená ve službě service externího úložiště, jako je například Azure Storage, Azure SQL Database nebo Azure Cosmos DB. Nestavovou službu použijte, pokud služba nemá žádné trvalého úložiště. Například pro službu kalkulačky, kde hodnoty se předají do služby, výpočet se provádí, která používá tyto hodnoty a pak vrátí výsledek.
* **Stavová**: Stavovou službu použijte, pokud chcete ke správě stavu vaší služby prostřednictvím jeho Reliable Collections nebo Reliable Actors programovací modely Service Fabric. Když vytvoříte pojmenovanou službu, zadejte kolik oddíly, které chcete nacházející se ve vašem státě pro zajištění škálovatelnosti. Také určete, jak často k replikaci svůj stav mezi uzly spolehlivosti. Každá pojmenované služba má jednu primární repliku a více sekundárních replik. Při zápisu na primární replice upravíte pojmenovanou službu stavu. Service Fabric pak replikuje tohoto stavu do sekundárních replik pro synchronizaci stavu. Service Fabric automaticky rozpozná, pokud selže primární repliku a podporuje stávající sekundární repliky na primární repliku. Service Fabric vytvoří sekundární repliku.  

**Replik nebo instancí** naleznete kód (a stavu pro stavové služby), služby, která je nasazena a spuštěna. Zobrazit [replik a instancí](service-fabric-concepts-replica-lifecycle.md).

**Rekonfigurace** se vztahuje k procesu změny sady replik služby. Zobrazit [Rekonfigurace](service-fabric-concepts-reconfiguration.md).

**Balíček služby**: Disk adresáře, který obsahuje typ služby `ServiceManifest.xml` souboru. Tento soubor odkazuje na kód, statických dat a balíčky pro konfiguraci pro typ služby. Soubory v adresáři balíčku služby odkazuje typ aplikace `ApplicationManifest.xml` souboru. Například může odkazovat na kód, statických dat a balíčky pro konfiguraci, které tvoří databázová služba balíčku služby.

**Služba s názvem**: Po vytvoření aplikace s názvem, můžete vytvořit instanci jednoho z jeho typy služeb v rámci clusteru. Zadejte typ služby s použitím jeho název a verzi. Každá instance typu služby je přiřazen název identifikátoru URI oboru v identifikátoru URI s názvem aplikace. Například pokud vytvoříte "Databáze" s názvem služby v rámci "MyNamedApp" s názvem aplikace, identifikátor URI vypadá: `"fabric:/MyNamedApp/MyDatabase"`. V rámci aplikace s názvem můžete vytvořit několik pojmenovaných služeb. Každý s názvem služby může mít svou vlastní schéma oddílu a instance nebo repliky se počítá.

**Balíček kódu**: Adresáře disku, který obsahuje typ služby spustitelné soubory, obvykle EXE nebo knihovny DLL. Soubory v adresáři balíčku kódu odkazuje typ služby `ServiceManifest.xml` souboru. Při vytváření služby s názvem balíček kódu je zkopírován do uzel nebo uzly vybrané ke spuštění s názvem služby. Pak se spustí kód. Existují dva druhy spustitelné soubory balíčku kódu:

* **Spustitelné soubory hosta**: Spustitelné soubory, které spustit jako-je na hostitelském operačním systému (Windows nebo Linuxem). Tyto spustitelných souborů není propojit nebo odkazovat na všechny soubory modulu runtime Service Fabric a proto nemusíte používat programovací modely Service Fabric. Tyto spustitelné soubory se nemůže použít některé funkce Service Fabric, jako je například služba pojmenování pro koncový bod zjišťování. Spustitelné soubory typu Host nemůže oznamovat zatížení metriky, které jsou specifické pro každou instanci služby.
* **Služby spustitelné soubory hostitele**: Spustitelné soubory, které pomocí programovacích modelů Service Fabric odkazování na soubory modulu runtime Service Fabric, povolení funkcí Service Fabric. Například pojmenované instanci služby můžete zaregistrovat koncové body službě pojmenování Service Fabric a může také nahlásit načíst metriky.

**Data balíčku**: Adresář disk, který obsahuje typ služby statické a jen pro čtení datových souborů, obvykle fotografií, zvukových a obrazových souborů. Soubory v adresáři balíčku dat je odkazováno dle typ služby `ServiceManifest.xml` souboru. Při vytváření služby s názvem, balíček data zkopírována do uzel nebo uzly vybrané ke spuštění s názvem služby. Kód se spustí a teď přístup k datové soubory.

**Balíček pro konfiguraci**: Adresář disk, který obsahuje typ služby statické a jen pro čtení konfiguračních souborů, obvykle textové soubory. Soubory v adresáři balíčku konfigurace odkazuje typ služby `ServiceManifest.xml` souboru. Při vytváření služby s názvem soubory v balíčku konfigurace jsou kopírovány jeden nebo více uzlů vybrané ke spuštění s názvem služby. Pak spustí kód ke spuštění a můžete teď přístup ke konfiguračním souborům.

**Kontejnery**: Ve výchozím nastavení Service Fabric nasadí a aktivuje služby jako procesy. Service Fabric dokáže nasadit také služby v imagí kontejnerů. Kontejnery jsou technologie virtualizace, která Virtualizuje základního operačního systému z aplikací. Aplikace a její modulu runtime, závislosti a systémové knihovny běží uvnitř kontejneru. Kontejner má úplné a privátní přístup k zobrazení kontejneru vlastní samostatný konstrukce operačního systému. Service Fabric podporuje kontejnery Docker pro kontejnery Linuxu a Windows Server. Další informace najdete v článku [Service Fabric a kontejnery](service-fabric-containers-overview.md).

**Schéma oddílu**: Když vytvoříte pojmenovanou službu, zadat schéma oddílu. Služby se značné množství stavu rozdělení dat napříč oddíly, které šíří stavu mezi uzly clusteru. Rozdělení dat napříč oddíly, umožňuje škálovat pojmenovanou službu stavu. V rámci oddílu mají bezstavové služby s názvem instance, že stavová s názvem služby repliky. Obvykle bezstavové služby s názvem mít jen jeden oddíl, protože nemají žádné vnitřní stav. Instance oddílu poskytují dostupnosti. Pokud se jedna instance nezdaří, dalších instancí se dál fungovat normálně a vytvoří novou instanci Service Fabric. Stavová s názvem služby spravovat, že jejich stav v rámci repliky a každý oddíl má svůj vlastní repliky nastavíte tak, aby stav je udržovat synchronizované. By měl replika selže, Service Fabric vytvoří novou repliku z existujících replik.

Přečtěte si [spolehlivých služeb Service Fabric oddílu](service-fabric-concepts-partitioning.md) najdete další informace.

## <a name="system-services"></a>Systémové služby
Existují systémových služeb, které jsou vytvořeny v každém clusteru, které poskytují funkce dané platformy Service Fabric.

**Služba pojmenování**: Každý cluster Service Fabric má službu pojmenování, které se překládá názvy služeb do umístění v clusteru. Spravovat názvy služeb a vlastnosti, jako je internet systému DNS (Domain Name) pro cluster. Klienti bezpečně komunikovat s libovolný uzel v clusteru s použitím služby pojmenování přeložit název služby a jeho umístění. Aplikace se přesouvat v rámci clusteru. Například to může být kvůli chybám, vyrovnávání prostředků nebo změně velikosti clusteru. Můžete vyvíjet, služeb a klientů, které odkazují aktuální umístění v síti. Klienti získat skutečnou adresu IP a port, kde je aktuálně spuštěna.

Čtení [komunikace se službami](service-fabric-connect-and-communicate-with-services.md) Další informace o komunikaci klienta a služby rozhraní API, které fungují ve službě pojmenování.

**Image Store service**: Každý cluster Service Fabric má službu Image Store, kde je aplikace nasazená, vyvíjených balíčků uchovávat. Zkopírujte balíček aplikace pro Store Image a pak zaregistrujte typ aplikace, které jsou obsaženy v rámci daného balíčku aplikace. Po zřízení typu aplikace vytvoříte aplikaci s názvem z něj. Po odstranění všechny pojmenované aplikace, můžete zrušit registraci typu aplikace ze služby Image Store.

Čtení [Principy nastavení ImageStoreConnectionString](service-fabric-image-store-connection-string.md) Další informace o službě Image Store.

Čtení [nasazení aplikace](service-fabric-deploy-remove-applications.md) najdete další informace o nasazení aplikace ke službě Store bitové kopie.

**Služba Správce převzetí služeb při selhání**: Každý cluster Service Fabric obsahuje službu Správce převzetí služeb při selhání, která je zodpovědná za následující akce:
   - Provádí funkce související s vysokou dostupnost a konzistence služeb.
   - Orchestruje inovace aplikací a clusteru.
   - Komunikuje s jinými součástmi systému.

**Nástroj pro správu oprav služby**: Toto je volitelná systémová služba, která umožňuje opravit akce provést v clusteru tak, aby je bezpečné, použijte a transparentní. Nástroj pro správu oprav se používá v:
   - Provádí Azure údržbu opraví na [stříbrné a zlaté odolnosti](service-fabric-cluster-capacity.md#the-durability-characteristics-of-the-cluster) clustery Azure Service Fabric.
   - Provádění akcí oprav pro [aplikace orchestraci oprav](service-fabric-patch-orchestration-application.md)

## <a name="deployment-and-application-models"></a>Modely nasazení a aplikace 

K nasazení služby, budete muset popisují, jak by měly být spuštěny. Service Fabric podporuje tři různé modely nasazení:

### <a name="resource-model-preview"></a>Model prostředků (preview)
Prostředky topologie Fabric Service jsou cokoli, co je možné nasadit jednotlivě do Service Fabric; včetně aplikací, služeb, sítě a svazky. Prostředky jsou definovány pomocí souboru JSON, který je možné nasadit do koncového bodu clusteru.  Pro Service Fabric sítě se používá schéma modelu prostředků Azure. Schéma souboru YAML lze také snadněji vytvářet soubory definic. Prostředky je možné nasadit všude, kde běží Service Fabric. Model prostředků je nejjednodušší způsob, jak popisují své aplikace Service Fabric. Hlavní zaměřuje se na jednoduché nasazení a správu kontejnerizovaných služeb. Další informace najdete v článku [Úvod do modelu prostředků služby Service Fabric](/azure/service-fabric-mesh/service-fabric-mesh-service-fabric-resources).

### <a name="native-model"></a>Nativní model
Model nativní aplikace poskytuje svoje aplikace s využitím úplného nízké úrovně přístupu do Service Fabric. Aplikace a služby jsou definovány jako registrovaných typů v souborech manifestu XML.

Nativní model podporuje Reliable Services a Reliable Actors rozhraní, která poskytuje přístup k rozhraní API modulu runtime Service Fabric a rozhraní API v jazyce C# a Java pro správu clusteru. Nativní model také podporuje libovolný kontejnery a spustitelné soubory. Nativní model nepodporuje [sítě pro Service Fabric prostředí](/azure/service-fabric-mesh/service-fabric-mesh-overview).

**Reliable Services**: Rozhraní API pro vytváření bezstavových a stavových služeb. Stavové služby uložení jejich stavu v Reliable Collections jako slovník nebo fronty. Můžete také zařadit různé komunikační balíky, například webové rozhraní API nebo Windows Communication Foundation (WCF).

**Reliable Actors**: Rozhraní API pro vytváření objektů bezstavových a stavových prostřednictvím virtuálního objektu Actor programovací model. Tento model je užitečný v případě, že máte spoustu nezávislých jednotek výpočtu nebo stavu. Tento model používá založen na řadě vy model vlákna, takže je vhodné, aby kód, který volá navýšení kapacity na jiné objekty actor nebo služby, protože prvek "actor" jednotlivých nemůže zpracovat ostatní příchozí požadavky, dokud se nedokončí všechny odchozí požadavky.

Svoje stávající aplikace. můžete také spustit na platformě Service Fabric:

**Kontejnery**:  Service Fabric podporuje nasazení kontejnerů Dockeru v Linuxu a Windows Server kontejnery ve Windows serveru 2016, společně s podporou pro režimu izolace Hyper-V. V Service Fabric [aplikační model](service-fabric-application-model.md), kontejner reprezentuje hostitele aplikace v několika službu jsou umístěny repliky. Service Fabric můžete spustit všechny kontejnery a scénář je podobný scénáři spustitelného souboru hosta kde balíček existující aplikaci uvnitř kontejneru. Kromě toho můžete [spuštění služeb Service Fabric v kontejnerech](service-fabric-services-inside-containers.md) také.

**Spustitelné soubory hosta**: Jakýkoli typ kódu, jako je Node.js, Java nebo C++ v Azure Service Fabric můžete spustit jako službu. Service Fabric odkazuje na tyto typy služeb jako hostující spustitelné soubory, které jsou považovány za bezstavové služby. Výhody ke spuštění spustitelného souboru v clusteru Service Fabric Host zahrnují vysokou dostupnost, monitorování stavu, Správa životního cyklu aplikací, s vysokou hustotou a možnosti rozpoznání.

Přečtěte si [vybrat programovací model pro vaši službu](service-fabric-choose-framework.md) najdete další informace.

### <a name="docker-compose"></a>Docker Compose 
[Docker Compose](https://docs.docker.com/compose/) je součástí projektu Dockeru. Service Fabric poskytuje omezenou podporu pro [nasazení aplikací pomocí Docker Compose modelu](service-fabric-docker-compose.md).

## <a name="environments"></a>Prostředí

Service Fabric je technologie open source platforma, která na základě několika různými službami a produkty. Společnost Microsoft poskytuje následující možnosti:

 - **Síť Azure Service Fabric**: Plně spravovaná služba pro spouštění aplikací Service Fabric v Microsoft Azure.
 - **Azure Service Fabric**: Nabídka clusteru Service Fabric hostované v Azure. Poskytuje integrace mezi službami Service Fabric a infrastrukturu Azure, spolu s upgradu a správy konfigurace clusterů Service Fabric.
 - **Service Fabric samostatné**: Instalace a konfigurace nástroje pro sadu [nasadit clustery Service Fabric kdekoli](/azure/service-fabric/service-fabric-deploy-anywhere) (v místním nebo jakýkoli jiný poskytovatel cloudu). Není spravuje Azure.
 - **Vývojový cluster Service Fabric**: Poskytuje místní vývojové prostředí na Windows, Linux nebo Mac pro vývoj aplikací Service Fabric.

## <a name="environment-framework-and-deployment-model-support-matrix"></a>Matice podpory prostředí, rozhraní a modelu nasazení
Různá prostředí mají různé úrovně podpory platformy a modely nasazení. Následující tabulka popisuje podporované architektury a kombinace modelu nasazení.

| Typ aplikace | Popisuje | Síť Azure Service Fabric | Clustery Azure Service Fabric (libovolný operační systém)| Místní cluster | Samostatný cluster |
|---|---|---|---|---|---|---|---|---|---|
| Aplikace Service Fabric sítě | Model prostředků (YAML a JSON) | Podporováno |Nepodporuje se | Windows – podporováno, Linux a Mac – není podporované | Windows – není podporované |
|Nativní aplikace Service Fabric | Model nativní aplikace (XML) | Nepodporuje se| Podporováno|Podporováno|Windows – podporováno|

Následující tabulka popisuje různé aplikačních modelů a nástrojů, která existuje pro ně pro Service Fabric.

| Typ aplikace | Popisuje | Visual Studio | Eclipse | SFCTL | AZ CLI | PowerShell|
|---|---|---|---|---|---|---|---|---|---|
| Aplikace Service Fabric sítě | Model prostředků (YAML a JSON) | VS 2017 |Nepodporuje se |Nepodporuje se | Podporuje se – pouze prostředí sítě | Nepodporuje se|
|Nativní aplikace Service Fabric | Model nativní aplikace (XML) | VS 2017 a VS 2015| Podporováno|Podporováno|Podporováno|Podporováno|

<!--Every topic should have next steps and links to the next logical set of content to keep the customer engaged-->
## <a name="next-steps"></a>Další postup
Další informace o službě Service Fabric:

* [Přehled Service Fabric](service-fabric-overview.md)
* [Proč při sestavování aplikací zvolit přístup založený na mikroslužbách?](service-fabric-overview-microservices.md)
* [Scénáře aplikací](service-fabric-application-scenarios.md)

Další informace o Service Fabric sítě:

* [Přehled služby prostředků infrastruktury sítě](/azure/service-fabric-mesh/service-fabric-mesh-overview)
