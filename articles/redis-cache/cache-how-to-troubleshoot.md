---
title: Řešení potíží s Azure Redis Cache | Microsoft Docs
description: Zjistěte, jak řešit obvyklé problémy s Azure Redis Cache.
services: redis-cache
documentationcenter: ''
author: wesmc7777
manager: cfowler
editor: ''
ms.assetid: 928b9b9c-d64f-4252-884f-af7ba8309af6
ms.service: cache
ms.workload: tbd
ms.tgt_pltfrm: cache-redis
ms.devlang: na
ms.topic: article
ms.date: 01/06/2017
ms.author: wesmc
ms.openlocfilehash: e5f6f423697d90e889ebde2cd203891e34278b3c
ms.sourcegitcommit: eeb5daebf10564ec110a4e83874db0fb9f9f8061
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/03/2018
ms.locfileid: "28984567"
---
# <a name="how-to-troubleshoot-azure-redis-cache"></a>Řešení potíží s Azure Redis Cache
Tento článek obsahuje pokyny pro řešení potíží s následující kategorie problémy s Azure Redis Cache.

* [Řešení potíží s klienta](#client-side-troubleshooting) – Tato část obsahuje pokyny pro identifikaci a řešení potíží se nezdařila z důvodu aplikace připojení k Azure Redis Cache.
* [Řešení potíží s serverové](#server-side-troubleshooting) – Tato část obsahuje pokyny pro identifikaci a řešení potíží se nezdařila z důvodu na straně serveru Azure Redis Cache.
* [Výjimkám časového limitu StackExchange.Redis](#stackexchangeredis-timeout-exceptions) – Tato část obsahuje informace o odstraňování problémů při používání klienta StackExchange.Redis.

> [!NOTE]
> Některé kroky uvedené v této příručce obsahovat pokyny ke spuštění příkazů Redis a monitorování různých metrik výkonu. Další informace a pokyny najdete v článcích v [Další informace o](#additional-information) části.
> 
> 

## <a name="client-side-troubleshooting"></a>Řešení potíží s straně klienta
Tato část popisuje řešení problémů, ke kterým dochází kvůli podmínce na klientské aplikaci.

* [Přetížení paměti na straně klienta](#memory-pressure-on-the-client)
* [Shluků provozu](#burst-of-traffic)
* [Klient vysoké využití procesoru](#high-client-cpu-usage)
* [Překročení šířky pásma na straně klienta](#client-side-bandwidth-exceeded)
* [Velikost velké požadavků a odpovědí](#large-requestresponse-size)
* [Co se stalo s mým datům v Redis?](#what-happened-to-my-data-in-redis)

### <a name="memory-pressure-on-the-client"></a>Přetížení paměti na straně klienta
#### <a name="problem"></a>Problém
Přetížení paměti v klientském počítači vede k nejrůznějších druhy problémy s výkonem, které můžou zdržet zpracování dat, který vám byl zaslán instance Redis bez jakéhokoli zpoždění. Pokud se dotkne přetížení paměti, systém obvykle má k datům stránky, od fyzické paměti na virtuální paměti, která je na disku. To *stránky chybující* způsobí, že systém výrazně zpomalit.

#### <a name="measurement"></a>Měření
1. Monitorování využití paměti v počítači a ujistěte se, že nepřekročí dostupné paměti. 
2. Monitorování `Page Faults/Sec` čítače výkonu. Většina systémů mají některé chyb stránek i při běžném provozu, takže sledování špiček v tomto čítači výkonu chyb stránky, odpovídajících s vypršení časových limitů.

#### <a name="resolution"></a>Řešení
Upgrade vašeho klienta ke klientovi větší velikost virtuálního počítače s více paměti nebo proniknout do vašeho vzory využití paměti ke snížení využití paměti.

### <a name="burst-of-traffic"></a>Shluků provozu
#### <a name="problem"></a>Problém
Shluky provozu v kombinaci s nízká `ThreadPool` nastavení může vést k prodlevám při zpracování dat již odesílané serverem Redis, ale ještě nebyla použije na straně klienta.

#### <a name="measurement"></a>Měření
Monitorování jak vaše `ThreadPool` časem pomocí kódu změnit statistiky [podobné výjimky](https://github.com/JonCole/SampleCode/blob/master/ThreadPoolMonitor/ThreadPoolLogger.cs). Můžete také zobrazit `TimeoutException` zprávu od StackExchange.Redis. Zde naleznete příklad:

    System.TimeoutException: Timeout performing EVAL, inst: 8, mgr: Inactive, queue: 0, qu: 0, qs: 0, qc: 0, wr: 0, wq: 0, in: 64221, ar: 0, 
    IOCP: (Busy=6,Free=999,Min=2,Max=1000), WORKER: (Busy=7,Free=8184,Min=2,Max=8191)

V předchozí zprávě existují několik problémů, které jsou zajímavé:

1. Všimněte si, že v `IOCP` části a `WORKER` části máte `Busy` hodnotu, která je větší než `Min` hodnotu. Tento rozdíl znamená, že vaše `ThreadPool` nastavení je třeba úprava.
2. Můžete také zjistit `in: 64221`. Tato hodnota označuje, že byly přijaty ve vrstvě soketu jádra 64,211 bajtů, ale nebyly přečteny aplikací (například StackExchange.Redis). Tento rozdíl obvykle znamená, že aplikace není čtení dat ze sítě serveru je odeslání vám rychle.

#### <a name="resolution"></a>Řešení
Konfigurace vaší [nastavení fondu podprocesů](https://gist.github.com/JonCole/e65411214030f0d823cb) a ujistěte se, že se váš fond vláken škálování rychle v části burst scénáře.

### <a name="high-client-cpu-usage"></a>Klient vysoké využití procesoru
#### <a name="problem"></a>Problém
Vysoké využití procesoru na straně klienta je znamená, že systém nedokáže držet krok s práci, kterou byla požádána k provedení. Tato situace znamená, že klient může fungovat zpracovat včas odpověď od Redis, i když Redis rychle odeslané odpovědi.

#### <a name="measurement"></a>Měření
Sledování využití procesoru široké systému prostřednictvím portálu Azure nebo čítače výkonu přidružený. Dejte pozor, abyste monitorování *proces* procesoru, protože v jednom procesu mohou být nízké využití procesoru ve stejné čas této celého systému procesoru může být vysoká. Sledování špiček využití procesoru, které odpovídají s vypršení časových limitů. V důsledku vysoké využití procesoru, může se také zobrazit základní `in: XXX` hodnoty v `TimeoutException` chybové zprávy, jak je popsáno v [shluků provozu](#burst-of-traffic) části.

> [!NOTE]
> StackExchange.Redis 1.1.603 a dále zahrnuje `local-cpu` metriky v `TimeoutException` chybové zprávy. Ujistěte se, používáte nejnovější verzi [balíčku StackExchange.Redis NuGet](https://www.nuget.org/packages/StackExchange.Redis/). Existují opravených neustále se kód, který ho robustnější do vypršení časových limitů, nejnovější verze je důležité.
> 
> 

#### <a name="resolution"></a>Řešení
Upgrade na větší velikost virtuálního počítače s větší kapacitu procesoru nebo zjistěte, co ho způsobuje vzroste využití procesoru. 

### <a name="client-side-bandwidth-exceeded"></a>Překročení šířky pásma na straně klienta
#### <a name="problem"></a>Problém
V závislosti na architektuře klientské počítače, můžou mít omezení na tom, kolik šířku pásma sítě mají k dispozici. Pokud klient překračuje dostupnou šířku pásma pomocí přetížení dostatečnou kapacitu sítě, pak není zpracování dat na straně klienta rychle odesílá na server. Tato situace může dojít k vypršení časových limitů.

#### <a name="measurement"></a>Měření
Monitorování, jak změnit použití šířky pásma v čase pomocí kódu [podobné výjimky](https://github.com/JonCole/SampleCode/blob/master/BandWidthMonitor/BandwidthLogger.cs). Tento kód se nemusí úspěšně spustit v některých prostředích s omezenými oprávněními (například weby Azure).

#### <a name="resolution"></a>Řešení
Zvětšete velikost virtuálního počítače klienta nebo snížení využití šířky pásma sítě.

### <a name="large-requestresponse-size"></a>Velikost velké požadavků a odpovědí
#### <a name="problem"></a>Problém
Velké požadavků a odpovědí může způsobit překročení časového limitu. Předpokládejme například, klientem hodnota časového limitu je 1 sekunda. Vaše aplikace požaduje dva klíče (například "A" a "B") ve stejnou dobu (pomocí stejné fyzické síti připojení). Většina klienti podporují "Pipelining" požadavků, tak, aby obě "A" a "B" jsou odeslání požadavků v drátové síti k serveru, jedna po druhé bez čekání odezvu. Server odešle odpovědi zpět ve stejném pořadí. Pokud je odpověď "A" dostatečně velké na to, můžete vyžadovat značné množství většinu časového limitu pro následné požadavky. 

Následující příklad ukazuje, tento scénář. V tomto scénáři jsou rychle odeslat žádost o "A" a "B", server se spustí rychle odesílání odpovědí "A" a "B", ale kvůli doba přenosu dat, "B" uváznout za dalších požadavků a časy se to i v případě, že rychle odpověď serveru.

    |-------- 1 Second Timeout (A)----------|
    |-Request A-|
         |-------- 1 Second Timeout (B) ----------|
         |-Request B-|
                |- Read Response A --------|
                                           |- Read Response B-| (**TIMEOUT**)



#### <a name="measurement"></a>Měření
Tento požadavek a odpověď je obtížné k měření. Máte v podstatě instrumentace váš klientský kód ke sledování velké požadavky a odpovědi. 

#### <a name="resolution"></a>Řešení
1. Redis je optimalizovaná pro velké množství malých hodnoty, nikoli několik velkých hodnot. Upřednostňované řešením je rozdělit data do související menší hodnoty. Najdete v článku [co je velikost rozsah hodnot ideální pro redis? Je příliš velký 100 KB? ](https://groups.google.com/forum/#!searchin/redis-db/size/redis-db/n7aa2A4DZDs/3OeEPHSQBAAJ) post podrobnosti kolem Proč se doporučují menší hodnoty.
2. Zvětšete velikost virtuálního počítače (pro klienta a serveru Redis mezipaměti), získat vyšší možnosti šířky pásma, snižuje doba přenosu dat pro větší odpovědi. Získávání větší šířku pásma na právě serveru nebo jenom na klienta nemusí stačit. Měření použití šířky pásma a porovnejte je s možností velikost virtuálního počítače, které máte aktuálně.
3. Zvyšte počet `ConnectionMultiplexer` objekty můžete použít a kruhového dotazování požadavky přes jiné připojení.

### <a name="what-happened-to-my-data-in-redis"></a>Co se stalo s mým datům v Redis?
#### <a name="problem"></a>Problém
Očekávání pro určité instance Moje Azure Redis Cache jde data, ale nebyla zdá být k dispozici.

#### <a name="resolution"></a>Řešení
V tématu [co se stalo s mým datům v Redis?](https://gist.github.com/JonCole/b6354d92a2d51c141490f10142884ea4#file-whathappenedtomydatainredis-md) možné příčiny a řešení.

## <a name="server-side-troubleshooting"></a>Řešení potíží s straně serveru
Tato část popisuje řešení problémů, ke kterým dochází z důvodu stavu na serveru mezipaměti.

* [Přetížení paměti na serveru](#memory-pressure-on-the-server)
* [Vysoké využití procesoru / Server načíst](#high-cpu-usage-server-load)
* [Překročení šířky pásma straně serveru](#server-side-bandwidth-exceeded)

### <a name="memory-pressure-on-the-server"></a>Přetížení paměti na serveru
#### <a name="problem"></a>Problém
Přetížení paměti na straně serveru vede k nejrůznějších druhy problémy s výkonem, které můžou zdržet zpracování požadavků. Pokud se dotkne přetížení paměti, systém obvykle má k datům stránky, od fyzické paměti na virtuální paměti, která je na disku. To *stránky chybující* způsobí, že systém výrazně zpomalit. Existuje několik možných příčin této přetížení paměti: 

1. Jste vyplnili mezipaměti, aby kapacita s daty. 
2. Redis se zobrazuje fragmentace paměti vysoké - nejčastěji způsobeno ukládání velkých objektů (Redis je optimalizovaná pro malé objekty - najdete [co je velikost rozsah hodnot ideální pro redis? Je příliš velký 100 KB? ](https://groups.google.com/forum/#!searchin/redis-db/size/redis-db/n7aa2A4DZDs/3OeEPHSQBAAJ) post podrobnosti). 

#### <a name="measurement"></a>Měření
Redis zpřístupní dvě metriky, které pomáhají identifikovat potíže. První je `used_memory` a druhá je `used_memory_rss`. [Tyto metriky](cache-how-to-monitor.md#available-metrics-and-reporting-intervals) jsou k dispozici na portálu Azure nebo prostřednictvím [Redis informace](http://redis.io/commands/info) příkaz.

#### <a name="resolution"></a>Řešení
Existuje několik možných změny, které můžete provést zajistit, aby byl v pořádku využití paměti:

1. [Nakonfigurujte zásady paměti](cache-configure.md#maxmemory-policy-and-maxmemory-reserved) a nastavit čas vypršení platnosti na vaše klíče. Tato konfigurace nemusí být dostačující, pokud máte fragmentace.
2. [Nakonfigurovat hodnotu vyhrazené maxmemory](cache-configure.md#maxmemory-policy-and-maxmemory-reserved) , je dostatečně velký pro kompenzovat fragmentace paměti.
3. Rozdělte vaší rozsáhlé objekty uložené v mezipaměti do menších související objekty.
4. [Škálování](cache-how-to-scale.md) větší velikost mezipaměti.
5. Pokud používáte [premium mezipaměť Redis clusteru povolena](cache-how-to-premium-clustering.md), můžete [zvýšit počet horizontálních oddílů](cache-how-to-premium-clustering.md#change-the-cluster-size-on-a-running-premium-cache).

### <a name="high-cpu-usage--server-load"></a>Vysoké využití procesoru / Server načíst
#### <a name="problem"></a>Problém
Vysoké využití procesoru může znamenat, že na straně klienta se nemusí zdařit zpracovat včas odpověď od Redis, i když Redis rychle odeslané odpovědi.

#### <a name="measurement"></a>Měření
Sledování využití procesoru široké systému prostřednictvím portálu Azure nebo čítače výkonu přidružený. Dejte pozor, abyste monitorování *proces* procesoru, protože v jednom procesu mohou být nízké využití procesoru ve stejné čas této celého systému procesoru může být vysoká. Sledování špiček využití procesoru, které odpovídají s vypršení časových limitů.

#### <a name="resolution"></a>Řešení
* Zkontrolujte doporučení a výstrahy podle [Redis Cache Advisor](cache-configure.md#redis-cache-advisor).
* Také zkontrolujte doporučení v tomto tématu a [osvědčené postupy pro Azure Redis](https://gist.github.com/JonCole/925630df72be1351b21440625ff2671f) chcete zobrazit, pokud jste použili všechny možnosti Optimalizovat vaší mezipaměti a vašeho klienta. 
* Zkontrolujte [výkonu Azure Redis Cache](cache-faq.md#azure-redis-cache-performance) grafů a zjistěte, zda se bude v blízkosti horní prahové hodnoty pro vaše současná vrstva. V případě potřeby [škálování](cache-how-to-scale.md) na vyšší úroveň mezipaměti s větší kapacitu procesoru. Pokud už používáte úroveň Premium, můžete chtít [horizontální navýšení kapacity s clustering](cache-how-to-premium-clustering.md)


### <a name="server-side-bandwidth-exceeded"></a>Překročení šířky pásma straně serveru
#### <a name="problem"></a>Problém
V závislosti na velikosti instance mezipaměti, mohou mít omezení na tom, kolik šířku pásma sítě mají k dispozici. Pokud server překračuje dostupnou šířku pásma, data neodesílají klientovi jako rychle. Tato situace může dojít k vypršení časových limitů.

#### <a name="measurement"></a>Měření
Můžete sledovat `Cache Read` metriku, což je množství dat načten z mezipaměti v MB za sekundu (MB/s) během zadaného intervalu sestavy. Tato hodnota odpovídá šířku pásma sítě používané této mezipaměti. Pokud chcete nastavit výstrahy pro omezení šířky pásma sítě na straně serveru, můžete je vytvořit pomocí této `Cache Read` čítače. Porovnání s hodnotami ve vašem odečty [Tato tabulka](cache-faq.md#cache-performance) mezí zjištěnou šířky pásma pro různé mezipaměti cenové úrovně a velikosti.

#### <a name="resolution"></a>Řešení
Pokud jste konzistentně téměř zjištěnou maximální šířka pásma pro cenovou úroveň a mezipaměti velikost, vezměte v úvahu [škálování](cache-how-to-scale.md) cenovou úroveň nebo velikost, která má větší šířku pásma sítě, pomocí hodnoty v [Tato tabulka](cache-faq.md#cache-performance) jako vodítko.

## <a name="stackexchangeredis-timeout-exceptions"></a>Výjimkám časového limitu StackExchange.Redis
Název nastavení používá StackExchange.Redis `synctimeout` pro synchronní operace, které mají výchozí hodnota je 1000 ms. Pokud synchronní volání nedokončí stanovené včas, vyvolá klienta StackExchange.Redis vypršení časového limitu, podobně jako v následujícím příkladu:

    System.TimeoutException: Timeout performing MGET 2728cc84-58ae-406b-8ec8-3f962419f641, inst: 1,mgr: Inactive, queue: 73, qu=6, qs=67, qc=0, wr=1/1, in=0/0 IOCP: (Busy=6, Free=999, Min=2,Max=1000), WORKER (Busy=7,Free=8184,Min=2,Max=8191)


Tato chybová zpráva obsahuje metriky, které může pomoci, přejděte na příčina a jejich možná řešení problému. Následující tabulka obsahuje podrobnosti o metriku chybová zpráva.

| Metrika chybová zpráva | Podrobnosti |
| --- | --- |
| INST |V posledním časovém intervalu: 0 příkazy vydané |
| Mgr |Správce soketu provádí `socket.select`, což znamená, že je dotazem operačního systému k označení soketu, který má něco udělat; v podstatě: čtečka není čtení aktivně ze sítě. vzhledem k tomu, že není myslíte, je něco udělat |
| Fronty |73 celkový průběh operace |
| qu |6 v průběhu operace jsou ve frontě neodeslaných a nebyly dosud zapsány do odchozí sítě |
| qs |67 probíhající operace, které byly odeslány na server, ale odpověď ještě není k dispozici. Odpovědí může být `Not yet sent by the server` nebo`sent by the server but not yet processed by the client.` |
| qc |0 v průběhu operací viděli jste, odpoví, ale ještě nebyly byl označen jako dokončení z důvodu čekání na dokončení smyčky |
| wr |Je bajtů/activewriters active zapisovače (což znamená, že nejsou ignorovány 6 neodeslaných požadavky) |
| ve |Neexistují žádné aktivní čtečky a nulový počet bajtů, které jsou k dispozici ke čtení v bajtech/activereaders síťový adaptér |

### <a name="steps-to-investigate"></a>Kroky k prozkoumání
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

    Další informace najdete v tématu [připojení k mezipaměti StackExchange.Redis](cache-dotnet-how-to-use-azure-redis-cache.md#connect-to-the-cache).

1. Zajistěte, aby Azure Redis Cache a klientská aplikace byly ve stejné oblasti v Azure. Například se vám může zobrazovat časové limity při vaše mezipaměť je ve východní USA, ale klient nachází v západní USA a není v rámci dokončení požadavku `synctimeout` interval, nebo může být získávání překročení časového limitu při ladění z místním vývojovém počítači. 
   
    Má důrazně doporučujeme mít mezipaměti a v klientovi ve stejné oblasti Azure. Pokud máte scénáře, který zahrnuje volání různých oblastí, byste měli nastavit `synctimeout` interval na hodnotu vyšší, než je výchozí interval 1000 ms zahrnutím `synctimeout` vlastnost v připojovacím řetězci. Následující příklad ukazuje fragment StackExchange.Redis mezipaměti připojovací řetězec s `synctimeout` z 2000 ms.
   
        synctimeout=2000,cachename.redis.cache.windows.net,abortConnect=false,ssl=true,password=...
2. Ujistěte se, používáte nejnovější verzi [balíčku StackExchange.Redis NuGet](https://www.nuget.org/packages/StackExchange.Redis/). Existují opravených neustále se kód, který ho robustnější do vypršení časových limitů, nejnovější verze je důležité.
3. Pokud jsou požadavky, které jsou získávání svázaná s omezení šířky pásma na serveru nebo klienta, trvá déle na jejich dokončení a tím způsobit překročení časového limitu. Pokud je vaše časový limit šířky pásma sítě na serveru najdete v tématu [šířky pásma serveru straně překročena](#server-side-bandwidth-exceeded). Pokud je vaše časový limit šířky pásma sítě klienta najdete v tématu [překročení šířky pásma na straně klienta](#client-side-bandwidth-exceeded).
4. Můžete získávání procesoru na serveru nebo na straně klienta je vázána?
   
   * Zkontrolujte, pokud jste se získávání svázaná s procesoru na vašeho klienta, což by mohlo způsobit požadavek nelze zpracovat v rámci `synctimeout` interval, což způsobuje vypršení časového limitu. Přechod na větší velikost klienta nebo distribuci zatížení vám může pomoct řídit tento problém. 
   * Zkontrolujte, jestli se zobrazuje procesoru vázaný na serveru pomocí monitorování `CPU` [mezipaměti metrika výkonu](cache-how-to-monitor.md#available-metrics-and-reporting-intervals). Požadavky přicházející Redis je vázán procesoru může způsobit těchto požadavků do vypršení časového limitu. Chcete-li vyřešit tento stav, můžete rozdělit zatížení mezi víc horizontálních oddílů v mezipaměti premium nebo upgradujte na větší velikost nebo cenovou úroveň. Další informace najdete v tématu [překročení šířky pásma serveru straně](#server-side-bandwidth-exceeded).
5. Existují příkazy trvá dlouhou dobu ke zpracování na serveru? Dlouho běžící příkazy, které trvá dlouhou dobu ke zpracování na serveru redis může způsobit překročení časového limitu. Některé příklady příkazů dlouhotrvající `mget` s velkým počtem klíče, `keys *` nebo chybně napsané skripty lua. Můžete se připojit k vaší instanci Azure Redis Cache pomocí rozhraní příkazového řádku redis klienta nebo používat [konzola Redis](cache-configure.md#redis-console) a spusťte [SlowLog](http://redis.io/commands/slowlog) příkaz, zobrazí, pokud jsou požadavky trvá déle, než se očekávalo. Serveru redis a StackExchange.Redis jsou optimalizované pro mnoho malých požadavků místo méně velké požadavky. Rozdělení na menší bloky dat může zvýšit věcí sem. 
   
    Informace o připojení ke koncovému bodu Azure Redis Cache SSL pomocí rozhraní příkazového řádku redis a stunnel najdete v tématu [uvedení ASP.NET poskytovatele stavu relace pro Redis verze Preview](http://blogs.msdn.com/b/webdev/archive/2014/05/12/announcing-asp-net-session-state-provider-for-redis-preview-release.aspx) příspěvku na blogu. Další informace najdete v tématu [SlowLog](http://redis.io/commands/slowlog).
6. Vysoké zatížení serveru Redis může způsobit překročení časového limitu. Zatížení serveru můžete monitorovat pomocí monitorování `Redis Server Load` [mezipaměti metrika výkonu](cache-how-to-monitor.md#available-metrics-and-reporting-intervals). Zatížení serveru 100 (maximální hodnota) označuje, že serveru redis byl zaneprázdněn prováděním žádné doba nečinnosti, po zpracování požadavků. Pokud chcete zobrazit, pokud jsou některé žádosti o zabírá tak všechny funkce serveru, spusťte příkaz SlowLog, jak je popsáno v předchozím odstavci. Další informace najdete v tématu [vysoké zatížení CPU / Server načíst](#high-cpu-usage-server-load).
7. Byl na straně klienta, která by mohla způsobit sítě blip jiná událost? Pokud se například událost škálování počtu instancí klientů nahoru nebo dolů, nebo nasazení nové verze klienta nebo automatické škálování je povolené, zkontrolujte na straně klienta (web, role pracovního procesu nebo virtuálních počítačů Iaas)? V našich testech jsme našli této škálování nebo škálování nebo dolů můžete příčina odchozího síťového připojení může dojít ke ztrátě pro několik sekund. Kód StackExchange.Redis odolný proti takové události a znovu připojí. Během této doby opětovného připojení všech požadavků ve frontě můžete vypršení časového limitu.
8. Byla žádost big předcházející několik malých požadavků k Redis Cache, který vypršel časový limit? Parametr `qs` v chybě zpráva znamená, kolik žádosti byly odeslány z klienta na server, ale nebyly dosud zpracovány odpověď. Tuto hodnotu můžete zachovat narůstají, protože StackExchange.Redis používá jednoho připojení TCP a může číst pouze jedna odpověď najednou. To i v případě, že vypršel časový limit operace první, ale nezastaví dat odeslaných ze serveru a ostatní požadavky jsou zablokované dokončení velkého požadavku způsobuje časové limity. Jedno řešení je omezit možnost vypršení časových limitů zajistíte, že vaše mezipaměť je dostatečně velký pro úlohy a rozdělení na menší bloky velké hodnoty. Další možnou příčinou je používat fond `ConnectionMultiplexer` objekty v vašeho klienta a vyberte aspoň načíst `ConnectionMultiplexer` při odesílání novou žádost. To by měl jeden časový limit zabránit v způsobuje ostatních požadavků na taky časový limit.
9. Pokud používáte `RedisSessionStateprovider`, ujistěte se, jste správně nastavili časový limit opakování. `retrytimeoutInMilliseconds`musí být vyšší než `operationTimeoutinMilliseonds`, jinak mohlo dojít žádné opakování. V následujícím příkladu `retrytimeoutInMilliseconds` je nastaven na 3000. Další informace najdete v tématu [poskytovatele stavu relace ASP.NET pro Azure Redis Cache](cache-aspnet-session-state-provider.md) a [jak používat parametry konfigurace poskytovatele stavu relace a poskytovatel výstupní mezipaměti](https://github.com/Azure/aspnet-redis-providers/wiki/Configuration).

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


1. Zkontrolujte využití paměti na serveru Azure Redis Cache pomocí [monitorování](cache-how-to-monitor.md#available-metrics-and-reporting-intervals) `Used Memory RSS` a `Used Memory`. Pokud zásady vyřazení je v místě, Redis spustí při vyřazení klíče `Used_Memory` dosáhne velikosti mezipaměti. V ideálním případě `Used Memory RSS` by měly být pouze mírně větší, než `Used memory`. Velký rozdíl znamená, že fragmentace paměti (interní nebo externí. Když `Used Memory RSS` je menší než `Used Memory`, znamená to, součástí paměti mezipaměti byla vzájemně zaměněny v operačním systému. Pokud tato odkládací dojde, můžete očekávat některé důležité latenci. Protože Redis nemá ovládat, jak jsou jeho přidělení mapované na paměťových stránek, vysoké `Used Memory RSS` je často výsledek špička využití paměti. Když Redis uvolní paměť, velikost paměti je uveden zpět k přidělujícího modulu a přidělujícího modulu může nebo nemusí poskytnout paměť zpět do systému. Může být v rozporu `Used Memory` hodnota a paměti spotřeby jsou uvedeny v operačním systému. To může být z důvodu fakt, na který byla používá paměti a vydané ve Redis, ale není zadaný zpátky do systému. Pro zmírnění problémy s pamětí, můžete provést následující kroky:
   
   * Upgrade na větší velikost mezipaměti, tak, že nejsou spuštěné zobrazení omezení paměti v systému.
   * Nastavit dobu vypršení platnosti na klíče tak, aby starší hodnoty jsou proaktivně vyřazování.
   * Monitorování `used_memory_rss` metrika do mezipaměti. Když se tato hodnota blíží velikosti mezipaměti, budete pravděpodobně začnou problémy s výkonem. Pokud používáte cache ve verzi premium, nebo upgradujte na větší velikost mezipaměti, distribuci dat mezi víc horizontálních oddílů.
   
   Další informace najdete v tématu [přetížení paměti na serveru](#memory-pressure-on-the-server).

## <a name="additional-information"></a>Další informace
* [Jaké nabídky a velikosti Redis Cache mám použít?](cache-faq.md#what-redis-cache-offering-and-size-should-i-use)
* [Jak můžete otestovat a testování výkonu Moje mezipaměti?](cache-faq.md#how-can-i-benchmark-and-test-the-performance-of-my-cache)
* [Jak můžete spouštět příkazy Redis?](cache-faq.md#how-can-i-run-redis-commands)
* [Tom, jak monitorovat Azure Redis Cache](cache-how-to-monitor.md)

