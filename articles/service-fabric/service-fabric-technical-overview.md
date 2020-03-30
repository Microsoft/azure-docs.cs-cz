---
title: Naučte terminologie Azure Service Fabric
description: Naučte se klíčové service fabric terminologie a koncepty používané ve zbytku dokumentace.
author: masnider
ms.topic: conceptual
ms.date: 09/17/2018
ms.author: masnider
ms.custom: sfrev
ms.openlocfilehash: a9266c2a8d2ad179cfdb12e367a14f37d1abc9b3
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "79258236"
---
# <a name="service-fabric-terminology-overview"></a>Přehled terminologie Service Fabric

Azure Service Fabric je platforma distribuovaných systémů usnadňující balení, nasazování a spravování škálovatelných a spolehlivých mikroslužeb.  Clustery Service Fabric můžete [hostovat kdekoli](service-fabric-deploy-anywhere.md): Azure, v místním datovém centru nebo na libovolném poskytovateli cloudu.  Service Fabric je orchestrátor, který pohání [Azure Service Fabric Mesh](/azure/service-fabric-mesh). Můžete použít libovolný rámec k zápisu služby a zvolte, kde chcete spustit aplikaci z více možností prostředí. Tento článek podrobně popisuje terminologii používanou service fabric k pochopení pojmů používaných v dokumentaci.

## <a name="infrastructure-concepts"></a>Koncepce infrastruktury

**Cluster**: Síťová sada virtuálních nebo fyzických počítačů, do kterých jsou vaše mikroslužby nasazeny a spravovány.  Clustery je možné škálovat na tisíce počítačů.

**Uzel**: Počítač nebo virtuální počítač, který je součástí clusteru, se nazývá *uzel*. Každému uzlu je přiřazen název uzlu (řetězec). Uzly mají vlastnosti, například vlastnosti umístění. Každý počítač nebo virtuální počítač má službu windows s automatickým spuštěním , `FabricHost.exe` `Fabric.exe` která `FabricGateway.exe`se spustí při spuštění a spustí dva spustitelné soubory: a . Tyto dva spustitelné soubory tvoří uzel. Pro testovací scénáře můžete hostovat více uzlů na jednom počítači nebo `Fabric.exe` `FabricGateway.exe`virtuálním počítači spuštěním více instancí a .

## <a name="application-and-service-concepts"></a>Aplikační a servisní koncepty

**Service Fabric Mesh Application**: Service Fabric Mesh Aplikace jsou popsány model prostředků (YAML a JSON soubory prostředků) a lze nasadit do libovolného prostředí, kde service fabric běží.

**Nativní aplikace Service Fabric**: Nativní aplikace Service Fabric jsou popsány nativním aplikačním modelem (manifesty aplikací a služeb založených na jazyce XML).  Nativní aplikace service fabric nelze spustit v síti Service Fabric.

### <a name="service-fabric-mesh-application-concepts"></a>Koncepty síťových aplikací service fabric

**Aplikace**: Aplikace je jednotka nasazení, správy verzí a životnosti aplikace Mesh. Životní cyklus každé instance aplikace lze spravovat nezávisle.  Aplikace se skládají z jednoho nebo více balíčků a nastavení kódu služby. Aplikace je definována pomocí schématu modelu prostředků Azure (RM).  Služby jsou popsány jako vlastnosti prostředku aplikace v šabloně RM.  Na sítě a svazky používané aplikací aplikace odkazuje.  Při vytváření aplikace jsou aplikace, služby, sítě a svazky modelovány pomocí modelu prostředků Service Fabric.

**Služba**: Služba v aplikaci představuje mikroslužbu a provádí úplnou a samostatnou funkci. Každá služba se skládá z jednoho nebo více balíčků kódu, které popisují vše potřebné ke spuštění image kontejneru přidružené k balíčku kódu.  Počet služeb v aplikaci lze škálovat nahoru a dolů.

**Síť**: Síťový prostředek vytvoří pro vaše aplikace privátní síť a je nezávislý na aplikacích nebo službách, které na něj mohou odkazovat. Součástí stejné sítě může být více služeb z různých aplikací. Sítě jsou nasaditelné prostředky, na které odkazují aplikace.

