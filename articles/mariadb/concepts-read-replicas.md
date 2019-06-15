---
title: Repliky pro čtení ve službě Azure Database pro MariaDB
description: Tento článek popisuje další repliky pro službu Azure Database pro MariaDB.
author: ajlam
ms.author: andrela
ms.service: mariadb
ms.topic: conceptual
ms.date: 06/10/2019
ms.openlocfilehash: 8abe257090b5159053a37350c9e24cc27073679b
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/13/2019
ms.locfileid: "67079363"
---
# <a name="read-replicas-in-azure-database-for-mariadb"></a>Repliky pro čtení ve službě Azure Database pro MariaDB

Funkce repliky pro čtení umožňuje replikaci dat ze služby Azure Database pro MariaDB server na serveru jen pro čtení. Můžete replikovat z hlavního serveru až pěti replikami v rámci stejné oblasti jako hlavní server. Repliky se aktualizují asynchronně pomocí technologie replikace na základě pozice souboru binární protokol (binlog) modul MariaDB s globálním transakčním ID (GTID). Další informace o binlog replikace, najdete v článku [Přehled replikace binlog](https://mariadb.com/kb/en/library/replication-overview/).

> [!IMPORTANT]
> Stejné oblasti čtení replik je aktuálně ve verzi public preview.

Repliky jsou nové servery, které spravujete podobně jako na běžnou – Azure Database pro MariaDB servery. Pro každé čtení replik, bude se vám účtovat za zřízených výpočetních jádrech a úložiště v GB / měsíc.

Další informace o GTID replikace, najdete v tématu [MariaDB replikace dokumentaci](https://mariadb.com/kb/en/library/gtid/).

## <a name="when-to-use-a-read-replica"></a>Kdy použít repliky pro čtení

Funkce repliky pro čtení pomáhá zlepšit výkon a škálování úlohy náročné na čtení. Úlohy pro čtení můžou být izolované do replik, při zápisu úlohy mohou být přesměrováni na hlavní server.

Běžný scénář, kdy je, aby BI a analytických úloh pomocí repliky pro čtení jako zdroj dat pro generování sestav.

Vzhledem k tomu repliky jen pro čtení, není zkracují přímo zápisu kapacity zatížení hlavní server. Tato funkce není určenou pro úlohy náročné na zápis.

Funkce repliky pro čtení používá asynchronní replikace. Tato funkce není určena pro scénáře synchronní replikace. Bude docházet k prodlevám měřitelné mezi hlavního serveru a repliky. Data v replice nakonec bude konzistentní s daty na hlavní server. Pomocí této funkce pro úlohy, které můžou vyhovovat tomuto zpoždění dochází.

Repliky pro čtení můžete vylepšit vašeho plánu zotavení po havárii. Je-li regionálního a hlavní server není k dispozici, můžete nasměrovat vaši úlohu do repliky v jiné oblasti. Provedete to tak, nejdřív umožní repliky přijímat zápisy pomocí funkce zastavení replikace. Aplikace můžete přesměrovat pak stačí aktualizovat připojovací řetězec. Další informace najdete v [zastavení replikace](#stop-replication) oddílu.

## <a name="create-a-replica"></a>Vytvoření repliky

Pokud hlavní server nemá žádné existující servery repliky, hlavní restartuje nejprve připraví pro replikaci.

Při spuštění pracovního postupu vytvoření repliky se vytvoří prázdná databáze Azure pro MariaDB server. Nový server je vyplněna data, která byla na hlavní server. Čas vytvoření závisí na množství dat na hlavní server a doba od poslední týdenní úplnou zálohu. Čas musí být v rozsahu několika minut i několik hodin.

> [!NOTE]
> Pokud nemáte sadu výstrah úložiště na serverech, doporučujeme vám to. Upozornění vás informuje, když na serveru se blíží limitu úložiště, který bude mít vliv na replikaci.

Zjistěte, jak [vytvoření repliky pro čtení na webu Azure Portal](howto-read-replicas-portal.md).

## <a name="connect-to-a-replica"></a>Připojení na repliku

Při vytváření repliky nedědí pravidla brány firewall nebo koncový bod služby virtuální sítě hlavního serveru. Tato pravidla musí být nezávisle nastavené pro repliku.

Replika dědí z hlavního serveru účet správce. Všechny uživatelské účty na hlavním serveru se replikují do repliky pro čtení. Pouze pro čtení repliky můžete připojit pomocí uživatelské účty, které jsou dostupné na hlavním serveru.

Můžete připojit k replice pomocí jeho názvu hostitele a platný uživatelský účet, jako byste to zvládli regulární Azure Database pro MariaDB server. Pro server s názvem **myreplica** s uživatelským jménem správce **myadmin**, můžete se připojíte k replice s použitím rozhraní příkazového řádku mysql:

```bash
mysql -h myreplica.mariadb.database.azure.com -u myadmin@myreplica -p
```

Do příkazového řádku zadejte heslo pro uživatelský účet.

## <a name="monitor-replication"></a>Monitorování replikace

Azure Database pro MariaDB poskytuje **zpoždění replikace během několika sekund** metriky ve službě Azure Monitor. Tato metrika je k dispozici pro pouze repliky.

Tato metrika se počítá pomocí `seconds_behind_master` metriky, které jsou k dispozici ve společnosti MariaDB `SHOW SLAVE STATUS` příkazu.

Nastavení upozornění pro informování, když je zpoždění replikace dosáhne hodnotu, která se nedají použít u svých úloh.

## <a name="stop-replication"></a>Zastavení replikace

Můžete zastavit replikaci mezi hlavní a repliku. Po zastavení replikace mezi hlavní server a repliky pro čtení, se stane replika samostatný server. Data v samostatném serveru jsou data, která byla k dispozici na replice v době, kdy byl spuštěn příkaz stop replikace. Samostatný server nebude dohnat s hlavním serverem.

Pokud budete chtít zastavit replikaci na repliku, ztratí všechny odkazy na jeho předchozí hlavní větev a ostatními replikami. Není k dispozici žádné automatické převzetí služeb při selhání mezi hlavní a její repliky.

> [!IMPORTANT]
> Samostatný server nelze je převést na repliku znovu.
> Před zastavením replikace na čtení replik, zajistěte, aby že replika bude mít veškerá data, které požadujete.

Zjistěte, jak [zastavit replikaci replik](howto-read-replicas-portal.md).

## <a name="considerations-and-limitations"></a>Požadavky a omezení

### <a name="pricing-tiers"></a>Cenové úrovně

Repliky pro čtení jsou aktuálně dostupné jenom v cenové úrovně pro obecné účely a optimalizované pro paměť.

### <a name="master-server-restart"></a>Hlavní server restartovat

Při vytváření repliky pro hlavní server, který nemá žádnou existující repliku hlavní restartuje nejprve připraví pro replikaci. Vzít v úvahu a provádění těchto operací během období mimo špičku.

### <a name="new-replicas"></a>Nové repliky

Čtení replika se vytváří jako novou službu Azure Database pro MariaDB server. Existující server nelze nastavit do repliky. Nejde vytvořit replika jiné repliky pro čtení.

### <a name="replica-configuration"></a>Konfigurace repliky

Replika je vytvořen pomocí stejné konfigurace serveru na hlavní server. Po vytvoření repliky několik nastavení lze změnit nezávisle z hlavního serveru: compute generace virtuálních jader, úložiště, období uchování zálohy a MariaDB verze modulu. Cenovou úroveň můžete změnit také nezávisle na sobě, s výjimkou do nebo z úrovně Basic.

> [!IMPORTANT]
> Před hlavním serverem služby konfigurace se aktualizuje na nové hodnoty, aktualizujte konfiguraci repliky na stejné nebo vyšší hodnoty. Tato akce zajistí, že replika je dokáže dodat se změnami provedenými na hlavní server.

### <a name="stopped-replicas"></a>Zastavené repliky

Chcete-li zrušit replikace mezi serverem a hlavním serverem repliky pro čtení, zastavené replika přestane být samostatný server, který přijímá operace čtení i zápisu. Samostatný server nelze je převést na repliku znovu.

### <a name="deleted-master-and-standalone-servers"></a>Odstraněné hlavní větev a samostatné servery

Při odstranění je hlavní server, se zastaví replikace na všechny repliky pro čtení. Tyto repliky se stanou samostatné servery. Hlavní server sám se odstraní.

### <a name="user-accounts"></a>Uživatelské účty

Uživatelé na hlavním serveru se replikují do repliky pro čtení. Můžete připojit jenom pro čtení replikou s použitím uživatelské účty, které jsou dostupné na hlavním serveru.

### <a name="server-parameters"></a>Parametry serveru

Chcete-li chránit data před stále synchronizované a vyhnuli potenciální ztráty nebo poškození dat, některé parametry serveru jsou zamknuté aktualizovány, pokud pomocí repliky pro čtení.

Následující parametry serveru, jsou uzamčené na hlavní server i repliky serveru:
- [`innodb_file_per_table`](https://mariadb.com/kb/en/library/innodb-system-variables/#innodb_file_per_table) 
- [`log_bin_trust_function_creators`](https://mariadb.com/kb/en/library/replication-and-binary-log-system-variables/#log_bin_trust_function_creators)

[ `event_scheduler` ](https://mariadb.com/kb/en/library/server-system-variables/#event_scheduler) Parametr je uzamčen na serverech repliky.

### <a name="other"></a>Ostatní

- Vytváří se replika repliky se nepodporuje.
- Tabulky v paměti může způsobit, že repliky přestane být synchronní. Jedná se omezení MariaDB technologie replikace.
- Ujistěte se, že se hlavní server tabulky primární klíče mají. Latence replikace mezi hlavním virtuálním počítači repliky a může znamenat chybějící primární klíče.

## <a name="next-steps"></a>Další postup

- Zjistěte, jak [vytvářet a spravovat další repliky pomocí webu Azure portal](howto-read-replicas-portal.md)
