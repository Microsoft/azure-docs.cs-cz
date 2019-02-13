---
title: Přehled programovacího modelu Service Fabric Reliable Service | Dokumentace Microsoftu
description: Další informace o programovacího modelu Service Fabric Reliable Service a začněte psát vlastní služby.
services: Service-Fabric
documentationcenter: .net
author: masnider
manager: timlt
editor: vturecek; mani-ramaswamy
ms.assetid: 0c88a533-73f8-4ae1-a939-67d17456ac06
ms.service: Service-Fabric
ms.devlang: dotnet
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 3/9/2018
ms.author: masnider
ms.openlocfilehash: bc4e4c9137003dad9cfd96772b7ebe231cd6eace
ms.sourcegitcommit: 301128ea7d883d432720c64238b0d28ebe9aed59
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/13/2019
ms.locfileid: "56171719"
---
# <a name="reliable-services-overview"></a>Přehled Reliable Services
Azure Service Fabric zjednodušuje vytváření a správu bezstavových a stavových Reliable Services. Toto téma obsahuje:

* Programovací model Reliable Services pro bezstavové a stavové služby.
* Volby, které je nutné provést při zápisu spolehlivé služby.
* Některé scénáře a příklady použití modelu Reliable Services a jak jsou zapsány.

Reliable Services je jedním z programovacích modelů dostupných na platformě Service Fabric. Druhá je programovací model Reliable Actors, která poskytuje virtuální programovací model objektu Actor nad rámec modelu Reliable Services. Další informace o tomto programovacím modelu Reliable Actors, naleznete v tématu [Úvod do Service Fabric Reliable Actors](service-fabric-reliable-actors-introduction.md).

Service Fabric spravuje životnost služeb – od zřizování a nasazení díky upgradu a odstranění, prostřednictvím [správy aplikací Service Fabric](service-fabric-deploy-remove-applications.md).

## <a name="what-are-reliable-services"></a>Co jsou Reliable Services?
Reliable Services poskytuje jednoduché, výkonné a nejvyšší úrovně programovací model, který pomáhá lépe vyjádřit, co je důležité pro vaši aplikaci. Programovacího modelu Reliable Services získáte:

* Přístup ke zbytku programovacího rozhraní API pro Service Fabric. Na rozdíl od služeb Service Fabric nemodelují jako [spustitelné soubory hosta](service-fabric-guest-executables-introduction.md), získat přímo pomocí rest API služby Service Fabric Reliable Services. To umožňuje službám:
  * dotaz na systém
  * Stav sestavy o entitách v clusteru
  * přijímat oznámení o změnách konfigurace a kódu
  * Najít a komunikovat s jinými službami
  * (volitelně) použít [Reliable Collections](service-fabric-reliable-services-reliable-collections.md)
  * .. .a pak se jim poskytne přístup k mnoha dalších funkcí z první třídy programovacím modelu v různých programovacích jazyků.
* Jednoduchý model pro spouštění vlastního kódu, který vypadá jako programovací modely, které jste zvyklí. Váš kód je dobře definovaný vstupní bod a snadno spravovatelné životního cyklu.
* Modulární komunikační model. Použít transport podle vašeho výběru, jako je například HTTP s [webového rozhraní API](service-fabric-reliable-services-communication-webapi.md), protokoly Websocket, vlastní protokoly TCP, nebo cokoli jiného. Reliable Services poskytují některé skvělé možnosti out-of-the-box můžete použít, nebo můžete zadat vlastní.
* Programovací model Reliable Services pro stavové služby, umožňuje konzistentně a spolehlivě ukládat svůj stav přímo v službě s použitím [Reliable Collections](service-fabric-reliable-services-reliable-collections.md). Spolehlivé kolekce jsou jednoduché sady vysoce dostupných a spolehlivých tříd kolekcí, které budou znát všichni, kdo použil C# kolekce. Tradičně služby k tomu externími systémy pro správu spolehlivé stavu. S Reliable Collections se dají ukládat svůj stav vedle výpočetní stejnou vysokou dostupnost a spolehlivost, kterou jste si zvykli z externích úložišť s vysokou dostupností. Tento model také zlepšuje latenci, protože jsou společně umístěných výpočetních a stavu, ve kterém musí fungovat.

## <a name="what-makes-reliable-services-different"></a>Co vlastně rozdíl Reliable Services?
Reliable Services v Service Fabric se liší od služby, který může mít napsali před. Service Fabric nabízí spolehlivosti, dostupnosti, konzistence a škálovatelnost.

