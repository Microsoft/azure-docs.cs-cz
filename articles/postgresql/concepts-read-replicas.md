---
title: Repliky pro čtení ve službě Azure Database for PostgreSQL – jeden Server
description: Tento článek popisuje funkce repliky pro čtení ve službě Azure Database for PostgreSQL – jeden Server.
author: rachel-msft
ms.author: raagyema
ms.service: postgresql
ms.topic: conceptual
ms.date: 06/05/2019
ms.openlocfilehash: 75a3c8a9912fe9ace70e411983996167da755128
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/13/2019
ms.locfileid: "66734653"
---
# <a name="read-replicas-in-azure-database-for-postgresql---single-server"></a>Repliky pro čtení ve službě Azure Database for PostgreSQL – jeden Server

Funkce repliky pro čtení umožňuje replikaci dat ze serveru Azure Database for PostgreSQL na serveru jen pro čtení. Můžete replikovat z hlavního serveru až pěti replikami. Repliky jsou aktualizované asynchronně technologie replikace nativní modul PostgreSQL.

> [!IMPORTANT]
> Čtení repliky můžete vytvořit ve stejné oblasti jako váš hlavní server, nebo v libovolné jiné oblasti Azure podle vašeho výběru. Replikace mezi oblastmi je aktuálně ve verzi public preview.

Repliky jsou nové servery, které spravujete podobně jako na běžnou – Azure Database for PostgreSQL servery. Pro každé čtení replik, bude se vám účtovat za zřízených výpočetních jádrech a úložiště v GB / měsíc.

Zjistěte, jak [vytvářet a spravovat repliky](howto-read-replicas-portal.md).

## <a name="when-to-use-a-read-replica"></a>Kdy použít repliky pro čtení
Funkce repliky pro čtení pomáhá zlepšit výkon a škálování úlohy náročné na čtení. Úlohy pro čtení můžou být izolované do replik, při zápisu úlohy mohou být přesměrováni na hlavní server.

Běžný scénář, kdy je, aby BI a analytických úloh pomocí repliky pro čtení jako zdroj dat pro generování sestav.

Vzhledem k tomu repliky jen pro čtení, není zkracují přímo zápisu kapacity zatížení hlavní server. Tato funkce není určenou pro úlohy náročné na zápis.

Funkce repliky pro čtení používá asynchronní replikace PostgreSQL. Tato funkce není určena pro scénáře synchronní replikace. Bude docházet k prodlevám měřitelné mezi hlavního serveru a repliky. Data v replice nakonec bude konzistentní s daty na hlavní server. Pomocí této funkce pro úlohy, které můžou vyhovovat tomuto zpoždění dochází.

