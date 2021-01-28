---
title: Použití Apache Spark ke čtení a zápisu dat do Azure SQL Database
description: Naučte se, jak nastavit připojení mezi clusterem HDInsight Spark a Azure SQL Database. Čtení dat, zápis dat a streamování dat do databáze SQL
ms.service: hdinsight
ms.topic: how-to
ms.custom: hdinsightactive,seoapr2020
ms.date: 04/20/2020
ms.openlocfilehash: 4d42cedbc5dc20c929703be106e732b4806f3902
ms.sourcegitcommit: 2f9f306fa5224595fa5f8ec6af498a0df4de08a8
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/28/2021
ms.locfileid: "98940611"
---
# <a name="use-hdinsight-spark-cluster-to-read-and-write-data-to-azure-sql-database"></a>Použití clusteru HDInsight Spark ke čtení a zápisu dat do Azure SQL Database

Naučte se, jak připojit cluster Apache Spark v Azure HDInsight pomocí Azure SQL Database. Pak můžete číst, zapisovat a streamovat data do databáze SQL. Pokyny v tomto článku používají Jupyter Notebook ke spouštění fragmentů kódu Scala. Můžete ale vytvořit samostatnou aplikaci v Scala nebo Pythonu a provádět stejné úlohy.

## <a name="prerequisites"></a>Požadavky

* Cluster Azure HDInsight Spark.  Postupujte podle pokynů v tématu [Vytvoření clusteru Apache Spark v HDInsight](apache-spark-jupyter-spark-sql.md).

* Azure SQL Database Postupujte podle pokynů v tématu [Vytvoření databáze v Azure SQL Database](../../azure-sql/database/single-database-create-quickstart.md). Ujistěte se, že jste vytvořili databázi s ukázkovým schématem a daty **AdventureWorksLT** . Také se ujistěte, že jste vytvořili pravidlo brány firewall na úrovni serveru, které umožní IP adrese vašeho klienta přístup k databázi SQL. Pokyny pro přidání pravidla brány firewall jsou k dispozici ve stejném článku. Po vytvoření databáze SQL se ujistěte, že jsou následující hodnoty užitečné. Budete je potřebovat pro připojení k databázi z clusteru Spark.

    * Název serveru
    * Název databáze.
    * Azure SQL Database uživatelské jméno a heslo správce.

* SQL Server Management Studio (SSMS). Postupujte podle pokynů v tématu [použití SSMS k připojení a dotazování dat](../../azure-sql/database/connect-query-ssms.md).

## <a name="create-a-jupyter-notebook"></a>Vytvoříte poznámkový blok Jupyter Notebooks.

Začněte vytvořením Jupyter Notebook přidruženého ke clusteru Spark. Pomocí tohoto poznámkového bloku spustíte fragmenty kódu používané v tomto článku.

