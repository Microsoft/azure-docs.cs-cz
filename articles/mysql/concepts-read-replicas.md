---
title: Čtení replik – Azure Database for MySQL
description: 'Přečtěte si o replikách pro čtení v Azure Database for MySQL: výběr oblastí, vytváření replik, připojení k replikám, monitorování replikace a zastavení replikace.'
author: savjani
ms.author: pariks
ms.service: mysql
ms.topic: conceptual
ms.date: 01/13/2021
ms.custom: references_regions
ms.openlocfilehash: c380a3edb556adb72d067cb2910c8afbf66b99a0
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2021
ms.locfileid: "98250260"
---
# <a name="read-replicas-in-azure-database-for-mysql"></a>Repliky pro čtení ve službě Azure Database for MySQL

Funkce repliky pro čtení umožňuje replikovat data ze serveru Azure Database for MySQL na server jen pro čtení. Ze zdrojového serveru je můžete replikovat až na pět replik. Repliky se aktualizují asynchronně s využitím technologie replikace na základě pozice v souboru binárního protokolu (binlog) nativní pro stroj MySQL. Další informace o replikaci binlog najdete v tématu [Přehled replikace MySQL binlog](https://dev.mysql.com/doc/refman/5.7/en/binlog-replication-configuration-overview.html).

Repliky jsou nové servery, které spravujete podobně jako běžné Azure Database for MySQL servery. Pro každou repliku čtení se vám bude účtovat zajištěné výpočetní prostředky v virtuální jádra a úložišti v GB/měsíc.

Další informace o funkcích a problémech replikace MySQL najdete v [dokumentaci k replikaci MySQL](https://dev.mysql.com/doc/refman/5.7/en/replication-features.html).

> [!NOTE]
> Tento článek obsahuje odkazy na _podřízený_ termín, termín, který už Microsoft nepoužívá. Po odebrání termínu ze softwaru ho odebereme z tohoto článku.
>

## <a name="when-to-use-a-read-replica"></a>Kdy použít repliku čtení

Funkce replika čtení pomáhá zlepšit výkon a škálu úloh náročných na čtení. Úlohy čtení se dají pro repliky izolovat, zatímco úlohy zápisu můžou být směrované na zdroj.

Běžným scénářem je, aby úlohy BI a analýzy používaly jako zdroj dat pro vytváření sestav repliku pro čtení.

Vzhledem k tomu, že repliky jsou jen pro čtení, nesnižují na zdroj přímo zátěž kapacity pro zápis. Tato funkce není určená pro úlohy, které jsou náročné na zápis.

Funkce replika čtení používá asynchronní replikaci MySQL. Tato funkce není určena pro scénáře synchronní replikace. Mezi zdrojem a replikou bude měřitelná prodleva. Data v replice nakonec budou konzistentní s daty ve zdroji. Tato funkce se používá pro úlohy, které můžou toto zpoždění obsloužit.

## <a name="cross-region-replication"></a>Replikace mezi oblastmi

Repliku pro čtení můžete vytvořit v jiné oblasti ze zdrojového serveru. Replikace mezi oblastmi může být užitečná pro scénáře, jako je plánování zotavení po havárii, nebo pro uživatele přiblížit data.

Zdrojový server můžete mít v libovolné [Azure Database for MySQL oblasti](https://azure.microsoft.com/global-infrastructure/services/?products=mysql).  Zdrojový server může mít repliku ve své spárované oblasti nebo oblastech univerzální repliky. Následující obrázek ukazuje, které oblasti repliky jsou k dispozici v závislosti na zdrojové oblasti.

[:::image type="content" source="media/concepts-read-replica/read-replica-regions.png" alt-text="Čtení oblastí repliky":::](media/concepts-read-replica/read-replica-regions.png#lightbox)

### <a name="universal-replica-regions"></a>Oblasti univerzální repliky

Repliku pro čtení můžete vytvořit v některé z následujících oblastí bez ohledu na to, kde se nachází zdrojový server. Mezi podporované oblasti univerzální repliky patří:

Austrálie – východ, Austrálie – jihovýchod, Brazílie – jih, Kanada – střed, Kanada – východ, Střed USA, Východní Asie, Východní USA, Východní USA 2, Japonsko – východ, Japonsko – západ, Jižní Korea, Korea – jih, střed USA – sever, Severní Evropa, střed USA – jih, jihovýchodní Asie, Velká Británie – jih, Velká Británie – západ, Západní Evropa, Západní USA, západní USA 2 a Středozápadní USA.

### <a name="paired-regions"></a>Spárované oblasti

Kromě oblastí univerzální repliky můžete vytvořit repliku pro čtení v oblasti párování Azure na vašem zdrojovém serveru. Pokud neznáte pár vaší oblasti, můžete získat další informace v [článku spárované oblasti Azure](../best-practices-availability-paired-regions.md).

Pokud používáte repliky mezi jednotlivými oblastmi pro plánování zotavení po havárii, doporučujeme vytvořit repliku v spárované oblasti namísto jedné z ostatních oblastí. Spárované oblasti zabraňují souběžným aktualizacím a přiřazují fyzickou izolaci a zasídlí dat.  

Je však třeba vzít v úvahu omezení: 

* Oblast dostupnosti: Azure Database for MySQL je k dispozici ve Francii – střed, Spojené arabské emiráty Severní a Německo – střed. Nicméně spárované oblasti nejsou k dispozici.

* Jednosměrné páry: některé oblasti Azure jsou spárovány pouze v jednom směru. Mezi tyto oblasti patří Západní Indie, Brazílie – jih a US Gov – Virginie.
   To znamená, že zdrojový server v Západní Indie může vytvořit repliku v Jižní Indie. Zdrojový server v Jižní Indie ale nemůže vytvořit repliku v Západní Indie. Důvodem je to, že sekundární oblast Západní Indie je Jižní Indie, ale sekundární oblast Jižní Indie není Západní Indie.

## <a name="create-a-replica"></a>Vytvoření repliky

> [!IMPORTANT]
> Funkce replika čtení je k dispozici pouze pro Azure Database for MySQL servery v cenové úrovni optimalizované pro Pro obecné účely nebo paměť. Ujistěte se, že je zdrojový server v jedné z těchto cenových úrovní.

Pokud na zdrojovém serveru nejsou žádné existující servery repliky, zdroj se nejdřív restartuje a připraví se pro replikaci.

Když spustíte pracovní postup vytvoření repliky, vytvoří se prázdný Azure Database for MySQL server. Nový server je vyplněn daty, která byla na zdrojovém serveru. Čas vytvoření závisí na množství dat ve zdroji a času od posledního týdenního úplného zálohování. Čas může být v rozsahu od několika minut až po několik hodin. Server repliky se vždycky vytvoří ve stejné skupině prostředků a v rámci stejného předplatného jako na zdrojovém serveru. Pokud chcete vytvořit server repliky pro jinou skupinu prostředků nebo jiné předplatné, můžete [server repliky](../azure-resource-manager/management/move-resource-group-and-subscription.md) po vytvoření přesunout.

U každé repliky je povoleno [Automatické zvětšování](concepts-pricing-tiers.md#storage-auto-grow)úložiště. Funkce automatického zvětšení umožňuje replice udržovat data, která jsou do ní replikována, a zabránit přerušení replikace v důsledku chyb způsobených nedostatkem úložiště.

Naučte se [vytvořit repliku pro čtení v Azure Portal](howto-read-replicas-portal.md).

## <a name="connect-to-a-replica"></a>Připojení k replice

Při vytváření repliky zdědí pravidla brány firewall zdrojového serveru. Tato pravidla jsou následně nezávislá na zdrojovém serveru.

Replika dědí účet správce ze zdrojového serveru. Všechny uživatelské účty na zdrojovém serveru se replikují do replik pro čtení. K replice pro čtení se můžete připojit pouze pomocí uživatelských účtů, které jsou k dispozici na zdrojovém serveru.

K replice se můžete připojit pomocí jejího názvu hostitele a platného uživatelského účtu, stejně jako při běžném Azure Database for MySQLm serveru. Pro server s názvem **myreplica** s uživatelským jménem správce **myadmin** se můžete připojit k replice pomocí rozhraní příkazového řádku MySQL:

```bash
mysql -h myreplica.mysql.database.azure.com -u myadmin@myreplica -p
```

Na příkazovém řádku zadejte heslo pro uživatelský účet.

## <a name="monitor-replication"></a>Monitorování replikace

Azure Database for MySQL poskytuje metriku **prodlevy replikace v sekundách** v Azure monitor. Tato metrika je k dispozici pouze pro repliky. Tato metrika se počítá pomocí `seconds_behind_master` metriky dostupné v `SHOW SLAVE STATUS` příkazu MySQL. Nastavte výstrahu, která vás informuje, když prodleva replikace dosáhne hodnoty, která není pro vaše zatížení přijatelná.

Pokud se zobrazí zvýšené zpoždění replikace, přečtěte si téma [řešení potíží s latencí replikace](howto-troubleshoot-replication-latency.md) k řešení potíží a pochopení možných příčin.

## <a name="stop-replication"></a>Zastavení replikace

Replikaci mezi zdrojem a replikou můžete zastavit. Po zastavení replikace mezi zdrojovým serverem a replikou pro čtení se replika na samostatný server. Data na samostatném serveru jsou data, která byla v replice k dispozici v době spuštění příkazu pro zastavení replikace. Samostatný server není zachytávání na zdrojovém serveru.

Pokud se rozhodnete zastavit replikaci do repliky, ztratíte všechny odkazy na předchozí zdrojové a další repliky. Mezi zdrojem a jeho replikou nedochází k automatizovanému převzetí služeb při selhání.

> [!IMPORTANT]
> Samostatný server se nedá znovu vytvořit do repliky.
> Před zastavením replikace v replice pro čtení zajistěte, aby měla replika všechna data, která požadujete.

Přečtěte si, jak [zastavit replikaci do repliky](howto-read-replicas-portal.md).

## <a name="failover"></a>Převzetí služeb při selhání

Mezi zdrojovým serverem a serverem repliky nedochází k automatizovanému převzetí služeb při selhání.

Vzhledem k tomu, že replikace je asynchronní, existuje prodleva mezi zdrojem a replikou. Velikost prodlevy může mít vliv na mnoho faktorů, jako je to, jak velké zatížení na zdrojovém serveru běží, a latence mezi datovými centry. Ve většině případů je prodleva repliky v rozsahu od několika sekund do několika minut. Vlastní prodlevu replikace můžete sledovat pomocí *prodlevy repliky* metriky, která je k dispozici pro každou repliku. Tato metrika ukazuje čas od poslední opakované transakce. Doporučujeme, abyste zjistili, jaký je průměrný prodleva tím, že v časovém intervalu pozoruje prodlevu repliky. Můžete nastavit upozornění na prodlevu repliky, takže pokud bude mimo očekávaný rozsah, můžete provést akci.

> [!Tip]
> Pokud převzetí služeb při selhání repliky přestanou, prodleva v době odpojování repliky ze zdroje bude označovat, kolik dat se ztratilo.

Až se rozhodnete, že chcete převzít služeb při selhání do repliky:

1. Zastavení replikace do repliky<br/>
   Tento krok je nezbytný k tomu, aby server repliky mohl přijímat zápisy. V rámci tohoto procesu se server repliky odpojí ze zdroje. Po zahájení zastavení replikace bude proces back-endu obvykle trvat přibližně 2 minuty, než se dokončí. V části [zastavení replikace](#stop-replication) v tomto článku se seznámíte s důsledky této akce.

2. Nasměrujte aplikaci na (bývalé) repliku.<br/>
   Každý server má jedinečný připojovací řetězec. Aktualizujte svou aplikaci tak, aby odkazovala na (bývalé) repliku místo zdroje.

Po úspěšném zpracování čtení a zápisu vaší aplikace jste dokončili převzetí služeb při selhání. Množství prostojů, na kterých bude prostředí aplikace záviset při zjištění problému a dokončení kroků 1 a 2 uvedených výše.

## <a name="global-transaction-identifier-gtid"></a>Identifikátor globální transakce (GTID)

Identifikátor globální transakce (GTID) je jedinečný identifikátor vytvořený s každou potvrzenou transakcí na zdrojovém serveru a ve výchozím nastavení je ve Azure Database for MySQL. GTID se podporuje ve verzích 5,7 a 8,0 a jenom na serverech, které podporují úložiště až na 16 TB. Další informace o GTID a o tom, jak se používá v replikaci, najdete v dokumentaci k [replikaci MySQL s GTID](https://dev.mysql.com/doc/refman/5.7/en/replication-gtids.html) .

MySQL podporuje dva typy transakcí: transakce GTID (identifikované s GTID) a anonymní transakce (nemáte přidělený GTID).

Pro konfiguraci GTID jsou k dispozici následující parametry serveru: 

|**Parametr serveru**|**Popis**|**Výchozí hodnota**|**Hodnoty**|
|--|--|--|--|
|`gtid_mode`|Určuje, zda se k identifikaci transakcí používají GTIDs. Změny mezi režimy lze provést pouze v jednom kroku ve vzestupném pořadí (např. `OFF` -> `OFF_PERMISSIVE` -> `ON_PERMISSIVE` -> `ON`)|`OFF`|`OFF`: Transakce nové a replikační replikace musí být anonymní. <br> `OFF_PERMISSIVE`: Nové transakce jsou anonymní. Replikované transakce můžou být buď anonymní, nebo GTID transakce. <br> `ON_PERMISSIVE`: Nové transakce jsou GTID transakcemi. Replikované transakce můžou být buď anonymní, nebo GTID transakce. <br> `ON`: Nové i replikované transakce musí být GTID transakcemi.|
|`enforce_gtid_consistency`|Vyhodnotí konzistenci GTID tím, že umožňuje provádění pouze těch příkazů, které mohou být zaprotokolovány prostřednictvím bezpečnostních opatření. Aby bylo možné `ON` Povolit replikaci GTID, musí být tato hodnota nastavená na hodnotu. |`OFF`|`OFF`: Všechny transakce můžou porušovat GTID konzistenci.  <br> `ON`: Žádná transakce nemůže narušovat GTID konzistenci. <br> `WARN`: Všechny transakce můžou porušovat konzistenci GTID, ale vygeneruje se upozornění. | 

> [!NOTE]
> Po povolení GTID ho nelze znovu zapnout. Pokud potřebujete GTID vypnout, obraťte se prosím na podporu. 

Pokud chcete povolit GTID a nakonfigurovat chování konzistence, aktualizujte `gtid_mode` `enforce_gtid_consistency` parametry serveru a pomocí [Azure Portal](howto-server-parameters.md), [Azure CLI](howto-configure-server-parameters-using-cli.md)nebo [PowerShellu](howto-configure-server-parameters-using-powershell.md).

Pokud je na zdrojovém serveru povolená možnost GTID ( `gtid_mode` = on), nově vytvořené repliky budou taky mít povolený GTID a budou používat replikaci GTID. Pokud chcete zachovat konzistenci replikace, nemůžete aktualizovat `gtid_mode` na zdrojovém nebo replikačním serveru.

## <a name="considerations-and-limitations"></a>Důležité informace a omezení

### <a name="pricing-tiers"></a>Cenové úrovně

Repliky čtení jsou v tuto chvíli dostupné jenom v Pro obecné účely a paměťově optimalizované cenové úrovně.

> [!NOTE]
> Náklady na spuštění serveru repliky jsou založené na oblasti, ve které je spuštěný server repliky.

### <a name="source-server-restart"></a>Restart zdrojového serveru

Když vytvoříte repliku pro zdroj, který nemá žádné existující repliky, zdroj se nejdřív restartuje, aby se připravil pro replikaci. Vezměte v úvahu a udělejte tyto operace v době mimo špičku.

### <a name="new-replicas"></a>Nové repliky

Replika pro čtení je vytvořená jako nový server Azure Database for MySQL. Existující server nelze vytvořit do repliky. Nelze vytvořit repliku jiné repliky pro čtení.

### <a name="replica-configuration"></a>Konfigurace repliky

Replika je vytvořena pomocí stejné konfigurace serveru jako zdroj. Po vytvoření repliky se dá změnit několik nastavení nezávisle na zdrojovém serveru: generování výpočetních prostředků, virtuální jádra, úložiště a doba uchovávání záloh. Cenová úroveň se dá změnit také nezávisle, s výjimkou nebo z úrovně Basic.

> [!IMPORTANT]
> Před aktualizací konfigurace zdrojového serveru na nové hodnoty aktualizujte konfiguraci repliky na stejné nebo vyšší hodnoty. Tato akce zajistí, že replika bude moct udržovat krok se všemi změnami na zdrojovém serveru.

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

* [`innodb_file_per_table`](https://dev.mysql.com/doc/refman/8.0/en/innodb-file-per-table-tablespaces.html) 
* [`log_bin_trust_function_creators`](https://dev.mysql.com/doc/refman/5.7/en/replication-options-binary-log.html#sysvar_log_bin_trust_function_creators)

[`event_scheduler`](https://dev.mysql.com/doc/refman/5.7/en/server-system-variables.html#sysvar_event_scheduler)Parametr je uzamčen na serverech repliky.

Pokud chcete na zdrojovém serveru aktualizovat jeden z výše uvedených parametrů, odstraňte servery repliky, aktualizujte hodnotu parametru ve zdroji a znovu vytvořte repliky.

### <a name="gtid"></a>GTID

GTID se podporuje na:

* Verze MySQL 5,7 a 8,0.
* Servery, které podporují úložiště až na 16 TB. Úplný seznam oblastí, které podporují 16 TB úložiště, najdete v článku o [cenové úrovni](concepts-pricing-tiers.md#storage) .

GTID je ve výchozím nastavení VYPNUTá. Po povolení GTID ho nemůžete znovu zapnout. Pokud potřebujete GTID vypnout, obraťte se na podporu.

Pokud je na zdrojovém serveru povolená možnost GTID, budou mít nově vytvořené repliky taky povolený GTID a budou používat replikaci GTID. Pokud chcete zachovat konzistenci replikace, nemůžete aktualizovat `gtid_mode` na zdrojovém nebo replikačním serveru.

### <a name="other"></a>Jiné

* Vytvoření repliky repliky se nepodporuje.
* Tabulky v paměti můžou způsobit, že se repliky nesynchronizují. Toto je omezení technologie replikace MySQL. Další informace najdete v [referenční dokumentaci k MySQL](https://dev.mysql.com/doc/refman/5.7/en/replication-features-memory.html) .
* Zajistěte, aby tabulky zdrojového serveru měly primární klíče. Nedostatek primárních klíčů může způsobit latenci replikace mezi zdrojem a replikami.
* Úplný seznam omezení replikace MySQL najdete v [dokumentaci k MySQL](https://dev.mysql.com/doc/refman/5.7/en/replication-features.html) .

## <a name="next-steps"></a>Další kroky

* Naučte se [vytvářet a spravovat repliky pro čtení pomocí Azure Portal](howto-read-replicas-portal.md)
* Naučte se [vytvářet a spravovat repliky pro čtení pomocí Azure CLI a REST API](howto-read-replicas-cli.md)