---
title: Použití pracovních postupů Hadoop Oozie v Azure HDInsight založeném na Linuxu
description: Použijte Hadoop Oozie v Linuxu-založené HDInsight. Naučte se definovat pracovní postup Oozie a odeslat úlohu Oozie.
author: omidm1
ms.author: omidm
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.date: 10/30/2019
ms.openlocfilehash: ece6fdb743035069bc6c666d6e90c76860f63e82
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "75744904"
---
# <a name="use-apache-oozie-with-apache-hadoop-to-define-and-run-a-workflow-on-linux-based-azure-hdinsight"></a>Použití Apache Oozie s Apache Haddopem k definování a spuštění pracovního procesu v linuxové službě Azure HDInsight

Přečtěte si, jak používat Apache Oozie s Apache Hadoop na Azure HDInsight. Oozie je workflow a koordinační systém, který spravuje hadoop pracovních míst. Oozie je integrován s zásobníkem Hadoop a podporuje následující úlohy:

* Apache Hadoop MapaReduce
* Apache prase
* Apache Úl
* Apačský sqoop

Oozie můžete také použít k plánování úloh, které jsou specifické pro systém, jako jsou programy Java nebo skripty prostředí.

> [!NOTE]  
> Další možností definování pracovních postupů pomocí HDInsightu je použití Azure Data Factory. Další informace o datové továrně najdete [v tématu Použití Apache Pig a Apache Hive s Totočí továrnou][azure-data-factory-pig-hive]. Chcete-li používat Oozie v clusterech s balíčkem Enterprise Security Package, přečtěte [si prosím spuštění Apache Oozie v clusterech HDInsight Hadoop s balíčkem Enterprise Security Package](domain-joined/hdinsight-use-oozie-domain-joined-clusters.md).

## <a name="prerequisites"></a>Požadavky

* **Cluster Hadoop na HDInsight**. Viz [Začínáme s HDInsight na Linuxu](hadoop/apache-hadoop-linux-tutorial-get-started.md).

* **Klient SSH**. Viz [Připojení k HDInsight (Apache Hadoop) pomocí SSH](hdinsight-hadoop-linux-use-ssh-unix.md).

* **Databáze Azure SQL**.  Viz [Vytvoření databáze Azure SQL na webu Azure Portal](../sql-database/sql-database-get-started.md).  Tento článek používá databázi s názvem **oozietest**.

