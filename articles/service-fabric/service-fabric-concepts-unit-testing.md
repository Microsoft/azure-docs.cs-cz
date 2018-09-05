---
title: Testování jednotek stavové služby v Azure Service Fabric | Dokumentace Microsoftu
description: Seznamte se s koncepty a postupy při testování služeb Stateful Service Fabric.
services: service-fabric
documentationcenter: .net
author: charleszipp
manager: timlt
editor: vturecek
ms.assetid: ''
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 09/04/2018
ms.author: ryanwi
ms.openlocfilehash: c2d98316b81b3d908ebbe6147fe40f231e94c142
ms.sourcegitcommit: cb61439cf0ae2a3f4b07a98da4df258bfb479845
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/05/2018
ms.locfileid: "43703611"
---
# <a name="unit-testing-stateful-services-in-service-fabric"></a>Testování jednotek stavové služby v Service Fabric

Tento článek popisuje koncepty a postupy při testování služeb Stateful Service Fabric. Testování v rámci Service Fabric si zaslouží svá specifika skutečnost, že kód aplikace, které aktivně běží v několika různých kontextech. Tento článek popisuje postupy, používá k zajištění, že kód aplikace spadá pod každým z různých kontextech možné spustit.

## <a name="unit-testing-and-mocking"></a>Testování jednotek a pro vytvoření modelu
Automatizované testování v rámci tohoto článku testování, které mohou být provedeny v rámci nástroje test runner, jako je například MSTest a NUnit. Testy jednotek v rámci tohoto článku není provedení operací s vzdálený prostředek, jako jsou databáze nebo rozhraní RESTFul API. By měl být imitace tyto vzdálené prostředky. Vytvoření modelu v rámci tohoto článku falšování, záznamu a řídit návratové hodnoty pro vzdálené prostředky.

### <a name="service-fabric-considerations"></a>Důležité informace o Service Fabric
Testování stavové služby Service Fabric je třeba mít na paměti. Za prvé spustí kód služby na několika uzlech, ale v různých rolích. Testy jednotek by se měl vyhodnotit kódu v rámci každé role k dosažení úplné pokrytí. Různé role by primární, aktivní sekundární, nečinná sekundární a neznámý. Žádná role nemusí obvykle zvláštní pokrytí jako Service Fabric bere v úvahu tuto roli služba void nebo mít hodnotu null. Za druhé každý uzel se změní jejich rolí v libovolném časovém okamžiku. K dosažení úplné pokrytí, by měl být testován cesta spuštění kódu se změnami role, ke kterým dochází.

## <a name="why-unit-test-stateful-services"></a>Proč to stavové služby testování částí? 
Testování stavové služby může pomoct odhalit další některých běžných chyb, které se provedou, které nemusí nutně být zachycena konvenční aplikace nebo testování částí specifického pro doménu. Například pokud má stavová služba některému ze stavů v paměti, tento typ testování můžete ověřit, že tento stav v paměti je udržovat synchronizované mezi jednotlivé repliky. Tento typ testování můžete také ověřit, že do stavové služby jsou reaguje na tokeny zrušení předaných v Service Fabric Orchestrace odpovídajícím způsobem. Při spuštění v případě by měl službu zastavit jakékoli dlouho spuštěn a/nebo asynchronní operace.  

## <a name="common-practices"></a>Běžné postupy

