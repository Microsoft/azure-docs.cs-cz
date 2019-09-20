---
title: Použití Apache Spark ke čtení a zápisu dat do služby Azure SQL Database
description: Naučte se, jak nastavit připojení mezi clusterem HDInsight Spark a databází SQL Azure pro čtení dat, zápis dat a streamování dat do SQL Database.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 05/21/2019
ms.openlocfilehash: 74bff4059442c85cfcde589c5a6cc7ab36472881
ms.sourcegitcommit: b03516d245c90bca8ffac59eb1db522a098fb5e4
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/19/2019
ms.locfileid: "71147046"
---
# <a name="use-hdinsight-spark-cluster-to-read-and-write-data-to-azure-sql-database"></a>Použití clusteru HDInsight Spark ke čtení a zápisu dat do služby Azure SQL Database

Naučte se připojit cluster Apache Spark ve službě Azure HDInsight se službou Azure SQL Database a pak číst, zapisovat a streamovat data do databáze SQL. Pokyny v tomto článku používají [Jupyter notebook](https://jupyter.org/) ke spouštění fragmentů kódu Scala. Můžete ale vytvořit samostatnou aplikaci v Scala nebo Pythonu a provádět stejné úlohy.

## <a name="prerequisites"></a>Požadavky

* **Cluster Azure HDInsight Spark**.  Postupujte podle pokynů v tématu [Vytvoření clusteru Apache Spark v HDInsight](apache-spark-jupyter-spark-sql.md).

* **Databáze SQL Azure**. Postupujte podle pokynů v tématu [Vytvoření databáze SQL Azure](../../sql-database/sql-database-get-started-portal.md). Ujistěte se, že jste vytvořili databázi s ukázkovým schématem a daty **AdventureWorksLT** . Také se ujistěte, že jste vytvořili pravidlo brány firewall na úrovni serveru, které umožní IP adrese vašeho klienta přístup k databázi SQL na serveru. Pokyny pro přidání pravidla brány firewall jsou k dispozici ve stejném článku. Po vytvoření databáze SQL Azure se ujistěte, že jsou následující hodnoty užitečné. Budete je potřebovat pro připojení k databázi z clusteru Spark.

    * Název serveru hostujícího databázi SQL Azure.
    * Název služby Azure SQL Database.
    * Uživatelské jméno a heslo správce služby Azure SQL Database.

* **SQL Server Management Studio:** Postupujte podle pokynů v tématu [použití SSMS k připojení a dotazování dat](../../sql-database/sql-database-connect-query-ssms.md).

## <a name="create-a-jupyter-notebook"></a>Vytvoření Jupyter Notebook 

Začněte vytvořením [Jupyter notebook](https://jupyter.org/) přidruženého ke clusteru Spark. Pomocí tohoto poznámkového bloku spustíte fragmenty kódu používané v tomto článku. 

1. Z [Azure Portal](https://portal.azure.com/)otevřete svůj cluster.
1. Na pravé straně vyberte **Poznámkový blok Jupyter** pod **řídicími panely clusteru** .  Pokud nevidíte **řídicí panely clusteru**, vyberte v nabídce vlevo možnost **Přehled** . Po vyzvání zadejte přihlašovací údaje správce clusteru.

    ![Jupyter Poznámkový blok na Apache Spark](./media/apache-spark-connect-to-sql-database/hdinsight-spark-cluster-dashboard-jupyter-notebook.png "Poznámkový blok Jupyter ve Sparku")
   
   > [!NOTE]  
   > K poznámkovým blokům Jupyter můžete získat přístup také otevřením následující adresy URL v prohlížeči. Nahraďte **CLUSTERNAME** názvem clusteru:
   >
   > `https://CLUSTERNAME.azurehdinsight.net/jupyter`

1. V Jupyter poznámkovém bloku v pravém horním rohu klikněte na **Nový**a pak kliknutím na **Spark** vytvořte Poznámkový blok Scala. Jupyter poznámkové bloky v clusteru HDInsight Spark také poskytují jádro **PySpark** pro aplikace Python2 a jádro **PySpark3** pro aplikace python3. V tomto článku vytvoříme Poznámkový blok Scala.
   
    ![Jádra pro Poznámkový blok Jupyter ve Sparku](./media/apache-spark-connect-to-sql-database/kernel-jupyter-notebook-on-spark.png "Jádra pro Poznámkový blok Jupyter ve Sparku")

    Další informace o jádrech najdete v tématu s popisem [použití jader poznámkových bloků Jupyter s clustery Apache Spark v HDInsight](apache-spark-jupyter-notebook-kernels.md).

   > [!NOTE]  
   > V tomto článku používáme jádro Spark (Scala), protože streamování dat z Spark do SQL Database se v současnosti podporuje jenom v Scala a Java. I když čtení z a psaní do SQL může být provedeno pomocí Pythonu, pro zajištění konzistence v tomto článku používáme Scala pro všechny tři operace.

1. Tím se otevře nový Poznámkový blok s výchozím názvem bez **názvu**. Klikněte na název poznámkového bloku a zadejte název podle svého výběru.

    ![Zadání názvu poznámkového bloku](./media/apache-spark-connect-to-sql-database/hdinsight-spark-jupyter-notebook-name.png "Zadání názvu poznámkového bloku")

Nyní můžete začít vytvářet aplikace.
    
## <a name="read-data-from-azure-sql-database"></a>Čtení dat ze služby Azure SQL Database

V této části si přečtete data z tabulky (například **tabulky SalesLT. Address**), která existuje v databázi AdventureWorks.

1. V novém poznámkovém bloku Jupyter vložte v buňce kódu následující fragment kódu a nahraďte zástupné hodnoty hodnotami pro vaši databázi SQL Azure.

       // Declare the values for your Azure SQL database

       val jdbcUsername = "<SQL DB ADMIN USER>"
       val jdbcPassword = "<SQL DB ADMIN PWD>"
       val jdbcHostname = "<SQL SERVER NAME HOSTING SDL DB>" //typically, this is in the form or servername.database.windows.net
       val jdbcPort = 1433
       val jdbcDatabase ="<AZURE SQL DB NAME>"

    Stiskněte **SHIFT+ENTER** a spusťte kód v buňce.  

1. Pomocí následujícího fragmentu kódu vytvoříte adresu URL JDBC, kterou můžete předat rozhraním API Spark dataframes, `Properties` vytvoří objekt pro uložení parametrů. Vložte fragment kódu do buňky kódu a stisknutím klávesy **SHIFT + ENTER** spusťte.

       import java.util.Properties

       val jdbc_url = s"jdbc:sqlserver://${jdbcHostname}:${jdbcPort};database=${jdbcDatabase};encrypt=true;trustServerCertificate=false;hostNameInCertificate=*.database.windows.net;loginTimeout=60;"
       val connectionProperties = new Properties()
       connectionProperties.put("user", s"${jdbcUsername}")
       connectionProperties.put("password", s"${jdbcPassword}")         

1. Pomocí následujícího fragmentu kódu můžete vytvořit datový rámec s daty z tabulky ve službě Azure SQL Database. V tomto fragmentu kódu používáme tabulku **tabulky SalesLT. Address** , která je k dispozici jako součást databáze **AdventureWorksLT** . Vložte fragment kódu do buňky kódu a stisknutím klávesy **SHIFT + ENTER** spusťte.

       val sqlTableDF = spark.read.jdbc(jdbc_url, "SalesLT.Address", connectionProperties)

1. Nyní můžete provádět operace s datovým rámcem, jako je například získání schématu dat:

       sqlTableDF.printSchema
   
    Zobrazí se výstup podobný následujícímu:

    ![výstup schématu](./media/apache-spark-connect-to-sql-database/read-from-sql-schema-output.png "výstup schématu")

1. Můžete také provádět operace, jako je, načíst prvních 10 řádků.

       sqlTableDF.show(10)

1. Nebo načte konkrétní sloupce z datové sady.

       sqlTableDF.select("AddressLine1", "City").show(10)

## <a name="write-data-into-azure-sql-database"></a>Zápis dat do služby Azure SQL Database

V této části použijeme ukázkový soubor CSV dostupný v clusteru k vytvoření tabulky ve službě Azure SQL Database a jejímu naplnění dat. Ukázkový soubor CSV (**TVK. csv**) je k dispozici na všech clusterech `HdiSamples/HdiSamples/SensorSampleData/hvac/HVAC.csv`HDInsight na adrese.

1. V novém poznámkovém bloku Jupyter vložte v buňce kódu následující fragment kódu a nahraďte zástupné hodnoty hodnotami pro vaši databázi SQL Azure.

       // Declare the values for your Azure SQL database

       val jdbcUsername = "<SQL DB ADMIN USER>"
       val jdbcPassword = "<SQL DB ADMIN PWD>"
       val jdbcHostname = "<SQL SERVER NAME HOSTING SDL DB>" //typically, this is in the form or servername.database.windows.net
       val jdbcPort = 1433
       val jdbcDatabase ="<AZURE SQL DB NAME>"

    Stiskněte **SHIFT+ENTER** a spusťte kód v buňce.  

1. Následující fragment kódu vytvoří adresu URL JDBC, kterou můžete předat rozhraním API Spark dataframe vytvoří `Properties` objekt pro uložení parametrů. Vložte fragment kódu do buňky kódu a stisknutím klávesy **SHIFT + ENTER** spusťte.

       import java.util.Properties

       val jdbc_url = s"jdbc:sqlserver://${jdbcHostname}:${jdbcPort};database=${jdbcDatabase};encrypt=true;trustServerCertificate=false;hostNameInCertificate=*.database.windows.net;loginTimeout=60;"
       val connectionProperties = new Properties()
       connectionProperties.put("user", s"${jdbcUsername}")
       connectionProperties.put("password", s"${jdbcPassword}")

1. Pomocí následujícího fragmentu kódu rozbalte schéma dat v souboru TVK. csv a pomocí schématu načtěte data ze sdíleného svazku clusteru v dataframe `readDf`. Vložte fragment kódu do buňky kódu a stisknutím klávesy **SHIFT + ENTER** spusťte.

       val userSchema = spark.read.option("header", "true").csv("wasbs:///HdiSamples/HdiSamples/SensorSampleData/hvac/HVAC.csv").schema
       val readDf = spark.read.format("csv").schema(userSchema).load("wasbs:///HdiSamples/HdiSamples/SensorSampleData/hvac/HVAC.csv")

1. Pomocí datového rámce vytvořte dočasnou tabulku, `temphvactable`. `readDf` Pak použijte dočasnou tabulku k vytvoření tabulky podregistru, `hvactable_hive`.

       readDf.createOrReplaceTempView("temphvactable")
       spark.sql("create table hvactable_hive as select * from temphvactable")

1. Nakonec použijte tabulku podregistr k vytvoření tabulky ve službě Azure SQL Database. Následující fragment kódu se `hvactable` vytvoří ve službě Azure SQL Database.

       spark.table("hvactable_hive").write.jdbc(jdbc_url, "hvactable", connectionProperties)

1. Připojte se ke službě Azure SQL Database pomocí SSMS a ověřte, že je `dbo.hvactable` tam zobrazená.

    a. Spusťte SSMS a připojte se ke službě Azure SQL Database zadáním podrobností o připojení, jak je znázorněno na snímku obrazovky níže.

    ![Připojení k SQL Database pomocí SSMS1](./media/apache-spark-connect-to-sql-database/connect-to-sql-db-ssms.png "Připojení k SQL Database pomocí SSMS1")

    b. V Průzkumník objektů rozbalte databázi SQL Azure a uzel tabulky, aby se zobrazila vytvořená tabulka **dbo. TVK** .

    ![Připojení k SQL Database pomocí SSMS2](./media/apache-spark-connect-to-sql-database/connect-to-sql-db-ssms-locate-table.png "Připojení k SQL Database pomocí SSMS2")

1. Spusťte dotaz v SSMS a zobrazte sloupce v tabulce.

        SELECT * from hvactable

## <a name="stream-data-into-azure-sql-database"></a>Streamování dat do služby Azure SQL Database

V této části streamuje data do sady **TVK** , kterou jste už vytvořili ve službě Azure SQL Database, v předchozí části.

1. V prvním kroku se ujistěte, že v police pro **TVK**nejsou žádné záznamy. Pomocí SSMS spusťte v tabulce následující dotaz.

       TRUNCATE TABLE [dbo].[hvactable]

1. Vytvořte nový Poznámkový blok Jupyter v clusteru HDInsight Spark. Do buňky kódu vložte následující fragment kódu a stiskněte klávesu **SHIFT + ENTER**:

       import org.apache.spark.sql._
       import org.apache.spark.sql.types._
       import org.apache.spark.sql.functions._
       import org.apache.spark.sql.streaming._
       import java.sql.{Connection,DriverManager,ResultSet}

1. Data z **TVK. csv** se streamují do služby TVK. V clusteru `/HdiSamples/HdiSamples/SensorSampleData/HVAC/`je k dispozici soubor TVK. csv. V následujícím fragmentu kódu nejprve získáme schéma dat, která se mají streamovat. Pak vytvoříme datový proud streamování pomocí tohoto schématu. Vložte fragment kódu do buňky kódu a stisknutím klávesy **SHIFT + ENTER** spusťte.

       val userSchema = spark.read.option("header", "true").csv("wasbs:///HdiSamples/HdiSamples/SensorSampleData/hvac/HVAC.csv").schema
       val readStreamDf = spark.readStream.schema(userSchema).csv("wasbs:///HdiSamples/HdiSamples/SensorSampleData/hvac/") 
       readStreamDf.printSchema

1. Výstup zobrazuje schéma **TVK. csv**. V **této části jsou** také stejné schéma. Výstup vypíše sloupce v tabulce.

    ![tabulka schématu Apache Spark HDInsight](./media/apache-spark-connect-to-sql-database/hdinsight-schema-table.png "Schéma tabulky")

1. Nakonec pomocí následujícího fragmentu kódu načtěte data ze souboru TVK. csv a Streamujte **ho do úložiště v Azure** SQL Database. Vložte fragment kódu do buňky kódu, nahraďte zástupné hodnoty hodnotami pro vaši databázi SQL Azure a potom stiskněte **SHIFT + ENTER** , aby se spustila.

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

1. Spuštěním následujícího dotazu v SQL Server Management Studio (SSMS) ověřte, zda jsou data přenášena do služby **TVK** . Pokaždé, když spustíte dotaz, zobrazí se počet řádků ve vzestupné tabulce.

        SELECT COUNT(*) FROM hvactable

## <a name="next-steps"></a>Další kroky

* [Použití clusteru HDInsight Spark k analýze dat v Data Lake Storage](apache-spark-use-with-data-lake-store.md)
* [Zpracování událostí streamování strukturovaných dat pomocí EventHub](apache-spark-eventhub-structured-streaming.md)
* [Použití strukturovaného streamování Apache Spark s Apache Kafka v HDInsight](../hdinsight-apache-kafka-spark-structured-streaming.md)
