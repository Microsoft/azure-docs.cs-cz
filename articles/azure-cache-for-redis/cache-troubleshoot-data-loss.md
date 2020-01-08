---
title: Řešení potíží se ztrátou dat v mezipaměti Azure pro Redis
description: Naučte se řešit problémy se ztrátou dat pomocí Azure cache pro Redis, jako je částečná ztráta klíčů, vypršení platnosti klíče nebo úplná ztráta klíčů.
author: yegu-ms
ms.author: yegu
ms.service: cache
ms.topic: conceptual
ms.date: 10/17/2019
ms.openlocfilehash: d54506b94f076f0a3d967f88bd4e2960a1ca6396
ms.sourcegitcommit: ce4a99b493f8cf2d2fd4e29d9ba92f5f942a754c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/28/2019
ms.locfileid: "75530897"
---
# <a name="troubleshoot-data-loss-in-azure-cache-for-redis"></a>Řešení potíží se ztrátou dat v mezipaměti Azure pro Redis

Tento článek popisuje, jak diagnostikovat skutečné nebo zjištěné ztráty dat, které se můžou vyskytnout v mezipaměti Azure pro Redis.

> [!NOTE]
> Několik kroků pro řešení potíží v této příručce obsahuje pokyny ke spouštění příkazů Redis a monitorování různých metrik výkonu. Další informace a pokyny najdete v článcích v části [Další informace](#additional-information) .
>

## <a name="partial-loss-of-keys"></a>Částečná ztráta klíčů

Azure cache pro Redis po uložení do paměti náhodně neodstraní klíče. Odebere ale klíče v reakci na zásady vypršení platnosti nebo vyřazení a explicitní příkazy pro odstraňování klíčů. Klíče, které byly zapsány do hlavního uzlu v mezipaměti Azure Premium nebo standard pro instanci Redis, nemusí být okamžitě k dispozici v replice. Data se replikují z hlavního serveru do repliky v asynchronním a neblokujícím způsobu.

Pokud zjistíte, že klíče z mezipaměti zmizely, podívejte se na tyto možné příčiny:

| Příčina | Popis |
|---|---|
| [Vypršení platnosti klíče](#key-expiration) | Klíče se odeberou kvůli časovým limitům nastaveným pro ně. |
| [Vyřazení klíčů](#key-eviction) | Klíče jsou odebrány v části tlak paměti. |
| [Odstranění klíče](#key-deletion) | Klíče jsou odebrány pomocí explicitních příkazů DELETE. |
| [Asynchronní replikace](#async-replication) | V replice nejsou k dispozici klíče kvůli zpožděním replikace dat. |

### <a name="key-expiration"></a>Vypršení platnosti klíče

Azure cache pro Redis odebere klíč automaticky, pokud je klíč přiřazen časový limit a období uplynulo. Další informace o vypršení platnosti klíče Redis najdete v dokumentaci k příkazům vypršení [platnosti](https://redis.io/commands/expire) . Hodnoty časového limitu lze také nastavit pomocí příkazů [set](https://redis.io/commands/set), [SETEX](https://redis.io/commands/setex), [Poznámka GetSet](https://redis.io/commands/getset)a další **\*Store** .

Chcete-li získat statistiku o tom, kolik klíčů vypršelo, použijte příkaz [info](https://redis.io/commands/info) . V části `Stats` se zobrazuje celkový počet klíčů s vypršenou platností. V části `Keyspace` najdete další informace o počtu klíčů s časovým limitem a s průměrnou hodnotou časového limitu.

```
# Stats

expired_keys:46583

# Keyspace

db0:keys=3450,expires=2,avg_ttl=91861015336
```

Můžete se také podívat na diagnostické metriky pro vaši mezipaměť, abyste viděli, jestli existuje korelace mezi tím, kdy klíč chybí, a špička v klíčích s vypršenou platností. Další informace o použití oznámení o prostoru a **monitorování** k ladění těchto typů problémů najdete v příloze [Redisch neúspěšných přístupů k prostoru](https://gist.github.com/JonCole/4a249477142be839b904f7426ccccf82#appendix) .

### <a name="key-eviction"></a>Vyřazení klíčů

Mezipaměť Azure pro Redis vyžaduje místo v paměti pro ukládání dat. Vyprázdní klíče a uvolní dostupnou paměť v případě potřeby. Když hodnoty **used_memory** nebo **Used_memory_rss** v příkazu [info](https://redis.io/commands/info) přistupují k nakonfigurovanému nastavení **maxmemory** , Azure cache pro Redis spustí vyřazení klíčů z paměti na základě [zásad mezipaměti](https://redis.io/topics/lru-cache).

Počet vyřazených klíčů můžete monitorovat pomocí příkazu [info](https://redis.io/commands/info) :

```
# Stats

evicted_keys:13224
```

Můžete se také podívat na diagnostické metriky pro vaši mezipaměť a zjistit, jestli existuje korelace mezi tím, kdy klíč chybí, a špičkou v vyřazených klíčích. Další informace o použití oznámení o prostoru a **monitorování** k ladění těchto typů problémů najdete v příloze [Redisch neúspěšných přístupů k prostoru](https://gist.github.com/JonCole/4a249477142be839b904f7426ccccf82#appendix) .

### <a name="key-deletion"></a>Odstranění klíče

Klienti Redis můžou pomocí příkazu [del](https://redis.io/commands/del) nebo [HDEL](https://redis.io/commands/hdel) explicitně odebrat klíče z mezipaměti Azure pro Redis. Počet operací odstranění můžete sledovat pomocí příkazu [info](https://redis.io/commands/info) . Pokud byly volány příkazy **del** nebo **HDEL** , budou uvedeny v části `Commandstats`.

```
# Commandstats

cmdstat_del:calls=2,usec=90,usec_per_call=45.00

cmdstat_hdel:calls=1,usec=47,usec_per_call=47.00
```

### <a name="async-replication"></a>Asynchronní replikace

Jakákoli instance Azure cache for Redis v úrovni Standard nebo Premium je nakonfigurovaná s hlavním uzlem a aspoň jednou replikou. Data jsou zkopírována z hlavního serveru do repliky asynchronně pomocí procesu na pozadí. Web [Redis.IO](https://redis.io/topics/replication) popisuje, jak obecně funguje replikace dat Redis. Pro scénáře, kdy klienti zapisují do Redis často, může dojít k částečné ztrátě dat, protože je zaručená okamžitá replikace. Pokud se například hlavní uzel *po* zapsání klíče do něj zapíše, ale *před tím, než* bude moci proces na pozadí odeslat tento klíč do repliky, dojde ke ztrátě klíče, když replika převezme novou hlavní databázi.

## <a name="major-or-complete-loss-of-keys"></a>Hlavní nebo úplná ztráta klíčů

Pokud z mezipaměti nezmizí většina nebo všechny klíče, podívejte se na tyto možné příčiny:

| Příčina | Popis |
|---|---|
| [Vyprazdňování klíče](#key-flushing) | Klíče byly vymazány ručně. |
| [Nesprávný výběr databáze](#incorrect-database-selection) | Mezipaměť Azure pro Redis je nastavená na použití jiné než výchozí databáze. |
| [Selhání instance Redis](#redis-instance-failure) | Server Redis není k dispozici. |

### <a name="key-flushing"></a>Vyprazdňování klíče

Klienti mohou volat příkaz [FLUSHDB](https://redis.io/commands/flushdb) a odebrat všechny klíče v *jedné* databázi nebo [FLUSHALL](https://redis.io/commands/flushall) pro odebrání všech klíčů ze *všech* databází v mezipaměti Redis. Chcete-li zjistit, zda byly klíče vyprázdněny, použijte příkaz [info](https://redis.io/commands/info) . V části `Commandstats` se dozvíte, zda byl buď volán příkaz **flush** :

```
# Commandstats

cmdstat_flushall:calls=2,usec=112,usec_per_call=56.00

cmdstat_flushdb:calls=1,usec=110,usec_per_call=52.00
```

### <a name="incorrect-database-selection"></a>Nesprávný výběr databáze

Azure cache pro Redis ve výchozím nastavení používá databázi **DB0** . Pokud přepnete do jiné databáze (například **DB1**) a pokusíte se z ní číst klíče, mezipaměť Azure pro Redis je nenalezne. Každá databáze je logicky oddělená jednotka a obsahuje jinou datovou sadu. Pomocí příkazu [Select](https://redis.io/commands/select) můžete použít další dostupné databáze a vyhledat klíče v každém z nich.

### <a name="redis-instance-failure"></a>Selhání instance Redis

Redis je úložiště dat v paměti. Data se uchovávají na fyzických nebo virtuálních počítačích, které hostují mezipaměť Redis Cache. Instance Azure cache for Redis se v úrovni Basic spouští jenom na jednom virtuálním počítači (VM). Pokud je tento virtuální počítač vypnutý, ztratí se všechna data uložená v mezipaměti. 

Mezipamětí v úrovních Standard a Premium nabízejí mnohem větší odolnost proti ztrátě dat pomocí dvou virtuálních počítačů v replikované konfiguraci. V případě selhání hlavního uzlu v takové mezipaměti převezme uzel repliky automaticky data. Tyto virtuální počítače se nacházejí v samostatných doménách pro chyby a aktualizace, aby se minimalizovala pravděpodobnost, že se současně stane nedostupnými. Pokud se ale dojde k výpadku velkých Datacenter, virtuální počítače se ale pořád rozstanou pohromadě. V těchto vzácných případech dojde ke ztrátě vašich dat.

Zvažte použití [Trvalost dat Redis](https://redis.io/topics/persistence) a [geografické replikace](https://docs.microsoft.com/azure/azure-cache-for-redis/cache-how-to-geo-replication) k vylepšení ochrany vašich dat před těmito selháními infrastruktury.

## <a name="additional-information"></a>Další informace

- [Řešení potíží se službou Azure Cache for Redis na straně serveru](cache-troubleshoot-server.md)
- [Jakou mezipaměť Azure pro nabídku a velikost Redis mám použít?](cache-faq.md#what-azure-cache-for-redis-offering-and-size-should-i-use)
- [Jak monitorovat Azure cache pro Redis](cache-how-to-monitor.md)
- [Jak můžu spustit příkazy Redis?](cache-faq.md#how-can-i-run-redis-commands)