**Balíček kódu**: Balíčky kódu popisují vše potřebné ke spuštění image kontejneru přidružené k balíčku kódu, včetně následujících:

* Název kontejneru, verze a registr
* Prostředky procesoru a paměti potřebné pro každý kontejner
* Koncové body sítě
* Svazky pro připojení do kontejneru, odkazující na samostatný prostředek svazku.

Všechny balíčky kódu definované jako součást prostředku aplikace jsou nasazeny a aktivovány společně jako skupina.

**Svazek**: Svazky jsou adresáře, které se připevní uvnitř instancí kontejneru, které můžete použít k zachování stavu. Ovladač svazku Souborů Azure připojí sdílené složky Azure Files do kontejneru a poskytuje spolehlivé úložiště dat prostřednictvím libovolného rozhraní API, které podporuje úložiště souborů. Svazky jsou nasaditelné prostředky, na které odkazují aplikace.

### <a name="service-fabric-native-application-concepts"></a>Koncepty nativních aplikací Service Fabric

**Aplikace**: Aplikace je kolekce základních služeb, které plní určitou funkci nebo funkce. Životní cyklus každé instance aplikace lze spravovat nezávisle.

**Služba**: Služba provádí úplnou a samostatnou funkci a může být spuštěna a spuštěna nezávisle na jiných službách. Služba se skládá z kódu, konfigurace a dat. Pro každou službu se kód skládá z spustitelných binárních souborů, konfigurace se skládá z nastavení služby, které lze načíst za běhu, a data se skládají z libovolných statických dat, která má služba spotřebovávat.

**Typ aplikace**: Název/verze přiřazená kolekci typů služeb. Je definován v `ApplicationManifest.xml` souboru a vložen do adresáře balíčku aplikace. Adresář je pak zkopírován do úložiště bitových obrázků clusteru Service Fabric. Potom můžete vytvořit pojmenovanou aplikaci z tohoto typu aplikace v rámci clusteru.

Další informace naleznete v článku [aplikačního modelu.](service-fabric-application-model.md)

**Balíček aplikace**: Diskový adresář obsahující `ApplicationManifest.xml` soubor typu aplikace. Odkazuje na balíčky služeb pro každý typ služby, který tvoří typ aplikace. Soubory v adresáři balíčku aplikace jsou zkopírovány do úložiště bitových obrázků clusteru Service Fabric. Balíček aplikace pro typ e-mailové aplikace může například obsahovat odkazy na balíček služby fronty, balíček front-end-service a balíček služby databáze.

