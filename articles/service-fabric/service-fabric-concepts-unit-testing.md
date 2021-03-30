---
title: Stavové služby testování částí v Azure Service Fabric
description: Přečtěte si o konceptech a postupech testování částí Service Fabric stavových služeb.
ms.topic: conceptual
ms.date: 09/04/2018
ms.openlocfilehash: 12e8a47d9685dee12594f4e2afaa848d9688d185
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/29/2021
ms.locfileid: "75433912"
---
# <a name="unit-testing-stateful-services-in-service-fabric"></a>Testování částí stavových služeb v Service Fabric

Tento článek popisuje koncepty a postupy testování částí Service Fabric stavových služeb. Testování částí v rámci Service Fabric zachovává své vlastní okolnosti z důvodu faktu, že se kód aplikace aktivně spouští v několika různých kontextech. Tento článek popisuje postupy, které slouží k zajištění toho, aby se kód aplikace kryl pod každým z různých kontextů, které může spustit.

## <a name="unit-testing-and-mocking"></a>Testování částí a napodobování
Testování částí v kontextu tohoto článku je automatizované testování, které lze provést v rámci kontextu spouštěče testů, jako je například MSTest nebo NUnit. Testování částí v rámci tohoto článku neprovádí operace se vzdáleným prostředkem, jako je například databáze nebo rozhraní RESTFul API. Tyto vzdálené prostředky by měly být navázány. Napodobování v kontextu tohoto článku bude nafalešné, zaznamenání a řízení vrácených hodnot pro vzdálené prostředky.

### <a name="service-fabric-considerations"></a>Service Fabric hlediska
Testování částí Service Fabric stavová služba má několik důležitých informací. Za prvé se kód služby spustí na více uzlech, ale v rámci různých rolí. Testy jednotek by měly vyhodnotit kód v rámci každé role, abyste dosáhli úplného pokrytí. Různé role by byly primární, aktivní sekundární, nečinný sekundární a neznámá. Role None většinou nevyžaduje žádné zvláštní pokrytí, protože Service Fabric považuje tuto roli za službu typu void nebo null. V druhé době každý uzel změní svou roli v jakémkoli daném bodě. Pro dosažení kompletního pokrytí by měla být testována cesta spuštění kódu se změnami rolí, ke kterým dochází.

## <a name="why-unit-test-stateful-services"></a>Proč stavové služby testu jednotek? 
Stavové služby testování částí můžou pokrývat některé běžné chyby, které se nemusely zachytit konvenční aplikací nebo testováním jednotek specifických pro doménu. Pokud má například stavová služba nějaký stav v paměti, tento typ testování může ověřit, zda je stav v paměti udržován v synchronizaci napříč každou replikou. Tento typ testování může také ověřit, zda stavová služba reaguje na tokeny zrušení předané v Service Fabric orchestrace odpovídajícím způsobem. Při aktivaci zrušení by služba měla zastavit všechny dlouhotrvající nebo asynchronní operace.  

## <a name="common-practices"></a>Běžné postupy

