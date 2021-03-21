---
title: Přehled Service Fabric Reliable Actors
description: Seznámení s programovacím modelem Service Fabric Reliable Actors na základě vzoru virtuálního objektu actor.
ms.topic: conceptual
ms.date: 11/01/2017
ms.custom: devx-track-csharp
ms.openlocfilehash: 1a8a7003a69deaf6b74d6fbb8a3cf84b0a78eecf
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2021
ms.locfileid: "96576379"
---
# <a name="introduction-to-service-fabric-reliable-actors"></a>Úvod do Service Fabric Reliable Actors
Reliable Actors je Service Fabric aplikační architektury založené na vzoru [virtuálního objektu actor](https://research.microsoft.com/en-us/projects/orleans/) . Rozhraní Reliable Actors API nabízí model programování s jedním vláknem sestavený na základě škálovatelnosti a záruk spolehlivosti poskytovaných Service Fabric.

## <a name="what-are-actors"></a>Co jsou objekty actor?
Objekt actor je izolovaná, nezávislá jednotka výpočtů a stav s jedním vláknovým prováděním. [Vzor objektu actor](https://en.wikipedia.org/wiki/Actor_model) je výpočetní model pro souběžné nebo distribuované systémy, ve kterých může být velký počet těchto aktérů spouštěn současně a nezávisle na sobě. Objekty actor můžou vzájemně komunikovat a můžou vytvářet další objekty actor.

### <a name="when-to-use-reliable-actors"></a>Kdy použít Reliable Actors
Service Fabric Reliable Actors je implementace vzoru návrhu objektu actor. Stejně jako u jakéhokoli vzoru návrhu softwaru se rozhodnutí, jestli se má použít konkrétní vzor, provede na základě toho, jestli problém s návrhem softwaru vyhovuje nebo není vzorem.

I když vzor návrhu objektu actor může být vhodný pro řadu problémů a scénářů distribuovaných systémů, je nutné provést pečlivou pozornost omezení vzoru a implementaci rozhraní .NET Framework. Jako obecné doprovodné materiály zvažte vzor objektu actor, ve kterém můžete modelovat problém nebo scénář, pokud:

* Vaše místo na problému zahrnuje velký počet (tisíců nebo více) malých, nezávislých a izolovaných jednotek stavu a logiky.
* Chcete pracovat s objekty s jedním vláknem, které nevyžadují významnou interakci z externích komponent, včetně stavu dotazování napříč sadou objektů actor.
* Instance objektu actor nebudou blokovat volajícím s nepředvídatelnými zpožděními vydáváním vstupně-výstupních operací.

## <a name="actors-in-service-fabric"></a>Objekty actor v Service Fabric
V Service Fabric jsou objekty actor implementovány v rozhraní Reliable Actors Framework: rozhraní aplikace založené na vzorcích actor postavené nad [Service Fabric Reliable Services](service-fabric-reliable-services-introduction.md). Každá služba Reliable Actors, kterou píšete, je vlastně dělená stavová spolehlivá služba.

Každý objekt actor je definován jako instance typu objektu actor, která je shodná se způsobem, jakým objekt .NET je instancí typu .NET. Může existovat například typ objektu actor, který implementuje funkce kalkulačky a může obsahovat mnoho aktérů tohoto typu, které jsou distribuovány na různých uzlech v rámci clusteru. Každý takový objekt actor je jednoznačně identifikován IDENTIFIKÁTORem objektu actor.

## <a name="actor-lifetime"></a>Doba života objektu actor
Service Fabric Actors jsou virtuální, což znamená, že jejich životnost není vázaná na jejich reprezentaci v paměti. V důsledku toho není nutné explicitně vytvářet ani zničeny. Modul runtime Reliable Actors automaticky aktivuje objekt actor při prvním přijetí žádosti pro toto ID objektu actor. Pokud se objekt actor nepoužívá v časovém intervalu, Reliable Actors uvolňování paměti za běhu – shromáždí objekt v paměti. Bude taky zajišťovat znalosti o existenci objektu actor, pokud ho budete muset znovu aktivovat později. Další podrobnosti najdete v tématu [životní cyklus objektu actor a uvolňování paměti](service-fabric-reliable-actors-lifecycle.md).

Tato abstrakce životnosti virtuálních objektů actor přináší některá upozornění v důsledku modelu virtuálního objektu actor a ve skutečnosti Reliable Actors odchylka od tohoto modelu od času.

* Objekt actor je automaticky aktivován (což způsobí vytvoření objektu actor) při prvním odeslání zprávy na jeho ID objektu actor. Po určité době je objekt objektu actor uvolněn z paměti. V budoucnu se znovu používá ID objektu actor, což způsobí, že bude vytvořen nový objekt actor. Stav objektu actor je živý životnost objektu, pokud je uložen ve Správci stavu.
* Volání jakékoli metody actor pro ID objektu actor aktivuje tohoto objektu actor. Z tohoto důvodu typy objektů actor mají svůj konstruktor implicitně volaný modulem runtime. Proto klientský kód nemůže předat parametry konstruktoru typu objektu actor, ačkoliv parametry mohou být předány konstruktoru objektu actor samotným službou. Výsledkem je, že mohou být aktéry konstruovány v částečně inicializovaném stavu podle času, kdy jsou volány jiné metody, pokud objekt actor vyžaduje parametry inicializace z klienta. Pro aktivaci objektu actor z klienta není k dispozici žádný jediný vstupní bod.
* I když Reliable Actors implicitně vytvářet objekty objektu actor; je možné explicitně odstranit objekt actor a jeho stav.

## <a name="distribution-and-failover"></a>Distribuce a převzetí služeb při selhání
V rámci zajištění škálovatelnosti a spolehlivosti Service Fabric distribuuje objekty Actors v celém clusteru a automaticky je migruje z neúspěšných uzlů do stavu v pořádku, jak je potřeba. Toto je abstrakce v [dělené stavové spolehlivé službě](service-fabric-concepts-partitioning.md). Funkce distribuce, škálovatelnosti, spolehlivosti a automatického převzetí služeb při selhání je zajištěná na základě skutečnosti, že se aktéri spouštějí v rámci stavové spolehlivé služby nazývané *Služba actor*.

Objekty actor jsou distribuovány napříč oddíly služby objektu actor a tyto oddíly jsou distribuovány napříč uzly v clusteru Service Fabric. Každý oddíl služby obsahuje sadu aktérů. Service Fabric spravuje distribuci a převzetí služeb při selhání oddílů služby.

Například služba objektu actor s devíti oddíly nasazenými do tří uzlů, které používají výchozí umístění oddílu objektu actor, by mohla být distribuována takto:

![Distribuce Reliable Actors][2]

Rozhraní objektu actor spravuje schéma oddílu a nastavení rozsahu klíčů pro vás. Tato možnost zjednodušuje některé možnosti, ale také přináší nějaké aspekty:

* Reliable Services umožňuje zvolit schéma dělení, rozsah klíčů (při použití schématu dělení rozsahu) a počet oddílů. Reliable Actors se omezuje na schéma dělení na oddíly (jednotné schéma Int64) a vyžaduje, abyste používali plný rozsah klíčů Int64.
* Ve výchozím nastavení jsou objekty actor náhodně umístěny do oddílů, které mají za následek jednotnou distribuci.
* Vzhledem k tomu, že jsou objekty actor náhodně umístěny, je vhodné očekávat, že operace objektu actor budou vyžadovat síťovou komunikaci, včetně serializace a deserializace dat volání metody, což snižuje latenci a režii.
* V pokročilých scénářích je možné řídit umístění oddílu objektu actor pomocí ID objektu actor typu Int64, které se mapují na konkrétní oddíly. Nicméně to může vést k nevyvážené distribuci aktérů napříč oddíly.

Další informace o tom, jak jsou rozdělené služby objektu actor, najdete v tématu [Koncepty dělení pro objekty](service-fabric-reliable-actors-platform.md#service-fabric-partition-concepts-for-actors)actor.

## <a name="actor-communication"></a>Komunikace objektu actor
Interakce objektu actor jsou definovány v rozhraní, které je sdíleno objektem Actor, který implementuje rozhraní, a klientem, který získává proxy do objektu actor přes stejné rozhraní. Vzhledem k tomu, že toto rozhraní se používá k asynchronnímu vyvolání metod objektu actor, musí každá metoda v rozhraní vracet úlohy.

Volání metod a jejich odpovědi nakonec mají za následek síťové požadavky v rámci clusteru, takže argumenty a typy výsledků úloh, které vrací, musí být serializovatelné platformou. Konkrétně musí být pro [kontrakt dat serializovatelný](service-fabric-reliable-actors-notes-on-actor-type-serialization.md).

### <a name="the-actor-proxy"></a>Proxy objekt actor
Rozhraní API klienta Reliable Actors zajišťuje komunikaci mezi instancí objektu actor a klientem objektu actor. Aby mohl klient komunikovat s objektem Actor, vytvoří objekt proxy objektu actor, který implementuje rozhraní objektu actor. Klient komunikuje s objektem actor vyvoláním metod objektu proxy. Proxy objekt actor se dá použít pro komunikaci mezi klientem a actor a actor-to-actor.

```csharp
// Create a randomly distributed actor ID
ActorId actorId = ActorId.CreateRandom();

// This only creates a proxy object, it does not activate an actor or invoke any methods yet.
IMyActor myActor = ActorProxy.Create<IMyActor>(actorId, new Uri("fabric:/MyApp/MyActorService"));

// This will invoke a method on the actor. If an actor with the given ID does not exist, it will be activated by this method call.
await myActor.DoWorkAsync();
```

```java
// Create actor ID with some name
ActorId actorId = new ActorId("Actor1");

// This only creates a proxy object, it does not activate an actor or invoke any methods yet.
MyActor myActor = ActorProxyBase.create(actorId, new URI("fabric:/MyApp/MyActorService"), MyActor.class);

// This will invoke a method on the actor. If an actor with the given ID does not exist, it will be activated by this method call.
myActor.DoWorkAsync().get();
```


Všimněte si, že dvě části informací, které slouží k vytvoření objektu proxy objektu actor, jsou ID objektu actor a název aplikace. ID objektu actor jednoznačně identifikuje objekt actor, zatímco název aplikace identifikuje [Service Fabric aplikaci](service-fabric-reliable-actors-platform.md#application-model) , kde je objekt actor nasazen.

`ActorProxy`Třída (C#)/ `ActorProxyBase` (Java) na straně klienta provádí nezbytné řešení k vyhledání objektu actor podle ID a k otevření komunikačního kanálu s ním. Také se znovu pokusí vyhledat objekt actor v případech selhání komunikace a převzetí služeb při selhání. V důsledku toho doručení zprávy obsahuje následující vlastnosti:

* Doručování zpráv je nejlepší úsilí.
* Objekty actor mohou obdržet duplicitní zprávy ze stejného klienta.

## <a name="concurrency"></a>Souběžnost
Modul runtime Reliable Actors poskytuje jednoduchý model přístupu založený na zapnutí pro přístup k metodám objektu actor. To znamená, že v kódu objektu actor může být kdykoli aktivní maximálně jeden podproces. Přístup podle potřeby výrazně zjednodušuje souběžné systémy, protože pro přístup k datům nepotřebujete mechanismy synchronizace. Také to znamená, že systémy musí být navržené se speciálními požadavky pro přístup k jednotlivým instancím objektů actor s jedním vláknem.

* Jedna instance objektu actor nemůže zpracovat více než jeden požadavek současně. Instance objektu actor může způsobit kritický problém propustnosti, pokud se očekává, že se budou zpracovávat souběžné požadavky.
* Actory se můžou vzájemně zablokovat, pokud existuje cyklická požadavek mezi dvěma aktéry, zatímco externí požadavek se provedl jednomu z objektů actor současně. Modul runtime objektu actor automaticky vyprší časový limit u volání actor a vyvolá výjimku volajícímu, aby přerušil možné výpadky při zablokování.

![Reliable Actors komunikace][3]

### <a name="turn-based-access"></a>Přístup na základě zapínání
V reakci na požadavek od jiných aktérů nebo klientů nebo z úplného provedení zpětného volání [časovače nebo připomenutí](service-fabric-reliable-actors-timers-reminders.md) se vytvoří sestává z kompletního provedení metody actor. I když jsou tyto metody a zpětná volání asynchronní, modul runtime actor je prokládá. Aby bylo možné zapnout nové zapnutí, musí být zapínání úplně dokončeno. Jinými slovy, metoda objektu actor nebo zpětné volání časovače nebo připomenutí, které je aktuálně prováděno, musí být kompletně dokončeno před tím, než je povoleno nové volání metody nebo zpětného volání. Metoda nebo zpětné volání je považováno za dokončené, pokud se provedení vrátilo z metody nebo zpětného volání a úloha vrácená metodou nebo zpětným voláním byla dokončena. Je potřeba zdůraznit, že souběžnost, která je založená na funkci, je zachována i napříč různými metodami, časovači a zpětnými voláními.

Modul runtime Actors vynutil souběžnost založenou na zapínání zámku na začátku zapnutí a uvolnění zámku na konci. Proto je souběžnost založené na založení na jednotlivých objektech actor a ne přes objekty Actors. Metody actor a zpětná volání časovače nebo připomenutí mohou být spouštěna současně jménem různých objektů actor.

Následující příklad ilustruje výše uvedené koncepty. Vezměte v úvahu typ objektu actor, který implementuje dvě asynchronní metody (například *– metoda1* a *Method2*), časovač a připomenutí. Následující diagram ukazuje příklad časové osy pro spuštění těchto metod a zpětná volání jménem dvou objektů actor (*ActorId1* a *ActorId2*), které patří do tohoto typu objektu actor.

![Reliable Actors souběžnost a přístup pomocí modulu runtime][1]

Tento diagram dodržuje tyto konvence:

* Každá svislá čára ukazuje logický tok provádění metody nebo zpětného volání jménem konkrétního objektu actor.
* Události označené na jednotlivých svislých řádcích se vyskytují v chronologickém pořadí s novějšími událostmi, ke kterým dochází pod staršími.
* Pro časové osy, které odpovídají různým aktérům, se používají různé barvy.
* Zvýrazňování se používá k označení doby trvání, za kterou se zámek vázaný na objekt actor koná jménem metody nebo zpětného volání.

Mezi důležité body, které je potřeba vzít v úvahu:

* Zatímco *– metoda1* je prováděna jménem *ActorId2* v reakci na požadavek klienta *xyz789*, dorazí jiný požadavek na klienta (*abc123*), který také vyžaduje, aby *– metoda1* spustil *ActorId2.* Nicméně druhé spuštění *– metoda1* se nezačne, dokud se nedokončí předchozí spuštění. Podobně je připomenutí zaregistrované v *ActorId2* aktivována během provádění *– metoda1* v reakci na žádost klienta *xyz789*. Zpětné volání připomenutí je provedeno až po dokončení obou spuštění *– metoda1* . To vše je způsobeno tím, že se pro *ActorId2* vynutila souběžnost založená na funkci.
* Podobně je souběžnost založená na zapínání také pro *ActorId1*, jak je znázorněno prováděním *– metoda1*, *Method2* a zpětného volání časovače jménem *ActorId1* probíhají v sériové podobě.
* Provádění *– metoda1* jménem *ActorId1* se překrývá s jeho prováděním jménem *ActorId2*. Důvodem je to, že souběžnost založená na funkci je vynutila pouze v rámci objektu actor a nikoli přes objekty Actors.
* V některých způsobech volání metody nebo zpětného volání se `Task` metoda (C#)/ `CompletableFuture` (Java) vrácená metodou nebo zpětným voláním dokončí po návratu metody. V některých dalších se asynchronní operace již dokončila v době, kdy se metoda nebo zpětné volání vrátí. V obou případech je zámek za objekt actor uvolněn pouze poté, co metoda nebo zpětné volání vrátí metodu a je dokončena asynchronní operace.

### <a name="reentrancy"></a>Vícenásobný přístup
Modul runtime Actors ve výchozím nastavení povoluje Vícenásobný přístup. To znamená, že pokud metoda actor objektu *actor a* volá metodu na *objekt actor B*, který volá jinou metodu objektu *actor a*, tato metoda může být spuštěna. Důvodem je to, že je součástí stejného logického kontextu řetězce volání. Všechna volání časovače a připomenutí začínají novým kontextem logického volání. Další podrobnosti najdete v [Reliable Actors Vícenásobný přístup](service-fabric-reliable-actors-reentrancy.md) .

### <a name="scope-of-concurrency-guarantees"></a>Rozsah záruk souběžnosti
Modul runtime Actors poskytuje tyto záruky souběžnosti v situacích, kdy řídí vyvolání těchto metod. Například poskytuje tyto záruky pro vyvolání metod, které jsou provedeny v reakci na požadavek klienta, a také pro zpětná volání časovače a připomenutí. Nicméně pokud kód objektu actor přímo vyvolá tyto metody mimo mechanismy poskytované modulem runtime Actors, pak modul runtime nemůže poskytnout žádné záruky souběžnosti. Například pokud je metoda vyvolána v kontextu nějaké úlohy, která není přidružena k úloze vrácené metodami objektu actor, nemůže modul runtime poskytnout záruky souběžnosti. Pokud je metoda vyvolána z vlákna, které objekt actor vytvoří sami, modul runtime také nemůže poskytnout záruky souběžnosti. Proto by měly objekty actor provádět operace na pozadí pomocí [časovačů objektů actor a připomenutí](service-fabric-reliable-actors-timers-reminders.md) , která se týkají souběžnosti založeného na funkci.

## <a name="next-steps"></a>Další kroky
Začněte vytvořením první Reliable Actors služby:
   * [Začínáme s Reliable Actors v .NET](service-fabric-reliable-actors-get-started.md)
   * [Začínáme s Reliable Actors v Java](./service-fabric-create-your-first-linux-application-with-java.md)

<!--Image references-->
[1]: ./media/service-fabric-reliable-actors-introduction/concurrency.png
[2]: ./media/service-fabric-reliable-actors-introduction/distribution.png
[3]: ./media/service-fabric-reliable-actors-introduction/actor-communication.png
