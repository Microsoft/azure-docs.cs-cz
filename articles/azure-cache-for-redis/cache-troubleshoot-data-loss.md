---
title: Řešení potíží se ztrátou dat ve službě Azure Cache for Redis
description: Zjistěte, jak vyřešit problémy se ztrátou dat pomocí Azure Cache for Redis, jako je částečná ztráta klíčů, vypršení platnosti klíčů nebo úplná ztráta klíčů.
author: yegu-ms
ms.author: yegu
ms.service: cache
ms.topic: conceptual
ms.date: 10/17/2019
ms.openlocfilehash: d54506b94f076f0a3d967f88bd4e2960a1ca6396
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "75530897"
---
# <a name="troubleshoot-data-loss-in-azure-cache-for-redis"></a>Řešení potíží se ztrátou dat ve službě Azure Cache for Redis

Tento článek popisuje, jak diagnostikovat skutečné nebo vnímané ztráty dat, ke kterým může dojít v azure cache pro Redis.

> [!NOTE]
> Několik kroků řešení potíží v této příručce obsahuje pokyny ke spuštění příkazů Redis a sledování různých metrik výkonu. Další informace a pokyny naleznete v článcích v části [Další informace.](#additional-information)
>

## <a name="partial-loss-of-keys"></a>Částečná ztráta klíčů

Azure Cache for Redis neodstraní náhodně klíče po jejich uložení v paměti. Však odebere klíče v reakci na vypršení platnosti nebo vyřazovací zásady a explicitní příkazy pro odstranění klíčů. Klíče, které byly zapsány do hlavního uzlu v premium nebo standardní Azure Cache pro Redis instance také nemusí být k dispozici na replice hned. Data jsou replikována z hlavního serveru do repliky asynchronním a neblokujícím způsobem.

Pokud zjistíte, že klíče zmizely z mezipaměti, zkontrolujte následující možné příčiny:

| Příčina | Popis |
|---|---|
| [Vypršení platnosti klíče](#key-expiration) | Klíče jsou odebrány z důvodu časového nastavení na nich. |
| [Vystěhování klíče](#key-eviction) | Klíče jsou odstraněny pod tlakem paměti. |
| [Odstranění klíče](#key-deletion) | Klíče jsou odebrány explicitními příkazy pro odstranění. |
| [Asynchronní replikace](#async-replication) | Klíče nejsou k dispozici v replice z důvodu zpoždění replikace dat. |

### <a name="key-expiration"></a>Vypršení platnosti klíče

Azure Cache for Redis odebere klíč automaticky, pokud je klíči přiřazen časový čas a toto období uplynulo. Další informace o vypršení platnosti klíče Redis naleznete v dokumentaci k příkazům [EXPIRE.](https://redis.io/commands/expire) Hodnoty časového času lze také nastavit pomocí příkazů [SET](https://redis.io/commands/set), [SETEX](https://redis.io/commands/setex), [GETSET](https://redis.io/commands/getset)a dalších ** \*příkazů STORE.**

Chcete-li získat statistiky o tom, kolik klíčů vypršelo, použijte příkaz [INFO.](https://redis.io/commands/info) V `Stats` části se zobrazí celkový počet klíčů s prošlou platností. Část `Keyspace` obsahuje další informace o počtu klíčů s časovým rozsahem a průměrnou hodnotou časového úseku.

```
# Stats

expired_keys:46583

# Keyspace

db0:keys=3450,expires=2,avg_ttl=91861015336
```

Můžete se také podívat na diagnostické metriky pro vaši mezipaměť, abyste zjistili, zda existuje korelace mezi zmizením klíče a špičkou klíčů s ukončenou platností. Informace o použití oznámení o klíčovém prostoru nebo **monitor monitoru** k ladění těchto typů problémů naleznete v dodatku k ladění funkce [Redis Keyspace.](https://gist.github.com/JonCole/4a249477142be839b904f7426ccccf82#appendix)

### <a name="key-eviction"></a>Vystěhování klíče

Azure Cache for Redis vyžaduje místo v paměti pro ukládání dat. V případě potřeby vyčistí klíče, aby se uvolnila dostupná paměť. Když **used_memory** nebo **used_memory_rss** hodnoty v příkazu [INFO](https://redis.io/commands/info) přiblíží k nakonfigurovanému nastavení **maximální paměti,** Azure Cache for Redis spustí vyřazování klíčů z paměti na základě [zásad mezipaměti](https://redis.io/topics/lru-cache).

Počet vyřazených klíčů můžete sledovat pomocí příkazu [INFO:](https://redis.io/commands/info)

```
# Stats

evicted_keys:13224
```

Můžete se také podívat na diagnostické metriky pro vaši mezipaměť, abyste zjistili, zda existuje korelace mezi zmizením klíče a špičkou v vyřazených klíčích. Informace o použití oznámení o klíčovém prostoru nebo **monitor monitoru** k ladění těchto typů problémů naleznete v dodatku k ladění funkce [Redis Keyspace.](https://gist.github.com/JonCole/4a249477142be839b904f7426ccccf82#appendix)

### <a name="key-deletion"></a>Odstranění klíče

Redis klienti můžete vydat [příkaz DEL](https://redis.io/commands/del) nebo [HDEL](https://redis.io/commands/hdel) explicitně odebrat klíče z mezipaměti Azure pro Redis. Počet operací odstranění můžete sledovat pomocí příkazu [INFO.](https://redis.io/commands/info) Pokud byly volány příkazy **DEL** nebo **HDEL,** `Commandstats` budou uvedeny v části.

```
# Commandstats

cmdstat_del:calls=2,usec=90,usec_per_call=45.00

cmdstat_hdel:calls=1,usec=47,usec_per_call=47.00
```

### <a name="async-replication"></a>Asynchronní replikace

Všechny instance Azure Cache for Redis na úrovni Standard nebo Premium je nakonfigurovaná s hlavním uzlem a alespoň jednou replikou. Data jsou zkopírována z hlavního serveru do repliky asynchronně pomocí procesu na pozadí. Web [redis.io](https://redis.io/topics/replication) popisuje, jak replikace dat Redis funguje obecně. Pro scénáře, kde klienti často zapisují, může dojít ke ztrátě částečné hodu dat, protože je zaručeno, že tato replikace bude okamžitá. Například pokud hlavní přejde dolů *poté, co* klient zapíše klíč k němu, ale *před* procesem na pozadí má šanci odeslat tento klíč do repliky, klíč je ztracen, když replika převezme jako nový hlavní server.

## <a name="major-or-complete-loss-of-keys"></a>Velká nebo úplná ztráta klíčů

Pokud většina nebo všechny klíče zmizely z mezipaměti, zkontrolujte následující možné příčiny:

| Příčina | Popis |
|---|---|
| [Vyprázdnění klíčů](#key-flushing) | Klíče byly vymazány ručně. |
| [Nesprávný výběr databáze](#incorrect-database-selection) | Azure Cache for Redis je nastavená na použití jiné než výchozí databáze. |
| [Selhání instance Redis](#redis-instance-failure) | Server Redis není k dispozici. |

### <a name="key-flushing"></a>Vyprázdnění klíčů

Klienti mohou volat [flushdb](https://redis.io/commands/flushdb) příkaz odebrat všechny klíče v *jedné* databázi nebo [FLUSHALL](https://redis.io/commands/flushall) odebrat všechny klíče ze *všech* databází v mezipaměti Redis. Chcete-li zjistit, zda byly klíče vyprázdněny, použijte příkaz [INFO.](https://redis.io/commands/info) V `Commandstats` části se zobrazí, zda byl volán příkaz **FLUSH:**

```
# Commandstats

cmdstat_flushall:calls=2,usec=112,usec_per_call=56.00

cmdstat_flushdb:calls=1,usec=110,usec_per_call=52.00
```

### <a name="incorrect-database-selection"></a>Nesprávný výběr databáze

Azure Cache for Redis používá ve výchozím nastavení databázi **db0.** Pokud přepnete do jiné databáze (například **db1**) a pokusíte se číst klíče z ní, Azure Cache pro Redis nenajdete je tam. Každá databáze je logicky samostatná jednotka a obsahuje jinou datovou sadu. Pomocí příkazu [SELECT](https://redis.io/commands/select) můžete použít jiné dostupné databáze a vyhledat klíče v každé z nich.

### <a name="redis-instance-failure"></a>Selhání instance Redis

Redis je úložiště dat v paměti. Data jsou uložena na fyzických nebo virtuálních počítačích, které hostují mezipaměť Redis. Instance Azure Cache for Redis na úrovni Basic se spouští pouze na jednom virtuálním počítači. Pokud je tento virtuální virtuální hotel vypnutý, dojde ke ztrátě všech dat, která jste uložili do mezipaměti. 

Mezipaměti na úrovních Standard a Premium nabízejí mnohem vyšší odolnost proti ztrátě dat pomocí dvou virtuálních počítačů v replikované konfiguraci. Pokud hlavní uzel v takové mezipaměti selže, uzel repliky převezme automatické zobrazování dat. Tyto virtuální servery jsou umístěny v samostatných doménách pro chyby a aktualizace, aby se minimalizovalo riziko, že se obě budou k dispozici současně. Pokud dojde k výpadku hlavního datového centra, virtuální chod může stále přejít dolů společně. Vaše data budou ztracena v těchto vzácných případech.

Zvažte použití [trvalosti dat Redis](https://redis.io/topics/persistence) a [geografické replikace](https://docs.microsoft.com/azure/azure-cache-for-redis/cache-how-to-geo-replication) ke zlepšení ochrany dat před těmito selháními infrastruktury.

## <a name="additional-information"></a>Další informace

- [Řešení potíží se službou Azure Cache for Redis na straně serveru](cache-troubleshoot-server.md)
- [Jakou mezipaměť Azure pro Redis mám použít?](cache-faq.md#what-azure-cache-for-redis-offering-and-size-should-i-use)
- [Jak monitorovat Azure Cache pro Redis](cache-how-to-monitor.md)
- [Jak lze spustit příkazy Redis?](cache-faq.md#how-can-i-run-redis-commands)