**Po**zkopírování balíčku aplikace do úložiště obrázků vytvoříte instanci aplikace v rámci clusteru. Instance se vytvoří, když zadáte typ aplikace balíčku aplikace pomocí jeho názvu nebo verze. Každé instanci typu aplikace je přiřazen název identifikátoru `"fabric:/MyNamedApp"`URI (uniformní ho dispozice" – . V rámci clusteru můžete vytvořit více pojmenovaných aplikací z jednoho typu aplikace. Můžete také vytvořit pojmenované aplikace z různých typů aplikací. Každá pojmenovaná aplikace je spravována a verzí nezávisle.

**Typ služby**: Název/verze přiřazená balíčkům kódu služby, balíčkům dat a konfiguračním balíčkům. Typ služby je `ServiceManifest.xml` definován v souboru a vložen do adresáře balíčku služeb. Na adresář balíčku služeb je pak odkazován `ApplicationManifest.xml` souborem balíčku aplikace. V rámci clusteru můžete po vytvoření pojmenované aplikace vytvořit pojmenovanou službu z jednoho z typů služeb typu aplikace. Soubor typu služby `ServiceManifest.xml` popisuje službu.

Další informace naleznete v článku [aplikačního modelu.](service-fabric-application-model.md)

Existují dva typy služeb:

* **Bezstavové**: Použijte bezstavovou službu, když je trvalý stav služby uložen v externí službě úložiště, jako je Azure Storage, Azure SQL Database nebo Azure Cosmos DB. Bezstavové služby použijte, pokud služba nemá trvalé úložiště. Například pro službu kalkulačky, kde jsou do služby předány hodnoty, je proveden výpočt, který používá tyto hodnoty a pak je vrácen výsledek.
* **Stavové**: Použijte stavovou službu, pokud chcete Service Fabric spravovat stav služby prostřednictvím svých spolehlivé kolekce nebo spolehlivé actors programovací modely. Při vytváření pojmenované služby určete, kolik oddílů chcete rozložit stav pro škálovatelnost. Také určete, kolikrát chcete replikovat stav mezi uzly, pro spolehlivost. Každá pojmenovaná služba má jednu primární repliku a více sekundárních replik. Při zápisu do primární repliky můžete upravit stav pojmenované služby. Service Fabric pak replikuje tento stav do všech sekundárních replik, aby váš stav v synchronizaci. Service Fabric automaticky zjistí, když selže primární replika a povýší existující sekundární repliku na primární repliku. Service Fabric pak vytvoří novou sekundární repliku.  

**Repliky nebo instance** odkazují na kód (a stav pro stavové služby) služby, která je nasazená a spuštěná. Viz [Repliky a instance](service-fabric-concepts-replica-lifecycle.md).

**Změna konfigurace** odkazuje na proces jakékoli změny v sadě replik služby. Viz [Změna konfigurace](service-fabric-concepts-reconfiguration.md).

**Balíček služeb**: Diskový adresář obsahující `ServiceManifest.xml` soubor typu služby. Tento soubor odkazuje na kód, statická data a konfigurační balíčky pro typ služby. Na soubory v adresáři balíčků služeb odkazuje `ApplicationManifest.xml` soubor typu aplikace. Balíček služeb může například odkazovat na kód, statická data a konfigurační balíčky, které tvoří databázovou službu.

**Po**vytvoření pojmenované aplikace můžete vytvořit instanci jednoho z jejích typů služeb v rámci clusteru. Typ služby zadáte pomocí jeho názvu/verze. Každé instanci typu služby je přiřazen název identifikátoru URI s rozsahem pod identifikátorem URI pojmenované aplikace. Pokud například vytvoříte službu "MyDatabase" s názvem "MyNamedApp" s `"fabric:/MyNamedApp/MyDatabase"`názvem aplikace, identifikátor URI vypadá takto: . V rámci pojmenované aplikace můžete vytvořit několik pojmenovaných služeb. Každá pojmenovaná služba může mít své vlastní schéma oddílů a počty instancí nebo replik.

**Balíček kódu**: Diskový adresář obsahující spustitelné soubory typu služby, obvykle soubory EXE/DLL. Na soubory v adresáři balíčku kódu odkazuje soubor `ServiceManifest.xml` typu služby. Při vytváření pojmenované služby je balíček kódu zkopírován do uzlu nebo uzlů vybraných ke spuštění pojmenované služby. Pak se spustí kód. Existují dva typy spustitelných souborů balíčku kódu:

* **Spustitelné soubory hosta**: Spustitelné soubory, které běží jako v hostitelském operačním systému (Windows nebo Linux). Tyto spustitelné soubory neodkazují na žádné soubory runtime Service Fabric ani na ně neodkazují, a proto nepoužívají žádné programovací modely Service Fabric. Tyto spustitelné soubory nelze použít některé funkce Service Fabric, jako je například služba pojmenování pro zjišťování koncového bodu. Spustitelné soubory hosta nemohou vykazovat metriky zatížení, které jsou specifické pro každou instanci služby.
* **Spustitelné soubory hostitele služby**: Spustitelné soubory, které používají programovací modely Service Fabric propojením se soubory runtime Service Fabric, což umožňuje funkce Service Fabric. Například instance pojmenované služby může zaregistrovat koncové body pomocí služby Service Fabric pojmenování služby a může také vykazovat metriky zatížení.

**Datový balíček**: Diskový adresář, který obsahuje statické datové soubory typu služby jen pro čtení, obvykle soubory fotografií, zvuku a videa. Na soubory v adresáři datového balíčku odkazuje `ServiceManifest.xml` soubor typu služby. Při vytváření pojmenované služby je datový balíček zkopírován do uzlu nebo uzlů vybraných ke spuštění pojmenované služby. Kód se spustí a nyní může přistupovat k datovým souborům.

**Konfigurační balíček**: Diskový adresář, který obsahuje statické konfigurační soubory typu služby jen pro čtení, obvykle textové soubory. Na soubory v adresáři konfiguračního balíčku `ServiceManifest.xml` odkazuje soubor typu služby. Když vytvoříte pojmenovanou službu, soubory v konfiguračním balíčku se zkopírují do jednoho nebo více uzlů vybraných ke spuštění pojmenované služby. Poté se spustí kód a nyní může přistupovat ke konfiguračním souborům.

**Kontejnery**: Ve výchozím nastavení Service Fabric nasazuje a aktivuje služby jako procesy. Service Fabric můžete také nasadit služby v inaimages kontejneru. Kontejnery jsou virtualizační technologie, která abstrahuje základní operační systém z aplikací. Aplikace a její runtime, závislosti a systémové knihovny spustit uvnitř kontejneru. Kontejner má úplný soukromý přístup k vlastnímu izolovanému zobrazení konstrukce operačního systému kontejneru. Service Fabric podporuje kontejnery Windows Server a kontejnery Dockeru na Linuxu. Další informace naleznete [v service fabric a kontejnery](service-fabric-containers-overview.md).

**Schéma oddílu**: Při vytváření pojmenované služby zadáte schéma oddílu. Služby s značné množství stavu rozdělit data mezi oddíly, které šíří stav mezi uzly clusteru. Rozdělením dat mezi oddíly, stav pojmenované služby můžete škálovat. V rámci oddílu bezstavové pojmenované služby mají instance, zatímco stavové pojmenované služby mají repliky. Bezstavové pojmenované služby mají obvykle pouze jeden oddíl, protože nemají žádný vnitřní stav. Instance oddílu poskytují dostupnost. Pokud jedna instance selže, ostatní instance nadále fungovat normálně a pak Service Fabric vytvoří novou instanci. Stavové pojmenované služby udržují svůj stav v replikách a každý oddíl má vlastní sadu replik, takže stav je udržován v synchronizaci. Pokud replika selže, Service Fabric vytvoří novou repliku z existujících replik.

Další informace naleznete v článku [spolehlivé služby Partition Service Fabric.](service-fabric-concepts-partitioning.md)

## <a name="system-services"></a>Systémové služby

Existují systémové služby, které jsou vytvořeny v každém clusteru, které poskytují možnosti platformy Service Fabric.

**Pojmenování :** Každý cluster Service Fabric má pojmenování služby, která řeší názvy služeb do umístění v clusteru. Spravujete názvy a vlastnosti služeb, například internetovou doménovou doménu (DNS) pro cluster. Klienti bezpečně komunikují s libovolným uzlem v clusteru pomocí služby pojmenování k vyřešení názvu služby a jejího umístění. Aplikace se pohybují v rámci clusteru. Může to být například způsobeno selháním, vyvažováním prostředků nebo změnami velikosti clusteru. Můžete vyvinout služby a klienty, kteří řeší aktuální umístění v síti. Klienti získají skutečnou ip adresu počítače a port, kde je aktuálně spuštěn.

Další informace o klientských a komunikačních komunikačních apich pro komunikaci klientů, které pracují se službou pojmenování, naleznete v [části Komunikujte se službami.](service-fabric-connect-and-communicate-with-services.md)

**Služba Úložiště bitových obrázků**: Každý cluster Service Fabric má službu Úložiště bitových obrázků, kde jsou uloženy balíčky aplikací s verzí. Zkopírujte balíček aplikace do úložiště obrázků a pak zaregistrujte typ aplikace obsažený v tomto balíčku aplikace. Po zřízení typu aplikace vytvoříte z ní pojmenovanou aplikaci. Po odstranění všech pojmenovaných aplikací můžete zrušit registraci typu aplikace ze služby Úložiště bitových obrazů.

Přečtěte si [článek Vysvětlení nastavení ImageStoreConnectionString](service-fabric-image-store-connection-string.md) pro další informace o službě Úložiště bitových obrázků.

Další informace o nasazení aplikací do služby Úložiště bitových údajů načtete v článku [Nasazení aplikace.](service-fabric-deploy-remove-applications.md)

**Služba Správce převzetí služeb při selhání**: Každý cluster prostředků infrastruktury služeb má službu Správce převzetí služeb při selhání, která je zodpovědná za následující akce:

 - Provádí funkce související s vysokou dostupností a konzistencí služeb.
 - Orchestruje upgrady aplikací a clusterů.
 - Spolupracuje s jinými součástmi systému.

**Služba Správce oprav**: Jedná se o volitelnou systémovou službu, která umožňuje provádět akce oprav v clusteru způsobem, který je bezpečný, automatizovaný a transparentní. Správce oprav se používá v:

   - Provádění oprav údržby Azure na clusterech Azure Service Fabric [s odolností a odolností.](service-fabric-cluster-capacity.md#the-durability-characteristics-of-the-cluster)
   - Provádění oprav aplikace [Orchestrace opravy](service-fabric-patch-orchestration-application.md)

## <a name="deployment-and-application-models"></a>Modely nasazení a aplikace

Chcete-li nasadit služby, musíte popsat, jak by měly být spuštěny. Service Fabric podporuje tři různé modely nasazení:

### <a name="resource-model-preview"></a>Model prostředků (náhled)

Prostředky Service Fabric jsou cokoli, co lze nasadit jednotlivě do Service Fabric; včetně aplikací, služeb, sítí a svazků. Prostředky jsou definovány pomocí souboru JSON, který lze nasadit do koncového bodu clusteru.  Pro síť Service Fabric se používá schéma modelu prostředků Azure. Schéma souboru YAML lze také použít ke snadněji vytvářet definiční soubory. Prostředky lze nasadit kdekoli service fabric spustí. Model prostředků je nejjednodušší způsob, jak popsat aplikace Service Fabric. Zaměřuje se především na jednoduché nasazení a správu kontejnerizovaných služeb. Další informace naleznete [v části Úvod do modelu prostředků service fabric](/azure/service-fabric-mesh/service-fabric-mesh-service-fabric-resources).

### <a name="native-model"></a>Nativní model

Nativní aplikační model poskytuje vašim aplikacím úplný nízkoúrovňový přístup k Service Fabric. Aplikace a služby jsou definovány jako registrované typy v souborech manifestů XML.

Nativní model podporuje architektury spolehlivé služby a spolehlivé objekty actor, které poskytuje přístup k rozhraní API prostředí Service Fabric runtime a rozhraní API pro správu clusteru v jazyce C# a java. Nativní model také podporuje libovolné kontejnery a spustitelné soubory. Nativní model není podporován v [prostředí Service Fabric Mesh](/azure/service-fabric-mesh/service-fabric-mesh-overview).

**Spolehlivé služby:** Rozhraní API pro vytváření bezstavových a stavových služeb. Stavové služby ukládají svůj stav v spolehlivé kolekce, jako je například slovník nebo fronty. Můžete také připojit různé komunikační zásobníky, jako je například webové rozhraní API a Windows Communication Foundation (WCF).

**Spolehlivé objekty actor:** Rozhraní API pro vytváření bezstavových a stavových objektů prostřednictvím virtuálního programovacího modelu actor. Tento model je užitečný, pokud máte velké množství nezávislých jednotek výpočtu nebo stavu. Tento model používá model tahového vlákna, takže je nejlepší se vyhnout kódu, který volá na jiné objekty actor nebo služby, protože jednotlivý objekt actor nemůže zpracovat jiné příchozí požadavky, dokud nebudou dokončeny všechny jeho odchozí požadavky.

Můžete také spustit existující aplikace na Service Fabric:

**Kontejnery**: Service Fabric podporuje nasazení kontejnerů Dockeru v kontejnerech Linuxu a Windows Serveru na Windows Server 2016 spolu s podporou režimu izolace Hyper-V. V modelu [aplikace](service-fabric-application-model.md)Service Fabric představuje kontejner hostitele aplikace, ve kterém je umístěno více replik služby. Service Fabric můžete spustit všechny kontejnery a scénář je podobný spustitelnému scénáři hosta, kde zabalíte existující aplikaci uvnitř kontejneru. Kromě toho můžete [spustit service fabric služby uvnitř kontejnerů](service-fabric-services-inside-containers.md) také.

**Spustitelné soubory hosta**: Jako službu můžete spustit libovolný typ kódu, například Node.js, Python, Java nebo C++ v Azure Service Fabric. Service Fabric odkazuje na tyto typy služeb jako spustitelné soubory hosta, které jsou považovány za služby bez státní příslušnosti. Mezi výhody spuštění spustitelného souboru hosta v clusteru Service Fabric patří vysoká dostupnost, monitorování stavu, správa životního cyklu aplikací, vysoká hustota a zjistitelnost.

Další informace naleznete v článku [Výběr programovacího modelu pro službu.](service-fabric-choose-framework.md)

### <a name="docker-compose"></a>Docker Compose 

[Docker Compose](https://docs.docker.com/compose/) je součástí projektu Docker. Service Fabric poskytuje omezenou podporu pro [nasazení aplikací pomocí modelu Docker Compose](service-fabric-docker-compose.md).

## <a name="environments"></a>Prostředí

Service Fabric je technologie platformy s otevřeným zdrojovým kódem, na které je založeno několik různých služeb a produktů. Společnost Microsoft nabízí následující možnosti:

 - **Azure Service Fabric Mesh:** Plně spravovaná služba pro spouštění aplikací Service Fabric v Microsoft Azure.
 - **Azure Service Fabric:** Nabídka clusteru Azure hostované služby Fabric. Poskytuje integraci mezi Service Fabric a infrastruktury Azure, spolu s upgradem a správou konfigurace clusterů Service Fabric.
 - **Service Fabric samostatný**: Sada nástrojů pro instalaci a konfiguraci [pro nasazení clusterů Service Fabric kdekoli](/azure/service-fabric/service-fabric-deploy-anywhere) (místně nebo na libovolném poskytovateli cloudu). Není spravováno Azure.
 - **Service Fabric vývoj clusteru**: Poskytuje prostředí pro místní vývoj v systému Windows, Linux nebo Mac pro vývoj aplikací Service Fabric.

## <a name="environment-framework-and-deployment-model-support-matrix"></a>Matice podpory modelu prostředí, architektury a nasazení

Různá prostředí mají různé úrovně podpory pro architektury a modely nasazení. Následující tabulka popisuje podporované kombinace architektury a modelu nasazení.

| Typ aplikace | Popsáno | Síť prostředků infrastruktury služby Azure | Clustery Fabric služby Azure (libovolný operační systém)| Místní cluster | Samostatný cluster |
|---|---|---|---|---|---|
| Síťové aplikace service fabric | Model prostředků (YAML & JSON) | Podporuje se |Nepodporuje se | Windows- podporované, Linux a Mac- není podporován | Systém Windows - není podporován |
|Nativní aplikace Service Fabric | Nativní aplikační model (XML) | Nepodporuje se| Podporuje se|Podporuje se|Systém Windows – podporován|

Následující tabulka popisuje různé modely aplikací a nástroje, které pro ně existují proti Service Fabric.

| Typ aplikace | Popsáno | Visual Studio | Eclipse | SFCTL | AZ CLI | PowerShell|
|---|---|---|---|---|---|---|
| Síťové aplikace service fabric | Model prostředků (YAML & JSON) | VS 2017 |Nepodporuje se |Nepodporuje se | Podporováno – pouze síťové prostředí | Nepodporuje se|
|Nativní aplikace Service Fabric | Nativní aplikační model (XML) | VS 2017 a VS 2015| Podporuje se|Podporuje se|Podporuje se|Podporuje se|

## <a name="next-steps"></a>Další kroky

Další informace o službě Service Fabric:

* [Přehled služby Service Fabric](service-fabric-overview.md)
* [Proč při sestavování aplikací zvolit přístup založený na mikroslužbách?](service-fabric-overview-microservices.md)
* [Scénáře aplikací](service-fabric-application-scenarios.md)

Další informace o síti Service Fabric Mesh:

* [Přehled sítě Service Fabric Mesh](/azure/service-fabric-mesh/service-fabric-mesh-overview)
