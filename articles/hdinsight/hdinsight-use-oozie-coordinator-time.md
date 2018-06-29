---
title: Použít časové coordinator Hadoop Oozie v HDInsight | Microsoft Docs
description: V prostředí HDInsight, Cloudová služba velkých dat pomocí Hadoop Oozie coordinator založené na čase. Zjistěte, jak definovat pracovní postupy Oozie a koordinátoři a odesílání úloh.
services: hdinsight
documentationcenter: ''
tags: azure-portal
author: mumian
manager: jhubbard
editor: cgronlun
ms.assetid: 00c3a395-d51a-44ff-af2d-1f116c4b1c83
ms.service: hdinsight
ms.custom: hdinsightactive
ms.devlang: na
ms.topic: conceptual
ms.date: 10/04/2017
ms.author: jgao
ROBOTS: NOINDEX
ms.openlocfilehash: c5819d39bf3ab7c0f4af32171aadea56e4f6a241
ms.sourcegitcommit: f06925d15cfe1b3872c22497577ea745ca9a4881
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/27/2018
ms.locfileid: "37063523"
---
# <a name="use-time-based-oozie-coordinator-with-hadoop-in-hdinsight-to-define-workflows-and-coordinate-jobs"></a>Pomocí nástroje Oozie coordinator založené na čase s Hadoop v HDInsight můžete definovat pracovní postupy a koordinovat úlohy
V tomto článku se dozvíte, jak definovat pracovní postupy a koordinátoři a spouštění koordinátor úlohy, na základě času. Je užitečné projít [Oozie použití s HDInsight] [ hdinsight-use-oozie] před přečtěte si tento článek. Kromě Oozie můžete také naplánovat úlohy pomocí Azure Data Factory. Další služby Azure Data Factory najdete v tématu [použijte Pig a Hive pomocí služby Data Factory](../data-factory/transform-data.md).

> [!NOTE]
> Tento článek vyžaduje cluster HDInsight se systémem Windows. Informace o používání Oozie, včetně úloh založené na čase, v clusteru se systémem Linux naleznete v části [Oozie použití se systémem Hadoop k definování a spuštění workflowu v HDInsight se systémem Linux](hdinsight-use-oozie-linux-mac.md)

## <a name="what-is-oozie"></a>Co je Oozie
Apache Oozie je pracovní postup nebo koordinaci systém, který spravuje úloh Hadoop. Je integrován do zásobníku Hadoop, a podporuje úloh Hadoop pro Apache MapReduce, Apache Pig, Apache Hive a Apache Sqoop. Můžete se také používají k plánování úloh, které jsou specifické pro systém, jako jsou programy v jazyce Java nebo skripty prostředí.

Následující obrázek ukazuje pracovní postup, který implementujete:

![Diagram pracovního postupu][img-workflow-diagram]

Pracovní postup obsahuje dvě akce:

1. Akce Hive spouští skript HiveQL k určení počtu výskytů každý typ úroveň protokolu v souboru protokolu log4j. Každý log4j protokolový soubor obsahuje řádku pole, která obsahuje pole [úroveň protokolu], které chcete zobrazit typ a závažnost, například:

        2012-02-03 18:35:34 SampleClass6 [INFO] everything normal for id 577725851
        2012-02-03 18:35:34 SampleClass4 [FATAL] system problem at id 1991281254
        2012-02-03 18:35:34 SampleClass3 [DEBUG] detail for id 1304807656
        ...

    Výstup skriptu Hive je podobná:

        [DEBUG] 434
        [ERROR] 3
        [FATAL] 1
        [INFO]  96
        [TRACE] 816
        [WARN]  4

    Další informace o Hivu najdete v tématu [Použití Hivu se službou HDInsight][hdinsight-use-hive].
2. Akce Sqoop Exportuje výstup akce HiveQL do tabulky v databázi Azure SQL. Další informace o Sqoop najdete v tématu [Sqoop použití s HDInsight][hdinsight-use-sqoop].

> [!NOTE]
> Podporované verze Oozie v clusterech prostředí HDInsight najdete v tématu [co je nového ve verzích clusterů poskytovaných v HDInsight?] [hdinsight-versions].
>
>

## <a name="prerequisites"></a>Požadavky
Je nutné, abyste před zahájením tohoto kurzu měli tyto položky:

* **Pracovní stanice s prostředím Azure PowerShell**.

    > [!IMPORTANT]
    > Podpora prostředí Azure PowerShell pro správu prostředků služby HDInsight pomocí Azure Service Manageru je **zastaralá** a 1. ledna 2017 dojde k jejímu odebrání. Kroky v tomto dokumentu používají nové rutiny služby HDInsight, které pracují s Azure Resource Managerem.
    >
    > Podle postupu v tématu [Instalace a konfigurace prostředí Azure PowerShell](/powershell/azureps-cmdlets-docs) si nainstalujte nejnovější verzi prostředí Azure PowerShell. Pokud máte skripty, které je potřeba upravit tak, aby používaly nové rutiny, které pracují s nástrojem Azure Resource Manager, najdete další informace v tématu [Migrace na vývojové nástroje založené na Azure Resource Manageru pro clustery služby HDInsight](hdinsight-hadoop-development-using-azure-resource-manager.md).

