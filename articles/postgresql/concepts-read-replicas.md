---
title: Čtení replik – Azure Database for PostgreSQL – jeden server
description: Tento článek popisuje funkci pro čtení repliky na serveru Azure Database for PostgreSQL-Single.
author: sr-msft
ms.author: srranga
ms.service: postgresql
ms.topic: conceptual
ms.date: 11/05/2020
ms.openlocfilehash: 8fabf8169270c3162604b6535a6cf2fb07cd9a9d
ms.sourcegitcommit: 7cc10b9c3c12c97a2903d01293e42e442f8ac751
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/06/2020
ms.locfileid: "93422140"
---
# <a name="read-replicas-in-azure-database-for-postgresql---single-server"></a>Čtení replik v Azure Database for PostgreSQL – jeden server

Funkce replika čtení umožňuje replikovat data z Azure Database for PostgreSQL serveru do serveru jen pro čtení. Repliky se **asynchronně** aktualizují s využitím nativní fyzické replikační technologie modulu PostgreSQL. Replikaci můžete provést z primárního serveru až do pěti replik.

Repliky jsou nové servery, které spravujete podobně jako běžné servery Azure Database for PostgreSQL. Pro každou repliku čtení se vám bude účtovat zajištěné výpočetní prostředky v virtuální jádra a úložišti v GB/měsíc.

Naučte se [vytvářet a spravovat repliky](howto-read-replicas-portal.md).

## <a name="when-to-use-a-read-replica"></a>Kdy použít repliku čtení
Funkce replika čtení pomáhá zlepšit výkon a škálu úloh náročných na čtení. Úlohy čtení se dají pro repliky izolovat, zatímco úlohy zápisu můžou být směrované na primární. Repliky čtení je také možné nasadit v jiné oblasti a v případě zotavení po havárii se dají zvýšit na server pro čtení i zápis.

Běžným scénářem je, aby úlohy BI a analýzy používaly jako zdroj dat pro vytváření sestav repliku pro čtení.

Vzhledem k tomu, že repliky jsou jen pro čtení, nesnižují přímo na primární úrovni zátěže s kapacitou pro zápis.

### <a name="considerations"></a>Co je potřeba vzít v úvahu
Tato funkce je určena pro scénáře, kde je prodleva přijatelná a určena pro zpracování nepřesměrování dotazů. Nejedná se o scénáře synchronní replikace, ve kterých se očekává, že jsou data repliky v aktuálním stavu. Mezi primárním serverem a replikou bude měřitelné zpoždění. Může to být v řádu minut nebo dokonce i v závislosti na zatížení a latenci mezi primárním serverem a replikou. Data v replice nakonec budou konzistentní s daty na primárním virtuálním počítači. Tato funkce se používá pro úlohy, které můžou toto zpoždění obsloužit. 

> [!NOTE]
> Pro většinu úloh, které replikují čtení, se z primární nabídky dokončí aktualizace v reálném čase. U trvalých primárních úloh náročných na zápis však může prodleva replikace pokračovat v růstu a nemusí být nikdy schopna zachytit primární. To může taky zvýšit využití úložiště na primárním, protože soubory WAL se neodstraní, dokud je neobdržíte v replice. Pokud tato situace přetrvává, odstranění a opětovné vytvoření repliky pro čtení po dokončení úloh náročných na zápis je možnost převést repliku zpátky do dobrého stavu s ohledem na prodlevu.
> Asynchronní repliky čtení nejsou vhodné pro tyto náročné úlohy zápisu. Při vyhodnocování replik pro čtení vaší aplikace Sledujte prodlevu v replice, abyste dosáhli plného cyklu zatížení v aplikaci, a to jak s časem špičky, tak bez špičky, abyste mohli získat přístup k možné prodlevě a očekávanému RTO/RPO v různých bodech cyklu úloh.
> 
## <a name="cross-region-replication"></a>Replikace mezi oblastmi
Repliku pro čtení můžete vytvořit v jiné oblasti z primárního serveru. Replikace mezi oblastmi může být užitečná pro scénáře, jako je plánování zotavení po havárii, nebo pro uživatele přiblížit data.

