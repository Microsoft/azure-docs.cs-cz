---
title: "Přehled modelu programování Service Fabric spolehlivé Service | Microsoft Docs"
description: "Další informace o programovací model spolehlivá služba Service Fabric a začněte psát vlastní služby."
services: Service-Fabric
documentationcenter: .net
author: masnider
manager: timlt
editor: vturecek; mani-ramaswamy
ms.assetid: 0c88a533-73f8-4ae1-a939-67d17456ac06
ms.service: Service-Fabric
ms.devlang: dotnet
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 3/9/2018
ms.author: masnider;
ms.openlocfilehash: 1cbc116a8d595cfea0e87cd17b556e005f276774
ms.sourcegitcommit: 8aab1aab0135fad24987a311b42a1c25a839e9f3
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/16/2018
---
# <a name="reliable-services-overview"></a>Přehled Reliable Services
Azure Service Fabric zjednodušuje zápis a správu bezstavové a stavové spolehlivé služby. Toto téma obsahuje:

* Spolehlivé služby programovací model pro bezstavové a stavové služby.
* Volby, které je nutné provést při zápisu spolehlivě.
* Některé scénáře a příklady použití spolehlivé služby a jak jsou napsány.

Spolehlivé služby je programovací modely k dispozici v Service Fabric. Druhá je programovací model objektu Actor spolehlivé, která poskytuje virtuální programovací model objektu Actor model spolehlivé služby. Další informace o programovacího modelu Reliable Actors najdete v tématu [Úvod do Service Fabric Reliable Actors](service-fabric-reliable-actors-introduction.md).

Service Fabric spravuje životnost služeb od zřízení a nasazení prostřednictvím aktualizace a odstranění, prostřednictvím [Správa aplikací Service Fabric](service-fabric-deploy-remove-applications.md).

## <a name="what-are-reliable-services"></a>Jaké jsou spolehlivé služby?
Spolehlivé služby poskytuje jednoduché, výkonné a nejvyšší úrovně programovací model můžete express, co je důležité k vaší aplikaci. Spolehlivé služby programovací model získáte:

* Přístup k zbytek Service Fabric programovací rozhraní API. Na rozdíl od služby Service Fabric modelován jako [spustitelné soubory hosta](service-fabric-guest-executables-introduction.md), spolehlivé služby získat zbytek rozhraní API služby prostředků infrastruktury používat přímo. To umožňuje službám:
  * dotaz na systém
  * Sestava stavu o entitách v clusteru
  * přijímání oznámení o změnách konfigurace a kódu
  * Najít a komunikovat s jinými službami
  * (volitelně) použít [spolehlivé kolekce](service-fabric-reliable-services-reliable-collections.md)
  * .. a bude mít přístup k řadu dalších funkcí z první třídy programovací model v několika programovací jazyky.
* Jednoduchý model pro spuštění vlastního kódu, který vypadá jako programovací modely, které se používají k. Váš kód má jasně definované vstupní bod a jednoduše spravované životního cyklu.
* Modulární komunikační model. Používají přenos podle vaší volby, jako je například HTTP s [webového rozhraní API](service-fabric-reliable-services-communication-webapi.md), Websocket, vlastní protokoly TCP, nebo cokoliv jiného. Spolehlivé služby poskytují některé skvělé možnosti se na pole můžete použít, nebo můžete zadat vlastní.
* Spolehlivé služby programovací model pro stavové služby, umožňuje konzistentní a spolehlivě ukládat vašemu stavu právo uvnitř vaší služby pomocí [spolehlivé kolekce](service-fabric-reliable-services-reliable-collections.md). Spolehlivé kolekce jsou jednoduchou sadou třídy vysoce dostupné a spolehlivého kolekce, které budou pro každý, kdo má používá kolekce C#. Tradičně služby pro správu spolehlivé stavu potřeba externími systémy. S spolehlivé kolekce se dají ukládat vašemu stavu vedle výpočetní stejnou vysokou dostupnost a spolehlivost, které jste si zvykli z úložiště s vysokou dostupností externí. Tento model taky zlepšuje latence, protože jsou společné umísťování výpočty a stavu, ve kterém musí fungovat.

Podívejte se na toto video Microsoft Virtual Academy přehled spolehlivé služby: <center>
<a target="_blank" href="https://mva.microsoft.com/en-US/training-courses/building-microservices-applications-on-azure-service-fabric-16747?l=HhD9566yC_4106218965">
<img src="./media/service-fabric-reliable-services-introduction/ReliableServicesVid.png" WIDTH="360" HEIGHT="244" />
</a>
</center>

## <a name="what-makes-reliable-services-different"></a>Díky spolehlivé služby různých?
Spolehlivé služby v Service Fabric se liší od služby, které může jste napsali před. Service Fabric nabízí spolehlivost, dostupnost, konzistence a škálovatelnost.

