---
title: Řešení potíží se službou Azure Cache for Redis na straně klienta
description: Zjistěte, jak vyřešit běžné problémy na straně klienta s Azure Cache pro Redis, jako je tlak paměti klienta Redis, provoz, vysoká kapacita procesoru, omezená šířka pásma, velké požadavky nebo velká velikost odezvy.
author: yegu-ms
ms.author: yegu
ms.service: cache
ms.topic: troubleshooting
ms.date: 10/18/2019
ms.openlocfilehash: ace953fcb278604cb64eef463753f0f2622d3d24
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "79277944"
---
# <a name="troubleshoot-azure-cache-for-redis-client-side-issues"></a>Řešení potíží se službou Azure Cache for Redis na straně klienta

Tato část popisuje řešení problémů, ke kterým dochází z důvodu podmínky pro klienta Redis, který používá vaše aplikace.

- [Tlak paměti na klienta Redis](#memory-pressure-on-redis-client)
- [Dopravní nárůst](#traffic-burst)
- [Vysoké využití procesoru klienta](#high-client-cpu-usage)
- [Omezení šířky pásma na straně klienta](#client-side-bandwidth-limitation)
- [Velká velikost požadavku nebo odpovědi](#large-request-or-response-size)

## <a name="memory-pressure-on-redis-client"></a>Tlak paměti na klienta Redis

Tlak paměti v klientském počítači vede ke všem druhům problémů s výkonem, které mohou zpozdit zpracování odpovědí z mezipaměti. Při zásahu tlaku v paměti může systém stránkovat data na disk. Tato _chyba stránky_ způsobí, že systém výrazně zpomalí.

Chcete-li zjistit tlak paměti na straně klienta:

- Sledujte využití paměti v počítači a ujistěte se, že nepřesahuje dostupnou paměť.
- Sledujte čítač výkonu klienta. `Page Faults/Sec` Během normálního provozu má většina systémů některé chyby stránky. Špičky v chybách stránky odpovídající časovým časovým časům požadavku mohou indikovat tlak paměti.

Vysoký tlak paměti na klienta lze zmírnit několika způsoby:

- Prodejte vzory využití paměti, abyste snížili spotřebu paměti v klientovi.
- Upgradujte virtuální počítač klienta na větší velikost s větší pamětí.

## <a name="traffic-burst"></a>Dopravní nárůst

Shluky provozu v `ThreadPool` kombinaci se špatným nastavením mohou mít za následek zpoždění při zpracování dat, která již server Redis server odeslal, ale ještě nebyla spotřebována na straně klienta.

Sledujte, `ThreadPool` jak se vaše statistiky v průběhu času mění pomocí [příkladu `ThreadPoolLogger` ](https://github.com/JonCole/SampleCode/blob/master/ThreadPoolMonitor/ThreadPoolLogger.cs). Můžete použít `TimeoutException` zprávy z StackExchange.Redis jako níže dále zkoumat:

    System.TimeoutException: Timeout performing EVAL, inst: 8, mgr: Inactive, queue: 0, qu: 0, qs: 0, qc: 0, wr: 0, wq: 0, in: 64221, ar: 0,
    IOCP: (Busy=6,Free=999,Min=2,Max=1000), WORKER: (Busy=7,Free=8184,Min=2,Max=8191)

V předchozí výjimce existuje několik otázek, které jsou zajímavé:

- Všimněte si, `IOCP` že `WORKER` v části `Busy` a části máte `Min` hodnotu, která je větší než hodnota. Tento rozdíl `ThreadPool` znamená, že vaše nastavení je třeba upravit.
- Můžete také `in: 64221`vidět . Tato hodnota označuje, že 64 211 bajtů bylo přijato ve vrstvě soketu jádra klienta, ale aplikace je nečetla. Tento rozdíl obvykle znamená, že vaše aplikace (například StackExchange.Redis) nečte data ze sítě tak rychle, jak vám je server odesílá.

Nastavení můžete nakonfigurovat tak, aby se fond vláken rychle zvětšoval v rámci scénářů shlukování. [ `ThreadPool` ](cache-faq.md#important-details-about-threadpool-growth)

## <a name="high-client-cpu-usage"></a>Vysoké využití procesoru klienta

Vysoké využití procesoru klienta znamená, že systém nemůže držet krok s prací, o kterou byl požádán. Přestože mezipaměti odeslala odpověď rychle, klient může selhat zpracovat odpověď včas.

Sledujte využití procesoru v celém systému klienta pomocí metrik dostupných na webu Azure Portal nebo prostřednictvím čítačů výkonu v počítači. Dávejte pozor, abyste nesledovali *proces* procesoru, protože jeden proces může mít nízké využití procesoru, ale celý systém procesoru může být vysoká. Sledujte špičky v využití procesoru, které odpovídají časovým limitům. Vysoký procesor může `in: XXX` také `TimeoutException` způsobit vysoké hodnoty v chybových zprávách, jak je popsáno v části [Dopravní burst.](#traffic-burst)

> [!NOTE]
> StackExchange.Redis 1.1.603 a `local-cpu` novější `TimeoutException` obsahuje metriku v chybové zprávy. Ujistěte se, že používáte nejnovější verzi [balíčku StackExchange.Redis NuGet](https://www.nuget.org/packages/StackExchange.Redis/). Tam jsou chyby neustále opravuje v kódu, aby bylo robustnější na časové osy, takže s nejnovější verzí je důležité.
>

Chcete-li zmírnit vysoké využití procesoru klienta:

- Zjistěte, co způsobuje špičky procesoru.
- Upgradujte klienta na větší velikost virtuálního počítače s větší kapacitou procesoru.

## <a name="client-side-bandwidth-limitation"></a>Omezení šířky pásma na straně klienta

V závislosti na architektuře klientských počítačů mohou mít omezení na to, kolik šířky pásma sítě mají k dispozici. Pokud klient překročí dostupnou šířku pásma přetížením kapacity sítě, nebudou data zpracována na straně klienta tak rychle, jak je server odesílá. Tato situace může vést k časovým časem.

Sledujte, jak se vaše využití šířky pásma v průběhu času mění pomocí [příkladu `BandwidthLogger` ](https://github.com/JonCole/SampleCode/blob/master/BandWidthMonitor/BandwidthLogger.cs). Tento kód nemusí úspěšně spustit v některých prostředích s omezenými oprávněními (jako jsou weby Azure).

Chcete-li zmírnit spotřebu šířky pásma sítě nebo zvýšit velikost virtuálního počítače klienta na jeden s větší kapacitou sítě.

## <a name="large-request-or-response-size"></a>Velká velikost požadavku nebo odpovědi

Velký požadavek/odpověď může způsobit časové přepočet. Jako příklad předpokládejme, že hodnota časového času nakonfigurovaná v klientovi je 1 sekunda. Aplikace požaduje dva klíče (například "A" a "B") současně (pomocí stejného fyzického síťového připojení). Většina klientů podporuje žádost "pipelining", kde jsou oba požadavky "A" a "B" odesílány jeden po druhém bez čekání na odpovědi. Server odešle odpovědi zpět ve stejném pořadí. Pokud odpověď 'A' je velký, může sníst většinu časového odčasového času pro pozdější požadavky.

V následujícím příkladu jsou požadavek 'A' a "B" odeslány rychle na server. Server začne rychle odesílat odpovědi 'A' a 'B'. Z důvodu doby přenosu dat musí odpověď "B" počkat za časovým obdobím odpovědi 'A', i když server reagoval rychle.

    |-------- 1 Second Timeout (A)----------|
    |-Request A-|
         |-------- 1 Second Timeout (B) ----------|
         |-Request B-|
                |- Read Response A --------|
                                           |- Read Response B-| (**TIMEOUT**)

Tento požadavek/odpověď je obtížné měřit. Můžete instrumentovat svůj klientský kód ke sledování velkých požadavků a odpovědí.

Rozlišení pro velké velikosti odpovědí jsou různé, ale zahrnují:

1. Optimalizujte aplikaci pro velký počet malých hodnot, nikoli několik velkých hodnot.
    - Upřednostňovaným řešením je rozdělit data do souvisejících menších hodnot.
    - Viz příspěvek [Jaký je ideální rozsah velikostí hodnot pro redis? Je 100 KB příliš velké?](https://groups.google.com/forum/#!searchin/redis-db/size/redis-db/n7aa2A4DZDs/3OeEPHSQBAAJ) podrobnosti o tom, proč jsou doporučeny menší hodnoty.
1. Zvětšení velikosti virtuálního počítače získáte vyšší možnosti šířky pásma
    - Větší šířka pásma na virtuálním počítači klienta nebo serveru může zkrátit dobu přenosu dat pro větší odpovědi.
    - Porovnejte aktuální využití sítě na obou počítačích s omezeními aktuální velikosti virtuálního počítače. Větší šířka pásma pouze na serveru nebo pouze na straně klienta nemusí stačit.
1. Zvyšte počet objektů připojení, které aplikace používá.
    - Použití přístupu kruhového dotazování k vytvoření požadavků přes různé objekty připojení.

## <a name="additional-information"></a>Další informace

- [Řešení potíží se službou Azure Cache for Redis na straně serveru](cache-troubleshoot-server.md)
- [Jak mohu porovnat a otestovat výkon mezipaměti?](cache-faq.md#how-can-i-benchmark-and-test-the-performance-of-my-cache)
