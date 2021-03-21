---
title: Použití pracovních postupů Hadoop Oozie v Azure HDInsight se systémem Linux
description: Použijte Hadoop Oozie v HDInsight se systémem Linux. Naučte se definovat pracovní postup Oozie a odeslat úlohu Oozie.
ms.service: hdinsight
ms.topic: how-to
ms.custom: seoapr2020
ms.date: 04/27/2020
ms.openlocfilehash: b72d94c1cba3ea0476c6d44e68583f6668b167e2
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/20/2021
ms.locfileid: "101702772"
---
# <a name="use-apache-oozie-with-apache-hadoop-to-define-and-run-a-workflow-on-linux-based-azure-hdinsight"></a>Použití Apache Oozie s Apache Haddopem k definování a spuštění pracovního procesu v linuxové službě Azure HDInsight

Naučte se používat Apache Oozie s Apache Hadoop v Azure HDInsight. Oozie je pracovní postup a systém koordinace, který spravuje úlohy systému Hadoop. Oozie je integrovaný do zásobníku Hadoop a podporuje následující úlohy:

* Apache Hadoop MapReduce
* Apache prasete
* Apache Hive
* Apache Sqoop

Oozie můžete použít také k plánování úloh, které jsou specifické pro systém, jako jsou programy Java nebo skripty prostředí.

> [!NOTE]  
> Další možností definování pracovních postupů pomocí HDInsight je použití Azure Data Factory. Další informace o Data Factory najdete v tématu [použití nástroje Apache prasete a Apache Hive s Data Factory](../data-factory/transform-data.md). Pokud chcete používat Oozie v clusterech s Balíček zabezpečení podniku, přečtěte si téma [spuštění Apache Oozie v clusterech HDInsight Hadoop s balíček zabezpečení podniku](domain-joined/hdinsight-use-oozie-domain-joined-clusters.md).

## <a name="prerequisites"></a>Předpoklady

* **Cluster Hadoop ve službě HDInsight**. Viz Začínáme [se službou HDInsight v systému Linux](hadoop/apache-hadoop-linux-tutorial-get-started.md).

* **Klient SSH**. Další informace najdete v tématu [připojení ke službě HDInsight (Apache Hadoop) pomocí SSH](hdinsight-hadoop-linux-use-ssh-unix.md).

* **Azure SQL Database**.  Viz téma [Vytvoření databáze v Azure SQL Database Azure Portal](../azure-sql/database/single-database-create-quickstart.md).  V tomto článku se používá databáze s názvem **oozietest**.

* Schéma identifikátoru URI pro primární úložiště clusterů. `wasb://` pro Azure Storage pro `abfs://` Azure Data Lake Storage Gen2 nebo `adl://` pro Azure Data Lake Storage Gen1. Pokud je pro Azure Storage povolený zabezpečený přenos, identifikátor URI by byl `wasbs://` . Viz také [zabezpečený přenos](../storage/common/storage-require-secure-transfer.md).

## <a name="example-workflow"></a>Ukázkový pracovní postup

Pracovní postup použitý v tomto dokumentu obsahuje dvě akce. Akce jsou definice pro úlohy, jako je například spuštění podregistru, Sqoop, MapReduce nebo jiné procesy:

![Diagram pracovního postupu HDInsight Oozie](./media/hdinsight-use-oozie-linux-mac/oozie-workflow-diagram.png)

1. Akce podregistru spustí skript HiveQL, který extrahuje záznamy z rozhraní `hivesampletable` , které je součástí služby HDInsight. Každý řádek dat popisuje návštěvu z konkrétního mobilního zařízení. Formát záznamu se zobrazí jako následující text:

    ```output
    8       18:54:20        en-US   Android Samsung SCH-i500        California     United States    13.9204007      0       0
    23      19:19:44        en-US   Android HTC     Incredible      Pennsylvania   United States    NULL    0       0
    23      19:19:46        en-US   Android HTC     Incredible      Pennsylvania   United States    1.4757422       0       1
    ```

    Skript podregistru použitý v tomto dokumentu počítá celkový počet návštěv pro každou platformu, například Android nebo iPhone, a ukládá počty do nové tabulky podregistru.

    Další informace o podregistru najdete v tématu [použití Apache Hive se službou HDInsight] [HDInsight-use-podregistr].

