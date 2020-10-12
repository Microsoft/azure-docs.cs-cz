---
title: Řešení potíží se službou Azure Cache for Redis na straně serveru
description: Naučte se řešit běžné problémy na straně serveru s mezipamětí Azure pro Redis, jako je tlak paměti, vysoký procesor, dlouho běžící příkazy nebo omezení šířky pásma.
author: yegu-ms
ms.author: yegu
ms.service: cache
ms.topic: conceptual
ms.date: 10/18/2019
ms.openlocfilehash: 12d78846f5892e71388de6e6e76b868f9b14d4de
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "88008912"
---
# <a name="troubleshoot-azure-cache-for-redis-server-side-issues"></a>Řešení potíží se službou Azure Cache for Redis na straně serveru

Tato část popisuje problémy, ke kterým dochází z důvodu podmínky v mezipaměti Azure pro Redis nebo virtuálních počítačů, které ji hostují.

- [Zatížení paměti na serveru Redis](#memory-pressure-on-redis-server)
- [Vysoké využití procesoru nebo zatížení serveru](#high-cpu-usage-or-server-load)
- [Dlouhotrvající příkazy](#long-running-commands)
- [Omezení šířky pásma na straně serveru](#server-side-bandwidth-limitation)

> [!NOTE]
> Několik kroků pro řešení potíží v této příručce obsahuje pokyny ke spouštění příkazů Redis a monitorování různých metrik výkonu. Další informace a pokyny najdete v článcích v části [Další informace](#additional-information) .
>

## <a name="memory-pressure-on-redis-server"></a>Zatížení paměti na serveru Redis

Tlak paměti na straně serveru vede ke všem druhům problémů s výkonem, které mohou zpozdit zpracování požadavků. Když dojde k zatížení paměti, systém může data stránkovat na disk. Při této _chybě stránky_ dojde k výraznému zpomalení systému. Existuje několik možných příčin tohoto zatížení paměti:

- Mezipaměť je vyplněna daty poblíž maximální kapacity.
- Redis zobrazuje velkou fragmentaci paměti. Tato fragmentace je nejčastěji způsobena ukládáním velkých objektů, protože Redis je optimalizován pro malé objekty.

Redis zpřístupňuje dvě statistiky prostřednictvím příkazu [info](https://redis.io/commands/info) , který vám může přispět k identifikaci tohoto problému: "used_memory" a "used_memory_rss". [Tyto metriky můžete zobrazit](cache-how-to-monitor.md#view-metrics-with-azure-monitor) pomocí portálu.

Pokud chcete pomoct zajistit, aby využití paměti bylo v pořádku, můžete provést několik změn:

- [Nakonfigurujte zásady paměti](cache-configure.md#maxmemory-policy-and-maxmemory-reserved) a nastavte časy vypršení platnosti vašich klíčů. Pokud dochází k fragmentaci, tyto zásady nemusí být dostatečné.
- [Nakonfigurujte maxmemory vyhrazenou hodnotu](cache-configure.md#maxmemory-policy-and-maxmemory-reserved) , která je dostatečně velká, aby bylo možné kompenzovat fragmentaci paměti.
- Rozdělte velké objekty uložené v mezipaměti na menší související objekty.
- [Vytvořte upozornění](cache-how-to-monitor.md#alerts) na metriky, jako je metrika využité paměti, abyste včas dostávali upozornění na potenciální dopady.
- [Škálovat](cache-how-to-scale.md) na větší velikost mezipaměti s větší kapacitou paměti.

## <a name="high-cpu-usage-or-server-load"></a>Vysoké využití procesoru nebo zatížení serveru

Vysoké zatížení serveru nebo využití procesoru znamená, že server nemůže včas zpracovávat žádosti. Server může být pomalý a nemůže zachovávat sazby za žádosti.

[Monitorujte metriky](cache-how-to-monitor.md#view-metrics-with-azure-monitor) , jako je například zatížení procesoru nebo serveru. Sledujte špičky využití procesoru, které odpovídají časovým limitům.

Pro zmírnění vysokého zatížení serveru můžete provést několik změn:

- Prozkoumejte, co způsobuje špičky procesoru, jako jsou například [dlouhotrvající příkazy](#long-running-commands) , které jsou uvedeny níže, nebo chyby stránky kvůli vysokému zatížení paměti.
- [Vytvářejte upozornění](cache-how-to-monitor.md#alerts) na metriky, jako je například zatížení procesoru nebo serveru, abyste byli včas informováni o možných dopadech.
- [Škálujte](cache-how-to-scale.md) na větší velikost mezipaměti s větší kapacitou procesoru.

## <a name="long-running-commands"></a>Dlouhotrvající příkazy

Některé příkazy Redis jsou dražší ke spouštění než jiné. V [dokumentaci k](https://redis.io/commands) příkazům Redis se zobrazuje složitost času jednotlivých příkazů. Vzhledem k tomu, že zpracování příkazu Redis je v jednom vlákně, příkaz, který trvá spuštění, zablokuje všechny ostatní, kteří jsou po ní. Měli byste zkontrolovat příkazy, které jste vystavili na server Redis, abyste pochopili dopad na výkon. Například příkaz [klíče](https://redis.io/commands/keys) se často používá bez vědomí, že se jedná O operaci o (N). KLÍČům se můžete vyhnout tak, že pomocí [kontroly](https://redis.io/commands/scan) omezíte špičky procesoru.

Pomocí příkazu [SLOWLOG](https://redis.io/commands/slowlog) můžete měřit náročné příkazy, které se spouštějí na serveru.

## <a name="server-side-bandwidth-limitation"></a>Omezení šířky pásma na straně serveru

Různé velikosti mezipaměti mají různé kapacity šířky pásma sítě. Pokud server překračuje dostupnou šířku pásma, data se do klienta odešlou rychleji. Žádosti klientů by mohly vynutit vypršení časového limitu, protože server nemůže doručovat data do klienta dostatečně rychle.

Metriky "čtení z mezipaměti" a "zápis do mezipaměti" lze použít k zobrazení, kolik šířky pásma na straně serveru se používá. [Tyto metriky můžete zobrazit](cache-how-to-monitor.md#view-metrics-with-azure-monitor) na portálu.

Omezení situací, kdy je využití šířky pásma sítě blízko maximální kapacity:

- Změňte chování volání klientů tak, aby se snížilo zatížení sítě.
- [Vytvořte upozornění](cache-how-to-monitor.md#alerts) na metriky, jako je metrika čtení z mezipaměti nebo zápisu do mezipaměti, abyste včas dostávali upozornění na potenciální dopady.
- [Škálujte](cache-how-to-scale.md) na větší velikost mezipaměti s větší kapacitou šířky pásma sítě.

## <a name="additional-information"></a>Další informace

- [Řešení potíží se službou Azure Cache for Redis na straně klienta](cache-troubleshoot-client.md)
- [Výběr správné úrovně](cache-overview.md#choosing-the-right-tier)
- [Jak mohu srovnávací testy a testovat výkon své mezipaměti?](cache-management-faq.md#how-can-i-benchmark-and-test-the-performance-of-my-cache)
- [Monitorování Azure Cache for Redis](cache-how-to-monitor.md)
- [Jak můžu spustit příkazy Redis?](cache-development-faq.md#how-can-i-run-redis-commands)
