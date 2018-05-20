---
title: Další Azure Service Fabric terminologie | Microsoft Docs
description: Přehled terminologie Service Fabric. Popisuje klíčové terminologie konceptů a termínů používaných ve zbývající části v dokumentaci.
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
ms.date: 03/26/2018
ms.author: ryanwi
ms.openlocfilehash: e3da081f9b327031d6d1e0afd2f2fb52383bf933
ms.sourcegitcommit: eb75f177fc59d90b1b667afcfe64ac51936e2638
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/16/2018
---
# <a name="service-fabric-terminology-overview"></a>Přehled terminologie Service Fabric
Azure Service Fabric je platforma distribuovaných systémů usnadňující balení, nasazování a spravování škálovatelných a spolehlivých mikroslužeb. Tento článek podrobně technologiím použitým pomocí Service Fabric pochopit termínů používaných v dokumentaci.

Koncepty uvedené v této části jsou popsány i v následující videa Microsoft Virtual Academy: <a target="_blank" href="https://mva.microsoft.com/en-US/training-courses/building-microservices-applications-on-azure-service-fabric-16747?l=tbuZM46yC_5206218965">základní koncepty</a>, <a target="_blank" href="https://mva.microsoft.com/en-US/training-courses/building-microservices-applications-on-azure-service-fabric-16747?l=tlkI046yC_2906218965">koncepty návrhu</a>, a <a target="_blank" href="https://mva.microsoft.com/en-US/training-courses/building-microservices-applications-on-azure-service-fabric-16747?l=x7CVH56yC_1406218965">Runtime koncepty</a>.

## <a name="infrastructure-concepts"></a>Koncepty infrastruktury
**Cluster**: sadu virtuální nebo fyzické počítače, do kterých jsou nasazené a spravovat vaše mikroslužeb připojené k síti.  Clustery je možné škálovat na tisíce počítačů.

**Uzel**: počítač nebo virtuální počítač, který je součástí clusteru, se nazývá *uzlu*. Každému uzlu je přiřazen název uzlu (řetězec). Uzly mají charakteristiky, jako je například vlastnosti umístění. Každý počítač nebo virtuální počítač má služby systému Windows automaticky spouštěná `FabricHost.exe`, který spuštění při spuštění a pak spustí dvě spustitelné soubory: `Fabric.exe` a `FabricGateway.exe`. Tyto dvě spustitelné soubory tvoří uzlu. Pro testování scénáře, může hostovat více uzlů na jeden počítač nebo virtuální počítač spuštěním několika instancí `Fabric.exe` a `FabricGateway.exe`.

## <a name="application-concepts"></a>Koncepty aplikace
**Typ aplikace**: název a verzi, která je přiřazena ke kolekci typů služeb. Je definována v `ApplicationManifest.xml` souboru a vkládat v adresáři balíčku aplikace. Adresář je pak zkopíruje do úložiště bitové kopie cluster Service Fabric. Potom můžete vytvořit s názvem aplikace z tohoto typu aplikací v rámci clusteru.

Pro čtení [aplikačního modelu](service-fabric-application-model.md) Další informace najdete v článku.

**Balíček aplikace**: adresář disk obsahující typ aplikace `ApplicationManifest.xml` souboru. Odkazuje na balíčky služeb pro každý typ služby, která vytváří typ aplikace. Soubory v adresáři balíčku aplikace se zkopírují do úložiště bitové kopie cluster Service Fabric. Balíček aplikace pro typ e-mailové aplikace může například obsahovat odkazy na balíček služby front, front-endové služby balíčku a balíček služba databáze.

**Aplikace s názvem**: Když zkopírujete balíček aplikace do úložiště bitové kopie, vytvoření instance aplikace v rámci clusteru. Když zadáte pomocí jeho názvu nebo verze balíčku aplikace typ aplikace, vytvoření instance. Každá instance typu aplikace je přiřazen název URI identifikátor URI, který vypadá takto: `"fabric:/MyNamedApp"`. V rámci clusteru můžete vytvořit více aplikací s názvem z typu jednu aplikaci. Můžete také vytvořit s názvem aplikace z typů jinou aplikaci. Každé pojmenované aplikace je spravovaná a verzí nezávisle.      