* **Cluster služby HDInsight**. Informace o vytváření clusteru služby HDInsight najdete v tématu [Tvorba clusterů HDInsight][hdinsight-provision], nebo [Začínáme s HDInsight][hdinsight-get-started]. Následující data, která mají absolvovat kurz budete potřebovat:

    <table border = "1">
    <tr><th>Vlastnost clusteru</th><th>Název proměnné prostředí Windows PowerShell</th><th>Hodnota</th><th>Popis</th></tr>
    <tr><td>Název clusteru HDInsight</td><td>$clusterName</td><td></td><td>Cluster HDInsight, na kterém budete spouštět v tomto kurzu.</td></tr>
    <tr><td>Uživatelské jméno clusteru HDInsight</td><td>$clusterUsername</td><td></td><td>Uživatelské jméno clusteru HDInsight. </td></tr>
    <tr><td>Heslo uživatele clusteru HDInsight </td><td>$clusterPassword</td><td></td><td>Heslo uživatele clusteru HDInsight.</td></tr>
    <tr><td>Název účtu úložiště Azure</td><td>$storageAccountName</td><td></td><td>Účet služby Azure Storage k dispozici ke clusteru HDInsight. V tomto kurzu použijte výchozí účet úložiště, který jste zadali během procesu zřizování clusteru.</td></tr>
    <tr><td>Název kontejneru Azure Blob</td><td>$containerName</td><td></td><td>V tomto příkladu použijte kontejner úložiště objektů Blob v Azure, který se používá pro výchozí systém souborů clusteru HDInsight. Ve výchozím nastavení má stejný název jako HDInsight cluster.</td></tr>
    </table>

* **Azure SQL database**. Je nutné nakonfigurovat pravidlo brány firewall pro server databáze SQL pro povolení přístupu z pracovní stanice. Pokyny týkající se vytváření databáze Azure SQL a konfiguraci brány firewall najdete v tématu [začít používat Azure SQL database][sqldatabase-get-started]. Tento článek obsahuje skript prostředí Windows PowerShell pro vytvoření tabulky databáze Azure SQL, které potřebujete pro účely tohoto kurzu.

    <table border = "1">
    <tr><th>Vlastnost databáze SQL</th><th>Název proměnné prostředí Windows PowerShell</th><th>Hodnota</th><th>Popis</th></tr>
    <tr><td>Název databáze serveru SQL</td><td>$sqlDatabaseServer</td><td></td><td>Databáze SQL server, ke kterému bude Sqoop exportovat data. </td></tr>
    <tr><td>Přihlašovací jméno SQL databáze</td><td>$sqlDatabaseLogin</td><td></td><td>Přihlašovací jméno SQL Database.</td></tr>
    <tr><td>Heslo pro přihlášení databáze SQL</td><td>$sqlDatabaseLoginPassword</td><td></td><td>Heslo přihlášení k databázi SQL.</td></tr>
    <tr><td>Název databáze SQL</td><td>$sqlDatabaseName</td><td></td><td>Azure SQL database, ke kterému bude Sqoop exportovat data. </td></tr>
    </table>

  > [!NOTE]
  > Ve výchozím nastavení Azure SQL database umožňuje připojení z Azure služby, jako je Azure HDInsight. Pokud toto nastavení brány firewall je zakázáno, musíte ji povolit z portálu Azure. Pokyny týkající se vytváření databáze SQL a konfigurace pravidel brány firewall, najdete v části [vytvořit a nakonfigurovat databázi SQL][sqldatabase-get-started].

> [!NOTE]
> Vyplňování hodnoty v tabulkách. Je užitečné při procházení tohoto kurzu.

## <a name="define-oozie-workflow-and-the-related-hiveql-script"></a>Definovat Oozie pracovního postupu a související skript HiveQL
Definice Oozie pracovní postupy jsou zapsány ve hPDL (jazyka definice proces XML). Výchozí název souboru pracovního postupu je *workflow.xml*.  Budete uložte místně soubor pracovního postupu a nasadíte ho do clusteru HDInsight pomocí prostředí Azure PowerShell později v tomto kurzu.

Akce Hive v pracovním postupu volá soubor skriptu HiveQL. Tento soubor skriptu obsahuje tři příkazy HiveQL:

1. **Příkaz DROP TABLE** odstraní tabulku Hive log4j, pokud existuje.
2. **Příkaz CREATE TABLE** vytvoří log4j externí tabulku Hive, který odkazuje na umístění souboru protokolu log4j;
3. **Umístění souboru protokolu log4j**. Oddělovač polí je ",". Oddělovač řádku výchozí je "\n". Externí tabulku Hive se používá předejdete datového souboru odebírán z původního umístění, v případě, že chcete spustit Oozie workflow vícekrát.
4. **Příkaz INSERT PŘEPSAT** počtu výskytů každý typ úroveň protokolu z tabulky Hive log4j a uloží výstup do umístění úložiště objektů Blob v Azure.

> [!NOTE]
> Je známý problém cesta Hive. Budete spouštět na tento problém při odesílání úlohu Oozie. Pokyny k nápravě problému naleznete na webu TechNet Wiki: [HDInsight Hive Chyba: nelze přejmenovat][technetwiki-hive-error].

**Zadat soubor skriptu HiveQL k volání v tomto pracovním postupu**

