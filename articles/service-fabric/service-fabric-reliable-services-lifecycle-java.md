---
title: Azure Service Fabric spolehlivé služby životního cyklu | Microsoft Docs
description: Další informace o události životního cyklu v Service Fabric spolehlivé služby.
services: service-fabric
documentationcenter: java
author: PavanKunapareddyMSFT
manager: timlt
ms.assetid: ''
ms.service: service-fabric
ms.devlang: java
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 06/30/2017
ms.author: pakunapa
ms.openlocfilehash: cc89d174a201b38d79c7993d548c8eac4a47fbcb
ms.sourcegitcommit: eb75f177fc59d90b1b667afcfe64ac51936e2638
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/16/2018
ms.locfileid: "34210677"
---
# <a name="reliable-services-lifecycle"></a>Životní cyklus Reliable Services
> [!div class="op_single_selector"]
> * [C# v systému Windows](service-fabric-reliable-services-lifecycle.md)
> * [Java v Linuxu](service-fabric-reliable-services-lifecycle-java.md)
>
>

Spolehlivé služby je programovací modely k dispozici v Azure Service Fabric. Při získávání informací o životním cyklu spolehlivé služby, je nejdůležitější, aby pochopili události základní životního cyklu. Seřazení událostí závisí na podrobnosti o konfiguraci. 

Obecně platí životní cyklus spolehlivé služby zahrnuje následující události:

* Při spuštění:
  * Služby se vytvářejí.
  * Služby mají možnost pro vytvoření a návrat nula nebo více naslouchací procesy.
  * Všechny vrácený naslouchací procesy jsou otevřené pro komunikaci se službou.
  * Služby `runAsync` metoda je volána, tak službu můžete provést dlouho běžící nebo práce na pozadí.
* Při vypnutí:
  * Token zrušení, který byl předán `runAsync` se zruší, a naslouchací procesy jsou uzavřeny.
  * Je destructed samotného objektu služby.

Pořadí událostí v spolehlivé služby může v závislosti na tom, jestli je spolehlivá služba bezstavové nebo stavová mírně změnit. 

Navíc pro stavové služby, musíte vyřešit, scénář primárních prohození. Během tohoto pořadí roli primární se přenese do jiné repliky (nebo, je vrácen) bez vypnutí služby. 

Nakonec budete muset vezměte v úvahu podmínek chyb nebo selhání.

## <a name="stateless-service-startup"></a>Spuštění bezstavové služby
Životní cyklus bezstavové služby je poměrně jednoduché. Tady je pořadí událostí:

1. Služba je vytvořená.
2. K těmto událostem dojde současně:
    - `StatelessService.createServiceInstanceListeners()` je volána, a všechny vrátí, moduly pro naslouchání jsou otevřené. `CommunicationListener.openAsync()` je volána v každé naslouchací proces.
    - Služby `runAsync` – metoda (`StatelessService.runAsync()`) je volána.
3. Pokud je k dispozici, služby vlastní `onOpenAsync` metoda je volána. Konkrétně `StatelessService.onOpenAsync()` je volána. Toto je neobvyklé přepsání, ale je k dispozici.

Je důležité si uvědomit, že neexistuje žádná řazení mezi volání vytvoří a otevře naslouchací procesy a volání `runAsync`. Před může otevřít posluchače `runAsync` je spuštěná. Podobně `runAsync` může být volána před naslouchací procesy komunikace jsou otevřené nebo před i byla vytvořená. Pokud žádnou synchronizaci se požaduje, musí být provádí implementátor. Tady jsou některá běžná řešení:

* Někdy naslouchací procesy nemůže fungovat, dokud se vytvoří další informace, nebo se provádí jinou práci. Pro bezstavové služby, které pracovní lze provést obvykle v konstruktoru služby. Je možné ji provést během `createServiceInstanceListeners()` volat, nebo jako součást konstrukce naslouchací proces sám sebe.
* Někdy kód v `runAsync` nespustí, dokud nebude posluchače jsou otevřené. V takovém případě je nutné další spolupráce. Běžné řešení je přidání příznak v posluchače. Příznak určuje, po dokončení naslouchací procesy. `runAsync` Tato metoda ověří před pokračováním samotnou práci.

## <a name="stateless-service-shutdown"></a>Vypnutí bezstavové služby
Při vypínání bezstavové služby, je stejného vzoru nakonec, ale obráceným:

1. K těmto událostem dojde současně:
    - Jsou zavřeny všechny otevřené naslouchací procesy. `CommunicationListener.closeAsync()` je volána v každé naslouchací proces.
    - Token zrušení, který byl předán `runAsync()` je zrušená. Kontrola token zrušení `isCancelled` vlastnost vrátí `true`a pokud je volána, je token `throwIfCancellationRequested` metoda vrátí `CancellationException`.
2. Když `closeAsync()` skončí u každé naslouchací proces a `runAsync()` také dokončení služby `StatelessService.onCloseAsync()` metoda je volána, pokud je k dispozici. Znovu nejedná se o běžné přepsání, ale lze bezpečně zavřít prostředky, zastavit zpracování na pozadí, dokončení ukládání externí stavu nebo ukončete stávající připojení.
3. Po `StatelessService.onCloseAsync()` je destructed dokončení objektu služby.

## <a name="stateful-service-startup"></a>Spuštění stavové služby
Stavová služba mají vzor, který je podobný bezstavové služby s několik změn.  Tady je pořadí událostí pro spuštění stavové služby:

1. Služba je vytvořená.
2. `StatefulServiceBase.onOpenAsync()` je volána. Toto volání není ve službě běžně přepsat.
3. K těmto událostem dojde současně:
    - `StatefulServiceBase.createServiceReplicaListeners()` je volána. 
      - Pokud služba je primární služba, jsou otevřené všechny vrácený naslouchací procesy. `CommunicationListener.openAsync()` je volána v každé naslouchací proces.
      - Pokud služba je sekundární službu, pouze moduly pro naslouchání označen jako `listenOnSecondary = true` jsou otevřené. Naslouchací procesy, které jsou otevřené na sekundárních hodnot je méně častých.
    - Pokud služba je aktuálně primárního serveru, službu na `StatefulServiceBase.runAsync()` metoda je volána.
4. Po všechny repliky naslouchací proces pro `openAsync()` volá dokončit a `runAsync()` je volána, `StatefulServiceBase.onChangeRoleAsync()` je volána. Toto volání není ve službě běžně přepsat.

Podobně jako bezstavové služby v stavové služby, je zde žádné koordinovat pořadí, ve kterém se vytváří a otevřít naslouchací procesy a kdy `runAsync` je volána. Pokud potřebujete koordinaci, řešení jsou podobné. Ale jeden další případ pro stavové služby. Řekněme, že volání, které přicházejí na naslouchací procesy komunikace vyžadovat informace uložené v produktu některé [spolehlivé kolekce](service-fabric-reliable-services-reliable-collections.md). Protože naslouchací procesy komunikace může otevřít před spolehlivé kolekce jsou z něj číst nebo zapisovat a před `runAsync` spustí, je nutné některé další spolupráce. Nejjednodušší a nejběžnější řešení je pro naslouchací procesy komunikace vrátit chybový kód. Klient použije kód chyby vědět, opakujte žádost.

## <a name="stateful-service-shutdown"></a>Vypnutí stavové služby
Podobně jako bezstavové služby události životního cyklu během vypnutí jsou stejné jako při spuštění, ale vrátit zpět. Když se právě vypíná stavové služby, dojde k následujícím událostem:

1. K těmto událostem dojde současně:
    - Jsou zavřeny všechny otevřené naslouchací procesy. `CommunicationListener.closeAsync()` je volána v každé naslouchací proces.
    - Token zrušení, který byl předán `runAsync()` byla zrušena. Token zrušení volání `isCancelled()` metoda vrátí `true`a pokud je volána, je token `throwIfCancellationRequested()` metoda vrátí `OperationCanceledException`.
2. Po `closeAsync()` skončí u každé naslouchací proces a `runAsync()` také dokončení služby `StatefulServiceBase.onChangeRoleAsync()` je volána. Toto volání není ve službě běžně přepsat.

   > [!NOTE]  
   > Čekání na `runAsync` ukončíte je nutné pouze v případě, že tato replika je primární repliky.

3. Po `StatefulServiceBase.onChangeRoleAsync()` metoda skončí, `StatefulServiceBase.onCloseAsync()` metoda je volána. Toto volání je neobvyklé přepsání, ale je k dispozici.
3. Po `StatefulServiceBase.onCloseAsync()` je destructed dokončení objektu služby.

## <a name="stateful-service-primary-swaps"></a>Primární záměna stavové služby
Když je spuštěný stavové služby, jsou otevřené naslouchací procesy komunikace a `runAsync` metoda je volána pouze pro primární repliky této stavové služby. Sekundární repliky se vytvářejí, ale zobrazit žádná další volání. Při spuštěné stavové služby, můžete repliky, který je aktuálně primární změnit. Události životního cyklu, které můžete zobrazit stavové repliky závisí na tom, jestli je replika se degradaci nebo povýší během prohození.

### <a name="for-the-demoted-primary"></a>Pro sníženou úrovní primární
Service Fabric musí primární repliku, která je k degradaci zastavit zpracování zpráv a zastavte všechny práce na pozadí. Tento krok je podobný při vypnutí služby. Jedním rozdílem je, že služba není destructed nebo zavřená, protože zůstane jako sekundární. Dojde k následující chybě:

1. K těmto událostem dojde současně:
    - Jsou zavřeny všechny otevřené naslouchací procesy. `CommunicationListener.closeAsync()` je volána v každé naslouchací proces.
    - Token zrušení, který byl předán `runAsync()` je zrušená. Kontrolu token zrušení `isCancelled()` metoda vrátí `true`. Pokud je volána, je token `throwIfCancellationRequested()` metoda vrátí `OperationCanceledException`.
2. Po `closeAsync()` skončí u každé naslouchací proces a `runAsync()` také dokončení služby `StatefulServiceBase.onChangeRoleAsync()` je volána. Toto volání není ve službě běžně přepsat.

### <a name="for-the-promoted-secondary"></a>Pro propagovaných sekundární
Podobně Service Fabric musí sekundární repliky, která se vyzval zahájit naslouchání pro zprávy v drátové síti a ke spuštění úlohy na pozadí, které potřebuje k dokončení. Tento proces je podobný při vytvoření služby. Rozdílem je, že replika samotné již existuje. Dojde k následující chybě:

1. K těmto událostem dojde současně:
    - `StatefulServiceBase.createServiceReplicaListeners()` je voláno, a všechny vrátí, moduly pro naslouchání jsou otevřené. `CommunicationListener.openAsync()` je volána v každé naslouchací proces.
    - Služby `StatefulServiceBase.runAsync()` metoda je volána.
2. Po všechny repliky naslouchací proces pro `openAsync()` volá dokončit a `runAsync()` je volána, `StatefulServiceBase.onChangeRoleAsync()` je volána. Toto volání není ve službě běžně přepsat.

### <a name="common-issues-during-stateful-service-shutdown-and-primary-demotion"></a>Běžné problémy při vypnutí stavové služby a primární snížení úrovně
Service Fabric změní primární stavové služby z několika důvodů. Jsou nejčastější příčiny [clusteru vyrovnává](service-fabric-cluster-resource-manager-balancing.md) a [upgradu aplikace](service-fabric-application-upgrade.md). Během těchto operací, je důležité, aby službu respektuje `cancellationToken`. To platí také během vypnutí normální služby, jako třeba když služba je Odstraněná.

Služby, které není řádně zpracovávat zrušení může mít několik problémy. Tyto operace jsou pomalé, protože Service Fabric čeká na služby, aby řádně ukončena. To může nakonec vést k selhání upgrady tento časový limit a vrácení zpět. Selhání respektovat token zrušení může také způsobit imbalanced clustery. Clustery stát nevyváženou, protože uzly získat aktivní. Ale služby nemůže být znovu vyrovnána protože trvá příliš dlouho přesunout na jiné místo. 

Protože se stavové služby, je také pravděpodobné, že služby používají [spolehlivé kolekce](service-fabric-reliable-services-reliable-collections.md). V Service Fabric při snížení úrovně primární jeden z nejdůležitějších věcí, které se stane, se zruší se oprávnění k zápisu do základní stav. To vede ke druhé sadě problémy, které mohou ovlivnit služby životního cyklu. Kolekce návratový výjimky založené na načasování a jestli je přesouvání repliky nebo vypnutí. Je důležité tyto výjimky správně zpracovat. 

Výjimky vyvolané Service Fabric se buď trvalé [(`FabricException`)](https://docs.microsoft.com/java/api/system.fabric.exception) nebo přechodný [(`FabricTransientException`)](https://docs.microsoft.com/java/api/system.fabric.exception._fabric_transient_exception). Trvalé výjimky by měl zaznamenána a vyvolána. Přechodný výjimky opakovat lze na základě logiky opakování.

Důležitou součástí testování a ověření spolehlivé služby je zpracování výjimek, které pocházejí z pomocí `ReliableCollections` ve spojení s událostmi služby životního cyklu. Doporučujeme vždy spustit služby zatížení. Je také vhodné provést upgrady a [chaos testování](service-fabric-controlled-chaos.md) před nasazením do produkčního prostředí. Tyto základní postup pomůže, ujistěte se, že služby je implementovaná správně a zda je správně zpracovává události životního cyklu.

## <a name="notes-on-service-lifecycle"></a>Poznámky k služby životního cyklu
* Obě `runAsync()` metoda a `createServiceInstanceListeners/createServiceReplicaListeners` volání jsou volitelné. Služba může mít jeden, oba, nebo žádný z nich. Například pokud je služba svou práci na základě výzvy pro uživatele, není třeba pro něj k implementaci `runAsync()`. Pouze naslouchací procesy komunikace a jejich přidružené kód jsou nezbytné.  Podobně vytváření a vrácení naslouchací procesy komunikace je volitelné. Službu může mít pouze pozadí činnost, proto je nutné implementovat `runAsync()`.
* Je platná pro službu k dokončení `runAsync()` úspěšně a zpět z něj. Toto není považováno za selhání podmínku. Reprezentuje práce na pozadí ukončení služby. Pro stavové služby spolehlivé `runAsync()` být volán znovu, pokud je služba degradaci z primární a poté vyzval zpět na primární.
* Pokud služba ukončí z `runAsync()` po vyvolání výjimky neočekávané výjimce, jedná se o chybu. Objekt služby je vypnutý, a hlášení stavu chyby.
* I když není žádný časový limit na vrácení z těchto metod, ztratíte okamžitě možnost zapisovat. Žádné skutečné práci proto nelze dokončit. Doporučujeme vám, že vrátit co nejrychleji po přijetí žádost o zrušení. Pokud vaše služba nereaguje na těchto volání rozhraní API v přiměřené době, Service Fabric může vynucené ukončení služby. Obvykle k tomu dojde pouze během upgradu aplikace nebo když se odstraňuje služby. Tento časový limit je 15 minut ve výchozím nastavení.
* Selhání v `onCloseAsync()` cesta povede `onAbort()` volána. Toto volání je poslední chance, typu best effort možnost pro službu vyčištění a uvolnit všechny prostředky, které budou požadovaly. Obecně se používá při zjištění trvalé selhání na uzlu, nebo když Service Fabric se nedají spravovat spolehlivě životního cyklu instance služby z důvodu vnitřní chyby.
* `OnChangeRoleAsync()` je volána, když replika stavové služby je například změna role na primární nebo sekundární. Primární repliky mají stav zápisu (je povoleno vytvoření a zápis do spolehlivé kolekcí). Sekundární repliky jsou uvedeny čtení stav (můžete číst jenom z existující spolehlivé kolekce). Většinu práce v stavové služby se provádí na primární replice. Sekundární repliky lze provést ověření jen pro čtení, generování sestav, dolování dat nebo jiné úlohy jen pro čtení.

## <a name="next-steps"></a>Další postup
* [Úvod do spolehlivé služby](service-fabric-reliable-services-introduction.md)
* [Spolehlivé služby rychlý start](service-fabric-reliable-services-quick-start-java.md)

