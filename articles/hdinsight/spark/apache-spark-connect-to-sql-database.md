---
title: Použití Apache Sparku ke čtení a zápisu dat do služby Azure SQL database
description: Zjistěte, jak nastavit připojení mezi clusterem HDInsight Spark a databázi Azure SQL pro data číst, zapisovat data a streamování dat do služby SQL database
services: hdinsight
author: jasonwhowell
ms.author: jasonh
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 05/01/2018
ms.openlocfilehash: 2aec894da6b4e5ffd59fee12bc8476b25955c991
ms.sourcegitcommit: 161d268ae63c7ace3082fc4fad732af61c55c949
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/27/2018
ms.locfileid: "43045792"
---
# <a name="use-hdinsight-spark-cluster-to-read-and-write-data-to-azure-sql-database"></a>Použití clusteru HDInsight Spark ke čtení a zápisu dat do služby Azure SQL database

Zjistěte, jak připojit cluster Apache Spark v Azure HDInsight s Azure SQL database a pak si můžete přečíst, zápis a Streamovat data do služby SQL database. Pokyny v tomto článku pomocí poznámkového bloku Jupyter ke spuštění Scala fragmenty kódu. Můžete však vytvořit samostatné aplikace ve Scale nebo Python a stejné úlohy provádět. 

## <a name="prerequisites"></a>Požadavky

* **Cluster Azure HDInsight Spark**.  Postupujte podle pokynů na adrese [vytvořit cluster Apache Spark v HDInsight](apache-spark-jupyter-spark-sql.md).

* **Azure SQL database**. Postupujte podle pokynů na adrese [vytvoření Azure SQL database](../../sql-database/sql-database-get-started-portal.md). Ujistěte se, že vytvoříte databázi s ukázkou **AdventureWorksLT** schéma a data. Také se ujistěte, že vytvořte pravidlo brány firewall na úrovni serveru umožňuje IP adresu vašeho klienta pro přístup k databázi SQL na serveru. Pokyny pro přidání pravidla brány firewall je k dispozici ve stejném článku. Po vytvoření databáze Azure SQL, ujistěte se, že po ruce zachovat následující hodnoty. Potřebujete připojení k databázi z clusteru Spark.

    * Název serveru, který je hostitelem databáze Azure SQL
    * Název databáze SQL Azure
    * Azure SQL database správce uživatelské jméno / heslo

* **SQL Server Management Studio:** Postupujte podle pokynů na adrese [použití SSMS k připojení a dotazování dat](../../sql-database/sql-database-connect-query-ssms.md).

## <a name="create-a-jupyter-notebook"></a>Vytvoření poznámkového bloku Jupyter

Začněte tím, že vytvoříte Poznámkový blok Jupyter přidružené ke clusteru Spark. Tento poznámkový blok použijete ke spuštění kódu použité v tomto článku. 

