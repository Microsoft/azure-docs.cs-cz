---
title: Repliky pro čtení v Azure Database for PostgreSQL
description: Tento článek popisuje další repliky ve službě Azure Database for PostgreSQL.
author: rachel-msft
ms.author: raagyema
ms.service: postgresql
ms.topic: conceptual
ms.date: 01/23/2019
ms.openlocfilehash: 017abd72b3aa1547844f977c3efda69e59901a6f
ms.sourcegitcommit: 5978d82c619762ac05b19668379a37a40ba5755b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/31/2019
ms.locfileid: "55487944"
---
# <a name="read-replicas-in-azure-database-for-postgresql"></a>Repliky pro čtení ve službě Azure Database for PostgreSQL

> [!IMPORTANT]
> Funkce repliky pro čtení je ve verzi Public Preview.

Funkce repliky pro čtení umožňuje replikaci dat ze serveru Azure Database for PostgreSQL server (správce) až k pěti jen pro čtení serverům (replik pro čtení) v rámci stejné oblasti Azure. Repliky pro čtení se aktualizují asynchronně pomocí technologie nativní replikace stroje PostgreSQL.

Repliky jsou nové servery, které je možné spravovat podobným způsobem jako normální samostatné databáze Azure pro servery PostgreSQL. Za každou repliku pro čtení se vám účtuje zřízený výpočetní výkon ve virtuálních jádrech a zřízené úložiště v GB za měsíc.

## <a name="when-to-use-read-replicas"></a>Kdy použít repliky pro čtení
Funkce repliky pro čtení je cílená na pomáhá zlepšit výkon a škálování úlohy náročné na čtení. Další úlohy, může být izolovaných replik, při zápisu úlohy mohou být přesměrováni na hlavní server.

Běžný scénář, kdy je, aby BI a analytických úloh pomocí repliky pro čtení jako zdroj dat pro generování sestav.

Protože repliky jsou jen pro čtení, jejich není zmírnění přímo zápisu kapacity zatížení hlavní a proto není tato funkce určenou pro úlohy náročné na zápis.

Funkce repliky pro čtení používá asynchronní replikace PostgreSQL a proto není určena pro scénáře synchronní replikace. Bude docházet k prodlevám měřitelné mezi hlavního serveru a repliky. Změní data v replice konzistentní s daty na hlavní server. Pomocí této funkce pro úlohy, které můžou vyhovovat tomuto zpoždění dochází.

## <a name="creating-a-replica"></a>Vytvoření repliky
Hlavní server musí mít **azure.replication_support** nastavit do REPLIKY. Změna tento parametr se vyžaduje restartování serveru se projeví. (**Azure.replication_support** parametr platí jenom pro úrovně obecné účely a optimalizované pro paměť).

Při zahájení pracovního postupu vytvoření repliky prázdné serveru Azure Database for PostgreSQL se vytvoří. Nový server je vyplněna data, která byla na hlavní server. Čas potřebný k vytvoření nové repliky závisí na množství dat na hlavní server a doba od poslední týdenní úplnou zálohu. To může v rozsahu od několik minut až několik hodin.

Funkce repliky pro čtení používá replikaci fyzických PostgreSQL (ne logické replikace). Streamování replikace používání slotů replikace je výchozí režim operace. Pokud je to nezbytné, přesouvání protokolu se používá pro zachytávání.

> [!NOTE]
> Pokud již nemáte sadu výstrah úložiště na serverech, které doporučujeme, abyste udělali tak informovat vás, když se na serveru se blíží limitu úložiště, protože to bude mít vliv na replikaci.

[Zjistěte, jak vytvořit další repliky na portálu Azure portal](howto-read-replicas-portal.md).

## <a name="connecting-to-a-replica"></a>Připojení na repliku
Při vytváření repliky nedědí pravidla brány firewall nebo koncový bod služby virtuální sítě hlavního serveru. Tato pravidla musí být nezávisle nastavené pro repliku.

Replika dědí její účet správce z hlavního serveru. Všechny uživatelské účty na hlavním serveru se replikují do repliky pro čtení. Můžete připojit jenom pro čtení replikou s použitím uživatelské účty, které jsou dostupné na hlavním serveru.

Pro repliku pomocí jeho názvu hostitele a platný uživatelský účet, se můžete připojit, jako byste to zvládli pravidelné serveru Azure Database for PostgreSQL. Například pokud název serveru myreplica a uživatelské jméno správce myadmin můžete připojit k němu z psql následujícím způsobem:

```
psql -h myreplica.postgres.database.azure.com -U myadmin@myreplica -d postgres
```
a při zobrazení výzvy zadejte heslo pro uživatelský účet.

## <a name="monitoring-replication"></a>Sledování replikace
Je **maximální prodleva mezi repliky** metriky, které jsou k dispozici ve službě Azure Monitor. Tato metrika je k dispozici na hlavní server. Metrika ukazuje prodleva mezi hlavní a většina obložení repliky. 

