---
title: Řešení potíží se službou Azure Cache for Redis na straně klienta
description: Naučte se řešit běžné problémy na straně klienta s mezipamětí Azure cache pro Redis, jako je tlak Redis klientské paměti, nárůst provozu, vysoký výkon procesoru, omezená šířka pásma, velké požadavky nebo Velká velikost odpovědi.
author: yegu-ms
ms.author: yegu
ms.service: cache
ms.topic: troubleshooting
ms.date: 10/18/2019
ms.openlocfilehash: 122c96c95aea794fbba9cab8a9a5b867f9f34b48
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "88008963"
---
# <a name="troubleshoot-azure-cache-for-redis-client-side-issues"></a>Řešení potíží se službou Azure Cache for Redis na straně klienta

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
- Monitoruje `Page Faults/Sec` čítač výkonu klienta. Při běžném provozu má většina systémů chyby stránky. Špičky chyb stránkování odpovídající vypršením časových limitů požadavků můžou značit zatížení paměti.

Vysoký tlak v paměti klienta může být omezen několika způsoby:

- Dig se ke schématům využití paměti, aby se snížila spotřeba paměti na klientovi.
- Upgradujte virtuální počítač klienta na větší velikost s větší pamětí.

## <a name="traffic-burst"></a>Nárůst provozu

Shluky přenosů v kombinaci s špatným `ThreadPool` nastavením můžou mít za následek prodlevy při zpracování dat, která už odesílá server Redis, ale zatím se nevyužil na straně klienta.

`ThreadPool`Pomocí [ `ThreadPoolLogger` příkladu ](https://github.com/JonCole/SampleCode/blob/master/ThreadPoolMonitor/ThreadPoolLogger.cs)Sledujte, jak se v průběhu času mění vaše Statistika. `TimeoutException`K dalšímu zkoumání můžete použít zprávy z stackexchange. Redis, podobně jako v následujícím příkladu:

```output
    System.TimeoutException: Timeout performing EVAL, inst: 8, mgr: Inactive, queue: 0, qu: 0, qs: 0, qc: 0, wr: 0, wq: 0, in: 64221, ar: 0,
    IOCP: (Busy=6,Free=999,Min=2,Max=1000), WORKER: (Busy=7,Free=8184,Min=2,Max=8191)
```

V předchozí výjimce je k dispozici několik problémů, které jsou zajímavé:

- Všimněte si, že v `IOCP` části a v `WORKER` oddílu máte `Busy` hodnotu, která je větší než `Min` hodnota. Tento rozdíl znamená, že je `ThreadPool` potřeba upravit nastavení.
- Můžete také zobrazit `in: 64221` . Tato hodnota označuje, že 64 211 bajtů bylo přijato na soketové vrstvě jádra klienta, ale aplikace ji nečetla. Tento rozdíl obvykle znamená, že vaše aplikace (například StackExchange. Redis) nečte data ze sítě, a to tak rychle, jak ji server posílá.

Můžete [nakonfigurovat `ThreadPool` Nastavení](cache-management-faq.md#important-details-about-threadpool-growth) , abyste se ujistili, že se váš fond vláken rychle škáluje v rámci shlukových scénářů.

## <a name="high-client-cpu-usage"></a>Vysoké využití CPU klienta

Vysoké využití procesoru klienta indikuje, že systém nemůže udržovat práci s prací, na kterou se žádá. I když mezipaměť odesílá odpověď rychle, může klient podařit se včas zpracovat odpověď.

Monitorujte využití CPU na úrovni systému klienta pomocí metrik dostupných ve Azure Portal nebo prostřednictvím čítačů výkonu v počítači. Dávejte pozor, abyste nesledovali procesor *procesů* , protože u jednoho procesu může být nízké využití procesoru, ale procesor může být vysoký. Sledujte špičky využití procesoru, které odpovídají časovým limitům. Vysoký procesor může také způsobovat vysoké `in: XXX` hodnoty v `TimeoutException` chybových zprávách, jak je popsáno v části [shlukování provozu](#traffic-burst) .

> [!NOTE]
> StackExchange. Redis 1.1.603 a novější zahrnuje `local-cpu` metriku v `TimeoutException` chybových zprávách. Ujistěte se, že používáte nejnovější verzi [balíčku NuGet stackexchange. Redis](https://www.nuget.org/packages/StackExchange.Redis/). V kódu jsou neustále opraveny chyby, aby bylo lépe robustní na vypršení časových limitů, takže je důležité mít nejnovější verzi.
>

Omezení vysokého využití procesoru klienta:

- Prozkoumejte, co způsobuje špičky procesoru.
- Upgradujte klienta na větší velikost virtuálního počítače s větší kapacitou procesoru.

## <a name="client-side-bandwidth-limitation"></a>Omezení šířky pásma na straně klienta

V závislosti na architektuře klientských počítačů můžou mít omezení, kolik šířky pásma sítě mají k dispozici. Pokud klient překročí dostupnou šířku pásma přetížením sítě, data se na straně klienta nezpracovávají tak rychle, jak ho Server odesílá. Tato situace může vést k vypršení časových limitů.

Sledujte, jak se v průběhu času mění využití šířky pásma pomocí [příkladu `BandwidthLogger` ](https://github.com/JonCole/SampleCode/blob/master/BandWidthMonitor/BandwidthLogger.cs). Tento kód se nemusí úspěšně spustit v některých prostředích s omezenými oprávněními (jako jsou weby Azure).

Chcete-li zmírnit omezení využití šířky pásma sítě nebo zvyšte velikost virtuálního počítače klienta na jednu s větší kapacitou sítě.

## <a name="large-request-or-response-size"></a>Velký požadavek nebo velikost odpovědi

Velký požadavek nebo odpověď může způsobit vypršení časových limitů. Předpokládejme například, že vaše hodnota časového limitu nakonfigurovaná na vašem klientovi je 1 sekunda. Vaše aplikace požaduje dva klíče (například a a B) současně (pomocí stejného fyzického připojení k síti). Většina klientů podporuje žádosti "přesměrování", kde jsou obě požadavky "a" a "B" odesílány jednou po druhém bez čekání na jejich odpovědi. Server odesílá odpovědi zpátky ve stejném pořadí. Pokud je odpověď "A" Velká, může pro pozdější požadavky vyjíst většinou časový limit.

V následujícím příkladu je žádost "A" a "B" na server odesílána rychle. Server rychle spouští posílání odpovědí a a B. Z důvodu doby přenosu dat musí odpověď B počkat na odezvu "A", a to i v případě, že server rychle odpoví.

```console
|-------- 1 Second Timeout (A)----------|
|-Request A-|
     |-------- 1 Second Timeout (B) ----------|
     |-Request B-|
            |- Read Response A --------|
                                       |- Read Response B-| (**TIMEOUT**)
```

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
- [Jak mohu srovnávací testy a testovat výkon své mezipaměti?](cache-management-faq.md#how-can-i-benchmark-and-test-the-performance-of-my-cache)
