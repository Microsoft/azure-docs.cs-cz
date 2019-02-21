---
title: Repliky pro čtení v Azure Database for PostgreSQL
description: Tento článek popisuje funkce repliky pro čtení ve službě Azure Database for PostgreSQL.
author: rachel-msft
ms.author: raagyema
ms.service: postgresql
ms.topic: conceptual
ms.date: 02/19/2019
ms.openlocfilehash: 40b31f166ea97cfce67d3cc386062e32338ffd45
ms.sourcegitcommit: 75fef8147209a1dcdc7573c4a6a90f0151a12e17
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/20/2019
ms.locfileid: "56455513"
---
# <a name="read-replicas-in-azure-database-for-postgresql"></a>Repliky pro čtení v Azure Database for PostgreSQL

Funkce repliky pro čtení umožňuje replikaci dat ze serveru Azure Database for PostgreSQL na serveru jen pro čtení. Můžete replikovat z hlavního serveru až pěti replikami v rámci stejné oblasti Azure. Repliky jsou aktualizované asynchronně technologie replikace nativní modul PostgreSQL.

> [!IMPORTANT]
> Funkce repliky pro čtení je ve verzi public preview.

Repliky jsou nové servery, které spravujete podobně jako na běžnou – Azure Database for PostgreSQL servery. Pro každé čtení replik, bude se vám účtovat za zřízených výpočetních jádrech a úložiště v GB / měsíc.

Zjistěte, jak [vytvářet a spravovat repliky](howto-read-replicas-portal.md).

## <a name="when-to-use-a-read-replica"></a>Kdy použít repliky pro čtení
Funkce repliky pro čtení pomáhá zlepšit výkon a škálování úlohy náročné na čtení. Úlohy pro čtení můžou být izolované do replik, při zápisu úlohy mohou být přesměrováni na hlavní server.

Běžný scénář, kdy je, aby BI a analytických úloh pomocí repliky pro čtení jako zdroj dat pro generování sestav.

Vzhledem k tomu repliky jen pro čtení, není zkracují přímo zápisu kapacity zatížení hlavní server. Tato funkce není určenou pro úlohy náročné na zápis.

Funkce repliky pro čtení používá asynchronní replikace PostgreSQL. Tato funkce není určena pro scénáře synchronní replikace. Bude docházet k prodlevám měřitelné mezi hlavního serveru a repliky. Data v replice nakonec bude konzistentní s daty na hlavní server. Pomocí této funkce pro úlohy, které můžou vyhovovat tomuto zpoždění dochází.

## <a name="create-a-replica"></a>Vytvoření repliky
Hlavní server musí mít `azure.replication_support` parametr nastaven na **REPLIKY**. Pokud tento parametr změníte, je nutné tato změna se projeví restartovat server. ( `azure.replication_support` Parametr platí pro obecné účely a optimalizovaný pro paměť úrovně pouze).

Při spuštění pracovního postupu vytvoření repliky prázdné serveru Azure Database for PostgreSQL se vytvoří. Nový server je vyplněna data, která byla na hlavní server. Čas vytvoření závisí na množství dat na hlavní server a doba od poslední týdenní úplnou zálohu. Čas musí být v rozsahu několika minut i několik hodin.

Funkce repliky pro čtení používá replikaci fyzických PostgreSQL, není logický replikace. Streamování s použitím replikace sloty replikace je výchozí režim operace. V případě potřeby se dohnat používá přesouvání protokolu.

> [!NOTE]
> Pokud nemáte sadu výstrah úložiště na serverech, doporučujeme vám to. Upozornění vás informuje, když na serveru se blíží limitu úložiště, který bude mít vliv na replikaci.

Zjistěte, jak [vytvoření repliky pro čtení na webu Azure Portal](howto-read-replicas-portal.md).

## <a name="connect-to-a-replica"></a>Připojení na repliku
Při vytváření repliky nedědí pravidla brány firewall nebo koncový bod služby virtuální sítě hlavního serveru. Tato pravidla musí být nezávisle nastavené pro repliku.

Replika dědí z hlavního serveru účet správce. Všechny uživatelské účty na hlavním serveru se replikují do repliky pro čtení. Pouze pro čtení repliky můžete připojit pomocí uživatelské účty, které jsou dostupné na hlavním serveru.

Můžete připojit k replice pomocí jeho názvu hostitele a platný uživatelský účet, jako byste to zvládli pravidelné serveru Azure Database for PostgreSQL. Pro server s názvem **myreplica** s uživatelským jménem správce **myadmin**, můžete se připojíte k replice pomocí nástroje psql:

```
psql -h myreplica.postgres.database.azure.com -U myadmin@myreplica -d postgres
```

Do příkazového řádku zadejte heslo pro uživatelský účet.

## <a name="monitor-replication"></a>Monitorování replikace
Azure Database for PostgreSQL nabízí **maximální prodleva mezi repliky** metriky ve službě Azure Monitor. Tato metrika je k dispozici na hlavní server. Metrika ukazuje je zpoždění v bajtech mezi hlavní a většina obložení repliky. 

