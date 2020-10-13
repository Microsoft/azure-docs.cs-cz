---
title: Řešení latence replikace – Azure Database for MySQL
description: Přečtěte si, jak řešit latenci replikace pomocí Azure Database for MySQL replik pro čtení.
keywords: MySQL, řešení potíží, latence replikace v sekundách
author: savjani
ms.author: pariks
ms.service: mysql
ms.topic: troubleshooting
ms.date: 10/08/2020
ms.openlocfilehash: 16a502a53b4441faf68ea342e0bc865731d38b1a
ms.sourcegitcommit: fbb620e0c47f49a8cf0a568ba704edefd0e30f81
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "91876989"
---
# <a name="troubleshoot-replication-latency-in-azure-database-for-mysql"></a>Řešení potíží s latencí replikace v Azure Database for MySQL

Funkce [replika čtení](concepts-read-replicas.md) umožňuje replikovat data z Azure Database for MySQL serveru do serveru repliky jen pro čtení. K horizontálnímu navýšení kapacity úloh se používají repliky čtení, které směrují dotazy pro čtení nebo vytváření sestav z aplikace na servery repliky. Tím se snižuje tlak na primárním serveru a zlepšuje se celkový výkon a latence aplikace při škálování. Repliky se aktualizují asynchronně s využitím technologie replikace na základě pozice v souboru binárního protokolu (binlog) nativní pro stroj MySQL. Další informace o replikaci binlog najdete v tématu [Přehled replikace MySQL binlog](https://dev.mysql.com/doc/refman/5.7/en/binlog-replication-configuration-overview.html). 

Prodleva replikace u sekundárních replik čtení závisí na počtu faktorů, včetně neomezeno na 

- Latence sítě
- Svazek transakce na zdrojovém serveru
- Výpočetní vrstva zdrojového a sekundárního serveru repliky pro čtení
- Dotazy spuštěné na primárním a sekundárním serveru. 

V tomto dokumentu se dozvíte, jak řešit latenci replikace v Azure Database for MySQL. Kromě toho také porozumíte některým z běžných příčin zvýšené latence replikace na serverech repliky.

## <a name="replication-concepts"></a>Koncepty replikace

Když je povolený binární protokol, zdrojový server zapíše potvrzenou transakci do binárního protokolu, který se používá pro replikaci. Binární protokol je ve výchozím nastavení zapnutý pro všechny nově zřízené servery, které podporují až 16 TB úložiště. Na serverech repliky existují dvě vlákna spuštěná na jeden server repliky, jednu s názvem vlákno v/v a druhá se nazývá vlákno SQL.

- **Vlákno v/** v se připojuje ke zdrojovému serveru a žádá o aktualizované binární protokoly. Až toto vlákno obdrží binární aktualizace protokolů, uloží se na server repliky do místního protokolu, který se nazývá protokol přenosu.
- **Vlákno SQL** přečte protokol Relay a použije změny dat na serverech repliky.

## <a name="monitoring-replication-latency"></a>Monitorování latence replikace

Azure Database for MySQL poskytuje metriku prodlevy replikace v sekundách v [Azure monitor](concepts-monitoring.md). Tato metrika je k dispozici pouze na serverech replik pro čtení. Tato metrika se počítá pomocí seconds_behind_master metriky dostupné v MySQL. Abyste pochopili hlavní příčiny zvýšené latence replikace, připojte se k serveru repliky pomocí [MySQL Workbench](connect-workbench.md) nebo [Azure Cloud shellu](https://shell.azure.com) a spusťte následující příkaz:

 Nahraďte hodnoty skutečným názvem serveru repliky a přihlašovacím jménem uživatele správce. Uživatelské jméno správce vyžaduje @ \<servername> pro Azure Database for MySQL:

  ```azurecli-interactive
  mysql --host=myreplicademoserver.mysql.database.azure.com --user=myadmin@mydemoserver -p 
  ```

  Tady je postup, jak vypadat v Cloud Shell terminálu.
  ```
  Requesting a Cloud Shell.Succeeded.
  Connecting terminal...

  Welcome to Azure Cloud Shell

  Type "az" to use Azure CLI
  Type "help" to learn about Cloud Shell

  user@Azure:~$mysql -h myreplicademoserver.mysql.database.azure.com -u myadmin@mydemoserver -p
  Enter password:
  Welcome to the MySQL monitor.  Commands end with ; or \g.
  Your MySQL connection id is 64796
  Server version: 5.6.42.0 Source distribution

  Copyright (c) 2000, 2020, Oracle and/or its affiliates. All rights reserved.

  Oracle is a registered trademark of Oracle Corporation and/or its
  affiliates. Other names may be trademarks of their respective
  owners.

  Type 'help;' or '\h' for help. Type '\c' to clear the current input statement.
  mysql>
  ```
  Ve stejném Azure Cloud Shell terminálu spusťte následující příkaz.

  ```
  mysql> SHOW SLAVE STATUS;
  ```

  Typický výstup bude vypadat takto:
  
>[!div class="mx-imgBorder"]
> :::image type="content" source="./media/howto-troubleshoot-replication-latency/show-status.png" alt-text="Monitorování latence replikace&quot;:::


Výstup obsahuje mnoho informací, ale obvykle je důležité se zaměřit pouze na následující sloupce:

|Metrika|Popis|
|---|---|
|Slave_IO_State| Aktuální stav vlákna v/v. V normálním případě se jedná o stav &quot;čeká se na odeslání události&quot; Master &quot;, pokud se synchronizuje. Pokud se ale zobrazí stav &quot;připojování k hlavnímu&quot;, pak replika ztratila připojení k hlavnímu serveru. Zkontroluje, jestli je hlavní server spuštěný, nebo jestli je připojení blokované bránou firewall.|
|Master_Log_File| Binární soubor protokolu, do kterého se má zapsat hlavní server.|
|Read_Master_Log_Pos| Představuje pozici ve výše uvedeném binárním souboru protokolu, ve kterém je hlavní zápis.|
|Relay_Master_Log_File| Indikuje, že představuje binární soubor protokolu, který server repliky čte z hlavního serveru.|
|Slave_IO_Running| Označuje, zda je vlákno v/v spuštěno. Měl by být &quot;Ano&quot;. Pokud ne, pravděpodobně je replikace přerušená.|
|Slave_SQL_Running| Označuje, zda je spuštěno vlákno SQL. Měl by být &quot;Ano&quot;. Pokud ne, pravděpodobně je replikace přerušená.|
|Exec_Master_Log_Pos| Zobrazuje pozici Relay_Master_Log_File, které replika používá. Pokud je latence, tato sekvence pozice by měla být menší než Read_Master_Log_Pos.|
|Relay_Log_Space|Zobrazuje horní limit velikosti protokolu Relay. Velikost můžete zjistit dotazem zobrazit globální proměnné, jako je &quot;relay_log_space_limit&quot;.|
|Seconds_Behind_Master| Zobrazuje latenci replikace v sekundách.|
|Last_IO_Errno|Zobrazí kód chyby vlákna v/v, pokud existuje. Další informace o těchto kódech najdete v [dokumentaci k MySQL](https://dev.mysql.com/doc/refman/5.7/en/server-error-reference.html).|
|Last_IO_Error| Zobrazí chybovou zprávu vlákna v/v, pokud existuje.|
|Last_SQL_Errno|Zobrazí kód chyby vlákna SQL, pokud existuje. Další informace o těchto kódech najdete v [dokumentaci k MySQL](https://dev.mysql.com/doc/refman/5.7/en/server-error-reference.html).|
|Last_SQL_Error|Zobrazí chybovou zprávu vlákna SQL, pokud existuje.|
|Slave_SQL_Running_State| Určuje aktuální stav vlákna SQL. Všimněte si, že &quot;systémový zámek" zobrazený v tomto stavu je normální chování. Je normální zobrazit stav "čeká se na potvrzení závislé transakce". Indikuje, že replika čeká, než hlavní server aktualizuje potvrzené transakce.|

Pokud je Slave_IO_Running Ano a Slave_SQL_Running je Ano, je replikace v pořádku. 

Dále je nutné zaškrtnout Last_IO_Errno, Last_IO_Error, Last_SQL_Errno a Last_SQL_Error.  Tato pole obsahují číslo chyby a chybovou zprávu o Poslední chybě, která způsobila zastavení vlákna SQL. Číslo chyby 0 a prázdná zpráva znamená, že nedošlo k žádné chybě. Nenulová hodnota v této chybě musí být dále prozkoumána tak, že v [dokumentaci MySQL](https://dev.mysql.com/doc/refman/5.7/en/server-error-reference.html)vyhledá kód chyby.

## <a name="common-scenarios-for-high-replication-latency"></a>Běžné scénáře pro vysokou latenci replikace

### <a name="network-latency-or-high-cpu-on-source-server"></a>Latence sítě nebo vysoký procesor na zdrojovém serveru

Pokud proberete následující hodnoty, Nejběžnější příčinou latence replikace je vysoká latence sítě nebo vysoká spotřeba procesoru na zdrojovém serveru. V tomto případě je vlákno v/v spuštěno a čeká na hlavní server. Hlavní (zdrojový Server) již napsal do binárního souboru protokolu #20, zatímco replika přijala pouze do souboru #10. Primární přispívající faktory pro latenci vysoké replikace v tomto scénáři jsou rychlost sítě nebo vysoké využití procesoru na zdrojovém serveru.  V Azure se latence sítě v rámci oblasti obvykle pohybuje v milisekundách a v celé oblasti může jít o dobu až sekund. Ve většině případů je zpoždění ve vstupně-výstupních vláknech k připojení ke zdrojovému serveru způsobené vysokým využitím procesoru na zdrojovém serveru, což způsobuje, že zpracování vlákna v/v není pomalé. To je možné zjistit monitorováním využití procesoru a sledováním počtu souběžných připojení na zdrojovém serveru pomocí Azure monitoru.

Pokud se na zdrojovém serveru nezobrazuje vysoké využití procesoru, mohou být možné příčiny latence sítě. Pokud se vám vysoká latence sítě neobvykle daří, doporučujeme zkontrolovat [stavovou stránku Azure](https://status.azure.com/status) , aby se zajistilo, že dojde k neznámým problémům nebo výpadkům. 

```
Slave_IO_State: Waiting for master to send event
Master_Log_File: the binary file sequence is larger then Relay_Master_Log_File, e.g. mysql-bin.00020
Relay_Master_Log_File: the file sequence is smaller than Master_Log_File, e.g. mysql-bin.00010
```

### <a name="heavy-burst-of-transactions-on-source-server"></a>Těžký nárůst transakcí na zdrojovém serveru

Pokud provedete následující hodnoty, Nejběžnější příčinou latence replikace je velmi velký nárůst počtu transakcí na zdrojovém serveru. I když může replika v níže uvedeném výstupu načíst binární protokol, vlákno v/v replika indikuje, že prostor protokolu relay je již plný. To znamená, že rychlost sítě nezpůsobuje zpoždění, protože replika se už snažila zachytit jako rychlou. Místo toho velikost aktualizovaného binárního protokolu překračuje horní limit místa protokolu Relay. Chcete-li tento problém vyřešit, je třeba povolit [pomalý protokol dotazů](concepts-server-logs.md) na hlavním serveru. Protokoly pomalých dotazů vám umožní identifikovat dlouhodobě běžící transakce na zdrojovém serveru. Identifikované dotazy je nutné ladit, aby se snížila latence na serveru. 

```
Slave_IO_State: Waiting for the slave SQL thread to free enough relay log space
Master_Log_File: the binary file sequence is larger then Relay_Master_Log_File, e.g. mysql-bin.00020
Relay_Master_Log_File: the file sequence is smaller then Master_Log_File, e.g. mysql-bin.00010
```

Níže jsou uvedené běžné příčiny latence v této kategorii:

#### <a name="replication-latency-due-to-data-load-on-source-server"></a>Latence replikace kvůli zatížení dat na zdrojovém serveru
V některých případech je na zdrojových serverech týdenní nebo měsíční zatížení dat. Omlouváme se, ale latence replikace je v tomto případě nenevyhnutelná. V tomto scénáři se servery repliky nakonec zachytí po dokončení načítání dat na zdrojovém serveru.


### <a name="slowness-on-the-replica-server"></a>Zpomalení serveru repliky

Pokud provedete následující hodnoty, nejčastější příčinou může být problém na serveru repliky, který potřebuje další šetření. V tomto scénáři, jak je vidět ve výstupu, jsou vlákna v/v i vlákna SQL i v případě, že replika čte stejný binární soubor protokolu jako hlavní zápisy. Na serveru repliky ale dojde k určité latenci, která odráží stejnou transakci ze zdrojového serveru. 

```
Slave_IO_State: Waiting for master to send event
Master_Log_File: The binary log file sequence equals to Relay_Master_Log_File, e.g. mysql-bin.000191
Read_Master_Log_Pos: The position of master server written to the above file is larger than Relay_Log_Pos, e.g. 103978138
Relay_Master_Log_File: mysql-bin.000191
Slave_IO_Running: Yes
Slave_SQL_Running: Yes
Exec_Master_Log_Pos: The position of slave reads from master binary log file is smaller than Read_Master_Log_Pos, e.g. 13468882
Seconds_Behind_Master: There is latency and the value here is greater than 0
```

Níže jsou uvedené běžné příčiny latence v této kategorii:

#### <a name="no-primary-or-unique-key-on-a-table"></a>V tabulce není primární ani jedinečný klíč.

Azure Database for MySQL používá replikaci na základě řádků. Při replikaci na základě řádků zapisuje hlavní server události do binárního protokolu o změně jednotlivých řádků tabulky. Vlákno SQL v rámci tohoto procesu provede tyto změny v odpovídajících řádcích tabulky na serveru repliky. Žádný primární nebo jedinečný klíč v tabulce je jedním z běžných příčin latence replikace. Nedostatek primárních nebo jedinečných klíčů vede ke kontrole všech řádků v cílové tabulce pomocí vlákna SQL, aby se změny projevily.

V MySQL je primárním klíčem přidružený index, který zajišťuje rychlý výkon dotazů, protože nemůže obsahovat hodnoty NULL. S modulem úložiště InnoDB jsou data tabulky fyzicky uspořádaná tak, aby se vytvářely velice rychlé vyhledávání a seřadí se na základě primárního klíče. Před vytvořením serveru repliky se proto doporučuje přidat primární klíč na tabulky na zdrojovém serveru. V tomto scénáři je potřeba přidat primární klíče na zdrojový server a znovu vytvořit repliky pro čtení, které vám pomůžou vylepšit latenci replikace.

Pomocí následujícího dotazu můžete zjistit, že na zdrojovém serveru nebyly nalezeny tabulky s primárním klíčem:

```sql 
select tab.table_schema as database_name, tab.table_name 
from information_schema.tables tab left join 
information_schema.table_constraints tco 
on tab.table_schema = tco.table_schema 
and tab.table_name = tco.table_name 
and tco.constraint_type = 'PRIMARY KEY' 
where tco.constraint_type is null 
and tab.table_schema not in('mysql', 'information_schema', 'performance_schema', 'sys') 
and tab.table_type = 'BASE TABLE' 
order by tab.table_schema, tab.table_name;

```

#### <a name="replication-latency-due-to-long-running-queries-on-replica-server"></a>Latence replikace kvůli dlouho běžícím dotazům na serveru repliky

Je možné, že zatížení na serveru repliky může zabránit tomu, aby vlákno SQL bylo uchováváno ve vlákně v/v. Toto je jeden z běžných příčin vysoké latence replikace, pokud je na serveru repliky dlouho běžící dotaz. V takovém případě by se měl na serveru repliky povolit [protokol pomalých dotazů](concepts-server-logs.md) , aby bylo možné tento problém vyřešit. Pomalé dotazy můžou zvýšit spotřebu prostředků nebo zpomalit Server, takže replika nebude schopná zachytit s hlavní větví. V tomto scénáři potřebujete ladit pomalé dotazy. Rychlejší dotazy zabraňují blokování vlákna SQL a významně zvyšují latenci replikace.


#### <a name="replication-latency-due-to-ddl-queries-on-source-server"></a>Latence replikace kvůli dotazům DDL na zdrojovém serveru
Pokud je na zdrojovém serveru dlouho spuštěný příkaz DDL, jako je například [Změna tabulky](https://dev.mysql.com/doc/refman/5.7/en/alter-table.html) spuštěná na zdrojovém serveru a jeho spuštění trvalo 1 hodinu. Během této doby můžou na zdrojovém serveru paralelně běžet tisíce dalších dotazů. Když se skript DDL replikuje do repliky, aby se zajistila konzistence databáze, musí modul MySQL spustit DDL v jednom vlákně replikace. Všechny ostatní replikované dotazy budou zablokované a bude potřeba počkat na hodinu nebo déle, dokud se operace DDL nedokončí na serveru repliky. To platí bez ohledu na online operaci DDL nebo ne. Při operacích DDL se očekává, že replikace bude sledovat zvýšení latence replikace.

Pokud máte na zdrojovém serveru [protokol pomalých dotazů](concepts-server-logs.md) povolený, můžete tento scénář zjistit tak, že si prohlédněte protokoly pomalých dotazů a zjistíte, jestli se na zdrojovém serveru SPUSTIL příkaz DDL. I když se vynechává index, přejmenování a vytvoření by se mělo používat na místě pro ALTER TABLE, který může zahrnovat kopírování dat tabulky, a opětovné sestavení tabulky. Pro souběžnou DML je obvykle podporováno, ale při přípravě a provádění této operace se může krátce v tabulce považovat exkluzivní zámek metadat. V takovém případě je možné pro příkaz CREATE INDEX použít algoritmus klauzulí a UZAMKNOUT pro použití metody kopírování tabulky a úrovně souběžnosti pro čtení a zápis, avšak přidání FULLTEXTového nebo PROSTORového indexu stále brání operacím DML. Podívejte se na příklad vytvoření indexu s klauzulemi and LOCK:

```sql
ALTER TABLE table_name ADD INDEX index_name (column), ALGORITHM=INPLACE, LOCK=NONE;
```

Pro příkaz DDL, který vyžaduje zámek, bohužel nelze zabránit latenci replikace, místo toho by se měly tyto typy operací DDL provádět v době mimo špičku, například během nighttime pro omezení potenciálního dopadu.

#### <a name="replication-latency-due-to-replica-server-lower-sku"></a>Latence replikace kvůli nižší SKU serveru repliky

V Azure Database for MySQL jsou repliky čtení vytvořené se stejnou konfigurací serveru, jako má hlavní server. Konfiguraci serveru repliky je možné po vytvoření změnit. Pokud se ale server repliky downgrade, může zatížení způsobit vyšší spotřebu prostředků, která zase může způsobit latenci replikace. To může být pozorováno monitorováním využití procesoru a paměti serveru repliky z Azure Monitor. V tomto scénáři se doporučuje, aby se konfigurace serveru repliky zachovala ve stejné nebo větší hodnotě než zdroj, aby bylo zajištěno, že je replika schopná s hlavní hodnotou zůstat.

#### <a name="improving-replication-latency-using-server-parameter-tuning-on-source-server"></a>Zvýšení latence replikace pomocí ladění parametrů serveru na zdrojovém serveru

V Azure Database for MySQL je replikace optimalizovaná tak, aby se ve výchozím nastavení spouštěla s paralelními vlákny na replikách. Pro úlohy s vysokou mírou souběžnosti na zdrojovém serveru, kde se server repliky nedaří zachytit, může latence replikace zlepšit konfigurací binlog_group_commit_sync_delay parametrů na zdrojovém serveru. Tento parametr určuje, kolik mikrosekund bude zápis binárního protokolu čekat před synchronizací binárního souboru protokolu. Výhodou je, že místo okamžitého použití všech potvrzených transakcí hlavní odesílá aktualizace binárního protokolu hromadně. Tím se snižuje počet vstupně-výstupních operací v replice a pomáhá zvýšit výkon. V tomto scénáři může být užitečné nastavit binlog_group_commit_sync_delay na 1000 nebo a monitorovat latenci replikace. Tento parametr by měl být nastaven obezřetně a využitý pouze pro vysoce souběžná zatížení. Pro scénář s nízkou souběžnou prioritou s velkým počtem transakcí typu Singleton může nastavení binlog_group_commit_sync_delay přidat k latenci, protože vstupně-výstupní operace čeká na hromadné aktualizace binárního protokolu, zatímco pouze pár transakcí může být potvrzeno. 

## <a name="next-steps"></a>Další kroky
Přečtěte si další informace o [binlog replikace MySQL](https://dev.mysql.com/doc/refman/5.7/en/binlog-replication-configuration-overview.html).
