---
title: Řešení potíží s Azure cache pro Redis problémy na straně klienta
description: Naučte se řešit běžné problémy na straně klienta s mezipamětí Azure cache pro Redis, jako je tlak Redis klientské paměti, nárůst provozu, vysoký výkon procesoru, omezená šířka pásma, velké požadavky nebo Velká velikost odpovědi.
author: yegu-ms
ms.author: yegu
ms.service: cache
ms.topic: troubleshooting
ms.date: 10/18/2019
ms.openlocfilehash: abb73f93116fae217f527e0a9faaf61e2b42ba6c
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/25/2019
ms.locfileid: "75433363"
---
# <a name="troubleshoot-azure-cache-for-redis-client-side-issues"></a>Řešení potíží s Azure cache pro Redis problémy na straně klienta

Tato část popisuje problémy, ke kterým dochází z důvodu podmínky klienta Redis, který vaše aplikace používá.

- [Tlak v paměti u klienta Redis](#memory-pressure-on-redis-client)
- [Nárůst provozu](#traffic-burst)
- [Vysoké využití CPU klienta](#high-client-cpu-usage)
- [Omezení šířky pásma na straně klienta](#client-side-bandwidth-limitation)
- [Velký požadavek nebo velikost odpovědi](#large-request-or-response-size)

## <a name="memory-pressure-on-redis-client"></a>Tlak v paměti u klienta Redis

Tlak paměti na klientském počítači vede ke všem druhům problémů s výkonem, které mohou zpozdit zpracování odpovědí z mezipaměti. Když dojde k zatížení paměti, systém může data stránkovat na disk. Při této _chybě stránky_ dojde k výraznému zpomalení systému.

Rozpoznání zatížení paměti klienta:

- Monitorujte využití paměti na počítači a ujistěte se, že nepřekračuje dostupnou paměť.
- Monitoruje Čítač výkonu `Page Faults/Sec` klienta. Při běžném provozu má většina systémů chyby stránky. Špičky chyb stránkování, které odpovídají časovým limitům požadavků, mohou označovat tlak na paměť.

Vysoký tlak v paměti klienta může být omezen několika způsoby:

- Dig se ke schématům využití paměti, aby se snížila spotřeba paměti na klientovi.
- Upgradujte virtuální počítač klienta na větší velikost s větší pamětí.

## <a name="traffic-burst"></a>Nárůst provozu

Shluky provozu v kombinaci s nedostatečnými nastaveními `ThreadPool` můžou způsobit zpoždění při zpracování dat, která už odesílá server Redis, ale ještě se nevyužil na straně klienta.

Sledujte, jak se Statistika `ThreadPool` v průběhu času mění pomocí [ukázkové `ThreadPoolLogger`](https://github.com/JonCole/SampleCode/blob/master/ThreadPoolMonitor/ThreadPoolLogger.cs). K dalšímu prozkoumání můžete použít `TimeoutException` zprávy z StackExchange. Redis podobný níže:

    System.TimeoutException: Timeout performing EVAL, inst: 8, mgr: Inactive, queue: 0, qu: 0, qs: 0, qc: 0, wr: 0, wq: 0, in: 64221, ar: 0,
    IOCP: (Busy=6,Free=999,Min=2,Max=1000), WORKER: (Busy=7,Free=8184,Min=2,Max=8191)

V předchozí výjimce je k dispozici několik problémů, které jsou zajímavé:

- Všimněte si, že v části `IOCP` a v `WORKER` oddílu máte `Busy` hodnotu, která je větší než hodnota `Min`. Tento rozdíl znamená, že nastavení `ThreadPool` potřebnou úpravu.
- Můžete také zobrazit `in: 64221`. Tato hodnota označuje, že 64 211 bajtů bylo přijato na soketové vrstvě jádra klienta, ale aplikace ji nečetla. Tento rozdíl obvykle znamená, že vaše aplikace (například StackExchange. Redis) nečte data ze sítě, a to tak rychle, jak ji server posílá.

[Nastavení `ThreadPool` můžete nakonfigurovat](https://gist.github.com/JonCole/e65411214030f0d823cb) tak, aby se zajistilo, že se váš fond vláken rychle škáluje v rámci shlukových scénářů.

## <a name="high-client-cpu-usage"></a>Vysoké využití CPU klienta

Vysoké využití procesoru klienta indikuje, že systém nemůže udržovat práci s prací, na kterou se žádá. I když mezipaměť odesílá odpověď rychle, může klient podařit se včas zpracovat odpověď.

Monitorujte využití CPU na úrovni systému klienta pomocí metrik dostupných ve Azure Portal nebo prostřednictvím čítačů výkonu v počítači. Dávejte pozor, abyste nesledovali procesor *procesů* , protože u jednoho procesu může být nízké využití procesoru, ale procesor může být vysoký. Sledujte špičky využití procesoru, které odpovídají časovým limitům. Vysoký procesor může také způsobit vysoké `in: XXX` hodnoty v `TimeoutException` chybové zprávy, jak je popsáno v části [shlukování provozu](#traffic-burst) .

> [!NOTE]
> StackExchange. Redis 1.1.603 a novější zahrnuje metriku `local-cpu` v chybových zprávách `TimeoutException`. Ujistěte se, že používáte nejnovější verzi [balíčku NuGet stackexchange. Redis](https://www.nuget.org/packages/StackExchange.Redis/). V kódu jsou neustále opraveny chyby, aby bylo lépe robustní na vypršení časových limitů, takže je důležité mít nejnovější verzi.
>

Omezení vysokého využití procesoru klienta:

- Prozkoumejte, co způsobuje špičky procesoru.
- Upgradujte klienta na větší velikost virtuálního počítače s větší kapacitou procesoru.

## <a name="client-side-bandwidth-limitation"></a>Omezení šířky pásma na straně klienta

V závislosti na architektuře klientských počítačů můžou mít omezení, kolik šířky pásma sítě mají k dispozici. Pokud klient překročí dostupnou šířku pásma přetížením sítě, data se na straně klienta nezpracovávají tak rychle, jak ho Server odesílá. Tato situace může vést k vypršení časových limitů.

Sledujte, jak se v průběhu času mění využití šířky pásma pomocí [příkladu `BandwidthLogger`](https://github.com/JonCole/SampleCode/blob/master/BandWidthMonitor/BandwidthLogger.cs). Tento kód se nemusí úspěšně spustit v některých prostředích s omezenými oprávněními (jako jsou weby Azure).

Chcete-li zmírnit omezení využití šířky pásma sítě nebo zvyšte velikost virtuálního počítače klienta na jednu s větší kapacitou sítě.

## <a name="large-request-or-response-size"></a>Velký požadavek nebo velikost odpovědi

Velký požadavek nebo odpověď může způsobit vypršení časových limitů. Předpokládejme například, že vaše hodnota časového limitu nakonfigurovaná na vašem klientovi je 1 sekunda. Vaše aplikace požaduje dva klíče (například a a B) současně (pomocí stejného fyzického připojení k síti). Většina klientů podporuje žádosti "přesměrování", kde jsou obě požadavky "a" a "B" odesílány jednou po druhém bez čekání na jejich odpovědi. Server odesílá odpovědi zpátky ve stejném pořadí. Pokud je odpověď "A" Velká, může pro pozdější požadavky vyjíst většinou časový limit.

V následujícím příkladu je žádost "A" a "B" na server odesílána rychle. Server rychle spouští posílání odpovědí a a B. Z důvodu doby přenosu dat musí odpověď B počkat na odezvu "A", a to i v případě, že server rychle odpoví.

    |-------- 1 Second Timeout (A)----------|
    |-Request A-|
         |-------- 1 Second Timeout (B) ----------|
         |-Request B-|
                |- Read Response A --------|
                                           |- Read Response B-| (**TIMEOUT**)

Tento požadavek nebo odpověď je pro měření obtížné. Můžete instrumentovat klientský kód pro sledování velkých požadavků a odpovědí.

Rozlišení pro velké velikosti odpovědí se liší, ale zahrnují:

1. Optimalizujte aplikaci pro velký počet malých hodnot, nikoli několik velkých hodnot.
    - Upřednostňovaným řešením je rozdělit data do souvisejících menších hodnot.
    - Podívejte se na téma [co je ideální rozsah velikosti pro Redis? Je 100 KB moc velký?](https://groups.google.com/forum/#!searchin/redis-db/size/redis-db/n7aa2A4DZDs/3OeEPHSQBAAJ) Podrobnosti o tom, proč se doporučují menší hodnoty.
1. Zvětšete velikost virtuálního počítače, abyste získali větší možnosti šířky pásma.
    - Větší šířka pásma na VIRTUÁLNÍm počítači klienta nebo serveru může snížit dobu přenosu dat pro větší odpovědi.
    - Porovnejte Aktuální využití sítě u obou počítačů s omezeními vaší aktuální velikosti virtuálního počítače. Větší šířka pásma pouze na serveru nebo pouze na klientovi nemusí být dostatečná.
1. Zvyšte počet objektů připojení, které vaše aplikace používá.
    - K vytváření žádostí přes různé objekty připojení použijte přístup s kruhovým dotazováním.

## <a name="additional-information"></a>Další informace

- [Řešení potíží se službou Azure Cache for Redis na straně serveru](cache-troubleshoot-server.md)
- [Jak mohu srovnávací testy a testovat výkon své mezipaměti?](cache-faq.md#how-can-i-benchmark-and-test-the-performance-of-my-cache)
