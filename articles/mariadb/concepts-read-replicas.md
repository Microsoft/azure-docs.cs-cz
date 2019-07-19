---
title: Čtení replik v Azure Database for MariaDB
description: Tento článek popisuje repliky pro čtení pro Azure Database for MariaDB.
author: ajlam
ms.author: andrela
ms.service: mariadb
ms.topic: conceptual
ms.date: 07/12/2019
ms.openlocfilehash: e6bbe15727a6f989d8c16c67591d39d7870d5708
ms.sourcegitcommit: de47a27defce58b10ef998e8991a2294175d2098
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/15/2019
ms.locfileid: "67874895"
---
# <a name="read-replicas-in-azure-database-for-mariadb"></a>Čtení replik v Azure Database for MariaDB

Funkce replika čtení umožňuje replikovat data z Azure Database for MariaDB serveru do serveru jen pro čtení. Replikaci můžete provést z hlavního serveru až do pěti replik. Repliky se asynchronně aktualizují pomocí technologie replikace v binárním protokolu (binlog) modulu MariaDB s ID globálního transakce (GTID). Další informace o replikaci binlog najdete v tématu [Přehled replikace binlog](https://mariadb.com/kb/en/library/replication-overview/).

> [!IMPORTANT]
> Repliku pro čtení můžete vytvořit ve stejné oblasti jako váš hlavní server nebo v libovolné jiné oblasti Azure podle vašeho výběru. Repliky čtení (stejné oblasti a mezi oblastí) jsou momentálně ve verzi Public Preview.

Repliky jsou nové servery, které spravujete podobně jako běžné Azure Database for MariaDB servery. Pro každou repliku čtení se vám bude účtovat zajištěné výpočetní prostředky v virtuální jádra a úložišti v GB/měsíc.

Další informace o replikaci GTID najdete v [dokumentaci k replikaci MariaDB](https://mariadb.com/kb/en/library/gtid/).

## <a name="when-to-use-a-read-replica"></a>Kdy použít repliku čtení

Funkce replika čtení pomáhá zlepšit výkon a škálu úloh náročných na čtení. Úlohy čtení se dají pro repliky izolovat, zatímco úlohy zápisu můžou být směrované do hlavní větve.

Běžným scénářem je, aby úlohy BI a analýzy používaly jako zdroj dat pro vytváření sestav repliku pro čtení.

Vzhledem k tomu, že repliky jsou jen pro čtení, nesnižují přímo na hlavní úrovni zátěže s kapacitou pro zápis. Tato funkce není zaměřená na úlohy náročné na zápis.

Funkce replika čtení používá asynchronní replikaci. Tato funkce není určena pro scénáře synchronní replikace. Mezi hlavním serverem a replikou bude měřitelné zpoždění. Data v replice nakonec budou konzistentní s daty v hlavní databázi. Tato funkce se používá pro úlohy, které můžou toto zpoždění obsloužit.

Čtení replik může zlepšit váš plán zotavení po havárii. Pokud dojde k regionální havárii a váš hlavní server není dostupný, můžete svoji úlohu nasměrovat na repliku v jiné oblasti. Chcete-li to provést, umožněte, aby replika přijímala zápisy pomocí funkce zastavit replikaci. Pak můžete svou aplikaci přesměrovat pomocí aktualizace připojovacího řetězce. Další informace najdete v části [zastavení replikace](#stop-replication) .

## <a name="create-a-replica"></a>Vytvoření repliky

Pokud hlavní server nemá žádné existující servery repliky, hlavní server se nejprve restartuje a připraví se pro replikaci.

Když spustíte pracovní postup vytvoření repliky, vytvoří se prázdný Azure Database for MariaDB Server. Nový server je vyplněn daty, která byla na hlavním serveru. Čas vytvoření závisí na množství dat v hlavní databázi a na čase od posledního týdenního úplného zálohování. Čas může být v rozsahu od několika minut až po několik hodin.

> [!NOTE]
> Pokud na svých serverech nemáte nastavené upozornění na úložiště, doporučujeme, abyste to provedli. Výstraha vás informuje, když se server blíží svému limitu úložiště, což bude mít vliv na replikaci.

Naučte se [vytvořit repliku pro čtení v Azure Portal](howto-read-replicas-portal.md).

## <a name="connect-to-a-replica"></a>Připojení k replice

Když vytváříte repliku, nedědí pravidla firewallu ani koncový bod služby VNet hlavního serveru. Tato pravidla musí být pro repliku nastavena nezávisle.

Replika dědí účet správce z hlavního serveru. Všechny uživatelské účty na hlavním serveru se replikují do replik pro čtení. K replice pro čtení se můžete připojit pouze pomocí uživatelských účtů, které jsou k dispozici na hlavním serveru.

K replice se můžete připojit pomocí jejího názvu hostitele a platného uživatelského účtu, stejně jako při běžném Azure Database for MariaDBm serveru. Pro server s názvem **myreplica** s uživatelským jménem správce **myadmin**se můžete připojit k replice pomocí rozhraní příkazového řádku MySQL:

```bash
mysql -h myreplica.mariadb.database.azure.com -u myadmin@myreplica -p
```

Na příkazovém řádku zadejte heslo pro uživatelský účet.

## <a name="monitor-replication"></a>Monitorování replikace

Azure Database for MariaDB poskytuje metriku **prodlevy replikace v sekundách** v Azure monitor. Tato metrika je k dispozici pouze pro repliky.

Tato metrika se počítá pomocí `seconds_behind_master` metriky dostupné v `SHOW SLAVE STATUS` příkazu MariaDB.

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

Replika pro čtení je vytvořená jako nový server Azure Database for MariaDB. Existující server nelze vytvořit do repliky. Nelze vytvořit repliku jiné repliky pro čtení.

### <a name="replica-configuration"></a>Konfigurace repliky

Replika je vytvořena pomocí stejné konfigurace serveru jako hlavní. Po vytvoření repliky je možné změnit několik nastavení nezávisle na hlavním serveru: generování výpočetních prostředků, virtuální jádra, úložiště, doba uchování zálohy a verze stroje MariaDB. Cenová úroveň se dá změnit také nezávisle, s výjimkou nebo z úrovně Basic.

> [!IMPORTANT]
> Než bude konfigurace hlavního serveru aktualizována na nové hodnoty, aktualizujte konfiguraci repliky na hodnotu stejné nebo větší. Tato akce zajistí, že replika bude udržovat všechny změny provedené v hlavní větvi.

### <a name="stopped-replicas"></a>Zastavené repliky

Pokud zastavíte replikaci mezi hlavním serverem a replikou pro čtení, zastavená replika se stane samostatným serverem, který přijímá čtení i zápis. Samostatný server se nedá znovu vytvořit do repliky.

### <a name="deleted-master-and-standalone-servers"></a>Odstraněné hlavní a samostatné servery

Při odstranění hlavního serveru se replikace zastaví na všechny repliky čtení. Tyto repliky se stanou samostatnými servery. Samotný hlavní server je odstraněný.

### <a name="user-accounts"></a>Uživatelské účty

Uživatelé na hlavním serveru se replikují do replik pro čtení. K replice pro čtení se můžete připojit pouze pomocí uživatelských účtů, které jsou k dispozici na hlavním serveru.

### <a name="server-parameters"></a>Parametry serveru

Aby nedocházelo k nedostatku synchronizace dat a aby se předešlo potenciálním ztrátám nebo poškození dat, některé parametry serveru jsou při použití replik čtení zamčené.

Následující parametry serveru jsou uzamčené na hlavním serveru i na serverech repliky:
- [`innodb_file_per_table`](https://mariadb.com/kb/en/library/innodb-system-variables/#innodb_file_per_table) 
- [`log_bin_trust_function_creators`](https://mariadb.com/kb/en/library/replication-and-binary-log-system-variables/#log_bin_trust_function_creators)

[`event_scheduler`](https://mariadb.com/kb/en/library/server-system-variables/#event_scheduler) Parametr je uzamčen na serverech repliky.

### <a name="other"></a>Ostatní

- Vytvoření repliky repliky není podporováno.
- Tabulky v paměti můžou způsobit, že se repliky nesynchronizují. Toto je omezení technologie MariaDB pro replikaci.
- Zajistěte, aby tabulky hlavního serveru měly primární klíče. Nedostatek primárních klíčů může způsobit latenci replikace mezi hlavními a replikami.

## <a name="next-steps"></a>Další postup

- Naučte se [vytvářet a spravovat repliky pro čtení pomocí Azure Portal](howto-read-replicas-portal.md)
