---
title: Zprovoznit kanálu analýzy dat – Azure | Microsoft Docs
description: Nastavení a kanál dat příklad, který je aktivován nová data a vytvoří stručným výsledky spouštět.
services: hdinsight
documentationcenter: ''
author: ashishthaps
manager: jhubbard
editor: cgronlun
ms.assetid: ''
ms.service: hdinsight
ms.custom: hdinsightactive
ms.devlang: na
ms.topic: article
ms.date: 01/11/2018
ms.author: ashishth
ms.openlocfilehash: 7ac1ed0db15d91ef8af009c879c3634148826286
ms.sourcegitcommit: 9cdd83256b82e664bd36991d78f87ea1e56827cd
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/16/2018
ms.locfileid: "31392183"
---
# <a name="operationalize-a-data-analytics-pipeline"></a>Zprovoznění kanálu datových analýz

*Datových kanálů* underly mnoho řešení pro analýzu dat. Jak již název naznačuje, datovém kanálu přebírá nezpracovaná data, vyčistí a změní tvar podle potřeby a potom obvykle provede výpočty nebo agregace před ukládání zpracovaná data. Zpracovaná data je spotřebované klienty, sestavy nebo rozhraní API. Kanál dat musíte zadat opakovatelných výsledky, zda podle plánu nebo když aktivovány nová data.

Tento článek popisuje postup zprovoznit datových kanálů pro opakovatelnost pomocí Oozie systémem clusterů systému HDInsight Hadoop. Ukázkový scénář vás provede procesem datový kanál, který připraví a zpracovává data časové řady letu letecká společnost.

V následujícím scénáři je vstupní data plochý soubor obsahující dávky dat letu na jeden měsíc. Tato data pohybující se zahrnují informace, jako jsou letiště původní a cílové, miles vede, odeslání a přijetí časy a tak dále. Cíl s tímto kanálem je shrnutí denní výkonu letecká společnost, kde každý letecká společnost má jeden řádek pro každý den s průměrnou odeslání a přijetí zpoždění v minutách a celková délka vede daný den.

| ROK | MĚSÍC | DAY_OF_MONTH | POSKYTOVATEL |AVG_DEP_DELAY | AVG_ARR_DELAY |TOTAL_DISTANCE |
| --- | --- | --- | --- | --- | --- | --- |
| 2017 | 1 | 3 | AA | 10.142229 | 7.862926 | 2644539 |
| 2017 | 1 | 3 | STEJNĚ JAKO | 9.435449 | 5.482143 | 572289 |
| 2017 | 1 | 3 | DL | 6.935409 | -2.1893024 | 1909696 |

Příklad kanálu počká, až dorazí data pohybující se nové časové období a pak ukládá tyto informace podrobné letu do datového skladu Hive pro dlouhodobé analýzy. Kanál také vytvoří mnohem menší datovou sadu, která shrnuje pouze denní data letu. Tato denní souhrnná data pohybující se posílá k databázi SQL vytvářet sestavy, například pro web.

Následující obrázek znázorňuje příklad kanálu.

![Letu datovém kanálu](./media/hdinsight-operationalize-data-pipeline/pipeline-overview.png)

## <a name="oozie-solution-overview"></a>Přehled řešení Oozie

Tento kanál používá Apache Oozie spuštěné v clusteru HDInsight Hadoop.

Oozie popisuje jeho kanály z hlediska *akce*, *pracovních*, a *koordinátoři*. Akce určí samotnou práci chcete provést, jako je například spuštění dotazů Hive. Pracovní postupy definují posloupnost akcí. Koordinátoři definujte plán pro spuštění pracovního postupu. Koordinátoři může také čekat na dostupnost nových dat před spuštěním instance pracovního postupu.

Následující diagram znázorňuje hlavnímu návrhu tento příklad Oozie kanálu.

![Oozie letu datovém kanálu](./media/hdinsight-operationalize-data-pipeline/pipeline-overview-oozie.png)

### <a name="provision-azure-resources"></a>Zřízení prostředků Azure

Tento kanál vyžaduje databázi SQL Azure a cluster HDInsight Hadoop ve stejném umístění. Databáze SQL Azure ukládá obou souhrnná data vytvořená kanálu a úložišti metadata Oozie.

