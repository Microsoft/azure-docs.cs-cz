---
title: Zprovoznění kanálu datových analýz – Azure
description: Nastavení a spuštění kanálu dat příklad, který se aktivuje nová data a vytvoří stručné výsledky.
services: hdinsight
ms.service: hdinsight
author: ashishthaps
ms.author: ashishth
ms.reviewer: jasonh
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 01/11/2018
ms.openlocfilehash: 9057d9f5d63598ea249e8f3193b84fd715018829
ms.sourcegitcommit: f6e2a03076679d53b550a24828141c4fb978dcf9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/27/2018
ms.locfileid: "43109967"
---
# <a name="operationalize-a-data-analytics-pipeline"></a>Zprovoznění kanálu datových analýz

*Datové kanály* underly mnoho řešení pro analýzu dat. Jak název napovídá, datového kanálu přijímá nezpracovaná data vyčistí a změní její tvar podle potřeby a potom obvykle provádí výpočty nebo agregace před uložením zpracovaná data. Zpracovaná data je využívána klientů, sestavy nebo rozhraní API. Datový kanál nezajistil opakovatelné výsledky, ať už podle plánu nebo aktivovaného nová data.

Tento článek popisuje, jak zprovoznit datových kanálů pro opakovatelnost použití Oozie s na clusterech HDInsight Hadoop. Ukázkový scénář vás provede datovým kanálem, který připraví a zpracovává data časových řad letu letecká společnost.

V následujícím scénáři je vstupní data plochého souboru, který obsahuje dávku zapisovači letových údajů na jeden měsíc. Tato letu data zahrnují informace, jako jsou letiště původu a cíle, mil předávány, odeslání a přijetí časy a tak dále. Cílem při vytváření tohoto kanálu je slouží ke shrnutí denní výkon letecká společnost, kde každý letecká společnost má jeden řádek pro každý den s průměrné zpoždění odeslání a přijetí během několika minut a celková délka předávány, daný den.

| ROK | MĚSÍC | DAY_OF_MONTH | POSKYTOVATEL SLUŽEB |AVG_DEP_DELAY | AVG_ARR_DELAY |TOTAL_DISTANCE |
| --- | --- | --- | --- | --- | --- | --- |
| 2017 | 1 | 3 | AA | 10.142229 | 7.862926 | 2644539 |
| 2017 | 1 | 3 | STEJNĚ JAKO | 9.435449 | 5.482143 | 572289 |
| 2017 | 1 | 3 | DISTRIBUČNÍ SEZNAM | 6.935409 | -2.1893024 | 1909696 |

Příklad kanálu počká, až dorazí nové časové období na zapisovači letových údajů a pak ukládá tyto informace podrobné letu do svého datového skladu Hive pro dlouhodobé analýzy. Kanál vytvoří také mnohem menší datové sady, který shrnuje jenom denní zapisovači letových údajů. Tento denní souhrn letů odesílat do služby SQL database poskytuje sestavy, například pro web.

Následující diagram znázorňuje kanál příklad.

![Flight datového kanálu](./media/hdinsight-operationalize-data-pipeline/pipeline-overview.png)

## <a name="oozie-solution-overview"></a>Přehled řešení Oozie

Tento kanál používá Apache Oozie spouští v clusteru HDInsight Hadoop.

Oozie popisuje její kanály z hlediska *akce*, *pracovních postupů*, a *koordinátoři*. Akce určují samotnou práci, abyste mohli provést, jako je například spuštění dotazu Hive. Pracovní postupy definují posloupnost akcí. Koordinátoři definovat plán, kdy se spustí pracovní postup. Koordinátoři mohou také čekat na dostupnost nových dat. před spuštěním instance pracovního postupu.

Následující diagram znázorňuje hlavnímu návrhu tohoto příkladu Oozie kanálu.

![Oozie letu datového kanálu](./media/hdinsight-operationalize-data-pipeline/pipeline-overview-oozie.png)

### <a name="provision-azure-resources"></a>Zřízení prostředků Azure

Tento kanál vyžaduje databázi SQL Azure a cluster HDInsight Hadoop ve stejném umístění. Azure SQL Database ukládá obou souhrn dat vytvářených úložiště metadat Oozie a kanálu.

#### <a name="provision-azure-sql-database"></a>Zřízení Azure SQL Database

