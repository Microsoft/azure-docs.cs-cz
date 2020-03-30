---
title: Zprovoznění kanálu analýzy dat – Azure
description: Nastavte a spusťte ukázkový datový kanál, který je spuštěn novými daty a vytváří stručné výsledky.
author: ashishthaps
ms.author: ashishth
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.custom: hdinsightactive
ms.date: 12/25/2019
ms.openlocfilehash: 16c7af4d66bd550eb4a286de7c86c436b1fe10e2
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "75922667"
---
# <a name="operationalize-a-data-analytics-pipeline"></a>Zprovoznění kanálu datových analýz

*Datové kanály* podcení mnoho řešení analýzy dat. Jak již název napovídá, datový kanál přebírá nezpracovaná data, čistí a přetváří je podle potřeby a před uložením zpracovaných dat obvykle provádí výpočty nebo agregace. Zpracovaná data jsou spotřebována klienty, sestavami nebo řešeními API. Datový kanál musí poskytovat opakovatelné výsledky, ať už podle plánu nebo při spuštění novými daty.

Tento článek popisuje, jak zprovoznit datové kanály pro opakovatelnost pomocí Oozie běží na HDInsight Hadoop clustery. Ukázkový scénář vás provede datovým kanálem, který připravuje a zpracovává data časových řad letu letecké společnosti.

V následujícím scénáři vstupní data je plochý soubor obsahující dávku letových dat po dobu jednoho měsíce. Tyto údaje o letu zahrnují informace, jako je letiště původu a určení, proletované míle, časy odletu a příletu atd. Cílem tohoto kanálu je shrnout denní výkon letecké společnosti, kde každá letecká společnost má jeden řádek za každý den s průměrným zpožděním odletu a příletu během několika minut a celkovým počtem ujetí kilometrů v ten den.

| YEAR | MONTH | DAY_OF_MONTH | Dopravce |AVG_DEP_DELAY | AVG_ARR_DELAY |TOTAL_DISTANCE |
| --- | --- | --- | --- | --- | --- | --- |
| 2017 | 1 | 3 | Aa | 10.142229 | 7.862926 | 2644539 |
| 2017 | 1 | 3 | AS | 9.435449 | 5.482143 | 572289 |
| 2017 | 1 | 3 | Dl | 6.935409 | -2.1893024 | 1909696 |

Ukázkový kanál čeká, až dorazí data o letu nového časového období, a pak uloží podrobné informace o letu do datového skladu Apache Hive pro dlouhodobé analýzy. Kanál také vytvoří mnohem menší datovou sadu, která shrnuje pouze denní data letu. Tato denní souhrnná data letu jsou odesílána do databáze SQL za účelem poskytování sestav, například pro web.

Následující diagram znázorňuje ukázkový kanál.

![Přehled datového kanálu příkladu letu HDI](./media/hdinsight-operationalize-data-pipeline/flight-pipeline-overview.png)

## <a name="apache-oozie-solution-overview"></a>Přehled řešení Apache Oozie

Tento kanál používá Apache Oozie spuštěné v clusteru HDInsight Hadoop.

Oozie popisuje své kanály z hlediska *akcí*, *pracovních postupů*a *koordinátorů*. Akce určují skutečnou práci, kterou chcete provést, například spuštění dotazu Hive. Pracovní postupy definují posloupnost akcí. Koordinátoři definují plán spuštění pracovního postupu. Koordinátoři mohou také čekat na dostupnost nových dat před spuštěním instance pracovního postupu.

Následující diagram znázorňuje návrh na vysoké úrovni tohoto příkladu kanálu Oozie.

![Oozie Flight příklad datového kanálu](./media/hdinsight-operationalize-data-pipeline/pipeline-overview-oozie.png)

## <a name="provision-azure-resources"></a>Zřízení prostředků Azure

Tento kanál vyžaduje Azure SQL Database a HDInsight Hadoop clusteru ve stejném umístění. Azure SQL Database ukládá jak souhrnná data vytvořená kanálem, tak úložiště metadat Oozie.

### <a name="provision-azure-sql-database"></a>Zřízení azure sql databáze

1. Vytvořte databázi Azure SQL. Viz [Vytvoření azure sql databáze na webu Azure Portal](../sql-database/sql-database-single-database-get-started.md).

