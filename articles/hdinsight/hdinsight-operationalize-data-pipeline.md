---
title: Zprovoznění kanálu data Analytics – Azure
description: Nastavte a spusťte Ukázkový datový kanál, který se aktivuje novými daty a vytváří stručné výsledky.
ms.service: hdinsight
author: ashishthaps
ms.author: ashishth
ms.reviewer: jasonh
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 01/11/2018
ms.openlocfilehash: dec3cdd63f3e3ff303bfd60ca1ae77a4c4641190
ms.sourcegitcommit: dd69b3cda2d722b7aecce5b9bd3eb9b7fbf9dc0a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/12/2019
ms.locfileid: "70961348"
---
# <a name="operationalize-a-data-analytics-pipeline"></a>Zprovoznění kanálu datových analýz

*Datové kanály* v rámci mnoha řešení pro analýzu dat. Jak název navrhuje, datový kanál přebírá nezpracovaná data, vyčistí a přetvaruje je podle potřeby a pak obvykle provede výpočty nebo agregace před uložením zpracovaných dat. Zpracovaná data spotřebovávají klienti, sestavy nebo rozhraní API. Datový kanál musí poskytnout opakující se výsledky, ať už podle plánu, nebo v případě, že jsou aktivované novými daty.

Tento článek popisuje, jak zprovoznění vaše datové kanály pro opakování pomocí Oozie spuštěných v clusterech HDInsight Hadoop. Ukázkový scénář vás provede datovým kanálem, který připraví a zpracovává data datových řad leteckých let.

V následujícím scénáři se vstupní data nachází v nestrukturovaném souboru, který obsahuje dávku letových dat za jeden měsíc. Tato letová data obsahují informace, jako je počátek a cílový letiště, míle předávány, časy odchodu a přijetí a tak dále. Cílem tohoto kanálu je shrnout každodenní výkon letecké společnosti, kde každá letecká společnost má jeden řádek za každý den s průměrem zpoždění odchodu a příchodu v řádu minut a celkovými mílemi předávány denně.

| YEAR | MONTH | DAY_OF_MONTH | LETECKÝ |AVG_DEP_DELAY | AVG_ARR_DELAY |TOTAL_DISTANCE |
| --- | --- | --- | --- | --- | --- | --- |
| 2017 | 1 | 3 | AA | 10,142229 | 7,862926 | 2644539 |
| 2017 | 1 | 3 | AS | 9,435449 | 5,482143 | 572289 |
| 2017 | 1 | 3 | DISTRIBUČNÍHO SEZNAMU | 6,935409 | -2,1893024 | 1909696 |

Příklad kanálu počká, dokud nepřijde nová časová data období, a pak uloží tyto podrobné informace o letu do datového skladu Apache Hive pro dlouhodobé analýzy. Kanál také vytvoří mnohem menší datovou sadu, která shrnuje pouze denní data letu. Tato denní souhrnná data letu se odešlou do SQL Database, která poskytuje sestavy, například pro web.

Příklad kanálu znázorňuje následující diagram.

![Kanál letových dat](./media/hdinsight-operationalize-data-pipeline/flight-pipeline-overview.png)

## <a name="apache-oozie-solution-overview"></a>Přehled řešení Apache Oozie

Tento kanál používá Apache Oozie běžící na clusteru HDInsight Hadoop.

Oozie popisuje jeho kanály z pohledu *akcí*, *pracovních postupů*a *koordinátorů*. Akce určují skutečnou práci, která má být provedena, například spuštění dotazu na podregistr. Pracovní postupy definují posloupnost akcí. Koordinátoři definují plán, kdy se pracovní postup spustí. Koordinátoři můžou také před spuštěním instance pracovního postupu počkat na dostupnost nových dat.

Následující diagram znázorňuje návrh vysoké úrovně v tomto ukázkovém kanálu Oozie.

![Kanál pro letová data Oozie](./media/hdinsight-operationalize-data-pipeline/pipeline-overview-oozie.png)

### <a name="provision-azure-resources"></a>Zřizování prostředků Azure

Tento kanál vyžaduje, aby ve stejném umístění byl cluster Hadoop Azure SQL Database a HDInsight. Azure SQL Database ukládá jak souhrnná data vytvořená kanálem, tak úložiště metadat Oozie.