Následující část upozorňuje na nejběžnější postupy testování částí stavové služby. Také radí, co by měla napodobná vrstva odpovídat Service Fabric Orchestrace a správy stavů. [ServiceFabric.](https://www.nuget.org/packages/ServiceFabric.Mocks/) 3.3.0s nebo novější představuje jednu takovou knihovnu, která poskytuje doporučené funkce napodobování a postupuje podle postupů uvedených níže.

### <a name="arrangement"></a>Úprava

#### <a name="use-multiple-service-instances"></a>Použití více instancí služby
Testy jednotek by měly spouštět více instancí stavové služby. To simuluje to, co se ve clusteru skutečně stane, když Service Fabric zřídí více replik, které spouští vaši službu v různých uzlech. Každá z těchto instancí bude nicméně vykonávána v jiném kontextu. Při spuštění testu by se měla každá instance provést s očekávanou konfigurací role v clusteru. Pokud má například služba očekávat velikost cílové repliky 3, Service Fabric by zřídila tři repliky na různých uzlech. Jedna z nich je primární a druhá druhá jako aktivní sekundární.

Ve většině případů se cesta spuštění služby bude mírně lišit pro každou z těchto rolí. Pokud například služba by neměla přijímat požadavky z aktivní sekundární služby, může být tato služba zkontrolována, aby vrátila zpět informativní výjimku, která indikuje, že byl požadavek proveden na sekundárním objektu. U více instancí bude možné tuto situaci otestovat.

Kromě toho více instancí umožňuje testům přepínat role každé z těchto instancí, aby bylo možné ověřit, jestli jsou odpovědi konzistentní Navzdory změnám role.

#### <a name="mock-the-state-manager"></a>Napodobení manažera stavu
Správce stavu by měl být považován za vzdálený prostředek, a proto je vypsaný. Při napodobování manažera stavu musí být k dispozici určité základní úložiště v paměti pro sledování, co je uloženo do Správce stavu, aby bylo možné ho přečíst a ověřit. Jednoduchým způsobem, jak toho dosáhnout, je vytvořit maketu instancí každého z typů spolehlivých kolekcí. V rámci těchto napodobenin použijte datový typ, který úzce zarovnává s operacemi provedenými proti této kolekci. Následuje několik navrhovaných typů dat pro každou spolehlivou kolekci.

- IReliableDictionary<TKey, TValue>-> System. Collections. současných ConcurrentDictionary<TKey, TValue>
- IReliableQueue \<T> -> System. Collections. Generic. Queue\<T>
- IReliableConcurrentQueue \<T> -> System. Collections. současných. ConcurrentQueue\<T>

#### <a name="many-state-manager-instances-single-storage"></a>Mnoho instancí správce stavu, jednotné úložiště
Jak už bylo zmíněno dříve, správce stavu a spolehlivé kolekce by se měly považovat za vzdálený prostředek. Proto by se tyto prostředky měly a nacházet v rámci testů jednotek. Pokud ale spustíte více instancí stavové služby, bude se jednat o výzvu k tomu, aby byl každý nadaný správce stavu synchronizovaný napříč různými instancemi stavové služby. Když je v clusteru spuštěná stavová služba, Service Fabric se postará o udržování správce stavu jednotlivých sekundárních replik v souladu s primární replikou. Proto by testy měly fungovat stejně, aby mohly simulovat změny rolí.

Jednoduchou způsob, jak tuto synchronizaci dosáhnout, je použít vzor singleton pro základní objekt, který ukládá data zapsaná do každé spolehlivé kolekce. Například pokud stavová služba používá `IReliableDictionary<string, string>` . Správce státních stavů by měl vrátit objekt typu `IReliableDictionary<string, string>` . Tento druh může použít `ConcurrentDictionary<string, string>` k udržení přehledu o zapsaných páru klíč/hodnota. `ConcurrentDictionary<string, string>`By měl být typu Singleton používaného všemi instancemi správců stavu předaných službě.

#### <a name="keep-track-of-cancellation-tokens"></a>Sledovat tokeny zrušení
Tokeny zrušení jsou důležité, ale obecně se jedná o aspekty stavových služeb. Když Service Fabric spouští primární repliku stavové služby, je k dispozici token zrušení. Tento token zrušení je určen k signalizaci službě při jejím odebrání nebo snížení úrovně na jinou roli. Stavová služba by měla zastavit všechny dlouhotrvající nebo asynchronní operace, aby Service Fabric mohl dokončit pracovní postup změny role.

Při spuštění testů jednotek by měly být všechny tokeny zrušení, které jsou k dispozici pro RunAsync, ChangeRoleAsync, OpenAsync a CloseAsync, uloženy během provádění testu. Držení na tyto tokeny umožní testu simulovat vypnutí služby nebo snížení úrovně a ověřit, jestli služba reaguje správně.

#### <a name="test-end-to-end-with-mocked-remote-resources"></a>Kompletní test s použitím vydaných vzdálených prostředků
Testy jednotek by se měly spouštět co nejvíc kódu aplikace, který může upravit stav stavové služby, jak je možné. Doporučuje se, aby testy byly v podstatě ucelené. Pouze ty, které existují, slouží k zaznamenávání, simulaci a ověření interakce vzdálených prostředků. To zahrnuje interakce se správcem stavu a spolehlivými kolekcemi. Následující fragment kódu je příkladem Gherkin pro test, který předvádí kompletní testování:

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

Tento test vyhodnotí, že data zaznamenaná na jedné replice jsou dostupná pro sekundární repliku, když je povýšen na primární. Za předpokladu, že spolehlivá kolekce je záložním úložištěm pro údaje o zaměstnancích, v případě, že se kód aplikace nespustí `CommitAsync` v transakci k uložení nového zaměstnance, je možné, že se v tomto testu nepoužila chyba. V takovém případě druhý požadavek na získání zaměstnanců nevrátí zaměstnance přidaný prvním požadavkem.

### <a name="acting"></a>Slouží
#### <a name="mimic-service-fabric-replica-orchestration"></a>Napodobení Service Fabric orchestrace replik
Při správě více instancí služby by testy měly inicializovat a odtrhnout tyto služby stejným způsobem jako orchestrace Service Fabric. Pokud je třeba služba vytvořena na nové primární replice, Service Fabric vyvolá CreateServiceReplicaListener, OpenAsync, ChangeRoleAsync a RunAsync. Události životního cyklu jsou popsány v následujících článcích:

- [Spuštění stavové služby](service-fabric-reliable-services-lifecycle.md#stateful-service-startup)
- [Vypnutí stavové služby](service-fabric-reliable-services-lifecycle.md#stateful-service-shutdown)
- [Primární swapy stavové služby](service-fabric-reliable-services-lifecycle.md#stateful-service-primary-swaps)

#### <a name="run-replica-role-changes"></a>Spustit změny role repliky
Testy jednotek by měly změnit role instancí služby stejným způsobem jako orchestrace Service Fabric. Stavový počítač role je popsán v následujícím článku:

[Stavový počítač role repliky](service-fabric-concepts-replica-lifecycle.md#replica-role)

Simulace změn rolí je jedním z nejdůležitějších aspektů testování a může odhalit problémy, kdy stav repliky nejsou vzájemně konzistentní. K nekonzistentnímu stavu repliky může dojít z důvodu uložení stavu v paměti ve statických proměnných nebo proměnných instancí na úrovni třídy. Příkladem může být tokeny zrušení, výčty a konfigurační objekty nebo hodnoty. Tím se také zajistí, že služba respektuje tokeny zrušení poskytnuté během RunAsync, aby došlo ke změně role. Simulace změn rolí může také odhalit problémy, které mohou nastat, pokud kód není napsán, aby bylo možné vyvolat RunAsync vícekrát.

#### <a name="cancel-cancellation-tokens"></a>Zrušit tokeny zrušení
Existují testy jednotek, kde je zrušen token zrušení zadaný do RunAsync. To umožní testu ověřit, zda se služba řádně ukončí. Během tohoto vypnutí je třeba zastavit všechny dlouhotrvající nebo asynchronní operace. Příkladem dlouhého běžícího procesu, který může existovat ve službě, je ten, který naslouchá zprávám ve spolehlivé frontě. To může existovat přímo v RunAsync nebo vlákně na pozadí. Implementace by měla obsahovat logiku pro ukončení operace, pokud je tento token zrušení zrušen.

Pokud stavové služby využívají jakoukoli mezipaměť nebo stav v paměti, které by měly existovat pouze na primárním uzlu, měla by být v tuto chvíli uvolněna. To zajistí, že tento stav bude konzistentní, pokud se uzel později znovu vytvoří jako primární. Testování zrušení umožní testu ověřit, zda je tento stav odstraněn správně.

#### <a name="execute-requests-against-multiple-replicas"></a>Spouštění požadavků proti několika replikám
Testy kontrolního výrazu by měly provádět stejnou žádost vůči jiné replice. Při párování se změnami role je možné zjistit problémy konzistence. Ukázkový test může provést následující kroky:
1. Provést požadavek na zápis proti aktuálnímu primárnímu poli
2. Provede požadavek na čtení, který vrátí data zapsaná v kroku 1 proti aktuálnímu primárnímu
3. Zvyšte úroveň sekundární na primární. Mělo by se taky snížit úroveň aktuální primární na sekundární.
4. Spustí stejnou žádost o čtení z kroku 2 proti novému sekundárnímu.

V posledním kroku test může uplatnit, že vrácená data jsou konzistentní. Možnou příčinou může být to, že data vracená službou můžou být v paměti, ale nakonec se zálohuje spolehlivou kolekcí. Tato data v paměti nemusí být správně udržována, pokud existují ve spolehlivé kolekci.

Data v paměti se obvykle používají k vytváření sekundárních indexů nebo agregací dat, která existují ve spolehlivé kolekci.

### <a name="asserting"></a>Vydává
#### <a name="ensure-responses-match-across-replicas"></a>Zajištění shody odpovědí napříč replikami
Testy jednotek by měly vyhodnotit, že odpověď pro daný požadavek je konzistentní v rámci několika replik po přechodu na primární. To může mít za následek potenciální problémy, kdy data poskytnutá v odpovědi nejsou založená na spolehlivé kolekci nebo jsou držená v paměti bez mechanismu pro synchronizaci dat mezi replikami. Tím se zajistí, že služba pošle zpátky konzistentní odpovědi po Service Fabric rebilanci nebo převzetí služeb při selhání novou primární replikou.

#### <a name="verify-service-respects-cancellation"></a>Ověřit zrušení ctí služby
Dlouhotrvající nebo asynchronní procesy, které by měly být ukončeny, pokud je zrušen zrušení tokenu zrušení, by mělo být ověřeno, že jsou ve skutečnosti ukončeny po zrušení. Tím se zajistí, že navzdory změnám rolí, které nejsou určené k tomu, aby zůstaly spuštěné na neprimární replice před dokončením přechodu. To může také odhalit problémy, při kterých takový proces blokuje změnu role nebo žádost o vypnutí z Service Fabric dokončení.

#### <a name="verify-which-replicas-should-serve-requests"></a>Ověření, které repliky by měly obsluhovat požadavky
Testy by měly vyhodnotit očekávané chování, je-li požadavek směrován do jiné než primární repliky. Service Fabric poskytuje možnost mít sekundární repliky, které obsluhují požadavky. Zápisy do spolehlivých kolekcí se ale můžou vyskytovat jenom z primární repliky. Pokud vaše aplikace považuje pouze primární repliky na obsluhu požadavků nebo, může být pouze podmnožina požadavků zpracována sekundárním objektem, testy by měly vyhodnotit očekávané chování jak pro kladné, tak i pro negativní případy. Negativní případ, kdy je požadavek směrován do repliky, která by neměla požadavek zpracovávat, a kladné, že se jedná o opak.

## <a name="next-steps"></a>Další kroky
Naučte se, jak [jednotkově testovat stavové služby](service-fabric-how-to-unit-test-stateful-services.md).