1. Pomocí webu Azure portal vytvořte novou skupinu prostředků s názvem `oozie` tak, aby obsahovala všechny prostředky používané v tomto příkladu.
2. V rámci `oozie` skupinu prostředků, zřízení Azure SQL Server a databáze. Není nutné databáze větší než cenové úrovně S1 Standard.
3. Pomocí webu Azure portal, přejděte do podokna pro vaše nově nasazená SQL Database a vyberte **nástroje**.

    ![Tlačítko nástroje](./media/hdinsight-operationalize-data-pipeline/sql-db-tools.png)

4. Vyberte **editoru dotazů**.

    ![Tlačítko editoru dotazů](./media/hdinsight-operationalize-data-pipeline/sql-db-query-editor.png)

5. V **editoru dotazů** vyberte **přihlášení**.

    ![Tlačítka pro přihlášení](./media/hdinsight-operationalize-data-pipeline/sql-db-login1.png)

6. Zadejte svoje přihlašovací údaje SQL Database a vyberte **OK**.

   ![Přihlašovací formulář](./media/hdinsight-operationalize-data-pipeline/sql-db-login2.png)

7. V oblasti textového editoru dotazů zadejte následující příkazy SQL vytvořit `dailyflights` tabulku, která se uloží souhrnná data z každé spuštění kanálu.

    ```
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

8. Vyberte **spustit** k provedení příkazů SQL.

    ![Tlačítko pro spuštění](./media/hdinsight-operationalize-data-pipeline/sql-db-run.png)

Azure SQL Database je teď připravený.

#### <a name="provision-an-hdinsight-hadoop-cluster"></a>Zřídit Cluster systému Hadoop HDInsight

1. Na webu Azure Portal, vyberte **+ nová** a vyhledejte HDInsight.
2. Vyberte **Vytvořit**.
3. V podokně základy zadejte jedinečný název pro váš cluster a vaše předplatné Azure.

    ![Název clusteru HDInsight a předplatné](./media/hdinsight-operationalize-data-pipeline/hdi-name-sub.png)

4. V **typ clusteru** podokně, vyberte **Hadoop** typ, clusteru **Linux** operačního systému a nejnovější verzi clusteru HDInsight. Nechte **vrstvy clusteru** na **standardní**.

    ![Typ clusteru HDInsight](./media/hdinsight-operationalize-data-pipeline/hdi-cluster-type.png)

5. Zvolte **vyberte** použít výběru typu clusteru.
6. Dokončení **Základy** podokně poskytuje přihlašovacího hesla a výběrem vašeho `oozie` ze seznamu skupinu prostředků a pak vyberte **Další**.

    ![Základní informace o HDInsight podokno](./media/hdinsight-operationalize-data-pipeline/hdi-basics.png)

7. V **úložiště** podokno, ponechejte tuto položku z primárního úložiště zadejte nastavenou na **služby Azure Storage**vyberte **vytvořit nový**a zadejte název pro nový účet.

    ![Nastavení účtu úložiště HDInsight](./media/hdinsight-operationalize-data-pipeline/hdi-storage.png)

8. Pro **nastavení Metastoru**v části **vybrat databázi SQL pro podregistr**, zvolte databázi, kterou jste vytvořili dřív.

    ![Nastavení Metastoru Hive v HDInsight](./media/hdinsight-operationalize-data-pipeline/hdi-metastore-hive.png)

9. Vyberte **ověřit databázi SQL**.

    ![Ověření Metastore Hive v HDInsight](./media/hdinsight-operationalize-data-pipeline/hdi-authenticate-sql.png)

10. Zadejte svoje uživatelské jméno pro databázi SQL a heslo a zvolte **vyberte**. 

       ![Metastore HDInsight Hive ověřování přihlášení](./media/hdinsight-operationalize-data-pipeline/hdi-authenticate-sql-login.png)

11. Zpět na **nastavení Metastoru** podokně, vyberte databázi pro Oozie metadata uložení a ověření, jako jste to udělali dříve. 

       ![Nastavení Metastoru HDInsight](./media/hdinsight-operationalize-data-pipeline/hdi-metastore-settings.png)

12. Vyberte **Další**.
13. Na **Souhrn** vyberte **vytvořit** k nasazení clusteru.

### <a name="verify-ssh-tunneling-setup"></a>Ověřte nastavení tunelování SSH

K zobrazení stavu koordinátor a instance pracovních postupů pomocí Oozie webovou konzolu, nastavení tunelu SSH ke clusteru HDInsight. Další informace najdete v tématu [tunel SSH](hdinsight-linux-ambari-ssh-tunnel.md).

> [!NOTE]
> Můžete také použít Chrome se [Foxy Proxy](https://getfoxyproxy.org/) rozšíření pro procházení webových prostředků vašeho clusteru přes tunelové propojení SSH. Konfigurace proxy serveru všechny žádosti přes hostitele `localhost` na port na tunel 9876. Tento přístup je kompatibilní s subsystém Windows pro Linux, označované také jako Bash on Windows 10.

1. Spusťte následující příkaz pro otevření tunelového propojení SSH k vašemu clusteru:

    ```
    ssh -C2qTnNf -D 9876 sshuser@[CLUSTERNAME]-ssh.azurehdinsight.net
    ```

2. Ověřte, zda že je tak, že přejdete Ambari na hlavního uzlu tak, že přejdete na funkční tunelového propojení:

    http://headnodehost:8080

3. Přístup **Oozie Webová konzola** ve Ambari, **Oozie**, **rychlé odkazy**a pak vyberte **Oozie Webová konzola**.

### <a name="configure-hive"></a>Konfigurace Hive

1. Stáhněte si příklad souboru .csv, který obsahuje zapisovači letových údajů na jeden měsíc. Stáhněte si svůj soubor ZIP `2017-01-FlightData.zip` z [úložiště HDInsight Github](https://github.com/hdinsight/hdinsight-dev-guide) a rozbalte ho do souboru CSV `2017-01-FlightData.csv`. 

2. Zkopírujte tento soubor CSV až do účtu služby Azure Storage, které jsou připojené ke clusteru HDInsight a jeho umístění `/example/data/flights` složky.

Můžete zkopírovat soubor pomocí spojovacího bodu služby v vaše `bash` prostředí relace.

1. Spojovací bod služby pomocí zkopírovat soubory z místního počítače do místního úložiště vašeho hlavního uzlu clusteru HDInsight.

    ```bash
    scp ./2017-01-FlightData.csv sshuser@[CLUSTERNAME]-ssh.azurehdinsight.net:2017-01-FlightData.csv
    ```

2. Pomocí příkazu HDFS zkopírovat soubor z hlavního uzlu místního úložiště do služby Azure Storage.

    ```bash
    hdfs dfs -put ./2017-01-FlightData.csv /example/data/flights/2017-01-FlightData.csv
    ```

Ukázková data jsou teď k dispozici. Kanál však vyžaduje dvě tabulky Hive pro zpracování, jeden pro příchozí data (`rawFlights`) a jeden pro souhrnná data (`flights`). Vytvořte tyto tabulky v Ambari následujícím způsobem.

1. Přihlaste se k Ambari tak, že přejdete do [ http://headnodehost:8080 ](http://headnodehost:8080).
2. V seznamu služeb vyberte **Hive**.

    ![Výběr v Ambari Hive](./media/hdinsight-operationalize-data-pipeline/hdi-ambari-services-hive.png)

3. Vyberte **přejít na zobrazení** vedle popisku 2.0 zobrazení Hive.

    ![Výběr zobrazení Hive v Ambari](./media/hdinsight-operationalize-data-pipeline/hdi-ambari-services-hive-summary.png)

4. Do textového pole dotaz, vložte následující příkazy k vytvoření `rawFlights` tabulky. `rawFlights` Tabulka obsahuje soubory sdíleného svazku clusteru v rámci schématu na pro čtení `/example/data/flights` složky ve službě Azure Storage. 

    ```
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