#### <a name="provision-azure-sql-database"></a>Zřídit Azure SQL Database

1. Pomocí Azure Portal vytvořte novou skupinu prostředků s názvem `oozie` , která bude obsahovat všechny prostředky používané v tomto příkladu.
2. V rámci skupiny prostředků zřiďte Azure SQL Server a databázi. `oozie` Nepotřebujete databázi větší, než je cenová úroveň Standard S1.
3. Pomocí Azure Portal přejděte k podoknu nově nasazeného SQL Database a vyberte **nástroje**.

    ![Tlačítko nástroje](./media/hdinsight-operationalize-data-pipeline/hdi-sql-db-tools-button.png)

4. Vyberte **Editor dotazů**.

    ![Tlačítko editoru dotazů](./media/hdinsight-operationalize-data-pipeline/sql-db-query-editor1.png)

5. V podokně **Editor dotazů** vyberte **přihlášení**.

    ![Tlačítko pro přihlášení](./media/hdinsight-operationalize-data-pipeline/sql-db-login-window1.png)

6. Zadejte přihlašovací údaje pro SQL Database a vyberte **OK**.

   ![Přihlašovací formulář](./media/hdinsight-operationalize-data-pipeline/sql-db-login-window2.png)

7. V textové oblasti editoru dotazů zadejte následující příkazy SQL pro vytvoření `dailyflights` tabulky, která bude ukládat souhrnná data z každého spuštění kanálu.

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

8. Výběrem **příkazu Spustit** spusťte příkazy SQL.

    ![Tlačítko spustit](./media/hdinsight-operationalize-data-pipeline/hdi-sql-db-run-button.png)

Vaše Azure SQL Database je teď připravená.

#### <a name="provision-an-hdinsight-hadoop-cluster"></a>Zřízení clusteru HDInsight Hadoop

1. V Azure Portal vyberte **+ Nový** a vyhledejte HDInsight.
2. Vyberte **Vytvořit**.
3. V podokně základy zadejte pro svůj cluster jedinečný název a vyberte své předplatné Azure.

    ![Název a předplatné clusteru HDInsight](./media/hdinsight-operationalize-data-pipeline/cluster-name-subscription.png)

4. V podokně **typ clusteru** vyberte typ clusteru **Hadoop** , operační systém **Linux** a nejnovější verzi clusteru HDInsight. **Úroveň clusteru** ponechte na úrovni **Standard**.

    ![Typ clusteru HDInsight](./media/hdinsight-operationalize-data-pipeline/hdinsight-cluster-type.png)

5. Zvolte **možnost vybrat** , pokud chcete použít výběr typu clusteru.
6. Dokončete podokno **základy** zadáním přihlašovacího hesla a výběrem `oozie` skupiny prostředků ze seznamu a pak vyberte **Další**.

    ![Základy HDInsight – podokno](./media/hdinsight-operationalize-data-pipeline/hdinsight-basics-pane.png)

7. V podokně **úložiště** ponechte typ primární úložiště nastaven na **Azure Storage**, vyberte **vytvořit novou**a zadejte název nového účtu.

    ![Nastavení účtu HDInsight Storage](./media/hdinsight-operationalize-data-pipeline/storage-account-settings.png)

8. Pro **Nastavení metastore**v části **Vybrat databázi SQL pro podregistr**vyberte databázi, kterou jste předtím vytvořili.

    ![Nastavení metastore podregistru HDInsight](./media/hdinsight-operationalize-data-pipeline/hive-metastore-settings.png)

9. Vyberte **ověřování SQL Database**.

    ![Ověření metastore podregistru HDInsight](./media/hdinsight-operationalize-data-pipeline/hdi-authenticate-sql.png)

10. Zadejte své uživatelské jméno a heslo SQL Database a zvolte **Vybrat**. 

       ![Ověření přihlašovacích údajů pro podregistr HDInsight metastore](./media/hdinsight-operationalize-data-pipeline/hdi-authenticate-sql-login.png)

11. Zpátky v podokně **Nastavení metastore** vyberte databázi pro úložiště metadat Oozie a proveďte ověření jako dříve. 

       ![Nastavení metastore HDInsight](./media/hdinsight-operationalize-data-pipeline/hdi-metastore-settings.png)