**Typ služby**: název a verzi, která je přiřazená kód balíčky, data balíčky a balíčky konfigurace služby. Typ služby je definována v `ServiceManifest.xml` souboru a vkládat v adresáři balíčku služby. Adresář balíčku služby se pak odkazuje balíček aplikace `ApplicationManifest.xml` souboru. V rámci clusteru po vytvoření pojmenované aplikace, můžete vytvořit s názvem služby z jednoho z typů služeb typ aplikace. Typ služby `ServiceManifest.xml` soubor popisuje službu.

Pro čtení [aplikačního modelu](service-fabric-application-model.md) Další informace najdete v článku.

Existují dva typy služeb:

* **Bezstavové**: použijte bezstavové služby, když je trvalý stav služby uložené ve službě service externího úložiště, jako je například Azure Storage, Azure SQL Database nebo Azure Cosmos DB. Bezstavové služby použijte, pokud služba používá žádné trvalé úložiště. Například pro službu kalkulačky, kde jsou hodnoty předány do služby, se provádí výpočet, který používá tyto hodnoty a potom se vrátí výsledek.
* **Stateful**: použijte stavové služby, pokud má Service Fabric pro správu služby stavu přes jeho spolehlivé kolekce nebo Reliable Actors programovací modely. Když vytvoříte s názvem služby, zadejte kolik oddíly, které chcete pro škálovatelnost rozloženy vašemu stavu. Také určete, jak často k vašemu stavu replikaci mezi uzly, spolehlivost. Každá s názvem služba má jednu primární repliku a více sekundárních replik. Při zápisu na primární replice upravíte s názvem služby stavu. Tento stav Service Fabric poté replikuje do všech sekundárních replikách, aby vašemu stavu synchronizovaná. Service Fabric automaticky rozpozná primární repliky se nezdaří a podporuje stávající sekundární repliky na primární repliku. Service Fabric pak vytvoří nové sekundární repliky.  

**Replik nebo instancí** podívejte se na kód (a stav pro stavové služby), služby, který je nasazen a spuštěna. V tématu [repliky a instance](service-fabric-concepts-replica-lifecycle.md).

**Změna konfigurace** představuje proces všechny změny v sadě replik služby. V tématu [Rekonfigurace](service-fabric-concepts-reconfiguration.md).

**Balíček služby**: adresář disk obsahující typ služby `ServiceManifest.xml` souboru. Tento soubor odkazuje na kód, statických dat a balíčky konfigurace pro typ služby. Soubory v adresáři balíčku služby odkazuje typ aplikace `ApplicationManifest.xml` souboru. Například může označovat kód, statických dat a konfigurace balíčky, které tvoří služba databáze balíčku služby.

**S názvem služby**: Po vytvoření pojmenované aplikace, můžete vytvořit instanci jednoho z jeho typů služeb v rámci clusteru. Typ služby zadáte pomocí jeho název a verzi. Každá instance typu služby je přiřazen název identifikátor URI oboru v identifikátoru URI s názvem aplikace. Například pokud vytvoříte "Databáze" s názvem služby v rámci "MyNamedApp" s názvem aplikace, identifikátor URI bude vypadat takto: `"fabric:/MyNamedApp/MyDatabase"`. V rámci pojmenované aplikace můžete vytvořit několik pojmenované služeb. Každé pojmenované služby může mít svou vlastní schéma oddílu a instance nebo počty repliky.

**Balíček kódu**: adresář disk obsahující typ služby spustitelné soubory, obvykle soubory EXE nebo DLL. Soubory v adresáři balíčku kódu se odkazuje typ služby `ServiceManifest.xml` souboru. Při vytváření pojmenovaného služby balíček kódu zkopíruje do uzlu nebo vybrané spouštět službu s názvem uzly. Potom se spustí kód. Existují dva typy spustitelné soubory balíčku kódu:

* **Spustitelné soubory hosta**: spustitelné soubory, které spustit jako-je na hostitelský operační systém (Windows nebo Linux). Tyto spustitelné soubory nemáte propojit nebo odkazovat všechny soubory modulu runtime Service Fabric a proto nebudete používat žádné Service Fabric programovací modely. Tyto spustitelné soubory nelze použít některé funkce Service Fabric, například službu pojmenování pro koncový bod zjišťování. Spustitelné soubory hosta nemůžete hlásit metriky zatížení, které jsou specifické pro každá instance služby.
* **Služby spustitelné soubory hostitele**: spustitelné soubory, které používají služby Fabric programovací modely propojením se soubory modulu runtime Service Fabric, povolení funkce Service Fabric. Například instanci s názvem služby můžete zaregistrovat koncové body služby DNS Service Fabric a můžete také sestavy metriky zatížení.      