5. Vyberte **Execute** k vytvoření této tabulky.

    ![Dotaz Hive v Ambari](./media/hdinsight-operationalize-data-pipeline/hdi-ambari-services-hive-query.png)

6. Chcete-li vytvořit `flights` tabulky, nahraďte text v textovém poli dotazu následující příkazy. `flights` Tabulek je spravovaná tabulky Hive, která dělí data do něho načtené za rok, měsíc a den v měsíci. Tato tabulka bude obsahovat všechny historické zapisovači letových údajů, s nejnižší členitosti ve zdrojových datech jeden řádek na cestě k dispozici.

    ```
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

7. Vyberte **Execute** k vytvoření této tabulky.

### <a name="create-the-oozie-workflow"></a>Vytvoření pracovního postupu Oozie

Kanály obvykle zpracovávat data v dávkách v daném časovém intervalu. V takovém případě kanálu zpracovává každý den zapisovači letových údajů. Tento přístup umožňuje pro vstupní soubory sdíleného svazku clusteru můžete přejít denně, týdně, měsíčně nebo ročně.

Ukázkový pracovní postup zpracovává letu data ve dnech, v tři hlavní kroky:

1. Spuštění dotazu Hive k extrakci dat pro tento den rozsah dat ze souboru CSV zdrojového reprezentována `rawFlights` tabulky a vložení dat do `flights` tabulky.
2. Spuštění dotazu Hive dynamicky se vytvářejí pracovní tabulky v podregistru za den, který obsahuje kopii zapisovači letových údajů automaticky shrnutý podle dne a poskytovatel služeb.
3. Použití Apache Sqoop pro zkopírování všech dat z každodenní pracovní tabulky v Hive do cíle `dailyflights` tabulky ve službě Azure SQL Database. Sqoop načteme řádků zdroje dat tabulky Hive, které se nacházejí ve službě Azure Storage a je načte do SQL Database s použitím JDBC připojení.

V pracovním postupu Oozie jsou koordinovaný tyto tři kroky. 

1. Vytvořit dotaz v souboru `hive-load-flights-partition.hql`.

    ```
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

    Použijte syntaxi proměnných Oozie `${variableName}`. Tyto proměnné se nastavují `job.properties` sdílené, jak je popsáno v následujícím kroku. Oozie se nahradí skutečnými hodnotami v době běhu.