12. Vyberte **Další**.
13. V podokně **Souhrn** vyberte **vytvořit** a nasaďte svůj cluster.

### <a name="verify-ssh-tunneling-setup"></a>Ověření nastavení tunelového propojení SSH

Pokud chcete pomocí webové konzoly Oozie zobrazit stav koordinátora a instancí pracovního postupu, nastavte tunel SSH na svůj cluster HDInsight. Další informace najdete v tématu [tunel SSH](hdinsight-linux-ambari-ssh-tunnel.md).

> [!NOTE]  
> K procházení webových prostředků clusteru v rámci tunelu SSH můžete použít také rozhraní Chrome s příponou [proxy Foxy](https://getfoxyproxy.org/) . Nakonfigurujte ho na proxy všech požadavků přes hostitele `localhost` na portu tunelového propojení 9876. Tento přístup je kompatibilní s podsystémem Windows pro Linux, označovaný také jako bash ve Windows 10.

1. Spuštěním následujícího příkazu otevřete tunelové propojení SSH s vaším clusterem:

    ```
    ssh -C2qTnNf -D 9876 sshuser@[CLUSTERNAME]-ssh.azurehdinsight.net
    ```

2. Ověřte funkčnost tunelu tak, že přejdete na Ambari na hlavním uzlu, a to tak, že přejdete na:

    http:\//headnodehost:8080

3. Chcete-li získat přístup k **webové konzoli Oozie** z Ambari, vyberte možnost **Oozie**, **Rychlé odkazy**a pak vyberte možnost **Oozie webová konzola**.

### <a name="configure-hive"></a>Konfigurovat podregistr

1. Stáhněte si ukázkový soubor CSV, který obsahuje data letů za jeden měsíc. Stáhněte si soubor `2017-01-FlightData.zip` zip z [úložiště GitHub HDInsight](https://github.com/hdinsight/hdinsight-dev-guide) a rozbalte ho do souboru `2017-01-FlightData.csv`CSV. 

2. Zkopírujte tento soubor CSV do účtu Azure Storage připojeného ke clusteru HDInsight a umístěte ho do `/example/data/flights` složky.

Soubor můžete zkopírovat pomocí spojovacího bodu služby `bash` v relaci prostředí.

1. Pomocí spojovacího bodu služby zkopírujte soubory z místního počítače do místního úložiště hlavního uzlu clusteru HDInsight.

    ```bash
    scp ./2017-01-FlightData.csv sshuser@[CLUSTERNAME]-ssh.azurehdinsight.net:2017-01-FlightData.csv
    ```

2. Pomocí příkazu HDFS zkopírujte soubor z místního úložiště hlavního uzlu do Azure Storage.

    ```bash
    hdfs dfs -put ./2017-01-FlightData.csv /example/data/flights/2017-01-FlightData.csv
    ```

Ukázková data jsou nyní k dispozici. Kanál ale vyžaduje ke zpracování dvě tabulky podregistru, jednu pro příchozí data (`rawFlights`) a jednu pro sumarizovaná data (`flights`). Vytvořte tyto tabulky v Ambari následujícím způsobem.

1. Přihlaste se k Ambari tak, že přejdete na http:\//headnodehost: 8080.
2. V seznamu služeb vyberte **podregistr**.

    ![Výběr podregistru v Ambari](./media/hdinsight-operationalize-data-pipeline/hdi-ambari-services-hive.png)

3. Vyberte **Přejít k zobrazení** vedle popisku zobrazení podregistru 2,0.

    ![Výběr zobrazení podregistru v Ambari](./media/hdinsight-operationalize-data-pipeline/hdi-ambari-services-hive-summary.png)

4. V oblasti text dotazu vložte následující příkazy k vytvoření `rawFlights` tabulky. Tabulka poskytuje schéma-čtení pro soubory `/example/data/flights` CSV ve složce v Azure Storage. `rawFlights` 

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

5. Vyberte **provést** a vytvořte tabulku.

    ![Dotaz na podregistr v Ambari](./media/hdinsight-operationalize-data-pipeline/hdi-ambari-services-hive-query.png)

6. Chcete-li `flights` vytvořit tabulku, nahraďte text v oblasti textu dotazu následujícími příkazy. `flights` Tabulka je spravovaná tabulka v podregistru, která umožňuje rozdělit data do oddílů podle roku, měsíce a dne v měsíci. Tato tabulka bude obsahovat všechna historická data letového řádu s nejnižší členitosti ve zdrojových datech jednoho řádku na jeden let.

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

7. Vyberte **provést** a vytvořte tabulku.

### <a name="create-the-oozie-workflow"></a>Vytvoření pracovního postupu Oozie

Kanály obvykle zpracovávají data v dávkách v daném časovém intervalu. V takovém případě kanál zpracovává letová data denně. Tento přístup umožňuje, aby vstupní soubory CSV dostaly denní, týdenní, měsíční nebo roční.

Ukázkový pracovní postup zpracovává data letů dne v průběhu dne tři hlavní kroky:

1. Spusťte dotaz na podregistr pro extrakci dat pro časové období tohoto dne ze zdrojového souboru CSV reprezentovaného `rawFlights` tabulkou a vložení dat `flights` do tabulky.
2. Spusťte dotaz na podregistr pro dynamické vytvoření pracovní tabulky v podregistru pro daný den, který obsahuje kopii letových dat shrnutých podle dne a dopravce.
3. Pomocí Apache Sqoop zkopírujte všechna data z každodenní pracovní tabulky v podregistru do cílové `dailyflights` tabulky v Azure SQL Database. Sqoop přečte zdrojové řádky z dat za tabulkou podregistru nacházející se v Azure Storage a načítá je do SQL Database pomocí připojení JDBC.

Tyto tři kroky jsou koordinovány pomocí pracovního postupu Oozie. 

1. Vytvořte v souboru `hive-load-flights-partition.hql`dotaz.

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

    Proměnné Oozie používají syntaxi `${variableName}`. Tyto proměnné jsou nastaveny v `job.properties` souboru, jak je popsáno v následujícím kroku. Oozie nahradí skutečné hodnoty za běhu.

2. Vytvořte v souboru `hive-create-daily-summary-table.hql`dotaz.

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

    Tento dotaz vytvoří pracovní tabulku, ve které budou uložena pouze souhrnná data po jednom dni, poznamenejte si příkaz SELECT, který vypočítá průměrné zpoždění a celkovou vzdálenost předávány podle data dopravce. Data vložená do této tabulky jsou uložená ve známém umístění (cesta označená proměnnou hiveDataFolder) tak, aby se v dalším kroku mohla použít jako zdroj pro Sqoop.

3. Spusťte následující příkaz Sqoop.

    ```
    sqoop export --connect ${sqlDatabaseConnectionString} --table ${sqlDatabaseTableName} --export-dir ${hiveDataFolder} -m 1 --input-fields-terminated-by "\t"
    ```

Tyto tři kroky jsou vyjádřené jako tři samostatné akce v následujícím souboru pracovního postupu Oozie s `workflow.xml`názvem.

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

Ke dvěma dotazům na podregistr se používá jejich cesta v Azure Storage a zbývající hodnoty proměnných jsou k dispozici v následujícím `job.properties` souboru. Tento soubor nakonfiguruje pracovní postup, který se má spustit pro datum 3. ledna 2017.

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

Následující tabulka shrnuje každou z vlastností a označuje, kde můžete najít hodnoty pro vlastní prostředí.

| Vlastnost | Zdroj hodnoty |
| --- | --- |
| NameNode | Úplná cesta k kontejneru Azure Storage připojenému ke clusteru HDInsight. |
| jobTracker | Interní název hostitele pro hlavní uzel PŘÍZe clusteru. Na domovské stránce Ambari v seznamu služeb vyberte možnost PŘÍZe a pak zvolte možnost aktivní Správce prostředků. Identifikátor URI názvu hostitele se zobrazí v horní části stránky. Připojit port 8050. |
| queueName | Název fronty PŘÍZe použité při plánování akcí podregistru Nechejte jako výchozí. |
| oozie.use.system.libpath | Nechejte jako true. |
| appBase | Cesta k podsložce v Azure Storage, do které nasadíte pracovní postup Oozie a podpůrné soubory. |
| oozie.wf.application.path | Umístění pracovního postupu `workflow.xml` Oozie, který se má spustit. |
| hiveScriptLoadPartition | Cesta v Azure Storage k souboru `hive-load-flights-partition.hql`dotazu na podregistr. |
| hiveScriptCreateDailyTable | Cesta v Azure Storage k souboru `hive-create-daily-summary-table.hql`dotazu na podregistr. |
| hiveDailyTableName | Dynamicky vygenerovaný název, který se má použít pro pracovní tabulku. |
| hiveDataFolder | Cesta v Azure Storage k datům obsaženým v pracovní tabulce. |
| sqlDatabaseConnectionString | Připojovací řetězec syntaxe JDBC k vašemu Azure SQL Database. |
| sqlDatabaseTableName | Název tabulky v Azure SQL Database, do které jsou vloženy souhrnné řádky. Nechejte jako `dailyflights`. |
| rok | Rok komponenty dne, pro kterou jsou vypočítány souhrny letů. Nechejte tak, jak je. |
| měsíc | Měsíční komponenta dne, pro kterou jsou vypočítány souhrny letů. Nechejte tak, jak je. |
| den | Složka dne v měsíci, pro kterou jsou vypočítány souhrny letů. Nechejte tak, jak je. |

> [!NOTE]  
> Než budete moct nasadit a spustit pracovní postup `job.properties` Oozie, nezapomeňte si aktualizovat kopii souboru s použitím hodnot, které jsou specifické pro vaše prostředí.

### <a name="deploy-and-run-the-oozie-workflow"></a>Nasazení a spuštění pracovního postupu Oozie

Pomocí spojovacího bodu služby z relace bash Nasaďte svůj pracovní`workflow.xml`postup Oozie (), dotazy`hive-load-flights-partition.hql` na `hive-create-daily-summary-table.hql`podregistr (a) a konfiguraci`job.properties`úlohy ().  V Oozie může existovat pouze `job.properties` soubor v místním úložišti hlavnímu uzlu. Všechny ostatní soubory musí být uloženy v HDFS, v tomto případě Azure Storage. Akce Sqoop, kterou pracovní postup používá, závisí na ovladači JDBC pro komunikaci s vaším SQL Database, která musí být zkopírována z hlavního uzlu do HDFS.

1. `load_flights_by_day` Vytvořte podsložku pod cestou uživatele v místním úložišti hlavního uzlu.

        ssh sshuser@[CLUSTERNAME]-ssh.azurehdinsight.net 'mkdir load_flights_by_day'

2. Zkopírujte všechny soubory v aktuálním adresáři ( `workflow.xml` `load_flights_by_day` soubory a `job.properties` ) do podsložky.

        scp ./* sshuser@[CLUSTERNAME]-ssh.azurehdinsight.net:load_flights_by_day

3. Připojte se k hlavnímu uzlu přes SSH a `load_flights_by_day` přejděte do složky.

        ssh sshuser@[CLUSTERNAME]-ssh.azurehdinsight.net
        cd load_flights_by_day

4. Zkopírujte soubory pracovního postupu do HDFS.

        hdfs dfs -put ./* /oozie/load_flights_by_day

5. Z `sqljdbc41.jar` místního hlavního uzlu zkopírujte do složky pracovního postupu v HDFS:

        hdfs dfs -put /usr/share/java/sqljdbc_4.1/enu/sqljdbc*.jar /oozie/load_flights_by_day

6. Spusťte pracovní postup.

        oozie job -config job.properties -run

7. Sledujte stav pomocí webové konzoly Oozie. V rámci Ambari vyberte možnost **Oozie**, **Rychlé odkazy**a pak **Oozie webové konzole**. Na kartě **úlohy pracovního postupu** vyberte **všechny úlohy**.

    ![Pracovní postupy webové konzoly Oozie](./media/hdinsight-operationalize-data-pipeline/hdi-oozie-web-console-workflows.png)

8. Po ÚSPĚŠNÉm dokončení dotazu do tabulky databáze SQL Zobrazte vložené řádky. Pomocí Azure Portal přejděte do podokna pro SQL Database, vyberte **nástroje**a otevřete **Editor dotazů**.

        SELECT * FROM dailyflights

Teď, když je pracovní postup spuštěný pro jeden den testu, můžete tento pracovní postup zabalit koordinátorovi, který naplánuje pracovní postup tak, aby běžel denně.

### <a name="run-the-workflow-with-a-coordinator"></a>Spuštění pracovního postupu se koordinátorem

Pokud chcete tento pracovní postup naplánovat tak, aby běžel denně (nebo všechny dny v rozsahu kalendářních dat), můžete použít koordinátora. Koordinátor je definován souborem XML, například `coordinator.xml`:

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

Jak vidíte, většina koordinátora právě předává informace o konfiguraci do instance pracovního postupu. Existuje však několik důležitých položek, které je třeba vyzvat.

* Bod 1: Atributy `start` a `end` na`coordinator-app` samotném prvku řídí časový interval, po kterém se koordinátor spouští.

    ```
    <coordinator-app ... start="2017-01-01T00:00Z" end="2017-01-05T00:00Z" frequency="${coord:days(1)}" ...>
    ```

    Koordinátor zodpovídá za plánování akcí v `start` rozsahu data a `end` v závislosti na intervalu určeném `frequency` atributem. Každá naplánovaná akce zase spustí pracovní postup, jak je nakonfigurován. Ve výše uvedené definici koordinátora je koordinátor nakonfigurovaný tak, aby spouštěl akce od 1. ledna 2017 do 5. ledna 2017. Frekvence je nastavená na 1 den podle `${coord:days(1)}` [Oozie výrazu jazyka výrazu](https://oozie.apache.org/docs/4.2.0/CoordinatorFunctionalSpec.html#a4.4._Frequency_and_Time-Period_Representation) . Výsledkem je, že koordinátor naplánuje akci (a tedy pracovní postup) jednou za den. Pro rozsahy dat, které jsou v minulosti, jako v tomto příkladu, bude naplánováno spuštění akce bez zpoždění. Začátek data, ze kterého je naplánováno spuštění akce, se označuje jako *nominální čas*. Například pro zpracování dat 1. ledna 2017 bude koordinátor plánovat akci s nominálním časem 2017-01-01T00:00:00 GMT.

* Bod 2: V rámci rozsahu dat pracovního postupu `dataset` prvek určuje, kde se má v poli HDFS vyhledat data pro konkrétní rozsah dat, a nakonfiguruje, jak Oozie určuje, zda jsou data pro zpracování ještě k dispozici.

    ```
    <dataset name="ds_input1" frequency="${coord:days(1)}" initial-instance="2016-12-31T00:00Z" timezone="UTC">
        <uri-template>${sourceDataFolder}${YEAR}-${MONTH}-FlightData.csv</uri-template>
        <done-flag></done-flag>
    </dataset>
    ```

    Cesta k datům v HDFS je dynamicky sestavena podle výrazu uvedeného v `uri-template` elementu. V tomto koordinátoru se pro datovou sadu používá také frekvence jednoho dne. Zatímco datum zahájení a ukončení v ovládacím prvku koordinátora v případě, že jsou akce naplánovány (a definuje jejich nominální časy) `initial-instance` , `frequency` a na ovládacím prvku DataSet výpočet data, která se používají při sestavování `uri-template`. V takovém případě nastavte počáteční instanci na jeden den před začátkem koordinátora, aby se zajistilo, že bude vycházet z dat prvního dne (1/1/2017). Výpočet data datové sady se započítává od hodnoty `initial-instance` (12/31/2016), která se zvyšuje v přírůstcích po četnosti datové sady (1 den), dokud nenajde poslední datum, které neprojde jmenovitým časem nastaveným koordinátorem (2017-01-01T00:00:00 GMT pro první akci).

    Prázdný `done-flag` element označuje, že když Oozie zkontroluje přítomnost vstupních dat v určeném čase, Oozie určuje, jestli je k dispozici přítomnost adresáře nebo souboru. V tomto případě je to přítomnost souboru CSV. Pokud je přítomen soubor CSV, Oozie předpokládá, že data jsou připravena a spustí instanci pracovního postupu pro zpracování souboru. Pokud není přítomen žádný soubor CSV, Oozie předpokládá, že data ještě nejsou připravena a že spuštění pracovního postupu přejde do stavu čekání.

* Bod 3: Prvek určuje konkrétní časové razítko, které má být použito jako nominální čas při nahrazování hodnot `uri-template` v objektu pro přidruženou datovou sadu. `data-in`

    ```
    <data-in name="event_input1" dataset="ds_input1">
        <instance>${coord:current(0)}</instance>
    </data-in>
    ```

    V takovém případě nastavte instanci na výraz `${coord:current(0)}`, který se přeloží na použití nominálního času akce původně naplánovaného koordinátorem. Jinými slovy, když koordinátor naplánuje akci pro spuštění s jmenovitým časem 01/01/2017, pak 01/01/2017 je to, co se používá k nahrazení proměnných YEAR (2017) a MONTH (01) v šabloně identifikátoru URI. Jakmile je pro tuto instanci vypočítána šablona identifikátoru URI, Oozie zkontroluje, jestli je k dispozici očekávaný adresář nebo soubor, a podle toho naplánuje další spuštění pracovního postupu.

Tři předchozí body se kombinují kvůli situaci, kdy koordinátor plánuje zpracování zdrojových dat každodenním způsobem. 

* Bod 1: Koordinátor začíná jmenovitým datem 2017-01-01.

* Bod 2: Oozie vyhledá data, která `sourceDataFolder/2017-01-FlightData.csv`jsou k dispozici v.

* Bod 3: Když Oozie tento soubor najde, naplánuje instanci pracovního postupu, která bude zpracovávat data pro 2017-01-01. Oozie pak pokračuje ve zpracování pro 2017-01-02. Toto vyhodnocení se opakuje až do, ale ne včetně 2017-01-05.

Stejně jako u pracovních postupů je konfigurace koordinátora definovaná v `job.properties` souboru, který má nadmnožinu nastavení, které používá pracovní postup.

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

V tomto `job.properties` souboru byly zavedeny pouze nové vlastnosti:

| Vlastnost | Zdroj hodnoty |
| --- | --- |
| oozie.coord.application.path | Určuje umístění souboru, `coordinator.xml` který obsahuje koordinátor Oozie, který se má spustit. |
| hiveDailyTableNamePrefix | Předpona, která se používá při dynamickém vytváření názvu tabulky pracovní tabulky |
| hiveDataFolderPrefix | Předpona cesty, kde budou uloženy všechny pracovní tabulky. |

### <a name="deploy-and-run-the-oozie-coordinator"></a>Nasazení a spuštění koordinátoru Oozie

Pokud chcete kanál spustit se koordinátorem, pokračujte podobným způsobem jako u pracovního postupu, s tím rozdílem, že pracujete se složkou, která je na jedné úrovni nad složkou, která obsahuje váš pracovní postup. Tato konvence složek odděluje koordinátory z pracovních postupů na disku, takže můžete přidružit jednoho koordinátora k různým podřízeným pracovním postupům.

1. Pomocí spojovacího bodu služby z místního počítače zkopírujte soubory koordinátoru do místního úložiště hlavního uzlu vašeho clusteru.

    ```bash
    scp ./* sshuser@[CLUSTERNAME]-ssh.azurehdinsight.net:~
    ```

2. Připojte se k hlavnímu uzlu přes SSH.

    ```bash
    ssh sshuser@[CLUSTERNAME]-ssh.azurehdinsight.net 
    ```

3. Zkopírujte soubory koordinátora do HDFS.

    ```bash
    hdfs dfs -put ./* /oozie/
    ```

4. Spusťte koordinátora.

    ```bash
    oozie job -config job.properties -run
    ```

5. Ověřte stav pomocí webové konzoly Oozie, tentokrát vyberte kartu **úlohy koordinátora** a pak klikněte na **všechny úlohy**.

    ![Oozie úlohy koordinátora webové konzoly](./media/hdinsight-operationalize-data-pipeline/hdi-oozie-web-console-coordinator-jobs.png)

6. Vyberte instanci koordinátora pro zobrazení seznamu plánovaných akcí. V takovém případě byste měli vidět čtyři akce s nominálními časy v rozsahu od 1/1/2017 do 1/4/2017.

    ![Úloha koordinátora webové konzoly Oozie](./media/hdinsight-operationalize-data-pipeline/hdi-oozie-web-console-coordinator-instance.png)

    Každá akce v tomto seznamu odpovídá instanci pracovního postupu, která zpracovává data o jednom dni, přičemž začátek tohoto dne je určen jmenovitým časem.

## <a name="next-steps"></a>Další postup

* [Dokumentace k Apache Oozie](https://oozie.apache.org/docs/4.2.0/index.html)

<!-- * Build the same pipeline [using Azure Data Factory](tbd.md).  -->
