---
title: Čtení replik v Azure Database for MySQL.
description: Tento článek popisuje repliky pro čtení pro Azure Database for MySQL.
author: ajlam
ms.author: andrela
ms.service: mysql
ms.topic: conceptual
ms.date: 09/04/2019
ms.openlocfilehash: f567eefee84cf6a01afad4e5245337dd92b8cc48
ms.sourcegitcommit: f176e5bb926476ec8f9e2a2829bda48d510fbed7
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/04/2019
ms.locfileid: "70309431"
---
# <a name="read-replicas-in-azure-database-for-mysql"></a>Repliky pro čtení ve službě Azure Database for MySQL

Funkce repliky pro čtení umožňuje replikovat data ze serveru Azure Database for MySQL na server jen pro čtení. Z hlavního serveru je můžete replikovat až na pět replik. Repliky se aktualizují asynchronně s využitím technologie replikace na základě pozice v souboru binárního protokolu (binlog) nativní pro stroj MySQL. Další informace o replikaci binlog najdete v tématu [Přehled replikace MySQL binlog](https://dev.mysql.com/doc/refman/5.7/en/binlog-replication-configuration-overview.html).

Repliky jsou nové servery, které spravujete podobně jako běžné Azure Database for MySQL servery. Pro každou repliku čtení se vám bude účtovat zajištěné výpočetní prostředky v virtuální jádra a úložišti v GB/měsíc.

Další informace o funkcích a problémech replikace MySQL najdete v [dokumentaci k replikaci MySQL](https://dev.mysql.com/doc/refman/5.7/en/replication-features.html).

## <a name="when-to-use-a-read-replica"></a>Kdy použít repliku čtení

Funkce replika čtení pomáhá zlepšit výkon a škálu úloh náročných na čtení. Úlohy čtení se dají pro repliky izolovat, zatímco úlohy zápisu můžou být směrované do hlavní větve.

Běžným scénářem je, aby úlohy BI a analýzy používaly jako zdroj dat pro vytváření sestav repliku pro čtení.

Vzhledem k tomu, že repliky jsou jen pro čtení, nesnižují přímo na hlavní úrovni zátěže s kapacitou pro zápis. Tato funkce není zaměřená na úlohy náročné na zápis.

Funkce replika čtení používá asynchronní replikaci MySQL. Tato funkce není určena pro scénáře synchronní replikace. Mezi hlavním serverem a replikou bude měřitelné zpoždění. Data v replice nakonec budou konzistentní s daty v hlavní databázi. Tato funkce se používá pro úlohy, které můžou toto zpoždění obsloužit.

## <a name="cross-region-replication"></a>Replikace mezi oblastmi
Z hlavního serveru můžete vytvořit repliku pro čtení v jiné oblasti. Replikace mezi oblastmi může být užitečná pro scénáře, jako je plánování zotavení po havárii, nebo pro uživatele přiblížit data.

Hlavní server můžete mít v libovolné [Azure Database for MySQL oblasti](https://azure.microsoft.com/global-infrastructure/services/?products=mysql).  Hlavní server může mít repliku ve své spárované oblasti nebo oblastech univerzální repliky.

### <a name="universal-replica-regions"></a>Oblasti univerzální repliky
Repliku pro čtení můžete vždy vytvořit v některé z následujících oblastí bez ohledu na to, kde se nachází váš hlavní server. Jedná se o oblasti univerzální repliky:

Austrálie – východ, Austrálie – jihovýchod, Střed USA, Východní Asie, Východní USA, Východní USA 2, Japonsko – východ, Japonsko – západ, Korea – jih, střed, střed USA – sever, Severní Evropa, střed USA – jih, jihovýchodní Asie, Velká Británie – jih, Velká Británie – západ, západní Evropa, Západní USA, západní USA 2.


### <a name="paired-regions"></a>Spárované oblasti
Kromě oblastí univerzální repliky můžete vytvořit repliku pro čtení ve spárované oblasti Azure vašeho hlavního serveru. Pokud neznáte pár vaší oblasti, můžete získat další informace v [článku spárované oblasti Azure](../best-practices-availability-paired-regions.md).

Pokud používáte repliky mezi jednotlivými oblastmi pro plánování zotavení po havárii, doporučujeme vytvořit repliku v spárované oblasti namísto jedné z ostatních oblastí. Spárované oblasti zabraňují souběžným aktualizacím a přiřazují fyzickou izolaci a zasídlí dat.  

Je však třeba vzít v úvahu omezení: 

* Dostupnost podle oblastí: Azure Database for MySQL je k dispozici v Západní USA 2, Francii Central, Spojené arabské emiráty Severní a Německo – střed. Nicméně jejich spárované oblasti nejsou k dispozici.
    
* Jednosměrné páry: Některé oblasti Azure jsou spárovány pouze v jednom směru. Mezi tyto oblasti patří Západní Indie, Brazílie – jih a US Gov – Virginie. 
   To znamená, že hlavní server v Západní Indie může vytvořit repliku v Jižní Indie. Hlavní server v Jižní Indie ale nemůže vytvořit repliku v Západní Indie. Důvodem je to, že sekundární oblast Západní Indie je Jižní Indie, ale sekundární oblast Jižní Indie není Západní Indie.


## <a name="create-a-replica"></a>Vytvoření repliky

Pokud hlavní server nemá žádné existující servery repliky, hlavní server se nejprve restartuje a připraví se pro replikaci.

Když spustíte pracovní postup vytvoření repliky, vytvoří se prázdný Azure Database for MySQL server. Nový server je vyplněn daty, která byla na hlavním serveru. Čas vytvoření závisí na množství dat v hlavní databázi a na čase od posledního týdenního úplného zálohování. Čas může být v rozsahu od několika minut až po několik hodin.

U každé repliky je povoleno [Automatické zvětšování](concepts-pricing-tiers.md#storage-auto-grow)úložiště. Funkce automatického zvětšení umožňuje replice udržovat data, která jsou do ní replikována, a zabránit přerušení replikace způsobené chybou úložiště.

Naučte se [vytvořit repliku pro čtení v Azure Portal](howto-read-replicas-portal.md).

## <a name="connect-to-a-replica"></a>Připojení k replice

Když vytváříte repliku, nedědí pravidla firewallu ani koncový bod služby VNet hlavního serveru. Tato pravidla musí být pro repliku nastavena nezávisle.

Replika dědí účet správce z hlavního serveru. Všechny uživatelské účty na hlavním serveru se replikují do replik pro čtení. K replice pro čtení se můžete připojit pouze pomocí uživatelských účtů, které jsou k dispozici na hlavním serveru.

K replice se můžete připojit pomocí jejího názvu hostitele a platného uživatelského účtu, stejně jako při běžném Azure Database for MySQLm serveru. Pro server s názvem **myreplica** s uživatelským jménem správce **myadmin**se můžete připojit k replice pomocí rozhraní příkazového řádku MySQL:

```bash
mysql -h myreplica.mysql.database.azure.com -u myadmin@myreplica -p
```

Na příkazovém řádku zadejte heslo pro uživatelský účet.

## <a name="monitor-replication"></a>Monitorování replikace

Azure Database for MySQL poskytuje metriku **prodlevy replikace v sekundách** v Azure monitor. Tato metrika je k dispozici pouze pro repliky.

Tato metrika se počítá pomocí `seconds_behind_master` metriky dostupné v `SHOW SLAVE STATUS` příkazu MySQL.

Nastavte výstrahu, která vás informuje, když prodleva replikace dosáhne hodnoty, která není pro vaše zatížení přijatelná.

## <a name="stop-replication"></a>Zastavit replikaci

Replikaci mezi hlavní a replikou můžete zastavit. Po zastavení replikace mezi hlavním serverem a replikou pro čtení se replika samostatného serveru. Data na samostatném serveru jsou data, která byla v replice k dispozici v době spuštění příkazu pro zastavení replikace. Samostatný server není zachytávání s hlavním serverem.

Pokud se rozhodnete zastavit replikaci do repliky, ztratíte všechny odkazy na předchozí hlavní a jiné repliky. Mezi hlavním serverem a jeho replikou neexistuje automatizované převzetí služeb při selhání.

> [!IMPORTANT]
> Samostatný server se nedá znovu vytvořit do repliky.
> Před zastavením replikace v replice pro čtení zajistěte, aby měla replika všechna data, která požadujete.

Přečtěte si, jak [zastavit replikaci do repliky](howto-read-replicas-portal.md).

## <a name="considerations-and-limitations"></a>Doporučení a omezení

### <a name="pricing-tiers"></a>Cenové úrovně

Repliky čtení jsou v tuto chvíli dostupné jenom v Pro obecné účely a paměťově optimalizované cenové úrovně.

### <a name="master-server-restart"></a>Restartování hlavního serveru

Když vytvoříte repliku pro hlavní server, který nemá žádné existující repliky, hlavní počítač se nejprve restartuje a připraví se pro replikaci. Věnujte prosím tuto pozornost a udělejte tyto operace v době mimo špičku.

### <a name="new-replicas"></a>Nové repliky

Replika pro čtení je vytvořená jako nový server Azure Database for MySQL. Existující server nelze vytvořit do repliky. Nelze vytvořit repliku jiné repliky pro čtení.

### <a name="replica-configuration"></a>Konfigurace repliky

Replika je vytvořena pomocí stejné konfigurace serveru jako hlavní. Po vytvoření repliky se dá několik nastavení měnit nezávisle na hlavním serveru: generování výpočetních prostředků, virtuální jádra, úložiště a doba uchovávání záloh. Cenová úroveň se dá změnit také nezávisle, s výjimkou nebo z úrovně Basic.

> [!IMPORTANT]
> Před aktualizací konfigurace hlavního serveru na nové hodnoty aktualizujte konfiguraci repliky na stejné nebo vyšší hodnoty. Tato akce zajistí, že replika bude moct udržovat krok se všemi změnami na hlavním serveru.

### <a name="stopped-replicas"></a>Zastavené repliky

Pokud zastavíte replikaci mezi hlavním serverem a replikou pro čtení, zastavená replika se stane samostatným serverem, který přijímá čtení i zápis. Samostatný server se nedá znovu vytvořit do repliky.

### <a name="deleted-master-and-standalone-servers"></a>Odstraněné hlavní a samostatné servery

Při odstranění hlavního serveru se replikace zastaví na všechny repliky čtení. Tyto repliky se stanou samostatnými servery. Samotný hlavní server je odstraněný.

### <a name="user-accounts"></a>Uživatelské účty

Uživatelé na hlavním serveru se replikují do replik pro čtení. K replice pro čtení se můžete připojit pouze pomocí uživatelských účtů, které jsou k dispozici na hlavním serveru.

### <a name="server-parameters"></a>Parametry serveru

Aby nedocházelo k nedostatku synchronizace dat a aby se předešlo potenciálním ztrátám nebo poškození dat, některé parametry serveru jsou při použití replik čtení zamčené.

Následující parametry serveru jsou uzamčené na hlavním serveru i na serverech repliky:
- [`innodb_file_per_table`](https://dev.mysql.com/doc/refman/5.7/en/innodb-multiple-tablespaces.html) 
- [`log_bin_trust_function_creators`](https://dev.mysql.com/doc/refman/5.7/en/replication-options-binary-log.html#sysvar_log_bin_trust_function_creators)

[`event_scheduler`](https://dev.mysql.com/doc/refman/5.7/en/server-system-variables.html#sysvar_event_scheduler) Parametr je uzamčen na serverech repliky. 

### <a name="other"></a>Ostatní

- Identifikátory globálních transakcí (GTID) se nepodporují.
- Vytvoření repliky repliky není podporováno.
- Tabulky v paměti můžou způsobit, že se repliky nesynchronizují. Toto je omezení technologie replikace MySQL. Další informace najdete v [referenční dokumentaci k MySQL](https://dev.mysql.com/doc/refman/5.7/en/replication-features-memory.html) .
- Zajistěte, aby tabulky hlavního serveru měly primární klíče. Nedostatek primárních klíčů může způsobit latenci replikace mezi hlavními a replikami.
- Úplný seznam omezení replikace MySQL najdete v [dokumentaci k MySQL](https://dev.mysql.com/doc/refman/5.7/en/replication-features.html) .

## <a name="next-steps"></a>Další postup

- Naučte se [vytvářet a spravovat repliky pro čtení pomocí Azure Portal](howto-read-replicas-portal.md)
- Naučte se [vytvářet a spravovat repliky pro čtení pomocí Azure CLI](howto-read-replicas-cli.md) .