* **Spolehlivost** – vaše služba zůstane nahoru i v nespolehlivé prostředích, kde vaše počítače selhat nebo stiskněte tlačítko problémů se sítí nebo v případech, kdy dojde k chybám a havárií služby sami, nebo selhání. Pro stavové služby je zachovaná vašemu stavu i v případě sítě nebo jiné chyby.
* **Dostupnost** – vaše služba je dostupná a dobře reagovaly. Service Fabric spravuje vaše požadovaný počet spuštěná kopie.
* **Škálovatelnost** – služby, které jsou odpojené od konkrétní hardware, a můžou růst nebo zmenšení podle potřeby prostřednictvím přidání nebo odebrání hardwaru nebo jiným prostředkům. Služby jsou snadno rozdělena na oddíly (zejména v případě stateful) k zajištění, že můžete škálovat službu a řešit částečné selhání. Služby lze vytvořit a odstranit dynamicky prostřednictvím kódu, povolení více instancí k spuštěné podle potřeby můžete v reakci na požadavky zákazníků. Nakonec Service Fabric podporuje services lightweight. Service Fabric umožňuje tisíce služby, které se má zřídit v jednom procesu, nikoli vyžadují nebo vyhradit celý instancí operačního systému nebo procesy do jediné instance služby.
* **Konzistence** -jakékoli informace uložené v rámci této služby můžete být musí být konzistentní. To platí i napříč více spolehlivé kolekcí v rámci služby. Změny mezi kolekcemi v rámci služby mohou být provedeny transakčně atomic způsobem.

## <a name="service-lifecycle"></a>Životní cyklus služby
Jestli stateful nebo bezstavové služby se poskytují spolehlivé služby jednoduché životního cyklu, které vám umožní rychle zařaďte kódu a začít pracovat.  Existují jen jednu nebo dvě metody, které potřebujete k implementaci pro zprovoznění služby.

* **CreateServiceReplicaListeners/CreateServiceInstanceListeners** -tato metoda je, kde služba definuje stack(s) komunikaci, která chce používat. Komunikace zásobníku, jako například [webového rozhraní API](service-fabric-reliable-services-communication-webapi.md), je co definuje naslouchání koncového bodu nebo koncové body pro službu (jak klienti moci připojit službu). Také definuje způsob, jakým zprávy, které se zobrazují komunikovat s ostatními kódu služby.
* **RunAsync** -tato metoda je, kde vaše služba spustí své obchodní logiky a kde by nové úlohy na pozadí, které měly být spuštěny po dobu jeho existence služby. Token zrušení, který je k dispozici je signál pro Pokud by se měla zastavit svou práci. Například pokud služba potřebuje načítat zprávy z fronty spolehlivé a jejich zpracování, toto je, kde se stane svou práci.

Pokud studujete spolehlivé služby poprvé, přečtěte si na! Pokud hledáte podrobný návod životního cyklu spolehlivé služby, můžete zamiřte na [v tomto článku](service-fabric-reliable-services-lifecycle.md).

## <a name="example-services"></a>Příklad služby
Znalost Tento programovací model, podívejme rychlý přehled dva různé služby, pokud chcete zobrazit, jak tyto součásti zapadají.

### <a name="stateless-reliable-services"></a>Bezstavové spolehlivé služby
Bezstavové služby je jeden, kdy není žádný stav uchovávaných v rámci služby v rámci volání. Žádný stav, který je k dispozici je zcela na jedno použití a nevyžaduje synchronizace replikace, trvalost nebo vysokou dostupnost.

Představte si třeba kalkulačky, která má nedostatek paměti a přijímá všechny podmínky a provést najednou.

