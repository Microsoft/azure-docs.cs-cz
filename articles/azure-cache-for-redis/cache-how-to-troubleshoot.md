---
title: Řešení potíží s Azure Cache pro Redis | Dokumentace Microsoftu
description: Zjistěte, jak vyřešit běžné problémy s mezipamětí Azure Redis.
services: cache
documentationcenter: ''
author: yegu-ms
manager: jhubbard
editor: ''
ms.assetid: 928b9b9c-d64f-4252-884f-af7ba8309af6
ms.service: cache
ms.workload: tbd
ms.tgt_pltfrm: cache
ms.devlang: na
ms.topic: article
ms.date: 03/15/2019
ms.author: yegu
ms.openlocfilehash: 838fc1da3e167d1df04fbb36a2fea33b8ac248a4
ms.sourcegitcommit: 0dd053b447e171bc99f3bad89a75ca12cd748e9c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/26/2019
ms.locfileid: "58482602"
---
# <a name="how-to-troubleshoot-azure-cache-for-redis"></a>Řešení potíží s Azure Cache pro Redis

Tento článek pomůže řešení potíží s různými druhy problémů, na které můžete narazit při připojení k mezipaměti Azure pro instance Redis.

- [Řešení potíží na straně klienta](#client-side-troubleshooting) pomáhá identifikovat a řešit problémy v aplikaci připojení k mezipaměti.
- [Řešení potíží na straně serveru](#server-side-troubleshooting) pomáhá identifikovat a řešit problémy, ke kterým dochází na ukládání do mezipaměti Azure Redis straně.
- [Řešení potíží s ztrátě dat](#data-loss-troubleshooting) pomáhá identifikovat a řešit incidenty, kde jsou klíče očekáván ale nebyl nalezen v mezipaměti.
- [Výjimkách časového limitu StackExchange.Redis](#stackexchangeredis-timeout-exceptions) obsahuje konkrétní pokyny k odstraňování problémů s knihovnou StackExchange.Redis.

> [!NOTE]
> Některé z kroků v tomto průvodci zahrnují pokyny, jak spouštět příkazy Redis a monitorování různých metrik výkonu. Další informace a pokyny najdete v článcích v [Další informace o](#additional-information) oddílu.
>
>

## <a name="client-side-troubleshooting"></a>Řešení potíží na straně klienta

Tato část popisuje řešení problémů, ke kterým dochází z důvodu podmínku na klientské aplikaci.

- [Přetížení paměti na straně klienta](#memory-pressure-on-the-client)
- [Shlukového přenosu](#burst-of-traffic)
- [Klient vysoké využití procesoru](#high-client-cpu-usage)
- [Překročení šířky pásma na straně klienta](#client-side-bandwidth-exceeded)
- [Velikost velkých žádostí a odpovědí](#large-requestresponse-size)

### <a name="memory-pressure-on-the-client"></a>Přetížení paměti na straně klienta

Přetížení paměti v klientském počítači vede na všechny typy problémů s výkonem, které můžou zdržet zpracování odpovědi z mezipaměti. Při volání přetížení paměti může systém stránce dat na disk. To _stránky chybující_ způsobí, že systém výrazně zpomalit.

Ke zjištění tlaku na paměť na straně klienta:

- Monitorovat využití paměti v počítači, abyste měli jistotu, že nemá být delší než dostupná paměť.
- Monitorování klienta `Page Faults/Sec` čítače výkonu. Během normálního provozu většina systémů mají některé chyby stránek. Poraďte se špičkami stránkování odpovídající vypršení časového limitu žádosti může znamenat přetížení paměti.

Několik způsobů, jak můžete minimalizovat zatížení vysoký poměr paměti na straně klienta:

- Ponořte se do vaší vzory využití paměti ke snížení využití paměti na straně klienta.
- Upgradujte klienta virtuálního počítače s víc paměti větší velikost.

### <a name="burst-of-traffic"></a>Shlukového přenosu

Nárůstům provozu v kombinaci s špatné `ThreadPool` nastavení může dojít k prodlevám při zpracování dat už poslali pomocí serveru Redis, ale dosud spotřebované na straně klienta.

Monitorování jak vaše `ThreadPool` statistiky změny pomocí [příklad `ThreadPoolLogger` ](https://github.com/JonCole/SampleCode/blob/master/ThreadPoolMonitor/ThreadPoolLogger.cs). Můžete použít `TimeoutException` zprávy z StackExchange.Redis podobná níže uvedenému příkladu k hlubšímu prošetření:

    System.TimeoutException: Timeout performing EVAL, inst: 8, mgr: Inactive, queue: 0, qu: 0, qs: 0, qc: 0, wr: 0, wq: 0, in: 64221, ar: 0,
    IOCP: (Busy=6,Free=999,Min=2,Max=1000), WORKER: (Busy=7,Free=8184,Min=2,Max=8191)

V předchozí výjimky jsou nejrůznější problémy, které jsou zajímavé:

- Všimněte si, že v `IOCP` oddílu a `WORKER` oddílu máte `Busy` hodnotu, která je větší než `Min` hodnotu. Tento rozdíl znamená, že vaše `ThreadPool` nastavení vyžadují úpravu.
- Můžete také zobrazit `in: 64221`. Tato hodnota označuje, že byly přijaty ve vrstvě klienta jádra soketu 64,211 bajtů, ale nebyly přečteny aplikací. Tento rozdíl obvykle znamená, že aplikace (například StackExchange.Redis) není čtení dat ze sítě serveru je odesílání vám rychle.

Je možné [konfigurovat vaše `ThreadPool` nastavení](https://gist.github.com/JonCole/e65411214030f0d823cb) abyste měli jistotu, že se váš fond vláken škálování rychle v části burst scénáře.

### <a name="high-client-cpu-usage"></a>Klient vysoké využití procesoru

Klient vysoké využití procesoru indikuje, že systém nemůže nadále práce, kterou je byla požádána provést. I v případě, že rychle odeslat odpověď mezipaměti, klient nemusí podařit zpracovat odpověď včas.

Monitorování využití procesoru systémová klienta pomocí metrik, které jsou k dispozici na webu Azure Portal nebo pomocí čítačů výkonu na počítači. Dejte pozor, abyste monitorování *procesu* procesoru protože jednoho procesu může mít nízké využití procesoru, ale systémová procesoru může být vysoká. Podívejte se na špiček zatížení procesoru, které odpovídají vypršení časového limitu. Vysoké využití procesoru může také způsobit vysokou `in: XXX` hodnoty v `TimeoutException` chybové zprávy, jak je popsáno v [shlukové přenosy](#burst-of-traffic) oddílu.

> [!NOTE]
> StackExchange.Redis 1.1.603 a dále zahrnuje `local-cpu` metriky v `TimeoutException` chybové zprávy. Zkontrolujte, že jste pomocí nejnovější verze [balíček StackExchange.Redis NuGet](https://www.nuget.org/packages/StackExchange.Redis/). Existují chyby neustále opravené v kódu, aby ho robustnější k vypršení časového limitu, je důležité mít na nejnovější verzi.
>
>

Ke zmírnění klienta vysoké využití procesoru:

- Zjistěte, co je příčinou špičky využití procesoru.
- Upgradujte klienta větší virtuální počítač s větší kapacitou procesoru.

### <a name="client-side-bandwidth-exceeded"></a>Překročení šířky pásma na straně klienta

V závislosti na architektuře klientské počítače, mohou mít omezení na jaký poměr šířky pásma sítě mají k dispozici. Pokud klient překračuje dostupnou šířku pásma přetěžováním kapacita sítě, pak dat není zpracována na straně klienta rychle odesílá na server. Tato situace může vést k vypršení časového limitu.

Sledovat, jak změnit vaše využití šířky pásma přes pomocí [příklad `BandwidthLogger` ](https://github.com/JonCole/SampleCode/blob/master/BandWidthMonitor/BandwidthLogger.cs). Tento kód nemusí v některých prostředích s omezenými oprávněními (jako jsou weby Azure) úspěšně spuštěna.

Pokud chcete omezit rizika, snížit využití šířky pásma sítě nebo zvětšete velikost virtuálního počítače na jeden s větší kapacitou sítě klienta.

### <a name="large-requestresponse-size"></a>Velikost velkých žádostí a odpovědí

Velké žádostí a odpovědí může způsobit vypršení časového limitu. Jako příklad předpokládejme, že vaše hodnotu časového limitu, který je nakonfigurován v klientu je 1 sekunda. Vaše aplikace požaduje dva klíče (například "A" a "B") ve stejnou dobu (s použitím stejného fyzického síťového připojení). Většina klientů podporují požadavek "paralelní zpracování", oba požadavky "A" a "B" se odešle jednu po druhé bez čekání na jejich odpovědi. Server odesílá odpovědí zpět ve stejném pořadí. Pokud je odpověď "A" velký, může jíst si většina časového limitu pro vyšší požadavky.

V následujícím příkladu se odesílají žádosti o "A" a "B" rychle k serveru. Spustí se server rychle odesílání odpovědi "A" a "B". Z důvodu doba přenosu dat odpověď, kterou musíte počkat "B", za odpověď "A" vyprší časový limit i v případě, že server odpověděl rychle.

    |-------- 1 Second Timeout (A)----------|
    |-Request A-|
         |-------- 1 Second Timeout (B) ----------|
         |-Request B-|
                |- Read Response A --------|
                                           |- Read Response B-| (**TIMEOUT**)

Tento požadavek/odpověď je obtížné k měření. Váš klientský kód ke sledování velkých požadavků a odpovědí může instrumentace.

Řešení pro velké odpovědi velikosti jsou různé, ale patří:

1. Optimalizovat výkon své aplikace pro velký počet malých hodnoty, nikoli několika velké hodnoty.
    - Preferovaným řešením je rozdělit data do související menší hodnoty.
    - Najdete v příspěvku [co je rozsah hodnot ideální velikost pro redis? 100 KB je moc velký? ](https://groups.google.com/forum/#!searchin/redis-db/size/redis-db/n7aa2A4DZDs/3OeEPHSQBAAJ) podrobnosti o důvod, proč se doporučuje menší hodnoty.
1. Zvětšete velikost virtuálního počítače zobrazíte možnosti vyšší šířku pásma
    - Větší šířku pásma u klienta nebo serveru virtuálního počítače může snížit dobu přenosu dat pro větší odpovědi.
    - Porovnejte vaše aktuální využití sítě na obou počítačích limity současnými velikostmi virtuálních počítačů. Větší šířku pásma na pouze serveru nebo pouze na straně klienta nemusí stačit.
1. Zvýšíte počet objektů připojení, které vaše aplikace používá.
    - Vytvářet požadavky přes jiné připojení objekty pomocí kruhového dotazování.

## <a name="server-side-troubleshooting"></a>Řešení potíží na straně serveru

Tato část popisuje řešení problémů, ke kterým dochází z důvodu stavu na serveru mezipaměti.

- [Přetížení paměti na serveru](#memory-pressure-on-the-server)
- [Vysoké využití procesoru / zátěž serveru](#high-cpu-usage--server-load)
- [Překročení šířky pásma na straně serveru](#server-side-bandwidth-exceeded)

### <a name="memory-pressure-on-the-server"></a>Přetížení paměti na serveru

Přetížení paměti na straně serveru vede na všechny typy problémů s výkonem, které můžou zdržet zpracování požadavků. Při volání přetížení paměti může systém stránce dat na disk. To _stránky chybující_ způsobí, že systém výrazně zpomalit. Existuje několik možných příčin této přetížení paměti:

- Mezipaměť je naplněný daty téměř maximální kapacitě.
- Redis je zobrazuje fragmentace velkého množství paměti. Fragmentace je nejčastěji způsobeno ukládání velkých objektů, protože Redis je optimalizovaná pro malé objekty.

Redis poskytuje dva statistiky prostřednictvím [informace](https://redis.io/commands/info) příkaz, který vám může pomoct identifikovat problém: "used_memory" a "used_memory_rss". Je možné [zobrazit tyto metriky](cache-how-to-monitor.md#view-metrics-with-azure-monitor) pomocí portálu.

Existuje několik možných provedené změny můžete zajistit, aby byl v pořádku využití paměti:

- [Nakonfigurujte zásady paměti](cache-configure.md#maxmemory-policy-and-maxmemory-reserved) a nastavit dobu vypršení platnosti klíče. Tyto zásady nemusí být dostatečné, pokud máte fragmentace.
- [Nastavení hodnoty vyhrazené maxmemory](cache-configure.md#maxmemory-policy-and-maxmemory-reserved) , který je dostatečně velký, aby se vykompenzovalo fragmentace paměti. Další informace najdete v části Další [důležité informace týkající se rezervace paměti](#considerations-for-memory-reservations) níže.
- Rozdělte velké objekty v mezipaměti do menších souvisejících objektů.
- [Vytvořit upozornění](cache-how-to-monitor.md#alerts) na metriky, jako jsou využité paměti již v rané fázi oznámení o možné dopady.
- [Škálování](cache-how-to-scale.md) na větší velikost mezipaměti s větší kapacitou paměti.

#### <a name="considerations-for-memory-reservations"></a>Důležité informace týkající se rezervace paměti

Probíhá aktualizace hodnot rezervace paměti, například maxmemory vyhrazené, může ovlivnit výkon mezipaměti. Předpokládejme, že máte 53GB mezipaměti, který je vyplněn 49 GB dat. Změna hodnoty rezervace na 8 GB sníží maximální dostupné systémové paměti na 45 GB. Pokud _used_memory_ nebo _used_memory_rss_ hodnoty jsou vyšší než 45 GB, systém může vyřazení dat do obou _used_memory_ a _used_memory_rss_ jsou nižší než 45 GB. Vyřazení zvýšit fragmentace zatížení a paměti serveru.

### <a name="high-cpu-usage--server-load"></a>Vysoké využití procesoru / zátěž serveru

Zatížení serveru s vysokou nebo podle využití procesorů znamená, že server nemůže zpracovat požadavky včas. Server může být pomalý a nemůže držet krok s mírou požadavku.

[Monitorování metrik](cache-how-to-monitor.md#view-metrics-with-azure-monitor) jako je zatížení procesoru nebo serveru. Podívejte se na špiček zatížení procesoru, které odpovídají vypršení časového limitu.

Existuje několik změn, které provedete ke zmírnění zatížení serveru s vysokou:

- Zjistěte, co je příčinou vzroste využití procesoru, jako je například spuštění [náročné příkazy](#expensive-commands) nebo stránky chybující z důvodu tlaku na paměť vysoký.
- [Vytvořit upozornění](cache-how-to-monitor.md#alerts) na metriky, jako je zatížení procesoru nebo server již v rané fázi oznámení o možné dopady.
- [Škálování](cache-how-to-scale.md) na větší velikost mezipaměti s větší kapacitou procesoru.

#### <a name="expensive-commands"></a>Náročné příkazy

Ne všechny příkazy Redis jsou vytvořeny stejnou měrou – některé jsou nákladnější než jiné. [Redis příkazy dokumentaci](https://redis.io/commands) ukazuje čas složitost jednotlivých příkazů. Doporučujeme, abyste že si přečetli příkazy, že používáte v mezipaměti k pochopení vlivu na výkon těchto příkazů. Například [klíče](https://redis.io/commands/keys) bez znalosti, že se jedná o operaci O(N) se často používá příkaz. KLÍČE můžete vyhnout použitím [KONTROLOVAT](https://redis.io/commands/scan) ke snížení využití procesoru špičky.

Použití [SLOWLOG](https://redis.io/commands/slowlog) příkazu, můžete měřit náročné příkazy, které se provádí na serveru.

### <a name="server-side-bandwidth-exceeded"></a>Překročení šířky pásma na straně serveru

Různé velikosti mají kapacitu šířky pásma jinou síť. Pokud server překračuje dostupnou šířku pásma, pak data se neodešlou do klienta rychle. Požadavky klientů může vypršení časového limitu, protože server nelze nabízení dat do klienta dostatečně rychle.

Metriky "Mezipaměti pro čtení" a "Mezipaměti zápisu" je možné zobrazit, jaký poměr šířky pásma na straně serveru se používá. Je možné [zobrazit tyto metriky](cache-how-to-monitor.md#view-metrics-with-azure-monitor) na portálu.

Ke zmírnění situacích, kde využití šířky pásma sítě je blízko maximální kapacita:

- Změna chování volání klienta ke snížení poptávky sítě.
- [Vytvořit upozornění](cache-how-to-monitor.md#alerts) na metriky, jako je mezipaměť pro čtení nebo zápis do mezipaměti a nastavit již v rané fázi oznamování možné dopady.
- [Škálování](cache-how-to-scale.md) na větší velikost mezipaměti s další kapacitu šířky pásma sítě.

## <a name="data-loss-troubleshooting"></a>Řešení potíží s ztráty dat

Očekávání pro určité dat v Azure mezipaměť pro instanci Redis, ale neměli zdá být k dispozici.

Zobrazit [co se stalo se data v Redis?](https://gist.github.com/JonCole/b6354d92a2d51c141490f10142884ea4#file-whathappenedtomydatainredis-md) možné příčiny a řešení.

## <a name="stackexchangeredis-timeout-exceptions"></a>Výjimkách časového limitu StackExchange.Redis

StackExchange.Redis používá konfiguraci nastavení s názvem `synctimeout` u synchronních operací s výchozí hodnotou 1000 ms. Pokud v tuto chvíli nedokončí synchronní volání, vyvolá klient StackExchange.Redis vypršení časového limitu, podobně jako v následujícím příkladu:

    System.TimeoutException: Timeout performing MGET 2728cc84-58ae-406b-8ec8-3f962419f641, inst: 1,mgr: Inactive, queue: 73, qu=6, qs=67, qc=0, wr=1/1, in=0/0 IOCP: (Busy=6, Free=999, Min=2,Max=1000), WORKER (Busy=7,Free=8184,Min=2,Max=8191)

Tato chybová zpráva obsahuje metriky, který vám pomůže odkazovat na příčiny a možná řešení problému. Následující tabulka obsahuje podrobnosti o chybové zprávy metriky.

| Chybová zpráva metrika | Podrobnosti |
| --- | --- |
| instrukce |V posledním časovém intervalu: byl vydán 0 příkazy |
| mgr |Správce soketu dělá `socket.select`, což znamená, že je s dotazem, operačního systému k označení soketu, který nabízí něco udělat. Čtečka není čtení aktivně ze sítě. protože nemá myslíte, že se něco udělat |
| fronta |Existují 73 celkový počet probíhajících operací |
| qu |6 v průběhu operace jsou ve frontě unsent a nebyly dosud napsány tak, aby odchozí síťové |
| qs |67 operací v průběhu byly odeslány na server, ale ještě není k dispozici odpověď. Odpovědí může být `Not yet sent by the server` nebo `sent by the server but not yet processed by the client.` |
| qc |0 nebo v průběhu operace viděli odpovědi, ale nebyly dosud byl označen jako úplný vzhledem k tomu, že se čeká na dokončení smyčky |
| WR |Je bajtů/activewriters aktivní zapisovače (to znamená, že 6 unsent požadavky nejsou ignorovány) |
| ve |Neexistují žádné aktivní čtecí zařízení a jsou k dispozici ke čtení na síťové rozhraní bajty/activereaders nula bajtů |

### <a name="steps-to-investigate"></a>Postup k prozkoumání

1. Jako osvědčený postup Ujistěte se, že používáte následující vzor pro připojení při používání klienta StackExchange.Redis.

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

    Další informace najdete v tématu [připojení k mezipaměti pomocí StackExchange.Redis](cache-dotnet-how-to-use-azure-redis-cache.md#connect-to-the-cache).

1. Ujistěte se, že váš server a klientské aplikace jsou ve stejné oblasti v Azure. Například se vám může zobrazovat vypršení časového limitu při vaše mezipaměť je v oblasti východní USA, ale klient je v oblasti západní USA a požadavek se nedokončil v rámci `synctimeout` interval, nebo může zobrazovat vypršení časového limitu při ladění ze svého místního vývojového počítače. 

    Je důrazně doporučujeme mít mezipaměti a v klientovi ve stejné oblasti Azure. Pokud máte scénáře, který obsahuje volání mezi různými oblastmi, byste měli nastavit `synctimeout` interval na hodnotu vyšší, než je výchozí interval 1000 ms zahrnutím `synctimeout` vlastnost připojovacího řetězce. Následující příklad ukazuje fragment kódu připojovacího řetězce pro StackExchange.Redis poskytované mezipaměti Azure pro Redis s `synctimeout` 2000 MS.

        synctimeout=2000,cachename.redis.cache.windows.net,abortConnect=false,ssl=true,password=...
1. Zkontrolujte, že jste pomocí nejnovější verze [balíček StackExchange.Redis NuGet](https://www.nuget.org/packages/StackExchange.Redis/). Existují chyby neustále opravené v kódu, aby ho robustnější k vypršení časového limitu, je důležité mít na nejnovější verzi.
1. Pokud vaše požadavky zavazujete se k jejich omezení šířky pásma na serveru nebo klienta, trvá déle, aby dokončí a může způsobit vypršení časového limitu. Chcete-li zjistit, jestli vaše vypršení časového limitu kvůli šířce pásma sítě na serveru, přečtěte si téma [šířky pásma na straně serveru překročil](#server-side-bandwidth-exceeded). Chcete-li zjistit, jestli vaše vypršení časového limitu z důvodu šířky pásma sítě klienta, přečtěte si téma [šířky pásma na straně klienta překročil](#client-side-bandwidth-exceeded).
1. Můžete získat procesoru na serveru nebo na straně klienta jsou vázány?

   - Zaškrtněte, pokud je získání svázaná podle využití procesoru u svého klienta. Vysoké využití procesoru může způsobit, že žádost o proto nebyla zpracována v rámci `synctimeout` interval a příčinu vypršení časového limitu žádosti. Přechod na větší velikost klienta nebo distribuci zatížení může pomoci řídit tento problém.
   - Zkontrolujte, jestli získáváte procesoru vázány na serveru pro monitorování `CPU` [mezipaměti metrika výkonu](cache-how-to-monitor.md#available-metrics-and-reporting-intervals). Požadavky přicházející Redis je závislá na procesoru může způsobit, že tyto požadavky, vynutí vypršení časového limitu. Chcete-li vyřešit tuto podmínku, můžete distribuovat zatížení mezi několika horizontálními oddíly v cache ve verzi premium nebo upgradovat na větší velikosti nebo cenové úrovně. Další informace najdete v tématu [překročení šířky pásma na straně serveru](#server-side-bandwidth-exceeded).
1. Existují příkazů trvá dlouhou dobu zpracování na serveru? Dlouho běžící příkazy, které trvá dlouhou dobu zpracování na serveru redis může způsobit vypršení časového limitu. Další informace o dlouho běžící příkazy najdete v tématu [náročné příkazy](#expensive-commands). Můžete se připojit k mezipaměti Azure pro instanci Redis pomocí klienta redis rozhraní příkazového řádku nebo [konzola Redis](cache-configure.md#redis-console). Potom spusťte [SLOWLOG](https://redis.io/commands/slowlog) příkazu zjistíte, jestli jsou požadavky pomaleji, než se očekávalo. Redis Server a StackExchange.Redis jsou optimalizované pro velký počet malých požadavků spíše než méně velkých požadavků. Rozdělení dat do menších bloků zvýšit zde věci.

    Informace o připojení na koncový bod SSL vaší mezipaměti pomocí rozhraní příkazového řádku redis a stunnelu, najdete v příspěvku blogu [oznamujeme zprostředkovatel stavu relací ASP.NET pro redis Cache ve verzi Preview](https://blogs.msdn.com/b/webdev/archive/2014/05/12/announcing-asp-net-session-state-provider-for-redis-preview-release.aspx).
1. Vysoké zatížení serveru Redis může způsobit vypršení časového limitu. Zatížení serveru můžete monitorovat pomocí monitorování `Redis Server Load` [mezipaměti metrika výkonu](cache-how-to-monitor.md#available-metrics-and-reporting-intervals). Zatížení serveru 100 (maximální hodnota) znamená, že redis server byla zaneprázdněno žádné určitou dobu nečinné, zpracování požadavků. Pokud chcete zobrazit, pokud některé požadavky spotřebovávají všechny funkce serveru, spusťte příkaz SlowLog, jak je popsáno v předchozím odstavci. Další informace najdete v tématu vysoké využití procesoru / zátěž serveru.
1. Pokusil se jakákoli jiná událost na straně klienta, která by mohla způsobit blip sítě? Běžné události zahrnují: počet instancí klientů, kteří vertikální navýšení nebo snížení kapacity, nasazení nové verze klienta nebo povolené automatické škálování. V našich testech jsme našli, že automatické škálování nebo škálování směrem nahoru nebo dolů může způsobit odchozího síťového připojení ke ztrátě několik sekund. Kód StackExchange.Redis je odolný vůči tyto události a znovu připojí. Při opětovné připojení, můžete všechny požadavky ve frontě časový limit.
1. Pokusil se velké žádosti před několika malých požadavků vypršel časový limit mezipaměti? Parametr `qs` v chybě se zpráva, že počet požadavků odeslaných z klienta na server, ale nebyly zpracovány odpověď. Tuto hodnotu můžete pořád rostou, protože StackExchange.Redis používá jedno připojení TCP a může číst pouze jednu odpověď najednou. Přestože první operace vypršení časového limitu, nezastaví další data z odesílané do nebo ze serveru. Ostatní žádosti budou blokovány, dokud velké žádosti bylo dokončeno a může způsobit, že časové limity. Jedním z řešení je minimalizovat riziko vypršení časového limitu pro zajištění, že vaše mezipaměť je příliš velká pro vaše úlohy a rozdělení do menších bloků velké hodnoty. Další možnou příčinou je použití fondu `ConnectionMultiplexer` objekty v klientovi a zvolte nejméně načíst `ConnectionMultiplexer` při odeslání nového požadavku. Načítání napříč více objektů připojení by měl jeden časový limit zabránit způsobí ostatní požadavky také vypršení časového limitu.
1. Pokud používáte `RedisSessionStateProvider`, ujistěte se, jste správně nastavili časový limit opakování. `retryTimeoutInMilliseconds` musí být vyšší než `operationTimeoutInMilliseconds`, jinak dojde k žádné opakování. V následujícím příkladu `retryTimeoutInMilliseconds` je nastavena na 3000. Další informace najdete v tématu [zprostředkovatel stavu relací ASP.NET pro Azure Cache pro Redis](cache-aspnet-session-state-provider.md) a [jak používat parametry konfigurace zprostředkovatele stavu relace a poskytovatel výstupní mezipaměti](https://github.com/Azure/aspnet-redis-providers/wiki/Configuration).

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

1. Zkontrolujte využití paměti na ukládání do mezipaměti Azure Redis serveru podle [monitorování](cache-how-to-monitor.md#available-metrics-and-reporting-intervals) `Used Memory RSS` a `Used Memory`. Pokud zásadu vyřazení je na místě, Redis spustí schopnost reagovat umožňuje vyhnání klíče při `Used_Memory` dosáhne velikosti mezipaměti. V ideálním případě `Used Memory RSS` by mělo být pouze mírně vyšší než `Used memory`. Velký rozdíl znamená, že existuje fragmentace paměti (interní nebo externí). Když `Used Memory RSS` je menší než `Used Memory`, to znamená, že část paměti mezipaměti byla vyměnit provozu v operačním systému. Pokud toto prohození dojde, můžete očekávat některé významné latenci. Protože Redis nebude mít kontrolu nad jeho přidělení zpřístupněných paměťových stránek, Vysoká `Used Memory RSS` je často výsledkem špička využití paměti. Když Redis server uvolnění paměti, alokátoru trvá paměť, ale může nebo nemusí poskytnout paměť zpět do systému. Může být rozdíl mezi `Used Memory` hodnotu nebo paměti využití podle operačního systému. Paměť se používá a vydány redis, ale není zadaný zpět do systému. Zmírnit problémy s pamětí, můžete provést následující kroky:

   - Upgradujte do mezipaměti na větší velikost tak, že nejsou spuštěné proti omezení paměti v systému.
   - Nastavte dobu vypršení platnosti klíčů tak, aby starší hodnoty se vyřadí aktivní.
   - Monitorování `used_memory_rss` metriky mezipaměti. Když se tato hodnota blíží velikosti mezipaměti, budete pravděpodobně začnou zobrazovat problémy s výkonem. Pokud pomocí mezipaměť premium, nebo upgradujte na větší velikost mezipaměti, Distribuujte data mezi několika horizontálními oddíly.

   Další informace najdete v tématu [přetížení paměti na serveru](#memory-pressure-on-the-server).

## <a name="additional-information"></a>Další informace

- [Jaké mezipaměti Azure Redis a jeho velikost mám použít?](cache-faq.md#what-azure-cache-for-redis-offering-and-size-should-i-use)
- [Jak můžu srovnávací testy a testování výkonu mezipaměť?](cache-faq.md#how-can-i-benchmark-and-test-the-performance-of-my-cache)
- [Jak mohu spustit příkazy Redis?](cache-faq.md#how-can-i-run-redis-commands)
- [Jak monitorovat Azure mezipaměti Redis](cache-how-to-monitor.md)