1. Z [webu Azure portal](https://portal.azure.com/), otevřete svůj cluster. 

1. Z **rychlé odkazy** klikněte na tlačítko **řídicí panely clusteru** otevřít **řídicí panely clusteru** zobrazení.  Pokud nevidíte **rychlé odkazy**, klikněte na tlačítko **přehled** v levé nabídce v okně.

    ![Řídicí panel clusteru ve Sparku](./media/apache-spark-connect-to-sql-database/hdinsight-cluster-dashboard-on-spark.png "řídicí panel clusteru ve Sparku") 

1. Klikněte na tlačítko **Poznámkový blok Jupyter**. Po vyzvání zadejte přihlašovací údaje správce clusteru.

    ![Poznámkový blok Jupyter ve Sparku](./media/apache-spark-connect-to-sql-database/hdinsight-jupyter-notebook-on-spark.png "Poznámkový blok Jupyter ve Sparku")
   
   > [!NOTE]
   > Poznámkový blok Jupyter v clusteru Spark dostanete také tak, že otevřete následující adresu URL v prohlížeči. Nahraďte **CLUSTERNAME** názvem clusteru:
   >
   > `https://CLUSTERNAME.azurehdinsight.net/jupyter`
   > 
   > 

1. Do poznámkového bloku Jupyter, v pravém horním rohu klikněte na tlačítko **nový**a potom klikněte na tlačítko **Spark** vytvořit poznámkový blok Scala. Poznámkové bloky Jupyter v clusteru HDInsight Spark poskytují také **PySpark** jádra pro Python2 aplikace a **PySpark3** jádra pro aplikace Python3. Pro účely tohoto článku vytvoříme Poznámkový blok Scala.
   
    ![Jádra pro Jupyter notebook ve Sparku](./media/apache-spark-connect-to-sql-database/kernel-jupyter-notebook-on-spark.png "jádra pro Jupyter notebook ve Sparku")

    Další informace o jádrech najdete v tématu s popisem [použití jader poznámkových bloků Jupyter s clustery Apache Spark v HDInsight](apache-spark-jupyter-notebook-kernels.md).

   > [!NOTE]
   > V tomto článku používáme jádra Spark (Scala), protože streamování dat z aplikace Spark do služby SQL database je podporovaný jenom v Scala a Java aktuálně. I v případě, že čtení a zápis do SQL lze provést pomocí Pythonu, pro zajištění konzistence v tomto článku, používáme Scala u všech tří typů operací.
   >

1. Tím se otevře nový poznámkový blok s výchozím názvem, **bez názvu**. Klikněte na název poznámkového bloku a zadejte název podle vašeho výběru.

    ![Zadání názvu poznámkového bloku](./media/apache-spark-connect-to-sql-database/hdinsight-spark-jupyter-notebook-name.png "Zadání názvu poznámkového bloku")

Teď můžete začít vytvářet aplikace.
    
## <a name="read-data-from-azure-sql-database"></a>Čtení dat z Azure SQL database

V této části se číst data z tabulky (například **SalesLT.Address**), která existuje v databázi AdventureWorks.

1. V nového poznámkového bloku Jupyter, do buňky kódu vložte následující fragment kódu a nahraďte zástupné hodnoty hodnotami pro vaši databázi Azure SQL.

       // Declare the values for your Azure SQL database

       val jdbcUsername = "<SQL DB ADMIN USER>"
       val jdbcPassword = "<SQL DB ADMIN PWD>"
       val jdbcHostname = "<SQL SERVER NAME HOSTING SDL DB>" //typically, this is in the form or servername.database.windows.net
       val jdbcPort = 1433
       val jdbcDatabase ="<AZURE SQL DB NAME>"

    Stiskněte **SHIFT+ENTER** a spusťte kód v buňce.  

1. Použít následující fragment kódu k sestavení JDBC adresu URL, kterou můžete předat do Spark dataframe vytvoří rozhraní API `Properties` objekt pro uložení parametrů. Vložte fragment kódu do buňky kódu a stiskněte klávesu **SHIFT + ENTER** ke spuštění.

       import java.util.Properties

       val jdbc_url = s"jdbc:sqlserver://${jdbcHostname}:${jdbcPort};database=${jdbcDatabase};encrypt=true;trustServerCertificate=false;hostNameInCertificate=*.database.windows.net;loginTimeout=60;"
       val connectionProperties = new Properties()
       connectionProperties.put("user", s"${jdbcUsername}")
       connectionProperties.put("password", s"${jdbcPassword}")         

1. Vytvořte datový rámec s daty z tabulky ve službě Azure SQL database pomocí následujícím fragmentu kódu. V tomto fragmentu kódu používáme **SalesLT.Address** tabulku, která je k dispozici jako součást **AdventureWorksLT** databáze. Vložte fragment kódu do buňky kódu a stiskněte klávesu **SHIFT + ENTER** ke spuštění.

       val sqlTableDF = spark.read.jdbc(jdbc_url, "SalesLT.Address", connectionProperties)

1. Teď můžete provádět operace v datového rámce, jako je například načtení schématu dat:

       sqlTableDF.printSchema
   
    Zobrazí se výstup podobný následujícímu:

    ![Zadání názvu poznámkového bloku](./media/apache-spark-connect-to-sql-database/read-from-sql-schema-output.png "Zadání názvu poznámkového bloku")

1. Můžete také provádět operace, jako je načtení prvních 10 řádků.

       sqlTableDF.show(10)

1. Nebo načíst konkrétní sloupce z datové sady.

       sqlTableDF.select("AddressLine1", "City").show(10)

## <a name="write-data-into-azure-sql-database"></a>Zápis dat do služby Azure SQL database

V této části používáme ukázkový soubor CSV k dispozici v clusteru k vytvoření tabulky ve službě Azure SQL database a jeho naplnění daty. Ukázkový soubor CSV (**HVAC.csv**) je k dispozici na všech clusterech HDInsight v `HdiSamples/HdiSamples/SensorSampleData/hvac/HVAC.csv`.

1. V nového poznámkového bloku Jupyter, do buňky kódu vložte následující fragment kódu a nahraďte zástupné hodnoty hodnotami pro vaši databázi Azure SQL.

       // Declare the values for your Azure SQL database

       val jdbcUsername = "<SQL DB ADMIN USER>"
       val jdbcPassword = "<SQL DB ADMIN PWD>"
       val jdbcHostname = "<SQL SERVER NAME HOSTING SDL DB>" //typically, this is in the form or servername.database.windows.net
       val jdbcPort = 1433
       val jdbcDatabase ="<AZURE SQL DB NAME>"

    Stiskněte **SHIFT+ENTER** a spusťte kód v buňce.  

1. Následující fragment kódu vytvoří adresu URL JDBC, kterou můžete předat do Spark dataframe vytvoří rozhraní API `Properties` objekt pro uložení parametrů. Vložte fragment kódu do buňky kódu a stiskněte klávesu **SHIFT + ENTER** ke spuštění.

       import java.util.Properties

       val jdbc_url = s"jdbc:sqlserver://${jdbcHostname}:${jdbcPort};database=${jdbcDatabase};encrypt=true;trustServerCertificate=false;hostNameInCertificate=*.database.windows.net;loginTimeout=60;"
       val connectionProperties = new Properties()
       connectionProperties.put("user", s"${jdbcUsername}")
       connectionProperties.put("password", s"${jdbcPassword}")

1. Použít následující fragment kódu k extrahování schématu dat v HVAC.csv a použití schématu pro načtení dat ze souboru CSV v datový rámec, `readDf`. Vložte fragment kódu do buňky kódu a stiskněte klávesu **SHIFT + ENTER** ke spuštění.

       val userSchema = spark.read.option("header", "true").csv("wasbs:///HdiSamples/HdiSamples/SensorSampleData/hvac/HVAC.csv").schema
       val readDf = spark.read.format("csv").schema(userSchema).load("wasbs:///HdiSamples/HdiSamples/SensorSampleData/hvac/HVAC.csv")

1. Použití `readDf` datový rámec vytvořit dočasnou tabulku `temphvactable`. Potom použijte dočasné tabulky k vytvoření tabulky hive, `hvactable_hive`.

       readDf.createOrReplaceTempView("temphvactable")
       spark.sql("create table hvactable_hive as select * from temphvactable")

1. Nakonec pomocí tabulky hive k vytvoření tabulky ve službě Azure SQL database. Následující fragment kódu vytvoří `hvactable` ve službě Azure SQL database.

       spark.table("hvactable_hive").write.jdbc(jdbc_url, "hvactable", connectionProperties)

1. Připojení k Azure SQL database pomocí aplikace SSMS a ověřte, že se zobrazuje `dbo.hvactable` existuje.

    a. Spusťte aplikaci SSMS a připojte se ke službě Azure SQL database tím, že poskytuje podrobné informace o připojení, jak je znázorněno v následujícím snímku obrazovky.

    ![Připojení k SQL database pomocí SSMS](./media/apache-spark-connect-to-sql-database/connect-to-sql-db-ssms.png "připojit ke službě SQL database pomocí SSMS")

    b. V Průzkumníku objektů rozbalte Azure SQL database a uzel tabulky zobrazíte **dbo.hvactable** vytvořili.

    ![Připojení k SQL database pomocí SSMS](./media/apache-spark-connect-to-sql-database/connect-to-sql-db-ssms-locate-table.png "připojit ke službě SQL database pomocí SSMS")

1. Spuštění dotazu v aplikaci SSMS zobrazíte sloupců v tabulce.

        SELECT * from hvactable

## <a name="stream-data-into-azure-sql-database"></a>Stream dat do služby Azure SQL database

V této části jsme Streamovat data do **hvactable** , že jste již vytvořili ve službě Azure SQL database v předchozí části.

1. Jako první krok, ujistěte se, že neexistují žádné záznamy v **hvactable**. Pomocí aplikace SSMS, spusťte následující dotaz na tabulku.

       DELETE FROM [dbo].[hvactable]

1. Vytvoření nového poznámkového bloku Jupyter pro cluster HDInsight Spark. Do buňky kódu, vložte následující fragment kódu a stiskněte klávesu **SHIFT + ENTER**:

       import org.apache.spark.sql._
       import org.apache.spark.sql.types._
       import org.apache.spark.sql.functions._
       import org.apache.spark.sql.streaming._
       import java.sql.{Connection,DriverManager,ResultSet}

1. Jsme data z datového proudu **HVAC.csv** do hvactable. Je k dispozici v clusteru na soubor HVAC.csv */HdiSamples/HdiSamples/SensorSampleData/HVAC/*. Následující fragment kódu jsme nejdřív získat schéma dat. Streamovat. Pak vytvoříme dataframe streamování pomocí tohoto schématu. Vložte fragment kódu do buňky kódu a stiskněte klávesu **SHIFT + ENTER** ke spuštění.

       val userSchema = spark.read.option("header", "true").csv("wasbs:///HdiSamples/HdiSamples/SensorSampleData/hvac/HVAC.csv").schema
       val readStreamDf = spark.readStream.schema(userSchema).csv("wasbs:///HdiSamples/HdiSamples/SensorSampleData/hvac/") 
       readStreamDf.printSchema

1. Výstup zobrazuje schéma **HVAC.csv**. **Hvactable** má stejné schéma. Výstup uvádí seznam sloupců v tabulce.

    ![Schéma tabulky](./media/apache-spark-connect-to-sql-database/schema-of-table.png "schéma tabulky")

1. Nakonec použijte následující fragment kódu pro čtení dat z HVAC.csv a Streamovat je do **hvactable** ve službě Azure SQL database. Vložte fragment kódu do buňky kódu, nahraďte zástupné hodnoty hodnotami pro vaši databázi Azure SQL a potom stiskněte klávesu **SHIFT + ENTER** ke spuštění.

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

1. Ověřte, že data je přenášen do **hvactable** spuštěním následujícího dotazu v SQL Server Management Studio (SSMS). Při každém spuštění dotazu, zobrazuje počet řádků ve vzestupném tabulky.

        SELECT COUNT(*) FROM hvactable

## <a name="next-steps"></a>Další postup

* [Použití clusteru Spark v HDInsight k analýze dat v Data Lake Store](apache-spark-use-with-data-lake-store.md)
* [Zpracování strukturované streamování událostí pomocí centra událostí](apache-spark-eventhub-structured-streaming.md)
* [Použití strukturovaného streamování Sparku se systémem Kafka v HDInsight](../hdinsight-apache-kafka-spark-structured-streaming.md)