2. Vytvořit dotaz v souboru `hive-create-daily-summary-table.hql`.

    ```
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

    Tento dotaz vytvoří pracovní tabulky, který bude ukládat pouze souhrnná data pro jeden den, poznamenejte si hodnotu příkazu SELECT, který vypočítává průměrné zpoždění a celkový počet vzdálenost předávány dopravce za den. Data vložená do této tabulky uloženou v známé umístění (cesta indikován proměnnou hiveDataFolder) tak, aby jej můžete použít jako zdroj pro Sqoop v dalším kroku.

3. Spusťte následující příkaz Sqoop.

    ```
    sqoop export --connect ${sqlDatabaseConnectionString} --table ${sqlDatabaseTableName} --export-dir ${hiveDataFolder} -m 1 --input-fields-terminated-by "\t"
    ```

Tyto tři kroky jsou vyjádřeny jako tři samostatné akce v následujícím souboru pracovního postupu Oozie s názvem `workflow.xml`.

```
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
            <archive>sqljdbc41.jar</archive>
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

Dva dotazy Hive jsou přístupné prostřednictvím jejich cesty ve službě Azure Storage a zbývající hodnoty proměnných jsou poskytovány následující `job.properties` souboru. Tento soubor nastaví spuštění pro data 3. ledna 2017 pracovního postupu.

