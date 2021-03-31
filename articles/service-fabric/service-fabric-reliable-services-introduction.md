---
title: Přehled programovacího modelu Reliable Service
description: Seznamte se s programovacím modelem spolehlivé služby Service Fabric a začněte psát vlastní služby.
author: masnider
ms.topic: conceptual
ms.date: 3/9/2018
ms.author: masnider
ms.openlocfilehash: 50b8cae11cef5ee8670e2e0270fd8506b1dad4d4
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/29/2021
ms.locfileid: "96576532"
---
# <a name="reliable-services-overview"></a>Přehled Reliable Services

Azure Service Fabric zjednodušuje psaní a správu bezstavových a stavových služeb. Toto téma obsahuje:

* Reliable Services programovací model pro bezstavové a stavové služby.
* Možnosti, které je třeba provést při psaní spolehlivé služby.
* Některé scénáře a příklady použití Reliable Services a způsobu jejich zápisu.

*Reliable Services* je jedním z programovacích modelů dostupných v Service Fabric. Dalším je programovací model *Reliable actor* , který poskytuje aplikační rozhraní [Virtual actor](https://research.microsoft.com/en-us/projects/orleans/) nad Reliable Servicesm modelem. Další informace o Reliable Actors najdete v tématu [Úvod do Service Fabric Reliable Actors](service-fabric-reliable-actors-introduction.md).

Service Fabric spravuje dobu života služeb, od zřizování a nasazení prostřednictvím aktualizace a odstranění prostřednictvím [Service Fabric správy aplikací](service-fabric-deploy-remove-applications.md).

## <a name="what-are-reliable-services"></a>Co jsou Reliable Services

Reliable Services poskytuje jednoduchý, výkonný model programování na nejvyšší úrovni, který vám umožní vyjádřit, co je pro vaši aplikaci důležité. Pomocí programovacího modelu Reliable Services získáte:

* Přístup k rozhraním API Service Fabric Na rozdíl od Service Fabric Services modelu jako [spustitelných souborů hosta](service-fabric-guest-executables-introduction.md)Reliable Services může rozhraní API Service Fabric používat přímo. Díky tomu můžou služby:
  * Dotaz na systém
  * Hlášení stavu entit v clusteru
  * Přijímání oznámení o změně konfigurace a kódu
  * Hledání a komunikace s dalšími službami,
  * Používejte [spolehlivé kolekce](service-fabric-reliable-services-reliable-collections.md)
  * Přístup k mnoha dalším funkcím, a to vše z první třídy programovacího modelu v několika programovacích jazycích.
* Jednoduchý model pro spuštění vlastního kódu, který je podobný jiným známým programovacím modelům. Váš kód má dobře definovaný vstupní bod a snadno spravovaný životní cyklus.
* Připojitelný komunikační model. Použijte přenos dle vašeho výběru, například HTTP s [webovým rozhraním API](./service-fabric-reliable-services-communication-aspnetcore.md), WebSockets, vlastní protokoly TCP nebo cokoli jiného. Reliable Services poskytují skvělé předem připravené možnosti, které můžete použít, nebo můžete zadat vlastní.
* Pro stavové služby Reliable Services programovací model umožňuje konzistentně a spolehlivě ukládat stav přímo do vaší služby pomocí [spolehlivých kolekcí](service-fabric-reliable-services-reliable-collections.md). Spolehlivé kolekce jsou jednoduché sady vysoce dostupných a spolehlivých tříd kolekcí, které budou obeznámeny s kýmkoli, kdo používal kolekce jazyka C#. Služby, které jsou potřeba pro správu spolehlivých stavů, tradičně vyžadují služby. Pomocí Reliable Collections můžete uložit svůj stav vedle vašich výpočtů se stejnou vysokou dostupností a spolehlivostí, kterou jste měli očekávat od vysoce dostupných externích obchodů. Tento model také zlepšuje latenci, protože společně vyhledáváte výpočetní výkon a stav, který potřebuje k fungování.

## <a name="what-makes-reliable-services-different"></a>Co dělá Reliable Services odlišnou

Reliable Services se liší od služeb, které jste mohli předtím napsat, protože Service Fabric poskytuje:

* **Spolehlivost** – služba se zachová i v nespolehlivých prostředích, kde vaše počítače selžou nebo narazí na problémy se sítí, nebo v případech, kdy dojde k chybám a selháním nebo selháním služeb. U stavových služeb se váš stav zachovává i v případě, že dojde k síti nebo jiným selháním.
* **Dostupnost** – vaše služba je dosažitelná a reaguje. Service Fabric udržuje požadovaný počet spuštěných kopií.
* **Škálovatelnost** – služby jsou odděleny od konkrétního hardwaru a můžou se podle potřeby zvětšovat nebo zmenšovat prostřednictvím přidávání nebo odebírání hardwaru nebo jiných prostředků. Služby je možné snadno rozdělit (zejména ve stavovém případě) a zajistit tak, aby služba mohla škálovat a zpracovávat částečné chyby. Služby je možné vytvořit a odstranit dynamicky prostřednictvím kódu, což umožňuje, aby se v případě potřeby další instance v reakci na žádosti zákazníků vyžádala. Nakonec Service Fabric doporučuje, aby byly služby odlehčené. Service Fabric umožňuje zřídit tisíce služeb v rámci jednoho procesu, nikoli vyžadovat nebo vyhradit celé instance nebo procesy operačního systému na jednu instanci služby.
* **Konzistence** – veškeré informace, které jsou uložené ve spolehlivé službě, si můžou být zaručené jako konzistentní. To platí i v případě více spolehlivých kolekcí v rámci služby. Změny napříč kolekcemi v rámci služby je možné provádět v důsledku neúčinných atomických úkonů.

## <a name="service-lifecycle"></a>Životní cyklus služby

Bez ohledu na to, jestli je vaše služba stavová nebo Bezstavová, Reliable Services poskytovat jednoduchý životní cyklus, který vám umožní rychle připojit váš kód a začít.  Zavedení a zprovoznění nové služby vyžaduje, abyste implementovali dvě metody:

* **CreateServiceReplicaListeners/CreateServiceInstanceListeners** – Tato metoda je tam, kde služba definuje komunikační zásobníky, které chce použít. Komunikační zásobník, jako je například [webové rozhraní API](./service-fabric-reliable-services-communication-aspnetcore.md), je definice bodu naslouchání nebo koncových bodů pro službu (jak klienti dosáhnou služby). Definuje také způsob, jakým budou zprávy zobrazeny v interakci se zbytkem kódu služby.
* **RunAsync** – Tato metoda je tam, kde vaše služba spouští svoji obchodní logiku a kde by mohla aktivovat všechny úlohy na pozadí, které by se měly spustit po dobu životnosti služby. Poskytnutý token zrušení je signálem, kdy se má tato práce zastavit. Například pokud služba potřebuje vyžádat zprávy ze spolehlivé fronty a zpracovat je, jedná se o to, kde k této práci dojde.

Pokud se o spolehlivých službách naučíte poprvé, přečtěte si téma. Pokud hledáte podrobný návod k životnímu cyklu spolehlivých služeb, podívejte se na [Přehled životního cyklu Reliable Services](service-fabric-reliable-services-lifecycle.md).

## <a name="example-services"></a>Ukázkové služby

Pojďme se podívat, jak model Reliable Services pracuje s bezstavovou a stavovou službou.

### <a name="stateless-reliable-services"></a>Bezstavová Reliable Services

*Bezstavová služba* je jedna, kde ve službě není v rámci volání udržován žádný stav. Jakýkoli přítomný stav je zcela jednorázově a nevyžaduje synchronizaci, replikaci, trvalost nebo vysokou dostupnost.

Představte si například kalkulačku, která nemá žádnou paměť a přijímá všechny kroky a operace, které je potřeba provést najednou.

V takovém případě `RunAsync()` může být (C#) nebo `runAsync()` (Java) služby prázdný, protože není k dispozici žádné zpracování úloh na pozadí, které služba potřebuje. Při vytvoření služby kalkulačky vrátí `ICommunicationListener` (C#) nebo `CommunicationListener` (Java) (například [webové rozhraní API](./service-fabric-reliable-services-communication-aspnetcore.md)), které otevře koncový bod naslouchání na některém portu. Toto naslouchání koncových bodů se zapojováním k různým metodám výpočtu (příklad: Add (N1, N2)), který definuje veřejné rozhraní API kalkulačky.

Při volání z klienta je vyvolána vhodná metoda a služba kalkulačky provede operace s poskytnutými daty a vrátí výsledek. Neukládá žádný stav.

Tento příklad kalkulačky usnadňuje neuložení žádného vnitřního stavu. Ale většina služeb není skutečně Bezstavová. Místo toho Externalize svůj stav na jiný obchod. (Například libovolná webová aplikace, která spoléhá na zachování stavu relace v záložním úložišti nebo v mezipaměti, není Bezstavová.)

Běžným příkladem toho, jak se v Service Fabric používají bezstavové služby, je jako front-end, který zpřístupňuje veřejné rozhraní API pro webovou aplikaci. Front-end služba pak mluví se stavovým službám, aby dokončila požadavek uživatele. V takovém případě jsou volání klientů směrována na známý port, například 80, kde naslouchá Bezstavová služba. Tato Bezstavová služba přijme volání a určí, zda je volání od důvěryhodné strany a služby, pro kterou je určena.  Bezstavová služba pak předá volání do správného oddílu stavové služby a počká na odpověď. Když Bezstavová služba obdrží odpověď, odpoví na původního klienta. Příkladem takové služby je ukázka *Service Fabric Začínáme* ([C#](https://github.com/Azure-Samples/service-fabric-dotnet-getting-started)  /  [Java](https://github.com/Azure-Samples/service-fabric-java-getting-started)), kromě dalších ukázek Service Fabric v tomto úložišti.

### <a name="stateful-reliable-services"></a>Stavová Reliable Services

*Stavová služba* je ta, která musí mít určitou část stavu konzistentní a přítomná, aby mohla služba fungovat. Vezměte v úvahu službu, která průběžně vypočítá Kumulovaný průměr určité hodnoty na základě aktualizací, které obdrží. Aby to bylo možné, musí mít aktuální sadu příchozích požadavků, které musí zpracovat, a aktuální průměr. Stav jakékoli služby, která načítá, zpracovává a ukládá informace v externím úložišti (například Azure Blob nebo Table Store Today). Pouze udržuje svůj stav v úložišti externího stavu.

Většina služeb dnes ukládá svůj stav externě, protože externí obchod zajišťuje spolehlivost, dostupnost, škálovatelnost a konzistenci pro daný stav. V Service Fabric služby nejsou nutné k externímu ukládání stavu. Service Fabric postará o tyto požadavky jak pro kód služby, tak pro stav služby.

Řekněme, že chceme napsat službu, která zpracovává obrázky. K tomu je potřeba, aby služba prováděla image a řadu převodů, které se v této imagi mají provést. Tato služba vrátí naslouchací proces komunikace (řekněme, že se jedná o WebAPI), který zpřístupňuje rozhraní API jako `ConvertImage(Image i, IList<Conversion> conversions)` . Když obdrží požadavek, služba ho uloží do a `IReliableQueue` vrátí nějaké ID klientovi, aby mohl žádost sledovat.

V této službě `RunAsync()` může být složitější. Služba obsahuje smyčku uvnitř své `RunAsync()` žádosti, která vyžádá `IReliableQueue` a provede požadované převody. Výsledky se ukládají do `IReliableDictionary` , takže když se klient vrátí zpátky, může získat převedené obrázky. Aby se zajistilo, že i v případě, že se něco nepovede, nebude image ztracena, Tato spolehlivá služba by se vyčerpala z fronty, prováděla převody a uloží výsledek do jediné transakce. V tomto případě je zpráva odebrána z fronty a výsledky jsou uloženy ve slovníku výsledků pouze v případě, že byly převody dokončeny. Alternativně může služba získat image z fronty a hned ji uložit do vzdáleného úložiště. Tím se snižuje množství stavu, který musí služba spravovat, ale zvyšuje složitost, protože služba musí uchovávat potřebná metadata pro správu vzdáleného úložiště. V případě, že se některý z těchto postupů nezdařil, požadavek zůstane ve frontě, která čeká na zpracování.

I když tato služba funguje jako typická služba .NET, je rozdílem, že používané datové struktury ( `IReliableQueue` a `IReliableDictionary` ) poskytují Service Fabric a jsou vysoce spolehlivé, dostupné a konzistentní.

## <a name="when-to-use-reliable-services-apis"></a>Kdy použít rozhraní API pro Reliable Services

Zvažte Reliable Services rozhraní API, pokud:

* Chcete, aby byl kód služby (a volitelně také stav) vysoce dostupný a spolehlivý.
* Je potřeba, aby transakční záruky byly v rámci více jednotek stavu (například objednávky a položky řádku objednávky).
* Stav vaší aplikace může být přirozeně modelován jako spolehlivé slovníky a fronty.
* Kód nebo stav vaší aplikace musí být vysoce dostupný s nízkou latencí čtení a zápisy.
* Vaše aplikace musí řídit souběžnost nebo členitost transakčních operací v rámci jedné nebo více spolehlivých kolekcí.
* Chcete spravovat komunikaci nebo řídit schéma dělení pro vaši službu.
* Váš kód potřebuje běhové prostředí s volnými vlákny.
* Vaše aplikace potřebuje dynamicky vytvářet nebo zničit spolehlivé slovníky nebo fronty nebo celé služby za běhu.
* Pro stav služby je nutné programově řídit Service Fabric funkcí zálohování a obnovení poskytovaných službou.
* Vaše aplikace musí udržovat historii změn pro své jednotky stavu.
* Chcete vyvíjet nebo využívat vlastní poskytovatele stavů vyvinutých třetími stranami.

## <a name="next-steps"></a>Další kroky

* [Rychlý Start Reliable Services](service-fabric-reliable-services-quick-start.md)
* [Spolehlivé kolekce](service-fabric-reliable-services-reliable-collections.md)
* [Reliable Actors programovací model](service-fabric-reliable-actors-introduction.md)
