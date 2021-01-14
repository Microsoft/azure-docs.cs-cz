---
title: Řešení latence replikace – Azure Database for MySQL
description: Přečtěte si, jak řešit latenci replikace pomocí Azure Database for MySQL replik pro čtení.
keywords: MySQL, řešení potíží, latence replikace v sekundách
author: savjani
ms.author: pariks
ms.service: mysql
ms.topic: troubleshooting
ms.date: 01/13/2021
ms.openlocfilehash: 34210d08ad5328f200f5b92c13bfcf85cfead3ec
ms.sourcegitcommit: 2bd0a039be8126c969a795cea3b60ce8e4ce64fc
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/14/2021
ms.locfileid: "98199474"
---
# <a name="troubleshoot-replication-latency-in-azure-database-for-mysql"></a>Řešení potíží s latencí replikace ve službě Azure Database for MySQL

[!INCLUDE[applies-to-single-flexible-server](./includes/applies-to-single-flexible-server.md)]

Funkce [replika čtení](concepts-read-replicas.md) umožňuje replikovat data z Azure Database for MySQL serveru do serveru repliky jen pro čtení. Můžete škálovat úlohy směrováním dotazů čtení a vytváření sestav z aplikace na servery repliky. Toto nastavení snižuje tlak na zdrojovém serveru. Zvyšuje také celkový výkon a latenci aplikace při škálování.