1. Vytvořte textový soubor s následujícím obsahem:

        DROP TABLE ${hiveTableName};
        CREATE EXTERNAL TABLE ${hiveTableName}(t1 string, t2 string, t3 string, t4 string, t5 string, t6 string, t7 string) ROW FORMAT DELIMITED FIELDS TERMINATED BY ' ' STORED AS TEXTFILE LOCATION '${hiveDataFolder}';
        INSERT OVERWRITE DIRECTORY '${hiveOutputFolder}' SELECT t4 AS sev, COUNT(*) AS cnt FROM ${hiveTableName} WHERE t4 LIKE '[%' GROUP BY t4;

    Existují tři proměnné používané ve skriptu:

   * ${hiveTableName}
   * ${hiveDataFolder}
   * ${hiveOutputFolder}

     Soubor definice pracovního postupu (workflow.xml v tomto kurzu) předá tyto hodnoty tento skript HiveQL v době běhu.
2. Uložte soubor jako **C:\Tutorials\UseOozie\useooziewf.hql** pomocí kódování ANSI (ASCII). (Použijte Poznámkový blok, pokud textového editoru neposkytuje tuto možnost.) Tento soubor skriptu nasadí do clusteru HDInsight později v tomto kurzu.

**Chcete-li definovat pracovní postup**

1. Vytvořte textový soubor s následujícím obsahem:

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
                <param>hiveOutputFolder=${hiveOutputFolder}</param>
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
            <arg>${hiveOutputFolder}</arg>
            <arg>-m</arg>
            <arg>1</arg>
            <arg>--input-fields-terminated-by</arg>
            <arg>"\001"</arg>
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

    Existují dvě akce, které jsou definovány v pracovním postupu. Tato akce spuštění *RunHiveScript*. Pokud se spustí akce *OK*, je další akce *RunSqoopExport*.

    RunHiveScript má několik proměnné. Při odesílání úlohy Oozie z pracovní stanice pomocí prostředí Azure PowerShell, projdou hodnoty.

    Proměnné pracovního postupu

    <table border = "1">
    <tr><th>Proměnné pracovního postupu</th><th>Popis</th></tr>
    <tr><td>${jobTracker}</td><td>Zadejte adresu URL ke sledovacímu modulu úlohy Hadoop. Použití <strong>jobtrackerhost:9010</strong> v HDInsight clusteru verze 3.0 a 2.0.</td></tr>
    <tr><td>${nameNode}</td><td>Zadejte adresu URL Hadoop název uzlu. Použít výchozí wasb systému souborů: / / adres, například <i>wasb: / /&lt;containerName&gt;@&lt;storageAccountName&gt;. blob.core.windows.net</i>.</td></tr>
    <tr><td>${queueName}</td><td>Určuje název fronty, který bude úloha odeslána. Použití <strong>výchozí</strong>.</td></tr>
    </table>

    Proměnné akcí v Hive

    <table border = "1">
    <tr><th>Hive proměnné akce</th><th>Popis</th></tr>
    <tr><td>${hiveDataFolder}</td><td>Zdrojový adresář pro příkaz Hive Create Table.</td></tr>
    <tr><td>${hiveOutputFolder}</td><td>Výstupní složky pro příkaz INSERT PŘEPSAT.</td></tr>
    <tr><td>${hiveTableName}</td><td>Název tabulky Hive, která odkazuje na log4j datových souborů.</td></tr>
    </table>

    Proměnné akcí v Sqoop

    <table border = "1">
    <tr><th>Sqoop proměnné akce</th><th>Popis</th></tr>
    <tr><td>${sqlDatabaseConnectionString}</td><td>Připojovací řetězec databáze SQL.</td></tr>
    <tr><td>${sqlDatabaseTableName}</td><td>Tabulka databáze Azure SQL do kterého se budou data exportovat.</td></tr>
    <tr><td>${hiveOutputFolder}</td><td>Výstupní složky pro příkaz Hive vložit PŘEPSAT. Toto je stejné složce, Sqoop export (export-dir).</td></tr>
    </table>

    Další informace o pracovním postupu Oozie a pomocí akce pracovního postupu najdete v tématu [dokumentaci Apache Oozie 4.0][apache-oozie-400] (u clusteru HDInsight verze 3.0) nebo [dokumentaci Apache Oozie 3.3.2][apache-oozie-332] (u clusteru HDInsight verze 2.1).

1. Uložte soubor jako **C:\Tutorials\UseOozie\workflow.xml** pomocí kódování ANSI (ASCII). (Použijte Poznámkový blok, pokud textového editoru neposkytuje tuto možnost.)

**Chcete-li definovat coordinator**

1. Vytvořte textový soubor s následujícím obsahem:

    ```xml
    <coordinator-app name="my_coord_app" frequency="${coordFrequency}" start="${coordStart}" end="${coordEnd}" timezone="${coordTimezone}" xmlns="uri:oozie:coordinator:0.4">
        <action>
            <workflow>
                <app-path>${wfPath}</app-path>
            </workflow>
        </action>
    </coordinator-app>
    ```

    Existují pět proměnné používané v definičním souboru:

   | Proměnná | Popis |
   | --- | --- |
   | ${coordFrequency} |Čas pozastavení úlohy. Frekvence je vždy vyjádřené v minutách. |
   | ${coordStart} |Čas spuštění úlohy. |
   | ${coordEnd} |Čas ukončení úlohy. |
   | ${coordTimezone} |Oozie zpracovává koordinátor úlohy v pevné časové pásmo s žádné letní čas (obvykle vyjádřený pomocí UTC). Toto časové pásmo se označuje jako "Oozie zpracování časové pásmo." |
   | ${wfPath} |Cesta pro workflow.xml.  Pokud název souboru pracovního postupu není výchozí název souboru (workflow.xml), je nutné zadat. |
2. Uložte soubor jako **C:\Tutorials\UseOozie\coordinator.xml** pomocí kódování ANSI (ASCII). (Použijte Poznámkový blok, pokud textového editoru neposkytuje tuto možnost.)

