---
title: Čtení replik – Azure Database for PostgreSQL – jeden server
description: Tento článek popisuje funkci pro čtení repliky na serveru Azure Database for PostgreSQL-Single.
author: rachel-msft
ms.author: raagyema
ms.service: postgresql
ms.topic: conceptual
ms.date: 01/23/2020
ms.openlocfilehash: 545d04bdede76a6ce25c9e4665f39c01ff6caa73
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/28/2020
ms.locfileid: "81531979"
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

Hlavní server můžete mít v libovolné [Azure Database for PostgreSQL oblasti](https://azure.microsoft.com/global-infrastructure/services/?products=postgresql). Hlavní server může mít repliku ve své spárované oblasti nebo oblastech univerzální repliky. Následující obrázek ukazuje, které oblasti repliky jsou k dispozici v závislosti na vaší hlavní oblasti.

[![Čtení oblastí repliky](media/concepts-read-replica/read-replica-regions.png)](media/concepts-read-replica/read-replica-regions.png#lightbox)

### <a name="universal-replica-regions"></a>Oblasti univerzální repliky
Repliku pro čtení můžete vždy vytvořit v některé z následujících oblastí bez ohledu na to, kde se nachází váš hlavní server. Jedná se o oblasti univerzální repliky:

Austrálie – východ, Austrálie – jihovýchod, Střed USA, Východní Asie, Východní USA, Východní USA 2, Japonsko – východ, Japonsko – západ, Korea – jih, střed, střed USA – sever, Severní Evropa, střed USA – jih, jihovýchodní Asie, Velká Británie – jih, Velká Británie – západ, Západní Evropa, západní USA.

* Západní USA 2 není dočasně k dispozici jako umístění repliky mezi oblastmi.


### <a name="paired-regions"></a>Spárované oblasti
Kromě oblastí univerzální repliky můžete vytvořit repliku pro čtení ve spárované oblasti Azure vašeho hlavního serveru. Pokud neznáte pár vaší oblasti, můžete získat další informace v [článku spárované oblasti Azure](../best-practices-availability-paired-regions.md).

Pokud používáte repliky mezi jednotlivými oblastmi pro plánování zotavení po havárii, doporučujeme vytvořit repliku v spárované oblasti namísto jedné z ostatních oblastí. Spárované oblasti zabraňují souběžným aktualizacím a přiřazují fyzickou izolaci a zasídlí dat.  

Je potřeba vzít v úvahu omezení: 

* Regionální dostupnost: Azure Database for PostgreSQL je k dispozici v Západní USA 2, Francii Central, Spojené arabské emiráty Severní a Německo – střed. Nicméně jejich spárované oblasti nejsou k dispozici.
    
* Jednosměrné páry: některé oblasti Azure jsou spárovány pouze v jednom směru. Mezi tyto oblasti patří Západní Indie, Brazílie – jih. 
   To znamená, že hlavní server v Západní Indie může vytvořit repliku v Jižní Indie. Hlavní server v Jižní Indie ale nemůže vytvořit repliku v Západní Indie. Důvodem je to, že sekundární oblast Západní Indie je Jižní Indie, ale sekundární oblast Jižní Indie není Západní Indie.


## <a name="create-a-replica"></a>Vytvoření repliky
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
select pg_wal_lsn_diff(pg_current_wal_lsn(), replay_lsn) 
AS total_log_delay_in_bytes from pg_stat_replication;
```

V PostgreSQL verze 9,6 a novější:

```SQL
select pg_xlog_location_diff(pg_current_xlog_location(), replay_location) 
AS total_log_delay_in_bytes from pg_stat_replication;
```

> [!NOTE]
> Pokud dojde k restartování hlavního serveru nebo čtení repliky, čas potřebný k restartování a zachytávání se projeví ve metrikě prodlevy repliky.

## <a name="stop-replication"></a>Zastavení replikace
Replikaci mezi hlavní a replikou můžete zastavit. Akce zastavit způsobí, že se replika restartuje a odebere se její nastavení replikace. Po zastavení replikace mezi hlavním serverem a replikou pro čtení se replika samostatného serveru. Data na samostatném serveru jsou data, která byla v replice k dispozici v době spuštění příkazu pro zastavení replikace. Samostatný server není zachytávání s hlavním serverem.

> [!IMPORTANT]
> Samostatný server se nedá znovu vytvořit do repliky.
> Před zastavením replikace v replice pro čtení zajistěte, aby měla replika všechna data, která požadujete.

Při zastavení replikace ztratí replika všechny odkazy na předchozí hlavní a jiné repliky.

Přečtěte si, jak [zastavit replikaci do repliky](howto-read-replicas-portal.md).

## <a name="failover"></a>Převzetí služeb při selhání
Mezi hlavním serverem a serverem repliky neexistuje automatizované převzetí služeb při selhání. 

Vzhledem k tomu, že replikace je asynchronní, existuje prodleva mezi hlavním serverem a replikou. Velikost prodlevy může mít vliv na několik faktorů, jako je to, jak těžké zatížení na hlavním serveru jsou a latence mezi datovými centry. Ve většině případů se prodlevy replikují mezi několik sekund až na několik minut. Vlastní prodlevu replikace můžete sledovat pomocí *prodlevy repliky*metriky, která je k dispozici pro každou repliku. Tato metrika ukazuje čas od poslední opakované transakce. Doporučujeme, abyste zjistili, jaký je průměrný prodleva tím, že v časovém intervalu pozoruje prodlevu repliky. Můžete nastavit upozornění na prodlevu repliky, takže pokud bude mimo očekávaný rozsah, můžete provést akci.

> [!Tip]
> Pokud dojde k převzetí služeb při selhání repliky, prodleva v době odpojování repliky z hlavní větve indikuje, kolik dat se ztratilo.

Jakmile se rozhodnete, že chcete převzít služeb při selhání do repliky, 

1. Zastavení replikace do repliky<br/>
   Tento krok je nezbytný k tomu, aby server repliky mohl přijímat zápisy. V rámci tohoto procesu se server repliky restartuje a odpojí se od hlavního serveru. Jakmile zahájíte zastavení replikace, proces back-endu obvykle trvá přibližně 2 minuty, než se dokončí. V části [zastavení replikace](#stop-replication) v tomto článku se seznámíte s důsledky této akce.
    
2. Nasměrujte aplikaci na (bývalé) repliku.<br/>
   Každý server má jedinečný připojovací řetězec. Aktualizujte svou aplikaci tak, aby odkazovala na (bývalé) repliku místo na hlavní.
    
Po úspěšném zpracování čtení a zápisu vaší aplikace jste dokončili převzetí služeb při selhání. Množství prostojů, na kterých bude prostředí aplikace záviset při zjištění problému a dokončení kroků 1 a 2 výše.


## <a name="considerations"></a>Požadavky

V této části najdete přehled informací o funkci Replika čtení.

### <a name="prerequisites"></a>Požadavky
Před vytvořením repliky pro čtení musí `azure.replication_support` být parametr nastaven na **repliku** na hlavním serveru. Pokud se tento parametr změní, je nutné restartovat server, aby se změna projevila. `azure.replication_support` Parametr se vztahuje pouze na pro obecné účely a paměťově optimalizované úrovně.

### <a name="new-replicas"></a>Nové repliky
Replika pro čtení je vytvořená jako nový server Azure Database for PostgreSQL. Existující server nelze vytvořit do repliky. Nelze vytvořit repliku jiné repliky pro čtení.

### <a name="replica-configuration"></a>Konfigurace repliky
Replika se vytvoří pomocí stejného nastavení výpočtů a úložiště jako hlavní. Po vytvoření repliky se dá několik nastavení měnit nezávisle na hlavním serveru: generování výpočetních prostředků, virtuální jádra, úložiště a doba uchovávání záloh. Cenová úroveň se dá změnit také nezávisle, s výjimkou nebo z úrovně Basic.

> [!IMPORTANT]
> Před aktualizací hlavního nastavení na novou hodnotu aktualizujte konfiguraci repliky na hodnotu rovná se nebo větší. Tato akce zajistí, že replika bude moct udržovat krok se všemi změnami na hlavním serveru.

PostgreSQL vyžaduje, aby hodnota `max_connections` parametru v replice pro čtení byla větší než nebo rovna hlavní hodnotě; v opačném případě se replika nespustí. V Azure Database for PostgreSQL je hodnota `max_connections` parametru založena na SKU. Další informace najdete v tématu [omezení v Azure Database for PostgreSQL](concepts-limits.md). 

Pokud se pokusíte aktualizovat výše popsané hodnoty serveru, ale nedodržují omezení, dojde k chybě.

Pravidla brány firewall, pravidla virtuální sítě a nastavení parametrů nejsou děděna z hlavního serveru do repliky, když je replika vytvořena nebo následně.

### <a name="max_prepared_transactions"></a>max_prepared_transactions
[PostgreSQL vyžaduje](https://www.postgresql.org/docs/current/runtime-config-resource.html#GUC-MAX-PREPARED-TRANSACTIONS) , aby hodnota `max_prepared_transactions` parametru v replice pro čtení byla větší než nebo rovna hlavní hodnotě; v opačném případě se replika nespustí. Pokud chcete změnit `max_prepared_transactions` hlavní server, nejdřív ho změňte na replikách.

### <a name="stopped-replicas"></a>Zastavené repliky
Pokud zastavíte replikaci mezi hlavním serverem a replikou pro čtení, bude se replika znovu používat pro změnu. Zastavená replika se stal samostatným serverem, který přijímá čtení i zápis. Samostatný server se nedá znovu vytvořit do repliky.

### <a name="deleted-master-and-standalone-servers"></a>Odstraněné hlavní a samostatné servery
Po odstranění hlavního serveru se všechny jeho repliky pro čtení stanou samostatnými servery. Repliky se restartují, aby se tato změna projevila.

## <a name="next-steps"></a>Další kroky
* Naučte se [vytvářet a spravovat repliky pro čtení v Azure Portal](howto-read-replicas-portal.md).
* Naučte se [vytvářet a spravovat repliky pro čtení v Azure CLI a REST API](howto-read-replicas-cli.md).
