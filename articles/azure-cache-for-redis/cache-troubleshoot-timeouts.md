---
title: Řešení potíží s časovými limity služby Azure Cache for Redis
description: Naučte se řešit běžné problémy s vypršením časového limitu v mezipaměti Azure pro Redis, jako jsou třeba opravy serveru Redis a výjimky StackExchange. Redis.
author: yegu-ms
ms.author: yegu
ms.service: cache
ms.topic: conceptual
ms.custom: devx-track-csharp
ms.date: 10/18/2019
ms.openlocfilehash: bf8b20dadd2fcd78657aa6877e796b645332dd94
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2021
ms.locfileid: "88213458"
---
# <a name="troubleshoot-azure-cache-for-redis-timeouts"></a>Řešení potíží s časovými limity služby Azure Cache for Redis

Tato část popisuje řešení potíží s časovým limitem, ke kterým dochází při připojování k Azure cache pro Redis.

- [Opravy serveru Redis](#redis-server-patching)
- [Výjimky časového limitu StackExchange. Redis](#stackexchangeredis-timeout-exceptions)

> [!NOTE]
> Několik kroků pro řešení potíží v této příručce obsahuje pokyny ke spouštění příkazů Redis a monitorování různých metrik výkonu. Další informace a pokyny najdete v článcích v části [Další informace](#additional-information) .
>

## <a name="redis-server-patching"></a>Opravy serveru Redis

Služba Azure cache pro Redis pravidelně aktualizuje svůj serverový software jako součást funkce spravované služby, kterou poskytuje. Tato aktivita [opravy](cache-failover.md) probíhá hlavně za scénou. Během opravy převzetí služeb při selhání se Redis klienti, kteří jsou připojení k těmto uzlům, můžou docházet k dočasným časovým limitům při přepínání mezi těmito uzly. Další informace o tom, jaké funkce aktualizace vedlejších účinků můžou mít v aplikaci a jak můžete vylepšit zpracování událostí oprav, najdete v tématu [jak převzetí služeb při selhání v klientské aplikaci ovlivní](cache-failover.md#how-does-a-failover-affect-my-client-application) .

## <a name="stackexchangeredis-timeout-exceptions"></a>Výjimky časového limitu StackExchange. Redis

StackExchange. Redis používá konfigurační nastavení s názvem `synctimeout` pro synchronní operace s výchozí hodnotou 5000 MS. Pokud se synchronní volání v tuto chvíli nedokončí, klient StackExchange. Redis vyvolá chybu s časovým limitem, která je podobná následujícímu příkladu:

```output
    System.TimeoutException: Timeout performing MGET 2728cc84-58ae-406b-8ec8-3f962419f641, inst: 1,mgr: Inactive, queue: 73, qu=6, qs=67, qc=0, wr=1/1, in=0/0 IOCP: (Busy=6, Free=999, Min=2,Max=1000), WORKER (Busy=7,Free=8184,Min=2,Max=8191)
```

Tato chybová zpráva obsahuje metriky, které vám pomohou Ukázat příčinu a možné řešení problému. Následující tabulka obsahuje podrobnosti o metrikách chybové zprávy.

| Metrika chybové zprávy | Podrobnosti |
| --- | --- |
| instrukce |V posledním časovém intervalu: byly vydány příkazy 0. |
| Mgr |Správce soketu to `socket.select` znamená, že se požádá o operační systém, aby označoval soket, který má něco udělat. Čtenář se aktivně nečte ze sítě, protože nebere v úvahu cokoli, co dělat. |
| fronta |K dispozici jsou 73 celkový počet probíhajících operací. |
| Thá |6 probíhajících operací je v neodeslané frontě a ještě není zapsaná do odchozí sítě. |
| qs |67 probíhajících operací bylo odesláno na server, ale odpověď zatím není k dispozici. Odpověď může být `Not yet sent by the server` nebo. `sent by the server but not yet processed by the client.` |
| QC |počet probíhajících operací zaznamenal odpovědi, ale ještě nebyly označeny jako splněné, protože čekají na cyklus dokončení. |
| radiační |Existuje aktivní zapisovač (to znamená, že 6 neodeslaných požadavků se Neignoruje) bajtů/activewriters |
| dovnitř |Nejsou k dispozici žádná aktivní čtecí zařízení a v bajtech síťových adaptérů/activereaders je k dispozici nula bajtů. |

Pomocí následujících kroků můžete prozkoumat možné hlavní příčiny.

1. V rámci osvědčeného postupu se ujistěte, že používáte následující model pro připojení při použití klienta StackExchange. Redis.

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

    Další informace najdete v tématu [připojení k mezipaměti pomocí stackexchange. Redis](cache-dotnet-how-to-use-azure-redis-cache.md#connect-to-the-cache).

1. Ujistěte se, že váš server a klientská aplikace jsou ve stejné oblasti v Azure. Například může docházet k vypršení časového limitu, když je vaše mezipaměť v Východní USA, ale klient se nachází v Západní USA a žádost se nedokončila v rámci `synctimeout` intervalu nebo může docházet k vypršení časového limitu při ladění z místního vývojového počítače. 

    Důrazně doporučujeme mít mezipaměť a klienta ve stejné oblasti Azure. Pokud máte scénář, který zahrnuje volání mezi oblastmi, nastavte `synctimeout` interval na hodnotu vyšší, než je výchozí interval 5000-MS zahrnutím `synctimeout` vlastnosti do připojovacího řetězce. Následující příklad ukazuje fragment připojovacího řetězce pro StackExchange. Redis poskytnutý službou Azure cache pro Redis s `synctimeout` 2000 MS.

    ```output
    synctimeout=2000,cachename.redis.cache.windows.net,abortConnect=false,ssl=true,password=...
    ```

1. Ujistěte se, že používáte nejnovější verzi [balíčku NuGet stackexchange. Redis](https://www.nuget.org/packages/StackExchange.Redis/). V kódu jsou neustále opraveny chyby, aby bylo lépe robustní na vypršení časových limitů, takže je důležité mít nejnovější verzi.
1. Pokud jsou vaše požadavky vázány omezeními šířky pásma na serveru nebo klientovi, trvá jejich dokončení déle a může způsobit vypršení časových limitů. Pokud chcete zjistit, jestli je váš časový limit z důvodu šířky pásma sítě na serveru, přečtěte si téma [omezení šířky pásma na straně serveru](cache-troubleshoot-server.md#server-side-bandwidth-limitation). Pokud chcete zjistit, jestli je časový limit z důvodu šířky pásma sítě klienta, přečtěte si téma [omezení šířky pásma na straně klienta](cache-troubleshoot-client.md#client-side-bandwidth-limitation).
1. Získáváte na serveru nebo na klientovi vazbu na procesor?

   - Ověřte, jestli na svém klientovi máte vazbu na základě procesoru. Vysoký procesor by mohl způsobit, že se požadavek v rámci intervalu nezpracovává `synctimeout` a způsobí vypršení časového limitu žádosti. Přesunutí na větší velikost klienta nebo distribuce zatížení může pomáhat s řízením tohoto problému.
   - Podívejte se, jestli se na serveru nepracujete s VYUŽITÍm monitorování [metriky výkonu mezipaměti](cache-how-to-monitor.md#available-metrics-and-reporting-intervals)procesoru. Žádosti přicházející v době, kdy je Redis procesor, můžou způsobit vypršení časového limitu těchto požadavků. Tuto podmínku můžete vyřešit tak, že zatížení rozšíříte mezi více horizontálních oddílů v mezipaměti Premium nebo upgradujete na větší velikost nebo cenovou úroveň. Další informace najdete v tématu [omezení šířky pásma na straně serveru](cache-troubleshoot-server.md#server-side-bandwidth-limitation).
1. Nemusíte na serveru zpracovat příkazy dlouhou dobu. Dlouhotrvající příkazy, jejichž zpracování na Redis serveru trvá dlouhou dobu, můžou způsobit vypršení časových limitů. Další informace o dlouhotrvajících příkazech naleznete v tématu [dlouhotrvající příkazy](cache-troubleshoot-server.md#long-running-commands). Můžete se připojit ke své instanci Azure cache for Redis pomocí klienta Redis-CLI nebo [konzoly Redis](cache-configure.md#redis-console). Pak spusťte příkaz [SLOWLOG](https://redis.io/commands/slowlog) , abyste viděli, zda jsou požadavky pomalejší, než bylo očekáváno. Redis Server a StackExchange. Redis jsou optimalizované pro mnoho malých požadavků místo méně velkých požadavků. Rozdělení dat do menších bloků může tady zlepšit.

    Informace o připojení ke koncovému bodu TLS/SSL v mezipaměti pomocí Redis-CLI a stunnelu najdete v blogovém příspěvku s [oznámením o poskytovateli stavu relace ASP.NET pro verzi Preview pro Redis Preview](https://devblogs.microsoft.com/aspnet/announcing-asp-net-session-state-provider-for-redis-preview-release/).
1. Vysoké zatížení serveru Redis může způsobit vypršení časových limitů. Zatížení serveru můžete monitorovat monitorováním `Redis Server Load` [metriky výkonu mezipaměti](cache-how-to-monitor.md#available-metrics-and-reporting-intervals). Zatížení serveru 100 (maximální hodnota) znamená, že server Redis je zaneprázdněný, a to bez času nečinnosti, zpracování požadavků. Pokud chcete zjistit, jestli některé žádosti zabírají všechny možnosti serveru, spusťte příkaz SlowLog, jak je popsáno v předchozím odstavci. Další informace najdete v tématu vysoké využití procesoru/zatížení serveru.
1. Bylo na straně klienta nějaká jiná událost, která by mohla způsobit Blip sítě? Mezi běžné události patří: škálování počtu instancí klientů nahoru nebo dolů, nasazení nové verze klienta nebo automatické škálování povoleno. V našem testování jsme zjistili, že automatické škálování nebo horizontální navýšení kapacity může způsobit ztrátu odchozího síťového připojení po dobu několika sekund. StackExchange. Redis kód je odolný vůči takovým událostem a znovu se připojuje. Při opětovném připojení můžou všechny žádosti ve frontě vyprší časový limit.
1. Existuje velký požadavek před několika malými požadavky na mezipaměť, jejichž časový limit vypršel? Parametr `qs` v chybové zprávě oznamuje, kolik požadavků bylo odesláno z klienta na server, ale nezpracovalo odpověď. Tato hodnota může zůstat větší, protože StackExchange. Redis používá jedno připojení TCP a může v jednom okamžiku číst jen jednu odpověď. I když vypršel časový limit první operace, nezastaví posílání více dat na server ani ze serveru. Ostatní požadavky budou zablokovány, dokud nebude velký požadavek dokončen a může dojít k vypršení časového limitu. Jedním z řešení je minimalizace pravděpodobnosti časových limitů tím, že zajistíte dostatečnou velikost mezipaměti pro vaše úlohy a rozdělení velkých hodnot do menších bloků dat. Dalším možným řešením je použít `ConnectionMultiplexer` ve vašem klientovi fond objektů a `ConnectionMultiplexer` při odesílání nové žádosti zvolit aspoň načtenou hodnotu. Načítání přes více objektů připojení by mělo zabránit jednomu časovému limitu, který by způsobil vypršení dalších požadavků.
1. Pokud používáte `RedisSessionStateProvider` , ujistěte se, že jste správně nastavili časový limit opakování. `retryTimeoutInMilliseconds` by měl být větší než `operationTimeoutInMilliseconds` , jinak nedojde k žádnému opakování. V následujícím příkladu `retryTimeoutInMilliseconds` je nastavená na 3000. Další informace najdete v tématu [zprostředkovatel stavu relací ASP.NET pro Azure cache pro Redis](cache-aspnet-session-state-provider.md) a [Jak používat konfigurační parametry zprostředkovatele stavu relace a zprostředkovatele výstupní mezipaměti](https://github.com/Azure/aspnet-redis-providers/wiki/Configuration).

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

1. Pomocí [monitorování](cache-how-to-monitor.md#available-metrics-and-reporting-intervals) a ověřte využití paměti na serveru Azure cache pro Redis `Used Memory RSS` `Used Memory` . Pokud je zásada vyřazení nastavená na místo, Redis spustí vyřazení klíčů při `Used_Memory` dosažení velikosti mezipaměti. V ideálním případě `Used Memory RSS` by měl být pouze mírně vyšší než `Used memory` . Velký rozdíl znamená fragmentaci paměti (interní nebo externí). Pokud `Used Memory RSS` je menší než `Used Memory` , znamená to, že část paměti mezipaměti byla prohozena operačním systémem. Pokud dojde k záměně, můžete očekávat některé významné latence. Vzhledem k tomu, že Redis nemá kontrolu nad tím, jak je jejich přidělení namapováno na paměťové stránky, `Used Memory RSS` je často výsledkem špičky využití paměti. Když server Redis uvolní paměť, přidělování paměti převezme paměť, ale může nebo nemusí získat paměť zpátky do systému. Může dojít k nesouladu mezi `Used Memory` hodnotou a spotřebou paměti, jak je uvedeno v operačním systému. Paměť mohla být použita a uvolněna Redis, ale nebyla vrácena zpět do systému. Chcete-li snížit problémy s pamětí, můžete provést následující kroky:

   - Upgradujte mezipaměť na větší velikost, aby neběžela omezení paměti systému.
   - Nastavte čas vypršení platnosti klíčů tak, aby byly starší hodnoty vyřazeny interaktivně.
   - Monitorujte `used_memory_rss` metriku mezipaměti. Pokud se tato hodnota blíží velikosti jejich mezipaměti, budete pravděpodobně moci začít zobrazovat problémy s výkonem. Pokud používáte mezipaměť Premium, můžete data distribuovat mezi několik horizontálních oddílů, nebo můžete upgradovat na větší velikost mezipaměti.

   Další informace najdete v tématu [tlak na paměť na serveru Redis](cache-troubleshoot-server.md#memory-pressure-on-redis-server).

## <a name="additional-information"></a>Další informace

- [Řešení potíží se službou Azure Cache for Redis na straně klienta](cache-troubleshoot-client.md)
- [Řešení potíží se službou Azure Cache for Redis na straně serveru](cache-troubleshoot-server.md)
- [Jak mohu srovnávací testy a testovat výkon své mezipaměti?](cache-management-faq.md#how-can-i-benchmark-and-test-the-performance-of-my-cache)
- [Monitorování Azure Cache for Redis](cache-how-to-monitor.md)
