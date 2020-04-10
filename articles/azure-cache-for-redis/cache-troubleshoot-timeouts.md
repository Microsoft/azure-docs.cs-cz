---
title: Řešení potíží s časovými limity služby Azure Cache for Redis
description: Zjistěte, jak vyřešit běžné problémy s časovým úsporem s Azure Cache for Redis, jako jsou opravy serveru redis a výjimky časového výpadku StackExchange.Redis.
author: yegu-ms
ms.author: yegu
ms.service: cache
ms.topic: conceptual
ms.date: 10/18/2019
ms.openlocfilehash: 4301a55e3f5ea5b445ef1540ee59d1b5c28ca0ed
ms.sourcegitcommit: ae3d707f1fe68ba5d7d206be1ca82958f12751e8
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/10/2020
ms.locfileid: "81010813"
---
# <a name="troubleshoot-azure-cache-for-redis-timeouts"></a>Řešení potíží s časovými limity služby Azure Cache for Redis

Tato část popisuje řešení problémů s časovým úvazkem, ke kterým dochází při připojování k Azure Cache for Redis.

- [Opravy serveru Redis](#redis-server-patching)
- [Výjimky časového meze souboru StackExchange.Redis](#stackexchangeredis-timeout-exceptions)

> [!NOTE]
> Několik kroků řešení potíží v této příručce obsahuje pokyny ke spuštění příkazů Redis a sledování různých metrik výkonu. Další informace a pokyny naleznete v článcích v části [Další informace.](#additional-information)
>

## <a name="redis-server-patching"></a>Opravy serveru Redis

Azure Cache for Redis pravidelně aktualizuje svůj serverový software jako součást funkce spravované služby, kterou poskytuje. Tato [záplatovací](cache-failover.md) činnost probíhá převážně za scénou. Během převzetí služeb při selhání při redis server uzly jsou opravy, Redis klienti připojení k těmto uzlům může dojít k dočasné časové přeběhy jako připojení jsou přepínat mezi těmito uzly. Další informace o tom, jaké vedlejší účinky mohou mít opravy ve vaší aplikaci a jak můžete zlepšit zpracování událostí oprav, naleznete [v tématu Jak převzetí služeb při selhání ovlivní klientskou aplikaci.](cache-failover.md#how-does-a-failover-affect-my-client-application)

## <a name="stackexchangeredis-timeout-exceptions"></a>Výjimky časového meze souboru StackExchange.Redis

Soubor StackExchange.Redis používá `synctimeout` nastavení konfigurace pojmenované pro synchronní operace s výchozí hodnotou 1000 ms. Pokud synchronní volání není dokončena v tomto okamžiku, klient StackExchange.Redis vyvolá chybu časového času podobné v následujícím příkladu:

    System.TimeoutException: Timeout performing MGET 2728cc84-58ae-406b-8ec8-3f962419f641, inst: 1,mgr: Inactive, queue: 73, qu=6, qs=67, qc=0, wr=1/1, in=0/0 IOCP: (Busy=6, Free=999, Min=2,Max=1000), WORKER (Busy=7,Free=8184,Min=2,Max=8191)

Tato chybová zpráva obsahuje metriky, které vám mohou pomoci s odkazem na příčinu a možné řešení problému. Následující tabulka obsahuje podrobnosti o metrikách chybových zpráv.

| Metrika chybové zprávy | Podrobnosti |
| --- | --- |
| Inst |V poslední čas řez: 0 příkazy byly vydány |
| Mgr |Správce soketu `socket.select`dělá , což znamená, že žádá operační ho s a označí soket, který má co do činění. Čtenář není aktivně čtení ze sítě, protože si nemyslí, že je co dělat |
| fronta |Probíhá celkem 73 operací |
| Qu |6 probíhajících operací je v neodeslané frontě a ještě nebylo zapsáno do odchozí sítě. |
| Qs |Na server bylo odesláno 67 probíhajících operací, ale odpověď ještě není k dispozici. Odpověď může `Not yet sent by the server` být nebo může být`sent by the server but not yet processed by the client.` |
| Qc |0 probíhajících operací zaznamenalo odpovědi, ale ještě nebyly označeny jako dokončené, protože čekají na dokončení smyčky |
| Wr |Existuje aktivní zapisovač (což znamená, že 6 neodeslaných požadavků není ignorováno) bajtů / activewriters |
| in |Nejsou k dispozici žádné aktivní čtečky a nula bajtů jsou k dispozici ke čtení na bajtů nic/activereaders |

Můžete použít následující kroky k prozkoumání možných příčin root.

1. Jako osvědčený postup se ujistěte, že používáte následující vzor pro připojení při použití klienta StackExchange.Redis.

    ```csharp
    private static Lazy<ConnectionMultiplexer> lazyConnection = new Lazy<ConnectionMultiplexer>(() =>
    {
        return ConnectionMultiplexer.Connect("cachename.redis.cache.windows.net,abortConnect=false,ssl=true,password=...");

    });

    public static ConnectionMultiplexer Connection
    {
        get
        {
            return lazyConnection.Value;
        }
    }
    ```

    Další informace naleznete [v tématu Připojení ke mezipaměti pomocí souboru StackExchange.Redis](cache-dotnet-how-to-use-azure-redis-cache.md#connect-to-the-cache).

1. Ujistěte se, že váš server a klientská aplikace jsou ve stejné oblasti v Azure. Například může být získávání časové osy, když vaše mezipaměť je v USA, ale klient `synctimeout` je v USA – západ a požadavek není dokončena v intervalu nebo může být získání časové osy při ladění z místního vývojového počítače. 

    Důrazně doporučujeme mít mezipaměť a v klientovi ve stejné oblasti Azure. Pokud máte scénář, který zahrnuje volání mezi `synctimeout` oblastmi, měli byste nastavit interval na hodnotu vyšší `synctimeout` než výchozí interval 1000 ms zahrnutím vlastnosti do připojovacího řetězce. Následující příklad ukazuje úryvek připojovacího řetězce pro StackExchange.Redis poskytované `synctimeout` azure cache pro Redis s 2000 ms.

        synctimeout=2000,cachename.redis.cache.windows.net,abortConnect=false,ssl=true,password=...
1. Ujistěte se, že používáte nejnovější verzi [balíčku StackExchange.Redis NuGet](https://www.nuget.org/packages/StackExchange.Redis/). Tam jsou chyby neustále opravuje v kódu, aby bylo robustnější na časové osy, takže s nejnovější verzí je důležité.
1. Pokud jsou vaše požadavky vázány omezeníšířky pásma na serveru nebo klienta, trvá déle pro jejich dokončení a může způsobit časové limity. Chcete-li zjistit, zda je časový rozsah z důvodu šířky pásma sítě na serveru, přečtěte si informace o [omezení šířky pásma na straně serveru](cache-troubleshoot-server.md#server-side-bandwidth-limitation). Chcete-li zjistit, zda je časový rozsah z důvodu šířky pásma klientské sítě, přečtěte si informace o [omezení šířky pásma na straně klienta](cache-troubleshoot-client.md#client-side-bandwidth-limitation).
1. Jste stále cpu vázán na serveru nebo na straně klienta?

   - Zkontrolujte, zda jste stále vázáncpu na vašem klientovi. Vysoký procesor může způsobit, že požadavek `synctimeout` nebude zpracován v intervalu a způsobí, že požadavek na časový limit. Přesunutí na větší velikost klienta nebo distribuce zatížení může pomoci řídit tento problém.
   - Sledováním [metriky výkonu mezipaměti](cache-how-to-monitor.md#available-metrics-and-reporting-intervals)procesoru zkontrolujte, zda na serveru přilne procesor. Požadavky přicházející v době, kdy je Redis vázán na procesor, mohou způsobit, že tyto požadavky budou vypovězení. Chcete-li tuto podmínku vyřešit, můžete distribuovat zatížení mezi více úlomků v mezipaměti premium nebo upgradovat na větší velikost nebo cenovou úroveň. Další informace naleznete v [tématu Omezení šířky pásma na straně serveru](cache-troubleshoot-server.md#server-side-bandwidth-limitation).
1. Zpracovávají se příkazy na serveru dlouho? Dlouhotrvající příkazy, které trvá dlouhou dobu zpracování na serveru redis může způsobit časové oběhu. Další informace o dlouhotrvajících příkazech naleznete v [tématu Long-running commands](cache-troubleshoot-server.md#long-running-commands). K instanci Azure Cache for Redis se můžete připojit pomocí klienta redis-cli nebo [konzoly Redis](cache-configure.md#redis-console). Potom spusťte příkaz [SLOWLOG](https://redis.io/commands/slowlog) a zjistěte, zda existují požadavky pomalejší, než bylo očekáváno. Redis Server a StackExchange.Redis jsou optimalizovány pro mnoho malých požadavků, nikoli méně velkých požadavků. Rozdělení dat na menší bloky dat může zlepšit věci zde.

    Informace o připojení ke koncovému bodu TLS/SSL mezipaměti pomocí redis-cli a stunnel najdete v příspěvku blogu [Oznámení ASP.NET zprostředkovatele stavu relace pro vydání Redis Preview .](https://blogs.msdn.com/b/webdev/archive/2014/05/12/announcing-asp-net-session-state-provider-for-redis-preview-release.aspx)
1. Vysoké zatížení serveru Redis může způsobit časové protytby. Zatížení serveru můžete sledovat sledováním `Redis Server Load` [metriky výkonu mezipaměti](cache-how-to-monitor.md#available-metrics-and-reporting-intervals). Zatížení serveru 100 (maximální hodnota) znamená, že server redis byl zaneprázdněn, bez doby nečinnosti, zpracování požadavků. Chcete-li zjistit, zda některé požadavky zabírají všechny funkce serveru, spusťte příkaz SlowLog, jak je popsáno v předchozím odstavci. Další informace naleznete v tématu Vysoké využití procesoru / Zatížení serveru.
1. Byla na straně klienta nějaká jiná událost, která mohla způsobit výkyv v síti? Mezi běžné události patří: škálování počtu instancí klienta nahoru nebo dolů, nasazení nové verze klienta nebo automatické škálování povoleno. V našem testování jsme zjistili, že automatické škálování nebo škálování nahoru/dolů může způsobit ztrátu odchozího připojení k síti na několik sekund. StackExchange.Redis kód je odolný vůči těmto událostem a znovu připojí. Při opětovném připojení mohou být všechny požadavky ve frontě vypodonomovat.
1. Byl tam velký požadavek předcházející několik malých požadavků do mezipaměti, která časový režim? Parametr `qs` v chybové zprávě informuje o tom, kolik požadavků bylo odesláno z klienta na server, ale nezpracovalodpověď. Tato hodnota může nadále růst, protože StackExchange.Redis používá jedno připojení TCP a lze číst pouze jednu odpověď najednou. I když byl časový režim první operace outd, nezastaví další data před odesláním na server nebo ze serveru. Ostatní požadavky budou blokovány, dokud nebude velký požadavek dokončen a může způsobit časové toky. Jedním z řešení je minimalizovat možnost časového nastavení tím, že zajistíte, že vaše mezipaměť je dostatečně velká pro vaše úlohy a rozdělení velkých hodnot na menší bloky. Dalším možným řešením je použití `ConnectionMultiplexer` fondu objektů v klientovi `ConnectionMultiplexer` a zvolte nejméně načtené při odesílání nového požadavku. Načítání přes více objektů připojení by měla zabránit jeden časový limit z příčinou jiných požadavků také časový limit.
1. Pokud používáte `RedisSessionStateProvider`, ujistěte se, že jste správně nastavili časový čas opakování. `retryTimeoutInMilliseconds`by měla `operationTimeoutInMilliseconds`být vyšší než , jinak nedojde k žádnému opakování. V následujícím `retryTimeoutInMilliseconds` příkladu je nastavena na 3000. Další informace naleznete [v tématu ASP.NET zprostředkovatele stavu relace pro Azure Cache for Redis](cache-aspnet-session-state-provider.md) a [Jak používat parametry konfigurace zprostředkovatele stavu relace a zprostředkovatele výstupní mezipaměti](https://github.com/Azure/aspnet-redis-providers/wiki/Configuration).

    ```xml
    <add
      name="AFRedisCacheSessionStateProvider"
      type="Microsoft.Web.Redis.RedisSessionStateProvider"
      host="enbwcache.redis.cache.windows.net"
      port="6380"
      accessKey="…"
      ssl="true"
      databaseId="0"
      applicationName="AFRedisCacheSessionState"
      connectionTimeoutInMilliseconds = "5000"
      operationTimeoutInMilliseconds = "1000"
      retryTimeoutInMilliseconds="3000" />
    ```

1. Zkontrolujte využití paměti na serveru Azure Cache `Used Memory`for Redis [monitorováním](cache-how-to-monitor.md#available-metrics-and-reporting-intervals) `Used Memory RSS` a . Pokud je zásada vyřazení na místě, Redis spustí `Used_Memory` vyřazování klíčů, když dosáhne velikosti mezipaměti. V ideálním `Used Memory RSS` případě by měla `Used memory`být jen mírně vyšší než . Velký rozdíl znamená, že je fragmentace paměti (vnitřní nebo externí). Pokud `Used Memory RSS` je `Used Memory`menší než , znamená to, že část mezipaměti byla prohozena operačním systémem. Pokud dojde k tomuto prohození, můžete očekávat některé významné latence. Vzhledem k tomu, že Redis nemá kontrolu nad tím, `Used Memory RSS` jak jsou jeho přidělení mapována na stránky paměti, vysoká je často výsledkem špičky využití paměti. Když server Redis uvolní paměť, alokátor převezme paměť, ale může nebo nemusí vrátit paměť do systému. Může být nesoulad mezi `Used Memory` hodnotou a spotřebou paměti podle zprávy operačního systému. Paměť byla pravděpodobně použita a uvolněna společností Redis, ale nebyla vrácena systému. Chcete-li zmírnit problémy s pamětí, můžete provést následující kroky:

   - Upgradujte mezipaměť na větší velikost, abyste neběželi proti omezením paměti v systému.
   - Nastavte časy vypršení platnosti klíčů tak, aby starší hodnoty byly proaktivně vyřazeny.
   - Sledujte `used_memory_rss` metriku mezipaměti. Když se tato hodnota blíží velikosti jejich mezipaměti, pravděpodobně se vám začnou zoaškovat problémy s výkonem. Pokud používáte prémiovou mezipaměť, distribuujte data mezi více úlomků nebo upgradujte na větší velikost mezipaměti.

   Další informace naleznete v [tématu Tlak paměti na serveru Redis](cache-troubleshoot-server.md#memory-pressure-on-redis-server).

## <a name="additional-information"></a>Další informace

- [Řešení potíží se službou Azure Cache for Redis na straně klienta](cache-troubleshoot-client.md)
- [Řešení potíží se službou Azure Cache for Redis na straně serveru](cache-troubleshoot-server.md)
- [Jak mohu porovnat a otestovat výkon mezipaměti?](cache-faq.md#how-can-i-benchmark-and-test-the-performance-of-my-cache)
- [Jak monitorovat Azure Cache pro Redis](cache-how-to-monitor.md)
