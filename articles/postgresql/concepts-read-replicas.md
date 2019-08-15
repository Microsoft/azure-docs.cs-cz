---
title: Čtení replik v Azure Database for PostgreSQL – jeden server
description: Tento článek popisuje funkci pro čtení repliky na serveru Azure Database for PostgreSQL-Single.
author: rachel-msft
ms.author: raagyema
ms.service: postgresql
ms.topic: conceptual
ms.date: 08/12/2019
ms.openlocfilehash: 928a85c9d03148198fe3e965636740812ce732f7
ms.sourcegitcommit: 62bd5acd62418518d5991b73a16dca61d7430634
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/13/2019
ms.locfileid: "68976290"
---
# <a name="read-replicas-in-azure-database-for-postgresql---single-server"></a>Čtení replik v Azure Database for PostgreSQL – jeden server

Funkce replika čtení umožňuje replikovat data z Azure Database for PostgreSQL serveru do serveru jen pro čtení. Z hlavního serveru je můžete replikovat až na pět replik. Repliky se asynchronně aktualizují pomocí technologie nativní replikace PostgreSQL Engine.

Repliky jsou nové servery, které spravujete podobně jako běžné Azure Database for PostgreSQL servery. Pro každou repliku čtení se vám bude účtovat zajištěné výpočetní prostředky v virtuální jádra a úložišti v GB/měsíc.

Naučte se [vytvářet a spravovat repliky](howto-read-replicas-portal.md).

## <a name="when-to-use-a-read-replica"></a>Kdy použít repliku čtení
Funkce replika čtení pomáhá zlepšit výkon a škálu úloh náročných na čtení. Úlohy čtení se dají pro repliky izolovat, zatímco úlohy zápisu můžou být směrované do hlavní větve.

Běžným scénářem je, aby úlohy BI a analýzy používaly jako zdroj dat pro vytváření sestav repliku pro čtení.

Vzhledem k tomu, že repliky jsou jen pro čtení, nesnižují přímo na hlavní úrovni zátěže s kapacitou pro zápis. Tato funkce není zaměřená na úlohy náročné na zápis.

Funkce replika čtení používá asynchronní replikaci PostgreSQL. Tato funkce není určena pro scénáře synchronní replikace. Mezi hlavním serverem a replikou bude měřitelné zpoždění. Data v replice nakonec budou konzistentní s daty v hlavní databázi. Tato funkce se používá pro úlohy, které můžou toto zpoždění obsloužit.

## <a name="cross-region-replication"></a>Replikace mezi oblastmi
Z hlavního serveru můžete vytvořit repliku pro čtení v jiné oblasti. Replikace mezi oblastmi může být užitečná pro scénáře, jako je plánování zotavení po havárii, nebo pro uživatele přiblížit data.

> [!IMPORTANT]
> Replikace mezi oblastmi je aktuálně ve verzi Public Preview.

