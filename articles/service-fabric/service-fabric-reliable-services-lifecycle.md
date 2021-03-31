---
title: Přehled životního cyklu Reliable Services
description: Přečtěte si informace o událostech životního cyklu v aplikaci Azure Service Fabric Reliable Services pro stavové a bezstavové služby.
author: masnider
ms.topic: conceptual
ms.date: 08/18/2017
ms.author: masnider
ms.openlocfilehash: 6ea8fa6933052374721d8d205d5b07386c807ae2
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/29/2021
ms.locfileid: "98784592"
---
# <a name="reliable-services-lifecycle-overview"></a>Přehled životního cyklu Reliable Services
> [!div class="op_single_selector"]
> * [C# v systému Windows](service-fabric-reliable-services-lifecycle.md)
> * [Java v Linuxu](service-fabric-reliable-services-lifecycle-java.md)
>
>

Když uvažujete o životní cyklus služby Azure Service Fabric Reliable Services, nejdůležitější jsou základy životního cyklu. Obecně platí, že životní cyklus zahrnuje následující:

- Při spuštění:
  - Služby jsou konstruovány.
  - Služby mají příležitost vytvořit a vrátit nula nebo více posluchačů.
  - Všechny vrácené naslouchací procesy jsou otevřené a umožňují komunikaci se službou.
  - Volá se metoda **RunAsync** služby, která umožňuje, aby služba běžela dlouhotrvající úkoly nebo práce na pozadí.
- Během vypnutí:
  - Token zrušení předaný metodě **RunAsync** je zrušen a naslouchací procesy jsou zavřeny.
  - Po zavření naslouchacího procesu posluchače dojde k destrukturování samotného objektu služby.

Existují podrobnosti kolem přesného pořadí těchto událostí. Pořadí událostí se může mírně měnit v závislosti na tom, jestli je spolehlivá služba Bezstavová nebo stavová. U stavových služeb je navíc nutné se zabývat primárním scénářem přepnutí. Během této sekvence se role primární přenáší do jiné repliky (nebo se vrátí zpátky) bez vypnutí služby. Nakonec se musíme domnívat, že se jedná o chybové nebo chybné podmínky.

## <a name="stateless-service-startup"></a>Spuštění bezstavové služby
Životní cyklus bezstavové služby je jednoduchý. Tady je pořadí událostí:

1. Služba je vytvořena.
2. Pak dojde k paralelnímu zpracování dvou věcí:
    - `StatelessService.CreateServiceInstanceListeners()` je vyvolána a jsou otevřeny všechny vracené naslouchací procesy. `ICommunicationListener.OpenAsync()` je volána u každého naslouchacího procesu.
    - `StatelessService.RunAsync()`Volá se metoda služby.
3. Je-li k dispozici, `StatelessService.OnOpenAsync()` je volána metoda služby. Toto volání je Neběžné přepsání, ale je k dispozici. V tuto chvíli můžete spustit inicializační úlohy rozšířené služby.

Pamatujte, že neexistuje žádné řazení mezi voláními pro vytváření a otevírání posluchačů a **RunAsync**. Naslouchací procesy lze otevřít před spuštěním **RunAsync** . Podobně můžete vyvolat **RunAsync** před otevřením nebo dokonce vytvořením posluchačů komunikace. Je-li vyžadována libovolná synchronizace, zůstane jako cvičení pro implementátora. Tady je několik běžných řešení:

  - V některých případech nemohou naslouchací procesy fungovat, dokud nebudou vytvořeny jiné informace nebo dokud nebude provedena práce. U bezstavových služeb se tato práce může obvykle provádět i v jiných umístěních, například v následujících případech: 
    - V konstruktoru služby.
    - Během `CreateServiceInstanceListeners()` volání.
    - Jako součást konstrukce samotného naslouchacího procesu.
  - Kód v **RunAsync** se někdy nespustí, dokud nebudou naslouchací procesy otevřené. V takovém případě je potřeba další koordinace. Jedním z běžných řešení je, že v rámci posluchačů, které označují, kdy byly dokončeny, existuje příznak. Tento příznak je pak vrácen se změnami **RunAsync** před pokračováním ve skutečné práci.

## <a name="stateless-service-shutdown"></a>Bezstavové vypnutí služby
Pokud chcete vypnout bezstavovou službu, je stejný vzor následovaný stejným způsobem jako v opačném případě:

1. Paralelně:
    - Všechny otevřené naslouchací procesy jsou uzavřeny. `ICommunicationListener.CloseAsync()` je volána u každého naslouchacího procesu.
    - Token zrušení předaný do `RunAsync()` je zrušen. Ověření vlastnosti token zrušení `IsCancellationRequested` vrátí hodnotu true a pokud je volána, metoda tokenu `ThrowIfCancellationRequested` vyvolá výjimku `OperationCanceledException` .
2. Po `CloseAsync()` dokončení každého naslouchacího procesu a `RunAsync()` také dokončení `StatelessService.OnCloseAsync()` je metoda služby volána, je-li k dispozici.  OnCloseAsync se volá, když se Bezstavová instance služby bude řádně vypnout. K tomu může dojít při upgradu kódu služby, instance služby se přesouvá z důvodu vyrovnávání zatížení, nebo je zjištěna přechodná chyba. Není běžné ho přepsat `StatelessService.OnCloseAsync()` , ale je možné ho použít k bezpečnému zavírání prostředků, zastavení zpracování na pozadí, dokončení ukládání externího stavu nebo zavření stávajících připojení.
3. Po `StatelessService.OnCloseAsync()` dokončení dojde k destrukturování objektu služby.

## <a name="stateful-service-startup"></a>Spuštění stavové služby
Stavové služby mají podobný model bezstavových služeb s několika změnami. Pro spuštění stavové služby je pořadí událostí následující:

1. Služba je vytvořena.
2. `StatefulServiceBase.OnOpenAsync()` je volána. Toto volání není ve službě obvykle přepsáno.
3. Paralelně probíhají následující akce:
    - `StatefulServiceBase.CreateServiceReplicaListeners()` je vyvolána. 
      - Pokud je služba primární službou, otevřou se všechny vrácené naslouchací procesy. `ICommunicationListener.OpenAsync()` je volána u každého naslouchacího procesu.
      - Pokud je služba Sekundární službou, otevírají se jenom ty naslouchací procesy označené jako `ListenOnSecondary = true` . Naslouchací procesy, které jsou otevřeny na sekundárních počítačích, jsou méně běžné.
    - Pokud je služba aktuálně primární, `StatefulServiceBase.RunAsync()` je volána metoda služby.
4. Po dokončení volání a volání všech volání naslouchacího procesu repliky se zavolá `OpenAsync()` `RunAsync()` `StatefulServiceBase.OnChangeRoleAsync()` . Toto volání není ve službě obvykle přepsáno.

Podobně jako u bezstavových služeb neexistuje žádná koordinace mezi pořadím, ve kterém jsou naslouchací procesy vytvářeny a otevírány, a když je volána metoda **RunAsync** . Pokud potřebujete koordinaci, řešení jsou mnohem stejná. Pro stavovou službu je k dispozici jeden další případ. Řekněme, že volání, která přicházejí do komunikačních posluchačů, vyžadují informace uchovávané v některých [spolehlivých kolekcích](service-fabric-reliable-services-reliable-collections.md).

   > [!NOTE]  
   > Vzhledem k tomu, že naslouchací procesy komunikace mohly být otevřeny před tím, než budou spolehlivé kolekce čitelné nebo zapisovatelné a před zahájením **RunAsync** , je nutné provést nějakou další koordinaci. Nejjednodušším a nejběžnějším řešením je, aby naslouchací procesy komunikace vracely kód chyby, který klient nástroje používá ke zjištění, že se žádost opakuje.

## <a name="stateful-service-shutdown"></a>Vypnutí stavové služby
Stejně jako bezstavové služby jsou události životního cyklu během vypnutí stejné jako při spuštění, ale stornovány. Při vypnutí stavové služby dojde k následujícím událostem:

1. Paralelně:
    - Všechny otevřené naslouchací procesy jsou uzavřeny. `ICommunicationListener.CloseAsync()` je volána u každého naslouchacího procesu.
    - Token zrušení předaný do `RunAsync()` je zrušen. Ověření vlastnosti token zrušení `IsCancellationRequested` vrátí hodnotu true a pokud je volána, metoda tokenu `ThrowIfCancellationRequested` vyvolá výjimku `OperationCanceledException` .
2. Po `CloseAsync()` dokončení každého naslouchacího procesu a `RunAsync()` také dokončí službu, `StatefulServiceBase.OnChangeRoleAsync()` se zavolá. Toto volání není ve službě obvykle přepsáno.

   > [!NOTE]  
   > Nutnost počkat na dokončení **RunAsync** je nutná jenom v případě, že je tato replika primární replikou.

3. Po `StatefulServiceBase.OnChangeRoleAsync()` dokončení metody `StatefulServiceBase.OnCloseAsync()` je volána metoda. Toto volání je Neběžné přepsání, ale je k dispozici.
3. Po `StatefulServiceBase.OnCloseAsync()` dokončení dojde k destrukturování objektu služby.

## <a name="stateful-service-primary-swaps"></a>Primární swapy stavové služby
Když je spuštěná stavová služba, mají otevřené naslouchací procesy komunikace jenom primární repliky těchto stavových služeb a zavolaly se jejich **RunAsync** metoda. Sekundární repliky jsou konstruovány, ale nejsou k dispozici žádné další volání. Když je spuštěná stavová služba, replika, která je aktuálně primární, se může změnit v důsledku chyby nebo optimalizace vyrovnávání zatížení clusteru. Co to znamená v souvislosti s událostmi životního cyklu, které může replika zobrazit? Chování stavové repliky se zobrazuje v závislosti na tom, zda je replika během swapu degradována nebo povýšena.

### <a name="for-the-primary-thats-demoted"></a>U primární úrovně s nižší úrovní
U primární repliky, u které se sníží úroveň, Service Fabric potřebuje, aby tato replika zastavila zpracování zpráv a ukončila činnost na pozadí, kterou dělá. V důsledku toho tento krok vypadá jako při vypnutí služby. Jednou z nich je, že služba není destrukturovaná ani uzavřená, protože zůstává jako sekundární. Jsou volána následující rozhraní API:

1. Paralelně:
    - Všechny otevřené naslouchací procesy jsou uzavřeny. `ICommunicationListener.CloseAsync()` je volána u každého naslouchacího procesu.
    - Token zrušení předaný do `RunAsync()` je zrušen. Ověření vlastnosti token zrušení `IsCancellationRequested` vrátí hodnotu true a pokud je volána, metoda tokenu `ThrowIfCancellationRequested` vyvolá výjimku `OperationCanceledException` .
2. Po `CloseAsync()` dokončení každého naslouchacího procesu a `RunAsync()` také dokončí službu, `StatefulServiceBase.OnChangeRoleAsync()` se zavolá. Toto volání není ve službě obvykle přepsáno.

### <a name="for-the-secondary-thats-promoted"></a>Pro sekundární povýšenou úroveň
Podobně Service Fabric potřebuje sekundární repliku, která je povýšená tak, aby začala naslouchat zprávám na lince a spustit všechny úlohy na pozadí, které musí dokončit. V důsledku toho tento proces vypadá stejně jako při vytvoření služby, s tím rozdílem, že replika již existuje. Jsou volána následující rozhraní API:

1. Paralelně:
    - `StatefulServiceBase.CreateServiceReplicaListeners()` je vyvolána a jsou otevřeny všechny vracené naslouchací procesy. `ICommunicationListener.OpenAsync()` je volána u každého naslouchacího procesu.
    - `StatefulServiceBase.RunAsync()`Volá se metoda služby.
2. Po dokončení volání a volání všech volání naslouchacího procesu repliky se zavolá `OpenAsync()` `RunAsync()` `StatefulServiceBase.OnChangeRoleAsync()` . Toto volání není ve službě obvykle přepsáno.

### <a name="common-issues-during-stateful-service-shutdown-and-primary-demotion"></a>Běžné problémy při vypínání stavové služby a primárním snížení úrovně
Service Fabric mění primární stavovou službu z nejrůznějších důvodů. Nejběžnější je [Vyrovnávání zatížení clusteru](service-fabric-cluster-resource-manager-balancing.md) a [Upgrade aplikace](service-fabric-application-upgrade.md). Během těchto operací (stejně jako při normálním vypnutí služby, jako je třeba zjistit, jestli se služba odstranila), je důležité, aby služba byla v souladu s `CancellationToken` . 

Služby, které nezpracovávají zrušení čistě, můžou vyskytnout několik problémů. Tyto operace jsou pomalé, protože Service Fabric čeká na řádné zastavení služeb. To může mít za následek neúspěšné upgrady, které vyprší a vrátí se zpět. Nedodržení tokenu zrušení může také způsobit nevyvážené clustery. Clustery se stanou nevyvážené, protože uzly fungují jako aktivní, ale služby se nedají znovu vyrovnávat, protože je nebudete moct přesunout jinam. 

Vzhledem k tomu, že služby jsou stavové, je také pravděpodobně používána [spolehlivými kolekcemi](service-fabric-reliable-services-reliable-collections.md). V Service Fabric, když dojde ke snížení úrovně primárního primárního obsahu, nastane jedna z prvních věcí, ke kterým dojde, aby byl přístup pro zápis do základního stavu odvolán. To vede k druhé sadě problémů, které mohou ovlivnit životní cyklus služby. Kolekce vrací výjimky založené na časování a na tom, zda je replika přesunuta nebo vypnuta. Tyto výjimky by měly být zpracovány správně. Výjimky vyvolané Service Fabric spadají do trvalých [( `FabricException` )](/dotnet/api/system.fabric.fabricexception) a přechodných [( `FabricTransientException` )](/dotnet/api/system.fabric.fabrictransientexception) kategorií. Trvalé výjimky by měly být protokolovány a vyvolány, zatímco přechodné výjimky lze opakovat na základě některé logiky opakování.

Zpracování výjimek, které pocházejí z použití `ReliableCollections` ve spojení s událostmi životního cyklu služby, je důležitou součástí testování a ověřování spolehlivé služby. Před nasazením do produkčního prostředí doporučujeme, abyste při provádění upgradů a [testování chaos](service-fabric-controlled-chaos.md) vždy spouštěli službu při zatížení. Tyto základní kroky vám pomůžou zajistit správnou implementaci vaší služby a správné zpracování událostí životního cyklu.


## <a name="notes-on-the-service-lifecycle"></a>Poznámky k životnímu cyklu služby
  - Jak `RunAsync()` metoda, tak `CreateServiceReplicaListeners/CreateServiceInstanceListeners` volání jsou volitelné. Služba může mít jednu z nich, nebo ani jeden z nich. Pokud například služba provádí veškerou práci v reakci na volání uživatele, není nutné ji implementovat `RunAsync()` . Jsou nutné pouze naslouchací procesy komunikace a jejich přidružený kód. Podobně vytváření a vracení posluchačů komunikace je volitelné, protože služba může mít pouze práci na pozadí, a proto stačí implementovat `RunAsync()` .
  - Je platný, aby se služba úspěšně dokončila `RunAsync()` a vrátila se z ní. Dokončení není podmínkou selhání. Dokončení `RunAsync()` znamená, že práce na pozadí služby byla dokončena. U stavových spolehlivých služeb `RunAsync()` se znovu zavolá, pokud se replika sníží z primární na sekundární a pak se převede zpět na primární.
  - Pokud se služba ukončí od `RunAsync()` vyvolání neočekávané výjimky, jedná se o chybu. Objekt služby je vypnutý a nahlásila se chyba stavu.
  - I když při návratu z těchto metod nedochází k žádným časovým limitům, okamžitě ztratíte možnost zapisovat do spolehlivých kolekcí, a proto nemůžete dokončit žádnou skutečnou práci. Po přijetí žádosti o zrušení doporučujeme co nejrychleji vracet co nejrychleji. Pokud vaše služba nereaguje na tato volání rozhraní API v rozumné době, Service Fabric může vynuceně ukončit vaši službu. K tomu obvykle dochází pouze během upgradu aplikace nebo při odstraňování služby. Ve výchozím nastavení je tento časový limit 15 minut.
  - Selhání ve `OnCloseAsync()` výsledku cesty `OnAbort()` , což je nejvyšší možná nejlepší příležitost pro službu k vyčištění a uvolnění všech prostředků, které požadoval. Tato operace se obecně volá, když se v uzlu zjistí trvalá chyba, nebo když Service Fabric nemůže spolehlivě spravovat životní cyklus instance služby z důvodu interního selhání.
  - `OnChangeRoleAsync()` se volá, když replika stavové služby mění roli (například primární nebo sekundární). Primárním replikám je dán stav zápisu (můžou vytvářet a zapisovat do spolehlivých kolekcí). Sekundárním replikám je předaný stav čtení (dá se číst jenom z existujících spolehlivých kolekcí). Většina práce ve stavové službě se provádí v primární replice. Sekundární repliky můžou provádět ověřování jen pro čtení, generování sestav, dolování dat nebo jiné úlohy jen pro čtení.

## <a name="next-steps"></a>Další kroky
- [Úvod do Reliable Services](service-fabric-reliable-services-introduction.md)
- [Rychlý Start Reliable Services](service-fabric-reliable-services-quick-start.md)
- [Repliky a instance](service-fabric-concepts-replica-lifecycle.md)
