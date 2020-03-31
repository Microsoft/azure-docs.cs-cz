---
title: Service Fabric Spolehlivé objekty přehled
description: Úvod do modelprogramování Service Fabric Reliable Actors na základě vzoru virtuálního objektu actor.
author: vturecek
ms.topic: conceptual
ms.date: 11/01/2017
ms.author: vturecek
ms.openlocfilehash: 6aafa2a3372c431f8afa7fad41051c26c3fe5fcd
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "75645561"
---
# <a name="introduction-to-service-fabric-reliable-actors"></a>Úvod do service fabric spolehlivé aktéry
Reliable Actors je rozhraní aplikace Service Fabric založené na [virtuální objekt actor](https://research.microsoft.com/en-us/projects/orleans/) vzor. Rozhraní RELIABLE Actors API poskytuje jednovláknový programovací model založený na zárukách škálovatelnosti a spolehlivosti poskytovaných service fabric.

## <a name="what-are-actors"></a>Co jsou herci?
Objekt actor je izolované, nezávislé jednotky výpočetní ch a stavu s jednovláknové spuštění. Objekt [actor vzor](https://en.wikipedia.org/wiki/Actor_model) je výpočetní model pro souběžné nebo distribuované systémy, ve kterém velký počet těchto aktérů lze spustit současně a nezávisle na sobě. Herci mohou komunikovat mezi sebou a mohou vytvořit více herců.

### <a name="when-to-use-reliable-actors"></a>Kdy použít spolehlivé aktéry
Service Fabric Reliable Actors je implementace vzoru návrhu objektu actor. Stejně jako u každého vzoru návrhu softwaru se rozhodnutí, zda použít určitý vzor, provádí na základě toho, zda problém návrhu softwaru vyhovuje vzoru.

Přestože objekt actor návrhvzor může být vhodné pro řadu distribuovaných systémů problémy a scénáře, pečlivé zvážení omezení vzor a rozhraní, které jej implementuje. Jako obecné pokyny zvažte objekt actor vzor modelovat váš problém nebo scénář, pokud:

* Problémový prostor zahrnuje velké množství (tisíce nebo více) malých, nezávislých a izolovaných jednotek stavu a logiky.
* Chcete pracovat s objekty s jedním podprocesem, které nevyžadují významnou interakci z externích součástí, včetně dotazování stavu v rámci sady objektů actor.
* Instance objektu actor nezablokují volající s nepředvídatelnými zpožděními vydáním vstupně-vaoperací.

## <a name="actors-in-service-fabric"></a>Herci v service fabric
V Service Fabric, objekty actor jsou implementovány v rámci reliable actors: rozhraní aplikace založené na vzoru actor postavené na [service fabric spolehlivé služby](service-fabric-reliable-services-introduction.md). Každá služba reliable actor, kterou napíšete, je ve skutečnosti rozdělená, stavová spolehlivá služba.

Každý objekt actor je definován jako instance typu objektu actor, shodný se způsobem, jakým je objekt .NET instancí typu .NET. Může například existovat typ objektu actor, který implementuje funkce kalkulačky a může existovat mnoho aktérů tohoto typu, které jsou distribuovány na různých uzlech v clusteru. Každý takový objekt actor je jednoznačně identifikován id objektu actor.

## <a name="actor-lifetime"></a>Herec Život
Objekty actor Service Fabric jsou virtuální, což znamená, že jejich životnost není vázána na jejich reprezentaci v paměti. V důsledku toho není nutné explicitně vytvořit nebo zničit. Za běhu Spolehlivé objekty actor automaticky aktivuje objekt actor při prvním obdrží požadavek na toto ID objektu actor. Pokud objekt actor není používán po určitou dobu, spolehlivé actors čas uvolňování paměti shromažďuje objekt v paměti. Bude také udržovat znalosti o existenci herce, pokud je třeba znovu aktivovat později. Další podrobnosti naleznete [v tématu Actor životního cyklu a uvolňování paměti](service-fabric-reliable-actors-lifecycle.md).

Tento virtuální actor celoživotní abstrakce nese některé výhrady v důsledku virtuálního objektu actor modelu a ve skutečnosti spolehlivé actors implementace odchyluje občas od tohoto modelu.

* Objekt actor je automaticky aktivován (způsobuje objekt objektu objektu actor, které mají být vytvořeny) při prvním odeslání zprávy do jeho id objektu actor. Po určité době objekt u actor je uvolněno. V budoucnu pomocí ID objektu znovu způsobí, že nový objekt objektu actor, které mají být vytvořeny. Stav objektu přežije životnost objektu při uložení ve správci stavu.
* Volání libovolné metody actor pro ID objektu actor aktivuje tento objekt actor. Z tohoto důvodu typy objektů actor mají jejich konstruktor volána implicitně runtime. Proto kód klienta nelze předat parametry konstruktoru typu objektu actor, i když parametry mohou být předány konstruktoru objektu actor samotnou službou. Výsledkem je, že objekty actor mohou být vytvořeny v částečně inicializovaném stavu v době, kdy jsou na něm volány jiné metody, pokud objekt actor vyžaduje parametry inicializace od klienta. Neexistuje žádný jediný vstupní bod pro aktivaci objektu actor z klienta.
* Přestože spolehlivé actor implicitně vytvořit objekty objekty actor; máte možnost explicitně odstranit objekt actor a jeho stav.

## <a name="distribution-and-failover"></a>Distribuce a převzetí služeb při selhání
Chcete-li poskytnout škálovatelnost a spolehlivost, Service Fabric distribuuje objekty actor v celém clusteru a automaticky je migruje z uzlů se nezdařilo na v pořádku ty podle potřeby. Toto je abstrakce přes [rozdělené, stavové spolehlivé služby](service-fabric-concepts-partitioning.md). Distribuce, škálovatelnost, spolehlivost a automatické převzetí služeb při selhání jsou poskytovány na základě skutečnosti, že objekty actor jsou spuštěny uvnitř stavové spolehlivé služby s názvem *Actor Service*.

Objekty actor jsou distribuovány napříč oddíly služby actor a tyto oddíly jsou distribuovány mezi uzly v clusteru Service Fabric. Každý oddíl služby obsahuje sadu aktérů. Service Fabric spravuje distribuci a převzetí služeb při selhání oddílů služby.

Například služba objektu actor s devíti oddíly nasazenými do tří uzlů pomocí výchozího umístění oddílu objektu actor by byla distribuována takto:

![Spolehlivá distribuce herců][2]

Objekt actor Framework spravuje schéma oddílů a nastavení rozsahu klíčů za vás. To zjednodušuje některé volby, ale také nese určitou pozornost:

* Spolehlivé služby umožňuje zvolit schéma dělení, rozsah klíčů (při použití schéma dělení rozsahu) a počet oddílů. Spolehlivé objekty actor je omezena na schéma dělení rozsahu (jednotné schéma Int64) a vyžaduje, abyste použili celý rozsah klíčů Int64.
* Ve výchozím nastavení jsou objekty actor náhodně umístěny do oddílů, což vede k jednotnému rozložení.
* Vzhledem k tomu, že objekty actor jsou náhodně umístěny, je třeba očekávat, že operace objektu actor bude vždy vyžadovat síťovou komunikaci, včetně serializace a deserializace dat volání metody, vzniku latence a režie.
* V pokročilých scénářích je možné řídit umístění oddílu objektu actor pomocí Int64 id objektu actor, které jsou mapovány na konkrétní oddíly. To však může mít za následek nevyvážené rozdělení objekty actor napříč oddíly.

Další informace o tom, jak jsou rozděleny služby objektu actor, naleznete [v oddílech koncepty pro objekty actor](service-fabric-reliable-actors-platform.md#service-fabric-partition-concepts-for-actors).

## <a name="actor-communication"></a>Komunikace s hercem
Interakce objektu actor jsou definovány v rozhraní, které je sdíleno objektu actor, který implementuje rozhraní a klient, který získá proxy objektu actor prostřednictvím stejného rozhraní. Vzhledem k tomu, že toto rozhraní se používá k vyvolání metody objektu actor asynchronně, každá metoda v rozhraní musí být task-returning.

Vyvolání metody a jejich odpovědi nakonec za následek síťové požadavky v celém clusteru, takže argumenty a typy výsledků úkoly, které vrátí musí být serializovatelné platformou. Zejména musí být [datové smlouvy serializovatelné](service-fabric-reliable-actors-notes-on-actor-type-serialization.md).

### <a name="the-actor-proxy"></a>Zmocněnec actor
Rozhraní API klienta Reliable Actors poskytuje komunikaci mezi instancí objektu actor a klientem objektu actor. Chcete-li komunikovat s objektem actor, klient vytvoří objekt proxy objektu objektu actor, který implementuje rozhraní objektu actor. Klient spolupracuje s objektem actor vyvoláním metod na proxy objektu. Proxy objektu actor lze použít pro komunikaci mezi klientem a objektem actor a actor.The actor proxy can be used for client-to-actor and actor-actor communication.

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


Všimněte si, že dvě části informací použitých k vytvoření objektu proxy objektu objektu objektu actor jsou ID objektu actor a název aplikace. ID objektu actor jednoznačně identifikuje objekt actor, zatímco název aplikace identifikuje [aplikace Service Fabric,](service-fabric-reliable-actors-platform.md#application-model) kde je nasazen objekt actor.

Třída `ActorProxy`(C#) `ActorProxyBase`/ (Java) na straně klienta provádí potřebné řešení pro vyhledání objektu actor podle ID a otevření komunikačního kanálu s ním. Také se pokusí vyhledat objekt actor v případě selhání komunikace a převzetí služeb při selhání. V důsledku toho má doručení zprávy následující charakteristiky:

* Doručení zprávy je nejlepší úsilí.
* Objekty actor mohou přijímat duplicitní zprávy od stejného klienta.

## <a name="concurrency"></a>Souběžnost
Za běhu Reliable Actors poskytuje jednoduchý model přístupu na základě panelu pro přístup k metodám objektu actor. To znamená, že v kódu objektu objektu objektu objektu objektu objektu objektu objektu objektu může být kdykoli aktivní. Tahový přístup výrazně zjednodušuje souběžné systémy, protože není potřeba synchronizační mechanismy pro přístup k datům. To také znamená, že systémy musí být navrženy se zvláštními ohledem na přístup jednoho vlákna povahy každé instance objektu actor.

* Jedna instance objektu actor nemůže zpracovat více než jeden požadavek najednou. Instance objektu actor může způsobit problémové místo propustnosti, pokud se očekává zpracování souběžných požadavků.
* Objekty actor můžete zablokování na sebe, pokud je kruhový požadavek mezi dvěma aktéry, zatímco externí požadavek je podána jeden z aktérů současně. Objekt u actor runtime automaticky časový režim na volání objektu actor a vyvolat výjimku volajícího přerušit možné situace zablokování.

![Spolehlivá komunikace s herci][3]

### <a name="turn-based-access"></a>Tahový přístup
Turn se skládá z úplného spuštění metody objektu actor v reakci na požadavek od jiných herců nebo klientů nebo úplné spuštění zpětného volání [timer/reminder.](service-fabric-reliable-actors-timers-reminders.md) I když tyto metody a zpětná volání jsou asynchronní, modul runtime Actors není prokládání je. Zatáčka musí být zcela dokončena, než je povolen nový tah. Jinými slovy, metoda objektu actor nebo zpětné volání timer/reminder, která je aktuálně spuštěna, musí být zcela dokončena před povolením nového volání metody nebo zpětného volání. Metoda nebo zpětné volání je považovánza dokončený, pokud spuštění vrátil z metody nebo zpětného volání a úkol vrácený metodou nebo zpětné volání byla dokončena. Stojí za to zdůraznit, že tahová souběžnost je respektována i napříč různými metodami, časovači a zpětná volání.

Actors runtime vynucuje souběžnost tahové získáním zámek objektu actor na začátku otočení a uvolnění mačkání zámku na konci tahu. Proto je souběžnost na základě tahové je vynuceno na základě objektu actor a nikoli napříč objekty actor. Metody objektu actor a zpětná volání časovače/připomenutí lze spustit současně jménem různých aktérů.

Následující příklad ilustruje výše uvedené koncepty. Zvažte typ objektu actor, který implementuje dvě asynchronní metody (řekněme *Method1* a *Method2*), časovač a připomenutí. Následující diagram ukazuje příklad časové osy pro provádění těchto metod a zpětná volání jménem dvou herců (*ActorId1* a *ActorId2*), které patří do tohoto typu objektu actor.

![Spolehlivá časová souběžnost a přístup za běhu actors][1]

Tento diagram se řídí těmito konvencemi:

* Každý svislý řádek zobrazuje logický tok provádění metody nebo zpětného volání jménem určitého objektu actor.
* Události vyznačené na každé svislé čáře se vyskytují v chronologickém pořadí, přičemž novější události se vyskytují pod staršími.
* Různé barvy se používají pro časové osy odpovídající různým aktérům.
* Zvýraznění se používá k označení doby, po kterou je zámek objektu actor držen jménem metody nebo zpětného volání.

Některé důležité body, aby zvážila:

* Zatímco *Method1* je spuštěn jménem *ActorId2* v reakci na požadavek *klienta xyz789*, další požadavek klienta (*abc123*) dorazí, který také vyžaduje *Metodu1,* které mají být provedeny *ActorId2*. Druhé spuštění *Method1* však nespustí, dokud předchozí spuštění bylo dokončeno. Podobně připomenutí registrované *ActorId2* požáry při *Method1* je prováděna v reakci na požadavek *klienta xyz789*. Zpětné volání připomenutí je provedeno až po dokončení obou spuštění *Method1.* To vše je způsobeno tahovou souběžnosti vynucena pro *ActorId2*.
* Podobně je vynucena také časová souběžnost pro *ActorId1*, jak ukazuje provádění *Method1*, *Method2*a zpětné volání časovače jménem *ActorId1* děje sériovým způsobem.
* Provedení *Method1* jménem *ActorId1* překrývá s jeho provedení jménem *ActorId2*. Důvodem je, že tahová souběžnost je vynucena pouze v rámci objektu actor a nikoli napříč objekty actor.
* V některých spuštění metody/zpětného `Task`volání (C#) / `CompletableFuture`(Java) vrácena metoda/zpětné volání dokončí po vrátí metoda. V některých jiných asynchronní operace již byla dokončena v době, kdy metoda/zpětné volání vrátí. V obou případech je zámek objektu actor uvolněn pouze po vrácení metody/zpětného volání a dokončení asynchronní operace.

### <a name="reentrancy"></a>Vícenásobný přístup
Objekty actor runtime umožňuje reentrancy ve výchozím nastavení. To znamená, že pokud objekt actor metoda *actor A* volá metodu na *actor B*, který zase volá jinou metodu na *actor A*, tato metoda je povoleno spustit. Důvodem je, že je součástí stejného kontextu logického řetězce volání. Všechna volání časovače a připomenutí začínají novým kontextem logického volání. Další podrobnosti najdete v tématu [Reliable Actors reentrancy.](service-fabric-reliable-actors-reentrancy.md)

### <a name="scope-of-concurrency-guarantees"></a>Rozsah záruk souběžnosti
Objekty actor runtime poskytuje tyto záruky souběžnosti v situacích, kdy řídí vyvolání těchto metod. Například poskytuje tyto záruky pro vyvolání metody, které jsou prováděny v reakci na požadavek klienta, stejně jako pro časovač a připomenutí zpětná volání. Však pokud kód objektu actor přímo vyvolá tyto metody mimo mechanismy poskytované actors runtime, pak za běhu nemůže poskytnout žádné záruky souběžnosti. Například pokud je metoda vyvolána v kontextu některé úlohy, která není přidružena k úloze vrácené metodami objektu actor, pak za běhu nemůže poskytnout záruky souběžnosti. Pokud je metoda vyvolána z vlákna, které objekt actor vytvoří samostatně, pak za běhu také nemůže poskytnout záruky souběžnosti. Proto k provedení operací na pozadí by měly objekty actor používat [časovače objektu actor a připomenutí objektu actor,](service-fabric-reliable-actors-timers-reminders.md) které respektují tahovou souběžnost.

## <a name="next-steps"></a>Další kroky
Můžete začít budováním své první služby Reliable Actors:
   * [Začínáme se spolehlivými aktéry v rozhraní .NET](service-fabric-reliable-actors-get-started.md)
   * [Začínáme se spolehlivými herci na Javě](service-fabric-reliable-actors-get-started-java.md)

<!--Image references-->
[1]: ./media/service-fabric-reliable-actors-introduction/concurrency.png
[2]: ./media/service-fabric-reliable-actors-introduction/distribution.png
[3]: ./media/service-fabric-reliable-actors-introduction/actor-communication.png