Poskytujeme také **repliky prodleva** metriky ve službě Azure Monitor. Tato metrika je k dispozici pro pouze repliky. 

Metrika se počítá od pg_stat_wal_receiver zobrazení:

```SQL
EXTRACT (EPOCH FROM now() - pg_last_xact_replay_timestamp())
```
Mějte na paměti, že repliky prodleva metrika zobrazuje čas, od posledního přehrály transakce. Pokud neexistují žádné transakce, ke kterým dochází v hlavní, metriku odráží tento časový interval.

Doporučujeme nastavit výstrahu, která informuje o tom, kdy prodleva repliky dosáhne hodnotu, která se nedají použít u svých úloh. 

Další informace zadat dotaz na hlavní server přímo k získání replikace prodlevy v bajtů u všech replik.
Na PG 10:
```SQL
select pg_wal_lsn_diff(pg_current_wal_lsn(), stat.replay_lsn) 
AS total_log_delay_in_bytes from pg_stat_replication;
```

Na PG 9.6 a níže:
```SQL
select pg_xlog_location_diff(pg_current_xlog_location(), stat.replay_location) 
AS total_log_delay_in_bytes from pg_stat_replication;
```

> [!NOTE]
> Pokud restartování serveru hlavní server nebo replik, čas potřebný k restartujte a pak dohnat se projeví ve metrika prodleva repliky.

## <a name="stopping-replication-to-a-replica"></a>Zastavuje se replikace na repliku
Můžete se na zastavení replikace mezi hlavní a repliku. To způsobí, že repliky restartovat, aby mohla odeberte nastavení replikace. Po zastavení replikace mezi hlavní a server repliky bude server repliky samostatný server. Data v samostatném serveru jsou data, která byla k dispozici na replice v době, kdy byl spuštěn příkaz stop replikace. Tento samostatný server nebude zachytávat s hlavním serverem.

Tento server nelze je převést na repliku znovu.

Zajistěte, aby replika všechna data, která vyžadují před zastavení replikace.

Je možné [zjistěte, jak zastavit repliky v kompletní dokumentace postupů](howto-read-replicas-portal.md).


## <a name="considerations"></a>Požadavky

### <a name="preparing-for-replica"></a>Příprava pro repliku
**Azure.replication_support** musí být nastavena na REPLIKU na hlavním serveru, než budete moct vytvořit repliky. Změna tento parametr se vyžaduje restartování serveru se projeví. Tento parametr platí jenom pro úrovně obecné účely a optimalizované pro paměť.

### <a name="stopped-replicas"></a>Zastavené repliky
Pokud budete chtít zastavit replikaci mezi hlavní a replika, replika se restartuje, aby tuto změnu použít. Repliky se pak stane serveru pro čtení i zápis. Potom se ji nelze nastavit do repliky znovu.

### <a name="replicas-are-new-servers"></a>Repliky jsou nové servery
Repliky jsou vytvořeny jako nové – Azure Database for postgresql – servery. Nelze je převést existující servery repliky.

### <a name="replica-server-configuration"></a>Konfigurace serveru repliky
Servery repliky jsou vytvořené pomocí stejné konfigurace serveru na hlavní server, který obsahuje následující konfigurace:
- Cenová úroveň
- Generace výpočetních funkcí
- Virtuální jádra
- Storage
- Období uchování zálohy
- Možnosti redundance zálohy
- Verze stroje PostgreSQL

Poté, co byla vytvořena replika, cenová úroveň (s výjimkou do a z Basic), výpočetní generaci, virtuální jádra, úložiště a období uchování zálohy lze změnit nezávisle z hlavního serveru.

> [!IMPORTANT]
> Předtím, než se konfigurace serveru hlavní server je aktualizovat na nové hodnoty, tyto repliky konfigurace se musí aktualizovat na stejné nebo vyšší hodnoty. Tím se zajistí, že tyto repliky jsou schopné udržovat tempo se změnami provedenými na hlavní server.

Zejména Postgres vyžaduje hodnotu server repliky pro parametr max_connections být větší než nebo rovna hodnotě předlohy v opačném případě, že replika nebude spuštěna. Ve službě Azure Database for PostgreSQL je nastavena hodnota max_connections v závislosti na tom, které skladová jednotka potřebuje. Další informace najdete v článku [dokumentu limity](concepts-limits.md). 

Pokus aktualizaci, která je v rozporu to povede k chybě.


### <a name="deleting-the-master"></a>Odstraňuje se hlavní
Při odstranění hlavního serveru stanou jeho čtení replik samostatné servery. Repliky se restartuje, aby obsahovala tuto změnu.

### <a name="other"></a>Ostatní
- Repliky pro čtení lze vytvořit pouze ve stejné oblasti Azure jako hlavní server.
- Vytváří se replika repliky se nepodporuje.

## <a name="next-steps"></a>Další postup
- [Jak vytvořit a spravovat čtení replik na webu Azure Portal](howto-read-replicas-portal.md).
