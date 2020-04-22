---
title: Čtení replik – Azure Database for MySQL.
description: 'Další informace o replikách pro čtení v Azure Database for MySQL: výběr oblastí, vytváření replik, připojení k replikám, sledování replik a zastavení replikace.'
author: ajlam
ms.author: andrela
ms.service: mysql
ms.topic: conceptual
ms.date: 04/21/2020
ms.openlocfilehash: 47f686f810f62fe03a9b0217677c436f3b91782b
ms.sourcegitcommit: d57d2be09e67d7afed4b7565f9e3effdcc4a55bf
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/22/2020
ms.locfileid: "81767883"
---
# <a name="read-replicas-in-azure-database-for-mysql"></a>Repliky pro čtení ve službě Azure Database for MySQL

Funkce repliky pro čtení umožňuje replikovat data ze serveru Azure Database for MySQL na server jen pro čtení. Z hlavního serveru je můžete replikovat až na pět replik. Repliky se aktualizují asynchronně s využitím technologie replikace na základě pozice v souboru binárního protokolu (binlog) nativní pro stroj MySQL. Další informace o replikaci přihrádek najdete v [přehledu replikace binlogu MySQL](https://dev.mysql.com/doc/refman/5.7/en/binlog-replication-configuration-overview.html).

Repliky jsou nové servery, které spravujete podobně jako běžné azure databáze pro servery MySQL. Pro každou repliku pro čtení se vám účtují zřízené výpočetní prostředky ve virtuálních jádrech a úložiště v GB/ měsíc.

Další informace o funkcích a problémech s replikací MySQL naleznete v [dokumentaci k replikaci MySQL](https://dev.mysql.com/doc/refman/5.7/en/replication-features.html).

## <a name="when-to-use-a-read-replica"></a>Kdy použít repliku pro čtení

Funkce repliky pro čtení pomáhá zlepšit výkon a škálování úloh náročných na čtení. Úlohy pro čtení lze izolovat do replik, zatímco úlohy zápisu lze přesměrovat na hlavní server.

Běžným scénářem je, aby BI a analytické úlohy používaly repliku pro čtení jako zdroj dat pro vytváření sestav.

Vzhledem k tomu, že repliky jsou jen pro čtení, nemají přímo snížit zatížení kapacity zápisu na předlohu. Tato funkce není zaměřena na úlohy náročné na zápis.

Funkce repliky pro čtení používá asynchronní replikaci MySQL. Tato funkce není určena pro scénáře synchronní replikace. Mezi předlohou a replikou bude měřitelné zpoždění. Data v replice nakonec stane konzistentní s daty na předlohu. Tuto funkci použijte pro úlohy, které mohou tuto zpozišit.

## <a name="cross-region-replication"></a>Replikace mezi oblastmi
Můžete vytvořit repliku pro čtení v jiné oblasti než hlavní server. Replikace mezi oblastmi může být užitečná pro scénáře, jako je plánování zotavení po havárii nebo přiblížení dat uživatelům.

Hlavní server můžete mít v libovolné [databázi Azure pro oblast MySQL](https://azure.microsoft.com/global-infrastructure/services/?products=mysql).  Hlavní server může mít repliku ve spárované oblasti nebo v oblastech univerzální repliky. Následující obrázek ukazuje, které oblasti replik jsou k dispozici v závislosti na hlavní oblasti.

[![Čtení oblastí replik](media/concepts-read-replica/read-replica-regions.png)](media/concepts-read-replica/read-replica-regions.png#lightbox)

### <a name="universal-replica-regions"></a>Oblasti univerzální repliky
Repliku pro čtení můžete vytvořit v některé z následujících oblastí bez ohledu na umístění hlavního serveru. Podporované oblasti univerzální repliky zahrnují:

Austrálie – východ, Austrálie – jihovýchod, střední USA, východní Asie, východní USA, východní USA 2, Japonsko – východ, Japonsko – západ, Korea – střed, Korea Jih, Severní střed USA, Severní Evropa, Jižní Střed USA, Jihovýchodní Asie, Velká Británie – jih, Velká Británie – západ, Západní Evropa, Západní USA.

*Západní USA 2 je dočasně nedostupný jako umístění repliky mezi oblastmi.

### <a name="paired-regions"></a>Spárované oblasti
Kromě oblastí univerzální repliky můžete vytvořit repliku pro čtení ve spárované oblasti Azure hlavního serveru. Pokud neznáte dvojici vaší oblasti, můžete se dozvědět více v [článku Spárované oblasti Azure](../best-practices-availability-paired-regions.md).

Pokud používáte repliky mezi oblastmi pro plánování zotavení po havárii, doporučujeme vytvořit repliku ve spárované oblasti namísto jedné z dalších oblastí. Spárované oblasti se vyhýbají souběžným aktualizacím a upřednostňují fyzickou izolaci a rezidenci dat.  

Existují však omezení, která je třeba zvážit: 

* Místní dostupnost: Azure Database for MySQL je dostupná v západníCH USA 2, France Central, SAE North a Germany Central. Jejich spárované oblasti však nejsou k dispozici.
    
* Jednosměrné páry: Některé oblasti Azure jsou spárované jenom v jednom směru. Mezi tyto oblasti patří západní Indie, Brazílie – jih a Vláda USA ve Virginii. 
   To znamená, že hlavní server v západní Indii může vytvořit repliku v jižní Indii. Hlavní server v jižní Indii však nemůže vytvořit repliku v západní Indii. Je to proto, že sekundárním regionem západní Indie je jižní Indie, ale jihoindická sekundární oblast není západní Indie.


## <a name="create-a-replica"></a>Vytvoření repliky

Pokud hlavní server nemá žádné existující repliky serverů, hlavní server se nejprve restartuje, aby se připravil na replikaci.

Při spuštění pracovního postupu vytvoření repliky se vytvoří prázdná databáze Azure pro mysql server. Nový server je vyplněn daty, která byla na hlavním serveru. Doba vytvoření závisí na množství dat na předloze a na době od poslednítýdenní úplné zálohy. Čas se může pohybovat od několika minut do několika hodin.

Každá replika je povolena pro [automatické zvětšování](concepts-pricing-tiers.md#storage-auto-grow)úložiště . Funkce automatického růstu umožňuje replikě držet krok s daty, která jsou do ní replikována, a zabránit přerušení replikace způsobenému chybami mimo úložiště.

Přečtěte si, jak [vytvořit repliku pro čtení na webu Azure Portal](howto-read-replicas-portal.md).

## <a name="connect-to-a-replica"></a>Připojení k replice

Při vytváření replika zdědí pravidla brány firewall hlavního serveru. Poté jsou tato pravidla nezávislá na hlavním serveru.

Replika zdědí účet správce z hlavního serveru. Všechny uživatelské účty na hlavním serveru jsou replikovány do replik pro čtení. Ke replice pro čtení se můžete připojit pouze pomocí uživatelských účtů, které jsou k dispozici na hlavním serveru.

Ke replice se můžete připojit pomocí názvu hostitele a platného uživatelského účtu, stejně jako na běžné databázi Azure pro mysql server. Pro server s názvem **myreplica** s admin uživatelské jméno **myadmin**, můžete se připojit k replice pomocí mysql CLI:

```bash
mysql -h myreplica.mysql.database.azure.com -u myadmin@myreplica -p
```

Na výzvu zadejte heslo pro uživatelský účet.

## <a name="monitor-replication"></a>Monitorování replikace

Azure Database for MySQL poskytuje **zpoždění replikace v sekundách** metriky v Azure Monitor. Tato metrika je k dispozici pouze pro repliky.

Tato metrika se `seconds_behind_master` vypočítá pomocí metriky `SHOW SLAVE STATUS` dostupné v příkazu MySQL.

Nastavte výstrahu, která vás bude informovat, když zpoždění replikace dosáhne hodnoty, která není přijatelná pro vaše úlohy.

## <a name="stop-replication"></a>Zastavení replikace

Replikace mezi předlohou a replikou můžete zastavit. Po zastavení replikace mezi hlavním serverem a replikou pro čtení se replika stane samostatným serverem. Data na samostatném serveru jsou data, která byla k dispozici v replice v době spuštění příkazu stop replication. Samostatný server nedožene hlavní server.

Pokud se rozhodnete zastavit replikaci repliky, ztratí všechna propojení s předchozím hlavním a jinými replikami. Neexistuje žádné automatické převzetí služeb při selhání mezi hlavní a jeho repliky.

> [!IMPORTANT]
> Samostatný server nelze znovu vytvořit v replice.
> Před ukončením repliky na čtené replice se ujistěte, že replika obsahuje všechna data, která požadujete.

Přečtěte si, jak [zastavit replikaci repliky repliky](howto-read-replicas-portal.md).

## <a name="considerations-and-limitations"></a>Důležité informace a omezení

### <a name="pricing-tiers"></a>Cenové úrovně

Repliky pro čtení jsou aktuálně k dispozici pouze v cenových úrovních optimalizované pro obecné účely a paměť.

### <a name="master-server-restart"></a>Restartování hlavního serveru

Když vytvoříte repliku pro hlavní server, který nemá žádné existující repliky, hlavní server se nejprve restartuje, aby se připravil na replikaci. Vezměte to v úvahu a proveďte tyto operace během období mimo špičku.

### <a name="new-replicas"></a>Nové repliky

Replika pro čtení se vytvoří jako nová databáze Azure pro server MySQL. Existující server nelze vytvořit v replice. Nelze vytvořit repliku jiné repliky pro čtení.

### <a name="replica-configuration"></a>Konfigurace repliky

Replika je vytvořena pomocí stejné konfigurace serveru jako předloha. Po vytvoření repliky lze změnit několik nastavení nezávisle na hlavním serveru: generování výpočetních prostředků, virtuální jádra, úložiště a doba uchovávání záloh. Cenovou úroveň lze také změnit nezávisle, s výjimkou úrovně Basic nebo z ní.

> [!IMPORTANT]
> Před aktualizací konfigurace hlavního serveru na nové hodnoty aktualizujte konfiguraci repliky na stejné nebo vyšší hodnoty. Tato akce zajistí, že replika bude moct udržovat krok se všemi změnami na hlavním serveru.

Pravidla brány firewall a nastavení parametrů jsou zděděna z hlavního serveru do repliky při vytvoření repliky. Poté jsou pravidla repliky nezávislá.

### <a name="stopped-replicas"></a>Zastavené repliky

Pokud zastavíte replikaci mezi hlavním serverem a replikou pro čtení, zastaví se zastavená replika samostatným serverem, který přijímá čtení i zápisy. Samostatný server nelze znovu vytvořit v replice.

### <a name="deleted-master-and-standalone-servers"></a>Odstraněné hlavní a samostatné servery

Po odstranění hlavního serveru je replikace zastavena na všechny repliky pro čtení. Tyto repliky se automaticky stanou samostatnými servery a mohou přijímat čtení i zápisy. Samotný hlavní server je odstraněn.

### <a name="user-accounts"></a>Uživatelské účty

Uživatelé na hlavním serveru jsou replikováni do replik pro čtení. Ke čtecí repse se můžete připojit pouze pomocí uživatelských účtů dostupných na hlavním serveru.

### <a name="server-parameters"></a>Parametry serveru

Aby se při použití replik pro čtení zabránilo přerušení synchronizace dat a možné ztrátě nebo poškození dat, některé parametry serveru neumožňují aktualizaci.

Následující parametry serveru jsou uzamčeny na serveru hlavního i replikového serveru:
- [`innodb_file_per_table`](https://dev.mysql.com/doc/refman/5.7/en/innodb-multiple-tablespaces.html) 
- [`log_bin_trust_function_creators`](https://dev.mysql.com/doc/refman/5.7/en/replication-options-binary-log.html#sysvar_log_bin_trust_function_creators)

Parametr [`event_scheduler`](https://dev.mysql.com/doc/refman/5.7/en/server-system-variables.html#sysvar_event_scheduler) je uzamčen na serverech repliky. 

### <a name="other"></a>Ostatní

- Globální identifikátory transakcí (GTID) nejsou podporovány.
- Vytvoření repliky repliky není podporováno.
- Tabulky v paměti může způsobit repliky stanou synchronizovány. Jedná se o omezení technologie replikace MySQL. Přečtěte si více v [referenční dokumentaci MySQL](https://dev.mysql.com/doc/refman/5.7/en/replication-features-memory.html) pro více informací.
- Ujistěte se, že tabulky hlavního serveru mají primární klíče. Nedostatek primárních klíčů může mít za následek latenci replikace mezi předlohou a replikami.
- Projděte si úplný seznam omezení replikace MySQL v [dokumentaci MySQL](https://dev.mysql.com/doc/refman/5.7/en/replication-features.html)

## <a name="next-steps"></a>Další kroky

- Zjistěte, jak [vytvářet a spravovat repliky pro čtení pomocí webu Azure Portal](howto-read-replicas-portal.md)
- Zjistěte, jak [vytvářet a spravovat repliky čtení pomocí rozhraní API Azure a rozhraní REST API](howto-read-replicas-cli.md)