```
nameNode=wasbs://[CONTAINERNAME]@[ACCOUNTNAME].blob.core.windows.net
jobTracker=hn0-[CLUSTERNAME].[UNIQUESTRING].dx.internal.cloudapp.net:8050
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

Následující tabulka shrnuje každého vlastností a označuje místo, kde najdete hodnoty pro konkrétní prostředí.

| Vlastnost | Hodnota zdroje |
| --- | --- |
| NameNode | Úplná cesta ke kontejneru úložiště Azure připojené ke clusteru HDInsight. |
| jobTracker | Interní název hostitele na aktivním clusteru YARN hlavního uzlu. Na domovské stránce Ambari vyberte ze seznamu služeb YARN a pak zvolte aktivní Resource Manageru. Název hostitele identifikátoru URI se zobrazí v horní části stránky. Připojte za ní port 8050. |
| queueName | Název fronty YARN při plánování akcí Hive. Ponechte jako výchozí. |
| oozie.use.system.libpath | Ponechejte tuto položku jako true. |
| rozsah základu aplikace | Cesta podsložky ve službě Azure Storage, ve kterém nasadíte pracovní postup Oozie a podpůrné soubory. |
| oozie.wf.application.path | Umístění pracovního postupu Oozie `workflow.xml` ke spuštění. |
| hiveScriptLoadPartition | Cesta k souboru dotazu Hive ve službě Azure Storage `hive-load-flights-partition.hql`. |
| hiveScriptCreateDailyTable | Cesta k souboru dotazu Hive ve službě Azure Storage `hive-create-daily-summary-table.hql`. |
| hiveDailyTableName | Dynamicky generovaný název, který chcete použít pro pracovní tabulky. |
| hiveDataFolder | Cesta data obsažená ve pracovní tabulky ve službě Azure Storage. |
| sqlDatabaseConnectionString | JDBC syntaxe připojovacího řetězce k databázi SQL Azure. |
| sqlDatabaseTableName | Název tabulky v Azure SQL Database, do kterého se vložily řádky souhrnu. Nechte `dailyflights`. |
| rok | Složku roku dne, pro které let se souhrny zpracovávají. Nechte, jak je. |
| měsíc | Komponentu měsíc dne, pro které let se souhrny zpracovávají. Nechte, jak je. |
| den | Den v měsíci dne, pro které let se souhrny zpracovávají. Nechte, jak je. |

> [!NOTE]
> Nezapomeňte aktualizovat kopii `job.properties` souboru s hodnotami, které jsou specifické pro vaše prostředí, aby bylo možné nasazení a spuštění pracovního postupu Oozie.

### <a name="deploy-and-run-the-oozie-workflow"></a>Nasazujte a spouštějte pracovní postup Oozie

Nasazení pracovního postupu Oozie pomocí spojovacího bodu služby z relace prostředí bash (`workflow.xml`), dotazy Hive (`hive-load-flights-partition.hql` a `hive-create-daily-summary-table.hql`) a konfigurace úlohy (`job.properties`).  V Oozie, pouze `job.properties` souboru může existovat v místním úložišti hlavního uzlu. Všechny ostatní soubory musí být uložen v HDFS, v tomto případu služby Azure Storage. Akce Sqoop používá pracovní postup závisí na ovladač JDBC pro komunikaci s databází SQL, který musí být zkopírován do rozhraní HDFS z hlavního uzlu.

1. Vytvořte `load_flights_by_day` podsložky pod cesty uživatele v místním úložišti k hlavnímu uzlu.

        ssh sshuser@[CLUSTERNAME]-ssh.azurehdinsight.net 'mkdir load_flights_by_day'

2. Zkopírujte všechny soubory v aktuálním adresáři ( `workflow.xml` a `job.properties` soubory) až `load_flights_by_day` podsložky.

        scp ./* sshuser@[CLUSTERNAME]-ssh.azurehdinsight.net:load_flights_by_day

3. Připojte se přes SSH hlavního uzlu a přejděte `load_flights_by_day` složky.

        ssh sshuser@[CLUSTERNAME]-ssh.azurehdinsight.net
        cd load_flights_by_day

4. Zkopírujte soubory pracovního postupu do rozhraní HDFS.

        hdfs dfs -put ./* /oozie/load_flights_by_day

5. Kopírování `sqljdbc41.jar` z místního hlavního uzlu do složky pracovního postupu v HDFS:

        hdfs dfs -put /usr/share/java/sqljdbc_4.1/enu/sqljdbc*.jar /oozie/load_flights_by_day

6. Spuštění pracovního postupu.

        oozie job -config job.properties -run

7. Sledujte stav pomocí Oozie webové konzole. V rámci Ambari, vyberte **Oozie**, **rychlé odkazy**a potom **Oozie Webová konzola**. V části **úlohy pracovního postupu** kartu, vyberte možnost **všechny úlohy**.

    ![Pracovní postupy Oozie webové konzoly](./media/hdinsight-operationalize-data-pipeline/hdi-oozie-web-console-workflows.png)

8. Pokud je stav úspěšné, dotazování tabulky databáze SQL zobrazíte vložených řádků. Pomocí webu Azure portal, přejděte do podokna pro vaši databázi SQL, vyberte **nástroje**a otevřete **editoru dotazů**.

        SELECT * FROM dailyflights

Teď, když pracovní postup běží za den jeden test, může obtékat tento pracovní postup službě Koordinátor DTC, která plánuje pracovního postupu, aby byly spuštěny každý den.

### <a name="run-the-workflow-with-a-coordinator"></a>Spuštění pracovního postupu se koordinátorem

Naplánování tento pracovní postup, aby byla spouštěna každý den (nebo všechny dny v období), můžete použít koordinátora. Koordinátor je definována v souboru XML, třeba `coordinator.xml`:

```
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