Azure Database for PostgreSQL poskytuje také **repliky prodleva** metriky ve službě Azure Monitor. Tato metrika je k dispozici pro pouze repliky. 

Metrika se počítá od `pg_stat_wal_receiver` zobrazení:

```SQL
EXTRACT (EPOCH FROM now() - pg_last_xact_replay_timestamp())
```

Prodleva repliky metrika zobrazuje doba od poslední přehraná transakce. Pokud neexistují žádné transakce, ke kterým dochází na hlavní server, metriku odráží tento časový interval.

Nastavení upozornění pro informování, když replika prodleva dosáhne hodnotu, která se nedají použít pro vaši úlohu. 

Další informace zadat dotaz na hlavní server přímo k získání replikace prodlevy v bajtů u všech replik.

V PostgreSQL verze 10:

```SQL
select pg_wal_lsn_diff(pg_current_wal_lsn(), stat.replay_lsn) 
AS total_log_delay_in_bytes from pg_stat_replication;
```

V PostgreSQL 9,6 a starší verze:

```SQL
select pg_xlog_location_diff(pg_current_xlog_location(), stat.replay_location) 
AS total_log_delay_in_bytes from pg_stat_replication;
```

> [!NOTE]
> Pokud se hlavní server nebo další repliky restartuje, čas potřebný k restartování a projděte si se projeví v metrika prodleva repliky.

## <a name="stop-replication"></a>Zastavit replikaci
Můžete zastavit replikaci mezi hlavní a repliku. Akce zastavení způsobí, že repliky restartovat a odebrat jeho nastavení replikace. Po zastavení replikace mezi hlavní server a repliky pro čtení, se stane replika samostatný server. Data v samostatném serveru jsou data, která byla k dispozici na replice v době, kdy byl spuštěn příkaz stop replikace. Samostatný server nebude dohnat s hlavním serverem.

> [!IMPORTANT]
> Samostatný server nelze je převést na repliku znovu.
> Před zastavením replikace na čtení replik, zajistěte, aby že replika bude mít veškerá data, které požadujete.

Zjistěte, jak [zastavit replikaci replik](howto-read-replicas-portal.md).


## <a name="considerations"></a>Požadavky

Tento oddíl shrnuje důležité informace o funkci repliky pro čtení.

### <a name="prerequisites"></a>Požadavky
Než vytvoříte další repliky `azure.replication_support` parametr musí být nastaven na **REPLIKY** na hlavní server. Pokud tento parametr změníte, je nutné tato změna se projeví restartovat server. `azure.replication_support` Parametr platí pro obecné účely a optimalizovaný pro paměť úrovně pouze.

### <a name="new-replicas"></a>Nové repliky
Čtení replika se vytváří jako nový server Azure Database for PostgreSQL. Existující server nelze nastavit do repliky. Číst repliku lze vytvořit pouze ve stejné oblasti Azure jako hlavní server. Nejde vytvořit replika jiné repliky pro čtení.

### <a name="replica-configuration"></a>Konfigurace repliky
Replika je vytvořen pomocí stejné konfigurace serveru na hlavní server. Po vytvoření repliky několik nastavení lze změnit nezávisle z hlavního serveru: výpočetní generace, virtuální jádra, úložiště a období uchovávání záloh. Cenovou úroveň můžete změnit také nezávisle na sobě, s výjimkou do nebo z úrovně Basic.

> [!IMPORTANT]
> Před hlavním serverem služby konfigurace se aktualizuje na nové hodnoty, aktualizujte konfiguraci repliky na stejné nebo vyšší hodnoty. Tato akce zajistí, že replika je dokáže dodat se změnami provedenými na hlavní server.

PostgreSQL vyžaduje hodnotu `max_connections` parametr čtení repliky být větší než nebo rovna hodnotě hlavní; v opačném případě repliky nelze spustit. Ve službě Azure Database for PostgreSQL `max_connections` hodnota parametru je založená na SKU. Další informace najdete v tématu [omezení ve službě Azure Database for PostgreSQL](concepts-limits.md). 

Pokud při pokusu o aktualizaci hodnot serveru, ale nedrží omezení, zobrazí se chybová zpráva.

### <a name="stopped-replicas"></a>Zastavené repliky
Chcete-li zrušit replikace mezi serverem a hlavním serverem repliky pro čtení replik restartuje na použití změny. Zastavené replika přestane být samostatný server, který přijímá operace čtení i zápisu. Samostatný server nelze je převést na repliku znovu.

### <a name="deleted-master-and-standalone-servers"></a>Odstraněné hlavní větev a samostatné servery
Při odstranění je hlavní server, všechny jeho čtení repliky se stanou samostatné servery. Repliky se restartují tak, aby odrážela tuto změnu.

## <a name="next-steps"></a>Další postup
Zjistěte, jak [vytvářet a spravovat další repliky na portálu Azure portal](howto-read-replicas-portal.md).