## <a name="deploy-the-oozie-project-and-prepare-the-tutorial"></a>Nasazení projektu Oozie a připravte kurz
Bude spustit skript prostředí Azure PowerShell k těmto činnostem:

* Zkopírujte skript HiveQL (useoozie.hql) do úložiště objektů Blob v Azure, wasb:///tutorials/useoozie/useoozie.hql.
* Zkopírujte workflow.xml wasb:///tutorials/useoozie/workflow.xml.
* Zkopírujte coordinator.xml wasb:///tutorials/useoozie/coordinator.xml.
* Zkopírujte datový soubor (nebo example/data/sample.log) k wasb:///tutorials/useoozie/data/sample.log.
* Vytvoření tabulky databáze Azure SQL pro ukládání dat export Sqoop. Název tabulky je *log4jLogCount*.

**Pochopení úložiště HDInsight**

HDInsight používá úložiště objektů Blob v Azure pro úložiště dat. wasb: / / je implementace systému souborů Hadoop distributed (HDFS) v Azure Blob storage společnosti Microsoft. Další informace najdete v tématu [použití Azure Blob storage s HDInsight][hdinsight-storage].

Při zřizování clusteru služby HDInsight, účet úložiště objektů Blob v Azure a konkrétní kontejner z daného účtu je určený jako výchozí systém souborů, jako v HDFS. Kromě tohoto účtu úložiště můžete přidat další účty úložiště ze stejného předplatného Azure nebo z různých předplatných Azure během procesu zřizování. Pokyny o přidání dalších účtů úložiště najdete v tématu [zřizování clusterů HDInsight][hdinsight-provision]. Pro zjednodušení skript prostředí PowerShell Azure používá v tomto kurzu, všechny soubory jsou uložené ve výchozím kontejneru systému soubor nacházející se v */kurzy/useoozie*. Ve výchozím nastavení tento kontejner má stejný název jako název clusteru HDInsight.
Syntaxe je:

    wasb[s]://<ContainerName>@<StorageAccountName>.blob.core.windows.net/<path>/<filename>

