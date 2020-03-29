---
title: Použití Apache Spark ke čtení a zápisu dat do Azure SQL Database
description: Zjistěte, jak nastavit připojení mezi clusterem HDInsight Spark a databází Azure SQL pro čtení dat, zápis dat a streamování dat do databáze SQL.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.custom: hdinsightactive
ms.date: 03/05/2020
ms.openlocfilehash: 4e0c1626582297aa7d80cbbd4241b6f81e314f8f
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "78927462"
---
# <a name="use-hdinsight-spark-cluster-to-read-and-write-data-to-azure-sql-database"></a>Použití clusteru HDInsight Spark ke čtení a zápisu dat do Azure SQL Database

Zjistěte, jak připojit cluster Apache Spark v Azure HDInsight u Azure SQL Database a pak číst, zapisovat a streamovat data do databáze SQL. Pokyny v tomto článku používají [poznámkový blok Jupyter](https://jupyter.org/) ke spuštění fragmentů kódu Scala. Můžete však vytvořit samostatnou aplikaci v Systému Scala nebo Python a provádět stejné úlohy.

## <a name="prerequisites"></a>Požadavky

* Cluster Azure HDInsight Spark.  Postupujte podle pokynů na [adrese Vytvoření clusteru Apache Spark v HDInsight](apache-spark-jupyter-spark-sql.md).

* Azure SQL Database. Postupujte podle pokynů na [webu Vytvoření databáze Azure SQL](../../sql-database/sql-database-get-started-portal.md). Ujistěte se, že vytvoříte databázi s ukázkovým schématem **AdventureWorksLT** a daty. Také se ujistěte, že jste vytvořili pravidlo brány firewall na úrovni serveru, které umožní ip adresu vašeho klienta pro přístup k databázi SQL na serveru. Pokyny pro přidání pravidla brány firewall jsou k dispozici ve stejném článku. Po vytvoření Azure SQL Database, ujistěte se, že máte po ruce následující hodnoty. Potřebujete je pro připojení k databázi z clusteru Spark.

    * Název serveru hostující azure sql database.
    * Název databáze Azure SQL.
    * Uživatelské jméno / heslo správce Azure SQL Database.

* SQL Server Management Studio (SSMS). Postupujte podle pokynů na [adrese Použití SSMS pro připojení a dotazování dat](../../sql-database/sql-database-connect-query-ssms.md).

## <a name="create-a-jupyter-notebook"></a>Vytvoříte poznámkový blok Jupyter Notebooks.

Začněte vytvořením [poznámkového bloku Jupyter](https://jupyter.org/) přidruženého ke clusteru Spark. Tento poznámkový blok slouží ke spuštění fragmentů kódu použitých v tomto článku.

1. Na [portálu Azure](https://portal.azure.com/)otevřete cluster.
1. Vyberte **poznámkový blok Jupyter** pod **řídicími panely clusteru** na pravé straně.  Pokud řídicí panely **clusteru**nevidíte , vyberte v levé nabídce **možnost Přehled.** Po vyzvání zadejte přihlašovací údaje správce clusteru.

    ![Jupyter notebook na Apache Spark](./media/apache-spark-connect-to-sql-database/hdinsight-spark-cluster-dashboard-jupyter-notebook.png "Jupyter notebook na Spark")

   > [!NOTE]  
   > K poznámkovému bloku Jupyter v clusteru Spark můžete také přistupovat otevřením následující adresy URL v prohlížeči. Nahraďte **název clusteru názvem clusteru:**
   >
   > `https://CLUSTERNAME.azurehdinsight.net/jupyter`

1. V poznámkovém bloku Jupyter klikněte v pravém horním rohu na **Nový**a potom kliknutím na **Spark** vytvořte poznámkový blok Scala. Jupyter notebooky v clusteru HDInsight Spark také poskytují jádro **PySpark** pro aplikace Python2 a jádro **PySpark3** pro aplikace Python3. Pro tento článek vytvoříme notebook Scala.

    ![Jádra pro poznámkový blok Jupyter na Spark](./media/apache-spark-connect-to-sql-database/kernel-jupyter-notebook-on-spark.png "Jádra pro poznámkový blok Jupyter na Spark")

    Další informace o jádrech najdete v tématu s popisem [použití jader poznámkových bloků Jupyter s clustery Apache Spark v HDInsight](apache-spark-jupyter-notebook-kernels.md).

   > [!NOTE]  
   > V tomto článku používáme jádro Spark (Scala), protože streamování dat ze Spark do sql databáze je podporováno pouze v Scala a Java v současné době. I když čtení a zápis do SQL lze provést pomocí Pythonu, pro konzistenci v tomto článku, používáme Scala pro všechny tři operace.

1. Otevře se nový poznámkový blok s výchozím názvem **Bez názvu**. Klikněte na název poznámkového bloku a zadejte název podle svého výběru.

    ![Zadání názvu poznámkového bloku](./media/apache-spark-connect-to-sql-database/hdinsight-spark-jupyter-notebook-name.png "Zadání názvu poznámkového bloku")

Nyní můžete začít vytvářet aplikaci.

## <a name="read-data-from-azure-sql-database"></a>Čtení dat z Azure SQL Database

V této části čtete data z tabulky (například **SalesLT.Address),** která existuje v databázi AdventureWorks.

1. V novém poznámkovém bloku Jupyter vložte do buňky kódu následující úryvek a nahraďte zástupné hodnoty hodnotami pro vaši Azure SQL Database.

       // Declare the values for your Azure SQL database

       val jdbcUsername = "<SQL DB ADMIN USER>"
       val jdbcPassword = "<SQL DB ADMIN PWD>"
       val jdbcHostname = "<SQL SERVER NAME HOSTING SDL DB>" //typically, this is in the form or servername.database.windows.net
       val jdbcPort = 1433
       val jdbcDatabase ="<AZURE SQL DB NAME>"

    Stiskněte **SHIFT+ENTER** a spusťte kód v buňce.  

1. Pomocí následujícího úryvku vytvořte adresu URL JDBC, kterou můžete předat rozhraním API datového rámce Spark. Kód vytvoří `Properties` objekt, který bude obsahovat parametry. Vložte výstřižek do buňky kódu a spusťte **klávesu SHIFT + ENTER.**

       import java.util.Properties

       val jdbc_url = s"jdbc:sqlserver://${jdbcHostname}:${jdbcPort};database=${jdbcDatabase};encrypt=true;trustServerCertificate=false;hostNameInCertificate=*.database.windows.net;loginTimeout=60;"
       val connectionProperties = new Properties()
       connectionProperties.put("user", s"${jdbcUsername}")
       connectionProperties.put("password", s"${jdbcPassword}")         

1. Pomocí níže uvedeného výstřižku vytvořte datový rámec s daty z tabulky v azure sql databázi. V tomto fragmentu `SalesLT.Address` používáme tabulku, která je k dispozici jako součást databáze **AdventureWorksLT.** Vložte výstřižek do buňky kódu a spusťte **klávesu SHIFT + ENTER.**

       val sqlTableDF = spark.read.jdbc(jdbc_url, "SalesLT.Address", connectionProperties)

1. Nyní můžete provádět operace s datovým rámcem, například získání schématu dat:

       sqlTableDF.printSchema

    Zobrazí se výstup podobný následujícímu:

    ![výstup schématu](./media/apache-spark-connect-to-sql-database/read-from-sql-schema-output.png "výstup schématu")

1. Můžete také provádět operace, jako je, načíst prvních 10 řádků.

       sqlTableDF.show(10)

1. Nebo načtěte určité sloupce z datové sady.

       sqlTableDF.select("AddressLine1", "City").show(10)

## <a name="write-data-into-azure-sql-database"></a>Zápis dat do Azure SQL Database

V této části použijeme ukázkový soubor CSV, který je k dispozici v clusteru, k vytvoření tabulky v Azure SQL Database a naplnění daty. Ukázkový soubor CSV (**HVAC.csv**) je k `HdiSamples/HdiSamples/SensorSampleData/hvac/HVAC.csv`dispozici ve všech clusterech HDInsight na adrese .

1. V novém poznámkovém bloku Jupyter vložte do buňky kódu následující úryvek a nahraďte zástupné hodnoty hodnotami pro vaši Azure SQL Database.

       // Declare the values for your Azure SQL database

       val jdbcUsername = "<SQL DB ADMIN USER>"
       val jdbcPassword = "<SQL DB ADMIN PWD>"
       val jdbcHostname = "<SQL SERVER NAME HOSTING SDL DB>" //typically, this is in the form or servername.database.windows.net
       val jdbcPort = 1433
       val jdbcDatabase ="<AZURE SQL DB NAME>"

    Stiskněte **SHIFT+ENTER** a spusťte kód v buňce.  

1. Následující úryvek vytvoří adresu URL JDBC, kterou můžete předat rozhraním API datového rámce Spark. Kód vytvoří `Properties` objekt, který bude obsahovat parametry. Vložte výstřižek do buňky kódu a spusťte **klávesu SHIFT + ENTER.**

       import java.util.Properties

       val jdbc_url = s"jdbc:sqlserver://${jdbcHostname}:${jdbcPort};database=${jdbcDatabase};encrypt=true;trustServerCertificate=false;hostNameInCertificate=*.database.windows.net;loginTimeout=60;"
       val connectionProperties = new Properties()
       connectionProperties.put("user", s"${jdbcUsername}")
       connectionProperties.put("password", s"${jdbcPassword}")

1. Pomocí následujícího fragmentu výstřižku extrahujte schéma dat v souboru HVAC.csv a pomocí schématu načtěte `readDf`data z csv v datovém rámci . Vložte výstřižek do buňky kódu a spusťte **klávesu SHIFT + ENTER.**

       val userSchema = spark.read.option("header", "true").csv("wasbs:///HdiSamples/HdiSamples/SensorSampleData/hvac/HVAC.csv").schema
       val readDf = spark.read.format("csv").schema(userSchema).load("wasbs:///HdiSamples/HdiSamples/SensorSampleData/hvac/HVAC.csv")

1. Pomocí `readDf` datového rámce vytvořte `temphvactable`dočasnou tabulku . Potom pomocí dočasné tabulky vytvořte tabulku `hvactable_hive`podregistru .

       readDf.createOrReplaceTempView("temphvactable")
       spark.sql("create table hvactable_hive as select * from temphvactable")

1. Nakonec pomocí tabulky podregistru vytvořte tabulku v Azure SQL Database. Následující úryvek `hvactable` vytvoří v Azure SQL Database.

       spark.table("hvactable_hive").write.jdbc(jdbc_url, "hvactable", connectionProperties)

1. Připojte se k Azure SQL Database pomocí SSMS a ověřte, že `dbo.hvactable` se tam zobrazí.

    a. Spusťte SSMS a připojte se k Azure SQL Database tím, že poskytnete podrobnosti o připojení, jak je znázorněno na následujícím obrázku.

    ![Připojení k databázi SQL pomocí SSMS1](./media/apache-spark-connect-to-sql-database/connect-to-sql-db-ssms.png "Připojení k databázi SQL pomocí SSMS1")

    b. V **Průzkumníkovi objektů**rozbalte Azure SQL Database a uzel Tabulka, abyste viděli vytvořenou **dbo.hvactable.**

    ![Připojení k databázi SQL pomocí SSMS2](./media/apache-spark-connect-to-sql-database/connect-to-sql-db-ssms-locate-table.png "Připojení k databázi SQL pomocí SSMS2")

1. Spuštěnídotazu v SSMS zobrazíte sloupce v tabulce.

    ```sql
    SELECT * from hvactable
    ```

## <a name="stream-data-into-azure-sql-database"></a>Streamování dat do Azure SQL Database

V této části streamujeme data do `hvactable` toho, které jste už vytvořili v Azure SQL Database v předchozí části.

1. Jako první krok se ujistěte, že `hvactable`v souboru . nejsou žádné záznamy. Pomocí SSMS spusťte následující dotaz v tabulce.

    ```sql
    TRUNCATE TABLE [dbo].[hvactable]
    ```

1. Vytvořte nový poznámkový blok Jupyter v clusteru HDInsight Spark. Do buňky kódu vložte následující úryvek a stiskněte **klávesu SHIFT + ENTER**:

       import org.apache.spark.sql._
       import org.apache.spark.sql.types._
       import org.apache.spark.sql.functions._
       import org.apache.spark.sql.streaming._
       import java.sql.{Connection,DriverManager,ResultSet}

1. Streamujeme data z **HVAC.csv** do . `hvactable` Soubor HVAC.csv je k `/HdiSamples/HdiSamples/SensorSampleData/HVAC/`dispozici v clusteru na adrese . V následujícím fragmentu nejprve získáme schéma dat, která mají být streamována. Potom vytvoříme datový rámec streamování pomocí tohoto schématu. Vložte výstřižek do buňky kódu a spusťte **klávesu SHIFT + ENTER.**

       val userSchema = spark.read.option("header", "true").csv("wasbs:///HdiSamples/HdiSamples/SensorSampleData/hvac/HVAC.csv").schema
       val readStreamDf = spark.readStream.schema(userSchema).csv("wasbs:///HdiSamples/HdiSamples/SensorSampleData/hvac/") 
       readStreamDf.printSchema

1. Výstup ukazuje schéma **HVAC.csv**. Má `hvactable` stejné schéma stejně. Ve výstupu jsou uvedeny sloupce v tabulce.

    ![hdinsight Apache Spark schéma tabulka](./media/apache-spark-connect-to-sql-database/hdinsight-schema-table.png "Schéma tabulky")

1. Nakonec použijte následující úryvek ke čtení dat z HVAC.csv `hvactable` a streamovat je do databáze Azure SQL Database. Vložte výstřižek do buňky kódu, nahraďte zástupné hodnoty hodnotami pro azure sql database a pak spusťte stisknutím **kláves SHIFT + ENTER.**

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

1. Ověřte, zda jsou data `hvactable` streamována do spuštění následujícího dotazu v aplikaci SQL Server Management Studio (SSMS). Při každém spuštění dotazu se zobrazí počet řádků v tabulce zvyšuje.

    ```sql
    SELECT COUNT(*) FROM hvactable
    ```

## <a name="next-steps"></a>Další kroky

* [Použití clusteru HDInsight Spark k analýze dat v úložišti datových jezer](apache-spark-use-with-data-lake-store.md)
* [Zpracování strukturovaných událostí streamování pomocí EventHubu](apache-spark-eventhub-structured-streaming.md)
* [Používejte strukturované streamování Apache Spark s Apache Kafka na HDInsightu](../hdinsight-apache-kafka-spark-structured-streaming.md)