Repliky se aktualizují asynchronně pomocí technologie replikace nativního binárního protokolu (binlog) modulu MySQL. Další informace najdete v tématu [Přehled konfigurace replikace na základě umístění souboru MySQL binlog](https://dev.mysql.com/doc/refman/5.7/en/binlog-replication-configuration-overview.html).

Prodleva replikace u sekundárních replik čtení závisí na několika faktorech. Tyto faktory zahrnují, ale nejsou omezené na:

- Latence sítě.
- Svazek transakce na zdrojovém serveru.
- Výpočetní vrstva zdrojového serveru a sekundárního serveru repliky pro čtení.
- Dotazy běžící na zdrojovém a sekundárním serveru.

V tomto článku se dozvíte, jak řešit latenci replikace v Azure Database for MySQL. Porozumíte také některým běžným příčinám zvýšené latence replikace na serverech repliky.

> [!NOTE]
> Komunikace bez posunu
>
> Microsoft podporuje různé a zahrnuté prostředí. Tento článek obsahuje odkazy na _Hlavní_ a _podřízený_ text. [Průvodce stylem Microsoftu pro komunikaci bez předplatných](https://github.com/MicrosoftDocs/microsoft-style-guide/blob/master/styleguide/bias-free-communication.md) je rozpoznává jako vyloučená slova. Tato slova se v tomto článku používají kvůli konzistenci, protože jsou aktuálně slova, která se zobrazují v softwaru. Když se software aktualizuje, aby se odstranila slova, Tento článek se aktualizuje tak, aby se vyrovnává.
>

## <a name="replication-concepts"></a>Koncepty replikace

Když je povolený binární protokol, zdrojový server zapíše potvrzené transakce do binárního protokolu. Binární protokol se používá pro replikaci. Tato možnost je ve výchozím nastavení zapnutá pro všechny nově zřízené servery, které podporují až 16 TB úložiště. Na serverech repliky se na každém serveru repliky spouštějí dvě vlákna. Jedno vlákno je *vstupně-výstupní podproces* a druhý je *vlákno SQL*:

- Vlákno v/v se připojuje ke zdrojovému serveru a žádá o aktualizované binární protokoly. Toto vlákno přijímá binární aktualizace protokolů. Tyto aktualizace se ukládají na server repliky v místním protokolu, který se nazývá *protokol přenosu*.
- Vlákno SQL přečte protokol Relay a pak použije změny dat na serverech repliky.

## <a name="monitoring-replication-latency"></a>Monitorování latence replikace

Azure Database for MySQL poskytuje metriku pro prodlevu replikace v sekundách v [Azure monitor](concepts-monitoring.md). Tato metrika je k dispozici pouze na serverech repliky pro čtení. Počítá se seconds_behind_master metrikou, která je dostupná v MySQL. 

Pokud chcete pochopit příčinu zvýšené latence replikace, připojte se k serveru repliky pomocí [MySQL Workbench](connect-workbench.md) nebo [Azure Cloud Shell](https://shell.azure.com). Pak spusťte následující příkaz.

>[!NOTE]
> V kódu nahraďte ukázkové hodnoty názvem serveru repliky a uživatelským jménem správce. Uživatelské jméno správce vyžaduje `@\<servername>` Azure Database for MySQL.

```azurecli-interactive
mysql --host=myreplicademoserver.mysql.database.azure.com --user=myadmin@mydemoserver -p 
```

Tady je postup, jak vypadá v Cloud Shell terminálu:

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

Ve stejném Cloud Shell terminálu spusťte následující příkaz:

```
mysql> SHOW SLAVE STATUS;
```

Zde je typický výstup:
  
>[!div class="mx-imgBorder"]
> :::image type="content" source="./media/howto-troubleshoot-replication-latency/show-status.png" alt-text="Monitorování latence replikace":::

Výstup obsahuje mnoho informací. Normálně se musíte zaměřit jenom na řádky, které jsou popsané v následující tabulce.

|Metrika|Popis|
|---|---|
|Slave_IO_State| Představuje aktuální stav vlákna v/v. V případě, že je zdrojový (hlavní) Server synchronizován, je obvykle stav "čeká na odeslání události". Stav, například "připojování k hlavní", označuje, že replika ztratila připojení ke zdrojovému serveru. Zajistěte, aby byl na zdrojovém serveru spuštěný, nebo zkontrolujte, jestli je připojení blokované bránou firewall.|
|Master_Log_File| Představuje binární soubor protokolu, na který zdrojový Server zapisuje.|
|Read_Master_Log_Pos| Určuje, kam zdrojový Server zapisuje do binárního souboru protokolu.|
|Relay_Master_Log_File| Představuje binární soubor protokolu, který server repliky čte ze zdrojového serveru.|
|Slave_IO_Running| Označuje, zda je vlákno v/v spuštěno. Hodnota by měla být `Yes` . Pokud je hodnota `NO` , je replikace nejspíš poškozená.|
|Slave_SQL_Running| Označuje, zda je spuštěno vlákno SQL. Hodnota by měla být `Yes` . Pokud je hodnota `NO` , je replikace nejspíš poškozená.|
|Exec_Master_Log_Pos| Určuje pozici Relay_Master_Log_File, kterou replika používá. V případě latence by tato pozice sekvence měla být menší než Read_Master_Log_Pos.|
|Relay_Log_Space|Určuje horní mez velikosti protokolu přenosu. Velikost můžete zjistit tak, jak se dotazuje `SHOW GLOBAL VARIABLES` `relay_log_space_limit` .|
|Seconds_Behind_Master| Zobrazuje latenci replikace v sekundách.|
|Last_IO_Errno|Zobrazí kód chyby vlákna v/v, pokud existuje. Další informace o těchto kódech naleznete v odkazu na [chybovou zprávu serveru MySQL](https://dev.mysql.com/doc/mysql-errors/5.7/en/server-error-reference.html).|
|Last_IO_Error| Zobrazí chybovou zprávu vlákna v/v, pokud existuje.|
|Last_SQL_Errno|Zobrazí kód chyby vlákna SQL, pokud existuje. Další informace o těchto kódech naleznete v odkazu na [chybovou zprávu serveru MySQL](https://dev.mysql.com/doc/mysql-errors/5.7/en/server-error-reference.html).|
|Last_SQL_Error|Zobrazí chybovou zprávu vlákna SQL, pokud existuje.|
|Slave_SQL_Running_State| Určuje aktuální stav vlákna SQL. V tomto stavu `System lock` je normální. Je také normální zobrazení stavu `Waiting for dependent transaction to commit` . Tento stav indikuje, že replika čeká, než zdrojový server aktualizuje potvrzené transakce.|

Pokud je Slave_IO_Running `Yes` a Slave_SQL_Running je `Yes` , je replikace v pořádku. 

Dále ověřte Last_IO_Errno, Last_IO_Error, Last_SQL_Errno a Last_SQL_Error.  Tato pole zobrazují číslo chyby a chybovou zprávu o nedávné chybě, která způsobila zastavení vlákna SQL. Číslo chyby `0` a prázdná zpráva znamená, že došlo k chybě. Zkontrolujte nenulovou hodnotu chyby tak, že zkontrolujete kód chyby v [odkazu na chybovou zprávu serveru MySQL](https://dev.mysql.com/doc/mysql-errors/5.7/en/server-error-reference.html).

## <a name="common-scenarios-for-high-replication-latency"></a>Běžné scénáře pro vysokou latenci replikace

V následujících částech najdete scénáře, ve kterých je vysoká latence replikace běžná.

### <a name="network-latency-or-high-cpu-consumption-on-the-source-server"></a>Latence sítě nebo vysoká spotřeba procesoru na zdrojovém serveru

Pokud se zobrazí následující hodnoty, je latence replikace pravděpodobně způsobena vysokou latencí sítě nebo vysokou spotřebou procesoru na zdrojovém serveru.

```
Slave_IO_State: Waiting for master to send event
Master_Log_File: the binary file sequence is larger then Relay_Master_Log_File, e.g. mysql-bin.00020
Relay_Master_Log_File: the file sequence is smaller than Master_Log_File, e.g. mysql-bin.00010
```

V takovém případě je vlákno v/v spuštěno a čeká na zdrojový server. Zdrojový server už je zapsaný do binárního souboru protokolu číslo 20. Replika přijala pouze do souboru číslo 10. Primárními faktory pro latenci vysoké replikace v tomto scénáři jsou rychlost sítě nebo vysoké využití procesoru na zdrojovém serveru.  

V Azure může být latence sítě v rámci oblasti obvykle měřená v milisekundách. V různých oblastech se latence pohybuje od milisekundy do sekund.

Ve většině případů je zpoždění připojení mezi vstupně-výstupními vlákny a zdrojovým serverem způsobeno vysokým využitím procesoru na zdrojovém serveru. Vstupně-výstupní vlákna jsou zpracovávána pomalu. Tento problém můžete zjistit pomocí Azure Monitor ke kontrole využití procesoru a počtu souběžných připojení na zdrojovém serveru.

Pokud se na zdrojovém serveru nezobrazuje vysoké využití procesoru, může být problém latence sítě. Pokud dojde k náhlému výpadku latence sítě, podívejte se na [stránku stav Azure](https://status.azure.com/status) , kde najdete známé problémy nebo výpadky. 

### <a name="heavy-bursts-of-transactions-on-the-source-server"></a>Těžké shluky transakcí na zdrojovém serveru

Pokud se zobrazí následující hodnoty, bude pravděpodobně příčinou latence replikace velký nárůst počtu transakcí na zdrojovém serveru. 

```
Slave_IO_State: Waiting for the slave SQL thread to free enough relay log space
Master_Log_File: the binary file sequence is larger then Relay_Master_Log_File, e.g. mysql-bin.00020
Relay_Master_Log_File: the file sequence is smaller then Master_Log_File, e.g. mysql-bin.00010
```

Výstup ukazuje, že replika může načíst binární protokol za zdrojový server. Ale vlákno v/v neudává, že se už místo protokolu přenosu zaplní.

Rychlost sítě nezpůsobí zpoždění. Replika se snaží zachytit. Velikost aktualizovaného binárního protokolu ale překračuje horní limit místa protokolu Relay.

Pokud chcete tento problém vyřešit, povolte na zdrojovém serveru [protokol pomalých dotazů](concepts-server-logs.md) . Pomocí pomalých protokolů dotazů Identifikujte dlouhotrvající transakce na zdrojovém serveru. Pak Nalaďte identifikované dotazy, aby se snížila latence na serveru. 

Latence replikace tohoto řazení je běžně způsobená zatížením dat na zdrojovém serveru. Když mají zdrojové servery týdenní nebo měsíční data, latence replikace je bohužel nenevyhnutelná. Servery repliky nakonec zachytí po dokončení načítání dat na zdrojovém serveru.

### <a name="slowness-on-the-replica-server"></a>Zpomalení serveru repliky

Pokud provedete následující hodnoty, pak se problém může nacházet na serveru repliky.

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

V tomto scénáři výstup ukazuje, že vstupně-výstupní vlákna i vlákno SQL běží dobře. Replika přečte stejný binární soubor protokolu, který zapisuje zdrojový server. Nicméně některá latence na serveru repliky odráží stejnou transakci ze zdrojového serveru.

Následující části popisují běžné příčiny tohoto typu latence.

#### <a name="no-primary-key-or-unique-key-on-a-table"></a>V tabulce není žádný primární klíč ani jedinečný klíč.

Azure Database for MySQL používá replikaci na základě řádků. Zdrojový Server zapisuje události do binárního protokolu a zaznamenává změny v jednotlivých řádcích tabulky. Vlákno SQL pak tyto změny replikuje do odpovídajících řádků tabulky na serveru repliky. Pokud tabulka neobsahuje primární klíč nebo jedinečný klíč, vlákno SQL zkontroluje všechny řádky v cílové tabulce, aby se změny projevily. Tato kontrola může způsobit latenci replikace.

V MySQL je primárním klíčem přidružený index, který zajišťuje rychlý výkon dotazů, protože nemůže obsahovat hodnoty NULL. Pokud používáte modul úložiště InnoDB, data tabulky jsou fyzicky organizována, aby bylo možné provádět extrémně rychlé vyhledávání a seřazení na základě primárního klíče.

Před vytvořením serveru repliky doporučujeme přidat primární klíč na tabulky na zdrojovém serveru. Přidejte primární klíče na zdrojovém serveru a pak znovu vytvořte repliky pro čtení, které vám pomůžou vylepšit latenci replikace.

Pomocí následujícího dotazu Zjistěte, ve kterých tabulkách chybí primární klíč na zdrojovém serveru:

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

#### <a name="long-running-queries-on-the-replica-server"></a>Dlouhotrvající dotazy na serveru repliky

Zatížení serveru repliky může provést prodlevu vlákna SQL za podprocesem v/v. Dlouhodobě běžící dotazy na serveru repliky jsou jedním z běžných příčin vysoké latence replikace. Pokud chcete tento problém vyřešit, povolte [protokol pomalých dotazů](concepts-server-logs.md) na serveru repliky.

Pomalé dotazy můžou zvýšit spotřebu prostředků nebo zpomalit Server, aby replika nedokázala zachytit zdrojový server. V tomto scénáři optimalizujete pomalé dotazy. Rychlejší dotazy zabraňují zablokování vlákna SQL a významně zlepšují latenci replikace.

#### <a name="ddl-queries-on-the-source-server"></a>Dotazy DDL na zdrojovém serveru

Na zdrojovém serveru může trvat delší dobu příkaz jazyka DDL (Data Definition Language) [`ALTER TABLE`](https://dev.mysql.com/doc/refman/5.7/en/alter-table.html) . I když je spuštěný příkaz DDL, můžou na zdrojovém serveru paralelně běžet tisíce dalších dotazů. 

Při replikaci knihovny DDL, aby byla zajištěna konzistence databáze, modul MySQL spustí skript DDL v jednom vlákně replikace. Během této úlohy jsou všechny ostatní replikované dotazy blokované a musí počkat, až se operace DDL dokončí na serveru repliky. I online operace DDL tuto prodlevu způsobí. Operace DDL zvyšují latenci replikace.

Pokud jste na zdrojovém serveru povolili [protokol pomalých dotazů](concepts-server-logs.md) , můžete zjistit příčinu tohoto problému s latencí zaškrtnutím příkazu DDL, který byl spuštěn na zdrojovém serveru. Při vyřazování, přejmenování a vytváření indexu můžete použít modul vložení pro příkaz ALTER TABLE. Může být nutné zkopírovat data tabulky a znovu sestavit tabulku.

Obvykle je souběžná DML podporovaná pro algoritmus inplace. Při přípravě a spuštění operace ale můžete v tabulce krátce přebírat výhradní zámek metadat. Proto pro příkaz CREATE INDEX můžete použít algoritmus klauzulí a UZAMKNOUT pro ovlivnění metody kopírování tabulky a úrovně souběžnosti pro čtení a zápis. Můžete přesto zabránit operacím DML přidáním FULLTEXTového indexu nebo PROSTORového indexu.

Následující příklad vytvoří index pomocí klauzulí ALGORITHM a LOCK.

```sql
ALTER TABLE table_name ADD INDEX index_name (column), ALGORITHM=INPLACE, LOCK=NONE;
```

Pro příkaz DDL, který vyžaduje zámek, bohužel nemůžete zamezit latenci replikace. Chcete-li snížit možné důsledky, proveďte tyto typy operací DDL v době mimo špičku, například během noci.

#### <a name="downgraded-replica-server"></a>Server repliky s downgradem

V Azure Database for MySQL používají repliky pro čtení stejnou konfiguraci serveru jako zdrojový server. Konfiguraci serveru repliky můžete změnit poté, co byla vytvořena.

Pokud je server repliky downgradované, může zatížení spotřebovávat více prostředků, což zase může způsobit latenci replikace. K detekci tohoto problému použijte Azure Monitor ke kontrole spotřeby procesoru a paměti serveru repliky.

V tomto scénáři doporučujeme, abyste zachovali konfiguraci serveru repliky v hodnotách, které jsou větší nebo rovny hodnotám zdrojového serveru. Tato konfigurace umožňuje, aby replika udržovala na zdrojovém serveru.

#### <a name="improving-replication-latency-by-tuning-the-source-server-parameters"></a>Zvýšení latence replikace vyladěním parametrů zdrojového serveru

V Azure Database for MySQL ve výchozím nastavení je replikace optimalizovaná tak, aby běžela s paralelními vlákny na replikách. Když úlohy s vysokou mírou souběžnosti na zdrojovém serveru způsobí, že server repliky bude klesnout na pozadí, můžete zvýšit latenci replikace nakonfigurováním parametru binlog_group_commit_sync_delay na zdrojovém serveru.

Parametr binlog_group_commit_sync_delay určuje, kolik mikrosekund bude zápis binárního protokolu čekat před synchronizací binárního souboru protokolu. Výhodou tohoto parametru je, že místo okamžitého použití každé potvrzené transakce odesílá zdrojový server hromadné aktualizace binárního protokolu. Tato prodleva snižuje počet vstupně-výstupních operací v replice a pomáhá zvýšit výkon.

Může být užitečné nastavit parametr binlog_group_commit_sync_delay na 1000. Pak Sledujte latenci replikace. Nastavte tento parametr opatrně a používejte ho pouze pro úlohy s vysokou mírou využití.

> [!IMPORTANT]
> Na serveru repliky se binlog_group_commit_sync_delay parametr doporučuje 0. Doporučuje se to proto, že na rozdíl od zdrojového serveru nebude mít server repliky vysokou souběžnost a zvýšení hodnoty binlog_group_commit_sync_delay na serveru repliky může nechtěně způsobit zvýšení prodlevy replikace.

Pro úlohy s nízkou souběžnou zátěží, které zahrnují mnoho transakcí s jedním prvkem, může nastavení binlog_group_commit_sync_delay zvýšit latenci. Latence může být zvýšena, protože vlákno v/v čeká na hromadné aktualizace binárních protokolů i v případě, že je potvrzena pouze jedna transakce.

## <a name="next-steps"></a>Další kroky

Podívejte se na [Přehled replikace MySQL binlog](https://dev.mysql.com/doc/refman/5.7/en/binlog-replication-configuration-overview.html).