* [Schéma URI](./hdinsight-hadoop-linux-information.md#URI-and-scheme) pro primární úložiště clusterů. To by `wasb://` bylo pro `abfs://` Azure Storage, pro `adl://` Azure Data Lake Storage Gen2 nebo pro Azure Data Lake Storage Gen1. Pokud je pro Azure Storage povolený zabezpečený přenos, identifikátor URI bude `wasbs://`. Viz také [bezpečný přenos](../storage/common/storage-require-secure-transfer.md).

## <a name="example-workflow"></a>Příklad pracovního postupu

Pracovní postup použitý v tomto dokumentu obsahuje dvě akce. Akce jsou definice pro úkoly, jako je například spuštění Hive, Sqoop, MapReduce nebo jiné procesy:

![HDInsight oozie diagram pracovního postupu](./media/hdinsight-use-oozie-linux-mac/oozie-workflow-diagram.png)

1. Akce Hive spustí skript HiveQL pro `hivesampletable` extrahování záznamů z toho, co je součástí HDInsight. Každý řádek dat popisuje návštěvu z konkrétního mobilního zařízení. Formát záznamu se zobrazí jako následující text:

        8       18:54:20        en-US   Android Samsung SCH-i500        California     United States    13.9204007      0       0
        23      19:19:44        en-US   Android HTC     Incredible      Pennsylvania   United States    NULL    0       0
        23      19:19:46        en-US   Android HTC     Incredible      Pennsylvania   United States    1.4757422       0       1

    Skript Hive použitý v tomto dokumentu počítá celkové návštěvy pro každou platformu, například Android nebo iPhone, a ukládá počty do nové tabulky Hive.

    Další informace o Hive najdete [v tématu Použití Apache Hive s HDInsight][hdinsight-use-hive].

2. Akce Sqoop exportuje obsah nové tabulky Hive do tabulky vytvořené v Azure SQL Database. Další informace o Sqoop najdete [v tématu Použití Apache Sqoop s HDInsight][hdinsight-use-sqoop].

> [!NOTE]  
> Podporované verze Oozie v clusterech HDInsight najdete v tématu [Co je nového ve verzích clusteru Hadoop poskytovaných službou HDInsight][hdinsight-versions].

## <a name="create-the-working-directory"></a>Vytvoření pracovního adresáře

Oozie očekává, že uložíte všechny prostředky potřebné pro úlohu ve stejném adresáři. Tento příklad `wasbs:///tutorials/useoozie`používá . Chcete-li vytvořit tento adresář, proveďte následující kroky:

1. Upravte níže uvedený `sshuser` kód, který nahradí uživatelským jménem SSH pro cluster, a nahraďte `CLUSTERNAME` jej názvem clusteru.  Poté zadejte kód pro připojení ke clusteru HDInsight [pomocí ssh](hdinsight-hadoop-linux-use-ssh-unix.md).  

    ```bash
    ssh sshuser@CLUSTERNAME-ssh.azurehdinsight.net
    ```

2. Chcete-li vytvořit adresář, použijte následující příkaz:

    ```bash
    hdfs dfs -mkdir -p /tutorials/useoozie/data
    ```

    > [!NOTE]  
    > Parametr `-p` způsobí vytvoření všech adresářů v cestě. Adresář `data` slouží k uložení dat používaných skriptem. `useooziewf.hql`

3. Upravte níže uvedený `sshuser` kód a nahraďte jej uživatelským jménem SSH.  Chcete-li se ujistit, že oozie může zosobnit váš uživatelský účet, použijte následující příkaz:

    ```bash
    sudo adduser sshuser users
    ```

    > [!NOTE]  
    > Můžete ignorovat chyby, které označují, že `users` uživatel je již členem skupiny.

## <a name="add-a-database-driver"></a>Přidání ovladače databáze

Vzhledem k tomu, že tento pracovní postup používá Sqoop k exportu dat do databáze SQL, musíte poskytnout kopii ovladače JDBC, který se používá k interakci s databází SQL. Chcete-li ovladač JDBC zkopírovat do pracovního adresáře, použijte následující příkaz z relace SSH:

```bash
hdfs dfs -put /usr/share/java/sqljdbc_7.0/enu/mssql-jdbc*.jar /tutorials/useoozie/
```

> [!IMPORTANT]  
> Ověřte skutečný ovladač JDBC, který existuje v . `/usr/share/java/`

Pokud váš pracovní postup používá jiné prostředky, například jar, který obsahuje aplikaci MapReduce, je třeba přidat i tyto prostředky.

## <a name="define-the-hive-query"></a>Definování dotazu Hive

Pomocí následujících kroků vytvořte skript dotazovacího jazyka Hive (HiveQL), který definuje dotaz. Dotaz použijete v pracovním postupu Oozie později v tomto dokumentu.

1. Z připojení SSH vytvořte soubor s názvem `useooziewf.hql`:

    ```bash
    nano useooziewf.hql
    ```

1. Po otevření editoru GNU nano použijte jako obsah souboru následující dotaz:

    ```hiveql
    DROP TABLE ${hiveTableName};
    CREATE EXTERNAL TABLE ${hiveTableName}(deviceplatform string, count string) ROW FORMAT DELIMITED
    FIELDS TERMINATED BY '\t' STORED AS TEXTFILE LOCATION '${hiveDataFolder}';
    INSERT OVERWRITE TABLE ${hiveTableName} SELECT deviceplatform, COUNT(*) as count FROM hivesampletable GROUP BY deviceplatform;
    ```

    Ve skriptu se používají dvě proměnné:

   * `${hiveTableName}`: Obsahuje název tabulky, která má být vytvořena.

   * `${hiveDataFolder}`: Obsahuje umístění pro uložení datových souborů pro tabulku.

     Definiční soubor pracovního postupu workflow.xml v tomto článku předá tyto hodnoty tomuto skriptu HiveQL za běhu.

1. Chcete-li soubor uložit, vyberte **Ctrl+X**, zadejte **Y**a pak vyberte **Enter**.  

1. Ke kopírování `useooziewf.hql` `wasbs:///tutorials/useoozie/useooziewf.hql`do :

    ```bash
    hdfs dfs -put useooziewf.hql /tutorials/useoozie/useooziewf.hql
    ```

    Tento příkaz `useooziewf.hql` ukládá soubor do úložiště kompatibilního s HDFS pro cluster.

## <a name="define-the-workflow"></a>Definování pracovního postupu

Definice pracovního postupu Oozie jsou napsány v jazyce definice hadoopského procesu (hPDL), což je jazyk definice procesu XML. K definování pracovního postupu použijte následující kroky:

1. K vytvoření a úpravě nového souboru použijte následující příkaz:

    ```bash
    nano workflow.xml
    ```

2. Po otevření nano editoru zadejte jako obsah souboru následující XML:

    ```xml
    <workflow-app name="useooziewf" xmlns="uri:oozie:workflow:0.2">
        <start to = "RunHiveScript"/>
        <action name="RunHiveScript">
        <hive xmlns="uri:oozie:hive-action:0.2">
            <job-tracker>${jobTracker}</job-tracker>
            <name-node>${nameNode}</name-node>
            <configuration>
            <property>
                <name>mapred.job.queue.name</name>
                <value>${queueName}</value>
            </property>
            </configuration>
            <script>${hiveScript}</script>
            <param>hiveTableName=${hiveTableName}</param>
            <param>hiveDataFolder=${hiveDataFolder}</param>
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
            <arg>${hiveDataFolder}</arg>
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

    V pracovním postupu jsou definovány dvě akce:

   * `RunHiveScript`: Tato akce je počáteční `useooziewf.hql` akce a spustí skript Hive.

   * `RunSqoopExport`: Tato akce exportuje data vytvořená ze skriptu Hive do databáze SQL pomocí Sqoop. Tato akce se `RunHiveScript` spustí pouze v případě, že je akce úspěšná.

     Pracovní postup má několik `${jobTracker}`položek, například . Tyto položky nahradíte hodnotami, které použijete v definici projektu. Definici úlohy vytvoříte později v tomto dokumentu.

     Všimněte `<archive>mssql-jdbc-7.0.0.jre8.jar</archive>` si také položky v sekci Sqoop. Tato položka pokyn Oozie, aby tento archiv k dispozici pro Sqoop, když tato akce běží.

3. Chcete-li soubor uložit, vyberte **Ctrl+X**, zadejte **Y**a pak vyberte **Enter**.  

4. Chcete-li soubor zkopírovat do následujícího příkazu, `workflow.xml` použijte `/tutorials/useoozie/workflow.xml`následující příkaz:

    ```bash
    hdfs dfs -put workflow.xml /tutorials/useoozie/workflow.xml
    ```

## <a name="create-a-table"></a>Vytvoření tabulky

> [!NOTE]  
> Existuje mnoho způsobů, jak se připojit k databázi SQL a vytvořit tabulku. V následujících krocích se používá [FreeTDS](https://www.freetds.org/) z clusteru HDInsight.

1. K instalaci služby FreeTDS do clusteru HDInsight použijte následující příkaz:

    ```bash
    sudo apt-get --assume-yes install freetds-dev freetds-bin
    ```

2. Upravte níže uvedený `<serverName>` kód, který nahradínázev `<sqlLogin>` serveru Azure SQL, a přihlášením serveru Azure SQL.  Zadejte příkaz pro připojení k požadované databázi SQL.  Na výzvu zadejte heslo.

    ```bash
    TDSVER=8.0 tsql -H <serverName>.database.windows.net -U <sqlLogin> -p 1433 -D oozietest
    ```

    Obdržíte výstup jako následující text:

        locale is "en_US.UTF-8"
        locale charset is "UTF-8"
        using default charset "UTF-8"
        Default database being set to oozietest
        1>

3. Na příkazovém řádku `1>` zadejte následující řádky:

    ```sql
    CREATE TABLE [dbo].[mobiledata](
    [deviceplatform] [nvarchar](50),
    [count] [bigint])
    GO
    CREATE CLUSTERED INDEX mobiledata_clustered_index on mobiledata(deviceplatform)
    GO
    ```

    Po zadání příkazu `GO` se vyhodnotí předchozí příkazy. Tyto příkazy vytvoří `mobiledata`tabulku s názvem , která se používá v pracovním postupu.

    Chcete-li ověřit, zda byla tabulka vytvořena, použijte následující příkazy:

    ```sql
    SELECT * FROM information_schema.tables
    GO
    ```

    Výstup se zobrazí jako následující text:

        TABLE_CATALOG   TABLE_SCHEMA    TABLE_NAME      TABLE_TYPE
        oozietest       dbo             mobiledata      BASE TABLE

4. Ukončete nástroj `exit` tsql `1>` zadáním na výzvu.

## <a name="create-the-job-definition"></a>Vytvořit definici úlohy

Definice úlohy popisuje, kde najít soubor workflow.xml. Také popisuje, kde najít další soubory používané pracovního postupu, například `useooziewf.hql`. Kromě toho definuje hodnoty vlastností použitých v rámci pracovního postupu a přidružených souborů.

1. Chcete-li získat úplnou adresu výchozího úložiště, použijte následující příkaz. Tato adresa se používá v konfiguračním souboru, který vytvoříte v dalším kroku.

    ```bash
    sed -n '/<name>fs.default/,/<\/value>/p' /etc/hadoop/conf/core-site.xml
    ```

    Tento příkaz vrátí informace, jako je následující XML:

    ```xml
    <name>fs.defaultFS</name>
    <value>wasbs://mycontainer@mystorageaccount.blob.core.windows.net</value>
    ```

    > [!NOTE]  
    > Pokud cluster HDInsight používá Azure Storage jako `<value>` výchozí úložiště, obsah prvku začíná . `wasbs://` Pokud azure data lake storage gen1 se `adl://`používá místo toho, začíná . Pokud se používá Azure Data Lake Storage `abfs://`Gen2, začíná na .

    Uložte obsah `<value>` prvku, jak se používá v dalších krocích.

2. Upravte xml níže takto:

    |Zástupná hodnota| Nahrazená hodnota|
    |---|---|
    |wasbs://mycontainer\@mystorageaccount.blob.core.windows.net| Hodnota přijatá z kroku 1.|
    |admin| Vaše přihlašovací jméno pro cluster HDInsight, pokud ne správce.|
    |Název_serveru| Název databázového serveru Azure SQL.|
    |sqlLogin| Přihlášení k databázovému serveru Azure SQL.|
    |sqlPassword| Přihlašovací heslo k databázovému serveru Azure SQL.|

    ```xml
    <?xml version="1.0" encoding="UTF-8"?>
    <configuration>

        <property>
        <name>nameNode</name>
        <value>wasbs://mycontainer@mystorageaccount.blob.core.windows.net</value>
        </property>

        <property>
        <name>jobTracker</name>
        <value>headnodehost:8050</value>
        </property>

        <property>
        <name>queueName</name>
        <value>default</value>
        </property>

        <property>
        <name>oozie.use.system.libpath</name>
        <value>true</value>
        </property>

        <property>
        <name>hiveScript</name>
        <value>wasbs://mycontainer@mystorageaccount.blob.core.windows.net/tutorials/useoozie/useooziewf.hql</value>
        </property>

        <property>
        <name>hiveTableName</name>
        <value>mobilecount</value>
        </property>

        <property>
        <name>hiveDataFolder</name>
        <value>wasbs://mycontainer@mystorageaccount.blob.core.windows.net/tutorials/useoozie/data</value>
        </property>

        <property>
        <name>sqlDatabaseConnectionString</name>
        <value>"jdbc:sqlserver://serverName.database.windows.net;user=sqlLogin;password=sqlPassword;database=oozietest"</value>
        </property>

        <property>
        <name>sqlDatabaseTableName</name>
        <value>mobiledata</value>
        </property>

        <property>
        <name>user.name</name>
        <value>admin</value>
        </property>

        <property>
        <name>oozie.wf.application.path</name>
        <value>wasbs://mycontainer@mystorageaccount.blob.core.windows.net/tutorials/useoozie</value>
        </property>
    </configuration>
    ```

    Většina informací v tomto souboru se používá k naplnění hodnot použitých v souborech `${nameNode}`workflow.xml nebo ooziewf.hql, například .  Pokud je cesta `wasbs` cesta, musíte použít úplnou cestu. Nezkracovat na `wasbs:///`pouhý . Položka `oozie.wf.application.path` definuje, kde najít soubor workflow.xml. Tento soubor obsahuje pracovní postup, který byl spuštěn touto úlohou.

3. Chcete-li vytvořit konfiguraci definice úlohy Oozie, použijte následující příkaz:

    ```bash
    nano job.xml
    ```

4. Po otevření editoru nano vložte upravený XML jako obsah souboru.

5. Chcete-li soubor uložit, vyberte **Ctrl+X**, zadejte **Y**a pak vyberte **Enter**.

## <a name="submit-and-manage-the-job"></a>Odeslání a správa úlohy

Následující kroky používají příkaz Oozie k odeslání a správě pracovních postupů Oozie v clusteru. Příkaz Oozie je přátelské rozhraní přes [Oozie REST API](https://oozie.apache.org/docs/4.1.0/WebServicesAPI.html).

> [!IMPORTANT]  
> Při použití příkazu Oozie, musíte použít fQDN pro hlavní uzel HDInsight. Tento hlavní název sítě je přístupný jenom z clusteru, nebo pokud je cluster ve virtuální síti Azure, z jiných počítačů ve stejné síti.

1. Chcete-li získat adresu URL služby Oozie, použijte následující příkaz:

    ```bash
    sed -n '/<name>oozie.base.url/,/<\/value>/p' /etc/oozie/conf/oozie-site.xml
    ```

    To vrátí informace, jako je následující XML:

    ```xml
    <name>oozie.base.url</name>
    <value>http://ACTIVE-HEADNODE-NAME.UNIQUEID.cx.internal.cloudapp.net:11000/oozie</value>
    ```

    Část `http://ACTIVE-HEADNODE-NAME.UNIQUEID.cx.internal.cloudapp.net:11000/oozie` je URL pro použití s příkazem Oozie.

2. Upravte kód a nahraďte adresu URL adresou URL, kterou jste obdrželi dříve. Chcete-li vytvořit proměnnou prostředí pro adresu URL, použijte následující, abyste ji nemuseli zadávat pro každý příkaz:

    ```bash
    export OOZIE_URL=http://HOSTNAMEt:11000/oozie
    ```

3. Chcete-li úlohu odeslat, použijte následující:

    ```bash
    oozie job -config job.xml -submit
    ```

    Tento příkaz načte `job.xml` informace o úloze a odešle je společnosti Oozie, ale nespustí ji.

    Po dokončení příkazu by měl vrátit ID úlohy, `0000005-150622124850154-oozie-oozi-W`například . Toto ID se používá ke správě úlohy.

4. Upravte níže uvedený `<JOBID>` kód, který nahradíi ID vráceným v předchozím kroku.  Chcete-li zobrazit stav úlohy, použijte následující příkaz:

    ```bash
    oozie job -info <JOBID>
    ```

    To vrátí informace, jako je následující text:

        Job ID : 0000005-150622124850154-oozie-oozi-W
        ------------------------------------------------------------------------------------------------------------------------------------
        Workflow Name : useooziewf
        App Path      : wasb:///tutorials/useoozie
        Status        : PREP
        Run           : 0
        User          : USERNAME
        Group         : -
        Created       : 2015-06-22 15:06 GMT
        Started       : -
        Last Modified : 2015-06-22 15:06 GMT
        Ended         : -
        CoordAction ID: -
        ------------------------------------------------------------------------------------------------------------------------------------

    Tato úloha má `PREP`stav . Tento stav označuje, že úloha byla vytvořena, ale nebyla spuštěna.

5. Upravte níže uvedený `<JOBID>` kód, který nahradíte dříve vráceným ID.  Chcete-li úlohu spustit, použijte následující příkaz:

    ```bash
    oozie job -start <JOBID>
    ```

    Pokud zaškrtnete stav po tomto příkazu, je ve spuštěném stavu a informace jsou vráceny pro akce v rámci úlohy.  Dokončení úlohy bude trvat několik minut.

6. Upravte níže uvedený `<serverName>` kód, který nahradínázev `<sqlLogin>` serveru Azure SQL, a přihlášením serveru Azure SQL.  *Po úspěšném dokončení úkolu* můžete pomocí následujícího příkazu ověřit, zda byla data vygenerována a exportována do tabulky databáze SQL.  Na výzvu zadejte heslo.

    ```bash
    TDSVER=8.0 tsql -H <serverName>.database.windows.net -U <sqlLogin> -p 1433 -D oozietest
    ```

    Na `1>` výzvu zadejte následující dotaz:

    ```sql
    SELECT * FROM mobiledata
    GO
    ```

    Vrácené informace se podobu následujícího textu:

        deviceplatform  count
        Android 31591
        iPhone OS       22731
        proprietary development 3
        RIM OS  3464
        Unknown 213
        Windows Phone   1791
        (6 rows affected)

Další informace o příkazu Oozie naleznete v [tématu Apache Oozie command-line tool](https://oozie.apache.org/docs/4.1.0/DG_CommandLineTool.html).

## <a name="oozie-rest-api"></a>Oozie REST API

S rozhraním Oozie REST API můžete vytvářet vlastní nástroje, které pracují s Oozie. Následuje informace specifické pro HDInsight o použití rozhraní Oozie REST API:

* **Identifikátor URI**: K rozhraní REST API `https://CLUSTERNAME.azurehdinsight.net/oozie`můžete přistupovat mimo cluster na adrese .

* **Ověřování**: K ověření použijte účet HTTP (správce) clusteru a heslo. Například:

    ```bash
    curl -u admin:PASSWORD https://CLUSTERNAME.azurehdinsight.net/oozie/versions
    ```

Další informace o použití rozhraní OOZIE REST API naleznete v [tématu Apache Oozie Web Services API](https://oozie.apache.org/docs/4.1.0/WebServicesAPI.html).

## <a name="oozie-web-ui"></a>Webové uživatelské uzlina Oozie

Webové uživatelské uživatelské uživatelské informace společnosti Oozie poskytuje webový pohled na stav úloh Oozie v clusteru. Pomocí webového uživatelského uživatelského panelu můžete zobrazit následující informace:

   * Stav úlohy
   * Definice úlohy
   * Konfigurace
   * Graf akcí v úloze
   * Protokoly pro úlohu

Můžete také zobrazit podrobnosti o akcích v rámci projektu.

Chcete-li získat přístup k webovému uživatelskému uživatelskému uživatelskému nastavení Oozie, proveďte následující kroky:

1. Vytvořte tunelssh s hřbetu hdinsight. Další informace naleznete [v tématu Použití tunelového propojení SSH s rozhraním HDInsight](hdinsight-linux-ambari-ssh-tunnel.md).

2. Po vytvoření tunelu otevřete webové uživatelské uživatelské prostředí Ambari `http://headnodehost:8080`ve webovém prohlížeči pomocí identifikátoru URI .

3. Na levé straně stránky vyberte **Oozie** > **Rychlé odkazy** > **Oozie Web UI**.

    ![Apache Ambari oozie web ui kroky](./media/hdinsight-use-oozie-linux-mac/hdi-oozie-web-ui-steps.png)

4. Výchozí uživatelské nastavení oozie webové ho zobrazit spuštěné úlohy pracovního postupu. Chcete-li zobrazit všechny úlohy pracovního postupu, vyberte **možnost Všechny úlohy**.

    ![Pracovní chod webové konzoly Oozie](./media/hdinsight-use-oozie-linux-mac/hdinsight-oozie-jobs.png)

5. Chcete-li zobrazit další informace o úloze, vyberte ji.

    ![HDInsight Apache Oozie informace o práci](./media/hdinsight-use-oozie-linux-mac/hdinsight-oozie-job-info.png)

6. Na kartě **Informace o úloze** můžete zobrazit základní informace o úloze a jednotlivé akce v rámci úlohy. Pomocí karet v horní části můžete zobrazit **definici úlohy**, **konfiguraci úlohy**, přístup k **protokolu úloh**nebo zobrazit směrovaný acyklický graf (DAG) úlohy v části **Dag.**

   * **Protokol úloh**: Chcete-li získat všechny protokoly pro úlohu, vyberte tlačítko **Získat protokoly** nebo protokoly filtrujte pomocí pole **Zadat filtr hledání.**

       ![Protokol úloh HDInsight Apache Oozie](./media/hdinsight-use-oozie-linux-mac/hdinsight-oozie-job-log.png)

   * **Práce DAG**: DAG je grafický přehled datových cest přijatých prostřednictvím pracovního postupu.

       ![HDInsight Apache Oozie práce dag](./media/hdinsight-use-oozie-linux-mac/hdinsight-oozie-job-dag.png)

7. Pokud vyberete jednu z akcí na kartě **Informace o projektu,** zobrazí se informace o akci. Vyberte například akci **RunSqoopExport.**

    ![HDInsight oozie pracovní akce info](./media/hdinsight-use-oozie-linux-mac/oozie-job-action-info.png)

8. Můžete zobrazit podrobnosti o akci, například odkaz na **adresu URL konzoly**. Tento odkaz slouží k zobrazení informací o sledování úlohy pro úlohu.

## <a name="schedule-jobs"></a>Naplánovat úlohy

Pomocí koordinátora můžete určit začátek, konec a četnost výskytů pro úlohy. Chcete-li definovat plán pracovního postupu, proveďte následující kroky:

1. K vytvoření souboru s názvem **coordinator.xml**použijte následující příkaz :

    ```bash
    nano coordinator.xml
    ```

    Jako obsah souboru použijte následující xml:

    ```xml
    <coordinator-app name="my_coord_app" frequency="${coordFrequency}" start="${coordStart}" end="${coordEnd}" timezone="${coordTimezone}" xmlns="uri:oozie:coordinator:0.4">
        <action>
        <workflow>
            <app-path>${workflowPath}</app-path>
        </workflow>
        </action>
    </coordinator-app>
    ```

    > [!NOTE]  
    > `${...}` Proměnné jsou nahrazeny hodnotami v definici úlohy za běhu. Proměnné jsou:
    >
    > * `${coordFrequency}`: Doba mezi spuštěním instancí úlohy.
    > * `${coordStart}`: Čas zahájení úlohy.
    > * `${coordEnd}`: Čas ukončení úlohy.
    > * `${coordTimezone}`: Úlohy koordinátora jsou v pevném časovém pásmu bez letního času, obvykle reprezentované pomocí času UTC. Toto časové pásmo se označuje jako *časové pásmo zpracování Oozie.*
    > * `${wfPath}`: Cesta k souboru workflow.xml.

2. Chcete-li soubor uložit, vyberte **Ctrl+X**, zadejte **Y**a pak vyberte **Enter**.

3. Chcete-li soubor zkopírovat do pracovního adresáře pro tuto úlohu, použijte následující příkaz:

    ```bash
    hadoop fs -put coordinator.xml /tutorials/useoozie/coordinator.xml
    ```

4. Chcete-li `job.xml` upravit soubor, který jste vytvořili dříve, použijte následující příkaz:

    ```bash
    nano job.xml
    ```

    Proveďte následující změny:

   * Chcete-li ooziemu dát pokyn ke spuštění `<name>oozie.wf.application.path</name>` `<name>oozie.coord.application.path</name>`souboru koordinátora namísto pracovního postupu, změňte na .

   * Chcete-li `workflowPath` nastavit proměnnou používanou koordinátorem, přidejte následující xml:

        ```xml
        <property>
            <name>workflowPath</name>
            <value>wasbs://mycontainer@mystorageaccount.blob.core.windows.net/tutorials/useoozie</value>
        </property>
        ```

       Nahraďte `wasbs://mycontainer@mystorageaccount.blob.core.windows` text hodnotou použitou v ostatních položkách v souboru job.xml.

   * Chcete-li definovat začátek, konec a frekvenci koordinátora, přidejte následující XML:

        ```xml
        <property>
            <name>coordStart</name>
            <value>2018-05-10T12:00Z</value>
        </property>

        <property>
            <name>coordEnd</name>
            <value>2018-05-12T12:00Z</value>
        </property>

        <property>
            <name>coordFrequency</name>
            <value>1440</value>
        </property>

        <property>
            <name>coordTimezone</name>
            <value>UTC</value>
        </property>
        ```

       Tyto hodnoty nastavují čas zahájení na 12:00 PM na květen 10, 2018 a koncový čas na květen 12, 2018. Interval pro spuštění této úlohy je nastaven na denní. Frekvence je v minutách, takže 24 hodin x 60 minut = 1440 minut. Nakonec je časové pásmo nastaveno na Čas UTC.

5. Chcete-li soubor uložit, vyberte **Ctrl+X**, zadejte **Y**a pak vyberte **Enter**.

6. Chcete-li úlohu odeslat a spustit, použijte následující příkaz:

    ```bash
    oozie job -config job.xml -run
    ```

7. Pokud přejdete na webové uživatelské uzulinu Oozie a vyberete kartu **Úlohy koordinátora,** zobrazí se informace jako na následujícím obrázku:

    ![Oozie web console koordinátor práce kartu](./media/hdinsight-use-oozie-linux-mac/coordinator-jobs-tab.png)

    Položka **Další materializace** obsahuje při příštím spuštění úlohy.

8. Stejně jako předchozí úloha pracovního postupu, pokud vyberete položku úlohy ve webovém uživatelském počítači, zobrazí se informace o úloze:

    ![Apache Oozie koordinátor informace o práci](./media/hdinsight-use-oozie-linux-mac/coordinator-job-info.png)

    > [!NOTE]  
    > Tento obrázek zobrazuje pouze úspěšné spuštění úlohy, nikoli jednotlivé akce v rámci naplánovaného pracovního postupu. Chcete-li zobrazit jednotlivé akce, vyberte jednu z položek **akce.**

    ![OOzie web console informace o práci kartu](./media/hdinsight-use-oozie-linux-mac/coordinator-action-job.png)

## <a name="troubleshooting"></a>Řešení potíží

Pomocí ui Oozie můžete zobrazit protokoly Oozie. Oozie UI také obsahuje odkazy na jobtracker protokoly pro MapReduce úkoly, které byly spuštěny pracovního postupu. Vzor pro řešení potíží by měl být:

   1. Zobrazit úlohu v oozie webové uživatelského uživatelského pole.

   2. Pokud dojde k chybě nebo selhání pro konkrétní akci, vyberte akci a zjistěte, zda pole **Chybová zpráva** obsahuje další informace o selhání.

   3. Pokud je k dispozici, použijte adresu URL z akce k zobrazení dalších podrobností, jako jsou protokoly JobTracker, pro akci.

Následují konkrétní chyby, se kterými se můžete setkat, a způsob jejich řešení.

### <a name="ja009-cannot-initialize-cluster"></a>JA009: Nelze inicializovat cluster

**Příznaky**: Stav úlohy se změní na **POZASTAVENO**. Podrobnosti o úloze zobrazují `RunHiveScript` stav jako **START_MANUAL**. Při výběru akce se zobrazí následující chybová zpráva:

    JA009: Cannot initialize Cluster. Please check your configuration for map

**Příčina**: Adresy úložiště objektů blob Azure použité v souboru **job.xml** neobsahují název kontejneru úložiště nebo účtu úložiště. Formát adresy úložiště objektů `wasbs://containername@storageaccountname.blob.core.windows.net`blob musí být .

**Řešení**: Změna adres úložiště objektů Blob, které úloha používá.

### <a name="ja002-oozie-is-not-allowed-to-impersonate-ltusergt"></a>JA002: Oozie není dovoleno &lt;vydávat se za uživatele&gt;

**Příznaky**: Stav úlohy se změní na **POZASTAVENO**. Podrobnosti o úloze zobrazují `RunHiveScript` stav jako **START_MANUAL**. Pokud vyberete akci, zobrazí se následující chybová zpráva:

    JA002: User: oozie is not allowed to impersonate <USER>

**Příčina**: Aktuální nastavení oprávnění neumožňuje společnosti Oozie zosobnit zadaný uživatelský účet.

**Řešení**: Oozie může zosobnit uživatele ve skupině **uživatelů.** Pomocí `groups USERNAME` zobrazíte skupiny, kterých je uživatelský účet členem. Pokud uživatel není členem skupiny **Users,** přidejte uživatele do skupiny pomocí následujícího příkazu:

    sudo adduser USERNAME users

> [!NOTE]  
> Může trvat několik minut, než HDInsight rozpozná, že uživatel byl přidán do skupiny.

### <a name="launcher-error-sqoop"></a>Chyba spouštěče (Sqoop)

**Příznaky**: Stav úlohy se změní na **KILLED**. Podrobnosti o úloze zobrazují `RunSqoopExport` stav jako **CHYBA**. Pokud vyberete akci, zobrazí se následující chybová zpráva:

    Launcher ERROR, reason: Main class [org.apache.oozie.action.hadoop.SqoopMain], exit code [1]

**Příčina**: Sqoop nemůže načíst ovladač databáze potřebný pro přístup k databázi.

**Řešení**: Při použití Sqoop z úlohy Oozie, musíte zahrnout ovladač databáze s ostatními prostředky, jako je například workflow.xml, úloha používá. Také odkazovat na archiv, který `<sqoop>...</sqoop>` obsahuje ovladač databáze z části workflow.xml.

Například pro úlohu v tomto dokumentu byste použili následující kroky:

1. Zkopírujte `mssql-jdbc-7.0.0.jre8.jar` soubor do adresáře **/tutorials/useoozie:**

    ```bash
    hdfs dfs -put /usr/share/java/sqljdbc_7.0/enu/mssql-jdbc-7.0.0.jre8.jar /tutorials/useoozie/mssql-jdbc-7.0.0.jre8.jar
    ```

2. Upravte `workflow.xml` chcete-li přidat následující XML `</sqoop>`na nový řádek výše :

    ```xml
    <archive>mssql-jdbc-7.0.0.jre8.jar</archive>
    ```

## <a name="next-steps"></a>Další kroky

V tomto článku jste se naučili, jak definovat pracovní postup Oozie a jak spustit úlohu Oozie. Další informace o práci s HDInsightem najdete v následujících článcích:

* [Nahrání dat pro úlohy Apache Hadoop v HDInsightu][hdinsight-upload-data]
* [Použijte Apache Sqoop s Apache Hadoop v HDInsight][hdinsight-use-sqoop]
* [Použití Apache Hive s Apache Hadoop na HDInsight][hdinsight-use-hive]
* [Vývoj programů Java MapReduce pro HDInsight](hadoop/apache-hadoop-develop-deploy-java-mapreduce-linux.md)

[azure-data-factory-pig-hive]: ../data-factory/transform-data.md
[hdinsight-versions]:  hdinsight-component-versioning.md
[hdinsight-use-sqoop]:hadoop/apache-hadoop-use-sqoop-mac-linux.md
[hdinsight-upload-data]: hdinsight-upload-data.md
[hdinsight-use-hive]:hadoop/hdinsight-use-hive.md