> [!NOTE]
> Pouze *wasb: / /* syntaxe je podporován v clusteru HDInsight verze 3.0. Starší *asv: / /* syntaxe je podporován v HDInsight 2.1 a 1.6 clustery, ale není podporována v clusterech HDInsight 3.0.
>
> Wasb: / / cesta je virtuální cesta. Další informace najdete v části [použití Azure Blob storage s HDInsight][hdinsight-storage].

Soubor, který je uložený ve výchozím kontejneru systému souborů je přístupný z HDInsight pomocí některé z následujících identifikátory URI (používám workflow.xml jako příklad):

    wasb://mycontainer@mystorageaccount.blob.core.windows.net/tutorials/useoozie/workflow.xml
    wasb:///tutorials/useoozie/workflow.xml
    /tutorials/useoozie/workflow.xml

Pokud chcete získat přístup k souboru přímo z účtu úložiště, je název objektu blob pro soubor:

    tutorials/useoozie/workflow.xml

**Pochopení interních a externích tabulek Hive**

Existuje několik věcí, které potřebujete vědět o vnitřních a vnějších tabulek Hive:

* Příkaz CREATE TABLE vytvoří interní tabulku, také známé jako spravované tabulku. Datový soubor se musí nacházet ve výchozím kontejneru.
* Příkaz CREATE TABLE přesune soubor dat /hive/skladu /<TableName> složky ve výchozím kontejneru.
* Příkaz CREATE TABLE externí vytvoří externí tabulku. Datový soubor může být umístěn mimo výchozí kontejner.
* Příkaz CREATE TABLE externí nepřesouvá datového souboru.
* Příkaz CREATE TABLE externí neumožňuje všechny podsložky složky, která je zadána v klauzuli umístění. To je důvod, proč tento kurz vytvoří kopii souboru sample.log.

Další informace najdete v tématu [HDInsight: Hive interní a externí tabulky ÚVOD][cindygross-hive-tables].

**Pro přípravu kurzu**

1. Otevřete Windows PowerShell ISE (na obrazovce Start systému Windows 8 zadejte **PowerShell_ISE**a potom klikněte na **Windows PowerShell ISE**. Další informace najdete v tématu [spuštění prostředí Windows PowerShell ve Windows 8 a Windows][powershell-start]).
2. V dolním podokně spusťte následující příkaz pro připojení k předplatnému Azure:

    ```powershell
    Add-AzureAccount
    ```

    Zobrazí se výzva k zadání přihlašovacích údajů účtu Azure. Tato metoda přidávání připojení předplatného vyprší, a po 12 hodinách, budete muset znovu spustit rutinu.

   > [!NOTE]
   > Pokud máte víc předplatných Azure a výchozí předplatné není ta, kterou chcete použít, použijte <strong>Select-AzureSubscription</strong> rutiny vyberte předplatné.

3. Zkopírujte následující skript do podokna Skript a potom nastavte prvních šesti proměnné:

    ```powershell
    # WASB variables
    $storageAccountName = "<StorageAccountName>"
    $containerName = "<BlobStorageContainerName>"

    # SQL database variables
    $sqlDatabaseServer = "<SQLDatabaseServerName>"
    $sqlDatabaseLogin = "<SQLDatabaseLoginName>"
    $sqlDatabaseLoginPassword = "SQLDatabaseLoginPassword>"
    $sqlDatabaseName = "<SQLDatabaseName>"
    $sqlDatabaseTableName = "log4jLogsCount"

    # Oozie files for the tutorial
    $hiveQLScript = "C:\Tutorials\UseOozie\useooziewf.hql"
    $workflowDefinition = "C:\Tutorials\UseOozie\workflow.xml"
    $coordDefinition =  "C:\Tutorials\UseOozie\coordinator.xml"

    # WASB folder for storing the Oozie tutorial files.
    $destFolder = "tutorials/useoozie"  # Do NOT use the long path here
    ```

    Další popis proměnných najdete v tématu [požadavky](#prerequisites) v tomto kurzu.

4. Připojte následující skript, v podokně skriptu:

    ```powershell
    # Create a storage context object
    $storageaccountkey = get-azurestoragekey $storageAccountName | %{$_.Primary}
    $destContext = New-AzureStorageContext -StorageAccountName $storageAccountName -StorageAccountKey $storageaccountkey

    function uploadOozieFiles()
    {
        Write-Host "Copy HiveQL script, workflow definition and coordinator definition ..." -ForegroundColor Green
        Set-AzureStorageBlobContent -File $hiveQLScript -Container $containerName -Blob "$destFolder/useooziewf.hql" -Context $destContext
        Set-AzureStorageBlobContent -File $workflowDefinition -Container $containerName -Blob "$destFolder/workflow.xml" -Context $destContext
        Set-AzureStorageBlobContent -File $coordDefinition -Container $containerName -Blob "$destFolder/coordinator.xml" -Context $destContext
    }

    function prepareHiveDataFile()
    {
        Write-Host "Make a copy of the sample.log file ... " -ForegroundColor Green
        Start-CopyAzureStorageBlob -SrcContainer $containerName -SrcBlob "example/data/sample.log" -Context $destContext -DestContainer $containerName -destBlob "$destFolder/data/sample.log" -DestContext $destContext
    }

    function prepareSQLDatabase()
    {
        # SQL query string for creating log4jLogsCount table
        $cmdCreateLog4jCountTable = " CREATE TABLE [dbo].[$sqlDatabaseTableName](
                [Level] [nvarchar](10) NOT NULL,
                [Total] float,
            CONSTRAINT [PK_$sqlDatabaseTableName] PRIMARY KEY CLUSTERED
            (
            [Level] ASC
            )
            )"

        #Create the log4jLogsCount table
        Write-Host "Create Log4jLogsCount table ..." -ForegroundColor Green
        $conn = New-Object System.Data.SqlClient.SqlConnection
        $conn.ConnectionString = "Data Source=$sqlDatabaseServer.database.windows.net;Initial Catalog=$sqlDatabaseName;User ID=$sqlDatabaseLogin;Password=$sqlDatabaseLoginPassword;Encrypt=true;Trusted_Connection=false;"
        $conn.open()
        $cmd = New-Object System.Data.SqlClient.SqlCommand
        $cmd.connection = $conn
        $cmd.commandtext = $cmdCreateLog4jCountTable
        $cmd.executenonquery()

        $conn.close()
    }

    # upload workflow.xml, coordinator.xml, and ooziewf.hql
    uploadOozieFiles;

    # make a copy of example/data/sample.log to example/data/log4j/sample.log
    prepareHiveDataFile;

    # create log4jlogsCount table on SQL database
    prepareSQLDatabase;
    ```

5. Klikněte na tlačítko **spustit skript** nebo stiskněte klávesu **F5** pro spuštění skriptu. Výstup bude vypadat podobně jako:

    ![Kurz přípravy výstup][img-preparation-output]

## <a name="run-the-oozie-project"></a>Spusťte projekt Oozie
Prostředí Azure PowerShell aktuálně neposkytuje žádné rutiny pro definování Oozie úloh. Můžete použít **Invoke-RestMethod** rutiny k vyvolání Oozie webové služby. Oozie webového rozhraní API služby je JSON rozhraní HTTP REST API. Další informace o rozhraní API Oozie webových služeb najdete v tématu [dokumentaci Apache Oozie 4.0][apache-oozie-400] (u clusteru HDInsight verze 3.0) nebo [dokumentaci Apache Oozie 3.3.2][apache-oozie-332] (u clusteru HDInsight verze 2.1).

**Odeslat úlohu Oozie**

1. Otevřete Windows PowerShell ISE (na obrazovce Start systému Windows 8, zadejte **PowerShell_ISE**a potom klikněte na **Windows PowerShell ISE**. Další informace najdete v tématu [spuštění prostředí Windows PowerShell ve Windows 8 a Windows][powershell-start]).
2. Zkopírujte následující skript do podokna Skript a potom nastavte nejprve čtrnáct proměnné (však přeskočit **$storageUri**).

    ```powershell
    #HDInsight cluster variables
    $clusterName = "<HDInsightClusterName>"
    $clusterUsername = "<HDInsightClusterUsername>"
    $clusterPassword = "<HDInsightClusterUserPassword>"

    #Azure Blob storage (WASB) variables
    $storageAccountName = "<StorageAccountName>"
    $storageContainerName = "<BlobContainerName>"
    $storageUri="wasb://$storageContainerName@$storageAccountName.blob.core.windows.net"

    #Azure SQL database variables
    $sqlDatabaseServer = "<SQLDatabaseServerName>"
    $sqlDatabaseLogin = "<SQLDatabaseLoginName>"
    $sqlDatabaseLoginPassword = "<SQLDatabaseloginPassword>"
    $sqlDatabaseName = "<SQLDatabaseName>"

    #Oozie WF/coordinator variables
    $coordStart = "2014-03-21T13:45Z"
    $coordEnd = "2014-03-21T13:45Z"
    $coordFrequency = "1440"    # in minutes, 24h x 60m = 1440m
    $coordTimezone = "UTC"    #UTC/GMT

    $oozieWFPath="$storageUri/tutorials/useoozie"  # The default name is workflow.xml. And you don't need to specify the file name.
    $waitTimeBetweenOozieJobStatusCheck=10

    #Hive action variables
    $hiveScript = "$storageUri/tutorials/useoozie/useooziewf.hql"
    $hiveTableName = "log4jlogs"
    $hiveDataFolder = "$storageUri/tutorials/useoozie/data"
    $hiveOutputFolder = "$storageUri/tutorials/useoozie/output"

    #Sqoop action variables
    $sqlDatabaseConnectionString = "Data Source=$sqlDatabaseServer.database.windows.net;user=$sqlDatabaseLogin@$sqlDatabaseServer;password=$sqlDatabaseLoginPassword;database=$sqlDatabaseName"  
    $sqlDatabaseTableName = "log4jLogsCount"

    $passwd = ConvertTo-SecureString $clusterPassword -AsPlainText -Force
    $creds = New-Object System.Management.Automation.PSCredential ($clusterUsername, $passwd)
    ```

    Další popis proměnných najdete v tématu [požadavky](#prerequisites) v tomto kurzu.

    $coordstart a $coordend jsou pracovního postupu počáteční a koncový čas. Chcete-li zjistit čas UTC nebo GMT, hledejte "čas utc" na vyhledávače bing.com. Jak často v minutách, kterou chcete spustit pracovní postup je $coordFrequency.
3. Připojte následující skript. Tato část definuje Oozie datové části:

    ```powershell
    #OoziePayload used for Oozie web service submission
    $OoziePayload =  @"
    <?xml version="1.0" encoding="UTF-8"?>
    <configuration>

        <property>
            <name>nameNode</name>
            <value>$storageUrI</value>
        </property>

        <property>
            <name>jobTracker</name>
            <value>jobtrackerhost:9010</value>
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
            <name>oozie.coord.application.path</name>
            <value>$oozieWFPath</value>
        </property>

        <property>
            <name>wfPath</name>
            <value>$oozieWFPath</value>
        </property>

        <property>
            <name>coordStart</name>
            <value>$coordStart</value>
        </property>

        <property>
            <name>coordEnd</name>
            <value>$coordEnd</value>
        </property>

        <property>
            <name>coordFrequency</name>
            <value>$coordFrequency</value>
        </property>

        <property>
            <name>coordTimezone</name>
            <value>$coordTimezone</value>
        </property>

        <property>
            <name>hiveScript</name>
            <value>$hiveScript</value>
        </property>

        <property>
            <name>hiveTableName</name>
            <value>$hiveTableName</value>
        </property>

        <property>
            <name>hiveDataFolder</name>
            <value>$hiveDataFolder</value>
        </property>

        <property>
            <name>hiveOutputFolder</name>
            <value>$hiveOutputFolder</value>
        </property>

        <property>
            <name>sqlDatabaseConnectionString</name>
            <value>&quot;$sqlDatabaseConnectionString&quot;</value>
        </property>

        <property>
            <name>sqlDatabaseTableName</name>
            <value>$SQLDatabaseTableName</value>
        </property>

        <property>
            <name>user.name</name>
            <value>admin</value>
        </property>

    </configuration>
    "@
    ```

   > [!NOTE]
   > Hlavní rozdíl ve srovnání se souborem datové části odeslání pracovního postupu je proměnná **oozie.coord.application.path**. Při odesílání úlohy pracovního postupu použijete **oozie.wf.application.path** místo.

4. Připojte následující skript. Tato část kontroluje stav Oozie webové služby:

    ```powershell
    function checkOozieServerStatus()
    {
        Write-Host "Checking Oozie server status..." -ForegroundColor Green
        $clusterUriStatus = "https://$clusterName.azurehdinsight.net:443/oozie/v2/admin/status"
        $response = Invoke-RestMethod -Method Get -Uri $clusterUriStatus -Credential $creds -OutVariable $OozieServerStatus

        $jsonResponse = ConvertFrom-Json (ConvertTo-Json -InputObject $response)
        $oozieServerSatus = $jsonResponse[0].("systemMode")
        Write-Host "Oozie server status is $oozieServerSatus..."

        if($oozieServerSatus -notmatch "NORMAL")
        {
            Write-Host "Oozie server status is $oozieServerSatus...cannot submit Oozie jobs. Check the server status and re-run the job."
        }
    }
    ```

5. Připojte následující skript. Tato část vytvoří úlohu Oozie:

    ```powershell
    function createOozieJob()
    {
        # create Oozie job
        Write-Host "Sending the following Payload to the cluster:" -ForegroundColor Green
        Write-Host "`n--------`n$OoziePayload`n--------"
        $clusterUriCreateJob = "https://$clusterName.azurehdinsight.net:443/oozie/v2/jobs"
        $response = Invoke-RestMethod -Method Post -Uri $clusterUriCreateJob -Credential $creds -Body $OoziePayload -ContentType "application/xml" -OutVariable $OozieJobName -debug -Verbose

        $jsonResponse = ConvertFrom-Json (ConvertTo-Json -InputObject $response)
        $oozieJobId = $jsonResponse[0].("id")
        Write-Host "Oozie job id is $oozieJobId..."

        return $oozieJobId
    }
    ```

   > [!NOTE]
   > Při odesílání úlohy pracovního postupu, je nutné provést volání spustíte úlohu po vytvoření úlohy jiné webové služby. V takovém případě se aktivuje úlohu koordinátora čas. Úloha se spustí automaticky.

6. Připojte následující skript. Tato část kontroluje stav úlohy Oozie:

    ```powershell
    function checkOozieJobStatus($oozieJobId)
    {
        # get job status
        Write-Host "Sleeping for $waitTimeBetweenOozieJobStatusCheck seconds until the job metadata is populated in the Oozie metastore..." -ForegroundColor Green
        Start-Sleep -Seconds $waitTimeBetweenOozieJobStatusCheck

        Write-Host "Getting job status and waiting for the job to complete..." -ForegroundColor Green
        $clusterUriGetJobStatus = "https://$clusterName.azurehdinsight.net:443/oozie/v2/job/" + $oozieJobId + "?show=info"
        $response = Invoke-RestMethod -Method Get -Uri $clusterUriGetJobStatus -Credential $creds
        $jsonResponse = ConvertFrom-Json (ConvertTo-Json -InputObject $response)
        $JobStatus = $jsonResponse[0].("status")

        while($JobStatus -notmatch "SUCCEEDED|KILLED")
        {
            Write-Host "$(Get-Date -format 'G'): $oozieJobId is in $JobStatus state...waiting $waitTimeBetweenOozieJobStatusCheck seconds for the job to complete..."
            Start-Sleep -Seconds $waitTimeBetweenOozieJobStatusCheck
            $response = Invoke-RestMethod -Method Get -Uri $clusterUriGetJobStatus -Credential $creds
            $jsonResponse = ConvertFrom-Json (ConvertTo-Json -InputObject $response)
            $JobStatus = $jsonResponse[0].("status")
        }

        Write-Host "$(Get-Date -format 'G'): $oozieJobId is in $JobStatus state!"
        if($JobStatus -notmatch "SUCCEEDED")
        {
            Write-Host "Check logs at http://headnode0:9014/cluster for detais."
        }
    }
    ```

7. (Volitelné) Připojte následující skript.

    ```powershell
    function listOozieJobs()
    {
        Write-Host "Listing Oozie jobs..." -ForegroundColor Green
        $clusterUriStatus = "https://$clusterName.azurehdinsight.net:443/oozie/v2/jobs"
        $response = Invoke-RestMethod -Method Get -Uri $clusterUriStatus -Credential $creds

        write-host "Job ID                                   App Name        Status      Started                         Ended"
        write-host "----------------------------------------------------------------------------------------------------------------------------------"
        foreach($job in $response.workflows)
        {
            Write-Host $job.id "`t" $job.appName "`t" $job.status "`t" $job.startTime "`t" $job.endTime
        }
    }

    function ShowOozieJobLog($oozieJobId)
    {
        Write-Host "Showing Oozie job info..." -ForegroundColor Green
        $clusterUriStatus = "https://$clusterName.azurehdinsight.net:443/oozie/v2/job/$oozieJobId" + "?show=log"
        $response = Invoke-RestMethod -Method Get -Uri $clusterUriStatus -Credential $creds
        write-host $response
    }

    function killOozieJob($oozieJobId)
    {
        Write-Host "Killing the Oozie job $oozieJobId..." -ForegroundColor Green
        $clusterUriStartJob = "https://$clusterName.azurehdinsight.net:443/oozie/v2/job/" + $oozieJobId + "?action=kill" #Valid values for the 'action' parameter are 'start', 'suspend', 'resume', 'kill', 'dryrun', 'rerun', and 'change'.
        $response = Invoke-RestMethod -Method Put -Uri $clusterUriStartJob -Credential $creds | Format-Table -HideTableHeaders -debug
    }
    ```

8. Připojte následující skript:

    ```powershell
    checkOozieServerStatus
    # listOozieJobs
    $oozieJobId = createOozieJob($oozieJobId)
    checkOozieJobStatus($oozieJobId)
    # ShowOozieJobLog($oozieJobId)
    # killOozieJob($oozieJobId)
    ```

Pokud chcete spustit další funkce, odeberte znak #.

9. Pokud váš clusteru HDinsight verze 2.1, nahraďte "https://$clusterName.azurehdinsight.net:443/oozie/v2/" s "https://$clusterName.azurehdinsight.net:443/oozie/v1/". Verze clusteru HDInsight 2.1 nemá podporuje verze 2 webové služby.
10. Klikněte na tlačítko **spustit skript** nebo stiskněte klávesu **F5** pro spuštění skriptu. Výstup bude vypadat podobně jako:

     ![Kurz spustit výstup pracovního postupu][img-runworkflow-output]
11. Připojte k vaší databázi SQL zobrazíte exportovaná data.

**Zkontrolujte protokol chyb úlohy**

Chcete-li vyřešit pracovního postupu, Oozie souboru protokolu najdete tady C:\apps\dist\oozie-3.3.2.1.3.2.0-05\oozie-win-distro\logs\Oozie.log z headnode clusteru. Informace o protokolu RDP, najdete v části [clusterů HDInsight správa pomocí portálu Azure][hdinsight-admin-portal].

**Spusťte znovu tohoto kurzu**

Chcete-li znovu spustit pracovní postup, musíte provést následující úlohy:

* Odstraňte výstupní soubor skriptu Hive.
* Odstraníte data v tabulce log4jLogsCount.

Tady je ukázkový skript prostředí Windows PowerShell, který můžete použít:

```powershell
$storageAccountName = "<AzureStorageAccountName>"
$containerName = "<ContainerName>"

#SQL database variables
$sqlDatabaseServer = "<SQLDatabaseServerName>"
$sqlDatabaseLogin = "<SQLDatabaseLoginName>"
$sqlDatabaseLoginPassword = "<SQLDatabaseLoginPassword>"
$sqlDatabaseName = "<SQLDatabaseName>"
$sqlDatabaseTableName = "log4jLogsCount"

Write-host "Delete the Hive script output file ..." -ForegroundColor Green
$storageaccountkey = get-azurestoragekey $storageAccountName | %{$_.Primary}
$destContext = New-AzureStorageContext -StorageAccountName $storageAccountName -StorageAccountKey $storageaccountkey
Remove-AzureStorageBlob -Context $destContext -Blob "tutorials/useoozie/output/000000_0" -Container $containerName

Write-host "Delete all the records from the log4jLogsCount table ..." -ForegroundColor Green
$conn = New-Object System.Data.SqlClient.SqlConnection
$conn.ConnectionString = "Data Source=$sqlDatabaseServer.database.windows.net;Initial Catalog=$sqlDatabaseName;User ID=$sqlDatabaseLogin;Password=$sqlDatabaseLoginPassword;Encrypt=true;Trusted_Connection=false;"
$conn.open()
$cmd = New-Object System.Data.SqlClient.SqlCommand
$cmd.connection = $conn
$cmd.commandtext = "delete from $sqlDatabaseTableName"
$cmd.executenonquery()

$conn.close()
```

## <a name="next-steps"></a>Další postup
V tomto kurzu jste se dozvěděli, jak definovat pracovním postupu Oozie a Oozie coordinator a jak spustit úlohu Oozie coordinator pomocí prostředí Azure PowerShell. Další informace naleznete v následujících článcích:

* [Začínáme s HDInsight][hdinsight-get-started]
* [Používat úložiště objektů Azure Blob s HDInsight][hdinsight-storage]
* [Spravovat HDInsight pomocí prostředí Azure PowerShell][hdinsight-admin-powershell]
* [Nahrání dat do služby HDInsight][hdinsight-upload-data]
* [Použití Sqoopu se službou HDInsight][hdinsight-use-sqoop]
* [Použití Hivu se službou HDInsight][hdinsight-use-hive]
* [Použití Pigu se službou HDInsight][hdinsight-use-pig]
* [Vývoj aplikací Java MapReduce pro HDInsight][hdinsight-develop-java-mapreduce]

[hdinsight-cmdlets-download]: http://go.microsoft.com/fwlink/?LinkID=325563

[hdinsight-versions]:  hdinsight-component-versioning.md
[hdinsight-storage]: hdinsight-hadoop-use-blob-storage.md
[hdinsight-get-started]:hadoop/apache-hadoop-linux-tutorial-get-started.md
[hdinsight-admin-portal]: hdinsight-administer-use-management-portal.md

[hdinsight-use-sqoop]:hadoop/hdinsight-use-sqoop.md
[hdinsight-provision]: hdinsight-hadoop-provision-linux-clusters.md
[hdinsight-admin-powershell]: hdinsight-administer-use-powershell.md
[hdinsight-upload-data]: hdinsight-upload-data.md
[hdinsight-use-hive]:hadoop/hdinsight-use-hive.md
[hdinsight-use-pig]:hadoop/hdinsight-use-pig.md
[hdinsight-storage]: hdinsight-hadoop-use-blob-storage.md
[hdinsight-develop-java-mapreduce]:hadoop/apache-hadoop-develop-deploy-java-mapreduce-linux.md
[hdinsight-use-oozie]: hdinsight-use-oozie.md

[sqldatabase-get-started]: ../sql-database/sql-database-get-started.md

[azure-management-portal]: https://portal.azure.com/
[azure-create-storageaccount]:../storage/common/storage-create-storage-account.md

[apache-hadoop]: http://hadoop.apache.org/
[apache-oozie-400]: http://oozie.apache.org/docs/4.0.0/
[apache-oozie-332]: http://oozie.apache.org/docs/3.3.2/

[powershell-download]: http://azure.microsoft.com/downloads/
[powershell-about-profiles]: http://go.microsoft.com/fwlink/?LinkID=113729
[powershell-install-configure]: /powershell/azureps-cmdlets-docs
[powershell-start]: https://docs.microsoft.com/en-us/powershell/scripting/setup/starting-windows-powershell?view=powershell-6
[powershell-script]: http://technet.microsoft.com/library/ee176949.aspx

[cindygross-hive-tables]: http://blogs.msdn.com/b/cindygross/archive/2013/02/06/hdinsight-hive-internal-and-external-tables-intro.aspx

[img-workflow-diagram]: ./media/hdinsight-use-oozie-coordinator-time/HDI.UseOozie.Workflow.Diagram.png
[img-preparation-output]: ./media/hdinsight-use-oozie-coordinator-time/HDI.UseOozie.Preparation.Output1.png
[img-runworkflow-output]: ./media/hdinsight-use-oozie-coordinator-time/HDI.UseOozie.RunCoord.Output.png

[technetwiki-hive-error]: http://social.technet.microsoft.com/wiki/contents/articles/23047.hdinsight-hive-error-unable-to-rename.aspx