>[!NOTE]
> Servery základní vrstvy podporují jenom replikaci stejné oblasti.

Primární server můžete mít v libovolné [Azure Database for PostgreSQL oblasti](https://azure.microsoft.com/global-infrastructure/services/?products=postgresql). Primární server může mít repliku ve své spárované oblasti nebo oblastech univerzální repliky. Následující obrázek ukazuje, které oblasti repliky jsou k dispozici v závislosti na vaší primární oblasti.

[:::image type="content" source="media/concepts-read-replica/read-replica-regions.png" alt-text="Čtení oblastí repliky":::](media/concepts-read-replica/read-replica-regions.png#lightbox)

### <a name="universal-replica-regions"></a>Oblasti univerzální repliky
Repliku pro čtení můžete vždy vytvořit v některé z následujících oblastí bez ohledu na to, kde se nachází primární server. Jedná se o oblasti univerzální repliky:

Austrálie – východ, Austrálie – jihovýchod, Brazílie – jih, Kanada – střed, Kanada – východ, Střed USA, Východní Asie, Východní USA, Východní USA 2, Japonsko – východ, Japonsko – západ, Jižní Korea, Korea – jih, střed USA – sever, Severní Evropa, střed USA – jih, jihovýchodní Asie, Velká Británie – jih, Velká Británie – západ, Západní Evropa, Západní USA, západní USA 2 a Středozápadní USA.

### <a name="paired-regions"></a>Spárované oblasti
Kromě oblastí univerzální repliky můžete vytvořit repliku pro čtení ve spárované oblasti Azure vašeho primárního serveru. Pokud neznáte pár vaší oblasti, můžete získat další informace v [článku spárované oblasti Azure](../best-practices-availability-paired-regions.md).

Pokud používáte repliky mezi jednotlivými oblastmi pro plánování zotavení po havárii, doporučujeme vytvořit repliku v spárované oblasti namísto jedné z ostatních oblastí. Spárované oblasti zabraňují souběžným aktualizacím a přiřazují fyzickou izolaci a zasídlí dat.  

Je potřeba vzít v úvahu omezení: 

* Oblast dostupnosti: Azure Database for PostgreSQL je k dispozici ve Francii – střed, Spojené arabské emiráty Severní a Německo – střed. Nicméně jejich spárované oblasti nejsou k dispozici.
    
* Jednosměrné páry: některé oblasti Azure jsou spárovány pouze v jednom směru. Mezi tyto oblasti patří Západní Indie, Brazílie – jih. 
   To znamená, že primární server v Západní Indie může vytvořit repliku v Jižní Indie. Primární server v Jižní Indie ale nemůže vytvořit repliku v Západní Indie. Důvodem je to, že sekundární oblast Západní Indie je Jižní Indie, ale sekundární oblast Jižní Indie není Západní Indie.


## <a name="create-a-replica"></a>Vytvoření repliky
Když spustíte pracovní postup vytvoření repliky, vytvoří se prázdný Azure Database for PostgreSQL Server. Nový server je vyplněn daty, která byla na primárním serveru. Čas vytvoření závisí na množství dat na primárním a času od posledního týdenního úplného zálohování. Čas může být v rozsahu od několika minut až po několik hodin.

U každé repliky je povoleno [Automatické zvětšování](concepts-pricing-tiers.md#storage-auto-grow)úložiště. Funkce automatického zvětšení umožňuje replice udržovat data, která jsou do ní replikována, a zabránit přerušení replikace způsobené chybou úložiště.

Funkce replika čtení používá fyzickou replikaci PostgreSQL, ne logickou replikaci. Výchozím režimem operace je replikace streamování pomocí slotů replikace. V případě potřeby se k zachytávání použije přenos protokolu.

Naučte se [vytvořit repliku pro čtení v Azure Portal](howto-read-replicas-portal.md).

## <a name="connect-to-a-replica"></a>Připojení k replice
Když vytváříte repliku, nedědí pravidla firewallu ani koncový bod služby virtuální sítě primárního serveru. Tato pravidla musí být pro repliku nastavena nezávisle.

Replika dědí účet správce z primárního serveru. Všechny uživatelské účty na primárním serveru se replikují do replik pro čtení. K replice pro čtení se můžete připojit pouze pomocí uživatelských účtů, které jsou k dispozici na primárním serveru.

K replice se můžete připojit pomocí jejího názvu hostitele a platného uživatelského účtu, stejně jako při běžném Azure Database for PostgreSQLm serveru. Pro server s názvem **Moje replika** s uživatelským jménem správce **myadmin** se můžete připojit k replice pomocí psql:

```bash
psql -h myreplica.postgres.database.azure.com -U myadmin@myreplica -d postgres
```

Na příkazovém řádku zadejte heslo pro uživatelský účet.

## <a name="monitor-replication"></a>Monitorování replikace
Azure Database for PostgreSQL poskytuje dvě metriky pro monitorování replikace. Tyto dvě metriky jsou **maximální prodlevou mezi replikami** a **prodlevou repliky**. Informace o tom, jak zobrazit tyto metriky, najdete v článku **monitorování repliky** v [článku věnovaném postupu čtení repliky](howto-read-replicas-portal.md).

Metrika **maximální prodlevy napříč replikami** zobrazuje prodlevu v bajtech mezi primárním a největším zpožděním repliky. Tato metrika je dostupná a dostupná jenom na primárním serveru a bude dostupná jenom v případě, že je aspoň jedna z replik pro čtení připojená k primární lokalitě a primární je v režimu replikace streamování. Prodleva nezobrazuje podrobnosti o tom, kdy je replika v procesu zachycení s primárním použitím archivovaných protokolů primární databáze v režimu replikace souborů.

Metrika **prodlevy repliky** zobrazuje čas od poslední opakované transakce. Pokud na primárním serveru nedochází k žádným transakcím, metrika tuto časovou prodlevu odráží. Tato metrika se dá použít a je dostupná jenom pro servery repliky. Prodleva repliky je vypočítána ze `pg_stat_wal_receiver` zobrazení:

```SQL
SELECT EXTRACT (EPOCH FROM now() - pg_last_xact_replay_timestamp());
```

Nastavte výstrahu, která vás informuje, když prodleva replik dosáhne hodnoty, která není pro vaše zatížení přijatelná. 

Další informace najdete v dotazování primárního serveru přímo za účelem získání prodlevy replikace v bajtech pro všechny repliky.

> [!NOTE]
> V případě restartování primárního serveru nebo čtení repliky se čas potřebný k restartování a zachytávání projeví ve metrikě prodlevy repliky.

## <a name="stop-replication--promote-replica"></a>Zastavení replikace/zvýšení úrovně repliky
Replikaci můžete kdykoli zastavit mezi primárním serverem a replikou. Akce zastavit způsobí, že se replika restartuje a povýší repliky jako nezávislého samostatného serveru pro čtení a zápis. Data na samostatném serveru jsou data, která byla k dispozici na serveru repliky v době zastavení replikace. Jakékoli následné aktualizace na primárním umístění nejsou rozšířeny do repliky. Server repliky ale může mít nashromážděné protokoly, které se zatím nepoužily. V rámci procesu restartování před přijetím připojení klientů používá replika všechny nevyřízené protokoly.  

### <a name="considerations"></a>Co je potřeba vzít v úvahu
- Před zastavením replikace v replice pro čtení zkontrolujte prodlevu replikace, abyste zajistili, že má replika všechna data, která požadujete. 
- Vzhledem k tomu, že replika pro čtení musí použít všechny nevyřízené protokoly předtím, než bude možné vytvořit samostatný server, RTO může být vyšší pro zápis těžkých úloh, když dojde k tomu, že se replikace stane významnou prodlevou na replice. Věnujte prosím pozornost tomu, když naplánujete zvýšení úrovně repliky.
- Povýšený server repliky nelze znovu provést do repliky.
- Pokud povýšíte repliku jako primární server, nebudete moct replikaci obnovit na starý primární server. Pokud se chcete vrátit ke staré primární oblasti, můžete buď vytvořit nový server repliky s novým názvem (nebo) odstranit starý primární objekt a vytvořit repliku pomocí starého primárního názvu.
- Pokud máte více replik pro čtení, a Pokud povýšíte jeden z nich na primární server, budou další servery repliky stále připojené k staré primární službě. Je možné, že bude nutné znovu vytvořit repliky z nového, propagovaného serveru.

Při zastavení replikace ztratí replika všechny odkazy na předchozí primární a jiné repliky.

Přečtěte si, jak [zastavit replikaci do repliky](howto-read-replicas-portal.md).

## <a name="failover-to-replica"></a>Převzetí služeb při selhání do repliky

V případě selhání primárního serveru se u repliky pro čtení **neprovede** automatické převzetí služeb při selhání. 

Vzhledem k tomu, že replikace je asynchronní, může být mezi primárním serverem a replikou značná prodleva. Množství prodlevy je ovlivněno několika faktory, jako je typ úlohy spuštěné na primárním serveru a latence mezi primárním serverem a serverem repliky. V typických případech se nominální zátěží zápisu se očekává prodleva repliky během několika sekund až po několik minut. V případech, kdy primární spouští velmi velkou úlohu náročné na zápis a replika není dostatečně rychlá, může být prodleva mnohem vyšší. Prodlevu replikace pro každou repliku můžete sledovat pomocí *prodlevy repliky* metriky. Tato metrika zobrazuje čas od poslední opakované transakce v replice. Doporučujeme, abyste identifikovali průměrné prodlevy tím, že v určitém časovém intervalu budete pozorovat prodlevu repliky. Můžete nastavit upozornění na prodlevu repliky, takže pokud bude mimo očekávaný rozsah, budete upozorněni na provedení akce.

> [!Tip]
> Pokud převzetí služeb při selhání repliky přestanou, prodleva v době odpojování repliky z primární aplikace indikuje, kolik dat se ztratilo.

Jakmile se rozhodnete, že chcete převzít služeb při selhání do repliky, 

1. Zastavení replikace do repliky<br/>
   Tento krok je nezbytný k tomu, aby se server repliky stal samostatným serverem a mohl přijímat zápisy. V rámci tohoto procesu se server repliky restartuje a odpojí se od primárního. Jakmile zahájíte zastavení replikace, proces back-endu obvykle trvá několik minut, než se aplikují zbývající protokoly, které se ještě nepoužily, a pro otevření databáze jako serveru pro čtení s možností zápisu. V části [zastavení replikace](#stop-replication--promote-replica) v tomto článku se seznámíte s důsledky této akce.
    
2. Nasměrujte aplikaci na (bývalé) repliku.<br/>
   Každý server má jedinečný připojovací řetězec. Aktualizujte připojovací řetězec aplikace tak, aby odkazoval na (bývalé) repliky namísto primárního.
    
Po úspěšném zpracování čtení a zápisu vaší aplikace jste dokončili převzetí služeb při selhání. Množství prostojů, na kterých bude prostředí aplikace záviset při zjištění problému a dokončení kroků 1 a 2 výše.

### <a name="disaster-recovery"></a>Zotavení po havárii

Když dojde k závažné události havárie, jako je třeba zóna dostupnosti nebo regionální selhání, můžete provést operaci zotavení po havárii tím, že povýšíte svoji repliku pro čtení. Z portálu uživatelského rozhraní můžete přejít na server repliky pro čtení. Pak klikněte na kartu replikace a můžete ji zastavit, aby byla povýšení replikována na nezávislý server. Alternativně můžete pomocí rozhraní příkazového [řádku Azure](/cli/azure/postgres/server/replica#az_postgres_server_replica_stop) zastavit a zvýšit úroveň serveru repliky.

## <a name="considerations"></a>Co je potřeba vzít v úvahu

V této části najdete přehled informací o funkci Replika čtení.

### <a name="prerequisites"></a>Předpoklady
Repliky čtení a [logické dekódování](concepts-logical.md) závisí na protokolu Postgres Write předem log (WAL). Tyto dvě funkce vyžadují různé úrovně protokolování z Postgres. Logické dekódování potřebuje vyšší úroveň protokolování než repliky čtení.

Ke konfiguraci správné úrovně protokolování použijte parametr podpory replikace Azure. Podpora replikace Azure má tři možnosti nastavení:

* **Off** – vloží do Wal minimální informace. Toto nastavení není k dispozici na většině Azure Database for PostgreSQL serverů.  
* **Replika** – přesnější podrobnější informace než **vypnuto**. Toto je minimální úroveň protokolování potřebného pro fungování [replik pro čtení](concepts-read-replicas.md) . Toto nastavení je na většině serverů výchozí.
* **Logický** – další podrobnější informace než **replika** Toto je minimální úroveň protokolování pro logické dekódování, které funguje. V tomto nastavení fungují i repliky pro čtení.


### <a name="new-replicas"></a>Nové repliky
Replika pro čtení je vytvořená jako nový server Azure Database for PostgreSQL. Existující server nelze vytvořit do repliky. Nelze vytvořit repliku jiné repliky pro čtení.

### <a name="replica-configuration"></a>Konfigurace repliky
Replika se vytvoří pomocí stejného nastavení výpočtů a úložiště jako primární. Po vytvoření repliky je možné změnit několik nastavení včetně doby uchování úložiště a zálohy.

Pravidla brány firewall, pravidla virtuální sítě a nastavení parametrů nejsou děděna z primárního serveru do repliky, když je replika vytvořena nebo následně.

### <a name="scaling"></a>Škálování
Škálování virtuální jádra nebo mezi Pro obecné účely a paměťově optimalizované:
* PostgreSQL vyžaduje `max_connections` , aby nastavení na sekundárním serveru bylo [větší nebo rovno nastavení na primárním](https://www.postgresql.org/docs/current/hot-standby.html)serveru, jinak se sekundární nespustí.
* V Azure Database for PostgreSQL je maximální povolená připojení pro každý server opravena na skladovou skladovou položku, protože připojení vybírají paměť. Můžete se dozvědět víc o [mapování mezi MAX_CONNECTIONS a sklady COMPUTE](concepts-limits.md).
* Horizontální **navýšení** kapacity: napřed navýšení kapacity a navýšení kapacity primárního objektu. Tato objednávka zabrání chybám v porušení `max_connections` požadavku.
* Horizontální navýšení **kapacity: nejdřív** navýšete kapacitu na výpočetní úrovni a pak proveďte horizontální navýšení kapacity repliky. Pokud se pokusíte škálovat repliku nižší než primární, dojde k chybě, protože tato akce je v rozporu s `max_connections` požadavkem.

Škálování úložiště:
* Všechny repliky mají povolený Automatický růst úložiště, aby nedocházelo k problémům s replikací z úložiště – úplná replika. Toto nastavení nelze zakázat.
* Můžete také ručně škálovat úložiště, stejně jako na jakémkoli jiném serveru.


### <a name="basic-tier"></a>Základní úroveň
Servery základní vrstvy podporují jenom replikaci stejné oblasti.

### <a name="max_prepared_transactions"></a>max_prepared_transactions
[PostgreSQL vyžaduje](https://www.postgresql.org/docs/current/runtime-config-resource.html#GUC-MAX-PREPARED-TRANSACTIONS) , aby hodnota `max_prepared_transactions` parametru v replice pro čtení byla větší než nebo stejná jako primární hodnota. v opačném případě se replika nespustí. Pokud se chcete `max_prepared_transactions` na primárním počítači změnit, nejdřív ho změňte na replikách.

### <a name="stopped-replicas"></a>Zastavené repliky
Pokud zastavíte replikaci mezi primárním serverem a replikou pro čtení, bude se replika znovu používat pro změnu. Zastavená replika se stal samostatným serverem, který přijímá čtení i zápis. Samostatný server se nedá znovu vytvořit do repliky.

### <a name="deleted-primary-and-standalone-servers"></a>Odstraněné primární a samostatné servery
Po odstranění primárního serveru se všechny jeho repliky pro čtení stanou samostatnými servery. Repliky se restartují, aby se tato změna projevila.

## <a name="next-steps"></a>Další kroky
* Naučte se [vytvářet a spravovat repliky pro čtení v Azure Portal](howto-read-replicas-portal.md).
* Naučte se [vytvářet a spravovat repliky pro čtení v Azure CLI a REST API](howto-read-replicas-cli.md).