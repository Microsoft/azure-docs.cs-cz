---
title: Testování částí stavové služby v Azure Service Fabric
description: Další informace o konceptech a postupech testování částí Service Fabric stavové služby.
ms.topic: conceptual
ms.date: 09/04/2018
ms.openlocfilehash: 12e8a47d9685dee12594f4e2afaa848d9688d185
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "75433912"
---
# <a name="unit-testing-stateful-services-in-service-fabric"></a>Stavové služby testování částí v service fabric

Tento článek popisuje koncepty a postupy testování částí Service Fabric stavové služby. Testování částí v rámci Service Fabric si zaslouží své vlastní aspekty vzhledem k tomu, že kód aplikace aktivně běží v různých kontextech. Tento článek popisuje postupy používané k zajištění kódu aplikace je zahrnuta v rámci každého z různých kontextů, které lze spustit.

## <a name="unit-testing-and-mocking"></a>Testování částí a zesměšňování
Testování částí v kontextu tohoto článku je automatizované testování, které lze provést v rámci testovacího běžce, jako je MSTest nebo NUnit. Testy částí v tomto článku neprovádějí operace proti vzdálenému prostředku, jako je například databáze nebo rozhraní RESTFul API. Tyto vzdálené prostředky by měly být zesměšňovány. Výsměch v kontextu tohoto článku bude falešné, záznam a řízení vrácené hodnoty pro vzdálené prostředky.

### <a name="service-fabric-considerations"></a>Důležité informace o službě Fabric
Testování částí služby Service Fabric stavové služby má několik aspektů. Za prvé, kód služby se spustí na více uzlech, ale pod různými rolemi. Jednotkové testy by měly vyhodnotit kód v rámci každé role k dosažení úplné pokrytí. Různé role by primární, aktivní sekundární, nečinné sekundární a neznámé. Žádná role obvykle nepotřebuje žádné zvláštní pokrytí jako Service Fabric považuje tuto roli za neplatné nebo null služby. Za druhé, každý uzel změní svou roli v daném okamžiku. Chcete-li dosáhnout úplné pokrytí, cesta spuštění kódu by měla být testována se změnami rolí dochází.

## <a name="why-unit-test-stateful-services"></a>Proč testování částí stavové služby? 
Stavové služby testování částí mohou pomoci odhalit některé běžné chyby, které jsou provedeny, které by nemusely být nutně zachyceny konvenční aplikací nebo testováním částí specifické pro doménu. Například pokud stavová služba má jakýkoli stav v paměti, tento typ testování můžete ověřit, že tento stav v paměti je udržována v synchronizaci v rámci každé repliky. Tento typ testování můžete také ověřit, že stavové služby reaguje na tokeny zrušení předaných orchestraci service fabric odpovídajícím způsobem. Při spuštění zrušení služby by měla zastavit všechny dlouho běžící nebo asynchronní operace.  

## <a name="common-practices"></a>Běžné postupy