Jak vidíte, je většinou koordinátor právě předávání konfiguračních informací instance pracovního postupu. Existují však několik důležitých položek provádět volání.

* Bod 1: `start` a `end` atributy na `coordinator-app` elementu samotného řízení časový interval nad tím, které spouští koordinátor.

    ```
    <coordinator-app ... start="2017-01-01T00:00Z" end="2017-01-05T00:00Z" frequency="${coord:days(1)}" ...>
    ```

    Koordinátor je zodpovědná za plánování akce v rámci `start` a `end` rozsah, kalendářních dat podle intervalu určeném `frequency` atribut. Všechny naplánované akce pak spustí pracovní postup podle konfigurace. Ve výše uvedená definice koordinátor koordinátor je nakonfigurovaný ke spouštění akcí z 1. ledna 2017 na 5. ledna 2017. Frekvence je nastavená na 1 den podle [jazyk výrazů Oozie](http://oozie.apache.org/docs/4.2.0/CoordinatorFunctionalSpec.html#a4.4._Frequency_and_Time-Period_Representation) frekvence výraz `${coord:days(1)}`. Výsledkem je koordinátor plánování akci (a tedy pracovního postupu) jednou za den. Pro rozsahy kalendářních dat, které jsou v minulosti, jako v následujícím příkladu bude naplánováno akce spustit bez zpoždění. Počáteční datum, ze kterých je naplánované spuštění akce je volána *nominální čas*. Například ke zpracování dat pro 1. ledna 2017 se koordinátor naplánuje akcí s dobou nominální 2017-01-01T00:00:00 GMT.

* Bod 2: v rámci období pracovního postupu `dataset` element určuje, kde se mají hledat data pro konkrétní rozsah v HDFS a konfiguruje jak Oozie Určuje, zda je k dispozici data ještě pro zpracování.

    ```
    <dataset name="ds_input1" frequency="${coord:days(1)}" initial-instance="2016-12-31T00:00Z" timezone="UTC">
        <uri-template>${sourceDataFolder}${YEAR}-${MONTH}-FlightData.csv</uri-template>
        <done-flag></done-flag>
    </dataset>
    ```

    Cesta k datům v HDFS je dynamicky sestavena podle výrazu součástí `uri-template` elementu. V tomto koordinátor frekvenci jeden den se používá také v datové sadě. Když akce, které jsou naplánovány (a definuje s úspěšností nominální), při počáteční a koncové datum v ovládacím prvku koordinátor element `initial-instance` a `frequency` na datovou sadu řídí výpočet kalendářního data, která se používá při konstrukci `uri-template`. V takovém případě nastavte počáteční instance na jeden den před začátkem koordinátor zajistit, že se vybere první den tohoto data za (1/1. června 2017). Výpočet datové sady data provede dopředné obnovení z hodnoty `initial-instance` posunutí (31/12/2016) v přírůstcích po frekvence datové sady (1 den) dokud nenajde poslední datum, který nepředá nominální čas nastavil koordinátor (2017-01-01T00:00:00 GMT pro první akci).

    Prázdné `done-flag` element označuje, že když Oozie kontroly na přítomnost vstupní data v určené době, Oozie Určuje data, zda je k dispozici podle přítomnosti adresář nebo soubor. V tomto případě je přítomnost souboru csv. Pokud se nachází soubor csv, Oozie předpokládá data připravená a spustí instanci pracovního postupu při zpracování souboru. Pokud není dostupný k dispozici žádný soubor csv, Oozie předpokládá, že data jsou ještě nejsou připravené a tento běh pracovního postupu přejde do stavu čekání.

* Bod 3: `data-in` prvek určuje konkrétní časové razítko pro použití jako o nominálních při nahrazení hodnot v `uri-template` pro přidružený objekt dataset.

    ```
    <data-in name="event_input1" dataset="ds_input1">
        <instance>${coord:current(0)}</instance>
    </data-in>
    ```

    V takovém případě nastavte instance na výraz `${coord:current(0)}`, který se přeloží na základě nominální doby akce podle původního naplánované koordinátorem. Jinými slovy, pokud koordinátor naplánuje akci pro spuštění pomocí nominální času 01/01/2017, 01/01/2017 se, co se používá k nahrazení roku (2017) a měsíce (01) proměnné v šablona identifikátoru URI. Jakmile se šablona identifikátoru URI je vypočítán pro tuto instanci, Oozie ověří, zda očekávaný adresář nebo soubor je k dispozici a odpovídajícím způsobem naplánuje další spuštění pracovního postupu.

Tři předchozí body se dá pozastavit situace, kdy koordinátor naplánuje zpracování datového zdroje v podobě – denně. 

* Bod 1: Koordinátor začíná nominální datum 2017-01-01.

* Bod 2: Oozie vyhledá data, které jsou k dispozici v `sourceDataFolder/2017-01-FlightData.csv`.

* Bod 3: Když Oozie vyhledá soubor, naplánuje instance pracovního postupu, který bude zpracovávat data pro 2017-01-01. Oozie pak bude pokračovat zpracování 2017-01-02. Toto hodnocení se opakuje až, ale bez zahrnutí 2017-01-05.

S pracovními postupy, konfigurace koordinátor ve smyslu `job.properties` soubor, který je nadmnožinou nastavení používané tímto pracovním postupem.

```
nameNode=wasbs://[CONTAINERNAME]@[ACCOUNTNAME].blob.core.windows.net
jobTracker=hn0-[CLUSTERNAME].[UNIQUESTRING].dx.internal.cloudapp.net:8050
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

Pouze nové vlastnosti, kterou v tomto `job.properties` souboru jsou:

| Vlastnost | Hodnota zdroje |
| --- | --- |
| oozie.coord.application.path | Určuje umístění `coordinator.xml` soubor, který obsahuje Oozie coordinator ke spuštění. |
| hiveDailyTableNamePrefix | Předpona použitá při vytváření dynamicky název tabulky v pracovní tabulce. |
| hiveDataFolderPrefix | Předpona cesty, kam se budou ukládat všechny přípravné tabulky. |

### <a name="deploy-and-run-the-oozie-coordinator"></a>Nasazení a spuštění Oozie Coordinator

Spuštění kanálu se koordinátorem, pokračujte podobným způsobem jako u pracovního postupu, s výjimkou pracujete ze složky jednu úroveň nad složku, která obsahuje váš pracovní postup. Tato složka konvence koordinátoři odděluje od pracovních postupů na disku, takže jeden koordinátor můžete přidružit různé podřízené pracovní postupy.

1. Pomocí spojovacího bodu služby z místního počítače koordinátor souborů až do místního úložiště k hlavnímu uzlu clusteru.

    ```bash
    scp ./* sshuser@[CLUSTERNAME]-ssh.azurehdinsight.net:~
    ```

2. Připojte se přes SSH hlavního uzlu.

    ```bash
    ssh sshuser@[CLUSTERNAME]-ssh.azurehdinsight.net 
    ```

3. Zkopírujte soubory koordinátor do rozhraní HDFS.

    ```bash
    hdfs dfs -put ./* /oozie/
    ```

4. Spusťte koordinátor.

    ```bash
    oozie job -config job.properties -run
    ```

5. Ověření stavu pomocí webové konzole Oozie tento výběr času **koordinátor úlohy** kartu a potom **všechny úlohy**.

    ![Oozie webové konzoly koordinátor úlohy](./media/hdinsight-operationalize-data-pipeline/hdi-oozie-web-console-coordinator-jobs.png)

6. Vyberte instanci koordinátor zobrazíte seznam akcí, naplánované. V takovém případě byste měli vidět čtyř akcí s dobou nominální v rozsahu od 1/1. června 2017 do 1/4/2017.

    ![Oozie webové konzoly koordinátor úlohy](./media/hdinsight-operationalize-data-pipeline/hdi-oozie-web-console-coordinator-instance.png)

    Všechny akce v tomto seznamu odpovídá instance pracovního postupu, který zpracovává data, za jeden den, kde je označen nominální čas začátku daný den.

## <a name="next-steps"></a>Další postup

* [Dokumentace Apache Oozie](http://oozie.apache.org/docs/4.2.0/index.html)

<!-- * Build the same pipeline [using Azure Data Factory](tbd.md).  -->