#### <a name="provision-azure-sql-database"></a>Zřízení Azure SQL Database

1. Pomocí portálu Azure, vytvořte novou skupinu prostředků s názvem `oozie` tak, aby obsahovala všechny prostředky používané v tomto příkladu.
2. V rámci `oozie` skupinu prostředků, přidělení k Azure SQL serveru a databáze. Není nutné databáze větší než S1 Standard cenovou úroveň.
3. Pomocí portálu Azure, přejděte do podokna pro nově nasazené databáze SQL a vyberte **nástroje**.

    ![Tlačítko nástroje](./media/hdinsight-operationalize-data-pipeline/sql-db-tools.png)

4. Vyberte **Editor dotazů**.

    ![Tlačítko Editor dotazů](./media/hdinsight-operationalize-data-pipeline/sql-db-query-editor.png)

5. V **Editor dotazů** podokně, vyberte **přihlášení**.

    ![Tlačítko pro přihlášení](./media/hdinsight-operationalize-data-pipeline/sql-db-login1.png)

6. Zadejte své přihlašovací údaje databáze SQL a vyberte **OK**.

   ![Přihlašovací formulář](./media/hdinsight-operationalize-data-pipeline/sql-db-login2.png)

7. V oblasti textového editoru dotazů, zadejte následující příkazy SQL k vytvoření `dailyflights` tabulku, která se uloží souhrnná data z každé spuštění kanálu.

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

8. Vyberte **spustit** pro spuštění příkazů SQL.

    ![Tlačítko Spustit](./media/hdinsight-operationalize-data-pipeline/sql-db-run.png)

Databáze SQL Azure je nyní připravena.

#### <a name="provision-an-hdinsight-hadoop-cluster"></a>Zřízení clusteru HDInsight Hadoop

1. Na portálu Azure vyberte **+ nový** a vyhledejte HDInsight.
2. Vyberte **Vytvořit**.
3. V podokně základy zadejte jedinečný název pro váš cluster a zvolte předplatné Azure.

    ![Název clusteru HDInsight a předplatného](./media/hdinsight-operationalize-data-pipeline/hdi-name-sub.png)

4. V **clusteru typ** podokně, vyberte **Hadoop** clusteru typu **Linux** operačního systému a nejnovější verze clusteru HDInsight. Ponechte **vrstvy clusteru** v **standardní**.

    ![Typ clusteru HDInsight](./media/hdinsight-operationalize-data-pipeline/hdi-cluster-type.png)

5. Zvolte **vyberte** použít výběr typu clusteru.
6. Dokončení **Základy** podokně zadání hesla přihlášení a výběrem vaší `oozie` prostředků skupiny ze seznamu a pak vyberte **Další**.

    ![Základy HDInsight podokně](./media/hdinsight-operationalize-data-pipeline/hdi-basics.png)

7. V **úložiště** podokně, ponechejte nastaven na typ primárního úložiště **Azure Storage**, vyberte **vytvořit nový**a zadejte název pro nový účet.

    ![Nastavení účtu úložiště HDInsight](./media/hdinsight-operationalize-data-pipeline/hdi-storage.png)

8. Pro **Metaúložiště nastavení**v části **vyberte databázi SQL pro Hive**, vyberte databázi, který jste předtím vytvořili.

    ![Nastavení Metaúložiště HDInsight Hive](./media/hdinsight-operationalize-data-pipeline/hdi-metastore-hive.png)

9. Vyberte **ověřování databáze SQL**.

    ![Ověření Metaúložiště Hive HDInsight](./media/hdinsight-operationalize-data-pipeline/hdi-authenticate-sql.png)

10. Zadejte databáze SQL uživatelské jméno a heslo a zvolte **vyberte**. 

       ![Metaúložiště HDInsight Hive ověření přihlášení](./media/hdinsight-operationalize-data-pipeline/hdi-authenticate-sql-login.png)

11. Zpět na **Metaúložiště nastavení** podokně, vyberte databázi pro Oozie metadata ukládat a ověření, protože jste provedli dříve. 

       ![Nastavení HDInsight Metaúložiště.](./media/hdinsight-operationalize-data-pipeline/hdi-metastore-settings.png)