Následující části doporučí v nejběžnějších postupů pro stavové služby testování částí. Také výzva, co by měl mít napodobování vrstvy úzce zarovnání na Service Fabric Orchestrace a správa stavů. Napodobování knihovny existovat knihoven, které poskytují tuto funkci. [ServiceFabric.Mocks](https://www.nuget.org/packages/ServiceFabric.Mocks/) od 3.3.0 nebo novější je jeden takový knihovny, který poskytuje funkci napodobování doporučené a dodržovat postupy uvedené níže.

### <a name="arrangement"></a>Uspořádání

#### <a name="use-multiple-service-instances"></a>Použití víc instancí služby
Testy jednotek by se měl spustit víc instancí do stavové služby. To simuluje, co přesně se stane v clusteru, kde Service Fabric zřídí několik replik službou na různých uzlech. Každá z těchto instancí budete spouštět v jiném kontextu ale. Při spuštění testu, by měla každá instance naplní konfiguraci role očekávání v clusteru. Například pokud služba má mít Cílová velikost repliky 3, Service Fabric zřizují tří replik na různých uzlech. Jeden z nich je primární databází a další dvě jako právě aktivní sekundární společnosti.

Ve většině případů se liší cesta spuštění služby mírně pro každou z těchto rolí. Například pokud služby by neměl přijímání požadavků z aktivní sekundární, služby může mít kontrolu pro tento případ vyvolání zpět, že došlo k pokusu o informativní výjimka, která označuje požadavek na sekundární. Má několik instancí vám umožní tato situace má být testována.

Kromě toho má více instancí umožňuje testy role pro každou z těchto instancí ověřte, zda že jsou konzistentní bez ohledu na změny role odpovědi z přepnout.

#### <a name="mock-the-state-manager"></a>Napodobení správce stavu
State Manager by měl být považován za vzdálený prostředek a proto imitace. Při vytvoření modelu správce stavu, je třeba některé základní úložiště v paměti pro sledování, co se uloží do Správce stavu tak, aby může číst a ověřit. Jednoduchý způsob, jak toho dosáhnout, je vytvoření mock výskytů jednotlivých typů Reliable Collections. V rámci těchto mocks použijte datový typ, který úzce v souladu s operací provedených v této kolekci. Následují některé typy dat navrhované pro jednotlivé spolehlivé kolekce

- IReliableDictionary < TKey, TValue > -> System.Collections.Concurrent.ConcurrentDictionary < TKey, TValue >
- IReliableQueue<T> System.Collections.Generic.Queue -><T>
- IReliableConcurrentQueue<T> System.Collections.Concurrent.ConcurrentQueue -><T>

#### <a name="many-state-manager-instances-single-storage"></a>Velký počet instancí správce stavu, jednoho úložiště
Jak jsme zmínili, State Manager a Reliable Collections mají být považována za vzdálený prostředek. Proto tyto prostředky by měly a bude imitace v rámci testů jednotek. Ale při spuštění více instancí stavové služby bude obtížné udržovat synchronizované každého správce imitaci stavu napříč instancemi jiné stavové služby. Neopravňují stavové služby v clusteru Service Fabric postará udržování správce stavu každou sekundární repliku v souladu s primární replikou. Proto testy by se chová stejně tak, aby se můžete simulovat změny role.

Jednoduchým způsobem, jak tuto synchronizaci můžete dosáhnout, je použít vzor s jedním prvkem pro základní objekt, který ukládá data zapsaná do každé spolehlivé kolekce. Například, pokud je stavové služby pomocí `IReliableDictionary<string, string>`. Správce mock stavu by měla vrátit model `IReliableDictionary<string, string>`. Tento model může použít `ConcurrentDictionary<string, string>` ke sledování páry klíč/hodnota zapsána. `ConcurrentDictionary<string, string>` Jednotlivý prvek použit všemi instancemi manažerů stavu předat do služby.

#### <a name="keep-track-of-cancellation-tokens"></a>Mějte přehled o tokeny zrušení
Tokeny zrušení jsou že důležitý ještě běžně přehlédnuta aspekt stavové služby. Při spuštění primární repliky pro stavové služby Service Fabric, je k dispozici token zrušení. Tento token zrušení slouží ke službě signalizuje, že když je odebrán nebo snížena na jinou roli. Stavové služby by se měla zastavit jakékoli dlouho běžícího nebo asynchronní operace, takže Service Fabric můžete dokončit pracovního postupu změny role.

Po spuštění jednotky testů, všechny tokeny zrušení, které jsou k dispozici RunAsync, ChangeRoleAsync a OpenAsync, CloseAsync, měla být uložená během byly spuštění testu. Test k simulaci vypnutí služby nebo snížení úrovně a odpovídajícím způsobem ověřte odpoví služba vám umožní podniku na tyto tokeny.

#### <a name="test-end-to-end-with-mocked-remote-resources"></a>Test ukončení až do konce se imitaci vzdálené prostředky
Testy jednotek by měl spustit jako velkou část kódu aplikace, který může změnit stav stavové služby, jako je to možné. Doporučuje se, že testy se více začátku do konce ze své podstaty. Pouze mocks, které existují mají záznam, simulace, a který může ověřit vzdálený prostředek interakce. To zahrnuje interakce s State Manager a Reliable Collections. Následující fragment kódu je příkladem gherkin pro test, který ukazuje testování začátku do konce:

```
    Given stateful service named "fabric:/MyApp/MyService" is created
    And a new replica is created as "Primary" with id "111"
    And a new replica is created as "IdleSecondary" with id "222"
    And a new replica is created as "IdleSecondary" with id "333"
    And all idle secondary replicas are promoted to active secondary
    When a request is made to add the an employee "John Smith"
    And the active secondary replica "222" is promoted to primary
    And a request is made to get all employees
    Then the request should should return the "John Smith" employee
```

Tento test vyhodnotí, že data jsou zachyceny na jednu repliku je k dispozici na na sekundární repliku, pokud je povýšen na primární. Za předpokladu, že do spolehlivé kolekce je záložní úložiště pro data zaměstnanců, Aa potenciálních selhání, které by mohly být zachycena s tímto testem je, pokud kód aplikace nevykonala příkaz `CommitAsync` v transakci, chcete-li uložit nové zaměstnance. V takovém případě druhý požadavek na získání zaměstnanci by návratové zaměstnance přidal(a) prvního požadavku.

### <a name="acting"></a>Funguje
#### <a name="mimic-service-fabric-replica-orchestration"></a>Napodobení Orchestrace repliky Service Fabric
Při správě víc instancí služby, testy by měl inicializovat a dovolí těchto služeb stejným způsobem jako Orchestrace Service Fabric. Například když se v nové primární replice služby, Service Fabric se vyvolá CreateServiceReplicaListener, OpenAsync, ChangeRoleAsync a RunAsync. Události životního cyklu jsou popsány v následujících článcích:

- [Spuštění stavové služby](service-fabric-reliable-services-lifecycle.md#stateful-service-startup)
- [Vypnutí stavové služby](service-fabric-reliable-services-lifecycle.md#stateful-service-shutdown)
- [Primární záměna stavové služby](service-fabric-reliable-services-lifecycle.md#stateful-service-primary-swaps)

#### <a name="run-replica-role-changes"></a>Spustit změny role repliky
Testy jednotek by měl změnit role instancí služby stejným způsobem jako Orchestrace Service Fabric. Stavový počítač role je uvedeno v následujícím článku:

[Stav Role repliky počítače](service-fabric-concepts-replica-lifecycle.md#replica-role)

Simulace změny role je jedním z více důležitých aspektů testování a můžete odhalit problémy, kde stavu repliky nejsou konzistentní mezi sebou. Replika nekonzistentní stav může nastat kvůli ukládání stavu v paměti v statické nebo proměnné úrovni instance třídy. Příklady to může být tokeny zrušení, výčty a objekty a hodnoty konfigurace. Tím se zajistí, služba je ale současně zachovává tokeny zrušení během RunAsync umožňující změna role dojde k dispozici. Simulace změny role můžete také odhalit problémy, které může dojít, pokud není zapisován kód umožňující vyvolání RunAsync více než jednou.

#### <a name="cancel-cancellation-tokens"></a>Zrušit tokeny zrušení
Existuje by měla existovat testy jednotek, ve kterém je zrušen token zrušení, poskytuje RunAsync. To vám umožní otestovat a ověřit, že je služba řádně ukončena. Během tohoto vypnutí jakékoli dlouho běžícího nebo asynchronní operace by se měla zastavit. Je například dlouhotrvající proces, který může existovat ve službě jednu, která naslouchá zprávám ve frontě spolehlivé. To může existovat přímo v metodě RunAsync nebo vlákna na pozadí. Implementace by měl obsahovat logiku pro ukončení operace, pokud je zrušen token zrušení.

Pokud stavový, který zkontrolujte služby používat mezipaměti nebo v paměti stavu, který by měla existovat jenom na primární, by mělo být uvolněno v tuto chvíli. Tím je zajištěno, že tento stav je konzistentní, pokud se uzel stane primární znovu později. Zrušení testování vám umožní otestovat a ověřit, že tento stav je uvolněn správně.

#### <a name="execute-requests-against-multiple-replicas"></a>Provádění požadavků víc replik
Vyhodnocení testy by se měl spustit stejný požadavek proti jiné repliky. V kombinaci s změny role, mohou být problémy s konzistencí nepokrytý. Test příklad může provádět následující kroky:
1. Provedení požadavku zápisu pro aktuální primární
2. Provést další požadavek, který vrací data zapsaná v kroku 1 proti aktuální primární
3. Zvýšení úrovně sekundárního do primárního. To by také snížit úroveň aktuální primární na sekundární
4. Spusťte stejný požadavek na čtení z kroku 2 pro novou sekundární.

V posledním kroku testu uplatnit data vrácená je konzistentní. Potenciální problém, který může odhalit to je, že data jsou vrácena službou mohou být v paměti, avšak zajištěné nakonec spolehlivé kolekce. Tato data v paměti nemusí se udržovat synchronizované s co ve spolehlivé kolekci existuje.

Data v paměti se obvykle používá k vytvoření sekundární indexy nebo agregace dat, který existuje ve spolehlivé kolekci.

### <a name="asserting"></a>Uplatnění
#### <a name="ensure-responses-match-across-replicas"></a>Ujistěte se, že odpovědi odpovídají napříč repliky
Testy jednotek by měl vyhodnocení, že odpovědi pro daný požadavek je konzistentní napříč víc replik po jejich přechod na primární. To může přinášet potenciální problémy, kde data zadaná v odpovědi není zajištěné spolehlivé kolekce nebo udržovat v paměti bez mechanismu tak synchronizaci dat v replikách. Tím se zajistí, že služba odesílá zpět konzistentní odpovědi po Service Fabric znovu vytvoří rovnováhu nebo převezme služby při selhání na novou primární repliku.

#### <a name="verify-service-respects-cancellation"></a>Ověření služby respektuje zrušení
Dlouho běžící nebo asynchronní procesy, které by měl být ukončen, když je zrušen token zrušení by měl ověřit, že se ve skutečnosti ukončit po zrušení. Tím se zajistí, že bez ohledu na repliku, změna role, procesy, které nejsou určeny běžela na jiné než primární replice zastavit před dokončením přechodu. To také můžete odhalit problémy, kde tento proces blokuje dokončit požadavek role změna nebo vypnutí ze Service Fabric.

#### <a name="verify-which-replicas-should-serve-requests"></a>Ověření, které repliky by měla obsluhovat požadavky
Testy by měly vyhodnocení očekávané chování, pokud požadavek prochází, jiné než primární replikou. Service Fabric umožňují mít sekundární repliky obsluhovat požadavky. Spolehlivé kolekce zápisy může však dojít pouze z primární repliky. Pokud vaše aplikace si klade za cíl pro pouze primární repliky pro požadavků nebo jenom určité podmnožiny požadavky může být zpracována sekundární, by měl testy uplatnit očekávané chování u oba kladné a záporné. Záporná případu se žádost se směruje na repliku, která by nemělo vyřizovat požadavku a pozitivní se opak.

## <a name="next-steps"></a>Další postup
Zjistěte, jak [stavové služby testovací jednotky](service-fabric-how-to-unit-test-stateful-services.md).