Následující část poskytuje rady o nejběžnější postupy pro testování částí stavové služby. Také radí, co zesměšňující vrstva by měla mít úzce zarovnat orchestraci service fabric a správu stavu. [ServiceFabric.Mocks](https://www.nuget.org/packages/ServiceFabric.Mocks/) od 3.3.0 nebo novější je jedna taková knihovna, která poskytuje doporučené funkce výsměchu a dodržuje níže uvedené postupy.

### <a name="arrangement"></a>Uspořádání

#### <a name="use-multiple-service-instances"></a>Použití více instancí služby
Testy částí by měly provést více instancí stavové služby. To simuluje, co se skutečně stane v clusteru, kde Service Fabric zřídí více replik, které spouštějí vaši službu v různých uzlech. Každá z těchto instancí bude spuštěna v jiném kontextu. Při spuštění testu by měla být každá instance připravena s konfigurací role očekávanou v clusteru. Například pokud se očekává, že služba má cílovou velikost repliky 3, Service Fabric by zřídit tři repliky na různých uzlech. Jedním z nich je primární a další dva jsou Active Secondary.

Ve většině případů cesta spuštění služby se bude mírně lišit pro každou z těchto rolí. Například pokud služba by neměla přijímat požadavky z aktivní sekundární, služba může mít kontrolu pro tento případ vyvolat zpět informativní výjimku, která označuje, že žádost byla pokus o sekundární. S více instancí umožní tuto situaci, které mají být testovány.

Navíc s více instancí umožňuje testy přepnout role každé z těchto instancí k ověření odpovědi jsou konzistentní i přes změny rolí.

#### <a name="mock-the-state-manager"></a>Zesměšňovat správce státu
Správce stavu by měl být považován za vzdálený prostředek a proto zesměšňován. Při zesměšňování správce stavu, musí být některé základní úložiště v paměti pro sledování toho, co je uloženo do správce stavu tak, aby jej lze číst a ověřovat. Jednoduchý způsob, jak toho dosáhnout, je vytvořit falešné instance každého z typů spolehlivé kolekce. V rámci těchto mocks použijte datový typ, který úzce zarovná operace prováděné proti této kolekci. Následují některé navrhované datové typy pro každou spolehlivou kolekci

- IReliableDictionary<TKey, TValue> -> System.Collections.Concurrent.ConcurrentDictionary<TKey, TValue>
- IReliableQueue\<T> -> System.Collections.Generic.Queue\<T>
- IReliableConcurrentQueue\<T> -> System.Collections.Concurrent.ConcurrentQueue\<T>

#### <a name="many-state-manager-instances-single-storage"></a>Mnoho instancí Správce stavů, jedno úložiště
Jak již bylo zmíněno dříve, správce stavu a spolehlivé kolekce by měly být považovány za vzdálený prostředek. Proto tyto prostředky by měly a budou zesměšňovány v rámci testů částí. Však při spuštění více instancí stavové služby bude výzvou, aby každý zesměšňovaný správce stavu v synchronizaci mezi různými instancemi stavové služby. Pokud je v clusteru spuštěna stavová služba, service fabric se postará o zachování správce stavu jednotlivých sekundárních replik v souladu s primární replikou. Proto testy by se měly chovat stejně tak, aby mohly simulovat změny rolí.

Jednoduchý způsob, jak lze dosáhnout této synchronizace, je použít singleton vzor pro základní objekt, který ukládá data zapsaná do každé spolehlivé kolekce. Například pokud stavová služba používá `IReliableDictionary<string, string>`. Mock správce stavu by měl `IReliableDictionary<string, string>`vrátit mock of . Tento mock může `ConcurrentDictionary<string, string>` použít ke sledování dvojice klíč/hodnota napsané. By `ConcurrentDictionary<string, string>` měl být singleton používá všechny instance správce stavu předán službě.

#### <a name="keep-track-of-cancellation-tokens"></a>Sledování tokenů zrušení
Tokeny zrušení jsou důležitým, ale běžně přehlíženým aspektem stavových služeb. Při service fabric spustí primární repliku pro stavové služby, je k dispozici token zrušení. Tento token zrušení je určen k signalizaci služby, když je odebrána nebo snížena na jinou roli. Stavová služba by měla zastavit všechny dlouho běžící nebo asynchronní operace, aby service fabric mohl dokončit pracovní postup změny role.

Při spuštění testů jednotek by měly být během provádění testu uchovávány všechny tokeny zrušení, které jsou k dispozici pro RunAsync, ChangeRoleAsync, OpenAsync a CloseAsync. Podržení těchto tokenů umožní test simulovat vypnutí služby nebo snížení úrovně a ověřit služba reaguje odpovídajícím způsobem.

#### <a name="test-end-to-end-with-mocked-remote-resources"></a>Testování od konce do konce pomocí zesměšňovaných vzdálených prostředků
Testy částí by měly provést co největší část kódu aplikace, který může změnit stav stavové služby, jak je to možné. Doporučuje se, aby testy byly více end-to-end v přírodě. Jediné falešné servery, které existují, jsou zaznamenání, simulace a/nebo ověření interakcí vzdálených prostředků. To zahrnuje interakce se správcem stavu a spolehlivé kolekce. Následující úryvek je příkladem obruby pro zkoušku, která ukazuje end-to-end testování:

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

Tento test potvrzuje, že data zachycená v jedné replice jsou k dispozici pro sekundární repliku, když je povýšena na primární. Za předpokladu, že spolehlivé kolekce je záložní úložiště pro data zaměstnance, Aa potenciální selhání, `CommitAsync` které by mohly být zachyceny s tímto testem je v případě, že kód aplikace nebyl proveden na transakci uložit nového zaměstnance. V takovém případě by druhá žádost o získání zaměstnanců nevrátila zaměstnance přidaného první žádost.

### <a name="acting"></a>Jednající
#### <a name="mimic-service-fabric-replica-orchestration"></a>Orchestrace replik prostředků na michálku
Při správě více instancí služby by testy měly inicializovat a strhnout tyto služby stejným způsobem jako orchestrace service fabric. Například při vytvoření služby na nové primární repliky, Service Fabric vyvolá CreateServiceReplicaListener, OpenAsync, ChangeRoleAsync a RunAsync. Události životního cyklu jsou popsány v následujících článcích:

- [Spuštění stavové služby](service-fabric-reliable-services-lifecycle.md#stateful-service-startup)
- [Vypnutí stavové služby](service-fabric-reliable-services-lifecycle.md#stateful-service-shutdown)
- [Primární swapy stavové služby](service-fabric-reliable-services-lifecycle.md#stateful-service-primary-swaps)

#### <a name="run-replica-role-changes"></a>Spuštění změn role repliky
Testy částí by měly změnit role instancí služby stejným způsobem jako orchestrace service fabric. Stavový počítač role je popsán v následujícím článku:

[Stavový počítač role repliky](service-fabric-concepts-replica-lifecycle.md#replica-role)

Simulace změny role je jedním z více kritických aspektů testování a může odhalit problémy, kde stav repliky nejsou konzistentní mezi sebou. Nekonzistentní stav repliky může nastat z důvodu ukládání stavu v paměti v proměnné instance statické nebo na úrovni třídy. Příkladem může být zrušení tokeny, výčty a konfigurační objekty/hodnoty. Tím se také zajistí, že služba respektuje tokeny zrušení poskytované během RunAsync, aby bylo možné dojít ke změně role. Simulace změny role může také odhalit problémy, které mohou vzniknout, pokud kód není zapsán a povolit vyvolání RunAsync vícekrát.

#### <a name="cancel-cancellation-tokens"></a>Zrušit tokeny zrušení
Měly by existovat testy jednotek, kde je zrušen token zrušení poskytnutý runasync. To umožní test ověřit, že služba řádně vypne. Během tohoto vypnutí všechny dlouho běžící nebo asynchronní operace by měly být zastaveny. Příkladem dlouho běžícího procesu, který může existovat ve službě, je ten, který naslouchá zprávám ve spolehlivé frontě. To může existovat přímo v rámci RunAsync nebo podprocesna na pozadí. Implementace by měla zahrnovat logiku pro ukončení operace, pokud je tento token zrušení zrušen.

Pokud stavové služby využít jakékoli mezipaměti nebo stavu v paměti, který by měl existovat pouze na primární, by měl být uvolněn v tomto okamžiku. Tím je zajištěno, že tento stav je konzistentní, pokud uzel stane primární znovu později. Testování zrušení umožní test ověřit tento stav je uvolněn správně.

#### <a name="execute-requests-against-multiple-replicas"></a>Provádění požadavků proti více replikám
Testy uplatnění by měly provést stejný požadavek proti různých replik. Při spárování se změnami rolí lze odkonto odhalit problémy konzistence. Ukázkový test může provést následující kroky:
1. Provedení požadavku na zápis proti aktuálníprimární
2. Spusťte požadavek na čtení, který vrátí data zapsaná v kroku 1 proti aktuální primární
3. Propagujte sekundární na primární. To by mělo také snížit současnou primární na sekundární
4. Proveďte stejný požadavek na čtení z kroku 2 proti nové sekundární.

V posledním kroku test můžete potvrdit, že vrácená data je konzistentní. Potenciální problém, který by to mohlo odhalit, je, že data vrácená službou mohou být v paměti, ale nakonec podpořena spolehlivou kolekcí. Data v paměti nemusí být správně synchronizována s tím, co existuje ve spolehlivé kolekci.

Data v paměti se obvykle používá k vytvoření sekundární indexy nebo agregace dat, která existuje ve spolehlivé kolekci.

### <a name="asserting"></a>Uplatňování
#### <a name="ensure-responses-match-across-replicas"></a>Zajistit, aby se odpovědi shodovaly mezi replikami
Testy částí by měly tvrdit, že odpověď pro daný požadavek je konzistentní napříč více replikami po jejich přechodu na primární. To může povrch potenciální problémy, kde data uvedená v odpovědi buď není zálohována spolehlivé kolekce nebo uchovávány v paměti bez mechanismu pro synchronizaci dat mezi replikami. Tím zajistíte, že služba odešle zpět konzistentní odpovědi po Service Fabric znovu vyvažuje nebo převzetí služeb při selhání na novou primární repliku.

#### <a name="verify-service-respects-cancellation"></a>Ověření zrušení služby respektuje
Dlouhotrvající nebo asynchronní procesy, které by měly být ukončeny při zrušení tokenu zrušení by měly být ověřeny, že jsou skutečně ukončeny po zrušení. Tím zajistíte, že i přes změny rolí repliky procesy, které nejsou určeny k tomu, aby byly spuštěny na neprimární replice zastavit před dokončením přechodu. To může také odhalit problémy, kde takový proces blokuje změnu role nebo požadavek na vypnutí z Service Fabric z dokončení.

#### <a name="verify-which-replicas-should-serve-requests"></a>Ověření, které repliky by měly být obsluhována požadavky
Testy by měly uplatnit očekávané chování, pokud je požadavek směrován do neprimární repliky. Service Fabric poskytuje možnost mít sekundární repliky obsluhovat požadavky. Však zápisy do spolehlivé kolekce může dojít pouze z primární repliky. Pokud vaše aplikace má v úmyslu pouze primární repliky sloužit požadavky nebo pouze podmnožinu požadavků mohou být zpracovány sekundární, testy by měly uplatnit očekávané chování pro pozitivní i negativní případy. Záporný případ je požadavek je směrován do repliky, která by neměla zpracovávat požadavek a pozitivní je opak.

## <a name="next-steps"></a>Další kroky
Přečtěte si, jak [otestovat stavové služby](service-fabric-how-to-unit-test-stateful-services.md).