12. Vyberte **Next** (Další).
13. Na **Souhrn** podokně, vyberte **vytvořit** k nasazení clusteru.

### <a name="verify-ssh-tunneling-setup"></a>Ověřte instalaci tunelování SSH

Zobrazení stavu coordinator a instancí pracovních postupů pomocí webové konzole Oozie, nastavení tunelu SSH ke svému clusteru HDInsight. Další informace najdete v tématu [tunelu SSH](hdinsight-linux-ambari-ssh-tunnel.md).

> [!NOTE]
> Můžete taky Chrome s [Foxy Proxy](https://getfoxyproxy.org/) rozšíření procházet váš cluster webovým prostředkům přes tunelové propojení SSH. Konfigurace proxy serveru všechny žádosti prostřednictvím hostitele `localhost` na port přes tunel 9876. Tento přístup je kompatibilní s Windows subsystém pro systémy Linux, také známé jako Bash ve Windows 10.

1. Spusťte následující příkaz pro otevření tunelového propojení SSH do clusteru:

    ```
    ssh -C2qTnNf -D 9876 sshuser@[CLUSTERNAME]-ssh.azurehdinsight.net
    ```

2. Ověřte, zda že je tak, že přejdete do Ambari hlavního uzlu procházením provozní tunelové propojení:

    http://headnodehost:8080

3. Pro přístup k **Oozie webové konzole** v rámci stejné, vyberte **Oozie**, **rychlé odkazy**a potom vyberte **Oozie webové konzole**.

### <a name="configure-hive"></a>Konfigurace Hive

1. Stáhněte si příklad souboru .csv, který obsahuje data pohybující se na jeden měsíc. Stáhněte si svůj soubor ZIP `2017-01-FlightData.zip` z [úložiště HDInsight Github](https://github.com/hdinsight/hdinsight-dev-guide) a rozbalte ho do souboru CSV `2017-01-FlightData.csv`. 

2. Zkopírujte tento soubor CSV až účtu úložiště Azure připojit ke svému clusteru HDInsight a jeho umístění `/example/data/flights` složky.

Můžete zkopírovat soubor pomocí spojovací bod služby ve vašem `bash` prostředí shell relace.

1. Spojovací bod služby pomocí kopírování souborů z místního počítače do místního úložiště vaše hlavního uzlu clusteru HDInsight.

    ```bash
    scp ./2017-01-FlightData.csv sshuser@[CLUSTERNAME]-ssh.azurehdinsight.net:2017-01-FlightData.csv
    ```

2. Použijte příkaz HDFS se zkopírovat soubor z hlavního uzlu místního úložiště do úložiště Azure.

    ```bash
    hdfs dfs -put ./2017-01-FlightData.csv /example/data/flights/2017-01-FlightData.csv
    ```

Ukázková data je nyní k dispozici. Však kanálu dvou tabulek Hive vyžaduje pro zpracování, jednu pro příchozí data (`rawFlights`) a jeden pro souhrnná data (`flights`). Vytváření těchto tabulek v Ambari následujícím způsobem.

1. Přihlaste se k Ambari přechodem na [ http://headnodehost:8080 ](http://headnodehost:8080).
2. V seznamu služeb vyberte **Hive**.

    ![Výběr v Ambari Hive](./media/hdinsight-operationalize-data-pipeline/hdi-ambari-services-hive.png)

3. Vyberte **přejít na zobrazení** vedle popisek 2.0 zobrazení Hive.

    ![Výběr zobrazení Hive v Ambari](./media/hdinsight-operationalize-data-pipeline/hdi-ambari-services-hive-summary.png)

4. V oblasti dotazu text vložte následující příkazy k vytvoření `rawFlights` tabulky. `rawFlights` Tabulka obsahuje soubory sdíleného svazku clusteru v rámci schématu na pro čtení `/example/data/flights` složky ve službě Azure Storage. 

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

5. Vyberte **Execute** pro vytvoření tabulky.

    ![Dotaz Hive v Ambari](./media/hdinsight-operationalize-data-pipeline/hdi-ambari-services-hive-query.png)

6. Chcete-li vytvořit `flights` tabulky, nahraďte text v oblasti text dotazu se následující příkazy. `flights` Tabulka je spravovaný tabulku Hive, která oddíly data načtena do ní rok, měsíc a den v měsíci. Tato tabulka bude obsahovat všechna data historických letu, s nejnižší rozlišením součástí zdroj dat jeden řádek na cestě.

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

7. Vyberte **Execute** pro vytvoření tabulky.

### <a name="create-the-oozie-workflow"></a>Vytvoření pracovního postupu Oozie

Kanály obvykle zpracovávají data v dávkách v daném časovém intervalu. V takovém případě kanálu zpracovává data pohybující se každý den. Tento přístup umožňuje vstupní soubory CSV příchod denně, týdně, měsíčně nebo ročně.

Ukázkový pracovní postup zpracovává cestě data ve dnech, tři hlavní kroky:

1. Spouštění dotazů Hive extrahovat data pro daný den rozsah ze zdrojového souboru CSV reprezentována `rawFlights` tabulky a vkládání dat do `flights` tabulky.
2. Spouštění dotazů Hive vytvořit dynamicky pracovní tabulky v podregistru den, který obsahuje kopii dat letu souhrnu den a poskytovatel.
3. Použití Apache Sqoop zkopírovat všechna data z každodenní pracovní tabulky v podregistru do cílového umístění `dailyflights` tabulky v databázi SQL Azure. Sqoop přečte řádků zdroje z dat za tabulku Hive, které se nacházejí ve službě Azure Storage a je načte do databáze SQL pomocí JDBC připojení.

Tyto tři kroky jsou koordinuje pracovním postupu Oozie. 

1. Vytvoření dotazu v souboru `hive-load-flights-partition.hql`.

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

    Proměnné Oozie použijte syntaxi `${variableName}`. Tyto proměnné jsou nastavené `job.properties` souboru, jak je popsáno v následujícím kroku. Oozie nahradí skutečnými hodnotami za běhu.

2. Vytvoření dotazu v souboru `hive-create-daily-summary-table.hql`.

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

    Tento dotaz vytvoří pracovní tabulky, který bude ukládat pouze souhrnná data pro jeden den, poznamenejte si příkaz SELECT, který vypočítá průměrnou zpoždění a celkem vzdálenost vede podle poskytovatel za den. Data vložená do této tabulky uloží do vhodného umístění (cesta indikován proměnnou hiveDataFolder) tak, aby může sloužit jako zdroj pro Sqoop v dalším kroku.

3. Spusťte následující příkaz Sqoop.

    ```
    sqoop export --connect ${sqlDatabaseConnectionString} --table ${sqlDatabaseTableName} --export-dir ${hiveDataFolder} -m 1 --input-fields-terminated-by "\t"
    ```

Tyto tři kroky jsou vyjádřené jako tři samostatné akce v následující soubor pracovního postupu Oozie s názvem `workflow.xml`.

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

Dva dotazy Hive přistupují jejich cestu ve službě Azure Storage a zbývající hodnoty proměnné jsou poskytovány následující `job.properties` souboru. Tento soubor lze konfigurovat pro datum 3. ledna 2017 spouštění pracovního postupu.

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

Následující tabulka shrnuje každé z vlastností a určuje, kde můžete najít hodnoty pro svého vlastního prostředí.

| Vlastnost | Hodnota zdroje |
| --- | --- |
| NameNode | Úplná cesta ke kontejneru úložiště Azure připojit ke svému clusteru HDInsight. |
| jobTracker | Interní název hostitele pro aktivní clusteru YARN hlavního uzlu. Na domovské stránce Ambari YARN vyberte ze seznamu služeb, a potom vyberte aktivní Resource Manager. Název hostitele URI se zobrazí v horní části stránky. Připojí port 8050. |
| queueName | Název fronty YARN použité při plánování akce Hive. Ponechte jako výchozí. |
| oozie.use.system.libpath | Ponechejte jako true. |
| appBase | Cesta k požadované podsložky ve službě Azure Storage, kde můžete nasadit Oozie pracovního postupu a podpůrné soubory. |
| oozie.wf.application.path | Umístění pracovního postupu Oozie `workflow.xml` ke spuštění. |
| hiveScriptLoadPartition | Cesta ve službě Azure Storage k soubor dotazů Hive `hive-load-flights-partition.hql`. |
| hiveScriptCreateDailyTable | Cesta ve službě Azure Storage k soubor dotazů Hive `hive-create-daily-summary-table.hql`. |
| hiveDailyTableName | Dynamicky generovaném název má použít pro pracovní tabulku. |
| hiveDataFolder | Cesta ve službě Azure Storage data obsažená v pracovní tabulce. |
| sqlDatabaseConnectionString | Syntaxe JDBC připojovací řetězec k vaší databázi SQL Azure. |
| sqlDatabaseTableName | Název tabulky v databázi SQL Azure, do kterého jsou vloženy souhrnné řádky. Ponechte jako `dailyflights`. |
| rok | Komponentu roku den, pro které let se vypočítávají souhrny. Nechte tak, jak je. |
| měsíc | Komponentu měsíc dne, pro které let se vypočítávají souhrny. Nechte tak, jak je. |
| den | Den měsíce součást den, pro které let se vypočítávají souhrny. Nechte tak, jak je. |

> [!NOTE]
> Nezapomeňte aktualizovat kopii `job.properties` souboru s hodnotami, které jsou specifické pro vaše prostředí, než bude možné nasadit a spustit svůj workflow Oozie.

### <a name="deploy-and-run-the-oozie-workflow"></a>Nasazení a spuštění pracovního postupu Oozie

Použít spojovací bod služby z vaší relace bash k nasazení pracovního postupu Oozie (`workflow.xml`), dotazy Hive (`hive-load-flights-partition.hql` a `hive-create-daily-summary-table.hql`) a konfiguraci úloh (`job.properties`).  V Oozie, jenom `job.properties` souboru může existovat v místního úložiště headnode. Všechny ostatní soubory musí být uložen v HDFS v této případu Azure Storage. Akce Sqoop používá pracovní postup závisí na ovladač JDBC pro komunikaci s vaší databázi SQL, které musí zkopírovat z hlavního uzlu do HDFS.

1. Vytvořte `load_flights_by_day` podsložky pod uživatele cestu v místním úložišti hlavního uzlu.

        ssh sshuser@[CLUSTERNAME]-ssh.azurehdinsight.net 'mkdir load_flights_by_day'

2. Zkopírujte všechny soubory v aktuálním adresáři ( `workflow.xml` a `job.properties` soubory) až `load_flights_by_day` podsložky.

        scp ./* sshuser@[CLUSTERNAME]-ssh.azurehdinsight.net:load_flights_by_day

3. SSH do hlavního uzlu a přejděte do `load_flights_by_day` složky.

        ssh sshuser@[CLUSTERNAME]-ssh.azurehdinsight.net
        cd load_flights_by_day

4. Zkopírujte soubory pracovního postupu HDFS.

        hdfs dfs -put ./* /oozie/load_flights_by_day

5. Kopírování `sqljdbc41.jar` z hlavního uzlu místní složky pracovního postupu v HDFS:

        hdfs dfs -put /usr/share/java/sqljdbc_4.1/enu/sqljdbc*.jar /oozie/load_flights_by_day

6. Spuštění pracovního postupu.

        oozie job -config job.properties -run

7. Sledujte stav pomocí Oozie webové konzole. V rámci stejné, vyberte **Oozie**, **rychlé odkazy**a potom **Oozie webové konzole**. V části **úlohy pracovního postupu** vyberte **všechny úlohy**.

    ![Pracovní postupy Oozie webové konzoly](./media/hdinsight-operationalize-data-pipeline/hdi-oozie-web-console-workflows.png)

8. Pokud je stav úspěšné, dotaz tabulky databáze SQL zobrazíte vložených řádků. Pomocí portálu Azure přejděte do podokna pro vaši databázi SQL, vyberte **nástroje**a otevřete **Editor dotazů**.

        SELECT * FROM dailyflights

Teď, když pracovní postup běží jeden testovací dne, může obtékat tento pracovní postup se koordinátorem, která plánuje pracovního postupu, takže spouštěna každý den.

### <a name="run-the-workflow-with-a-coordinator"></a>Spuštění pracovního postupu se koordinátorem

Při plánování tento pracovní postup, aby byla spouštěna každý den (nebo všechny dny v určitém rozsahu dat), můžete použít koordinátor. Koordinátor je definována souboru XML, třeba `coordinator.xml`:

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

Jak můžete vidět, většina koordinátorem právě předává informace o konfiguraci na instanci pracovního postupu. Existují však několik důležitých položek vyvolávající.

* Bod 1: `start` a `end` atributy na `coordinator-app` časový interval, za které se spouští koordinátorem řízení elementu samotného.

    ```
    <coordinator-app ... start="2017-01-01T00:00Z" end="2017-01-05T00:00Z" frequency="${coord:days(1)}" ...>
    ```

    Koordinátor je zodpovědná za plánování akce v rámci `start` a `end` datum rozsahu, podle intervalu určeného `frequency` atribut. Každá naplánované akce zase spouští pracovní postup, jak nakonfigurovat. V výše uvedená definice coordinator koordinátorem nastaven na akce od 1. ledna 2017 do 5. ledna 2017. Frekvence nastavená na 1 den pomocí [Oozie výrazu jazyka](http://oozie.apache.org/docs/4.2.0/CoordinatorFunctionalSpec.html#a4.4._Frequency_and_Time-Period_Representation) frekvence výraz `${coord:days(1)}`. Výsledkem je koordinátorem plánování akce (a tím i pracovního postupu) jednou za den. Pro rozsahy dat, které jsou v minulosti, jako v následujícím příkladu bude akci naplánovat na spuštění bez zpoždění. Počáteční datum, ze kterého je naplánováno spuštění akce je volána *nominální čas*. Zpracování dat pro 1. ledna 2017 například koordinátorem se naplánuje akce s nominální čas 2017-01-01T00:00:00 GMT.

* Bod 2: V rozsahu dat pracovního postupu `dataset` element určuje, kde má být vyhledán ve HDFS data pro konkrétní období a konfiguruje způsob, jakým Oozie určí, zda je k dispozici data pro zpracování ještě.

    ```
    <dataset name="ds_input1" frequency="${coord:days(1)}" initial-instance="2016-12-31T00:00Z" timezone="UTC">
        <uri-template>${sourceDataFolder}${YEAR}-${MONTH}-FlightData.csv</uri-template>
        <done-flag></done-flag>
    </dataset>
    ```

    Cesta k datům v HDFS vychází dynamicky podle výrazu součástí `uri-template` elementu. V této coordinator se také používá frekvencí jeden den v datové sadě. Při počátečním a koncovým datem na ovládací prvek elementu coordinator, když akce, které jsou naplánovány (a definuje časy jejich nominální), `initial-instance` a `frequency` na datovou sadu řídit výpočet data, která se používá při vytváření `uri-template`. V takovém případě nastavte počáteční instance na jeden den před zahájením koordinátorem zajistit, že převezme první den je data za (1/1/2017). Výpočet data datovou sadu posunete od hodnoty `initial-instance` (12/31. prosinci 2016) přechodu v přírůstcích po frekvence datové sady (1 den) dokud nenajde nepředává nominální čas poslední datum nastavit koordinátorem (2017-01-01T00:00:00 GMT pro první akci).

    Prázdné `done-flag` element označuje, že Oozie, až kontrolovat přítomnost vstupní data v určené době Oozie určuje dat zda je k dispozici ve přítomnost adresář nebo soubor. V takovém případě je přítomnost souboru csv. Pokud se nachází soubor csv, Oozie předpokládá data jsou připravena a spustí instanci pracovního postupu při zpracování souboru. Pokud žádný soubor csv, Oozie předpokládá, že data je ještě není připravené a že spustit pracovní postup přejde do stavu čekání.

* Bod 3: `data-in` element určuje konkrétní časové razítko pro použití jako nominální čas při nahrazení hodnot v `uri-template` přidružené datové sady.

    ```
    <data-in name="event_input1" dataset="ds_input1">
        <instance>${coord:current(0)}</instance>
    </data-in>
    ```

    V takovém případě nastavte instanci výrazu `${coord:current(0)}`, což znamená, že je pomocí nominální čas v původním naplánována koordinátorem akce. Jinými slovy Pokud koordinátorem plány akci pro spuštění s nominální čas 01, 01/2017, 01, 01/2017 je co se používá k nahrazení rok (2017) a proměnné měsíc (01) v šabloně identifikátor URI. Jakmile šablonu identifikátoru URI je počítaný pro tuto instanci, Oozie ověří, zda očekávaný adresář nebo soubor je k dispozici a odpovídajícím způsobem plány příštím spuštění pracovního postupu.

Tři předchozí body se kombinují k yield situaci, kdy koordinátorem plány zpracování zdrojová data způsobem ve dnech. 

* Bod 1: Koordinátorem začíná nominální datum 2017-01-01.

* Bod 2: Oozie vyhledá data, které jsou k dispozici v `sourceDataFolder/2017-01-FlightData.csv`.

* Bod 3: Když Oozie vyhledá tento soubor, naplánuje instance pracovního postupu, který bude zpracovávat data pro 2017-01-01. Oozie potom pokračuje ve zpracování pro 2017-01-02. Toto testování opakuje, až do s výjimkou 2017-01-05.

S pracovními postupy, konfigurace koordinátoru ve smyslu `job.properties` souboru, který je nadmnožinou nastavení používané v tomto pracovním postupu.

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

Pouze nové vlastnosti zavedená v tomto `job.properties` souboru jsou:

| Vlastnost | Hodnota zdroje |
| --- | --- |
| oozie.coord.application.path | Určuje umístění `coordinator.xml` obsahující Oozie coordinator ke spuštění. |
| hiveDailyTableNamePrefix | Předpona použitá při vytváření dynamicky název tabulky pracovní tabulky. |
| hiveDataFolderPrefix | Předpona cesty, kde bude uložena pracovních tabulek. |

### <a name="deploy-and-run-the-oozie-coordinator"></a>Nasazení a spuštění Oozie Coordinator

Pokud chcete spustit kanál se koordinátorem, pokračujte podobným způsobem jako pracovní postup, s výjimkou pracujete ze složky jednu úroveň výš složku, která obsahuje pracovní postup. Tyto složky názvů odděluje koordinátoři z pracovních postupů na disku, takže jeden coordinator můžete přidružit jiné podřízené pracovní postupy.

1. Zkopírujte soubory coordinator až do místního úložiště hlavního uzlu clusteru pomocí spojovací bod služby z místního počítače.

    ```bash
    scp ./* sshuser@[CLUSTERNAME]-ssh.azurehdinsight.net:~
    ```

2. SSH do hlavního uzlu.

    ```bash
    ssh sshuser@[CLUSTERNAME]-ssh.azurehdinsight.net 
    ```

3. Zkopírujte soubory coordinator HDFS.

    ```bash
    hdfs dfs -put ./* /oozie/
    ```

4. Spusťte koordinátorem.

    ```bash
    oozie job -config job.properties -run
    ```

5. Ověření stavu pomocí webové konzole Oozie tento výběr čas **koordinátor úlohy** kartě a potom **všechny úlohy**.

    ![Oozie webové konzole koordinátor úlohy](./media/hdinsight-operationalize-data-pipeline/hdi-oozie-web-console-coordinator-jobs.png)

6. Vyberte instanci coordinator zobrazíte seznam plánované akce. V takovém případě měli byste vidět čtyř akcí s nominální časy v rozsahu od 1/1/2017 do 1/4 nebo 2017.

    ![Oozie webové konzole koordinátor úlohy](./media/hdinsight-operationalize-data-pipeline/hdi-oozie-web-console-coordinator-instance.png)

    Každá akce v tomto seznamu odpovídá instance pracovního postupu, který zpracovává data, za jeden den, kde je spuštění daný den indikován nominální čas.

## <a name="next-steps"></a>Další postup

* [Apache Oozie dokumentace](http://oozie.apache.org/docs/4.2.0/index.html)

<!-- * Build the same pipeline [using Azure Data Factory](tbd.md).  -->