V takovém případě `RunAsync()` (C#) nebo `runAsync()` (Java) služby nesmí být prázdné, protože neexistuje žádná úloha zpracování na pozadí kterou služba potřebuje. Když je vytvořen službu kalkulačky, vrátí `ICommunicationListener` (C#) nebo `CommunicationListener` (Java) (například [webového rozhraní API](service-fabric-reliable-services-communication-webapi.md)), otevře se koncový bod naslouchání na některé portu. Tento koncový bod naslouchání zachytí různé výpočetní metody (Příklad: "Přidat (n1, n2)"), definovat Kalkulačka na veřejné rozhraní API.

Při volání z klienta odpovídající metoda je volána a službu kalkulačky provádí operace na dostupných dat a vrátí výsledek. Jakýkoli stav nejsou uložena.

Neukládejte žádný vnitřní stav je tento příklad kalkulačky snadné. Ale většina služeb nejsou skutečně bezstavové. Místo toho využívajícím jejich stavu na některá další úložiště. (Například webové aplikace, které jsou závislé na zachování stavu relace v úložišti zálohování nebo mezipaměti není bezstavové.)

Běžným příkladem jak bezstavové služby se používají v Service Fabric je jako front-end, zpřístupní veřejné rozhraní API pro webovou aplikaci. Front-endová služba potom komunikuje se stavové služby k dokončení požadavku uživatele. V takovém případě jsou směrované volání od klientů známé port, jako třeba 80, kde bezstavové služby naslouchá. Tato služba bezstavové přijme hovor a určuje, jestli je volání z důvěryhodné strany a ji obsluhovat je určené pro.  Bezstavové služby poté předává volání správné oddílu stavové služby a čeká na odpověď. Když bezstavové služby obdrží odpověď, reaguje na původní klienta. Příkladem takové služby je v naše ukázky [C#](https://github.com/Azure-Samples/service-fabric-dotnet-getting-started) / [Java](https://github.com/Azure-Samples/service-fabric-java-getting-started/tree/master/Actors/VisualObjectActor/VisualObjectWebService). Toto je pouze jedním z příkladů tohoto vzoru v ukázkách, existují i další v jiných ukázky také.

### <a name="stateful-reliable-services"></a>Stavová spolehlivé služby
Stavové služby je ten, který musí mít některé část stavu zachovány konzistentní a v pořadí pro službu funkce. Zvažte službu, která neustále vypočítá kumulativní průměr některá z hodnot na základě aktualizací, které obdrží. Chcete-li to provést, musí mít aktuální sadu příchozích požadavků, které potřebuje pro proces a aktuální průměr. Jakoukoli službu, která načte, procesy a ukládá informace v externím obchodu (například Azure blob nebo table úložišti dnes) je stavový. V úložišti externí stavu právě udržuje její stav.

Většina služeb dnes uložit jejich stavu externě, vzhledem k tomu, že externí úložiště je pro tento stav co nabízí spolehlivost, dostupnost, škálovatelnost a konzistence. V Service Fabric nejsou potřeba služby k uložení jejich stavu externě. Service Fabric má na starosti tyto požadavky pro kódu služby a stav služby.

Řekněme, že chceme zápisu služba, která zpracovává bitové kopie. K tomuto účelu službu přebírá bitovou kopii a řadu převody k plnění této bitové kopie. Tato služba vrátí naslouchací proces komunikace (umožňuje Předpokládejme, že je WebAPI), jako například zpřístupňuje rozhraní API `ConvertImage(Image i, IList<Conversion> conversions)`. Pokud obdrží požadavek, služba uloží jej do `IReliableQueue`a vrátí některé id do klienta, takže ho můžete sledovat žádosti.

V rámci této služby `RunAsync()` může být složitější. Služba obsahuje smyčku v jeho `RunAsync()` , vrátí požadavky mimo `IReliableQueue` a provádí převody požadovaný. Získat výsledky uložené v `IReliableDictionary` tak, že když klient zpátky mohou získat jejich převedený bitové kopie. Ujistěte se, že i když se něco nezdaří bitovou kopii není ztraceny, tato spolehlivá služba by pro vyžádání obsahu z fronty, proveďte převody a uložit výsledek vše v rámci jedné transakce. V takovém případě je zpráva odebrána z fronty a výsledky jsou uloženy ve slovníku výsledek jenom v případě, že převody jsou dokončeny. Alternativně může služba bitovou kopii z fronty pro vyžádání obsahu a okamžitě ji uložit do vzdáleného úložiště. Tím se snižuje množství stavu, ve kterém je služba pro správu, ale zvyšuje složitost od služby má zachovat metadata potřebná ke správě vzdáleného úložiště. S buď přístup Pokud se něco nepovedlo uprostřed zůstane požadavek ve frontě čekajících na zpracování.

Poznámka o této službě je, že vypadá podobně jako normální služby rozhraní .NET! Jediným rozdílem je, že data struktury používá (`IReliableQueue` a `IReliableDictionary`) jsou poskytovány Service Fabric a jsou vysoce spolehlivé, k dispozici a konzistentní.

## <a name="when-to-use-reliable-services-apis"></a>Kdy použít rozhraní API spolehlivé služby
Pokud některé z následujících charakterizovat služby potřebám vaší aplikace, měli byste zvážit spolehlivé rozhraní API služby:

* Má vaše služba kód (a volitelně stavu) vysoce dostupné a spolehlivé
* Je nutné transakční záruky přes víc jednotek služby stavu (například objednávek a položek řádku pořadí).
* Stav aplikace můžete přirozeně modelován jako slovník spolehlivé a fronty.
* Kód aplikace nebo stavu musí být vysoce dostupný s nízkou latencí čtení a zápisy.
* Aplikace musí pro řízení souběžnosti nebo členitost zpracovaných operací na jeden nebo více kolekcí spolehlivé.
* Chcete spravovat informace, nebo řídit schéma rozdělení oddílů pro vaši službu.
* Váš kód potřebuje podprocesy běhového prostředí.
* Aplikace musí dynamicky vytvořit nebo odstranit spolehlivé slovník nebo fronty nebo celý služby za běhu.
* Budete muset prostřednictvím kódu programu řízení zadaný Service Fabric zálohování a obnovení funkce pro služby stavu.
* Aplikace musí udržovat historii změn pro jeho jednotky stavu.
* Chcete vyvíjet nebo využívat zprostředkovatelé třetí strany vyvinuté, vlastní stavu.

## <a name="next-steps"></a>Další kroky
* [Spolehlivé služby rychlý start](service-fabric-reliable-services-quick-start.md)
* [Spolehlivé služby advanced využití](service-fabric-reliable-services-advanced-usage.md)
* [Programovacího modelu Reliable Actors](service-fabric-reliable-actors-introduction.md)
