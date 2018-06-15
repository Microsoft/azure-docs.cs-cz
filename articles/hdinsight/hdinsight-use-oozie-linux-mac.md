---
title: Použití pracovních postupů Hadoop Oozie v HDInsight se systémem Linux Azure | Microsoft Docs
description: Použijte Hadoop Oozie v HDInsight se systémem Linux. Zjistěte, jak definovat pracovním postupu Oozie a odeslat úlohu Oozie.
services: hdinsight
documentationcenter: ''
author: Blackmist
manager: jhubbard
editor: cgronlun
tags: azure-portal
ms.assetid: d7603471-5076-43d1-8b9a-dbc4e366ce5d
ms.service: hdinsight
ms.custom: hdinsightactive
ms.devlang: na
ms.topic: conceptual
ms.date: 04/23/2018
ms.author: larryfr
ms.openlocfilehash: 8a25507ab076c4eecccea4e8a503d68ff1441ae5
ms.sourcegitcommit: e2adef58c03b0a780173df2d988907b5cb809c82
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/28/2018
ms.locfileid: "32179074"
---
# <a name="use-oozie-with-hadoop-to-define-and-run-a-workflow-on-linux-based-azure-hdinsight"></a>Použití Oozie se systémem Hadoop k definování a spuštění workflowu v Azure HDInsight se systémem Linux

[!INCLUDE [oozie-selector](../../includes/hdinsight-oozie-selector.md)]

Další informace o použití Apache Oozie s Hadoop v Azure HDInsight. Oozie je systém koordinace a pracovního postupu, který spravuje úloh Hadoop. Oozie je integrována do zásobníku Hadoop a podporuje následující úlohy:

* Apache MapReduce
* Apache Pig
* Apache Hive
* Apache Sqoop

Můžete taky Oozie při plánování úloh, které jsou specifické pro systém, jako jsou programy v jazyce Java nebo skripty prostředí.

> [!NOTE]
> Další možností k definování pracovních postupů v prostředí HDInsight je pomocí Azure Data Factory. Další informace o Data Factory najdete v tématu [použijte Pig a Hive pomocí služby Data Factory][azure-data-factory-pig-hive].

> [!IMPORTANT]
> Oozie není povoleno v doméně HDInsight.

## <a name="prerequisites"></a>Požadavky

* **Cluster služby HDInsight**: najdete v části [Začínáme s prostředím HDInsight v Linuxu](/hadoop/apache-hadoop-linux-tutorial-get-started.md)

