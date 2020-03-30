---
title: Přehled programovacího modelu Spolehlivé služby
description: Seznamte se s programovacím modelem service fabric reliable service a můžete začít psát vlastní služby.
author: masnider
ms.topic: conceptual
ms.date: 3/9/2018
ms.author: masnider
ms.custom: sfrev
ms.openlocfilehash: 58259b0d19d68c468779a579bd9c86e77106c18d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "77083516"
---
# <a name="reliable-services-overview"></a>Přehled Reliable Services

Azure Service Fabric zjednodušuje psaní a správu bezstavových a stavových služeb. Toto téma obsahuje:

* Model programování služby spolehlivé služby pro bezstavové a stavové služby.
* Volby, které musíte udělat při psaní spolehlivé služby.
* Některé scénáře a příklady, kdy použít spolehlivé služby a jak jsou zapsány.

*Spolehlivé služby* je jedním z programovacích modelů dostupných na Service Fabric. Dalším je model programování *spolehlivého objektu actor,* který poskytuje rámec aplikace [virtuálního objektu actor](https://research.microsoft.com/en-us/projects/orleans/) nad modelem spolehlivých služeb. Další informace o spolehlivé objekty actor, naleznete [v tématu Úvod do service fabric spolehlivé objekty](service-fabric-reliable-actors-introduction.md).

Service Fabric spravuje životnost služeb, od zřizování a nasazení přes upgrade a odstranění, a to prostřednictvím [správy aplikací Service Fabric](service-fabric-deploy-remove-applications.md).

## <a name="what-are-reliable-services"></a>Jaké jsou spolehlivé služby

Spolehlivé služby vám poskytují jednoduchý, výkonný programovací model nejvyšší úrovně, který vám pomůže vyjádřit, co je pro vaši aplikaci důležité. S programovacím modelem Spolehlivých služeb získáte:

* Přístup k service fabric API. Na rozdíl od služby Service Fabric modelované jako [hostovatelné spustitelné soubory](service-fabric-guest-executables-introduction.md), spolehlivé služby můžete použít service fabric API přímo. To umožňuje služby:
  * Dotaz na systém
  * Sestavy stavu o entitách v clusteru
  * Příjem oznámení o změnách konfigurace a kódu
  * Najděte a komunikujte s dalšími službami,
  * Používejte [spolehlivé kolekce](service-fabric-reliable-services-reliable-collections.md)
  * Získejte přístup k mnoha dalším funkcím, a to vše z prvotřídního programovacího modelu v několika programovacích jazycích.
* Jednoduchý model pro spuštění vlastního kódu, který se cítí jako jiné známé programovací modely. Váš kód má dobře definovaný vstupní bod a snadno spravovat životní cyklus.
* Připojitelný komunikační model. Použijte přenos podle vašeho výběru, například HTTP s [webovým rozhraním API](service-fabric-reliable-services-communication-webapi.md), WebSockets, vlastní protokoly TCP nebo cokoli jiného. Spolehlivé služby poskytují některé skvělé možnosti, které můžete použít, nebo můžete poskytnout své vlastní.
* Pro stavové služby umožňuje programovací model Spolehlivé služby konzistentně a spolehlivě ukládat váš stav přímo do služby pomocí [spolehlivých kolekcí](service-fabric-reliable-services-reliable-collections.md). Spolehlivé kolekce jsou jednoduchá sada vysoce dostupné a spolehlivé třídy kolekce, které budou známé každému, kdo používá kolekce C#. Služby tradičně potřebovaly externí systémy pro spolehlivou správu stavu. Se spolehlivými kolekcemi můžete svůj stav uložit vedle výpočetních prostředků se stejnou vysokou dostupností a spolehlivostí, jakou jste očekávali od vysoce dostupných externích obchodů. Tento model také zlepšuje latenci, protože spoluloučíte výpočetní prostředky a stavu, který potřebuje ke funkci.

## <a name="what-makes-reliable-services-different"></a>Čím se spolehlivé služby liší

Spolehlivé služby se liší od služeb, které jste již dříve napsali, protože Service Fabric poskytuje:

* **Spolehlivost** – Vaše služba zůstane vzhůru i v nespolehlivých prostředích, kde vaše počítače selžou nebo narazí na problémy se sítí, nebo v případech, kdy se samotné služby setkávají s chybami a selháním nebo selháním. U stavových služeb je váš stav zachován i v přítomnosti síťových nebo jiných selhání.
* **Dostupnost** – Vaše služba je dostupná a citlivá. Service Fabric udržuje požadovaný počet spuštěných kopií.
* **Škálovatelnost** – služby jsou odděleny od konkrétního hardwaru a mohou se podle potřeby zvětšovat nebo zmenšovat přidáním nebo odebráním hardwaru nebo jiných prostředků. Služby jsou snadno rozděleny do oddílů (zejména ve stavovém případě), aby bylo zajištěno, že služba může škálovat a zpracovávat částečné chyby. Služby lze vytvářet a mazat dynamicky prostřednictvím kódu, což umožňuje více instancí, které mají být střídá nynější podle potřeby, například v reakci na požadavky zákazníků. A konečně, Service Fabric podporuje služby, které mají být lehké. Service Fabric umožňuje tisíce služeb, které mají být zřízeny v rámci jednoho procesu, spíše než vyžadovat nebo vynaložit celé instance operačního systému nebo procesy na jednu instanci služby.
* **Konzistence** – Veškeré informace uložené ve spolehlivé službě mohou být zaručeny konzistentní. To platí i v rámci více spolehlivé kolekce v rámci služby. Změny mezi kolekcemi v rámci služby lze provést transakčním atomovým způsobem.

## <a name="service-lifecycle"></a>Životní cyklus služby

Ať už je vaše služba stavová nebo bezstavová, spolehlivé služby poskytují jednoduchý životní cyklus, který vám umožní rychle připojit kód a začít.  Zprovoznění nové služby vyžaduje implementaci dvou metod:

* **CreateServiceReplicaListeners/CreateServiceInstanceListeners** - Tato metoda je místo, kde služba definuje komunikační zásobníky, které chce použít. Zásobník komunikace, jako je [například webové rozhraní API](service-fabric-reliable-services-communication-webapi.md), je to, co definuje koncový bod naslouchání nebo koncové body pro službu (jak klienti dosáhnou služby). Také definuje, jak zprávy, které se zobrazí, interagují se zbytkem kódu služby.
* **RunAsync** - Tato metoda je, kde vaše služba spouští svou obchodní logiku a kde by spustit všechny úlohy na pozadí, které by měly být spuštěny po dobu životnosti služby. Zrušení token, který je k dispozici, je signál, kdy by měla být tato práce zastavit. Například pokud služba potřebuje vyžádat zprávy ze spolehlivé fronty a zpracovat je, toto je místo, kde se tato práce stane.

Pokud se poprvé dozvídáte o spolehlivých službách, čtěte dál! Pokud hledáte podrobný návod životního cyklu spolehlivých služeb, podívejte se na [přehled životního cyklu spolehlivých služeb](service-fabric-reliable-services-lifecycle.md).

## <a name="example-services"></a>Ukázkové služby

Podívejme se blíže, jak model spolehlivých služeb pracuje s bezstavové i stavové služby.

### <a name="stateless-reliable-services"></a>Spolehlivé služby bez státní příslušnosti

*Bezstavová služba* je služba, kde neexistuje žádný stav udržován v rámci služby přes volání. Jakýkoli stav, který je k dispozici, je zcela na jedno použití a nevyžaduje synchronizaci, replikaci, trvalost nebo vysokou dostupnost.

Zvažte například kalkulačku, která nemá paměť a přijímá všechny podmínky a operace, které mají být provádět najednou.

V tomto případě `RunAsync()` (C#) `runAsync()` nebo (Java) služby může být prázdný, protože neexistuje žádné zpracování úloh na pozadí, které služba potřebuje provést. Po vytvoření služby kalkulačky `ICommunicationListener` vrátí (C#) nebo `CommunicationListener` (Java) (například webové rozhraní [API),](service-fabric-reliable-services-communication-webapi.md)které otevře koncový bod naslouchání na některém portu. Tento koncový bod naslouchání připojí k různým metodám výpočtu (příklad: "Add(n1, n2)"), které definují veřejné rozhraní API kalkulačky.

Při volání z klienta je vyvolána příslušná metoda a kalkulačka služba provádí operace na zadaných dat a vrátí výsledek. Neukládá žádný stav.

Není ukládání žádné vnitřní stav je tento příklad kalkulačky jednoduché. Ale většina služeb není skutečně bez státní příslušnosti. Místo toho externalizují svůj stav do jiného úložiště. (Například žádná webová aplikace, která spoléhá na udržování stavu relace v záložním úložišti nebo mezipaměti, není bezstavová.)

Běžným příkladem toho, jak bezstavové služby se používají v Service Fabric je jako front-end, který zveřejňuje veřejné rozhraní API pro webové aplikace. Front-endová služba pak přepne na stavové služby k dokončení požadavku uživatele. V tomto případě volání od klientů jsou směrovány do známého portu, například 80, kde služba bez stavové naslouchání. Tato bezstavová služba přijme volání a určí, zda je volání z důvěryhodné strany a pro kterou službu je určena.  Potom bezstavové služby předává volání správné hodstavu služby a čeká na odpověď. Když služba bez stavů obdrží odpověď, odpoví původnímu klientovi. Příkladem takové služby je ukázka *Začínáme s prostředků služby* ([C#](https://github.com/Azure-Samples/service-fabric-dotnet-getting-started) / [Java](https://github.com/Azure-Samples/service-fabric-java-getting-started)), mimo jiné ukázky service fabric v tomto repo.

### <a name="stateful-reliable-services"></a>Spolehlivé služby

*Stavová služba* je služba, která musí mít určitou část stavu konzistentní a přítomná, aby služba fungovala. Zvažte službu, která neustále vypočítává klouzavý průměr některé hodnoty na základě aktualizací, které obdrží. Chcete-li to provést, musí mít aktuální sadu příchozích požadavků, které potřebuje ke zpracování a aktuální průměr. Každá služba, která načítá, zpracovává a ukládá informace v externím úložišti (například objekt blob Azure nebo úložiště tabulek dnes) je stavová. To prostě udržuje svůj stav v externím stavu úložiště.

Většina služeb dnes ukládají svůj stav externě, protože externí úložiště je to, co poskytuje spolehlivost, dostupnost, škálovatelnost a konzistenci pro tento stav. V Service Fabric služby nejsou nutné ukládat jejich stavu externě. Service Fabric se postará o tyto požadavky pro kód služby a stav služby.

Řekněme, že chceme napsat službu, která zpracovává obrázky. Chcete-li to provést, služba přebírá bitovou kopii a řadu převodů, které se mají na tomto obrázku provést. Tato služba vrátí naslouchací proces komunikace (předpokládejme, že `ConvertImage(Image i, IList<Conversion> conversions)`je webapi), který zveřejňuje rozhraní API jako . Když obdrží požadavek, služba jej uloží `IReliableQueue`do aplikace a vrátí klientovi některé ID, aby mohl požadavek sledovat.

V této `RunAsync()` službě, může být složitější. Služba má smyčku `RunAsync()` uvnitř jeho, která `IReliableQueue` vyžádá požadavky z a provádí požadované převody. Výsledky se uloží `IReliableDictionary` tak, aby se klient vrátil, aby mohli získat své převedené obrázky. Chcete-li zajistit, že i v případě, že něco selže image není ztracena, tato spolehlivá služba by vytáhnout z fronty, provést převody a uložit výsledek vše v jedné transakci. V takovém případě je zpráva odebrána z fronty a výsledky jsou uloženy ve slovníku výsledků pouze po dokončení převodů. Alternativně služba může vytáhnout obrázek z fronty a okamžitě uložit do vzdáleného úložiště. To snižuje množství stavu služby musí spravovat, ale zvyšuje složitost, protože služba musí zachovat potřebná metadata pro správu vzdáleného úložiště. S oběma přístup, pokud se něco nezdařilo uprostřed požadavek zůstane ve frontě čeká na zpracování.

Přestože tato služba zní jako typické služby .NET, rozdíl`IReliableQueue` je, že datové struktury používané ( a `IReliableDictionary`) jsou poskytovány Service Fabric a jsou vysoce spolehlivé, dostupné a konzistentní.

## <a name="when-to-use-reliable-services-apis"></a>Kdy použít api služby reliable services

Zvažte spolehlivá lana služeb, pokud:

* Chcete, aby kód vaší služby (a volitelně stav) být vysoce dostupné a spolehlivé.
* Potřebujete transakční záruky napříč více státními jednotkami (například objednávky a řádkové položky objednávky).
* Stav aplikace lze přirozeně modelovat jako spolehlivé slovníky a fronty.
* Kód nebo stav aplikací musí být vysoce dostupné s nízkou latencí čtení a zápisy.
* Vaše aplikace potřebuje řídit souběžnost nebo rozlišovací schopnost transakčních operací v rámci jedné nebo více spolehlivých kolekcí.
* Chcete spravovat komunikaci nebo řídit schéma dělení pro vaši službu.
* Váš kód potřebuje prostředí runtime s volnými vlákny.
* Aplikace musí dynamicky vytvářet nebo zničit spolehlivé slovníky nebo fronty nebo celé služby za běhu.
* Je třeba programově řídit service fabric poskytované funkce zálohování a obnovení pro stav vaší služby.
* Aplikace potřebuje udržovat historii změn pro své jednotky stavu.
* Chcete vyvinout nebo konzumovat vlastní zprostředkovatele vlastního stavu vyvinutých třetími stranami.

## <a name="next-steps"></a>Další kroky

* [Rychlý start spolehlivých služeb](service-fabric-reliable-services-quick-start.md)
* [Spolehlivé kolekce](service-fabric-reliable-services-reliable-collections.md)
* [Programový model Reliable Actors](service-fabric-reliable-actors-introduction.md)
