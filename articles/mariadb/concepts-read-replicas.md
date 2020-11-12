---
title: Čtení replik – Azure Database for MariaDB
description: 'Přečtěte si o replikách pro čtení v Azure Database for MariaDB: výběr oblastí, vytváření replik, připojení k replikám, monitorování replikace a zastavení replikace.'
author: savjani
ms.author: pariks
ms.service: mariadb
ms.topic: conceptual
ms.date: 10/15/2020
ms.openlocfilehash: b2dbaa932c01c96582cb038143fa7686707be67d
ms.sourcegitcommit: 6ab718e1be2767db2605eeebe974ee9e2c07022b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/12/2020
ms.locfileid: "94541160"
---
# <a name="read-replicas-in-azure-database-for-mariadb"></a>Repliky pro čtení ve službě Azure Database for MariaDB

Funkce repliky pro čtení umožňuje replikovat data ze serveru Azure Database for MariaDB na server jen pro čtení. Ze zdrojového serveru je můžete replikovat až na pět replik. Repliky se asynchronně aktualizují pomocí technologie replikace v binárním protokolu (binlog) modulu MariaDB s ID globálního transakce (GTID). Další informace o replikaci binlog najdete v tématu [Přehled replikace binlog](https://mariadb.com/kb/en/library/replication-overview/).

Repliky jsou nové servery, které spravujete podobně jako běžné Azure Database for MariaDB servery. Pro každou repliku čtení se vám bude účtovat zajištěné výpočetní prostředky v virtuální jádra a úložišti v GB/měsíc.

Další informace o replikaci GTID najdete v [dokumentaci k replikaci MariaDB](https://mariadb.com/kb/en/library/gtid/).

> [!NOTE]
> Komunikace bez posunu
>
> Microsoft podporuje různé a zahrnuté prostředí. Tento článek obsahuje odkazy na _podřízený_ text. [Průvodce stylem Microsoft pro komunikaci bez předplatných](https://github.com/MicrosoftDocs/microsoft-style-guide/blob/master/styleguide/bias-free-communication.md) se tímto způsobem rozpoznává jako vyloučené slovo. Toto slovo se v tomto článku používá kvůli konzistenci, protože je aktuálně slovo, které se zobrazuje v softwaru. Když se software aktualizuje, aby se odebralo slovo, aktualizuje se tento článek na zarovnání.
>

## <a name="when-to-use-a-read-replica"></a>Kdy použít repliku čtení

Funkce replika čtení pomáhá zlepšit výkon a škálu úloh náročných na čtení. Úlohy spočívající ve čtení je možné oddělit do replik a úlohy spočívající v zápisu budou směrované do hlavní databáze.

Běžným scénářem je, aby úlohy BI a analýzy používaly jako zdroj dat pro vytváření sestav repliku pro čtení.

Vzhledem k tomu, že repliky jsou jen pro čtení, nesnižují přímo na hlavní úrovni zátěže s kapacitou pro zápis. Tato funkce není určená pro úlohy, které jsou náročné na zápis.

Funkce replika čtení používá asynchronní replikaci. Tato funkce není určena pro scénáře synchronní replikace. Mezi zdrojem a replikou bude měřitelná prodleva. Data v replice nakonec budou konzistentní s daty v hlavní databázi. Tato funkce se používá pro úlohy, které můžou toto zpoždění obsloužit.

## <a name="cross-region-replication"></a>Replikace mezi oblastmi
Repliku pro čtení můžete vytvořit v jiné oblasti ze zdrojového serveru. Replikace mezi oblastmi může být užitečná pro scénáře, jako je plánování zotavení po havárii, nebo pro uživatele přiblížit data.

Zdrojový server můžete mít v libovolné [Azure Database for MariaDB oblasti](https://azure.microsoft.com/global-infrastructure/services/?products=mariadb).  Zdrojový server může mít repliku ve své spárované oblasti nebo oblastech univerzální repliky. Následující obrázek ukazuje, které oblasti repliky jsou k dispozici v závislosti na zdrojové oblasti.

[![Čtení oblastí repliky](media/concepts-read-replica/read-replica-regions.png)](media/concepts-read-replica/read-replica-regions.png#lightbox)

### <a name="universal-replica-regions"></a>Oblasti univerzální repliky
Repliku pro čtení můžete vytvořit v některé z následujících oblastí bez ohledu na to, kde se nachází zdrojový server. Mezi podporované oblasti univerzální repliky patří:

Austrálie – východ, Austrálie – jihovýchod, Brazílie – jih, Kanada – střed, Kanada – východ, Střed USA, Východní Asie, Východní USA, Východní USA 2, Japonsko – východ, Japonsko – západ, Jižní Korea, Korea – jih, střed USA – sever, Severní Evropa, střed USA – jih, jihovýchodní Asie, Velká Británie – jih, Velká Británie – západ, Západní Evropa, Západní USA, západní USA 2 a Středozápadní USA.

### <a name="paired-regions"></a>Spárované oblasti
Kromě oblastí univerzální repliky můžete vytvořit repliku pro čtení v oblasti párování Azure na vašem zdrojovém serveru. Pokud neznáte pár vaší oblasti, můžete získat další informace v [článku spárované oblasti Azure](../best-practices-availability-paired-regions.md).

Pokud používáte repliky mezi jednotlivými oblastmi pro plánování zotavení po havárii, doporučujeme vytvořit repliku v spárované oblasti namísto jedné z ostatních oblastí. Spárované oblasti zabraňují souběžným aktualizacím a přiřazují fyzickou izolaci a zasídlí dat.  

Je však třeba vzít v úvahu omezení: 

* Oblast dostupnosti: Azure Database for MariaDB je k dispozici ve Francii – střed, Spojené arabské emiráty Severní a Německo – střed. Nicméně jejich spárované oblasti nejsou k dispozici.
    
* Jednosměrné páry: některé oblasti Azure jsou spárovány pouze v jednom směru. Mezi tyto oblasti patří Západní Indie, Brazílie – jih a US Gov – Virginie. 
   To znamená, že zdrojový server v Západní Indie může vytvořit repliku v Jižní Indie. Zdrojový server v Jižní Indie ale nemůže vytvořit repliku v Západní Indie. Důvodem je to, že sekundární oblast Západní Indie je Jižní Indie, ale sekundární oblast Jižní Indie není Západní Indie.

## <a name="create-a-replica"></a>Vytvoření repliky

> [!IMPORTANT]
> Funkce replika čtení je k dispozici pouze pro Azure Database for MariaDB servery v cenové úrovni optimalizované pro Pro obecné účely nebo paměť. Ujistěte se, že je zdrojový server v jedné z těchto cenových úrovní.

Pokud na zdrojovém serveru nejsou žádné existující servery repliky, zdroj se nejdřív restartuje a připraví se pro replikaci.

Když spustíte pracovní postup vytvoření repliky, vytvoří se prázdný Azure Database for MariaDB Server. Nový server je vyplněn daty, která byla na zdrojovém serveru. Čas vytvoření závisí na množství dat ve zdroji a času od posledního týdenního úplného zálohování. Čas může být v rozsahu od několika minut až po několik hodin.

> [!NOTE]
> Pokud na svých serverech nemáte nastavené upozornění na úložiště, doporučujeme, abyste to provedli. Výstraha vás informuje, když se server blíží svému limitu úložiště, což bude mít vliv na replikaci.

Naučte se [vytvořit repliku pro čtení v Azure Portal](howto-read-replicas-portal.md).

## <a name="connect-to-a-replica"></a>Připojení k replice

Při vytváření repliky zdědí pravidla brány firewall zdrojového serveru. Tato pravidla jsou následně nezávislá na zdrojovém serveru.

Replika dědí účet správce ze zdrojového serveru. Všechny uživatelské účty na zdrojovém serveru se replikují do replik pro čtení. K replice pro čtení se můžete připojit pouze pomocí uživatelských účtů, které jsou k dispozici na zdrojovém serveru.

K replice se můžete připojit pomocí jejího názvu hostitele a platného uživatelského účtu, stejně jako při běžném Azure Database for MariaDBm serveru. Pro server s názvem **myreplica** s uživatelským jménem správce **myadmin** se můžete připojit k replice pomocí rozhraní příkazového řádku MySQL:

```bash
mysql -h myreplica.mariadb.database.azure.com -u myadmin@myreplica -p
```

Na příkazovém řádku zadejte heslo pro uživatelský účet.

## <a name="monitor-replication"></a>Monitorování replikace

Azure Database for MariaDB poskytuje metriku **prodlevy replikace v sekundách** v Azure monitor. Tato metrika je k dispozici pouze pro repliky.

Tato metrika se počítá pomocí `seconds_behind_master` metriky dostupné v `SHOW SLAVE STATUS` příkazu MariaDB.

Nastavte výstrahu, která vás informuje, když prodleva replikace dosáhne hodnoty, která není pro vaše zatížení přijatelná.

## <a name="stop-replication"></a>Zastavení replikace

Replikaci mezi zdrojem a replikou můžete zastavit. Po zastavení replikace mezi zdrojovým serverem a replikou pro čtení se replika na samostatný server. Data na samostatném serveru jsou data, která byla v replice k dispozici v době spuštění příkazu pro zastavení replikace. Samostatný server není zachytávání na zdrojovém serveru.

Pokud se rozhodnete zastavit replikaci do repliky, ztratíte všechny odkazy na předchozí zdrojové a další repliky. Mezi zdrojem a jeho replikou nedochází k automatizovanému převzetí služeb při selhání.

> [!IMPORTANT]
> Samostatný server se nedá znovu vytvořit do repliky.
> Před zastavením replikace v replice pro čtení zajistěte, aby měla replika všechna data, která požadujete.

Přečtěte si, jak [zastavit replikaci do repliky](howto-read-replicas-portal.md).

## <a name="failover"></a>Převzetí služeb při selhání

Mezi zdrojovým serverem a serverem repliky neexistuje automatizované převzetí služeb při selhání. 

Vzhledem k tomu, že replikace je asynchronní, existuje prodleva mezi zdrojem a replikou. Velikost prodlevy může mít vliv na několik faktorů, jako je to, jak velké zatížení na zdrojovém serveru běží a latence mezi datovými centry. Ve většině případů je prodleva repliky v rozsahu od několika sekund do několika minut. Vlastní prodlevu replikace můžete sledovat pomocí *prodlevy repliky* metriky, která je k dispozici pro každou repliku. Tato metrika ukazuje čas od poslední opakované transakce. Doporučujeme, abyste zjistili, jaký je průměrný prodleva tím, že v časovém intervalu pozoruje prodlevu repliky. Můžete nastavit upozornění na prodlevu repliky, takže pokud bude mimo očekávaný rozsah, můžete provést akci.

> [!Tip]
> Pokud převzetí služeb při selhání repliky přestanou, prodleva v době odpojování repliky ze zdroje bude označovat, kolik dat se ztratilo.

Jakmile se rozhodnete, že chcete převzít služeb při selhání do repliky, 

1. Zastavení replikace do repliky<br/>
   Tento krok je nezbytný k tomu, aby server repliky mohl přijímat zápisy. V rámci tohoto procesu se server repliky odpojí z hlavního serveru. Jakmile zahájíte zastavení replikace, proces back-endu obvykle trvá přibližně 2 minuty, než se dokončí. V části [zastavení replikace](#stop-replication) v tomto článku se seznámíte s důsledky této akce.
    
2. Nasměrujte aplikaci na (bývalé) repliku.<br/>
   Každý server má jedinečný připojovací řetězec. Aktualizujte svou aplikaci tak, aby odkazovala na (bývalé) repliku místo na hlavní.
    
Po úspěšném zpracování čtení a zápisu vaší aplikace jste dokončili převzetí služeb při selhání. Množství prostojů, na kterých bude prostředí aplikace záviset při zjištění problému a dokončení kroků 1 a 2 výše.

## <a name="considerations-and-limitations"></a>Důležité informace a omezení

### <a name="pricing-tiers"></a>Cenové úrovně

Repliky čtení jsou v tuto chvíli dostupné jenom v Pro obecné účely a paměťově optimalizované cenové úrovně.

> [!NOTE]
> Náklady na spuštění serveru repliky jsou založené na oblasti, ve které je spuštěný server repliky.

### <a name="source-server-restart"></a>Restart zdrojového serveru

Když vytvoříte repliku pro zdroj, který nemá žádné existující repliky, zdroj se nejdřív restartuje, aby se připravil pro replikaci. Vezměte v úvahu a udělejte tyto operace v době mimo špičku.

### <a name="new-replicas"></a>Nové repliky

Replika pro čtení je vytvořená jako nový server Azure Database for MariaDB. Existující server nelze vytvořit do repliky. Nelze vytvořit repliku jiné repliky pro čtení.

### <a name="replica-configuration"></a>Konfigurace repliky

Replika je vytvořena pomocí stejné konfigurace serveru jako hlavní. Po vytvoření repliky se dá změnit několik nastavení nezávisle na zdrojovém serveru: generování výpočetních prostředků, virtuální jádra, úložiště, doba uchování zálohy a verze stroje MariaDB. Cenová úroveň se dá změnit také nezávisle, s výjimkou nebo z úrovně Basic.

> [!IMPORTANT]
> Před aktualizací konfigurace zdrojového serveru na nové hodnoty aktualizujte konfiguraci repliky na stejné nebo vyšší hodnoty. Tato akce zajistí, že replika bude moct udržovat krok se všemi změnami na hlavním serveru.

Pravidla brány firewall a nastavení parametrů se při vytvoření repliky dědí ze zdrojového serveru do repliky. Pak jsou pravidla repliky nezávislá.

### <a name="stopped-replicas"></a>Zastavené repliky

Pokud zastavíte replikaci mezi zdrojovým serverem a replikou pro čtení, zastavená replika se stane samostatným serverem, který přijímá čtení i zápis. Samostatný server se nedá znovu vytvořit do repliky.

### <a name="deleted-source-and-standalone-servers"></a>Odstraněné zdrojové a samostatné servery

Po odstranění zdrojového serveru se replikace zastaví na všechny repliky čtení. Tyto repliky se automaticky změní na samostatné servery a můžou přijímat operace čtení i zápisu. Samotný zdrojový server se odstraní.

### <a name="user-accounts"></a>Uživatelské účty

Uživatelé na zdrojovém serveru se replikují do replik pro čtení. K replice pro čtení se můžete připojit pouze pomocí uživatelských účtů, které jsou k dispozici na zdrojovém serveru.

### <a name="server-parameters"></a>Parametry serveru

Aby se při použití replik pro čtení zabránilo přerušení synchronizace dat a možné ztrátě nebo poškození dat, některé parametry serveru neumožňují aktualizaci.

Následující parametry serveru jsou uzamčené na zdrojovém serveru i na serverech repliky:
- [`innodb_file_per_table`](https://mariadb.com/kb/en/library/innodb-system-variables/#innodb_file_per_table) 
- [`log_bin_trust_function_creators`](https://mariadb.com/kb/en/library/replication-and-binary-log-system-variables/#log_bin_trust_function_creators)

[`event_scheduler`](https://mariadb.com/kb/en/library/server-system-variables/#event_scheduler)Parametr je uzamčen na serverech repliky.

Pokud chcete na zdrojovém serveru aktualizovat jeden z výše uvedených parametrů, odstraňte prosím servery repliky, aktualizujte hodnotu parametru v hlavní větvi a znovu vytvořte repliky.

### <a name="other"></a>Další

- Vytvoření repliky repliky není podporováno.
- Tabulky v paměti můžou způsobit, že se repliky nesynchronizují. Toto je omezení technologie MariaDB pro replikaci.
- Zajistěte, aby tabulky zdrojového serveru měly primární klíče. Nedostatek primárních klíčů může způsobit latenci replikace mezi zdrojem a replikami.

## <a name="next-steps"></a>Další kroky

- Naučte se [vytvářet a spravovat repliky pro čtení pomocí Azure Portal](howto-read-replicas-portal.md)
- Naučte se [vytvářet a spravovat repliky pro čtení pomocí Azure CLI a REST API](howto-read-replicas-cli.md)