> [!IMPORTANT]
> Kroky v tomto dokumentu vyžadují clusteru služby HDInsight, který používá Linux. Linux je jenom operační systém používaný v HDInsight verze 3.4 nebo novější. Další informace najdete v tématu [Vyřazení prostředí HDInsight ve Windows](hdinsight-component-versioning.md#hdinsight-windows-retirement).

## <a name="example-workflow"></a>Příklad pracovního postupu

Pracovní postup v tomto dokumentu obsahuje dvě akce. Akce jsou definice pro úlohy, jako je například spuštění Hive, Sqoop, MapReduce nebo jiné procesy:

![Diagram pracovního postupu][img-workflow-diagram]

1. Akce Hive spouští skript HiveQL k extrakci záznamy ze **hivesampletable** má součástí HDInsight. Každý řádek dat popisuje návštěvu z určité mobilní zařízení. Formát záznamu se zobrazí jako následující text:

        8       18:54:20        en-US   Android Samsung SCH-i500        California     United States    13.9204007      0       0
        23      19:19:44        en-US   Android HTC     Incredible      Pennsylvania   United States    NULL    0       0
        23      19:19:46        en-US   Android HTC     Incredible      Pennsylvania   United States    1.4757422       0       1

    V tomto dokumentu skriptu Hive počítá celkový počet návštěv pro každou platformu, třeba na Android nebo iPhone a ukládá počty na novou tabulku Hive.

    Další informace o Hivu najdete v tématu [Použití Hivu se službou HDInsight][hdinsight-use-hive].

2. Akce Sqoop exportuje obsah novou tabulku Hive do tabulky v databázi SQL Azure. Další informace o Sqoop najdete v tématu [Sqoop pomocí Hadoop v prostředí HDInsight][hdinsight-use-sqoop].

> [!NOTE]
> Podporované verze Oozie v clusterech prostředí HDInsight najdete v tématu [co je nového ve verzích clusterů systému Hadoop poskytovaných v HDInsight][hdinsight-versions].

## <a name="create-the-working-directory"></a>Vytvořte pracovní adresář

Oozie se očekává, že budete ukládat všechny prostředky potřebné pro úlohu ve stejném adresáři. Tento příklad používá **wasb: / / / kurzy/useoozie**. Pokud chcete vytvořit tento adresář, proveďte následující kroky:

1. Připojte se ke clusteru HDInsight pomocí protokolu SSH:

    ```bash
    ssh sshuser@clustername-ssh.azurehdinsight.net
    ```

    Nahraďte `sshuser` s uživatelským jménem SSH pro cluster. Nahraďte `clustername` s názvem clusteru. Další informace najdete v tématu [Použití SSH se službou HDInsight](hdinsight-hadoop-linux-use-ssh-unix.md).

2. Chcete-li vytvořit adresář, použijte následující příkaz:

    ```bash
    hdfs dfs -mkdir -p /tutorials/useoozie/data
    ```

    > [!NOTE]
    > `-p` Parametr způsobí, že vytváření všech adresářů v cestě. **Data** directory se používá k ukládání dat používané **useooziewf.hql** skriptu.

3. Abyste měli jistotu, že váš uživatelský účet může vystupovat jako Oozie, použijte následující příkaz:

    ```bash
    sudo adduser username users
    ```

    Nahraďte `username` svým uživatelským jménem SSH.

    > [!NOTE]
    > Můžete ignorovat, které označují, uživatel je již členem `users` skupiny.

## <a name="add-a-database-driver"></a>Přidat ovladač databáze

Protože tento pracovní postup používá Sqoop export dat k databázi SQL, je třeba zadat kopii ovladač JDBC používají k interakci s databází SQL. Chcete-li zkopírujte ovladač JDBC pracovní adresář, použijte následující příkaz z relace SSH:

```bash
hdfs dfs -put /usr/share/java/sqljdbc_4.1/enu/sqljdbc*.jar /tutorials/useoozie/
```

> [!NOTE]
> Může se zobrazit zpráva, že soubor již existuje.

Pokud pracovní postup používá jiné prostředky, jako je například jar, který obsahuje aplikaci MapReduce, je nutné přidat také tyto prostředky.

## <a name="define-the-hive-query"></a>Zadejte dotaz Hive

Pomocí následujících kroků můžete vytvořit skript jazyka (HiveQL) dotaz Hive, který definuje dotazu. V pracovním postupu Oozie později v tomto dokumentu použijete dotaz.

1. Z připojení SSH, použijte následující příkaz k vytvoření souboru s názvem `useooziewf.hql`:

    ```bash
    nano useooziewf.hql
    ```

3. Až se otevře GNU nano editor, použijte následující dotaz jako obsah souboru:

    ```hiveql
    DROP TABLE ${hiveTableName};
    CREATE EXTERNAL TABLE ${hiveTableName}(deviceplatform string, count string) ROW FORMAT DELIMITED
    FIELDS TERMINATED BY '\t' STORED AS TEXTFILE LOCATION '${hiveDataFolder}';
    INSERT OVERWRITE TABLE ${hiveTableName} SELECT deviceplatform, COUNT(*) as count FROM hivesampletable GROUP BY deviceplatform;
    ```

    Existují dvě proměnné používané ve skriptu:

    * `${hiveTableName}`: Obsahuje název tabulky, který se má vytvořit.

    * `${hiveDataFolder}`: Obsahuje umístění pro uložení souborů dat pro tabulku.

    Soubor definice pracovního postupu, workflow.xml v tomto kurzu předává tyto hodnoty tento skript HiveQL za běhu.

4. Zavřete editor, vyberte Ctrl + X. Po zobrazení výzvy vyberte `Y` k uložení souboru, zadejte `useooziewf.hql` jako název souboru a potom vyberte **Enter**.

5. Použijte následující příkazy pro kopírování `useooziewf.hql` k `wasb:///tutorials/useoozie/useooziewf.hql`:

    ```bash
    hdfs dfs -put useooziewf.hql /tutorials/useoozie/useooziewf.hql
    ```

    Tento příkaz uloží `useooziewf.hql` souboru v HDFS kompatibilní úložiště pro cluster.

## <a name="define-the-workflow"></a>Definice pracovního postupu

Definice pracovního postupu Oozie jsou zapsány v Hadoop proces Definition Language (hPDL), což je proces definice jazyka XML. Pomocí následujících kroků můžete definovat pracovní postup:

1. Můžete vytvářet a upravovat nový soubor, použijte následující příkaz:

    ```bash
    nano workflow.xml
    ```

2. Až se otevře nano editor, zadejte následující kód XML jako obsah souboru:

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

    Existují dvě akce, které jsou definovány v pracovním postupu:

   * `RunHiveScript`: Tato akce je akci spuštění a běží `useooziewf.hql` skript podregistru.

   * `RunSqoopExport`: Tato akce provede export dat vytvořené z skriptu Hive na databázi SQL s použitím Sqoop. Tato akce je spuštěna pouze pokud `RunHiveScript` akce je úspěšné.

     Pracovní postup má několik položek, jako například `${jobTracker}`. Tyto položky budou nahraďte hodnoty, které můžete použít v definici úlohy. Později v tomto dokumentu se vytvoří definici úlohy.

     Všimněte si také `<archive>sqljdbc4.jar</archive>` položky v části Sqoop. Tato položka dá pokyn Oozie archivu k dispozici na pro Sqoop spuštění této akce.

3. Chcete-li uložit soubor, vyberte Ctrl + X, zadejte `Y`a potom vyberte **Enter**. 

4. Použijte následující příkaz pro kopírování `workflow.xml` do souboru `/tutorials/useoozie/workflow.xml`:

    ```bash
    hdfs dfs -put workflow.xml /tutorials/useoozie/workflow.xml
    ```

## <a name="create-the-database"></a>Vytvoření databáze

Pokud chcete vytvořit databázi SQL, postupujte podle kroků v [vytvoření databáze SQL](../sql-database/sql-database-get-started.md) dokumentu. Když vytvoříte databázi, použijte `oozietest` jako název databáze. Také si poznamenejte název databázového serveru.

### <a name="create-the-table"></a>Vytvoření tabulky

> [!NOTE]
> Pro připojení k databázi SQL a vytvořte tabulku mnoha způsoby. Následující postup použijte [FreeTDS](http://www.freetds.org/) z clusteru HDInsight.


1. Použijte následující příkaz k instalaci FreeTDS v clusteru HDInsight:

    ```bash
    sudo apt-get --assume-yes install freetds-dev freetds-bin
    ```

2. Po instalaci FreeTDS, použijte následující příkaz pro připojení k databázovému serveru SQL, které jste vytvořili dříve:

    ```bash
    TDSVER=8.0 tsql -H <serverName>.database.windows.net -U <sqlLogin> -P <sqlPassword> -p 1433 -D oozietest
    ```

    Zobrazí se výstup jako následující text:

        locale is "en_US.UTF-8"
        locale charset is "UTF-8"
        using default charset "UTF-8"
        Default database being set to oozietest
        1>

3. Na `1>` výzva, zadejte následující řádky:

    ```sql
    CREATE TABLE [dbo].[mobiledata](
    [deviceplatform] [nvarchar](50),
    [count] [bigint])
    GO
    CREATE CLUSTERED INDEX mobiledata_clustered_index on mobiledata(deviceplatform)
    GO
    ```

    Když `GO` příkaz je zadán, jsou vyhodnocovány předchozí příkazy. Tyto příkazy vytvořit tabulku, s názvem **mobiledata**, který se používá v tomto pracovním postupu.

    Pokud chcete ověřit, zda byl vytvořen v tabulce, použijte následující příkazy:

    ```sql
    SELECT * FROM information_schema.tables
    GO
    ```

    Zobrazí výstup jako následující text:

        TABLE_CATALOG   TABLE_SCHEMA    TABLE_NAME      TABLE_TYPE
        oozietest       dbo     mobiledata      BASE TABLE

4. Ukončete nástroj tsql, zadejte `exit` na `1>` řádku.

## <a name="create-the-job-definition"></a>Vytvořit definici úlohy

Definice úlohy popisuje, kde najít workflow.xml. Také popisuje, kde najít další soubory, které používá pracovní postup, jako například `useooziewf.hql`. Kromě toho definuje hodnoty pro vlastnosti používané v rámci pracovního postupu a přidružené soubory.

1. K získání úplné adresy výchozí úložiště, použijte následující příkaz. Tato adresa se používá v konfiguračním souboru, který vytvoříte v dalším kroku.

    ```bash
    sed -n '/<name>fs.default/,/<\/value>/p' /etc/hadoop/conf/core-site.xml
    ```

    Tento příkaz vrátí informace, jako jsou následující kód XML:

    ```xml
    <name>fs.defaultFS</name>
    <value>wasb://mycontainer@mystorageaccount.blob.core.windows.net</value>
    ```

    > [!NOTE]
    > Pokud HDInsight cluster používá úložiště Azure jako výchozí úložiště, `<value>` obsah elementu začínat `wasb://`. Pokud je použita Azure Data Lake Store, začne s `adl://`.

    Uložení obsahu `<value>` element, protože se používá v dalších krocích.

2. K vytvoření konfigurace definice úlohy Oozie, použijte následující příkaz:

    ```bash
    nano job.xml
    ```

3. Až se otevře nano editor, použijte následující kód XML jako obsah souboru:

    ```xml
    <?xml version="1.0" encoding="UTF-8"?>
    <configuration>

        <property>
        <name>nameNode</name>
        <value>wasb://mycontainer@mystorageaccount.blob.core.windows.net</value>
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
        <value>wasb://mycontainer@mystorageaccount.blob.core.windows.net/tutorials/useoozie/useooziewf.hql</value>
        </property>

        <property>
        <name>hiveTableName</name>
        <value>mobilecount</value>
        </property>

        <property>
        <name>hiveDataFolder</name>
        <value>wasb://mycontainer@mystorageaccount.blob.core.windows.net/tutorials/useoozie/data</value>
        </property>

        <property>
        <name>sqlDatabaseConnectionString</name>
        <value>"jdbc:sqlserver://serverName.database.windows.net;user=adminLogin;password=adminPassword;database=oozietest"</value>
        </property>

        <property>
        <name>sqlDatabaseTableName</name>
        <value>mobiledata</value>
        </property>

        <property>
        <name>user.name</name>
        <value>YourName</value>
        </property>

        <property>
        <name>oozie.wf.application.path</name>
        <value>wasb://mycontainer@mystorageaccount.blob.core.windows.net/tutorials/useoozie</value>
        </property>
    </configuration>
    ```

   * Nahraďte všechny výskyty `wasb://mycontainer@mystorageaccount.blob.core.windows.net` s hodnotou jste obdrželi dříve pro výchozí úložiště.

     > [!WARNING]
     > Pokud se cesta `wasb` cestu, musíte použít úplnou cestu. Není jeho právě Zkraťte `wasb:///`.

   * Nahraďte `YourName` s vaše přihlašovací jméno pro HDInsight cluster.
   * Nahraďte `serverName`, `adminLogin`, a `adminPassword` s informacemi pro vaši databázi SQL.

     Většinu informací v tomto souboru se používá k naplnění hodnoty používané v workflow.xml nebo ooziewf.hql souborech, jako například `${nameNode}`.

     > [!NOTE]
     > `oozie.wf.application.path` Položka udává kde najít soubor workflow.xml. Tento soubor obsahuje pracovní postup, který byl spustit tuto úlohu.

5. Chcete-li uložit soubor, vyberte Ctrl + X, zadejte `Y`a potom vyberte **Enter**.

## <a name="submit-and-manage-the-job"></a>Odesílat a spravovat úlohy

Následující postup použijte příkaz Oozie k odeslání a správa pracovních postupů Oozie v clusteru. Příkaz Oozie je popisný rozhraní přes [Oozie REST API](https://oozie.apache.org/docs/4.1.0/WebServicesAPI.html).

> [!IMPORTANT]
> Pokud použijete příkaz Oozie, je nutné použít plně kvalifikovaný název domény pro hlavní uzel HDInsight. Tento plně kvalifikovaný název domény je k dispozici pouze z clusteru, nebo pokud je clusteru na virtuální síť Azure, z jiných počítačů ve stejné síti.


1. Pokud chcete získat adresu URL pro službu Oozie, použijte následující příkaz:

    ```bash
    sed -n '/<name>oozie.base.url/,/<\/value>/p' /etc/oozie/conf/oozie-site.xml
    ```

    Tento příkaz vrátí informace, jako jsou následující kód XML:

    ```xml
    <name>oozie.base.url</name>
    <value>http://hn0-CLUSTERNAME.randomcharacters.cx.internal.cloudapp.net:11000/oozie</value>
    ```

    `http://hn0-CLUSTERNAME.randomcharacters.cx.internal.cloudapp.net:11000/oozie` Část je adresa URL pro použití s příkazem Oozie.

2. K vytvoření proměnné prostředí pro adresu URL, použijte následující příkaz, takže nemáte k jeho zadání pro každý příkaz:

    ```bash
    export OOZIE_URL=http://HOSTNAMEt:11000/oozie
    ```

    Nahraďte adresu URL, které jste dostali dříve.
3. Postup odeslání úlohy, použijte tento příkaz:

    ```bash
    oozie job -config job.xml -submit
    ```

    Tento příkaz načte informace o úloze z `job.xml` a odešle ji Oozie, ale nejde spustit.

    Po dokončení příkazu, měla by vrátit ID úlohy, například `0000005-150622124850154-oozie-oozi-W`. Toto ID se používá ke správě úlohy.

4. Chcete-li zobrazit stav úlohy, použijte následující příkaz:

    ```bash
    oozie job -info <JOBID>
    ```

    > [!NOTE]
    > Nahraďte `<JOBID>` s ID, vrátí se v předchozím kroku.

    Tento příkaz vrátí informace, jako jsou následující text:

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

    Tato úloha je ve stavu `PREP`. Tento stav indikuje, že byla úloha vytvořen, ale není spuštěna.

5. Chcete-li spustit úlohu, použijte následující příkaz:

    ```bash
    oozie job -start JOBID
    ```

    > [!NOTE]
    > Nahraďte `<JOBID>` s ID vrátil dříve.

    Pokud po tento příkaz Zkontrolovat stav, je v běžícím stavu a se vrátí informace pro akce v rámci úlohy.

6. Po úspěšném dokončení této úlohy můžete ověřit, že byla data generované a exportovat do tabulky databáze SQL pomocí následujícího příkazu:

    ```bash
    TDSVER=8.0 tsql -H <serverName>.database.windows.net -U <adminLogin> -P <adminPassword> -p 1433 -D oozietest
    ```

    Na `1>` výzva, zadejte následující dotaz:

    ```sql
    SELECT * FROM mobiledata
    GO
    ```

    Vrácené informace je třeba následující text:

        deviceplatform  count
        Android 31591
        iPhone OS       22731
        proprietary development 3
        RIM OS  3464
        Unknown 213
        Windows Phone   1791
        (6 rows affected)

Další informace o příkazu Oozie najdete v tématu [nástroj příkazového řádku Oozie](https://oozie.apache.org/docs/4.1.0/DG_CommandLineTool.html).

## <a name="oozie-rest-api"></a>Oozie REST API

Pomocí rozhraní REST API Oozie můžete vytvořit vlastní nástroje, které pracují s Oozie. Toto je HDInsight konkrétní informace o použití rozhraní REST API Oozie:

* **Identifikátor URI**: můžete přístup k rozhraní REST API z mimo cluster v `https://CLUSTERNAME.azurehdinsight.net/oozie`.

* **Ověřování**: Chcete-li ověřit, použít rozhraní API účet clusteru HTTP (správce) a heslo. Příklad:

    ```bash
    curl -u admin:PASSWORD https://CLUSTERNAME.azurehdinsight.net/oozie/versions
    ```

Další informace o tom, jak použít rozhraní API REST Oozie najdete v tématu [Oozie Web Services API](https://oozie.apache.org/docs/4.1.0/WebServicesAPI.html).

## <a name="oozie-web-ui"></a>Oozie webového uživatelského rozhraní

Oozie webového uživatelského rozhraní poskytuje webové pohled na stav Oozie úloh v clusteru. Webové uživatelské rozhraní se může zobrazit následující informace:

   * Stav úlohy
   * Definice úlohy
   * Konfigurace
   * Graf akce pro úlohu
   * Protokoly pro úlohu

Můžete také zobrazit podrobnosti pro akce v rámci úlohy.

Pro přístup k Oozie webového uživatelského rozhraní, proveďte následující kroky:

1. Vytvoření tunelu SSH do clusteru HDInsight. Další informace najdete v tématu [používání tunelového propojení SSH s HDInsight](hdinsight-linux-ambari-ssh-tunnel.md).

2. Po vytvoření tunelu, otevřete webovému uživatelskému rozhraní Ambari ve webovém prohlížeči. Je identifikátor URI webu Ambari `https://CLUSTERNAME.azurehdinsight.net`. Nahraďte `CLUSTERNAME` s názvem vašeho clusteru HDInsight se systémem Linux.

3. Na levé straně stránky vyberte **Oozie** > **rychlé odkazy** > **Oozie webového uživatelského rozhraní**.

    ![obrázek v nabídkách](./media/hdinsight-use-oozie-linux-mac/ooziewebuisteps.png)

4. Chcete-li zobrazit spuštěné úlohy pracovního postupu výchozí nastavení Oozie webového uživatelského rozhraní. Pokud chcete zobrazit všechny úlohy pracovního postupu, vyberte **všechny úlohy**.

    ![Zobrazí všechny úlohy](./media/hdinsight-use-oozie-linux-mac/ooziejobs.png)

5. Chcete-li zobrazit další informace o úlohy, vyberte úlohu.

    ![Informace o úloze](./media/hdinsight-use-oozie-linux-mac/jobinfo.png)

6. Z **informace o úloze** kartě, můžete zobrazit informace o základní úlohy a jednotlivé akce v rámci úlohy. Karty v horní části můžete použít k zobrazení **definice úlohy**, **úlohy konfigurace**, přístup **protokol úlohy**, nebo můžete zobrazit necyklicky (DAG) úlohy v části **Úlohy DAG**.

   * **Protokol úlohy**: vyberte **získat protokoly** tlačítko získat všechny protokoly pro úlohu, nebo použijte **zadejte vyhledávací filtr** pole, které chcete filtrovat protokoly.

       ![Protokol úlohy](./media/hdinsight-use-oozie-linux-mac/joblog.png)

   * **Úloha DAG**: DAG je grafické přehled cest k datům prováděné v pracovním postupu.

       ![Úloha DAG](./media/hdinsight-use-oozie-linux-mac/jobdag.png)

7. Pokud vyberete jednu z akcí z **informace o úloze** kartě přináší si informace o akci. Vyberte například **RunSqoopExport** akce.

    ![Informace o akci](./media/hdinsight-use-oozie-linux-mac/action.png)

8. Zobrazí podrobnosti pro akce, například odkaz **adresa URL konzoly**. Použijte tento odkaz k zobrazení informací o sledování úloh pro úlohu.

## <a name="schedule-jobs"></a>Plánování úloh

Koordinátor můžete použít k určení spuštění, element end a četnost výskytu pro úlohy. K definování plánu pro pracovní postup, proveďte následující kroky:

1. Pomocí následujícího příkazu vytvořte soubor s názvem **coordinator.xml**:

    ```bash
    nano coordinator.xml
    ```

    Použijte následující kód XML jako obsah souboru:

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
    > `${...}` Proměnné jsou nahrazena hodnotami v definici úlohy za běhu. Proměnné jsou:
    >
    > * `${coordFrequency}`: Doba mezi spuštěné instance úlohy.
    > * `${coordStart}`: Úloha Počáteční čas.
    > * `${coordEnd}`: Času ukončení úlohy.
    > * `${coordTimezone}`: Koordinátor úlohy jsou v pevné časové pásmo s žádné letní čas, obvykle reprezentována pomocí standardu UTC. Toto časové pásmo se označuje jako *Oozie zpracování časové pásmo.*
    > * `${wfPath}`: Cesta k workflow.xml.

2. Chcete-li uložit soubor, vyberte Ctrl + X, zadejte `Y`a potom vyberte **Enter**.

3. Chcete-li kopírovat soubor do pracovní adresář pro tuto úlohu, použijte následující příkaz:

    ```bash
    hadoop fs -put coordinator.xml /tutorials/useoozie/coordinator.xml
    ```

4. Chcete-li upravit `job.xml` souboru, použijte následující příkaz:

    ```
    nano job.xml
    ```

    Proveďte následující změny:

   * Dáte pokyn, aby Oozie coordinator soubor namísto pracovního postupu spustíte, změnit `<name>oozie.wf.application.path</name>` k `<name>oozie.coord.application.path</name>`.

   * Chcete-li nastavit `workflowPath` proměnné používané koordinátorem, přidejte následující kód XML:

        ```xml
        <property>
            <name>workflowPath</name>
            <value>wasb://mycontainer@mystorageaccount.blob.core.windows.net/tutorials/useoozie</value>
        </property>
        ```

       Nahraďte `wasb://mycontainer@mystorageaccount.blob.core.windows` text hodnota použitá v jiných položek v souboru job.xml.

   * Chcete-li definovat začátek, konec a četnost pro koordinátorem, přidejte následující kód XML:

        ```xml
        <property>
            <name>coordStart</name>
            <value>2017-05-10T12:00Z</value>
        </property>

        <property>
            <name>coordEnd</name>
            <value>2017-05-12T12:00Z</value>
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

       Tyto hodnoty nastaveny, počáteční čas na 12:00 PM na 10 může 2017 a koncový čas na 12 může 2017. Interval pro spuštění této úlohy je nastavený na hodnotu denně. Frekvence se v minutách, takže 24 hodin x 60 minut = 1 440 minut. Nakonec časové pásmo je nastavena na UTC.

5. Chcete-li uložit soubor, vyberte Ctrl + X, zadejte `Y`a potom vyberte **Enter**.

6. Pokud chcete spustit úlohu, použijte následující příkaz:

    ```
    oozie job -config job.xml -run
    ```

    Tento příkaz odešle a spustí úlohu.

7. Pokud přejdete do Oozie webové uživatelské rozhraní a vyberte **koordinátor úlohy** kartě uvidíte informace jako na následujícím obrázku:

    ![Karta úlohy Coordinator](./media/hdinsight-use-oozie-linux-mac/coordinatorjob.png)

    **Další Materialization** položka obsahuje další dobu, která má být úloha spuštěna.

8. Jako starší úlohy pracovního postupu Pokud vyberete položku úlohy v webového uživatelského rozhraní zobrazuje informace v úloze:

    ![Informace o úloze Coordinator](./media/hdinsight-use-oozie-linux-mac/coordinatorjobinfo.png)

    > [!NOTE]
    > Tento image se zobrazí pouze úspěšně spustí úlohy, není jednotlivé akce v rámci naplánované pracovní postup. Chcete-li zobrazit jednotlivé akce, vyberte jednu z **akce** položky.

    ![Informace o akci](./media/hdinsight-use-oozie-linux-mac/coordinatoractionjob.png)

## <a name="troubleshooting"></a>Řešení potíží

Oozie protokoly můžete zobrazit pomocí rozhraní Oozie. Rozhraní Oozie obsahuje taky odkazy na protokoly JobTracker pro úlohy MapReduce, které byly spuštěny v tomto pracovním postupu. Vzor pro řešení potíží s by měla být:

   1. Zobrazte úlohy v Oozie webového uživatelského rozhraní.

   2. Pokud dojde k chybě nebo pro určité akce se nezdařilo, vyberte akci, která zjistí, zda **chybová zpráva** pole poskytuje další informace o selhání.

   3. Pokud je k dispozici, použijte adresu URL akce zobrazíte další podrobnosti, jako je například protokol JobTracker pro akci.

Dále jsou uvedeny konkrétní chyby, ke kterým může dojít a způsob jejich řešení.

### <a name="ja009-cannot-initialize-cluster"></a>JA009: Nelze inicializovat clusteru

**Příznaky**: stav úlohy změní na **POZASTAVENO**. Podrobnosti o úloze zobrazit `RunHiveScript` stav jako **START_MANUAL**. Výběr akce, zobrazí se následující chybová zpráva:

    JA009: Cannot initialize Cluster. Please check your configuration for map

**Příčina**: Azure Blob storage adresy použité v **job.xml** soubor neobsahuje kontejner úložiště nebo název účtu úložiště. Musí být ve formátu adresa úložiště objektů Blob `wasb://containername@storageaccountname.blob.core.windows.net`.

**Řešení**: Změna adresy úložiště objektů Blob, které používá úlohy.

### <a name="ja002-oozie-is-not-allowed-to-impersonate-ltuser"></a>JA002: Oozie není povoleno zosobnění &lt;uživatele >

**Příznaky**: stav úlohy změní na **POZASTAVENO**. Podrobnosti o úloze zobrazit `RunHiveScript` stav jako **START_MANUAL**. Pokud vyberete akci, zobrazí se následující chybová zpráva:

    JA002: User: oozie is not allowed to impersonate <USER>

**Příčina**: nastavení oprávnění Nepovolit Oozie zosobnit zadaný uživatelský účet.

**Řešení**: Oozie mít povoleno zosobnit uživatele v **uživatelé** skupiny. Použití `groups USERNAME` zobrazení skupin, které uživatelský účet je členem skupiny. Pokud uživatel není členem **uživatelé** skupiny, použijte následující příkaz a přidejte uživatele do skupiny:

    sudo adduser USERNAME users

> [!NOTE]
> To může trvat několik minut, než HDInsight rozpozná, že uživatel byl přidán do skupiny.

### <a name="launcher-error-sqoop"></a>Spouštěč chyby (Sqoop)

**Příznaky**: stav úlohy změní na **KILLED**. Podrobnosti o úloze zobrazit `RunSqoopExport` stav jako **chyba**. Pokud vyberete akci, zobrazí se následující chybová zpráva:

    Launcher ERROR, reason: Main class [org.apache.oozie.action.hadoop.SqoopMain], exit code [1]

**Příčina**: Sqoop se nepodařilo načíst ovladač databáze, které jsou nutné pro přístup k databázi.

**Řešení**: při použití Sqoop z úlohu Oozie, je nutné zahrnout ovladač databáze s další prostředky, jako je například workflow.xml, používá úlohy. Navíc odkazovat obsahující ovladač databáze z archivu `<sqoop>...</sqoop>` části workflow.xml.

Například by pro úlohu v tomto dokumentu, použijte následující kroky:

1. Kopírování `sqljdbc4.1.jar` do souboru **/kurzy/useoozie** directory:

    ```bash
    hdfs dfs -put /usr/share/java/sqljdbc_4.1/enu/sqljdbc41.jar /tutorials/useoozie/sqljdbc41.jar
    ```

2. Změnit `workflow.xml` a přidejte následující kód XML na nový řádek výše `</sqoop>`:

    ```xml
    <archive>sqljdbc41.jar</archive>
    ```

## <a name="next-steps"></a>Další postup

V tomto kurzu jste se dozvěděli, jak definovat pracovním postupu Oozie a jak spustit úlohu Oozie. Další informace o tom, jak pracovat s HDInsight, naleznete v následujících článcích:

* [Použijte Oozie Coordinator založené na čase s HDInsight][hdinsight-oozie-coordinator-time]
* [Nahrání dat pro úlohy Hadoop v HDInsight][hdinsight-upload-data]
* [Použití nástroje Sqoop se systémem Hadoop v HDInsight][hdinsight-use-sqoop]
* [Použijte Hive s Hadoop v HDInsight][hdinsight-use-hive]
* [Použijte Pig s Hadoop v HDInsight][hdinsight-use-pig]
* [Vývoj aplikací Java MapReduce pro HDInsight][hdinsight-develop-mapreduce]

[hdinsight-cmdlets-download]: http://go.microsoft.com/fwlink/?LinkID=325563
[azure-data-factory-pig-hive]: ../data-factory/transform-data.md
[hdinsight-oozie-coordinator-time]: hdinsight-use-oozie-coordinator-time.md
[hdinsight-versions]:  hdinsight-component-versioning.md
[hdinsight-storage]: hdinsight-use-blob-storage.md
[hdinsight-get-started]: hdinsight-get-started.md
[hdinsight-use-sqoop]:hadoop/apache-hadoop-use-sqoop-mac-linux.md
[hdinsight-provision]: hdinsight-hadoop-provision-linux-clusters.md
[hdinsight-upload-data]: hdinsight-upload-data.md
[hdinsight-use-mapreduce]:hadoop/hdinsight-use-mapreduce.md
[hdinsight-use-hive]:hadoop/hdinsight-use-hive.md
[hdinsight-use-pig]:hadoop/hdinsight-use-pig.md
[hdinsight-storage]: hdinsight-use-blob-storage.md
[hdinsight-get-started-emulator]: hdinsight-get-started-emulator.md
[hdinsight-develop-mapreduce]:hadoop/apache-hadoop-develop-deploy-java-mapreduce-linux.md

[sqldatabase-create-configue]: sql-database-create-configure.md
[sqldatabase-get-started]: sql-database-get-started.md

[azure-create-storageaccount]:../storage/common/storage-create-storage-account.md

[apache-hadoop]: http://hadoop.apache.org/
[apache-oozie-400]: http://oozie.apache.org/docs/4.0.0/
[apache-oozie-332]: http://oozie.apache.org/docs/3.3.2/

[powershell-download]: http://azure.microsoft.com/downloads/
[powershell-about-profiles]: http://go.microsoft.com/fwlink/?LinkID=113729
[powershell-install-configure]: /powershell/azureps-cmdlets-docs
[powershell-start]: http://technet.microsoft.com/library/hh847889.aspx
[powershell-script]: https://technet.microsoft.com/library/ee176961.aspx

[cindygross-hive-tables]: http://blogs.msdn.com/b/cindygross/archive/2013/02/06/hdinsight-hive-internal-and-external-tables-intro.aspx

[img-workflow-diagram]: ./media/hdinsight-use-oozie/HDI.UseOozie.Workflow.Diagram.png
[img-preparation-output]: ./media/hdinsight-use-oozie/HDI.UseOozie.Preparation.Output1.png
[img-runworkflow-output]: ./media/hdinsight-use-oozie/HDI.UseOozie.RunWF.Output.png

[technetwiki-hive-error]: http://social.technet.microsoft.com/wiki/contents/articles/23047.hdinsight-hive-error-unable-to-rename.aspx