Hlavní server můžete mít v libovolné [Azure Database for PostgreSQL oblasti](https://azure.microsoft.com/global-infrastructure/services/?products=postgresql).  Hlavní server může mít repliku ve své spárované oblasti nebo oblastech univerzální repliky.

### <a name="universal-replica-regions"></a>Oblasti univerzální repliky
Repliku pro čtení můžete vždy vytvořit v některé z následujících oblastí bez ohledu na to, kde se nachází váš hlavní server. Jedná se o oblasti univerzální repliky:

Austrálie – východ, Austrálie – jihovýchod, Střed USA, Východní Asie, Východní USA, Východní USA 2, Japonsko – východ, Japonsko – západ, Korea – jih, střed, střed USA – sever, Severní Evropa, střed USA – jih, jihovýchodní Asie, Velká Británie – jih, Velká Británie – západ, západní Evropa, Západní USA, západní USA 2.


### <a name="paired-regions"></a>Spárované oblasti
Kromě oblastí univerzální repliky můžete vytvořit repliku pro čtení ve spárované oblasti Azure vašeho hlavního serveru. Pokud neznáte pár vaší oblasti, můžete získat další informace v [článku spárované oblasti Azure](https://docs.microsoft.com/azure/best-practices-availability-paired-regions).

Pokud používáte repliky mezi jednotlivými oblastmi pro plánování zotavení po havárii, doporučujeme vytvořit repliku v spárované oblasti namísto jedné z ostatních oblastí. Spárované oblasti zabraňují souběžným aktualizacím a přiřazují fyzickou izolaci a zasídlí dat.  

Je však třeba vzít v úvahu omezení: 

* Dostupnost podle oblastí: Azure Database for PostgreSQL je k dispozici v Západní USA 2, Francii Central, Spojené arabské emiráty Severní a Německo – střed. Nicméně jejich spárované oblasti nejsou k dispozici.
    
* Jednosměrné páry: Některé oblasti Azure jsou spárovány pouze v jednom směru. Mezi tyto oblasti patří Západní Indie, Brazílie – jih a US Gov – Virginie. 
   To znamená, že hlavní server v Západní Indie může vytvořit repliku v Jižní Indie. Hlavní server v Jižní Indie ale nemůže vytvořit repliku v Západní Indie. Důvodem je to, že sekundární oblast Západní Indie je Jižní Indie, ale sekundární oblast Jižní Indie není Západní Indie.


## <a name="create-a-replica"></a>Vytvoření repliky
Hlavní server musí mít `azure.replication_support` parametr nastavený na **repliku**. Pokud se tento parametr změní, je nutné restartovat server, aby se změna projevila. `azure.replication_support` (Parametr se vztahuje pouze na pro obecné účely a paměťově optimalizované úrovně).

Když spustíte pracovní postup vytvoření repliky, vytvoří se prázdný Azure Database for PostgreSQL Server. Nový server je vyplněn daty, která byla na hlavním serveru. Čas vytvoření závisí na množství dat v hlavní databázi a na čase od posledního týdenního úplného zálohování. Čas může být v rozsahu od několika minut až po několik hodin.

U každé repliky je povoleno [Automatické zvětšování](concepts-pricing-tiers.md#storage-auto-grow)úložiště. Funkce automatického zvětšení umožňuje replice udržovat data, která jsou do ní replikována, a zabránit přerušení replikace způsobené chybou úložiště.

Funkce replika čtení používá fyzickou replikaci PostgreSQL, ne logickou replikaci. Výchozím režimem operace je replikace streamování pomocí slotů replikace. V případě potřeby se k zachytávání použije přenos protokolu.

Naučte se [vytvořit repliku pro čtení v Azure Portal](howto-read-replicas-portal.md).

## <a name="connect-to-a-replica"></a>Připojení k replice
Když vytváříte repliku, nedědí pravidla firewallu ani koncový bod služby VNet hlavního serveru. Tato pravidla musí být pro repliku nastavena nezávisle.

Replika dědí účet správce z hlavního serveru. Všechny uživatelské účty na hlavním serveru se replikují do replik pro čtení. K replice pro čtení se můžete připojit pouze pomocí uživatelských účtů, které jsou k dispozici na hlavním serveru.

K replice se můžete připojit pomocí jejího názvu hostitele a platného uživatelského účtu, stejně jako při běžném Azure Database for PostgreSQLm serveru. Pro server s názvem **Moje replika** s uživatelským jménem správce **myadmin**se můžete připojit k replice pomocí psql:

```
psql -h myreplica.postgres.database.azure.com -U myadmin@myreplica -d postgres
```

Na příkazovém řádku zadejte heslo pro uživatelský účet.

## <a name="monitor-replication"></a>Monitorování replikace
Azure Database for PostgreSQL poskytuje dvě metriky pro monitorování replikace. Tyto dvě metriky jsou **maximální prodlevou mezi replikami** a **prodlevou repliky**. Informace o tom, jak zobrazit tyto metriky, najdete v článku **monitorování repliky** v [článku věnovaném postupu čtení repliky](howto-read-replicas-portal.md).

Metrika **maximální prodlevy napříč replikami** zobrazuje zpoždění v bajtech mezi hlavními a nejvyššími zpožděními repliky. Tato metrika je k dispozici pouze na hlavním serveru.

Metrika **prodlevy repliky** zobrazuje čas od poslední opakované transakce. Pokud na vašem hlavním serveru nedochází k žádným transakcím, metrika tuto časovou prodlevu odráží. Tato metrika je k dispozici pouze pro servery repliky. Prodleva repliky je vypočítána ze `pg_stat_wal_receiver` zobrazení:

```SQL
EXTRACT (EPOCH FROM now() - pg_last_xact_replay_timestamp());
```

Nastavte výstrahu, která vás informuje, když prodleva replik dosáhne hodnoty, která není pro vaše zatížení přijatelná. 

Další informace najdete v části dotazování hlavního serveru přímo za účelem získání prodlevy replikace v bajtech na všech replikách.

V PostgreSQL verze 10:

```SQL
select pg_wal_lsn_diff(pg_current_wal_lsn(), stat.replay_lsn) 
AS total_log_delay_in_bytes from pg_stat_replication;
```

V PostgreSQL verze 9,6 a novější:

```SQL
select pg_xlog_location_diff(pg_current_xlog_location(), stat.replay_location) 
AS total_log_delay_in_bytes from pg_stat_replication;
```

> [!NOTE]
> Pokud dojde k restartování hlavního serveru nebo čtení repliky, čas potřebný k restartování a zachytávání se projeví ve metrikě prodlevy repliky.

## <a name="stop-replication"></a>Zastavit replikaci
Replikaci mezi hlavní a replikou můžete zastavit. Akce zastavit způsobí, že se replika restartuje a odebere se její nastavení replikace. Po zastavení replikace mezi hlavním serverem a replikou pro čtení se replika samostatného serveru. Data na samostatném serveru jsou data, která byla v replice k dispozici v době spuštění příkazu pro zastavení replikace. Samostatný server není zachytávání s hlavním serverem.

> [!IMPORTANT]
> Samostatný server se nedá znovu vytvořit do repliky.
> Před zastavením replikace v replice pro čtení zajistěte, aby měla replika všechna data, která požadujete.

Při zastavení replikace ztratí replika všechny odkazy na předchozí hlavní a jiné repliky. Mezi hlavním serverem a replikou neexistuje automatizované převzetí služeb při selhání. 

Přečtěte si, jak [zastavit replikaci do repliky](howto-read-replicas-portal.md).


## <a name="considerations"></a>Požadavky

V této části najdete přehled informací o funkci Replika čtení.

### <a name="prerequisites"></a>Požadavky
Před vytvořením repliky `azure.replication_support` pro čtení musí být parametr nastaven na **repliku** na hlavním serveru. Pokud se tento parametr změní, je nutné restartovat server, aby se změna projevila. `azure.replication_support` Parametr se vztahuje pouze na pro obecné účely a paměťově optimalizované úrovně.

### <a name="new-replicas"></a>Nové repliky
Replika pro čtení je vytvořená jako nový server Azure Database for PostgreSQL. Existující server nelze vytvořit do repliky. Nelze vytvořit repliku jiné repliky pro čtení.

### <a name="replica-configuration"></a>Konfigurace repliky
Replika je vytvořena pomocí stejné konfigurace serveru jako hlavní. Po vytvoření repliky se dá několik nastavení měnit nezávisle na hlavním serveru: generování výpočetních prostředků, virtuální jádra, úložiště a doba uchovávání záloh. Cenová úroveň se dá změnit také nezávisle, s výjimkou nebo z úrovně Basic.

> [!IMPORTANT]
> Než bude konfigurace hlavního serveru aktualizována na nové hodnoty, aktualizujte konfiguraci repliky na hodnotu stejné nebo větší. Tato akce zajistí, že replika bude udržovat všechny změny provedené v hlavní větvi.

PostgreSQL vyžaduje, aby hodnota `max_connections` parametru v replice pro čtení byla větší než nebo rovna hlavní hodnotě. v opačném případě se replika nespustí. V Azure Database for PostgreSQL `max_connections` je hodnota parametru založena na SKU. Další informace najdete v tématu [omezení v Azure Database for PostgreSQL](concepts-limits.md). 

Pokud se pokusíte aktualizovat hodnoty serveru, ale nedodržují limity, dojde k chybě.

### <a name="max_prepared_transactions"></a>max_prepared_transactions
[PostgreSQL vyžaduje](https://www.postgresql.org/docs/10/runtime-config-resource.html#GUC-MAX-PREPARED-TRANSACTIONS) , aby hodnota `max_prepared_transactions` parametru v replice pro čtení byla větší než nebo rovna hlavní hodnotě. v opačném případě se replika nespustí. Pokud chcete změnit `max_prepared_transactions` hlavní server, nejdřív ho změňte na replikách.

### <a name="stopped-replicas"></a>Zastavené repliky
Pokud zastavíte replikaci mezi hlavním serverem a replikou pro čtení, bude se replika znovu používat pro změnu. Zastavená replika se stal samostatným serverem, který přijímá čtení i zápis. Samostatný server se nedá znovu vytvořit do repliky.

### <a name="deleted-master-and-standalone-servers"></a>Odstraněné hlavní a samostatné servery
Po odstranění hlavního serveru se všechny jeho repliky pro čtení stanou samostatnými servery. Repliky se restartují, aby se tato změna projevila.

## <a name="next-steps"></a>Další kroky
Naučte se [vytvářet a spravovat repliky pro čtení v Azure Portal](howto-read-replicas-portal.md).
