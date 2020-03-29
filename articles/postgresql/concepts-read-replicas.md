---
title: Čtení replik – databáze Azure pro PostgreSQL – jeden server
description: Tento článek popisuje funkci repliky pro čtení v Azure Database for PostgreSQL – Single Server.
author: rachel-msft
ms.author: raagyema
ms.service: postgresql
ms.topic: conceptual
ms.date: 01/23/2020
ms.openlocfilehash: fd6d3e24adfc22d2f6ea17f09b8dea4638a054b6
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "76769047"
---
# <a name="read-replicas-in-azure-database-for-postgresql---single-server"></a>Čtení replik v databázi Azure pro PostgreSQL – jeden server

Funkce repliky pro čtení umožňuje replikovat data z databáze Azure pro postgreSQL server na server jen pro čtení. Z hlavního serveru je můžete replikovat až na pět replik. Repliky jsou aktualizovány asynchronně pomocí technologie nativní replikace modulu PostgreSQL.

Repliky jsou nové servery, které spravujete podobně jako běžné azure databáze pro servery PostgreSQL. Pro každou repliku pro čtení se vám účtují zřízené výpočetní prostředky ve virtuálních jádrech a úložiště v GB/ měsíc.

Přečtěte si, jak [vytvářet a spravovat repliky](howto-read-replicas-portal.md).

## <a name="when-to-use-a-read-replica"></a>Kdy použít repliku pro čtení
Funkce repliky pro čtení pomáhá zlepšit výkon a škálování úloh náročných na čtení. Úlohy pro čtení lze izolovat do replik, zatímco úlohy zápisu lze přesměrovat na hlavní server.

Běžným scénářem je, aby BI a analytické úlohy používaly repliku pro čtení jako zdroj dat pro vytváření sestav.

Vzhledem k tomu, že repliky jsou jen pro čtení, nemají přímo snížit zatížení kapacity zápisu na předlohu. Tato funkce není zaměřena na úlohy náročné na zápis.

Funkce repliky pro čtení používá asynchronní replikaci PostgreSQL. Tato funkce není určena pro scénáře synchronní replikace. Mezi předlohou a replikou bude měřitelné zpoždění. Data v replice nakonec stane konzistentní s daty na předlohu. Tuto funkci použijte pro úlohy, které mohou tuto zpozišit.

## <a name="cross-region-replication"></a>Replikace mezi oblastmi
Můžete vytvořit repliku pro čtení v jiné oblasti než hlavní server. Replikace mezi oblastmi může být užitečná pro scénáře, jako je plánování zotavení po havárii nebo přiblížení dat uživatelům.