2. Akce Sqoop Exportuje obsah nové tabulky podregistru do tabulky vytvořené v Azure SQL Database. Další informace o Sqoop najdete v tématu [použití Apache Sqoop se službou HDInsight](hadoop/apache-hadoop-use-sqoop-mac-linux.md).

> [!NOTE]  
> Podporované verze Oozie v clusterech HDInsight najdete v tématu [co je nového ve verzích clusterů Hadoop poskytovaných službou HDInsight](hdinsight-component-versioning.md).

## <a name="create-the-working-directory"></a>Vytvořit pracovní adresář

Oozie očekává, že budete ukládat všechny prostředky, které jsou potřeba pro úlohu ve stejném adresáři. Tento příklad používá `wasbs:///tutorials/useoozie`. Tento adresář vytvoříte tak, že provedete následující kroky:

1. Úpravou následujícího kódu nahraďte `sshuser` uživatelské jméno SSH pro cluster a nahraďte `CLUSTERNAME` názvem clusteru.  Pak zadejte kód pro připojení ke clusteru HDInsight [pomocí SSH](hdinsight-hadoop-linux-use-ssh-unix.md).  

    ```bash
    ssh sshuser@CLUSTERNAME-ssh.azurehdinsight.net
    ```

2. Chcete-li vytvořit adresář, použijte následující příkaz:

    ```bash
    hdfs dfs -mkdir -p /tutorials/useoozie/data
    ```

    > [!NOTE]  
    > `-p`Parametr způsobí vytvoření všech adresářů v cestě. `data`Adresář slouží k uložení dat používaných `useooziewf.hql` skriptem.

3. Úpravou kódu níže nahraďte `sshuser` uživatelské jméno SSH.  Abyste se ujistili, že Oozie může zosobnit svůj uživatelský účet, použijte následující příkaz:

    ```bash
    sudo adduser sshuser users
    ```

    > [!NOTE]  
    > Můžete ignorovat chyby indikující, že uživatel je již členem `users` skupiny.

## <a name="add-a-database-driver"></a>Přidání ovladače databáze

Tento pracovní postup používá Sqoop k exportu dat do databáze SQL. Proto musíte poskytnout kopii ovladače JDBC, který se používá k interakci s databází SQL. K zkopírování ovladače JDBC do pracovního adresáře použijte následující příkaz z relace SSH:

```bash
hdfs dfs -put /usr/share/java/sqljdbc_7.0/enu/mssql-jdbc*.jar /tutorials/useoozie/
```

> [!IMPORTANT]  
> Ověřte skutečný ovladač JDBC, který existuje v umístění `/usr/share/java/` .

Pokud váš pracovní postup použil další prostředky, jako je například jar, který obsahuje aplikaci MapReduce, je nutné tyto prostředky také přidat.

## <a name="define-the-hive-query"></a>Definujte dotaz na podregistr.

Pomocí následujících kroků vytvořte skript HiveQL (podregistr Query Language), který definuje dotaz. Dotaz v pracovním postupu Oozie použijete později v tomto dokumentu.

1. Z připojení SSH pomocí následujícího příkazu vytvořte soubor s názvem `useooziewf.hql` :

    ```bash
    nano useooziewf.hql
    ```

1. Po otevření editoru GNU nano se jako obsah souboru použijte následující dotaz:

    ```hiveql
    DROP TABLE ${hiveTableName};
    CREATE EXTERNAL TABLE ${hiveTableName}(deviceplatform string, count string) ROW FORMAT DELIMITED
    FIELDS TERMINATED BY '\t' STORED AS TEXTFILE LOCATION '${hiveDataFolder}';
    INSERT OVERWRITE TABLE ${hiveTableName} SELECT deviceplatform, COUNT(*) as count FROM hivesampletable GROUP BY deviceplatform;
    ```

    Ve skriptu se používají dvě proměnné:

   * `${hiveTableName}`: Obsahuje název tabulky, která se má vytvořit.

   * `${hiveDataFolder}`: Obsahuje umístění pro uložení datových souborů pro tabulku.

     Soubor definice pracovního postupu, workflow.xml v tomto článku, předá tyto hodnoty do tohoto skriptu HiveQL za běhu.

