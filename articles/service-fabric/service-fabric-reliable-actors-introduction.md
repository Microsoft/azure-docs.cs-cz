---
title: Služba Fabric Reliable Actors přehled | Microsoft Docs
description: Úvod do programovacího modelu Service Fabric Reliable Actors.
services: service-fabric
documentationcenter: .net
author: vturecek
manager: timlt
editor: ''
ms.assetid: 7fdad07f-f2d6-4c74-804d-e0d56131f060
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 11/01/2017
ms.author: vturecek
ms.openlocfilehash: 6a13ced8b1c49239d1ad5fb96775f43de9c3943e
ms.sourcegitcommit: 48ab1b6526ce290316b9da4d18de00c77526a541
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/23/2018
---
# <a name="introduction-to-service-fabric-reliable-actors"></a>Seznámení se službou Service Fabric Reliable Actors
Reliable Actors je architektura aplikace Service Fabric na základě [virtuální objektu Actor](http://research.microsoft.com/en-us/projects/orleans/) vzor. Rozhraní API spolehlivé aktéři poskytuje programovací model jednovláknové založený na škálovatelnost a spolehlivost záruk poskytnutých Service Fabric.

## <a name="what-are-actors"></a>Jaké jsou aktéři?
Objekt actor je s jednotkou izolované, nezávislé výpočetních operací a stavu s jedním podprocesem provádění. [Vzor objektu actor](https://en.wikipedia.org/wiki/Actor_model) je výpočetní model souběžných nebo distribuovaných systémů, ve které velké množství těchto aktéři mohou být prováděny současně a nezávisle na sobě navzájem. Aktéři mohou komunikovat navzájem a mohou vytvářet další aktéři.

### <a name="when-to-use-reliable-actors"></a>Kdy použít Reliable Actors
Služba Fabric Reliable Actors je implementace tohoto vzoru návrhu objektu actor. Rozhodnutí, jestli se má používat specifického vzoru se provádí podle zda softwaru návrh problém stejně jako u jakékoli vzoru návrhu softwaru vyhovuje vzoru.

I když objektu actor návrh vzor dobrou vejdou na počet distribuovaných systémů problémy a scénáře, postupujte opatrně, že musí být provedeny zvážení omezení vzoru a rozhraní ho implementujete. Jako obecné pokyny vezměte v úvahu vzor objektu actor pro modelování problém nebo scénář, pokud:

* Váš prostor problém zahrnuje velký počet (tisíc nebo více) malé, nezávislé a izolované jednotek stavu a logiku.
* Budete chtít pracovat s jedním podprocesem objekty, které nevyžadují významné interakce z externí součásti, včetně dotaz na stav mezi sadu aktéři.
* Vaše instance objektu actor neblokuje volající s nepředvídatelným zpoždění vydáním vstupně-výstupních operací.

## <a name="actors-in-service-fabric"></a>Aktéři v Service Fabric
V Service Fabric aktéři jsou implementované v rámci Reliable Actors: na základě objektu actor vzor aplikační rozhraní založené na [spolehlivé služby Service Fabric](service-fabric-reliable-services-introduction.md). Každý spolehlivé objektu Actor službu, kterou píšete je ve skutečnosti oddílů, stavová spolehlivá služba.

Každý objekt actor je definován jako instanci objektu actor typu identické způsobem, jakým objekt .NET je instance typu .NET. Například může být typ objektu actor, která implementuje funkce kalkulačky a může být mnoho aktéři daného typu, které jsou rozmístěny v různých uzlech v clusteru. Každé takové objektu actor je jedinečně identifikovaný identifikátor objektu actor.

## <a name="actor-lifetime"></a>Doba života objektu actor
Service Fabric aktéři jsou virtuální, což znamená, že své životnosti není vázaný k jejich reprezentaci v paměti. V důsledku toho se nemusíte být explicitně vytvořen nebo zničeno. Modul runtime Reliable Actors automaticky aktivuje doba objektu actor první obdrží žádost pro ID tohoto objektu actor. Pokud objekt actor se nepoužívá pro určitou dobu, modul runtime Reliable Actors uvolňování paměti – shromažďuje objekt v paměti. Také zachová znalostní báze objektu actor existence měli později znovu aktivovat. Další podrobnosti najdete v tématu [kolekce paměti a životního cyklu objektu Actor](service-fabric-reliable-actors-lifecycle.md).

Tato abstrakce doba života objektu actor virtuální představuje některé upozornění v důsledku virtuální objektu actor modelu a ve skutečnosti implementace Reliable Actors odchylují někdy z tohoto modelu.

* Objekt actor se automaticky aktivuje (což objekt actor tak, aby zkonstruovat) při prvním přijetí zprávy jeho ID objektu actor. Po nějaké časové období je objekt actor uvolnění z paměti. V budoucnu znovu pomocí ID objektu actor, způsobí, že objekt nového objektu actor zkonstruovat. Stav objektu actor outlives doba života objektu, když uložené v správce stavu.
* Voláním jakékoli metody objektu actor pro ID objektu actor aktivuje tohoto objektu actor. Z tohoto důvodu objektu actor typy mají jejich konstruktor volána implicitně modulem runtime. Kód klienta proto nemůžete předat parametry do konstruktoru objektu actor typu, i když může být předány parametry objektu actor konstruktor samotné služby. Výsledkem je, že aktéři může zkonstruovat ve stavu částečně inicializovat podle času, které se nazývají jiné metody, pokud objektu actor vyžaduje inicializační parametry z klienta. Neexistuje jeden vstupní bod pro aktivaci objektu actor z klienta.
* I když Reliable Actors implicitně vytvořit objekty objektu actor; Máte možnost explicitně odstranit objekt actor a její stav.

## <a name="distribution-and-failover"></a>Distribuce a převzetí služeb při selhání
Zajistit škálovatelnost a spolehlivost, Service Fabric distribuuje aktéři v rámci clusteru a automaticky je migraci z selhání uzlů do pořádku ty, které jsou podle potřeby. Toto je abstrakci přes [oddílů, stavová služba spolehlivé](service-fabric-concepts-partitioning.md). Distribuce, škálovatelnost, spolehlivost a automatické převzetí služeb při selhání jsou k všechny dispozici základě skutečnost, že aktéři běží ve stavové služby spolehlivé volat *služby objektu Actor*.

Aktéři jsou distribuovány na oddíly služby objektu Actor a tyto oddíly jsou rozdělené mezi uzly v clusteru Service Fabric. Každý oddíl služby obsahuje sadu aktéři. Service Fabric spravuje distribuce a převzetí služeb při selhání oddílů služby.

Například služby objektu actor s devět oddíly, které jsou nasazené na tři uzly pomocí výchozí umístění oddílu objektu actor by distribuována thusly:

![Spolehlivé aktéři distribuce][2]

Rozhraní objektu Actor oddílu schéma a klíč nastavení rozsahu pro vás spravuje. Zjednodušuje některé možnosti, ale také představuje některé pozornost:

* Spolehlivé služby umožňuje vyberte schéma rozdělení oddílů, klíče rozsah (při použití rozsah dělení schéma) a počet oddílu. Reliable Actors je omezen na rozsah dělení schéma (uniform Int64 schéma) a vyžaduje, že používáte plný rozsah klíče Int64.
* Ve výchozím nastavení se umístí do oddílů, které jsou výsledkem uniform distribuční náhodně aktéři.
* Protože aktéři jsou náhodně umístěn, je třeba očekávat, objektu actor operace budou vždy vyžadovat komunikaci sítě, včetně serializace a deserializace dat volání metody, by docházelo k latenci a zatížení.
* V pokročilých scénářích je možné umístění oddílu objektu actor ovládacího prvku pomocí objektu actor Int64 ID, které mapují na konkrétní oddíly. Ale to tak může způsobit jako nevyváženou distribučního aktéři napříč oddíly.

Další informace o tom, jak jsou služby objektu actor do několika oddílů, najdete v části [dělení koncepty pro aktéři](service-fabric-reliable-actors-platform.md#service-fabric-partition-concepts-for-actors).

## <a name="actor-communication"></a>Komunikace objektu actor
Interakce objektu actor jsou definovány v rozhraní, které sdílí objektu actor, který implementuje rozhraní a klienta, který získá proxy serveru k objektu actor prostřednictvím stejné rozhraní. Protože toto rozhraní se použije k vyvolání metody objektu actor asynchronně, musí být každý metoda v rozhraní vrácení úloh.

Volání metod a jejich odpovědi konečným výsledkem je síťové požadavky napříč clusterem, takže argumenty a typy výsledků úlohy, které vracejí musejí být serializovatelná platformou. Konkrétně musí být [kontraktů dat serializovatelný](service-fabric-reliable-actors-notes-on-actor-type-serialization.md).

### <a name="the-actor-proxy"></a>Proxy objektu actor
Klient Reliable Actors rozhraní API poskytuje komunikaci mezi instanci objektu actor a objektu actor klienta. Ke komunikaci s objektu actor, klient vytvoří objekt actor proxy, který implementuje rozhraní objektu actor. Klient komunikuje s objektu actor vyvoláním metody pro objekt proxy serveru. Proxy objektu actor lze použít pro komunikaci klienta do objektu actor a objektu actor actor.

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


Upozorňujeme, že jsou dva kusy informace, které slouží k vytvoření objektu actor proxy objektu objekt actor s ID a název aplikace. ID objektu actor jednoznačně identifikuje objektu actor, zatímco identifikuje název aplikace [aplikace Service Fabric](service-fabric-reliable-actors-platform.md#application-model) kde je nasazená objektu actor.

`ActorProxy`(C#) nebo `ActorProxyBase`– třída (Java) na straně klienta provádí nezbytné řešení pro vyhledání objektu actor podle ID a otevřete komunikační kanál s ním. Také se pokusí najít objektu actor v případě selhání komunikace a převzetí služeb při selhání. V důsledku toho doručování zpráv má následující vlastnosti:

* Doručení zpráv je nejlepší úsilí.
* Aktéři může se zobrazit duplicitní zprávy ze stejného klienta.

## <a name="concurrency"></a>Souběžnost
Modul runtime Reliable Actors poskytuje jednoduché přístupu na základě zapněte model pro přístup k objektu actor metody. To znamená, že kdykoli může být aktivní uvnitř objekt actor kód více než jedno vlákno. Není nutné pro synchronizaci mechanismy pro přístup k datům přístupu na základě zapnout výrazně zjednodušuje souběžných systémy. Taky to znamená, že systémy musí být vytvořeny s zvláštní upozornění pro jednovláknové přístup povaze každá instance objektu actor.

* Více než jeden požadavek nelze zpracovat instance jednoho objektu actor v čase. Instance objektu actor může způsobit úzkým místem propustnost, pokud se očekává, zpracování souběžných požadavků.
* Aktéři můžete zablokování na sobě navzájem, pokud je požadavek cyklické mezi dvěma aktéři při externí požadavku na jednu z aktéři současně. Modul runtime objektu actor automaticky vypršení časového limitu při volání objektu actor a způsobí výjimku volajícího k přerušení situacích možné zablokování.

![Spolehlivé aktéři komunikace][3]

### <a name="turn-based-access"></a>Přístupu na základě zapnout
Zapněte se skládá z dokončení provádění metody objektu actor v reakci na žádost o jiných klientů nebo aktéři nebo dokončení provádění [časovače nebo připomenutí](service-fabric-reliable-actors-timers-reminders.md) zpětného volání. I když jsou tyto metody a zpětná volání asynchronní, modul runtime aktéři není prokládání dat je. Předtím, než je povolený nový zapnout, musí být plně dokončení zapnout. Jinými slovy musí být plně dokončení před nové volání do metody objektu actor metoda nebo časovače nebo připomenutí zpětné volání, které právě probíhá nebo je povoleno zpětného volání. Metoda nebo zpětné volání se považuje dokončily, pokud provádění vrátila z metody nebo dokončení zpětného volání a úloha vrácená metoda nebo zpětného volání. Je vhodné zdůraznění, že na základě zapnout concurrency je dodržena i přes různé metody, časovače a zpětná volání.

Modul runtime aktéři vynucuje na základě zapnout souběžnosti získávání zámku na objektu actor na začátku zapnout a uvolnění uzamčení na konci zapnout. Na základě zapnout souběžnosti je proto vynucují na základě za objekt actor a není mezi aktéři. Metody objektu actor a zpětná volání časovače nebo připomenutí můžete spustit současně jménem různých aktéři.

Následující příklad ilustruje výše koncepty. Vezměte v úvahu typ objektu actor, který implementuje dvě asynchronní metody (Řekněme, *Method1* a *Method2*), a časovač a připomenutí. Následující diagram ukazuje příklad časovou osu pro provádění těchto metod a zpětná volání jménem dvě aktéři (*ActorId1* a *ActorId2*), patří do tohoto typu objektu actor.

![Spolehlivé aktéři modul runtime na základě zapnout souběžnosti a přístup][1]

Tento diagram dodržovat tyto konvence:

* Každé svislé čáry zobrazuje logický tok provádění metody nebo zpětné volání jménem konkrétního objektu actor.
* V chronologickém pořadí s novější událostí pod starší dochází k události na každé svislé čáry označit.
* Různé barvy se používají pro odpovídající různých aktéři časové osy.
* Zvýraznění je slouží k určení doby trvání, pro který zámek na objektu actor trvá jménem metoda nebo zpětného volání.

Některé důležité body vzít v úvahu:

* Při *Method1* provádí jménem *ActorId2* v reakci na žádost klienta *xyz789*, další požadavek klienta (*abc123*) dorazí, který taky vyžaduje *Method1* být vykonán *ActorId2*. Ale druhý provádění *Method1* nemá na začátku až do dokončení předchozí provádění. Podobně, zobrazí se připomenutí registrovaných *ActorId2* aktivuje se při *Method1* je spouštěna v reakci na žádost klienta *xyz789*. Zpětné volání připomenutí se spustí až po obou spuštěních z *Method1* jsou dokončeny. Všechny tyto je z důvodu souběžnosti na základě zapnout vynucení pro *ActorId2*.
* Podobně se na základě zapnout souběžnosti také vynucuje pro *ActorId1*, jak je znázorněno pomocí provádění *Method1*, *Method2*a zpětné volání časovače jménem  *ActorId1* děje sériové způsobem.
* Provádění *Method1* jménem *ActorId1* se překrývá s jeho spuštění jménem *ActorId2*. Je to proto, že na základě zapnout concurrency se vynucuje jenom v rámci objektu actor a není napříč aktéři.
* V některých spuštěních metoda/zpětného volání `Task`(C#) nebo `CompletableFuture`(Java) vrácený dokončení metoda/zpětného volání po vrátí metoda. V některých jiných asynchronní operace již byla dokončena o dobu, kterou vrátí metoda zpětného volání. V obou případech zámek na objektu actor vydání až po, vrátí metoda zpětného volání i dokončení asynchronní operace.

### <a name="reentrancy"></a>Vícenásobný přístup
Modul runtime aktéři umožňuje vícenásobný přístup ve výchozím nastavení. To znamená, že pokud metoda objektu actor *objektu Actor A* volá metodu na *objektu Actor B*, která volá jinou metodu na *objektu Actor A*, že je metoda může spustit. Je to proto, že je součástí stejného logického řetězce volání kontextu. Všechna volání časovače a připomenutí začínat nový kontext logické volání. Najdete v článku [vícenásobný přístup Reliable Actors](service-fabric-reliable-actors-reentrancy.md) další podrobnosti.

### <a name="scope-of-concurrency-guarantees"></a>Rozsah záruky souběžnosti
Modul runtime aktéři poskytuje tyto záruky souběžnosti v situacích, kde se řídí volání z těchto metod. Například poskytuje tyto záruky pro volání metod, které se provádějí v odpovědi na požadavek klienta, a také pro zpětné volání časovače a připomenutí. Ale pokud kód objektu actor přímo vyvolá tyto metody mimo mechanismy, které poskytuje modulem runtime aktéři, modul runtime nelze zadejte jakékoli záruky souběžnosti. Například pokud metoda je volána v rámci některých úloh, který není spojen s Úloha vrácená metody objektu actor, modul runtime nelze zadejte souběžnosti záruky. Pokud je metoda je vyvolána z vlákna, která objektu actor vytvoří sama o sobě, modul runtime nelze zadejte také souběžnosti záruky. Proto k provedení operace na pozadí, měli používat aktéři [objektu actor časovače a upomínek objektu actor](service-fabric-reliable-actors-timers-reminders.md) , respektují na základě zapnout souběžnosti.

## <a name="next-steps"></a>Další postup
Začínáme se ve vaší první službě Reliable Actors:
   * [Začínáme s Reliable Actors na rozhraní .NET](service-fabric-reliable-actors-get-started.md)
   * [Začínáme s Reliable Actors v jazyce Java](service-fabric-reliable-actors-get-started-java.md)

<!--Image references-->
[1]: ./media/service-fabric-reliable-actors-introduction/concurrency.png
[2]: ./media/service-fabric-reliable-actors-introduction/distribution.png
[3]: ./media/service-fabric-reliable-actors-introduction/actor-communication.png