**Data balíčku**: adresář disku, který obsahuje typ služby statické, jen pro čtení datových souborů, obvykle fotografií, zvuk a video soubory. Soubory v adresáři data balíčku se odkazuje typ služby `ServiceManifest.xml` souboru. Při vytváření pojmenovaného služby datový balíček zkopíruje do uzlu nebo vybrané spouštět službu s názvem uzly. Kód spuštění a teď mají přístup k datové soubory.

**Konfigurační balíček**: adresář disku, který obsahuje typ služby statické, jen pro čtení konfigurační soubory, obvykle textových souborů. Soubory v adresáři balíčku konfigurace odkazuje typ služby `ServiceManifest.xml` souboru. Když vytvoříte s názvem služby, soubory v balíčku konfigurace jsou kopírovány jeden nebo více uzlů vybrat spustí službu s názvem. Pak spustí kód spustit a může nyní přístup konfigurační soubory.

**Kontejnery**: ve výchozím nastavení, Service Fabric nasadí a aktivuje služby jako procesy. Service Fabric lze také nasadit services v kontejneru bitové kopie. Kontejnery jsou technologie virtualizace, která Virtualizuje příslušný operační systém z aplikací. Aplikace a její runtime, závislosti a knihovny systému běžet uvnitř kontejneru. Kontejner má úplné, soukromý přístup do kontejneru vlastní izolované zobrazení konstrukce operačního systému. Service Fabric podporuje kontejnery Docker kontejnerům Linux a Windows Server. Další informace najdete v tématu [Service Fabric a kontejnery](service-fabric-containers-overview.md).

**Schéma oddílu**: při vytváření služby s názvem, můžete zadat schéma oddílu. Služby s významné množství stavu rozdělit data do oddílů, které šíří stav mezi uzly clusteru. Rozdělením data napříč oddíly můžete škálovat s názvem služby stavu. V rámci oddílu mají bezstavové služby s názvem instance, zatímco stateful s názvem služby využívají repliky. Obvykle bezstavové služby s názvem mít jen jeden oddíl, protože mají žádný vnitřní stav. Instance oddílu zadejte dostupnosti. Pokud jedna instance nezdaří, ostatní instance i nadále fungovat normálně, a pak Service Fabric vytvoří novou instanci. Stateful s názvem služby udržovat, že jejich stavu v rámci repliky a každý oddíl má svou vlastní repliku nastavíte tak, aby je udržována v synchronizovaném stavu. Má replika selhat, Service Fabric vytvoří novou repliku z existujících replik.

Pro čtení [Service Fabric oddíl spolehlivé služby](service-fabric-concepts-partitioning.md) Další informace najdete v článku.

## <a name="system-services"></a>Systémových služeb
Existují systémových služeb vytvořených v každém clusteru, které poskytují možnosti platformy Service Fabric.

**Zásady vytváření názvů služby**: služby DNS, který se přeloží názvy služby do umístění v clusteru má každý Service Fabric cluster. Správa služby názvy a vlastnosti, jako je Internetu systému DNS (Domain Name) pro cluster. Klienti bezpečné komunikaci s libovolného uzlu v clusteru pomocí služby DNS přeložit název služby a jeho umístění. Přesuňte aplikace v rámci clusteru. To může být například kvůli chybám, vyrovnávání prostředků nebo změna velikosti clusteru. Můžete vyvíjet služeb a klientů, které vyřešit aktuální umístění v síti. Klienti získat skutečný počítače IP adresu a port, kde je aktuálně spuštěna.

Čtení [komunikace službou](service-fabric-connect-and-communicate-with-services.md) Další informace o komunikaci klienta a služby rozhraní API, které pracují s služby DNS.

