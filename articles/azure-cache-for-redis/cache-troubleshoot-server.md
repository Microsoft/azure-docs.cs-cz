---
title: Řešení potíží se službou Azure Cache for Redis na straně serveru
description: Zjistěte, jak vyřešit běžné problémy na straně serveru s Azure Cache pro Redis, jako je tlak v paměti, vysoký procesor, dlouho běžící příkazy nebo omezení šířky pásma.
author: yegu-ms
ms.author: yegu
ms.service: cache
ms.topic: conceptual
ms.date: 10/18/2019
ms.openlocfilehash: a68c27de304a0da6470745ee4abf69590d9bf78c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "79277931"
---
# <a name="troubleshoot-azure-cache-for-redis-server-side-issues"></a>Řešení potíží se službou Azure Cache for Redis na straně serveru

Tato část popisuje problémy s řešením potíží, ke kterým dochází z důvodu podmínky na Azure Cache pro Redis nebo virtuální chody, které ji hostují.

- [Zatížení paměti na serveru Redis](#memory-pressure-on-redis-server)
- [Vysoké využití procesoru nebo zatížení serveru](#high-cpu-usage-or-server-load)
- [Dlouhotrvající příkazy](#long-running-commands)
- [Omezení šířky pásma na straně serveru](#server-side-bandwidth-limitation)

> [!NOTE]
> Několik kroků řešení potíží v této příručce obsahuje pokyny ke spuštění příkazů Redis a sledování různých metrik výkonu. Další informace a pokyny naleznete v článcích v části [Další informace.](#additional-information)
>

## <a name="memory-pressure-on-redis-server"></a>Zatížení paměti na serveru Redis

Tlak paměti na straně serveru vede ke všem druhům problémů s výkonem, které mohou zpozdit zpracování požadavků. Při zásahu tlaku v paměti může systém stránkovat data na disk. Tato _chyba stránky_ způsobí, že systém výrazně zpomalí. Existuje několik možných příčin tohoto tlaku paměti:

- Mezipaměť je naplněna daty, která se blíží maximální kapacitě.
- Redis je vidět vysokou fragmentaci paměti. Tato fragmentace je nejčastěji způsobena ukládáním velkých objektů, protože Redis je optimalizován pro malé objekty.

Redis vystavuje dva statistiky prostřednictvím příkazu [INFO,](https://redis.io/commands/info) který vám pomůže identifikovat tento problém: "used_memory" a "used_memory_rss". Tyto [metriky](cache-how-to-monitor.md#view-metrics-with-azure-monitor) můžete zobrazit pomocí portálu.

Existuje několik možných změn, které vám pomohou udržet využití paměti v pořádku:

- [Nakonfigurujte zásady paměti](cache-configure.md#maxmemory-policy-and-maxmemory-reserved) a nastavte u klíčů dobu vypršení platnosti. Tato zásada nemusí být dostatečná, pokud máte fragmentaci.
- [Nakonfigurujte hodnotu vyhrazenou pro maximální paměť,](cache-configure.md#maxmemory-policy-and-maxmemory-reserved) která je dostatečně velká, aby kompenzovala fragmentaci paměti.
- Rozdělte velké objekty uložené v mezipaměti na menší související objekty.
- [Vytvořte upozornění](cache-how-to-monitor.md#alerts) na metriky, jako je použitá paměť, která má být včas upozorněna na potenciální dopady.
- [Škálujte](cache-how-to-scale.md) na větší velikost mezipaměti s větší kapacitou paměti.

## <a name="high-cpu-usage-or-server-load"></a>Vysoké využití procesoru nebo zatížení serveru

Vysoké zatížení serveru nebo využití procesoru znamená, že server nemůže zpracovat požadavky včas. Server může být pomalé reagovat a nemůže držet krok s požadavky sazby.

[Sledujte metriky,](cache-how-to-monitor.md#view-metrics-with-azure-monitor) jako je zatížení procesoru nebo serveru. Sledujte špičky v využití procesoru, které odpovídají časovým limitům.

Existuje několik změn, které můžete provést ke zmírnění vysoké zatížení serveru:

- Zjistěte, co způsobuje špičky procesoru, jako jsou [dlouhotrvající příkazy](#long-running-commands) uvedené níže nebo chyba stránky z důvodu vysokého tlaku paměti.
- [Vytvořte výstrahy](cache-how-to-monitor.md#alerts) na metriky, jako je zatížení procesoru nebo serveru, abyste byli včas upozorněni na potenciální dopady.
- [Škálujte](cache-how-to-scale.md) na větší velikost mezipaměti s větší kapacitou procesoru.

## <a name="long-running-commands"></a>Dlouhotrvající příkazy

Některé příkazy Redis jsou dražší než jiné. [Redis příkazy dokumentace](https://redis.io/commands) zobrazuje časovou složitost každého příkazu. Vzhledem k tomu, že zpracování příkazů Redis je jednovláknové, příkaz, který vyžaduje čas ke spuštění, zablokuje všechny ostatní, které po něm přijdou. Měli byste zkontrolovat příkazy, které vydáváte na server Redis, abyste pochopili jejich dopady na výkon. Například [příkaz KEYS](https://redis.io/commands/keys) se často používá bez vědomí, že se jedná o operaci O(N). Můžete se vyhnout klíče pomocí [funkce Procan](https://redis.io/commands/scan) ke snížení špičky procesoru.

Pomocí příkazu [SLOWLOG](https://redis.io/commands/slowlog) můžete měřit nákladné příkazy prováděné proti serveru.

## <a name="server-side-bandwidth-limitation"></a>Omezení šířky pásma na straně serveru

Různé velikosti mezipaměti mají různé kapacity šířky pásma sítě. Pokud server překročí dostupnou šířku pásma, nebudou data klientovi odeslána tak rychle. Požadavky klientů může časový modus out, protože server nelze nabízená data klientovi dostatečně rychle.

Metriky "Čtení do mezipaměti" a "Zápis do mezipaměti" lze použít k zobrazení, kolik šířky pásma na straně serveru se používá. Tyto [metriky](cache-how-to-monitor.md#view-metrics-with-azure-monitor) můžete zobrazit na portálu.

Chcete-li zmírnit situace, kdy využití šířky pásma sítě se blíží maximální kapacitě:

- Změňte chování volání klienta, abyste snížili požadavky na síť.
- [Vytvořte výstrahy](cache-how-to-monitor.md#alerts) na metriky, jako je čtení do mezipaměti nebo zápis do mezipaměti, abyste byli včas upozorněni na potenciální dopady.
- [Škálujte](cache-how-to-scale.md) na větší velikost mezipaměti s větší kapacitou šířky pásma sítě.

## <a name="additional-information"></a>Další informace

- [Řešení potíží se službou Azure Cache for Redis na straně klienta](cache-troubleshoot-client.md)
- [Jakou mezipaměť Azure pro Redis mám použít?](cache-faq.md#what-azure-cache-for-redis-offering-and-size-should-i-use)
- [Jak mohu porovnat a otestovat výkon mezipaměti?](cache-faq.md#how-can-i-benchmark-and-test-the-performance-of-my-cache)
- [Jak monitorovat Azure Cache pro Redis](cache-how-to-monitor.md)
- [Jak lze spustit příkazy Redis?](cache-faq.md#how-can-i-run-redis-commands)