* **Spolehlivost** – vaše služba zůstane až i v nespolehlivé prostředích, kde své počítače selže nebo stiskněte tlačítko problémy se sítí nebo v případech, kdy vlastních služeb dojde k chybám a chyb nebo selhání. Pro stavové služby je stav svého zachovány i v případě výskytu sítě nebo jiné chyby.
* **Dostupnost** – je vaše služba dostupná a rychlost reakce. Service Fabric spravuje vaše požadovaný počet běžících kopie.
* **Škálovatelnost** – služby jsou oddělené od konkrétní hardware, a může zvětšovat nebo zmenšovat se podle potřeby prostřednictvím přidání nebo odebrání hardwaru nebo jiným prostředkům. Služby jsou snadno rozdělit na oddíly (zejména v případě stavové) k zajištění, že můžete škálovat služby a zpracování částečného selhání. Služby lze vytvořit a odstranit dynamicky prostřednictvím kódu, povolení další instance, aby nahodíme podle potřeby, Řekněme, že v reakci na požadavky zákazníků. A konečně Service Fabric podporuje služby jako jednoduchý. Service Fabric umožňuje tisíce služeb, které se mají zřídit v rámci jednoho procesu, spíše než vyžadující nebo přidělíte celou instancí operačního systému nebo procesy na jednu instanci služby.
* **Konzistence** – jakékoli informace uložené v této službě mohou být zaručena konzistence. To platí dokonce i v několika spolehlivé kolekce v rámci služby. Transakčně atomické způsobem můžete provádět změny mezi kolekcemi v rámci služby.

## <a name="service-lifecycle"></a>Životní cyklus služby
Jestli je vaše služba stavových nebo bezstavových, Reliable Services poskytují jednoduchý životního cyklu, které vám umožní rychle zařadit váš kód a začít pracovat.  Existují jenom jednu nebo dvě metody, které potřebujete k implementaci pro zprovoznění služby.

* **CreateServiceReplicaListeners/CreateServiceInstanceListeners** – tato metoda je, kde služba definuje stack(s) komunikace, která chce použít. Zásobník komunikace, jako například [webového rozhraní API](service-fabric-reliable-services-communication-webapi.md), co definuje koncový bod naslouchací nebo koncové body služby (jak klienti zpřístupnění služby). Definuje také interakci zprávy, které se zobrazují se zbytkem kódu služby.
* **RunAsync** – tato metoda je, kde běží vaše služba jeho obchodní logiku, a kde by aktivovat úlohy na pozadí, kterými by měly běžet po dobu životnosti služby. Token zrušení, který je k dispozici je signál pro kdy by se měla zastavit, které pracují. Například pokud služba potřebuje zpráv z fronty spolehlivé a jejich zpracování, to je, kde se stane, které pracují.

Pokud jste získat informace o modelu reliable services poprvé, přečtěte si o! Pokud hledáte podrobný návod k životní cyklus reliable services, můžete přejít na [v tomto článku](service-fabric-reliable-services-lifecycle.md).

## <a name="example-services"></a>Příklad služby
Znalost Tento programovací model, Pojďme rychlý pohled na dvě různé služby, pokud chcete zobrazit, jak přizpůsobit tyto kusy dohromady.

### <a name="stateless-reliable-services"></a>Bezstavové modelu Reliable Services
Jednoho, kde je Bezstavová služba neexistuje žádný stav uchovávaných v rámci služby v rámci volání. Jakýkoli stav, který je k dispozici je úplně uvolnitelné a nevyžaduje, aby synchronizace, replikace, trvalého nebo vysokou dostupnost.

Představte si třeba kalkulačku, která nemá žádné paměti a přijímá všechny podmínky a operace mají provést najednou.