1. Chcete-li se ujistit, že váš cluster HDInsight má přístup k připojené azure sql database, nakonfigurujte pravidla brány firewall Azure SQL Database, abyste umožnili službám a prostředkům Azure přístup k serveru. Tuto možnost můžete povolit na webu Azure Portal tak, že vyberete **Nastavit serverovou bránu firewall**a v **pod** **povolte služby azure a prostředky** pro přístup k tomuto serveru pro server nebo databázi Azure SQL Database. Další informace naleznete v [tématu Vytvoření a správa pravidel brány firewall IP](../sql-database/sql-database-firewall-configure.md#use-the-azure-portal-to-manage-server-level-ip-firewall-rules).

1. Pomocí [editoru dotazů](../sql-database/sql-database-single-database-get-started.md#query-the-database) vytvořte následující `dailyflights` příkazy SQL k vytvoření tabulky, která bude ukládat souhrnná data z každého spuštění kanálu.

    ```sql
    CREATE TABLE dailyflights
    (
        YEAR INT,
        MONTH INT,
        DAY_OF_MONTH INT,
        CARRIER CHAR(2),
        AVG_DEP_DELAY FLOAT,
        AVG_ARR_DELAY FLOAT,
        TOTAL_DISTANCE FLOAT
    )
    GO

    CREATE CLUSTERED INDEX dailyflights_clustered_index on dailyflights(YEAR,MONTH,DAY_OF_MONTH,CARRIER)
    GO
    ```

Vaše Azure SQL Database je teď připravená.

### <a name="provision-an-apache-hadoop-cluster"></a>Zřízení clusteru Apache Hadoop

Vytvořte cluster Apache Hadoop s vlastním metastorem. Při vytváření clusteru z portálu na kartě **Úložiště** zkontrolujte, zda jste v nastavení Metastore vybrali databázi SQL v části **Nastavení Metastore**. Další informace o výběru metaúložiště naleznete v [tématu Výběr vlastního metaúložiště během vytváření clusteru](./hdinsight-use-external-metadata-stores.md#select-a-custom-metastore-during-cluster-creation). Další informace o vytváření clusteru najdete [v tématu Začínáme s HDInsight na Linuxu](hadoop/apache-hadoop-linux-tutorial-get-started.md).

## <a name="verify-ssh-tunneling-set-up"></a>Ověřit nastavení tunelového propojení SSH

Chcete-li pomocí webové konzoly Oozie zobrazit stav instance koordinátora a pracovního postupu, nastavte tunelové propojení SSH do clusteru HDInsight. Další informace naleznete v [tématu Tunel SSH](hdinsight-linux-ambari-ssh-tunnel.md).

> [!NOTE]  
> Chrome můžete také použít s rozšířením [Foxy Proxy](https://getfoxyproxy.org/) k procházení webových prostředků clusteru v tunelu SSH. Nakonfigurujte jej tak, aby proxy všechny požadavky prostřednictvím hostitele `localhost` na portu tunelu 9876. Tento přístup je kompatibilní s Windows Subsystem pro Linux, také známý jako Bash na Windows 10.

1. Spuštěním následujícího příkazu otevřete tunel SSH do clusteru, kde `CLUSTERNAME` je název clusteru:

    ```cmd
    ssh -C2qTnNf -D 9876 sshuser@CLUSTERNAME-ssh.azurehdinsight.net
    ```

1. Ověřte, zda je tunel funkční, přejdete na Ambari na hlavním uzlu procházením:

    `http://headnodehost:8080`

1. Chcete-li získat přístup k **webové konzoli Oozie** z ambari, přejděte na **oozie** > **rychlé odkazy** > [Active server] > **Oozie Web UI**.

## <a name="configure-hive"></a>Konfigurace úlu

### <a name="upload-data"></a>Nahrání dat

1. Stáhněte si ukázkový soubor CSV, který obsahuje letová data po dobu jednoho měsíce. Stáhněte si `2017-01-FlightData.zip` jeho ZIP soubor z [úložiště HDInsight GitHub](https://github.com/hdinsight/hdinsight-dev-guide) `2017-01-FlightData.csv`a rozbalte jej do souboru CSV .

1. Zkopírujte tento soubor CSV až do účtu Azure Storage připojeného `/example/data/flights` k clusteru HDInsight a umístěte ho do složky.

    1. Pomocí protokolu SCP zkopírujte soubory z místního počítače do místního úložiště hlavního uzlu clusteru HDInsight.

        ```cmd
        scp ./2017-01-FlightData.csv sshuser@CLUSTERNAME-ssh.azurehdinsight.net:2017-01-FlightData.csv
        ```

    1. Pomocí [příkazu ssh](./hdinsight-hadoop-linux-use-ssh-unix.md) se připojte ke clusteru. Upravte níže uvedený `CLUSTERNAME` příkaz nahrazením názvem clusteru a zadejte příkaz:

        ```cmd
        ssh sshuser@CLUSTERNAME-ssh.azurehdinsight.net
        ```

    1. Z relace ssh použijte příkaz HDFS ke zkopírování souboru z místního úložiště hlavního uzlu do Služby Azure Storage.

        ```bash
        hadoop fs -mkdir /example/data/flights
        hdfs dfs -put ./2017-01-FlightData.csv /example/data/flights/2017-01-FlightData.csv
        ```

### <a name="create-tables"></a>Vytvoření tabulek

Ukázková data jsou nyní k dispozici. Kanál však vyžaduje dvě tabulky Hive pro zpracování,`rawFlights`jednu pro příchozí data (`flights`) a jednu pro souhrnná data ( ). Vytvořte tyto tabulky v Ambari následujícím způsobem.

1. Přihlaste se do Ambari přejdete na `http://headnodehost:8080`.

2. V seznamu služeb vyberte **Možnost Hive**.

    ![Seznam služeb Apache Ambari výběrem Hive](./media/hdinsight-operationalize-data-pipeline/hdi-ambari-services-hive.png)

3. Vyberte **Přejít na zobrazení** vedle popisku Zobrazení úlu 2.0.

    ![Souhrnný seznam Ambari Apache Hive](./media/hdinsight-operationalize-data-pipeline/hdi-ambari-services-hive-summary.png)

4. V textové oblasti dotazu vložte následující `rawFlights` příkazy k vytvoření tabulky. Tabulka `rawFlights` obsahuje schéma při čtení pro soubory CSV ve `/example/data/flights` složce ve službě Azure Storage.

    ```sql
    CREATE EXTERNAL TABLE IF NOT EXISTS rawflights (
        YEAR INT,
        MONTH INT,
        DAY_OF_MONTH INT,
        FL_DATE STRING,
        CARRIER STRING,
        FL_NUM STRING,
        ORIGIN STRING,
        DEST STRING,
        DEP_DELAY FLOAT,
        ARR_DELAY FLOAT,
        ACTUAL_ELAPSED_TIME FLOAT,
        DISTANCE FLOAT)
    ROW FORMAT SERDE 'org.apache.hadoop.hive.serde2.OpenCSVSerde'
    WITH SERDEPROPERTIES
    (
        "separatorChar" = ",",
        "quoteChar"     = "\""
    )
    LOCATION '/example/data/flights'
    ```

5. Chcete-li tabulku vytvořit, vyberte **spustit.**

    ![dotaz na podregistr služeb hdi ambari](./media/hdinsight-operationalize-data-pipeline/hdi-ambari-services-hive-query.png)

6. Chcete-li `flights` tabulku vytvořit, nahraďte text v textové oblasti dotazu následujícími příkazy. Tabulka `flights` je tabulka spravovaná podhodnoum, která rozdělí data načtená do ní podle roku, měsíce a dne v měsíci. Tato tabulka bude obsahovat všechna historická letová data s nejnižší rozlišovací schopností obsaženou ve zdrojových datech jednoho řádku na let.

    ```sql
    SET hive.exec.dynamic.partition.mode=nonstrict;

    CREATE TABLE flights
    (
        FL_DATE STRING,
        CARRIER STRING,
        FL_NUM STRING,
        ORIGIN STRING,
        DEST STRING,
        DEP_DELAY FLOAT,
        ARR_DELAY FLOAT,
        ACTUAL_ELAPSED_TIME FLOAT,
        DISTANCE FLOAT
    )
    PARTITIONED BY (YEAR INT, MONTH INT, DAY_OF_MONTH INT)
    ROW FORMAT SERDE 'org.apache.hadoop.hive.serde2.OpenCSVSerde'
    WITH SERDEPROPERTIES 
    (
        "separatorChar" = ",",
        "quoteChar"     = "\""
    );
    ```

7. Chcete-li tabulku vytvořit, vyberte **spustit.**

## <a name="create-the-oozie-workflow"></a>Vytvoření pracovního postupu Oozie

Kanály obvykle zpracovávají data v dávkách v daném časovém intervalu. V tomto případě potrubí zpracovává letová data denně. Tento přístup umožňuje vstupní soubory CSV docházet denně, týdně, měsíčně nebo ročně.

Ukázkový pracovní postup zpracovává data letu ze dne na den ve třech hlavních krocích:

1. Spusťte dotaz Hive, chcete-li extrahovat data pro rozsah dat `rawFlights` daný den ze `flights` zdrojového souboru CSV reprezentovaného tabulkou a vložte data do tabulky.
2. Spusťte dotaz Hive a dynamicky vytvořte pracovní tabulku v Hive pro den, který obsahuje kopii letových dat shrnutých podle dne a dopravce.
3. Pomocí Apache Sqoop zkopírujte všechna data z denní pracovní `dailyflights` tabulky v Hive do cílové tabulky v Azure SQL Database. Sqoop čte zdrojové řádky z dat za tabulkou Hive, která se načítá ve službě Azure Storage, a načte je do databáze SQL pomocí připojení JDBC.

Tyto tři kroky jsou koordinovány pracovním postupem Oozie.

1. Z místní pracovní stanice vytvořte `job.properties`soubor s názvem . Jako počáteční obsah souboru použijte následující text.
Pak aktualizujte hodnoty pro konkrétní prostředí. Tabulka pod textem shrnuje každou z vlastností a označuje, kde můžete najít hodnoty pro vlastní prostředí.

    ```text
    nameNode=wasbs://[CONTAINERNAME]@[ACCOUNTNAME].blob.core.windows.net
    jobTracker=[ACTIVERESOURCEMANAGER]:8050
    queueName=default
    oozie.use.system.libpath=true
    appBase=wasbs://[CONTAINERNAME]@[ACCOUNTNAME].blob.core.windows.net/oozie
    oozie.wf.application.path=${appBase}/load_flights_by_day
    hiveScriptLoadPartition=wasbs://[CONTAINERNAME]@[ACCOUNTNAME].blob.core.windows.net/oozie/load_flights_by_day/hive-load-flights-partition.hql
    hiveScriptCreateDailyTable=wasbs://[CONTAINERNAME]@[ACCOUNTNAME].blob.core.windows.net/oozie/load_flights_by_day/hive-create-daily-summary-table.hql
    hiveDailyTableName=dailyflights${year}${month}${day}
    hiveDataFolder=wasbs://[CONTAINERNAME]@[ACCOUNTNAME].blob.core.windows.net/example/data/flights/day/${year}/${month}/${day}
    sqlDatabaseConnectionString="jdbc:sqlserver://[SERVERNAME].database.windows.net;user=[USERNAME];password=[PASSWORD];database=[DATABASENAME]"
    sqlDatabaseTableName=dailyflights
    year=2017
    month=01
    day=03
    ```

    | Vlastnost | Zdroj hodnoty |
    | --- | --- |
    | nameNode | Úplná cesta ke kontejneru úložiště Azure připojenému k vašemu clusteru HDInsight. |
    | jobTracker | Název interního hostitele hlavního uzlu YARN aktivního clusteru. Na domovské stránce Ambari vyberte ze seznamu služeb položku YARN a pak zvolte Active Resource Manager. Název hostitele URI se zobrazí v horní části stránky. Připojte port 8050. |
    | název_fronty | Název fronty YARN použitý při plánování akcí Hive. Ponechat jako výchozí. |
    | oozie.use.system.libpath | Odejdi jako pravda. |
    | appBase | Cesta k podsložce ve službě Azure Storage, kde nasadíte pracovní postup Oozie a podpůrné soubory. |
    | oozie.wf.application.path | Umístění pracovního postupu `workflow.xml` Oozie ke spuštění. |
    | hiveScriptLoadPartition | Cesta v Azure Storage k souboru `hive-load-flights-partition.hql`dotazu Hive . |
    | hiveScriptCreateDailyTable | Cesta v Azure Storage k souboru `hive-create-daily-summary-table.hql`dotazu Hive . |
    | hiveDailyTableName | Dynamicky generovaný název, který se má použít pro pracovní tabulku. |
    | hiveDataFolder | Cesta v Azure Storage k datům obsaženým v pracovní tabulce. |
    | sqlDatabaseConnectionString | Připojovací řetězec syntaxe JDBC k databázi Azure SQL. |
    | sqlDatabaseTableName | Název tabulky v Azure SQL Database, do které se vkládají souhrnné řádky. Nechte `dailyflights`jako . |
    | year | Roční složka dne, pro který jsou vypočítány souhrny letů. Odejděte tak, jak je. |
    | month | Měsíční složka dne, pro který jsou vypočítány souhrny letů. Odejděte tak, jak je. |
    | day | Denní měsíc součást den, pro který jsou počítány souhrny letu. Odejděte tak, jak je. |

1. Z místní pracovní stanice vytvořte `hive-load-flights-partition.hql`soubor s názvem . Jako obsah souboru použijte níže uvedený kód.

    ```sql
    SET hive.exec.dynamic.partition.mode=nonstrict;

    INSERT OVERWRITE TABLE flights
    PARTITION (YEAR, MONTH, DAY_OF_MONTH)
    SELECT 
        FL_DATE,
        CARRIER,
        FL_NUM,
        ORIGIN,
        DEST,
        DEP_DELAY,
        ARR_DELAY,
        ACTUAL_ELAPSED_TIME,
        DISTANCE,
        YEAR,
        MONTH,
        DAY_OF_MONTH
    FROM rawflights
    WHERE year = ${year} AND month = ${month} AND day_of_month = ${day};
    ```

    Oozie proměnné používají `${variableName}`syntaxi . Tyto proměnné jsou nastaveny v souboru. `job.properties` Oozie nahrazuje skutečné hodnoty za běhu.

1. Z místní pracovní stanice vytvořte `hive-create-daily-summary-table.hql`soubor s názvem . Jako obsah souboru použijte níže uvedený kód.

    ```sql
    DROP TABLE ${hiveTableName};
    CREATE EXTERNAL TABLE ${hiveTableName}
    (
        YEAR INT,
        MONTH INT,
        DAY_OF_MONTH INT,
        CARRIER STRING,
        AVG_DEP_DELAY FLOAT,
        AVG_ARR_DELAY FLOAT,
        TOTAL_DISTANCE FLOAT
    )
    ROW FORMAT DELIMITED
    FIELDS TERMINATED BY '\t' STORED AS TEXTFILE LOCATION '${hiveDataFolder}';
    INSERT OVERWRITE TABLE ${hiveTableName}
    SELECT  year, month, day_of_month, carrier, avg(dep_delay) avg_dep_delay, 
            avg(arr_delay) avg_arr_delay, sum(distance) total_distance 
    FROM flights
    GROUP BY year, month, day_of_month, carrier 
    HAVING year = ${year} AND month = ${month} AND day_of_month = ${day};
    ```

    Tento dotaz vytvoří pracovní tabulku, která bude ukládat pouze souhrnná data pro jeden den, vzít na vědomí příkaz SELECT, který vypočítá průměrné zpoždění a součet vzdáleností proletěných dopravcem ve dne. Data vložená do této tabulky uložená ve známém umístění (cesta označená proměnnou hiveDataFolder), takže je lze použít jako zdroj pro Sqoop v dalším kroku.

1. Z místní pracovní stanice vytvořte `workflow.xml`soubor s názvem . Jako obsah souboru použijte níže uvedený kód. Výše uvedené kroky jsou vyjádřeny jako samostatné akce v souboru pracovního postupu Oozie.

    ```xml
    <workflow-app name="loadflightstable" xmlns="uri:oozie:workflow:0.5">
        <start to = "RunHiveLoadFlightsScript"/>
        <action name="RunHiveLoadFlightsScript">
            <hive xmlns="uri:oozie:hive-action:0.2">
                <job-tracker>${jobTracker}</job-tracker>
                <name-node>${nameNode}</name-node>
                <configuration>
                <property>
                    <name>mapred.job.queue.name</name>
                    <value>${queueName}</value>
                </property>
                </configuration>
                <script>${hiveScriptLoadPartition}</script>
                <param>year=${year}</param>
                <param>month=${month}</param>
                <param>day=${day}</param>
            </hive>
            <ok to="RunHiveCreateDailyFlightTableScript"/>
            <error to="fail"/>
        </action>
    
        <action name="RunHiveCreateDailyFlightTableScript">
            <hive xmlns="uri:oozie:hive-action:0.2">
                <job-tracker>${jobTracker}</job-tracker>
                <name-node>${nameNode}</name-node>
                <configuration>
                <property>
                    <name>mapred.job.queue.name</name>
                    <value>${queueName}</value>
                </property>
                </configuration>
                <script>${hiveScriptCreateDailyTable}</script>
                <param>hiveTableName=${hiveDailyTableName}</param>
                <param>year=${year}</param>
                <param>month=${month}</param>
                <param>day=${day}</param>
                <param>hiveDataFolder=${hiveDataFolder}/${year}/${month}/${day}</param>
            </hive>
            <ok to="RunSqoopExport"/>
            <error to="fail"/>
        </action>
    
        <action name="RunSqoopExport">
            <sqoop xmlns="uri:oozie:sqoop-action:0.2">
                <job-tracker>${jobTracker}</job-tracker>
                <name-node>${nameNode}</name-node>
                <configuration>
                <property>
                    <name>mapred.compress.map.output</name>
                    <value>true</value>
                </property>
                </configuration>
                <arg>export</arg>
                <arg>--connect</arg>
                <arg>${sqlDatabaseConnectionString}</arg>
                <arg>--table</arg>
                <arg>${sqlDatabaseTableName}</arg>
                <arg>--export-dir</arg>
                <arg>${hiveDataFolder}/${year}/${month}/${day}</arg>
                <arg>-m</arg>
                <arg>1</arg>
                <arg>--input-fields-terminated-by</arg>
                <arg>"\t"</arg>
                <archive>mssql-jdbc-7.0.0.jre8.jar</archive>
                </sqoop>
            <ok to="end"/>
            <error to="fail"/>
        </action>
        <kill name="fail">
            <message>Job failed, error message[${wf:errorMessage(wf:lastErrorNode())}] </message>
        </kill>
        <end name="end"/>
    </workflow-app>
    ```

Dva dotazy Hive jsou přístupné podle jejich cestu ve službě Azure Storage a `job.properties` zbývající hodnoty proměnných jsou poskytovány souborem. Tento soubor konfiguruje pracovní postup pro datum 3.

## <a name="deploy-and-run-the-oozie-workflow"></a>Nasazení a spuštění pracovního postupu Oozie

Pomocí protokolu SCP z relace bash nasadíte pracovní postup Oozie`workflow.xml`( ), dotazy Hive (`hive-load-flights-partition.hql` a `hive-create-daily-summary-table.hql`) a konfiguraci úlohy (`job.properties`).  V Oozie, `job.properties` pouze soubor může existovat na místní úložiště headnode. Všechny ostatní soubory musí být uloženy v HDFS, v tomto případě Azure Storage. Akce Sqoop používaná pracovním postupem závisí na ovladači JDBC pro komunikaci s databází SQL, který musí být zkopírován z hlavního uzlu do HDFS.

1. Vytvořte `load_flights_by_day` podsložku pod cestou uživatele v místním úložišti hlavního uzlu. Z otevřené relace ssh proveďte následující příkaz:

    ```bash
    mkdir load_flights_by_day
    ```

1. Zkopírujte všechny soubory v `workflow.xml` aktuálním adresáři (a `job.properties` soubory) až do `load_flights_by_day` podsložky. Z místní pracovní stanice proveďte následující příkaz:

    ```cmd
    scp ./* sshuser@CLUSTERNAME-ssh.azurehdinsight.net:load_flights_by_day
    ```

1. Zkopírujte soubory pracovního postupu do HDFS. Z otevřené relace ssh proveďte následující příkazy:

    ```bash
    cd load_flights_by_day
    hadoop fs -mkdir -p /oozie/load_flights_by_day
    hdfs dfs -put ./* /oozie/load_flights_by_day
    ```

1. Zkopírujte `mssql-jdbc-7.0.0.jre8.jar` z místního hlavního uzlu do složky pracovního postupu v systému HDFS. Pokud cluster obsahuje jiný soubor jar, můžete příkaz podle potřeby revidovat. Podle `workflow.xml` potřeby je revidován, aby odrážel jiný soubor jar. Z otevřené relace ssh proveďte následující příkaz:

    ```bash
    hdfs dfs -put /usr/share/java/sqljdbc_7.0/enu/mssql-jdbc*.jar /oozie/load_flights_by_day
    ```

1. Spusťte pracovní postup. Z otevřené relace ssh proveďte následující příkaz:

    ```bash
    oozie job -config job.properties -run
    ```

1. Sledujte stav pomocí webové konzole Oozie. V rámci Ambari, vyberte **Oozie**, **Rychlé odkazy**, a pak **Oozie Web Console**. Na kartě **Úlohy pracovního postupu** vyberte **Všechny úlohy**.

    ![hdi oozie webové konzole pracovní chod](./media/hdinsight-operationalize-data-pipeline/hdi-oozie-web-console-workflows.png)

1. Pokud je stav succeeded, dotaz sql database tabulka zobrazit vložené řádky. Pomocí portálu Azure přejděte do podokna databáze SQL, vyberte **Nástroje**a otevřete **Editor dotazů**.

        SELECT * FROM dailyflights

Nyní, když je pracovní postup spuštěn pro jeden testovací den, můžete tento pracovní postup zabalit s koordinátorem, který naplánuje pracovní postup tak, aby byl spuštěn denně.

## <a name="run-the-workflow-with-a-coordinator"></a>Spuštění pracovního postupu s koordinátorem

Chcete-li naplánovat tento pracovní postup tak, aby se schylovat denně (nebo všechny dny v období, ale můžete použít koordinátora. Koordinátor je definován souborem XML, `coordinator.xml`například :

```xml
<coordinator-app name="daily_export" start="2017-01-01T00:00Z" end="2017-01-05T00:00Z" frequency="${coord:days(1)}" timezone="UTC" xmlns="uri:oozie:coordinator:0.4">
    <datasets>
        <dataset name="ds_input1" frequency="${coord:days(1)}" initial-instance="2016-12-31T00:00Z" timezone="UTC">
            <uri-template>${sourceDataFolder}${YEAR}-${MONTH}-FlightData.csv</uri-template>
            <done-flag></done-flag>
        </dataset>
    </datasets>
    <input-events>
        <data-in name="event_input1" dataset="ds_input1">
            <instance>${coord:current(0)}</instance>
        </data-in>
    </input-events>
    <action>
        <workflow>
            <app-path>${appBase}/load_flights_by_day</app-path>
            <configuration>
                <property>
                    <name>year</name>
                    <value>${coord:formatTime(coord:nominalTime(), 'yyyy')}</value>
                </property>
                <property>
                    <name>month</name>
                    <value>${coord:formatTime(coord:nominalTime(), 'MM')}</value>
                </property>
                <property>
                    <name>day</name>
                    <value>${coord:formatTime(coord:nominalTime(), 'dd')}</value>
                </property>
                <property>
                    <name>hiveScriptLoadPartition</name>
                    <value>${hiveScriptLoadPartition}</value>
                </property>
                <property>
                    <name>hiveScriptCreateDailyTable</name>
                    <value>${hiveScriptCreateDailyTable}</value>
                </property>
                <property>
                    <name>hiveDailyTableNamePrefix</name>
                    <value>${hiveDailyTableNamePrefix}</value>
                </property>
                <property>
                    <name>hiveDailyTableName</name>
                    <value>${hiveDailyTableNamePrefix}${coord:formatTime(coord:nominalTime(), 'yyyy')}${coord:formatTime(coord:nominalTime(), 'MM')}${coord:formatTime(coord:nominalTime(), 'dd')}</value>
                </property>
                <property>
                    <name>hiveDataFolderPrefix</name>
                    <value>${hiveDataFolderPrefix}</value>
                </property>
                <property>
                    <name>hiveDataFolder</name>
                    <value>${hiveDataFolderPrefix}${coord:formatTime(coord:nominalTime(), 'yyyy')}/${coord:formatTime(coord:nominalTime(), 'MM')}/${coord:formatTime(coord:nominalTime(), 'dd')}</value>
                </property>
                <property>
                    <name>sqlDatabaseConnectionString</name>
                    <value>${sqlDatabaseConnectionString}</value>
                </property>
                <property>
                    <name>sqlDatabaseTableName</name>
                    <value>${sqlDatabaseTableName}</value>
                </property>
            </configuration>
        </workflow>
    </action>
</coordinator-app>
```

Jak můžete vidět, většina koordinátora právě předává informace o konfiguraci instanci pracovního postupu. Existuje však několik důležitých položek, které je třeba zavolat.

* Bod 1: `start` `end` Atributy a `coordinator-app` na samotném prvku řídí časový interval, přes který koordinátor běží.

    ```
    <coordinator-app ... start="2017-01-01T00:00Z" end="2017-01-05T00:00Z" frequency="${coord:days(1)}" ...>
    ```

    Koordinátor je zodpovědný za plánování `start` `end` akcí v rámci a rozsah `frequency` dat podle intervalu určeného atributem. Každá naplánovaná akce zase spustí pracovní postup tak, jak je nakonfigurován. Ve výše uvedené definici koordinátora je koordinátor konfigurován tak, aby spouštěl akce od 1. Frekvence je nastavena na jeden den výrazem `${coord:days(1)}`frekvence jazyka Výraz [Oozie](https://oozie.apache.org/docs/4.2.0/CoordinatorFunctionalSpec.html#a4.4._Frequency_and_Time-Period_Representation) . Výsledkem je, že koordinátor jednou denně naplánuje akci (a tím i pracovní postup). Pro rozsahy dat, které jsou v minulosti, jako v tomto příkladu, akce bude naplánováno spustit bez prodlení. Začátek data, od kterého je naplánováno spuštění akce se nazývá *nominální čas*. Ke zpracování dat za 1.

* Bod 2: V rozsahu dat pracovního postupu `dataset` prvek určuje, kde se mají v HDFS hledat data pro určité časové období, a konfiguruje, jak Oozie určuje, zda jsou data ještě k dispozici pro zpracování.

    ```xml
    <dataset name="ds_input1" frequency="${coord:days(1)}" initial-instance="2016-12-31T00:00Z" timezone="UTC">
        <uri-template>${sourceDataFolder}${YEAR}-${MONTH}-FlightData.csv</uri-template>
        <done-flag></done-flag>
    </dataset>
    ```

    Cesta k datům v HDFS je vytvořena dynamicky podle `uri-template` výrazu uvedeného v prvku. V tomto koordinátorovi se s datovou sadou používá také frekvence jednoho dne. Zatímco počáteční a koncové datum ovládacího prvku koordinátora při plánování akcí (a `initial-instance` `frequency` definuje jejich nominální časy), a na datové sadě `uri-template`řídí výpočet data, které se používá při vytváření . V takovém případě nastavte počáteční instanci na jeden den před začátkem koordinátora, abyste zajistili, že vyzvedne údaje za první den (1/ 1 / 2017). Výpočet data datové sady se posouvá vpřed `initial-instance` z hodnoty (12/31/2016) postupující v přírůstcích frekvence datové sady (jeden den), dokud nenajde poslední datum, které neprojde nominálním časem nastaveným koordinátorem (2017-01-01T00:00:00 GMT pro první akci).

    Prázdný `done-flag` prvek označuje, že když Oozie kontroluje přítomnost vstupních dat ve stanovený čas, Oozie určuje data, zda jsou k dispozici přítomností adresáře nebo souboru. V tomto případě je to přítomnost souboru CSV. Pokud je k dispozici soubor csv, Oozie předpokládá, že data jsou připravena a spustí instanci pracovního postupu pro zpracování souboru. Pokud není k dispozici žádný soubor CSV, Oozie předpokládá, že data ještě nejsou připravena a že spuštění pracovního postupu přejde do stavu čekání.

* Bod 3: `data-in` Prvek určuje konkrétní časové razítko, které se má `uri-template` použít jako nominální čas při nahrazování hodnot v pro přidruženou datovou sadu.

    ```xml
    <data-in name="event_input1" dataset="ds_input1">
        <instance>${coord:current(0)}</instance>
    </data-in>
    ```

    V takovém případě nastavte instanci na výraz `${coord:current(0)}`, což se promítá do použití nominálního času akce, jak bylo původně naplánováno koordinátorem. Jinými slovy, když koordinátor naplánuje akci spustit s nominálním časem 01/01/2017, pak 01/01/2017 je to, co se používá k nahrazení proměnných YEAR (2017) a MONTH (01) v šabloně URI. Po výpočtu šablony URI pro tuto instanci Oozie zkontroluje, zda je k dispozici očekávaný adresář nebo soubor, a odpovídajícím způsobem naplánuje další spuštění pracovního postupu.

Tři předchozí body se spojí, aby se dala k situaci, kdy koordinátor plánuje zpracování zdrojových dat den za dnem.

* Bod 1: Koordinátor začíná nominálním datem 2017-01-01.

* Bod 2: Oozie hledá `sourceDataFolder/2017-01-FlightData.csv`údaje dostupné v .

* Bod 3: Když Oozie najde tento soubor, naplánuje instanci pracovního postupu, který bude zpracovávat data pro 2017-01-01. Oozie pak pokračuje ve zpracování pro 2017-01-02. Toto hodnocení se opakuje až do období 2017-01-05, ale nezahrnuje.

Stejně jako u pracovních postupů je `job.properties` konfigurace koordinátora definována v souboru, který má nadmnožinu nastavení používaných pracovním postupem.

```text
nameNode=wasbs://[CONTAINERNAME]@[ACCOUNTNAME].blob.core.windows.net
jobTracker=[ACTIVERESOURCEMANAGER]:8050
queueName=default
oozie.use.system.libpath=true
appBase=wasbs://[CONTAINERNAME]@[ACCOUNTNAME].blob.core.windows.net/oozie
oozie.coord.application.path=${appBase}
sourceDataFolder=wasbs://[CONTAINERNAME]@[ACCOUNTNAME].blob.core.windows.net/example/data/flights/
hiveScriptLoadPartition=wasbs://[CONTAINERNAME]@[ACCOUNTNAME].blob.core.windows.net/oozie/load_flights_by_day/hive-load-flights-partition.hql
hiveScriptCreateDailyTable=wasbs://[CONTAINERNAME]@[ACCOUNTNAME].blob.core.windows.net/oozie/load_flights_by_day/hive-create-daily-summary-table.hql
hiveDailyTableNamePrefix=dailyflights
hiveDataFolderPrefix=wasbs://[CONTAINERNAME]@[ACCOUNTNAME].blob.core.windows.net/example/data/flights/day/
sqlDatabaseConnectionString="jdbc:sqlserver://[SERVERNAME].database.windows.net;user=[USERNAME];password=[PASSWORD];database=[DATABASENAME]"
sqlDatabaseTableName=dailyflights
```

Jediné nové vlastnosti zavedené `job.properties` v tomto souboru jsou:

| Vlastnost | Zdroj hodnoty |
| --- | --- |
| oozie.coord.application.path | Označuje umístění souboru `coordinator.xml` obsahujícího koordinátora Oozie ke spuštění. |
| hiveDailyTableNamePrefix | Předpona použitá při dynamickém vytváření názvu tabulky pracovní tabulky. |
| hiveDataFolderPrefix | Předpona cesty, kde budou uloženy všechny pracovní tabulky. |

## <a name="deploy-and-run-the-oozie-coordinator"></a>Nasazení a spuštění koordinátora Oozie

Chcete-li spustit kanál s koordinátorem, postupujte podobným způsobem jako pro pracovní postup, s výjimkou práce ze složky o jednu úroveň nad složkou, která obsahuje váš pracovní postup. Tato konvence složek odděluje koordinátory od pracovních postupů na disku, takže můžete přidružit jednoho koordinátora k různým podřízeným pracovním postupům.

1. Pomocí protokolu SCP z místního počítače zkopírujte soubory koordinátora do místního úložiště hlavního uzlu clusteru.

    ```bash
    scp ./* sshuser@CLUSTERNAME-ssh.azurehdinsight.net:~
    ```

2. SSH do vašeho hlavního uzlu.

    ```bash
    ssh sshuser@CLUSTERNAME-ssh.azurehdinsight.net
    ```

3. Zkopírujte soubory koordinátora do HDFS.

    ```bash
    hdfs dfs -put ./* /oozie/
    ```

4. Spusťte koordinátora.

    ```bash
    oozie job -config job.properties -run
    ```

5. Ověřte stav pomocí webové konzoly Oozie, tentokrát vyberte kartu **Úlohy koordinátora** a potom **všechny úlohy**.

    ![Pracovní příležitosti koordinátora webové konzoly Oozie](./media/hdinsight-operationalize-data-pipeline/hdi-oozie-web-console-coordinator-jobs.png)

6. Vyberte instanci koordinátora, chcete-li zobrazit seznam naplánovaných akcí. V tomto případě byste měli vidět čtyři akce s nominálnídobou v rozsahu od 1/1/2017 do 1/4/2017.

    ![Úloha koordinátora webové konzoly Oozie](./media/hdinsight-operationalize-data-pipeline/hdi-oozie-web-console-coordinator-instance.png)

    Každá akce v tomto seznamu odpovídá instanci pracovního postupu, který zpracovává data za jeden den, kde je začátek tohoto dne označen nominálním časem.

## <a name="next-steps"></a>Další kroky

[Apache Oozie Dokumentace](https://oozie.apache.org/docs/4.2.0/index.html)