1. Pokud chcete soubor uložit, vyberte **CTRL + X**, zadejte **Y** a pak vyberte **ENTER**.  

1. Následující příkaz použijte ke zkopírování `useooziewf.hql` do `wasbs:///tutorials/useoozie/useooziewf.hql` :

    ```bash
    hdfs dfs -put useooziewf.hql /tutorials/useoozie/useooziewf.hql
    ```

    Tento příkaz uloží `useooziewf.hql` soubor do úložiště kompatibilního se systémem HDFS pro cluster.

## <a name="define-the-workflow"></a>Definovat pracovní postup

Definice pracovních postupů Oozie se napíší v jazyce hPDL (Hadoop proces Definition Language), což je jazyk definice procesu XML. Pracovní postup můžete definovat pomocí následujících kroků:

1. Pomocí následujícího příkazu vytvořte a upravte nový soubor:

    ```bash
    nano workflow.xml
    ```

2. Po otevření editoru nano zadejte jako obsah souboru následující kód XML:

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

   * `RunHiveScript`: Tato akce je spouštěcí akce a spustí `useooziewf.hql` skript podregistru.

   * `RunSqoopExport`: Tato akce exportuje data vytvořená ze skriptu z podregistru do databáze SQL pomocí Sqoop. Tato akce se spustí jenom v případě, že `RunHiveScript` je akce úspěšná.

     Pracovní postup má několik záznamů, například `${jobTracker}` . Tyto položky nahradíte hodnotami, které použijete v definici úlohy. Definici úlohy vytvoříte později v tomto dokumentu.

     Také si poznamenejte `<archive>mssql-jdbc-7.0.0.jre8.jar</archive>` položku v části Sqoop. Tato položka dává pokyn pro Oozie k tomu, aby tento archiv byl k dispozici pro Sqoop při spuštění této akce.

3. Pokud chcete soubor uložit, vyberte **CTRL + X**, zadejte **Y** a pak vyberte **ENTER**.  

4. Pomocí následujícího příkazu zkopírujte `workflow.xml` soubor do `/tutorials/useoozie/workflow.xml` :

    ```bash
    hdfs dfs -put workflow.xml /tutorials/useoozie/workflow.xml
    ```

## <a name="create-a-table"></a>Vytvoření tabulky

