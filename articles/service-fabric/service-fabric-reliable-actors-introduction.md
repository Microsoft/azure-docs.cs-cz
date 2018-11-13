---
title: Service Fabric Reliable Actors – přehled | Dokumentace Microsoftu
description: Úvod do programovacího modelu Service Fabric Reliable Actors.
services: service-fabric
documentationcenter: .net
author: vturecek
manager: timlt
editor: ''
ms.assetid: 7fdad07f-f2d6-4c74-804d-e0d56131f060
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 11/01/2017
ms.author: vturecek
ms.openlocfilehash: 363cba145ed4d5bcf138cf3f7130763891c51e8b
ms.sourcegitcommit: da3459aca32dcdbf6a63ae9186d2ad2ca2295893
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/07/2018
ms.locfileid: "51258057"
---
# <a name="introduction-to-service-fabric-reliable-actors"></a>Seznámení se službou Service Fabric Reliable Actors
Reliable Actors je architektura aplikací Service Fabric na základě [virtuálního objektu Actor](https://research.microsoft.com/en-us/projects/orleans/) vzor. Reliable Actors rozhraní API poskytuje programovací model s jedním vláknem založená na škálovatelnost a spolehlivost záruky poskytované Service Fabric.

## <a name="what-are-actors"></a>Co jsou Actors?
Prvek "actor" je izolované, nezávislé jednotka výpočetního výkonu a stavu se spuštění s jedním vláknem. [Vzor objektu actor](https://en.wikipedia.org/wiki/Actor_model) je výpočetní model pro souběžné nebo distribuované systémy, ve které velký počet těchto objektů actor mohou spouštět souběžně a nezávisle na sobě. Actors můžou klienti komunikovat mezi sebou a bude možné vytvořit více objektů actor.

### <a name="when-to-use-reliable-actors"></a>Použití Reliable Actors
Service Fabric Reliable Actors je implementace návrhový vzor objektu actor. Rozhodnutí, jestli se má použít konkrétní vzor se provádí podle toho, zda je či není softwaru návrhu problém stejně jako u jakékoli vzoru návrhu softwaru vyhovuje vzoru.

I když objekt actor návrhu může být vhodná přizpůsobit počet problémů distribuovaných systémů a scénáře, pozor, že se musí vzít v úvahu omezení rozhraní implementace a vzor. Jako obecné pokyny zvažte vzor objektu actor pro modelování problém nebo scénáři, pokud:

* Zahrnuje velké množství prostoru problém (tisíce nebo více) malé, nezávislou a izolovanou jednotek ve stavu a logiku.
* Chcete pracovat s objekty s jedním vláknem, které nevyžadují žádné významné interakce z externí komponenty, včetně dotazování na stav mezi sadu objektů actor.
* Vaše instance objektu actor nebude blokovat volající s neočekávaným zpožděním vydáním vstupně-výstupních operací.

## <a name="actors-in-service-fabric"></a>V Service Fabric actors
V Service Fabric actors jsou implementovány v rámci Reliable Actors: na základě objektů actor vzor aplikační rozhraní založené na horní [Service Fabric Reliable Services](service-fabric-reliable-services-introduction.md). Každá služba Reliable Actors, který napíšete je ve skutečnosti oddílů, stavovou službu Reliable.

Každý objekt actor se definuje jako instance objektu actor typu identické s stejným způsobem jako objekt .NET je instance typu .NET. Například může být typem objektu actor, který implementuje funkce kalkulačku a může být mnoho actors tohoto typu, které jsou distribuovány na různých uzlech v clusteru. Každý takový objekt actor se jednoznačně identifikují pomocí ID objektu actor

## <a name="actor-lifetime"></a>Doba života objektu actor
Service Fabric actors jsou virtuální, což znamená, že jejich životního cyklu objektu se neváže na jejich reprezentaci v paměti. V důsledku toho se nemusíte být explicitně vytvoření nebo zničení. Modul runtime Reliable Actors automaticky aktivuje objektu actor první čas přijme žádost pro ID tohoto objektu actor. Pokud prvek "actor" se nepoužívá pro určitou dobu, modul runtime Reliable Actors uvolňování paměti – shromažďuje objektů v paměti. Také bude udržovat znalostní báze existence objektu actor, by mělo stačit později znovu aktivovat. Další podrobnosti najdete v tématu [objektu Actor životní cyklus a uvolňování paměti kolekce](service-fabric-reliable-actors-lifecycle.md).

Tato abstrakce životního cyklu virtuálního objektu actor provádí některé upozornění v důsledku vzoru virtuálního objektu actor a ve skutečnosti odchylují implementace Reliable Actors v některých případech z tohoto modelu.

* Prvek "actor" se automaticky aktivuje (což objekt actor budovat) při prvním přijetí zprávy jeho ID objektu actor. Po nějaké době objektu actor je uvolněna z paměti. V budoucnu znovu s použitím ID objektu actor, způsobí, že byly konstruovány nový objekt actor. Prvek "actor" stavu outlives životnosti objektu při uložení do Správce stavu.
* Voláním jakékoli metody objektu actor pro ID objektu actor se aktivuje tohoto objektu actor. Z tohoto důvodu objektu actor typy mají jejich konstruktoru nevolá implicitně modulem runtime. Proto klientský kód nemůžete předat parametry konstruktoru typu objektu actor, i když parametrů může být předán konstruktoru objektu actor samotné služby. Výsledkem je, že actors lze vytvořit ve stavu částečně inicializovaného v době, kdy ostatní metody jsou volány, pokud objekt actor vyžaduje inicializační parametry z klienta. Neexistuje žádné jedním vstupním bodem pro aktivaci objektu actor z klienta.
* I když v Reliable Actors implicitně vytvořit objekty actor. Máte možnost explicitně odstranit prvek "actor" a jeho stav.

## <a name="distribution-and-failover"></a>Distribuce a převzetí služeb při selhání
Zajištění škálovatelnosti a spolehlivosti Service Fabric distribuuje actors v rámci clusteru a automaticky je migruje z uzlů s chybou v pořádku ty podle potřeby. To je abstrakcí než [oddílů, stavové služby Reliable](service-fabric-concepts-partitioning.md). Distribuce, škálovatelnost, spolehlivost a automatické převzetí služeb při selhání jsou všechny tím, že skutečnost, že actors běží uvnitř spolehlivou stavovou službu volá se, *služba objektu Actor*.

Actors jsou distribuovány napříč oddíly služba objektu Actor a tyto oddíly jsou distribuovány napříč uzly v clusteru Service Fabric. Každý oddíl služby obsahuje sadu objektů actor. Service Fabric spravuje distribuce a převzetí služeb při selhání oddílů služby.

Službu actor service s devět oddíly, které jsou nasazené na tři uzly pomocí výchozí umístění oddílu objektu actor například by distribuovat thusly:

![Distribuce Reliable Actors][2]

Rozhraní objektu Actor spravuje oddílu schématu a klíč nastavení rozsahu za vás. To zjednodušuje některé možnosti ale také provede věnujete pozornost:

* Reliable Services umožňuje vyberte schéma rozdělení oddílů, rozsahu klíčů (při použití řady schéma vytváření oddílů) a počet oddílů. Reliable Actors je omezen na rozsah (jednotné schéma Int64) schéma vytváření oddílů a vyžaduje, že používáte celý rozsah klíčů Int64.
* Ve výchozím nastavení se umístí do oddílů, což vede k rovnoměrné náhodně objektů actor.
* Protože umísťují náhodně objektů actor, by měla očekávat, že operace objektu actor budou vždy vyžadovat síťové komunikace, včetně serializace a deserializace dat volání metody, latenci a zatížení.
* V pokročilých scénářích je možné do umístění oddílu objektu actor ovládacího prvku s použitím objektu actor Int64 ID, které se mapují na konkrétní oddíly. Nicméně to uděláte tak může vést k nevyvážené distribuci aktérů napříč oddíly.

Další informace o tom, jak jsou rozdělené služby objektu actor, najdete [dělení koncepty pro objekty actor](service-fabric-reliable-actors-platform.md#service-fabric-partition-concepts-for-actors).

## <a name="actor-communication"></a>Komunikace objektu actor
Objekt actor interakce jsou definovány v rozhraní, které jsou sdíleny, který implementuje rozhraní objektu actor a klienta, který získá proxy pro prvek "actor" prostřednictvím stejné rozhraní. Protože toto rozhraní je použito k volání metody objektu actor asynchronně, každou metodu v rozhraní musí být vracející úlohy.

Volání metod a jejich odpovědi nakonec za následek síťové požadavky napříč clusterem, tak argumenty a typy výsledků úlohy, které vracejí musí být serializovatelný platformou. Konkrétně musí být [kontraktů dat serializovatelný](service-fabric-reliable-actors-notes-on-actor-type-serialization.md).

### <a name="the-actor-proxy"></a>Proxy server objektu actor
Reliable Actors klientského rozhraní API poskytuje komunikaci mezi instance objektu actor a klient služby objektu actor. Ke komunikaci s prvek "actor", klient vytvoří objekt proxy objektu actor, který implementuje rozhraní objektu actor. Klient komunikuje s objektem actor vyvoláním metody u objektu proxy. Proxy server objektu actor lze použít pro komunikaci klienta do objektu actor a objekt actor actor.

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


Všimněte si, že jsou dva druhy údajů použít k vytvoření objektu actor proxy objekt actor s ID a název aplikace. Objekt actor s ID jednoznačně identifikuje objekt actor, zatímco identifikuje název aplikace [aplikace Service Fabric](service-fabric-reliable-actors-platform.md#application-model) nasazená objekt actor.

`ActorProxy`(C#) / `ActorProxyBase`Třídy (Java) na straně klienta provádí potřebné řešení vyhledejte objekt actor podle ID a otevřete komunikační kanál s ním. Také se pokusí vyhledat objekt actor v případě selhání komunikace a převzetí služeb při selhání. V důsledku toho doručování zpráv má následující vlastnosti:

* Doručování zpráv je nejlepší úsilí.
* Objekty actor může se zobrazit duplicitní zprávy ze stejného klienta.

## <a name="concurrency"></a>Souběžnost
Modul runtime Reliable Actors poskytuje jednoduchý přístup na základě zapnout model pro přístup k objektu actor metody. To znamená, že v každém okamžiku může být aktivní uvnitř objektu actor kódu více než jedno vlákno. Přístupu na základě zapnout výrazně zjednodušuje souběžných systémy, protože není nutné pro synchronizaci mechanismy pro přístup k datům. Také znamená, že systémy musí být vytvořeny s speciální aspekty pro povahy jednovláknový přístup u každé instance objektu actor.

* Instance jednoho objektu actor více než jeden požadavek nelze zpracovat v čase. Instance objektu actor může způsobit kritickým bodem propustnost, pokud se očekává, že zpracování souběžných požadavků.
* Pokud žádost o cyklické mezi dvěma objekty actor během externí požadavek je na jednu z aktéry současně, můžete actors zablokování na sobě navzájem. Modulu runtime actor automaticky časový limit při volání objektu actor a vyvolání výjimky na volajícím přerušení situace vzájemného zablokování je to možné.

![Reliable Actors komunikace][3]

### <a name="turn-based-access"></a>Přístup založený na řadě vy
Zapnutí se skládá z dokončení provádění metody objektu actor v reakci na žádost z jiných objektů actor nebo klienty nebo dokončení provádění [časovače a připomenutí](service-fabric-reliable-actors-timers-reminders.md) zpětného volání. I když tyto metody a zpětná volání jsou asynchronní, modul runtime Actors není prokládání dat je. Předtím, než je povolený nový zapnout, musí být kompletní dokončená zapnout. Jinými slovy musí být kompletní dokončená před nové volání do metody objektu actor metody nebo časovače a připomenutí zpětné volání, které právě probíhá nebo zpětného volání není povolen. Metoda nebo zpětné volání se považuje za dokončení provádění vrátil z metody nebo dokončení zpětného volání a úkol vrácený metodou nebo zpětného volání. Je které stojí za to, kdy se klade důraz že je dodržena této souběžnosti založená na řadě vy i přes různé metody, časovačů a zpětná volání.

Modul runtime Actors získávání zámku na objektu actor na začátku na řadě vy a uvolnění zámku na konci zapnout vynucuje souběžnosti založená na řadě vy. Proto souběžnosti založená na řadě vy vynucuje na základě podle objektu actor a není mezi objekty actor. Metody objektu actor a zpětná volání časovače a připomenutí lze spustit současně jménem jiné objekty actor.

Následující příklad znázorňuje výše uvedené koncepty. Vezměte v úvahu typ objektu actor, který implementuje dvě asynchronní metody (například *– metoda1* a *Method2*), časovače a připomenutí. Následující diagram ukazuje příklad časovou osu pro provádění těchto metod a zpětná volání jménem dva objekty actor (*ActorId1* a *ActorId2*), která patří k tomuto typu objektu actor.

![Reliable Actors runtime zapnout souběžnosti na základě a přístup][1]

Tento diagram dodržovat tato konvence:

* Každý svislá čára ukazuje logický tok provádění metody nebo zpětné volání jménem konkrétního objektu actor.
* V chronologickém pořadí s novější událostí pod starší dochází k události na každé svislé čáry.
* Různé barvy se používají pro odpovídající jiné objekty actor časové osy.
* Zvýraznění se používá k označení doby trvání, pro kterou je držen zámek na objektu actor jménem metody nebo zpětného volání.

Některé důležité body ke zvážení:

* Zatímco *– metoda1* provádí jménem *ActorId2* v reakci na žádost klienta *xyz789*, jiná žádost klienta (*abc123*) doručení, která vyžaduje také *– metoda1* být vykonán *ActorId2*. Ale druhý provádění *– metoda1* nezačíná až do dokončení předchozích spuštění. Podobně, připomenutí registrovaných *ActorId2* je aktivována při *– metoda1* se provádí v reakci na žádost klienta *xyz789*. Zpětné volání připomenutí provádí pouze po obou prováděných *– metoda1* se dokončí. Toto vše je z důvodu souběžnosti na základě zapnout u zůstala vynucená *ActorId2*.
* Podobně se souběžnosti na základě zapnout také vynucuje pro *ActorId1*, jak je ukázáno prováděním *– metoda1*, *Method2*a zpětné volání časovače jménem  *ActorId1* děje způsobem sériového portu.
* Provádění *– metoda1* jménem *ActorId1* se překrývá s produktem jménem *ActorId2*. Je to proto, že vynucuje souběžnosti na základě vypněte pouze v rámci objektu actor a není mezi objekty actor.
* V některých spuštění metody/zpětného volání `Task`(C#) / `CompletableFuture`(Java) vrácený metodou/zpětné volání dokončení po vrácení metody. V některých jiných asynchronní operace již byla dokončena v době, kdy metoda zpětného volání vrátí. V obou případech se uvolní zámek na objektu actor až po metoda zpětného volání vrátí i dokončení asynchronní operace.

### <a name="reentrancy"></a>Vícenásobný přístup
Modul runtime Actors umožňuje opětovný vstup ve výchozím nastavení. To znamená, že pokud metoda objektu actor *objektu Actor A* volá metodu pro *objektu Actor B*, která volá jinou metodu na *objektu Actor A*, že metoda je povoleno spuštění. Toto je vzhledem k tomu, že je součástí stejného logického řetěz volání kontextu. Všechna volání časovače a připomenutí začínat nového logického kontextu volání. Zobrazit [vícenásobný přístup v Reliable Actors](service-fabric-reliable-actors-reentrancy.md) další podrobnosti.

### <a name="scope-of-concurrency-guarantees"></a>Obor záruky souběžnosti
Modul runtime Actors poskytuje tyto souběžnosti záruky v situacích, kde řídí volání těchto metod. Například poskytuje tyto záruky pro volání metod, které se provádějí v reakci na požadavek klienta, a také pro zpětná volání časovače a připomenutí. Ale pokud kód objektu actor přímo volá tyto metody mimo mechanismy poskytované runtime objektů actor, modul runtime nemůže zadejte jakékoli záruky souběžnosti. Například pokud je metoda vyvolána v rámci některé úlohy, který není spojen s úlohou vrácené z metody objektu actor, modul runtime nemůže zadejte souběžnosti záruky. Pokud metoda se volá z vlákna, která vytvoří objekt actor sama o sobě, modul runtime také nelze zadejte souběžnosti záruky. Proto k provádění operací na pozadí, měli používat actors [objektu actor časovače a připomenutí objektu actor](service-fabric-reliable-actors-timers-reminders.md) , respektují souběžnosti založená na řadě vy.

## <a name="next-steps"></a>Další postup
Začínáme vytvořením první služby Reliable Actors:
   * [Začínáme s Reliable Actors v .NET](service-fabric-reliable-actors-get-started.md)
   * [Začínáme s Reliable Actors v Javě](service-fabric-reliable-actors-get-started-java.md)

<!--Image references-->
[1]: ./media/service-fabric-reliable-actors-introduction/concurrency.png
[2]: ./media/service-fabric-reliable-actors-introduction/distribution.png
[3]: ./media/service-fabric-reliable-actors-introduction/actor-communication.png