Hlavní server můžete mít v libovolné [databázi Azure pro oblast PostgreSQL](https://azure.microsoft.com/global-infrastructure/services/?products=postgresql). Hlavní server může mít repliku ve spárované oblasti nebo v oblastech univerzální repliky. Následující obrázek ukazuje, které oblasti replik jsou k dispozici v závislosti na hlavní oblasti.

[![Čtení oblastí replik](media/concepts-read-replica/read-replica-regions.png)](media/concepts-read-replica/read-replica-regions.png#lightbox)

### <a name="universal-replica-regions"></a>Oblasti univerzální repliky
Vždy můžete vytvořit repliku pro čtení v některé z následujících oblastí bez ohledu na to, kde je umístěn hlavní server. Jedná se o oblasti univerzální repliky:

Austrálie – východ, Austrálie – jihovýchod, střední USA, východní Asie, východní USA, východní USA 2, Japonsko – východ, Japonsko – západ, Korea – střed, Korea Jih, Severní střed USA, Severní Evropa, Jižní Střed USA, Jihovýchodní Asie, Velká Británie – jih, Velká Británie – západ, Západní Evropa, Západní USA.

*Západní USA 2 je dočasně nedostupný jako umístění repliky mezi oblastmi.


### <a name="paired-regions"></a>Spárované oblasti
Kromě oblastí univerzální repliky můžete vytvořit repliku pro čtení ve spárované oblasti Azure hlavního serveru. Pokud neznáte dvojici vaší oblasti, můžete se dozvědět více v [článku Spárované oblasti Azure](../best-practices-availability-paired-regions.md).

Pokud používáte repliky mezi oblastmi pro plánování zotavení po havárii, doporučujeme vytvořit repliku ve spárované oblasti namísto jedné z dalších oblastí. Spárované oblasti se vyhýbají souběžným aktualizacím a upřednostňují fyzickou izolaci a rezidenci dat.  

Existují omezení, aby zvážila: 

* Místní dostupnost: Azure Database for PostgreSQL je dostupná v západníCH USA 2, France Central, SAE North a Germany Central. Jejich spárované oblasti však nejsou k dispozici.
    
* Jednosměrné páry: Některé oblasti Azure jsou spárované jenom v jednom směru. Mezi tyto oblasti patří západní Indie, Brazílie – jih. 
   To znamená, že hlavní server v západní Indii může vytvořit repliku v jižní Indii. Hlavní server v jižní Indii však nemůže vytvořit repliku v západní Indii. Je to proto, že sekundárním regionem západní Indie je jižní Indie, ale jihoindická sekundární oblast není západní Indie.


## <a name="create-a-replica"></a>Vytvoření repliky
Při spuštění pracovního postupu vytvoření repliky se vytvoří prázdná databáze Azure pro postgreSQL server. Nový server je vyplněn daty, která byla na hlavním serveru. Doba vytvoření závisí na množství dat na předloze a na době od poslednítýdenní úplné zálohy. Čas se může pohybovat od několika minut do několika hodin.

Každá replika je povolena pro [automatické zvětšování](concepts-pricing-tiers.md#storage-auto-grow)úložiště . Funkce automatického růstu umožňuje replikě držet krok s daty, která jsou do ní replikována, a zabránit přerušení replikace způsobené chybami z úložiště.

Funkce repliky pro čtení používá fyzickou replikaci PostgreSQL, nikoli logickou replikaci. Streamování replikace pomocí slotů replikace je výchozí provozní režim. V případě potřeby, přesouvání protokolu se používá k dohnat.

Přečtěte si, jak [vytvořit repliku pro čtení na webu Azure Portal](howto-read-replicas-portal.md).

## <a name="connect-to-a-replica"></a>Připojení k replice
Když vytvoříte repliku, nezdědí pravidla brány firewall nebo koncový bod služby virtuální sítě hlavního serveru. Tato pravidla musí být nastavena nezávisle pro repliku.

Replika zdědí účet správce z hlavního serveru. Všechny uživatelské účty na hlavním serveru jsou replikovány do replik pro čtení. Ke replice pro čtení se můžete připojit pouze pomocí uživatelských účtů, které jsou k dispozici na hlavním serveru.

Ke replice se můžete připojit pomocí názvu hostitele a platného uživatelského účtu, stejně jako na běžné masce Azure Database for PostgreSQL server. Pro server s názvem **moje replika** s admin uživatelské jméno **myadmin**, můžete se připojit k replice pomocí psql:

```
psql -h myreplica.postgres.database.azure.com -U myadmin@myreplica -d postgres
```

Na výzvu zadejte heslo pro uživatelský účet.

## <a name="monitor-replication"></a>Monitorování replikace
Azure Database pro PostgreSQL poskytuje dvě metriky pro sledování replikace. Tyto dvě metriky jsou **Maximální zpoždění napříč replikami** a **replika Lag**. Informace o tom, jak zobrazit tyto metriky, naleznete v části **Sledování repliky** [článku s návody k čtení](howto-read-replicas-portal.md).

Metrika **Max Lag Across Replicas** zobrazuje zpoždění v bajtech mezi hlavní a nejvíce zaostávající replikou. Tato metrika je k dispozici pouze na hlavním serveru.

Metrika **Zpoždění replik** zobrazuje čas od poslední přehrané transakce. Pokud na hlavním serveru nedochází k žádným transakcím, metrika odráží tuto časovou prodlevu. Tato metrika je k dispozici pouze pro repliky serverů. Zpoždění replik se `pg_stat_wal_receiver` vypočítá ze zobrazení:

```SQL
EXTRACT (EPOCH FROM now() - pg_last_xact_replay_timestamp());
```

Nastavte výstrahu, která vás bude informovat, když zpoždění repliky dosáhne hodnoty, která není přijatelná pro vaše úlohy. 

Chcete-li získat další přehled, zadejte dotaz na hlavní server přímo, abyste získali zpoždění replikace v bajtů na všech replikách.

V PostgreSQL verzi 10:

```SQL
select pg_wal_lsn_diff(pg_current_wal_lsn(), stat.replay_lsn) 
AS total_log_delay_in_bytes from pg_stat_replication;
```

V PostgreSQL verze 9.6 a starší:

```SQL
select pg_xlog_location_diff(pg_current_xlog_location(), stat.replay_location) 
AS total_log_delay_in_bytes from pg_stat_replication;
```

> [!NOTE]
> Pokud se hlavní server nebo replika čtení restartuje, čas potřebný k restartování a dohonění se projeví v metrike Zpoždění replik.

## <a name="stop-replication"></a>Zastavení replikace
Replikace mezi předlohou a replikou můžete zastavit. Akce stop způsobí restartování repliky a odebrání nastavení replikace. Po zastavení replikace mezi hlavním serverem a replikou pro čtení se replika stane samostatným serverem. Data na samostatném serveru jsou data, která byla k dispozici v replice v době spuštění příkazu stop replication. Samostatný server nedožene hlavní server.

> [!IMPORTANT]
> Samostatný server nelze znovu vytvořit v replice.
> Před ukončením repliky na čtené replice se ujistěte, že replika obsahuje všechna data, která požadujete.

Když zastavíte repliku, replika ztratí všechna propojení s předchozím hlavním a jinými replikami.

Přečtěte si, jak [zastavit replikaci repliky repliky](howto-read-replicas-portal.md).

## <a name="failover"></a>Převzetí služeb při selhání
Neexistuje žádné automatické převzetí služeb při selhání mezi hlavním serverem a replikami serverů. 

Vzhledem k tomu, že replikace je asynchronní, je prodleva mezi hlavní a repliky. Množství zpoždění může být ovlivněno řadou faktorů, jako je jak těžké je zatížení spuštěné na hlavním serveru a latence mezi datovými centry. Ve většině případů se zpoždění repliky pohybuje mezi několika sekundami až několika minutami. Skutečné zpoždění replikace můžete sledovat pomocí metriky *Zpoždění replik*, která je k dispozici pro každou repliku. Tato metrika zobrazuje čas od poslední přehrané transakce. Doporučujeme zjistit, jaké je vaše průměrné zpoždění, sledováním zpoždění replik y po určitou dobu. Můžete nastavit výstrahu na zpoždění replik, takže pokud přejde mimo očekávaný rozsah, můžete provést akci.

> [!Tip]
> Pokud převezmete služeb při selhání repliky, zpoždění v době, kdy odpojíte repliku z hlavního serveru, bude znamenat, kolik dat je ztraceno.

Jakmile se rozhodnete, že chcete převzetí služeb při selhání na repliku, 

1. Zastavení replikace replikace do repliky<br/>
   Tento krok je nezbytný k tomu, aby byl server repliky schopen přijímat zápisy. V rámci tohoto procesu bude server repliky restartován a bude odpojen od předlohy. Po zahájení zastavení replikace proces back-end obvykle trvá přibližně 2 minuty. Naleznete [stop replikace](#stop-replication) části tohoto článku pochopit důsledky této akce.
    
2. Nasměrovat aplikaci na (bývalou) repliku<br/>
   Každý server má jedinečný připojovací řetězec. Aktualizujte aplikaci tak, aby místo hlavního serveru ukazovala na (bývalou) repliku.
    
Jakmile aplikace úspěšně zpracovává čtení a zápisy, dokončili jste převzetí služeb při selhání. Množství prostojů, které vaše aplikace uvidí, bude záviset na tom, kdy zjistíte problém a provedete výše uvedené kroky 1 a 2.


## <a name="considerations"></a>Požadavky

Tato část shrnuje aspekty týkající se funkce repliky pro čtení.

### <a name="prerequisites"></a>Požadavky
Před vytvořením repliky `azure.replication_support` pro čtení musí být parametr nastaven na **repliku** na hlavním serveru. Při změně tohoto parametru je nutné restartovat server, aby se změna projevila. Parametr `azure.replication_support` se vztahuje pouze na úrovně optimalizované pro obecné účely a paměť.

### <a name="new-replicas"></a>Nové repliky
Replika pro čtení se vytvoří jako nová databáze Azure pro postgreSQL server. Existující server nelze vytvořit v replice. Nelze vytvořit repliku jiné repliky pro čtení.

### <a name="replica-configuration"></a>Konfigurace repliky
Replika je vytvořena pomocí stejného nastavení výpočetních prostředků a úložiště jako předloha. Po vytvoření repliky lze změnit několik nastavení nezávisle na hlavním serveru: generování výpočetních prostředků, virtuální jádra, úložiště a doba uchovávání záloh. Cenovou úroveň lze také změnit nezávisle, s výjimkou úrovně Basic nebo z ní.

> [!IMPORTANT]
> Před aktualizací hlavního nastavení na novou hodnotu aktualizujte konfiguraci repliky na stejnou nebo vyšší hodnotu. Tato akce zajistí, že replika bude moct udržovat krok se všemi změnami na hlavním serveru.

PostgreSQL vyžaduje, aby `max_connections` hodnota parametru na čtené replice byla větší nebo rovna hlavní hodnotě; v opačném případě se replika nespustí. V Azure Database for PostgreSQL je hodnota parametru `max_connections` založená na skladové jednotce. Další informace najdete [v tématu Limity v Azure Database for PostgreSQL](concepts-limits.md). 

Pokud se pokusíte aktualizovat výše popsané hodnoty serveru, ale nedodržujete limity, zobrazí se chyba.

Pravidla brány firewall, pravidla virtuální sítě a nastavení parametrů nejsou zděděna z hlavního serveru do repliky při vytvoření repliky nebo po jejich vytvoření.

### <a name="max_prepared_transactions"></a>max_prepared_transactions
[PostgreSQL vyžaduje,](https://www.postgresql.org/docs/current/runtime-config-resource.html#GUC-MAX-PREPARED-TRANSACTIONS) aby `max_prepared_transactions` hodnota parametru na čtené replice byla větší nebo rovna hlavní hodnotě; v opačném případě se replika nespustí. Pokud chcete změnit `max_prepared_transactions` na předloze, nejprve změnit na repliky.

### <a name="stopped-replicas"></a>Zastavené repliky
Pokud zastavíte replikaci mezi hlavním serverem a replikou pro čtení, replika se restartuje a použije změnu. Zastavená replika se stane samostatným serverem, který přijímá čtení i zápisy. Samostatný server nelze znovu vytvořit v replice.

### <a name="deleted-master-and-standalone-servers"></a>Odstraněné hlavní a samostatné servery
Po odstranění hlavního serveru se všechny jeho repliky pro čtení stanou samostatnými servery. Repliky jsou restartovány tak, aby odrážely tuto změnu.

## <a name="next-steps"></a>Další kroky
* Přečtěte si, jak [vytvářet a spravovat repliky pro čtení na webu Azure Portal](howto-read-replicas-portal.md).
* Zjistěte, jak [vytvářet a spravovat repliky čtení v rozhraní API Azure a rozhraní REST API](howto-read-replicas-cli.md).