V takovém případě `RunAsync()` (C#) nebo `runAsync()` (Java) služby nemůže být prázdný, protože neexistuje žádná úloha – zpracování na pozadí, které služba potřebuje pro výkon. Když se vytvoří službu kalkulačky, vrátí `ICommunicationListener` (C#) nebo `CommunicationListener` (Java) (například [webového rozhraní API](service-fabric-reliable-services-communication-webapi.md)), který otevře koncový bod naslouchající na některé portu. Tento koncový bod naslouchací zavěšení na různé výpočetní metody (Příklad: "Přidat (n1, n2)"), které definují rozhraní kalkulačky veřejné rozhraní API.

Když je volána z klienta, je vyvolána vhodná metoda a službu kalkulačky provádí operace na dostupných dat a vrátí výsledek. Jakýkoli stav nejsou uložena.

Tato Kalkulačka příklad neukládejte žádné vnitřní stav zjednodušuje. Ale většina služeb nejsou skutečně bezstavové. Místo toho využívajícím svůj stav na některé další úložiště. (Například jakékoli webové aplikaci, která závisí na uchování stavu relace v záložním úložišti nebo v mezipaměti není bezstavové.)

Běžným příkladem způsobu použití bezstavové služby v Service Fabric je jako front-end, který poskytuje veřejná rozhraní API pro webovou aplikaci. Front-endová služba potom přednášky na stavové služby k dokončení požadavku uživatele. V tomto případě jsou volání od klientů směrované na známé port, třeba 80, kde Bezstavová služba naslouchá. Přijímá volání této bezstavové službě a určuje, zda je volání od důvěryhodné strany a kterou službu je určený pro.  Bezstavová služba potom předává volání správného oddílu stavové služby a čeká na odpověď. Pokud bezstavové služby obdrží odpověď, odpovídá původní klienta. Příkladem takové služby je v naše vzorcích [ C# ](https://github.com/Azure-Samples/service-fabric-dotnet-getting-started)  /  [Java](https://github.com/Azure-Samples/service-fabric-java-getting-started). Toto je pouze jedním z příkladů tohoto modelu v ukázkách, existují i další v jiných vzorcích také.

### <a name="stateful-reliable-services"></a>Stavovém modelu Reliable Services
Stavové služby je ten, který musí mít některých částí pracovního stavu udržovat konzistentní a k dispozici v pořadí pro službu na funkci. Vezměte v úvahu služba, která neustále vypočítá souhrnný průměr některá z hodnot na základě aktualizací, které obdrží. Chcete-li to provést, musí mít aktuální sadu příchozích požadavků, které potřebuje k procesu a aktuální průměr. Jakoukoliv službu, která načte, zpracovává a ukládá informace v externím úložišti (například Azure objektů blob nebo tabulek úložiště dnes) je stavový. Zachová jenom stavu v úložišti stavů externí.

Většina služeb ještě dnes jejich stav ukládat externě, protože externí úložiště je co zajišťující spolehlivost, dostupnost, škálovatelnost a konzistence tento stav. V Service Fabric není nutné služby jejich stav ukládat externě. Service Fabric se postará o tyto požadavky pro kód služby a stav služby.

Řekněme, že chceme napsat služba, která zpracovává obrázky. K tomuto účelu služba přijímá bitovou kopii a řadu převody provést u této image. Tato služba vrátí naslouchací proces komunikace (Řekněme Předpokládejme, že je WebAPI) který zpřístupňuje rozhraní API jako `ConvertImage(Image i, IList<Conversion> conversions)`. Když přijme požadavek, služba ukládá ho `IReliableQueue`a vrátí některé id do klienta, takže ji můžete sledovat žádosti.

V této službě `RunAsync()` by mohl být postup mnohem složitější. Služba obsahuje smyčku v jeho `RunAsync()` , který si vyžádá žádosti z `IReliableQueue` a provádí převody požadavku. Získat výsledky uloženy v `IReliableDictionary` tak, aby když se klient připojí zpět můžou získat jejich převedený bitové kopie. Ujistěte se, že i když něco selže bitovou kopii není ztraceny, toto spolehlivé služby by o přijetí změn z fronty, provádět převody a uloží výsledek v rámci jedné transakce. V takovém případě se odebere zprávu z fronty a výsledky jsou uloženy ve slovníku výsledek, pouze po dokončení převody. Služba může také, vyžádejte si image z fronty a okamžitě ho uložit do vzdáleného úložiště. To snižuje množství stavu, ve kterém má službu pro správu, ale zvyšuje složitost od služby má zajistit potřebné metadat, která chcete spravovat vzdálené úložiště. Díky kterýkoliv přístup Pokud něco selže uprostřed zůstávají požadavek ve frontě čekajících na zpracování.

Poznámka o této službě je, že snaha běžné služby .NET! Jediným rozdílem je, že datových strukturách používá (`IReliableQueue` a `IReliableDictionary`) jsou k dispozici v Service Fabric a jsou vysoce spolehlivé, dostupné a konzistentní vzhledem k aplikacím.

## <a name="when-to-use-reliable-services-apis"></a>Kdy použít rozhraní API služby Reliable Services
Pokud kterýkoli z následujících charakterizují služby potřeby vaší aplikace, měli byste zvážit Reliable Services rozhraní API:

* Má vaše služba kód (a volitelně stavu) pro vysoce dostupné a spolehlivé
* Potřebujete transakční záruky napříč více jednotek stavu (například objednávek a položky řádku pořadí).
* Stav vaší aplikace můžete přirozeně nemodelují jako spolehlivé slovníky a fronty.
* Kód aplikace nebo stav musí být s vysokou dostupností s nízkou latencí operací čtení a zápisu.
* Vaše aplikace potřebuje k řízení souběžnosti nebo členitost počet zrušených zpracovaných operací v jedné nebo více spolehlivých kolekcí.
* Chcete spravovat informace nebo ovládací prvek schéma rozdělení oddílů pro vaši službu.
* Váš kód potřebuje volných vláken běhového prostředí.
* Vaše aplikace potřebuje dynamicky vytvořit nebo odstranit spolehlivé slovníky nebo fronty nebo celou služby za běhu.
* Budete potřebovat programově řídit poskytované Service Fabric zálohování a obnovení funkce pro vaši službu stavu.
* Vaše aplikace potřebuje udržovat historii změn pro jeho jednotky stavu.
* Chcete pro vývoj nebo využívat poskytovatele třetí strany vyvinutých, vlastní stavu.

## <a name="next-steps"></a>Další postup
* [Rychlý start Reliable Services](service-fabric-reliable-services-quick-start.md)
* [Spolehlivé kolekce](service-fabric-reliable-services-reliable-collections.md)
* [Programovací model Reliable Actors](service-fabric-reliable-actors-introduction.md)