> [!NOTE]  
> Existuje mnoho způsobů, jak se připojit k SQL Database vytvořit tabulku. V následujících krocích se používá [FreeTDS](https://www.freetds.org/) z clusteru HDInsight.

1. K instalaci FreeTDS na cluster HDInsight použijte následující příkaz:

    ```bash
    sudo apt-get --assume-yes install freetds-dev freetds-bin
    ```

2. Úpravou kódu níže nahraďte `<serverName>` [logickým názvem SQL serveru](../azure-sql/database/logical-servers.md) a `<sqlLogin>` přihlášením serveru.  Zadejte příkaz pro připojení k požadované databázi SQL.  Na příkazovém řádku zadejte heslo.

    ```bash
    TDSVER=8.0 tsql -H <serverName>.database.windows.net -U <sqlLogin> -p 1433 -D oozietest
    ```

    Zobrazí se výstup podobný následujícímu textu:

    ```output
    locale is "en_US.UTF-8"
    locale charset is "UTF-8"
    using default charset "UTF-8"
    Default database being set to oozietest
    1>
    ```

3. Na příkazovém řádku `1>` zadejte následující řádky:

    ```sql
    CREATE TABLE [dbo].[mobiledata](
    [deviceplatform] [nvarchar](50),
    [count] [bigint])
    GO
    CREATE CLUSTERED INDEX mobiledata_clustered_index on mobiledata(deviceplatform)
    GO
    ```

    Po zadání příkazu `GO` se vyhodnotí předchozí příkazy. Tyto příkazy vytvoří tabulku s názvem `mobiledata` , kterou používá pracovní postup.

    Chcete-li ověřit, zda byla tabulka vytvořena, použijte následující příkazy:

    ```sql
    SELECT * FROM information_schema.tables
    GO
    ```

    Zobrazí se výstup podobný následujícímu textu:

    ```output
    TABLE_CATALOG   TABLE_SCHEMA    TABLE_NAME      TABLE_TYPE
    oozietest       dbo             mobiledata      BASE TABLE
    ```

4. Ukončete nástroj TSQL zadáním `exit` na `1>` příkazovém řádku.

## <a name="create-the-job-definition"></a>Vytvoření definice úlohy

Definice úlohy popisuje, kde najít workflow.xml. Popisuje také místo, kde najít jiné soubory používané pracovním postupem, například `useooziewf.hql` . Také definuje hodnoty vlastností použitých v rámci pracovního postupu a přidružených souborů.

1. Chcete-li získat úplnou adresu výchozího úložiště, použijte následující příkaz. Tato adresa se používá v konfiguračním souboru, který jste vytvořili v dalším kroku.

    ```bash
    sed -n '/<name>fs.default/,/<\/value>/p' /etc/hadoop/conf/core-site.xml
    ```

    Tento příkaz vrátí informace jako následující kód XML:

    ```xml
    <name>fs.defaultFS</name>
    <value>wasbs://mycontainer@mystorageaccount.blob.core.windows.net</value>
    ```

    > [!NOTE]  
    > Pokud cluster HDInsight používá Azure Storage jako výchozí úložiště, `<value>` obsah elementu začíná na `wasbs://` . Pokud se místo toho použije Azure Data Lake Storage Gen1, začíná na `adl://` . Pokud se používá Azure Data Lake Storage Gen2, začíná na `abfs://` .

    Uložte obsah `<value>` elementu, jak je použit v dalším postupu.

2. Upravte XML následujícím způsobem:

    |Hodnota zástupného textu| Nahrazená hodnota|
    |---|---|
    |wasbs://mycontainer \@ mystorageaccount.blob.Core.Windows.NET| Hodnota přijatá z kroku 1.|
    |správce| Přihlašovací jméno pro cluster HDInsight, pokud není správce.|
    |serverName| Název serveru Azure SQL Database.|
    |sqlLogin| Přihlášení k serveru Azure SQL Database.|
    |sqlPassword| Přihlašovací heslo serveru Azure SQL Database.|

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

    Většina informací v tomto souboru slouží k naplnění hodnot použitých v souborech workflow.xml nebo ooziewf. HQL, jako je například `${nameNode}` .  Pokud se jedná o cestu `wasbs` , musíte použít úplnou cestu. Nezkraťte ho jenom na `wasbs:///` . `oozie.wf.application.path`Položka určuje, kde se má najít soubor workflow.xml. Tento soubor obsahuje pracovní postup, který byl spuštěn touto úlohou.

3. Chcete-li vytvořit konfiguraci definice úlohy Oozie, použijte následující příkaz:

    ```bash
    nano job.xml
    ```

4. Po otevření editoru nano vložte upravený XML jako obsah souboru.

5. Pokud chcete soubor uložit, vyberte **CTRL + X**, zadejte **Y** a pak vyberte **ENTER**.

## <a name="submit-and-manage-the-job"></a>Odeslat a spravovat úlohu

Následující kroky používají příkaz Oozie k odesílání a správě pracovních postupů Oozie v clusteru. Příkaz Oozie je uživatelsky přívětivé rozhraní přes rozhraní [Oozie REST API](https://oozie.apache.org/docs/4.1.0/WebServicesAPI.html).

> [!IMPORTANT]  
> Když použijete příkaz Oozie, musíte použít plně kvalifikovaný název domény pro hlavní uzel HDInsight. Tento plně kvalifikovaný název domény je dostupný jenom z clusteru, nebo pokud je cluster ve službě Azure Virtual Network, z jiných počítačů ve stejné síti.

1. Adresu URL služby Oozie získáte pomocí následujícího příkazu:

    ```bash
    sed -n '/<name>oozie.base.url/,/<\/value>/p' /etc/oozie/conf/oozie-site.xml
    ```

    Tato funkce vrátí informace jako následující kód XML:

    ```xml
    <name>oozie.base.url</name>
    <value>http://ACTIVE-HEADNODE-NAME.UNIQUEID.cx.internal.cloudapp.net:11000/oozie</value>
    ```

    `http://ACTIVE-HEADNODE-NAME.UNIQUEID.cx.internal.cloudapp.net:11000/oozie`Část je adresa URL, která se má použít s příkazem Oozie.

2. Upravte kód tak, aby nahradil adresu URL tu, kterou jste získali dříve. Chcete-li vytvořit proměnnou prostředí pro adresu URL, použijte následující, takže ji nemusíte zadávat pro každý příkaz:

    ```bash
    export OOZIE_URL=http://HOSTNAMEt:11000/oozie
    ```

3. K odeslání úlohy použijte následující kód:

    ```bash
    oozie job -config job.xml -submit
    ```

    Tento příkaz načte informace o úloze z `job.xml` a odešle ji do Oozie, ale nespustí ji.

    Po dokončení příkazu by měl vrátit ID úlohy, například `0000005-150622124850154-oozie-oozi-W` . Toto ID se používá ke správě úlohy.

4. Upravte kód níže, aby `<JOBID>` se nahradil identifikátorem vráceným v předchozím kroku.  Chcete-li zobrazit stav úlohy, použijte následující příkaz:

    ```bash
    oozie job -info <JOBID>
    ```

    Tato funkce vrátí informace jako následující text:

    ```output
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
    ```

    Tato úloha má stav `PREP` . Tento stav indikuje, že se úloha vytvořila, ale nezačala.

5. Upravte kód níže, aby `<JOBID>` se nahradil identifikátorem vráceným dříve.  Chcete-li spustit úlohu, použijte následující příkaz:

    ```bash
    oozie job -start <JOBID>
    ```

    Pokud po provedení tohoto příkazu zkontrolujete stav, je ve spuštěném stavu a pro akce v rámci úlohy se vrátí informace.  Dokončení úlohy bude trvat několik minut.

6. Níže uvedený kód nahraďte `<serverName>` názvem vašeho serveru a `<sqlLogin>` přihlášením k serveru.  *Po úspěšném dokončení úlohy* můžete ověřit, že data byla vygenerována a exportována do tabulky databáze SQL pomocí následujícího příkazu.  Na příkazovém řádku zadejte heslo.

    ```bash
    TDSVER=8.0 tsql -H <serverName>.database.windows.net -U <sqlLogin> -p 1433 -D oozietest
    ```

    Na `1>` příkazovém řádku zadejte následující dotaz:

    ```sql
    SELECT * FROM mobiledata
    GO
    ```

    Vrácené informace se podobají následujícímu textu:

    ```output
    deviceplatform  count
    Android 31591
    iPhone OS       22731
    proprietary development 3
    RIM OS  3464
    Unknown 213
    Windows Phone   1791
    (6 rows affected)
    ```

Další informace o příkazu Oozie najdete v tématu [Nástroj příkazového řádku Apache Oozie](https://oozie.apache.org/docs/4.1.0/DG_CommandLineTool.html).

## <a name="oozie-rest-api"></a>Oozie REST API

Pomocí REST API Oozie můžete vytvářet vlastní nástroje, které pracují s Oozie. Následující informace specifické pro HDInsight týkající se použití REST API Oozie:

* **Identifikátor URI**: přístup k REST API můžete získat mimo cluster na adrese `https://CLUSTERNAME.azurehdinsight.net/oozie` .

* **Ověřování**: k ověření použijte rozhraní API. účet http clusteru (správce) a heslo. Například:

    ```bash
    curl -u admin:PASSWORD https://CLUSTERNAME.azurehdinsight.net/oozie/versions
    ```

Další informace o tom, jak používat REST API Oozie, najdete v tématu [rozhraní API pro webové služby Apache Oozie](https://oozie.apache.org/docs/4.1.0/WebServicesAPI.html).

## <a name="oozie-web-ui"></a>Webové uživatelské rozhraní Oozie

Webové uživatelské rozhraní Oozie poskytuje webové zobrazení na stav úloh Oozie v clusteru. S webovým uživatelským rozhraním můžete zobrazit následující informace:

   * Stav úlohy
   * Definice úlohy
   * Konfigurace
   * Graf akcí v úloze
   * Protokoly pro úlohu

Můžete si také prohlédnout podrobnosti o akcích v rámci úlohy.

Chcete-li získat přístup k webovému uživatelskému rozhraní Oozie, proveďte následující kroky:

1. Vytvořte tunel SSH pro cluster HDInsight. Další informace najdete v tématu [použití tunelového propojení SSH se službou HDInsight](hdinsight-linux-ambari-ssh-tunnel.md).

2. Po vytvoření tunelu otevřete webové uživatelské rozhraní Ambari ve webovém prohlížeči pomocí identifikátoru URI `http://headnodehost:8080` .

3. Na levé straně stránky vyberte **Oozie**  >  **Rychlé odkazy**  >  **Oozie web UI**.

    ![Kroky pro webové uživatelské rozhraní Apache Ambari Oozie](./media/hdinsight-use-oozie-linux-mac/hdi-oozie-web-ui-steps.png)

4. Webové uživatelské rozhraní Oozie ve výchozím nastavení zobrazuje spuštěné úlohy pracovního postupu. Chcete-li zobrazit všechny úlohy pracovního postupu, vyberte možnost **všechny úlohy**.

    ![Úlohy pracovního postupu webové konzoly Oozie](./media/hdinsight-use-oozie-linux-mac/hdinsight-oozie-jobs.png)

5. Chcete-li zobrazit další informace o úloze, vyberte úlohu.

    ![Informace o úloze HDInsight Apache Oozie](./media/hdinsight-use-oozie-linux-mac/hdinsight-oozie-job-info.png)

6. Na kartě **informace o úloze** můžete zobrazit základní informace o úloze a jednotlivé akce v rámci úlohy. Karty v horní části můžete použít k zobrazení **definice úlohy**, **Konfigurace úlohy**, přístupu k **protokolu úlohy** nebo zobrazení orientovaného acyklického grafu (DAG) úlohy v rámci **úlohy DAG**.

   * **Protokol úlohy**: kliknutím na tlačítko **získat protokoly** zobrazíte všechny protokoly pro úlohu, nebo použijte pole **zadat vyhledávací filtr** k filtrování protokolů.

       ![Protokol úloh HDInsight Apache Oozie](./media/hdinsight-use-oozie-linux-mac/hdinsight-oozie-job-log.png)

   * **DAG úlohy**: Dag je grafický přehled cest k datům pořízených prostřednictvím pracovního postupu.

       ![DAG úlohy HDInsight Apache Oozie](./media/hdinsight-use-oozie-linux-mac/hdinsight-oozie-job-dag.png)

7. Pokud vyberete jednu z akcí na kartě **informace o úloze** , zobrazí se informace o akci. Vyberte například akci **RunSqoopExport** .

    ![Informace o akci úlohy Oozie HDInsight](./media/hdinsight-use-oozie-linux-mac/oozie-job-action-info.png)

8. Můžete zobrazit podrobnosti o akci, jako je například odkaz na **adresu URL konzoly**. Pomocí tohoto odkazu můžete zobrazit informace o sledování úloh pro úlohu.

## <a name="schedule-jobs"></a>Plánování úloh

Koordinátora můžete použít k určení začátku, konce a frekvence výskytu úloh. Chcete-li definovat plán pro pracovní postup, proveďte následující kroky:

1. Pomocí následujícího příkazu vytvořte soubor s názvem **coordinator.xml**:

    ```bash
    nano coordinator.xml
    ```

    Jako obsah souboru použijte následující kód XML:

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
    > `${...}`Proměnné jsou nahrazeny hodnotami v definici úlohy za běhu. Proměnné jsou:
    >
    > * `${coordFrequency}`: Čas mezi běžícími instancemi úlohy.
    > * `${coordStart}`: Čas zahájení úlohy.
    > * `${coordEnd}`: Čas ukončení úlohy.
    > * `${coordTimezone}`: Úlohy koordinátora jsou v pevně stanoveném časovém pásmu bez letního času, který je obvykle reprezentován pomocí standardu UTC. Toto časové pásmo se označuje jako *časové pásmo zpracování Oozie.*
    > * `${wfPath}`: Cesta k workflow.xml.

2. Pokud chcete soubor uložit, vyberte **CTRL + X**, zadejte **Y** a pak vyberte **ENTER**.

3. Chcete-li zkopírovat soubor do pracovního adresáře pro tuto úlohu, použijte následující příkaz:

    ```bash
    hadoop fs -put coordinator.xml /tutorials/useoozie/coordinator.xml
    ```

4. Chcete-li upravit `job.xml` soubor, který jste vytvořili dříve, použijte následující příkaz:

    ```bash
    nano job.xml
    ```

    Proveďte následující změny:

   * Pokud chcete, aby Oozie spustil soubor koordinátora místo pracovního postupu, změňte `<name>oozie.wf.application.path</name>` na `<name>oozie.coord.application.path</name>` .

   * Chcete-li nastavit `workflowPath` proměnnou použitou koordinátorem, přidejte následující kód XML:

        ```xml
        <property>
            <name>workflowPath</name>
            <value>wasbs://mycontainer@mystorageaccount.blob.core.windows.net/tutorials/useoozie</value>
        </property>
        ```

       Nahraďte `wasbs://mycontainer@mystorageaccount.blob.core.windows` text hodnotou použitou v ostatních položkách v souboru job.xml.

   * Pro definování počátečního, koncového a četnosti koordinátoru přidejte následující kód XML:

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

       Tyto hodnoty nastaví počáteční čas na 12:00 odp. května 2018 a koncový čas do 12. května 2018. Interval pro spuštění této úlohy je nastaven na denně. Frekvence je v minutách, takže 24 hodin × 60 minut = 1440 minut. Nakonec je časové pásmo nastaveno na hodnotu UTC.

5. Pokud chcete soubor uložit, vyberte **CTRL + X**, zadejte **Y** a pak vyberte **ENTER**.

6. Chcete-li odeslat a spustit úlohu, použijte následující příkaz:

    ```bash
    oozie job -config job.xml -run
    ```

7. Pokud přejdete na webové uživatelské rozhraní Oozie a vyberete kartu **úlohy koordinátora** , zobrazí se vám informace, jako na následujícím obrázku:

    ![Karta úlohy koordinátora webové konzoly Oozie](./media/hdinsight-use-oozie-linux-mac/coordinator-jobs-tab.png)

    **Další položka Vymaterializování** obsahuje při příštím spuštění úlohy.

8. Podobně jako u předchozí úlohy pracovního postupu, pokud vyberete položku úlohy ve webovém uživatelském rozhraní, zobrazí se informace o úloze:

    ![Informace o úloze koordinátoru Apache Oozie](./media/hdinsight-use-oozie-linux-mac/coordinator-job-info.png)

    > [!NOTE]  
    > Tento obrázek zobrazuje pouze úspěšné běhy úlohy, nikoli jednotlivé akce v rámci naplánovaného pracovního postupu. Chcete-li zobrazit jednotlivé akce, vyberte jednu z položek **Akce** .

    ![Karta informace o úloze webové konzoly OOzie](./media/hdinsight-use-oozie-linux-mac/coordinator-action-job.png)

## <a name="next-steps"></a>Další kroky

V tomto článku jste zjistili, jak definovat pracovní postup Oozie a jak spustit úlohu Oozie. Další informace o tom, jak pracovat se službou HDInsight, najdete v následujících článcích:

* [Nahrávání dat pro úlohy Apache Hadoop v HDInsight](hdinsight-upload-data.md)
* [Použití Apache Sqoop s Apache Hadoop ve službě HDInsight](hadoop/apache-hadoop-use-sqoop-mac-linux.md)
* [Použití Apache Hive s Apache Hadoop v HDInsight](hadoop/hdinsight-use-hive.md)
* [Řešení potíží s Apache Oozie](./troubleshoot-oozie.md)