1. Z [Azure Portal](https://portal.azure.com/)otevřete svůj cluster.
1. Na pravé straně vyberte **Jupyter notebook** pod **řídicími panely clusteru** .  Pokud nevidíte **řídicí panely clusteru**, vyberte v nabídce vlevo možnost **Přehled** . Po vyzvání zadejte přihlašovací údaje správce clusteru.

    ![Jupyter Notebook v Apache Spark](./media/apache-spark-connect-to-sql-database/hdinsight-spark-cluster-dashboard-jupyter-notebook.png "Jupyter Notebook ve Sparku")

   > [!NOTE]  
   > K Jupyter Notebook v clusteru Spark můžete přistupovat také otevřením následující adresy URL v prohlížeči. Nahraďte **název_clusteru** názvem vašeho clusteru:
   >
   > `https://CLUSTERNAME.azurehdinsight.net/jupyter`

1. V Jupyter Notebook v pravém horním rohu klikněte na **Nový** a pak kliknutím na **Spark** vytvořte Poznámkový blok Scala. Jupyter poznámkové bloky v clusteru HDInsight Spark také poskytují jádro **PySpark** pro aplikace Python2 a jádro **PySpark3** pro aplikace python3. V tomto článku vytvoříme Poznámkový blok Scala.

    ![Jádra pro Jupyter Notebook ve Sparku](./media/apache-spark-connect-to-sql-database/kernel-jupyter-notebook-on-spark.png "Jádra pro Jupyter Notebook ve Sparku")

    Další informace o jádrech najdete v tématu [použití Jupyter Notebookch jader s Apache Spark clustery v HDInsight](apache-spark-jupyter-notebook-kernels.md).

   > [!NOTE]  
   > V tomto článku používáme jádro Spark (Scala), protože streamování dat z Spark do SQL Database se v současnosti podporuje jenom v Scala a Java. I když čtení z a psaní do SQL může být provedeno pomocí Pythonu, pro zajištění konzistence v tomto článku používáme Scala pro všechny tři operace.

1. Otevře se nový Poznámkový blok s výchozím názvem bez **názvu**. Klikněte na název poznámkového bloku a zadejte název podle svého výběru.

    ![Zadání názvu poznámkového bloku](./media/apache-spark-connect-to-sql-database/hdinsight-spark-jupyter-notebook-name.png "Zadání názvu poznámkového bloku")

Nyní můžete začít vytvářet aplikace.

## <a name="read-data-from-azure-sql-database"></a>Čtení dat z Azure SQL Database

V této části si přečtete data z tabulky (například **tabulky SalesLT. Address**), která existuje v databázi AdventureWorks.

1. V nové Jupyter Notebook v buňce kódu vložte následující fragment kódu a nahraďte zástupné hodnoty hodnotami pro vaši databázi.

    ```scala
    // Declare the values for your database

    val jdbcUsername = "<SQL DB ADMIN USER>"
    val jdbcPassword = "<SQL DB ADMIN PWD>"
    val jdbcHostname = "<SQL SERVER NAME HOSTING SDL DB>" //typically, this is in the form or servername.database.windows.net
    val jdbcPort = 1433
    val jdbcDatabase ="<AZURE SQL DB NAME>"
    ```

    Stiskněte **SHIFT+ENTER** a spusťte kód v buňce.  

1. Pomocí následujícího fragmentu kódu vytvoříte adresu URL JDBC, kterou můžete předat rozhraním API Spark dataframe. Kód vytvoří `Properties` objekt pro uložení parametrů. Vložte fragment kódu do buňky kódu a stisknutím klávesy **SHIFT + ENTER** spusťte.

    ```scala
    import java.util.Properties

    val jdbc_url = s"jdbc:sqlserver://${jdbcHostname}:${jdbcPort};database=${jdbcDatabase};encrypt=true;trustServerCertificate=false;hostNameInCertificate=*.database.windows.net;loginTimeout=60;"
    val connectionProperties = new Properties()
    connectionProperties.put("user", s"${jdbcUsername}")
    connectionProperties.put("password", s"${jdbcPassword}")
    ```

1. Pomocí následujícího fragmentu kódu můžete vytvořit datový rámec s daty z tabulky v databázi. V tomto fragmentu kódu používáme `SalesLT.Address` tabulku, která je k dispozici jako součást databáze **AdventureWorksLT** . Vložte fragment kódu do buňky kódu a stisknutím klávesy **SHIFT + ENTER** spusťte.

    ```scala
    val sqlTableDF = spark.read.jdbc(jdbc_url, "SalesLT.Address", connectionProperties)
    ```

1. Nyní můžete provádět operace s datovým rámcem, jako je například získání schématu dat:

    ```scala
    sqlTableDF.printSchema
    ```

    Zobrazí se výstup podobný následujícímu obrázku:

    ![výstup schématu](./media/apache-spark-connect-to-sql-database/read-from-sql-schema-output.png "výstup schématu")

1. Můžete také provést operace, jako je, načíst prvních 10 řádků.

    ```scala
    sqlTableDF.show(10)
    ```

1. Nebo načte konkrétní sloupce z datové sady.

    ```scala
    sqlTableDF.select("AddressLine1", "City").show(10)
    ```

## <a name="write-data-into-azure-sql-database"></a>Zápis dat do Azure SQL Database

V této části použijeme ukázkový soubor CSV dostupný v clusteru k vytvoření tabulky v databázi a naplníme je daty. Ukázkový soubor CSV (**HVAC.csv**) je k dispozici na všech clusterech HDInsight na adrese `HdiSamples/HdiSamples/SensorSampleData/hvac/HVAC.csv` .

1. V nové Jupyter Notebook v buňce kódu vložte následující fragment kódu a nahraďte zástupné hodnoty hodnotami pro vaši databázi.

    ```scala
    // Declare the values for your database

    val jdbcUsername = "<SQL DB ADMIN USER>"
    val jdbcPassword = "<SQL DB ADMIN PWD>"
    val jdbcHostname = "<SQL SERVER NAME HOSTING SDL DB>" //typically, this is in the form or servername.database.windows.net
    val jdbcPort = 1433
    val jdbcDatabase ="<AZURE SQL DB NAME>"
    ```

    Stiskněte **SHIFT+ENTER** a spusťte kód v buňce.  

1. Následující fragment kódu vytvoří adresu URL JDBC, kterou můžete předat rozhraním API Spark dataframe. Kód vytvoří `Properties` objekt pro uložení parametrů. Vložte fragment kódu do buňky kódu a stisknutím klávesy **SHIFT + ENTER** spusťte.

    ```scala
    import java.util.Properties

    val jdbc_url = s"jdbc:sqlserver://${jdbcHostname}:${jdbcPort};database=${jdbcDatabase};encrypt=true;trustServerCertificate=false;hostNameInCertificate=*.database.windows.net;loginTimeout=60;"
    val connectionProperties = new Properties()
    connectionProperties.put("user", s"${jdbcUsername}")
    connectionProperties.put("password", s"${jdbcPassword}")
    ```

1. Pomocí následujícího fragmentu kódu rozbalte schéma dat v HVAC.csv a pomocí schématu načtěte data z sdíleného svazku clusteru do datového rámce `readDf` . Vložte fragment kódu do buňky kódu a stisknutím klávesy **SHIFT + ENTER** spusťte.

    ```scala
    val userSchema = spark.read.option("header", "true").csv("wasbs:///HdiSamples/HdiSamples/SensorSampleData/hvac/HVAC.csv").schema
    val readDf = spark.read.format("csv").schema(userSchema).load("wasbs:///HdiSamples/HdiSamples/SensorSampleData/hvac/HVAC.csv")
    ```

1. Pomocí datového `readDf` rámce vytvořte dočasnou tabulku, `temphvactable` . Pak použijte dočasnou tabulku k vytvoření tabulky podregistru, `hvactable_hive` .

    ```scala
    readDf.createOrReplaceTempView("temphvactable")
    spark.sql("create table hvactable_hive as select * from temphvactable")
    ```

1. Nakonec pomocí tabulky podregistr vytvořte tabulku v databázi. Následující fragment kódu vytvoří `hvactable` v Azure SQL Database.

    ```scala
    spark.table("hvactable_hive").write.jdbc(jdbc_url, "hvactable", connectionProperties)
    ```

1. Připojte se k Azure SQL Database pomocí SSMS a ověřte, že se zobrazí `dbo.hvactable` .

    a. Spusťte SSMS a připojte se k Azure SQL Database poskytnutím podrobností o připojení, jak je znázorněno na snímku obrazovky níže.

    ![Připojení k SQL Database pomocí SSMS1](./media/apache-spark-connect-to-sql-database/connect-to-sql-db-ssms.png "Připojení k SQL Database pomocí SSMS1")

    b. Z **Průzkumník objektů** rozbalte databázi a uzel tabulky, aby se zobrazila vytvořená tabulka **dbo. TVK** .

    ![Připojení k SQL Database pomocí SSMS2](./media/apache-spark-connect-to-sql-database/connect-to-sql-db-ssms-locate-table.png "Připojení k SQL Database pomocí SSMS2")

1. Spusťte dotaz v SSMS a zobrazte sloupce v tabulce.

    ```sql
    SELECT * from hvactable
    ```

## <a name="stream-data-into-azure-sql-database"></a>Streamování dat do Azure SQL Database

V této části streamuje data do `hvactable` , které jste vytvořili v předchozí části.

1. V prvním kroku se ujistěte, že v nástroji nejsou žádné záznamy `hvactable` . Pomocí SSMS spusťte v tabulce následující dotaz.

    ```sql
    TRUNCATE TABLE [dbo].[hvactable]
    ```

1. Vytvořte nový Jupyter Notebook v clusteru HDInsight Spark. Do buňky kódu vložte následující fragment kódu a stiskněte klávesu **SHIFT + ENTER**:

    ```scala
    import org.apache.spark.sql._
    import org.apache.spark.sql.types._
    import org.apache.spark.sql.functions._
    import org.apache.spark.sql.streaming._
    import java.sql.{Connection,DriverManager,ResultSet}
    ```

1. Streamují data z **HVAC.csv** do `hvactable` . Soubor HVAC.csv je v clusteru k dispozici na adrese `/HdiSamples/HdiSamples/SensorSampleData/HVAC/` . V následujícím fragmentu kódu nejprve získáme schéma dat, která se mají streamovat. Pak vytvoříme datový proud streamování pomocí tohoto schématu. Vložte fragment kódu do buňky kódu a stisknutím klávesy **SHIFT + ENTER** spusťte.

    ```scala
    val userSchema = spark.read.option("header", "true").csv("wasbs:///HdiSamples/HdiSamples/SensorSampleData/hvac/HVAC.csv").schema
    val readStreamDf = spark.readStream.schema(userSchema).csv("wasbs:///HdiSamples/HdiSamples/SensorSampleData/hvac/") 
    readStreamDf.printSchema
    ```

1. Výstup ukazuje schéma **HVAC.csv**. `hvactable`Má také stejné schéma. Výstup vypíše sloupce v tabulce.

    ![Tabulka schématu Apache Spark HDInsight](./media/apache-spark-connect-to-sql-database/hdinsight-schema-table.png "Schéma tabulky")

1. Nakonec použijte následující fragment kódu ke čtení dat z HVAC.csv a jejich streamování do do `hvactable` ve vaší databázi. Vložte fragment kódu do buňky kódu, nahraďte zástupné hodnoty hodnotami pro vaši databázi a potom stiskněte **SHIFT + ENTER** pro spuštění.

    ```scala
    val WriteToSQLQuery  = readStreamDf.writeStream.foreach(new ForeachWriter[Row] {
        var connection:java.sql.Connection = _
        var statement:java.sql.Statement = _

        val jdbcUsername = "<SQL DB ADMIN USER>"
        val jdbcPassword = "<SQL DB ADMIN PWD>"
        val jdbcHostname = "<SQL SERVER NAME HOSTING SDL DB>" //typically, this is in the form or servername.database.windows.net
        val jdbcPort = 1433
        val jdbcDatabase ="<AZURE SQL DB NAME>"
        val driver = "com.microsoft.sqlserver.jdbc.SQLServerDriver"
        val jdbc_url = s"jdbc:sqlserver://${jdbcHostname}:${jdbcPort};database=${jdbcDatabase};encrypt=true;trustServerCertificate=false;hostNameInCertificate=*.database.windows.net;loginTimeout=30;"

        def open(partitionId: Long, version: Long):Boolean = {
        Class.forName(driver)
        connection = DriverManager.getConnection(jdbc_url, jdbcUsername, jdbcPassword)
        statement = connection.createStatement
        true
        }

        def process(value: Row): Unit = {
        val Date  = value(0)
        val Time = value(1)
        val TargetTemp = value(2)
        val ActualTemp = value(3)
        val System = value(4)
        val SystemAge = value(5)
        val BuildingID = value(6)  

        val valueStr = "'" + Date + "'," + "'" + Time + "'," + "'" + TargetTemp + "'," + "'" + ActualTemp + "'," + "'" + System + "'," + "'" + SystemAge + "'," + "'" + BuildingID + "'"
        statement.execute("INSERT INTO " + "dbo.hvactable" + " VALUES (" + valueStr + ")")
        }

        def close(errorOrNull: Throwable): Unit = {
        connection.close
        }
        })

    var streamingQuery = WriteToSQLQuery.start()
    ```

1. `hvactable`Spuštěním následujícího dotazu v SQL Server Management Studio (SSMS) ověřte, zda jsou data přenášena do datového proudu. Pokaždé, když spustíte dotaz, zobrazí se počet řádků ve vzestupné tabulce.

    ```sql
    SELECT COUNT(*) FROM hvactable
    ```

## <a name="next-steps"></a>Další kroky

* [Použití clusteru HDInsight Spark k analýze dat v Data Lake Storage](apache-spark-use-with-data-lake-store.md)
* [Načtení dat a spuštění dotazů v clusteru Apache Spark ve službě Azure HDInsight](apache-spark-load-data-run-query.md)
* [Použití strukturovaného streamování Apache Spark s Apache Kafka v HDInsight](../hdinsight-apache-kafka-spark-structured-streaming.md)