**Služby úložiště Image Store**: každý Service Fabric cluster má služby úložiště Image Store, kde jsou uchovány balíčky verzí nasazené aplikace. Zkopírujte balíček aplikace do úložiště bitové kopie a poté registrace typu aplikace, které jsou obsažené v tomto balíčku aplikace. Po zřízení typu aplikace vytvoříte aplikaci s názvem z něj. Po odstranění všechny pojmenované aplikace, můžete zrušit registraci typ aplikace ze služby úložiště bitových kopií.

Čtení [pochopit nastavení parametr ImageStoreConnectionString](service-fabric-image-store-connection-string.md) Další informace o službě úložiště bitových kopií.

Pro čtení [nasazení aplikace](service-fabric-deploy-remove-applications.md) článku pro další informace o nasazení aplikace do úložiště bitové kopie služby.

**Služba Správce převzetí služeb při selhání**: má každý Service Fabric cluster převzetí služeb při selhání Manager service, která je zodpovědná za následující akce:
   - Provádí funkce související s vysokou dostupností a konzistence služeb.
   - Orchestruje upgrady aplikací a cluster.
   - Komunikuje s jinými součástmi systému.

**Oprava Manager service**: Toto je volitelné systému služba, která umožňuje provést na cluster, které není bezpečné, akce opravy automatable a transparentní. Opravte manager se používá v:
   - Provádění údržby Azure opraví na [stříbrný a zlatý odolnost](service-fabric-cluster-capacity.md#the-durability-characteristics-of-the-cluster) Azure Service Fabric clusterů.
   - Oprava akcí pro [oprava Orchestration aplikace](service-fabric-patch-orchestration-application.md)

## <a name="built-in-programming-models"></a>Předdefinované programovací modely
Nejsou k dispozici pro můžete vytvářet služby, Service Fabric rozhraní .NET Framework a Java programovací modely:

**Spolehlivé služby**: K rozhraní API k sestavení bezzstavovými i stavovými službami. Stavové služby uložit jejich stavu spolehlivé kolekcí, jako je například slovník nebo fronty. Můžete také připojit v různých balíčcích, komunikace, jako jsou webové rozhraní API a Windows Communication Foundation (WCF).

**Reliable Actors**: K rozhraní API k sestavení bezstavové a stavové objekty prostřednictvím virtuální objektu Actor programovací model. Tento model je užitečné, když máte velké množství nezávislých jednotek výpočtu nebo stavu. Tento model používá na základě zapnout vláken model, takže je vhodné, aby se zabránilo kód, který volá do jiných aktéři nebo služeb, protože jednotlivé objektu actor nemůže zpracovat další příchozí požadavky, až do dokončení všechny odchozí požadavky.

Můžete taky spustit existující aplikace v Service Fabric:

**Kontejnery**: Service Fabric podporuje nasazení kontejnerů Docker na Linux a Windows Server kontejnery v systému Windows Server 2016, společně s podporou pro technologii Hyper-V izolovaném režimu. V Service Fabric [aplikačního modelu](service-fabric-application-model.md), kontejner představuje hostitele aplikace ve více služby, která jsou umístěna repliky. Service Fabric můžete spustit všechny kontejnery a tento scénář je podobná hosta spustitelné scénář, kde balíček existující aplikaci uvnitř kontejneru. Kromě toho můžete [spuštění služeb Service Fabric uvnitř kontejnery](service-fabric-services-inside-containers.md) také.

**Spustitelné soubory hosta**: jakýkoli typ kódu, třeba Node.js, Java nebo C++ můžete spustit v Azure Service Fabric jako služba. Service Fabric odkazuje na těchto typů služeb jako hosta spustitelné soubory, které jsou považovány za bezstavové služby. Výhody pro spuštění spustitelného souboru v clusteru Service Fabric Host zahrnují vysokou dostupnost, monitorování stavu, Správa životního cyklu aplikací, s vysokou hustotou a možnosti rozpoznání.

Pro čtení [zvolte programovací model pro vaši službu](service-fabric-choose-framework.md) Další informace najdete v článku.

<!--Every topic should have next steps and links to the next logical set of content to keep the customer engaged-->
## <a name="next-steps"></a>Další postup
Další informace o Service Fabric:

* [Přehled Service Fabric](service-fabric-overview.md)
* [Proč při sestavování aplikací zvolit přístup založený na mikroslužbách?](service-fabric-overview-microservices.md)
* [Scénáře aplikací](service-fabric-application-scenarios.md)


