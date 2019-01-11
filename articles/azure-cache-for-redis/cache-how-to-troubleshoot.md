---
title: Řešení potíží s Azure Cache pro Redis | Dokumentace Microsoftu
description: Zjistěte, jak vyřešit běžné problémy s mezipamětí Azure Redis.
services: azure-cache-for-redis
documentationcenter: ''
author: wesmc7777
manager: cfowler
editor: ''
ms.assetid: 928b9b9c-d64f-4252-884f-af7ba8309af6
ms.service: cache
ms.workload: tbd
ms.tgt_pltfrm: azure-cache-for-redis
ms.devlang: na
ms.topic: article
ms.date: 01/06/2017
ms.author: wesmc
ms.openlocfilehash: 154f5200872dbc06550f396717cb215f3db4f7dd
ms.sourcegitcommit: d4f728095cf52b109b3117be9059809c12b69e32
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/10/2019
ms.locfileid: "54199574"
---
# <a name="how-to-troubleshoot-azure-cache-for-redis"></a>Řešení potíží s Azure Cache pro Redis
Tento článek obsahuje pokyny pro řešení potíží s následující kategorie mezipaměti Azure Redis problémů.

* [Řešení potíží na straně klienta](#client-side-troubleshooting) – Tato část obsahuje pokyny pro identifikaci a řešení potíží s způsobeno aplikací, připojení k mezipaměti Azure pro Redis.
* [Řešení potíží na straně serveru](#server-side-troubleshooting) – Tato část obsahuje pokyny pro identifikaci a řešení potíží s způsobená na ukládání do mezipaměti Azure na straně serveru Redis.
* [Výjimkách časového limitu StackExchange.Redis](#stackexchangeredis-timeout-exceptions) – Tato část obsahuje informace o řešení problémů při používání klienta StackExchange.Redis.

> [!NOTE]
> Některé z kroků v tomto průvodci zahrnují pokyny, jak spouštět příkazy Redis a monitorování různých metrik výkonu. Další informace a pokyny najdete v článcích v [Další informace o](#additional-information) oddílu.
> 
> 

## <a name="client-side-troubleshooting"></a>Řešení potíží na straně klienta
Tato část popisuje řešení problémů, ke kterým dochází z důvodu podmínku na klientské aplikaci.

* [Přetížení paměti na straně klienta](#memory-pressure-on-the-client)
* [Shlukového přenosu](#burst-of-traffic)
* [Klient vysoké využití procesoru](#high-client-cpu-usage)
* [Překročení šířky pásma na straně klienta](#client-side-bandwidth-exceeded)
* [Velikost velkých žádostí a odpovědí](#large-requestresponse-size)
* [Co se stalo s Redis má data?](#what-happened-to-my-data-in-redis)

### <a name="memory-pressure-on-the-client"></a>Přetížení paměti na straně klienta
#### <a name="problem"></a>Problém
Přetížení paměti v klientském počítači vede na všechny typy problémů s výkonem, které můžou zdržet zpracování dat, která byla odeslána instance Redis bez jakéhokoli zpoždění. Pokud narazí na tlaku na paměť, obvykle má systém k datům stránky z fyzické paměti pro virtuální paměti, která je na disku. To *stránky chybující* způsobí, že systém výrazně zpomalit.

#### <a name="measurement"></a>Měření
1. Monitorovat využití paměti v počítači, abyste měli jistotu, že nepřekračuje dostupné paměti. 
2. Monitorování `Page Faults/Sec` čítače výkonu. Většina systémů mají některé chyby stránky i během normálního provozu, proto sledujte špiček v tomto čítači výkonu chyb stránky, které odpovídají vypršení časového limitu.

#### <a name="resolution"></a>Řešení
Upgradovat klienta ke klientovi větší velikost virtuálního počítače s více paměti nebo ponořte se do vaší vzory využití paměti ke snížení využití paměti.

### <a name="burst-of-traffic"></a>Shlukového přenosu
#### <a name="problem"></a>Problém
Nárůstům provozu v kombinaci s špatné `ThreadPool` nastavení může dojít k prodlevám při zpracování dat už poslali pomocí serveru Redis, ale dosud spotřebované na straně klienta.

#### <a name="measurement"></a>Měření
Monitorování jak vaše `ThreadPool` statistiky mění v čase pomocí kódu [tímto způsobem](https://github.com/JonCole/SampleCode/blob/master/ThreadPoolMonitor/ThreadPoolLogger.cs). Můžete také prohlédnout `TimeoutException` zprávu od StackExchange.Redis. Zde naleznete příklad:

    System.TimeoutException: Timeout performing EVAL, inst: 8, mgr: Inactive, queue: 0, qu: 0, qs: 0, qc: 0, wr: 0, wq: 0, in: 64221, ar: 0, 
    IOCP: (Busy=6,Free=999,Min=2,Max=1000), WORKER: (Busy=7,Free=8184,Min=2,Max=8191)

V předchozí zprávy jsou nejrůznější problémy, které jsou zajímavé:

1. Všimněte si, že v `IOCP` oddílu a `WORKER` oddílu máte `Busy` hodnotu, která je větší než `Min` hodnotu. Tento rozdíl znamená, že vaše `ThreadPool` nastavení vyžadují úpravu.
2. Můžete také zobrazit `in: 64221`. Tato hodnota označuje, že byly přijaty ve vrstvě soketu jádra 64,211 bajtů, ale nebyly přečteny aplikací (například StackExchange.Redis). Tento rozdíl obvykle znamená, že vaše aplikace není čtení dat ze sítě tak rychle, jak na serveru je odesláním.

#### <a name="resolution"></a>Řešení
Konfigurace vašeho [nastavení fondu vláken](https://gist.github.com/JonCole/e65411214030f0d823cb) abyste měli jistotu, že se váš fond vláken škálování rychle v části burst scénáře.

### <a name="high-client-cpu-usage"></a>Klient vysoké využití procesoru
#### <a name="problem"></a>Problém
Vysoké využití procesoru na straně klienta je znamením, že systém nemůže nadále práce, která byla požádána provádět. Tato situace znamená, že klient může dojít k selhání zpracování odpovědi z Redis včas. i když Redis rychlé odeslané odpovědi.

#### <a name="measurement"></a>Měření
Sledovat využití procesoru široké systému prostřednictvím webu Azure Portal nebo čítače výkonu přidružený. Dejte pozor, abyste monitorování *procesu* procesoru protože jednoho procesu může mít nízké využití procesoru na stejný čas celkového systému můžou být vysoké využití procesoru. Podívejte se na špiček zatížení procesoru, které odpovídají vypršení časového limitu. V důsledku vysoké využití procesoru, může se zobrazit také nejvyšší `in: XXX` hodnoty v `TimeoutException` chybové zprávy, jak je popsáno v [shlukové přenosy](#burst-of-traffic) oddílu.

> [!NOTE]
> StackExchange.Redis 1.1.603 a dále zahrnuje `local-cpu` metriky v `TimeoutException` chybové zprávy. Zkontrolujte, že jste pomocí nejnovější verze [balíček StackExchange.Redis NuGet](https://www.nuget.org/packages/StackExchange.Redis/). Existují chyby neustále opravené v kódu, aby ho robustnější k vypršení časového limitu, je důležité mít na nejnovější verzi.
> 
> 

#### <a name="resolution"></a>Řešení
Upgradovat na větší virtuální počítač s větší kapacitu procesoru nebo zjistit, co je příčinou špičky využití procesoru. 

### <a name="client-side-bandwidth-exceeded"></a>Překročení šířky pásma na straně klienta
#### <a name="problem"></a>Problém
V závislosti na architektuře klientské počítače, mohou mít omezení na jaký poměr šířky pásma sítě mají k dispozici. Pokud klient překračuje dostupnou šířku pásma přetěžováním kapacita sítě, pak dat není zpracována na straně klienta rychle odesílá na server. Tato situace může vést k vypršení časového limitu.

#### <a name="measurement"></a>Měření
Sledovat, jak se využití šířky pásma mění v čase pomocí kódu [tímto způsobem](https://github.com/JonCole/SampleCode/blob/master/BandWidthMonitor/BandwidthLogger.cs). Tento kód nemusí v některých prostředích s omezenými oprávněními (jako jsou weby Azure) úspěšně spuštěna.

#### <a name="resolution"></a>Řešení
Velikost virtuálního počítače klienta zvýšit nebo snížit využití šířky pásma sítě.

### <a name="large-requestresponse-size"></a>Velikost velkých žádostí a odpovědí
#### <a name="problem"></a>Problém
Velké žádostí a odpovědí může způsobit vypršení časového limitu. Jako příklad předpokládejme, že vaše hodnotu časového limitu, který je nakonfigurován v klientu je 1 sekunda. Vaše aplikace požaduje dva klíče (například "A" a "B") ve stejnou dobu (s použitím stejného fyzického síťového připojení). Většina klientů podporují "Pipelining" požadavků, tak, aby obě "A" a "B" odešlou požadavky na lince na server, jeden za druhým bez čekání na odpovědi. Server odesílá odpovědí zpět ve stejném pořadí. Pokud odpověď "A" je příliš velká, může vyžadovat značné množství většinu časový limit pro odeslání dalších žádostí. 

Následující příklad ukazuje tento scénář. V tomto scénáři se rychle odesílají žádosti o "A" a "B", spustí se server rychle odesílání odpovědi "A" a "B", ale kvůli doba přenosu dat, "B" zablokuje za další žádosti a vyprší i v případě, že server odpověděl rychle.

    |-------- 1 Second Timeout (A)----------|
    |-Request A-|
         |-------- 1 Second Timeout (B) ----------|
         |-Request B-|
                |- Read Response A --------|
                                           |- Read Response B-| (**TIMEOUT**)



#### <a name="measurement"></a>Měření
Tento požadavek/odpověď je obtížné k měření. V podstatě máte instrumentace váš klientský kód ke sledování velkých požadavků a odpovědí. 

#### <a name="resolution"></a>Řešení
1. Redis je optimalizovaná pro velký počet malých hodnoty, nikoli několika velké hodnoty. Preferovaným řešením je rozdělit data do související menší hodnoty. Zobrazit [co je rozsah hodnot ideální velikost pro redis? 100 KB je moc velký? ](https://groups.google.com/forum/#!searchin/redis-db/size/redis-db/n7aa2A4DZDs/3OeEPHSQBAAJ) Odeslat podrobnosti o Proč se doporučuje menší hodnoty.
2. Zvětšete velikost virtuálního počítače (pro klienta a mezipaměti Azure redis Cache serveru), získat funkce vyšší šířku pásma, snížení doby přenosu dat pro větší odpovědi. Získání větší šířku pásma na právě serveru nebo jenom na klientovi nemusí stačit. Měření využití šířky pásma a porovnání řetězce se možnosti velikosti virtuálního počítače momentálně máte.
3. Zvýšit počet `ConnectionMultiplexer` objekty je použití a požadavky kruhové dotazování přes jiné připojení.

### <a name="what-happened-to-my-data-in-redis"></a>Co se stalo s Redis má data?
#### <a name="problem"></a>Problém
Očekávání pro určité dat v Azure mezipaměť pro instanci Redis, ale neměli zdá být k dispozici.

#### <a name="resolution"></a>Řešení
Zobrazit [co se stalo se data v Redis?](https://gist.github.com/JonCole/b6354d92a2d51c141490f10142884ea4#file-whathappenedtomydatainredis-md) možné příčiny a řešení.

## <a name="server-side-troubleshooting"></a>Řešení potíží na straně serveru
Tato část popisuje řešení problémů, ke kterým dochází z důvodu stavu na serveru mezipaměti.

* [Přetížení paměti na serveru](#memory-pressure-on-the-server)
* [Vysoké využití procesoru / zátěž serveru](#high-cpu-usage-server-load)
* [Překročení šířky pásma na straně serveru](#server-side-bandwidth-exceeded)

### <a name="memory-pressure-on-the-server"></a>Přetížení paměti na serveru
#### <a name="problem"></a>Problém
Přetížení paměti na straně serveru vede na všechny typy problémů s výkonem, které můžou zdržet zpracování požadavků. Pokud narazí na tlaku na paměť, obvykle má systém k datům stránky z fyzické paměti pro virtuální paměti, která je na disku. To *stránky chybující* způsobí, že systém výrazně zpomalit. Existuje několik možných příčin této přetížení paměti: 

1. Mezipaměť plná kapacita mají naplněný daty. 
2. Redis je zobrazení velkého množství paměti fragmentace - nejčastěji způsobeny ukládání rozsáhlých objektů (Redis je optimalizovaná pro malé objekty – viz [co je rozsah hodnot ideální velikost pro redis? 100 KB je moc velký? ](https://groups.google.com/forum/#!searchin/redis-db/size/redis-db/n7aa2A4DZDs/3OeEPHSQBAAJ) Odeslat podrobnosti). 

#### <a name="measurement"></a>Měření
Redis poskytuje dvě metriky, které vám mohou pomoci zjistit potíže. První je `used_memory` a druhý je `used_memory_rss`. [Tyto metriky](cache-how-to-monitor.md#available-metrics-and-reporting-intervals) jsou k dispozici na webu Azure Portal nebo prostřednictvím [Redis informace](https://redis.io/commands/info) příkazu.

#### <a name="resolution"></a>Řešení
Existuje několik možných změn, které lze zajistit, aby byl v pořádku využití paměti:

1. [Nakonfigurujte zásady paměti](cache-configure.md#maxmemory-policy-and-maxmemory-reserved) a nastavit dobu vypršení platnosti klíče. Tato konfigurace nemusí být dostatečné, pokud máte fragmentace.
2. [Nastavení hodnoty vyhrazené maxmemory](cache-configure.md#maxmemory-policy-and-maxmemory-reserved) , který je dostatečně velký, aby se vykompenzovalo fragmentace paměti.
3. Rozdělte velké objekty v mezipaměti do menších souvisejících objektů.
4. [Škálování](cache-how-to-scale.md) na větší velikost mezipaměti.
5. Pokud používáte [mezipaměť premium s clusterem Redis povolené](cache-how-to-premium-clustering.md), můžete [zvýšit počet horizontálních dělení](cache-how-to-premium-clustering.md#change-the-cluster-size-on-a-running-premium-cache).

### <a name="high-cpu-usage--server-load"></a>Vysoké využití procesoru / zátěž serveru
#### <a name="problem"></a>Problém
Vysoké využití procesoru může znamenat, že na straně klienta může dojít k selhání zpracování odpovědi z Redis včas. i když Redis rychlé odeslané odpovědi.

#### <a name="measurement"></a>Měření
Sledovat využití procesoru široké systému prostřednictvím webu Azure Portal nebo čítače výkonu přidružený. Dejte pozor, abyste monitorování *procesu* procesoru protože jednoho procesu může mít nízké využití procesoru na stejný čas celkového systému můžou být vysoké využití procesoru. Podívejte se na špiček zatížení procesoru, které odpovídají vypršení časového limitu.

#### <a name="resolution"></a>Řešení
* Projděte si doporučení a výstrahy podle [mezipaměti Azure redis Cache Advisoru](cache-configure.md#azure-cache-for-redis-advisor).
* Projděte si také další doporučení v tomto tématu a [osvědčené postupy pro Azure redis cache](https://gist.github.com/JonCole/925630df72be1351b21440625ff2671f) zobrazíte, pokud jste použili k optimalizaci vaší mezipaměti a váš klient všechny možnosti. 
* Zkontrolujte [mezipaměti Azure Redis výkonu](cache-faq.md#azure-cache-for-redis-performance) grafy a zjistěte, zda může být v blízkosti horní prahové hodnoty pro váš aktuální úroveň. V případě potřeby [škálování](cache-how-to-scale.md) větší úroveň mezipaměti s větší kapacitou procesoru. Pokud už používáte úroveň Premium, můžete chtít [horizontální navýšení kapacity s clusteringem](cache-how-to-premium-clustering.md)


### <a name="server-side-bandwidth-exceeded"></a>Překročení šířky pásma na straně serveru
#### <a name="problem"></a>Problém
V závislosti na velikosti instance mezipaměti, mohou mít omezení na jaký poměr šířky pásma sítě mají k dispozici. Pokud server překračuje dostupnou šířku pásma, data neodesílají ke klientovi jako rychle. Tato situace může vést k vypršení časového limitu.

#### <a name="measurement"></a>Měření
Můžete monitorovat `Cache Read` metriku, která je množství dat číst z mezipaměti v MB za sekundu (MB/s) během zadaného intervalu sestavy. Tato hodnota odpovídá šířky pásma sítě používané tuto mezipaměť. Pokud chcete nastavit výstrahy pro omezení šířky pásma sítě na straně serveru, můžete vytvořit, je použití této funkce `Cache Read` čítače. Porovnejte vaše údaje s hodnotami v [Tato tabulka](cache-faq.md#cache-performance) mezí zjištěnou šířky pásma pro různé cenové úrovně a velikosti mezipaměti.

#### <a name="resolution"></a>Řešení
Pokud jste konzistentně téměř zjištěnou maximální šířka pásma pro cenovou úroveň a mezipaměti velikost, vezměte v úvahu [škálování](cache-how-to-scale.md) cenovou úroveň nebo velikost, která má větší šířku pásma sítě pomocí hodnot v [Tato tabulka](cache-faq.md#cache-performance)jako vodítko.

## <a name="stackexchangeredis-timeout-exceptions"></a>Výjimkách časového limitu StackExchange.Redis
StackExchange.Redis používá konfiguraci nastavení s názvem `synctimeout` u synchronních operací, které mají výchozí hodnota je 1000 ms. Pokud se synchronní volání nedokončí ve stanoveném čase, klient StackExchange.Redis vyvolá vypršení časového limitu, podobně jako v následujícím příkladu:

    System.TimeoutException: Timeout performing MGET 2728cc84-58ae-406b-8ec8-3f962419f641, inst: 1,mgr: Inactive, queue: 73, qu=6, qs=67, qc=0, wr=1/1, in=0/0 IOCP: (Busy=6, Free=999, Min=2,Max=1000), WORKER (Busy=7,Free=8184,Min=2,Max=8191)


Tato chybová zpráva obsahuje metriky, který vám pomůže odkazovat na příčiny a možná řešení problému. Následující tabulka obsahuje podrobnosti o chybové zprávy metriky.

| Chybová zpráva metrika | Podrobnosti |
| --- | --- |
| instrukce |V posledním časovém intervalu: byl vydán 0 příkazy |
| Mgr |Správce soketu provádí `socket.select`, což znamená, že je s výzvou k označení soketu, který souvisí; operační systém v podstatě: čtecí modul není čtení aktivně ze sítě. vzhledem k tomu, že ji nebude myslíte, že se něco udělat |
| fronta |Existují 73 celkový počet probíhajících operací |
| časový |6 v průběhu operace jsou ve frontě unsent a nebyly dosud napsány tak, aby odchozí síťové |
| qs |67 operací v průběhu byly odeslány na server, ale odpověď ještě není k dispozici. Odpovědí může být `Not yet sent by the server` nebo `sent by the server but not yet processed by the client.` |
| qc |0 nebo v průběhu operace viděli odpovědi, ale zatím ještě nebyly označeny jako dokončenou z důvodu čekání na dokončení smyčky |
| WR |Je bajtů/activewriters aktivní zapisovače (to znamená, že 6 unsent požadavky nejsou ignorovány) |
| ve |Neexistují žádné aktivní čtecí zařízení a jsou k dispozici ke čtení na síťové rozhraní bajty/activereaders nula bajtů |

### <a name="steps-to-investigate"></a>Postup k prozkoumání
1. Jako osvědčený postup, ujistěte se, používají následující vzor pro připojení při používání klienta StackExchange.Redis.

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
    ````

    Další informace najdete v tématu [připojení k mezipaměti pomocí StackExchange.Redis](cache-dotnet-how-to-use-azure-redis-cache.md#connect-to-the-cache).

1. Zajistěte, aby vaší mezipaměti Azure Redis a klientská aplikace byly ve stejné oblasti v Azure. Například se vám může zobrazovat vypršení časového limitu při vaše mezipaměť je v oblasti východní USA, ale klient je v oblasti západní USA a požadavek se nedokončil v rámci `synctimeout` interval, nebo může zobrazovat vypršení časového limitu při ladění ze svého místního vývojového počítače. 
   
    Je důrazně doporučujeme mít mezipaměti a v klientovi ve stejné oblasti Azure. Pokud máte scénáře, který obsahuje volání mezi různými oblastmi, byste měli nastavit `synctimeout` interval na hodnotu vyšší, než je výchozí interval 1000 ms zahrnutím `synctimeout` vlastnost připojovacího řetězce. Následující příklad ukazuje StackExchange.Azure mezipaměti Redis připojovací řetězec fragmentu s `synctimeout` 2000 MS.
   
        synctimeout=2000,cachename.redis.cache.windows.net,abortConnect=false,ssl=true,password=...
2. Zkontrolujte, že jste pomocí nejnovější verze [balíček StackExchange.Redis NuGet](https://www.nuget.org/packages/StackExchange.Redis/). Existují chyby neustále opravené v kódu, aby ho robustnější k vypršení časového limitu, je důležité mít na nejnovější verzi.
3. Pokud jsou požadavky, které jsou získávání vázány omezení šířky pásma na serveru nebo klienta, trvá déle pro ně dokončí, a tím způsobit překročení časového limitu. Pokud je vaše časový limit šířky pásma sítě na serveru najdete v tématu [šířky pásma na straně serveru překročil](#server-side-bandwidth-exceeded). Chcete-li zjistit, jestli vaše vypršení časového limitu šířky pásma sítě klienta, přečtěte si téma [šířky pásma na straně klienta překročil](#client-side-bandwidth-exceeded).
4. Můžete získat procesoru na serveru nebo na straně klienta jsou vázány?
   
   * Zaškrtněte, pokud je získání zavazujete se k jejich využití procesoru na klientovi, což by mohlo způsobit žádost proto nebyla zpracována v rámci `synctimeout` intervalu, což způsobuje vypršení časového limitu. Přechod na větší velikost klienta nebo distribuci zatížení může pomoci řídit tento problém. 
   * Zkontrolujte, jestli se zobrazuje procesoru vázány na serveru pro monitorování `CPU` [mezipaměti metrika výkonu](cache-how-to-monitor.md#available-metrics-and-reporting-intervals). Požadavky přicházející Redis je závislá na procesoru může způsobit vypršení časového limitu tyto požadavky. Chcete-li vyřešit tuto podmínku, můžete distribuovat zatížení mezi několika horizontálními oddíly v cache ve verzi premium nebo upgradovat na větší velikosti nebo cenové úrovně. Další informace najdete v tématu [překročení šířky pásma na straně serveru](#server-side-bandwidth-exceeded).
5. Existují příkazů trvá dlouhou dobu zpracování na serveru? Dlouho běžící příkazy, které trvá dlouhou dobu zpracování na serveru redis může způsobit vypršení časového limitu. Tady je několik příkladů příkazů dlouho běžící `mget` s velkým počtem klíče, `keys *` nebo chybně napsané skripty lua. Můžete připojit ke své mezipaměti Azure pro instanci Redis pomocí klienta redis rozhraní příkazového řádku nebo můžete použít [konzola Redis](cache-configure.md#redis-console) a spustit [SlowLog](https://redis.io/commands/slowlog) příkazu zkontrolujte, jestli požadavků trvá déle, než se očekávalo. Redis Server a StackExchange.Redis jsou optimalizované pro velký počet malých požadavků spíše než méně velkých požadavků. Rozdělení dat do menších bloků zvýšit zde věci. 
   
    Informace o připojení k mezipaměti Azure redis Cache SSL koncový bod pomocí rozhraní příkazového řádku redis a stunnelu, najdete v článku [oznamujeme zprostředkovatel stavu relací ASP.NET pro redis Cache ve verzi Preview](https://blogs.msdn.com/b/webdev/archive/2014/05/12/announcing-asp-net-session-state-provider-for-redis-preview-release.aspx) blogový příspěvek. Další informace najdete v tématu [SlowLog](https://redis.io/commands/slowlog).
6. Vysoké zatížení serveru Redis může způsobit vypršení časového limitu. Zatížení serveru můžete monitorovat pomocí monitorování `Redis Server Load` [mezipaměti metrika výkonu](cache-how-to-monitor.md#available-metrics-and-reporting-intervals). Zatížení serveru 100 (maximální hodnota) znamená, že redis server byla zaneprázdněno žádné určitou dobu nečinné, zpracování požadavků. Pokud chcete zobrazit, pokud některé požadavky spotřebovávají všechny funkce serveru, spusťte příkaz SlowLog, jak je popsáno v předchozím odstavci. Další informace najdete v tématu [vysoké využití procesoru / Server načíst](#high-cpu-usage-server-load).
7. Pokusil se jakákoli jiná událost na straně klienta, která by mohla způsobit blip sítě? Na straně klienta (web, role pracovního procesu nebo Virtuálním počítači IaaS) zkontrolujte, jestli došlo k události, jako je počet instancí klientů, kteří škálování směrem nahoru nebo dolů, nebo nasazení nové verze klienta nebo automatické škálování je povolené? V našich testech jsme našli tento automatické škálování nebo vertikální navýšení kapacity/dolů můžete příčina odchozího síťového připojení může dojít ke ztrátě několik sekund. Kód StackExchange.Redis je odolný vůči tyto události a znovu připojí. Během této doby opětovného připojení všech požadavků ve frontě můžete vypršení časového limitu.
8. Pokusil se žádost o velké objemy před několika malých požadavků do mezipaměti Azure pro Redis, který vypršel časový limit? Parametr `qs` v chybě se zpráva, že počet požadavků odeslaných z klienta na server, ale ještě zpracovány odpověď. Tuto hodnotu můžete pořád rostou, protože StackExchange.Redis používá jedno připojení TCP a může číst pouze jednu odpověď najednou. I v případě, že první operace vypršení časového limitu, ale nezastaví datech odesílaných ze serveru a další požadavky jsou blokovány, dokud velké žádosti o dokončení, způsobí časové limity. Jedním z řešení je minimalizovat riziko vypršení časového limitu pro zajištění, že vaše mezipaměť je příliš velká pro vaše úlohy a rozdělení do menších bloků velké hodnoty. Další možnou příčinou je použití fondu `ConnectionMultiplexer` objekty v klientovi a zvolte nejméně načíst `ConnectionMultiplexer` při odeslání nového požadavku. To by měl jeden časový limit zabránit v způsobí ostatní požadavky také vypršení časového limitu.
9. Pokud používáte `RedisSessionStateProvider`, ujistěte se, jste správně nastavili časový limit opakování. `retryTimeoutInMilliseconds` musí být vyšší než `operationTimeoutInMilliseconds`, jinak dojde k žádné opakování. V následujícím příkladu `retryTimeoutInMilliseconds` je nastavena na 3000. Další informace najdete v tématu [zprostředkovatel stavu relací ASP.NET pro Azure Cache pro Redis](cache-aspnet-session-state-provider.md) a [jak používat parametry konfigurace zprostředkovatele stavu relace a poskytovatel výstupní mezipaměti](https://github.com/Azure/aspnet-redis-providers/wiki/Configuration).

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


1. Zkontrolujte využití paměti na ukládání do mezipaměti Azure Redis serveru podle [monitorování](cache-how-to-monitor.md#available-metrics-and-reporting-intervals) `Used Memory RSS` a `Used Memory`. Pokud zásadu vyřazení je na místě, Redis spustí schopnost reagovat umožňuje vyhnání klíče při `Used_Memory` dosáhne velikosti mezipaměti. V ideálním případě `Used Memory RSS` by mělo být pouze mírně vyšší než `Used memory`. Velký rozdíl znamená, že existuje fragmentace paměti (interní nebo externí). Když `Used Memory RSS` je menší než `Used Memory`, to znamená, že část paměti mezipaměti byla vyměnit provozu v operačním systému. Pokud toto prohození dojde, můžete očekávat některé významné latenci. Protože Redis nemá kontrolu nad jeho přidělení zpřístupněných paměťových stránek, Vysoká `Used Memory RSS` je často výsledkem špička využití paměti. Pokud Redis uvolní paměť, alokátoru je paměť přidělena zpět a alokátoru může nebo nemusí poskytnout paměť zpět do systému. Může být rozdíl mezi `Used Memory` hodnotu nebo paměti využití podle operačního systému. To může být z důvodu skutečnost byl paměti používá a vydání redis, ale není zadaný zpět do systému. Pro zmírnění problémů s pamětí, můžete provést následující kroky:
   
   * Upgradujte do mezipaměti na větší velikost tak, že nejsou spuštěné mimo omezení paměti v systému.
   * Nastavte dobu vypršení platnosti klíčů tak, aby starší hodnoty se vyřadí aktivní.
   * Monitorování `used_memory_rss` metriky mezipaměti. Když se tato hodnota blíží velikosti mezipaměti, budete pravděpodobně začnou zobrazovat problémy s výkonem. Pokud používáte mezipaměť premium, nebo upgradujte na větší velikost mezipaměti, Distribuujte data mezi několika horizontálními oddíly.
   
   Další informace najdete v tématu [přetížení paměti na serveru](#memory-pressure-on-the-server).

## <a name="additional-information"></a>Další informace
* [Jaké mezipaměti Azure Redis a jeho velikost mám použít?](cache-faq.md#what-azure-cache-for-redis-offering-and-size-should-i-use)
* [Jak můžu srovnávací testy a testování výkonu mezipaměť?](cache-faq.md#how-can-i-benchmark-and-test-the-performance-of-my-cache)
* [Jak mohu spustit příkazy Redis?](cache-faq.md#how-can-i-run-redis-commands)
* [Jak monitorovat Azure mezipaměti Redis](cache-how-to-monitor.md)

