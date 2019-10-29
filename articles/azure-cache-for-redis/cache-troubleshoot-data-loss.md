---
title: Řešení potíží s Azure cache pro Redis ztráty dat | Microsoft Docs
description: Naučte se řešit problémy se ztrátou dat pomocí Azure cache pro Redis.
services: cache
documentationcenter: ''
author: yegu-ms
manager: maiye
editor: ''
ms.assetid: ''
ms.service: cache
ms.workload: tbd
ms.tgt_pltfrm: cache
ms.devlang: na
ms.topic: article
ms.date: 10/17/2019
ms.author: yegu
ms.openlocfilehash: 4fee7c84b394e84369b28d2a4191d0e581f3beba
ms.sourcegitcommit: 38251963cf3b8c9373929e071b50fd9049942b37
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/29/2019
ms.locfileid: "73044364"
---
# <a name="troubleshoot-azure-cache-for-redis-data-loss"></a>Řešení potíží s Azure cache pro Redis ztrátu dat

Tato část popisuje, jak diagnostikovat skutečné nebo zjištěné ztráty dat, které se můžou vyskytnout v mezipaměti Azure pro Redis.

- [Částečná ztráta klíčů](#partial-loss-of-keys)
- [Hlavní nebo úplná ztráta klíčů](#major-or-complete-loss-of-keys)

> [!NOTE]
> Několik kroků pro řešení potíží v této příručce obsahuje pokyny ke spouštění příkazů Redis a monitorování různých metrik výkonu. Další informace a pokyny najdete v článcích v části [Další informace](#additional-information) .
>

## <a name="partial-loss-of-keys"></a>Částečná ztráta klíčů

Redis po uložení do paměti náhodně neodstraní klíče. Odebere ale klíče v reakci na zásady vypršení platnosti nebo vyřazení a taky explicitní příkazy pro odstranění klíčů. Navíc klíče, které byly zapsány do hlavního uzlu v mezipaměti Azure Premium nebo standard pro Redis, nemusí být hned k dispozici na replice. Data se replikují z hlavního serveru do repliky v asynchronním a neblokujícím způsobu.

Pokud zjistíte, že tyto klíče z mezipaměti zmizely, můžete podle následujících pokynů zjistit, které příčiny můžou být příčinou:

| Příčina | Popis |
|---|---|
| [Vypršení platnosti klíče](#key-expiration) | Klíče jsou odebrány z důvodu nastavených časových limitů. |
| [Vyřazení klíčů](#key-eviction) | Klíče jsou odstraněny při tlaku paměti |
| [Odstranění klíče](#key-deletion) | Klíče jsou odebrány pomocí explicitních příkazů DELETE. |
| [Asynchronní replikace](#async-replication) | V replice nejsou k dispozici klíče kvůli zpožděním při replikaci dat. |

### <a name="key-expiration"></a>Vypršení platnosti klíče

Redis odebere klíč automaticky, pokud se mu přiřadí časový limit a tato doba uplynula. Další podrobnosti o vypršení platnosti klíče Redis najdete v dokumentaci k příkazu [vypršení platnosti](http://redis.io/commands/expire) . Hodnoty časového limitu lze také nastavit pomocí příkazu [set](http://redis.io/commands/set), [SETEX](https://redis.io/commands/setex), [Poznámka GetSet](https://redis.io/commands/getset)a dalších příkazů \*STORE.

Pomocí příkazu [info](http://redis.io/commands/info) můžete získat statistiku o počtu vypršení platnosti klíčů. V části *Statistika* se zobrazuje celkový počet klíčů s vypršenou platností. Část s *místem na disku* poskytuje další informace o počtu klíčů s časovými limity a průměrnou hodnotu časového limitu.

```
# Stats

expired_keys:46583

# Keyspace

db0:keys=3450,expires=2,avg_ttl=91861015336
```

Kromě toho se můžete podívat na diagnostické metriky pro vaši mezipaměť, abyste viděli, jestli existuje korelace mezi tím, kdy klíč chybí, a špička v klíčích s vypršenou platností. Informace o tom, jak používat oznámení a monitorování místa na disku k ladění těchto typů problémů, najdete v [příloze](https://gist.github.com/JonCole/4a249477142be839b904f7426ccccf82#appendix) .

### <a name="key-eviction"></a>Vyřazení klíčů

Redis vyžaduje místo v paměti pro ukládání dat. Vyprázdní klíče pro uvolnění dostupné paměti, pokud je to nutné. Když hodnoty **used_memory** nebo **Used_memory_rss** v příkazu [info](http://redis.io/commands/info) přistupují k nakonfigurovanému nastavení **maxmemory** , Redis spustí vyřazení klíčů z paměti na základě [zásad mezipaměti](http://redis.io/topics/lru-cache).

Pomocí příkazu [info](http://redis.io/commands/info) můžete monitorovat počet vyřazení klíčů.

```
# Stats

evicted_keys:13224
```

Kromě toho se můžete podívat na diagnostické metriky pro vaši mezipaměť a zjistit, jestli existuje korelace mezi tím, kdy klíč chybí, a špičkou v vyřazených klíčích. Informace o tom, jak používat oznámení a monitorování místa na disku k ladění těchto typů problémů, najdete v [příloze](https://gist.github.com/JonCole/4a249477142be839b904f7426ccccf82#appendix) .

### <a name="key-deletion"></a>Odstranění klíče

Klienti Redis můžou pomocí příkazu [del](http://redis.io/commands/del) nebo [HDEL](http://redis.io/commands/hdel) explicitně odebrat klíče z Redis. Počet operací odstranění můžete sledovat pomocí příkazu [info](http://redis.io/commands/info) . Pokud byly volány příkazy DEL nebo HDEL, budou uvedeny v části *Commandstats* .

```
# Commandstats

cmdstat_del:calls=2,usec=90,usec_per_call=45.00

cmdstat_hdel:calls=1,usec=47,usec_per_call=47.00
```

### <a name="async-replication"></a>Asynchronní replikace

Jakákoli mezipaměť Azure pro Redis v úrovni Standard nebo Premium je nakonfigurovaná s hlavním uzlem a aspoň jednou replikou. Data jsou zkopírována z hlavního serveru do repliky asynchronně pomocí procesu na pozadí. Web [Redis.IO](http://redis.io/topics/replication) popisuje, jak obecně funguje replikace dat Redis. V případě scénářů, kdy klienti zapisují do Redis často, může dojít k částečné ztrátě dat kvůli tomu, že je zaručená okamžitá replikace. Pokud se například hlavní uzel přestane _po_ zapsání klíče do něj zapisuje, ale _před_ tím, než bude mít proces na pozadí možnost Odeslat tento klíč do repliky, klíč se ztratí, když replika převezme novou hlavní databázi.

## <a name="major-or-complete-loss-of-keys"></a>Hlavní nebo úplná ztráta klíčů

Pokud zjistíte, že z mezipaměti zmizela většina nebo všechny klíče, můžete podle následujících pokynů zjistit, které příčiny můžou být příčinou:

| Příčina | Popis |
|---|---|
| [Vyprazdňování klíče](#key-flushing) | Klíče byly ručně vyprázdněny |
| [Nesprávný výběr databáze](#incorrect-database-selection) | Redis je nastavená na použití jiné než výchozí databáze. |
| [Selhání instance Redis](#redis-instance-failure) | Server Redis je nedostupný. |

### <a name="key-flushing"></a>Vyprazdňování klíče

Klienti mohou volat příkaz [FLUSHDB](http://redis.io/commands/flushdb) a odebrat všechny klíče v **jedné** databázi nebo [FLUSHALL](http://redis.io/commands/flushall) pro odebrání všech klíčů ze **všech** databází v mezipaměti Redis. To, jestli se klíče vyprázdní, můžete zjistit pomocí příkazu [info](http://redis.io/commands/info) . Zobrazí se v případě, že se v části *Commandstats* zavolal buď příkaz Flush.

```
# Commandstats

cmdstat_flushall:calls=2,usec=112,usec_per_call=56.00

cmdstat_flushdb:calls=1,usec=110,usec_per_call=52.00
```

### <a name="incorrect-database-selection"></a>Nesprávný výběr databáze

Azure cache pro Redis ve výchozím nastavení používá databázi **DB0** . Pokud přepnete do jiné databáze (například DB1) a pokusíte se z ní číst klíče, Redis je nenalezne, protože každá databáze je logicky oddělená jednotka a obsahuje jinou datovou sadu. Pomocí příkazu [Select](http://redis.io/commands/select) můžete použít další dostupné databáze a vyhledat klíče v každém z nich.

### <a name="redis-instance-failure"></a>Selhání instance Redis

Redis je úložiště dat v paměti. Data se uchovávají na fyzických nebo virtuálních počítačích, které hostují Redis. Instance Azure cache for Redis se v úrovni Basic spouští jenom na jednom virtuálním počítači (VM). Pokud je tento virtuální počítač vypnutý, ztratí se všechna data uložená v mezipaměti. Mezipamětí v úrovních Standard a Premium nabízejí mnohem větší odolnost proti ztrátě dat pomocí dvou virtuálních počítačů v replikované konfiguraci. Pokud hlavní uzel v takové mezipaměti selže, bude uzel repliky přebírat data do obsluhy automaticky. Tyto virtuální počítače se nacházejí v samostatných doménách selhání a aktualizují, aby se minimalizovala pravděpodobnost, že se současně stane nedostupnými. V případě výpadku většího datového centra ale můžou virtuální počítače pořád dál nacházet. V těchto vzácných případech dojde ke ztrátě vašich dat.

Měli byste zvážit použití [Trvalost dat Redis](http://redis.io/topics/persistence) a [geografické replikace](https://docs.microsoft.com/azure/azure-cache-for-redis/cache-how-to-geo-replication) k vylepšení ochrany vašich dat před těmito selháními infrastruktury.

## <a name="additional-information"></a>Další informace

- [Řešení potíží s Azure cache pro Redis na straně serveru](cache-troubleshoot-server.md)
- [Jakou mezipaměť Azure pro nabídku a velikost Redis mám použít?](cache-faq.md#what-azure-cache-for-redis-offering-and-size-should-i-use)
- [Jak monitorovat Azure cache pro Redis](cache-how-to-monitor.md)
- [Jak můžu spustit příkazy Redis?](cache-faq.md#how-can-i-run-redis-commands)