Repliky pro čtení můžete vylepšit vašeho plánu zotavení po havárii. Nejprve musíte mít v jiné oblasti Azure z hlavního serveru repliky. Pokud nedojde k havárii oblasti, můžete zastavit replikaci touto replikou a přesměrovat úlohy na ni. Zastavuje se replikace umožňuje repliky začít přijímat zápisy, jakož i čtení. Další informace najdete v [zastavení replikace](#stop-replication) oddílu. 

## <a name="create-a-replica"></a>Vytvoření repliky
Hlavní server musí mít `azure.replication_support` parametr nastaven na **REPLIKY**. Pokud tento parametr změníte, je nutné tato změna se projeví restartovat server. ( `azure.replication_support` Parametr platí pro obecné účely a optimalizovaný pro paměť úrovně pouze).

Při spuštění pracovního postupu vytvoření repliky prázdné serveru Azure Database for PostgreSQL se vytvoří. Nový server je vyplněna data, která byla na hlavní server. Čas vytvoření závisí na množství dat na hlavní server a doba od poslední týdenní úplnou zálohu. Čas musí být v rozsahu několika minut i několik hodin.

Repliky je povolený pro úložiště [auto-grow](concepts-pricing-tiers.md#storage-auto-grow). Funkce auto-grow umožňuje repliky držet krok s dat replikovaných do ní a zabránili přerušení v replikaci nedostatku chyby úložiště.

Funkce repliky pro čtení používá replikaci fyzických PostgreSQL, není logický replikace. Streamování s použitím replikace sloty replikace je výchozí režim operace. V případě potřeby se dohnat používá přesouvání protokolu.

Zjistěte, jak [vytvoření repliky pro čtení na webu Azure Portal](howto-read-replicas-portal.md).

## <a name="connect-to-a-replica"></a>Připojení na repliku
Při vytváření repliky nedědí pravidla brány firewall nebo koncový bod služby virtuální sítě hlavního serveru. Tato pravidla musí být nezávisle nastavené pro repliku.

Replika dědí z hlavního serveru účet správce. Všechny uživatelské účty na hlavním serveru se replikují do repliky pro čtení. Pouze pro čtení repliky můžete připojit pomocí uživatelské účty, které jsou dostupné na hlavním serveru.

Můžete připojit k replice pomocí jeho názvu hostitele a platný uživatelský účet, jako byste to zvládli pravidelné serveru Azure Database for PostgreSQL. Pro server s názvem **repliku** s uživatelským jménem správce **myadmin**, můžete se připojíte k replice pomocí nástroje psql:

```
psql -h myreplica.postgres.database.azure.com -U myadmin@myreplica -d postgres
```

Do příkazového řádku zadejte heslo pro uživatelský účet.

## <a name="monitor-replication"></a>Monitorování replikace
Azure Database for PostgreSQL poskytuje dvě metriky pro monitorování replikace. Jsou dvě metriky **maximální prodleva mezi repliky** a **repliky prodleva**. Zjistěte, jak zobrazit tyto metriky, najdete v článku **monitorovat repliku** část [přečíst článek repliky](howto-read-replicas-portal.md).

**Maximální prodleva mezi repliky** metrika zobrazuje je zpoždění v bajtech mezi hlavní a většina obložení repliky. Tato metrika je k dispozici na hlavní server.

**Repliky prodleva** metrika zobrazuje čas, od posledního přehrály transakce. Pokud neexistují žádné transakce, ke kterým dochází na hlavní server, metriku odráží tento časový interval. Tato metrika je k dispozici pouze servery repliky. Prodleva repliky se počítá od `pg_stat_wal_receiver` zobrazení:

```SQL
EXTRACT (EPOCH FROM now() - pg_last_xact_replay_timestamp());
```

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

## <a name="stop-replication"></a>Zastavení replikace
Můžete zastavit replikaci mezi hlavní a repliku. Akce zastavení způsobí, že repliky restartovat a odebrat jeho nastavení replikace. Po zastavení replikace mezi hlavní server a repliky pro čtení, se stane replika samostatný server. Data v samostatném serveru jsou data, která byla k dispozici na replice v době, kdy byl spuštěn příkaz stop replikace. Samostatný server nebude dohnat s hlavním serverem.

> [!IMPORTANT]
> Samostatný server nelze je převést na repliku znovu.
> Před zastavením replikace na čtení replik, zajistěte, aby že replika bude mít veškerá data, které požadujete.

Při zastavení replikace replika ztratí všechny odkazy na jeho předchozí hlavní větev a ostatními replikami. Není k dispozici žádné automatické převzetí služeb při selhání mezi hlavní a repliky. 

Zjistěte, jak [zastavit replikaci replik](howto-read-replicas-portal.md).


## <a name="considerations"></a>Požadavky

Tento oddíl shrnuje důležité informace o funkci repliky pro čtení.

### <a name="prerequisites"></a>Požadavky
Než vytvoříte další repliky `azure.replication_support` parametr musí být nastaven na **REPLIKY** na hlavní server. Pokud tento parametr změníte, je nutné tato změna se projeví restartovat server. `azure.replication_support` Parametr platí pro obecné účely a optimalizovaný pro paměť úrovně pouze.

### <a name="new-replicas"></a>Nové repliky
Čtení replika se vytváří jako nový server Azure Database for PostgreSQL. Existující server nelze nastavit do repliky. Nejde vytvořit replika jiné repliky pro čtení.

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
