---
title: Používat Apache Spark ke čtení a zápisu dat do Azure SQL database | Microsoft Docs
description: Zjistěte, jak nastavit připojení mezi clusteru HDInsight Spark a Azure SQL database na data číst, zapisovat data a data datového proudu do databáze SQL
services: hdinsight
documentationcenter: ''
author: nitinme
manager: cgronlun
editor: cgronlun
ms.service: hdinsight
ms.custom: hdinsightactive
ms.devlang: na
ms.topic: conceptual
ms.date: 03/28/2018
ms.author: nitinme
ms.openlocfilehash: 6ef0b1ce589bd19693d45a9e4f579ef260530a40
ms.sourcegitcommit: 1362e3d6961bdeaebed7fb342c7b0b34f6f6417a
ms.translationtype: HT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/18/2018
---
# <a name="use-hdinsight-spark-cluster-to-read-and-write-data-to-azure-sql-database"></a>Použití clusteru HDInsight Spark ke čtení a zápisu dat do Azure SQL database

Zjistěte, jak připojit cluster Apache Spark v Azure HDInsight s Azure SQL database a pak číst, zapisovat a Streamovat data do databáze SQL. Podle pokynů v tomto článku použít Poznámkový blok Jupyter ke spuštění Scala fragmenty kódu. Můžete však vytvoření samostatné aplikace v Scala nebo Python a provádění stejných úloh. 

## <a name="prerequisites"></a>Požadavky

* **Cluster Azure HDInsight Spark**.  Postupujte podle pokynů v [vytvářet cluster Apache Spark v HDInsight](apache-spark-jupyter-spark-sql.md).

* **Databáze SQL Azure**. Postupujte podle pokynů v [vytvoření Azure SQL database](../../sql-database/sql-database-get-started-portal.md). Ujistěte se, můžete vytvořit databázi s ukázkou **AdventureWorksLT** schéma a data. Taky se ujistěte, že vytvoříte pravidla brány firewall na úrovni serveru, které povolí IP adresa vašeho klienta pro přístup k databázi SQL na serveru. Pokynů a přidejte pravidlo brány firewall je k dispozici ve stejném článku. Po vytvoření Azure SQL database, ujistěte se, že byste mít následující hodnoty užitečné. Musíte jim připojení k databázi z clusteru Spark.

    * Název serveru, který je hostitelem databáze Azure SQL
    * Název databáze SQL Azure
    * Azure SQL database správce uživatelské jméno nebo heslo

* **SQL Server Management Studio:** Postupujte podle pokynů v [pomocí SSMS připojit a zadávat dotazy na data](../../sql-database/sql-database-connect-query-ssms.md).

## <a name="create-a-jupyter-notebook"></a>Vytvoření poznámkového bloku Jupyter

Začněte vytvořením poznámkového bloku Jupyter spojené s clusterem Spark. Tento poznámkový blok, který použijete ke spuštění fragmenty kódu používané v tomto článku. 

1. Z [portál Azure](https://portal.azure.com/), otevřete váš cluster. 

2. Z **rychlé odkazy** klikněte na tlačítko **clusteru řídicí panely** otevřete **clusteru řídicí panely** zobrazení.  Pokud nevidíte **rychlé odkazy**, klikněte na tlačítko **přehled** v levé nabídce v okně.

    ![Řídicí panel clusteru Spark](./media/apache-spark-connect-to-sql-database/hdinsight-cluster-dashboard-on-spark.png "řídicí panel clusteru Spark") 

3. Klikněte na tlačítko **Poznámkový blok Jupyter**. Po vyzvání zadejte přihlašovací údaje správce clusteru.

    ![Poznámkový blok Jupyter na Spark](./media/apache-spark-connect-to-sql-database/hdinsight-jupyter-notebook-on-spark.png "Poznámkový blok Jupyter na Spark")
   
   > [!NOTE]
   > Poznámkový blok Jupyter v clusteru Spark se dostanete tak, že otevřete následující adresu URL v prohlížeči. Nahraďte **CLUSTERNAME** názvem clusteru:
   >
   > `https://CLUSTERNAME.azurehdinsight.net/jupyter`
   > 
   > 

4. V poznámkovém bloku Jupyter, v pravém horním rohu, klikněte na tlačítko **nový**a potom klikněte na **Spark** vytvořit Scala Poznámkový blok. Poznámkové bloky Jupyter v clusteru HDInsight Spark poskytují také **PySpark** jádra pro Python2 aplikace a **PySpark3** jádra pro Python3 aplikace. V tomto článku se nám vytvořit Scala Poznámkový blok.
   
    ![Jádra pro poznámkový blok Jupyter na Spark](./media/apache-spark-connect-to-sql-database/kernel-jupyter-notebook-on-spark.png "jádra pro poznámkový blok Jupyter na Spark")

    Další informace o jádrech najdete v tématu s popisem [použití jader poznámkových bloků Jupyter s clustery Apache Spark v HDInsight](apache-spark-jupyter-notebook-kernels.md).

   > [!NOTE]
   > V tomto článku používáme jádra Spark (Scala), protože data streamovaná z Spark do databáze SQL je podporována pouze v Scala a Java aktuálně. I když čtení a zápis do SQL lze provést pomocí Python, konzistence v tomto článku, používáme Scala pro všechny tři operace.
   >

5. Otevře se nový poznámkový blok s výchozím názvem **bez názvu**. Klikněte na název poznámkového bloku a zadejte název svého výběru.

    ![Zadání názvu poznámkového bloku](./media/apache-spark-connect-to-sql-database/hdinsight-spark-jupyter-notebook-name.png "Zadání názvu poznámkového bloku")

Nyní můžete spustit vytvoření vaší aplikace.
    
## <a name="read-data-from-azure-sql-database"></a>Čtení dat z databáze Azure SQL

V této části, čtení dat z tabulky (například **SalesLT.Address**), existuje v databázi AdventureWorks.

1. V nový poznámkový blok Jupyter, v buňce kódu vložte následující fragment a zástupné hodnoty nahraďte hodnotami pro vaši databázi Azure SQL.

       // Declare the values for your Azure SQL database

       val jdbcUsername = "<SQL DB ADMIN USER>"
       val jdbcPassword = "<SQL DB ADMIN PWD>"
       val jdbcHostname = "<SQL SERVER NAME HOSTING SDL DB>" //typically, this is in the form or servername.database.windows.net
       val jdbcPort = 1433
       val jdbcDatabase ="<AZURE SQL DB NAME>"

    Stiskněte **SHIFT+ENTER** a spusťte kód v buňce.  

2. Následující fragment kódu vytvoří adresu URL JDBC, který můžete předat do dataframe Spark vytvoří rozhraní API `Properties` objekt pro uložení parametry. Vložte fragment kódu buňky a stiskněte **SHIFT + ENTER** ke spuštění.

       import java.util.Properties

       val jdbc_url = s"jdbc:sqlserver://${jdbcHostname}:${jdbcPort};database=${jdbcDatabase};encrypt=true;trustServerCertificate=false;hostNameInCertificate=*.database.windows.net;loginTimeout=60;"
       val connectionProperties = new Properties()
       connectionProperties.put("user", s"${jdbcUsername}")
       connectionProperties.put("password", s"${jdbcPassword}")         

3. Následující fragment vytváří dataframe s daty z tabulky v databázi Azure SQL. V tento fragment kódu, použijeme **SalesLT.Address** tabulku, která je k dispozici jako součást **AdventureWorksLT** databáze. Vložte fragment kódu buňky a stiskněte **SHIFT + ENTER** ke spuštění.

       val sqlTableDF = spark.read.jdbc(jdbc_url, "SalesLT.Address", connectionProperties)

4. Teď můžete provádět operace na dataframe, jako je například získávání schéma dat:

       sqlTableDF.printSchema
   
    Zobrazí výstup podobný následujícímu:

    ![Zadání názvu poznámkového bloku](./media/apache-spark-connect-to-sql-database/read-from-sql-schema-output.png "Zadání názvu poznámkového bloku")

5. Můžete také provést operace, jako načtení prvních 10 řádků.

       sqlTableDF.show(10)

6. Nebo načíst určité sloupce z datové sady.

       sqlTableDF.select("AddressLine1", "City").show(10)

## <a name="write-data-into-azure-sql-database"></a>Zapsat data do databáze Azure SQL

V této části používáme ukázkový soubor CSV k dispozici v clusteru k vytvoření tabulky v databázi Azure SQL a jeho naplnění daty. Ukázkový soubor CSV (**HVAC.csv**) je k dispozici na všech clusterech HDInsight v `HdiSamples/HdiSamples/SensorSampleData/hvac/HVAC.csv`.

1. V nový poznámkový blok Jupyter, v buňce kódu vložte následující fragment a zástupné hodnoty nahraďte hodnotami pro vaši databázi Azure SQL.

       // Declare the values for your Azure SQL database

       val jdbcUsername = "<SQL DB ADMIN USER>"
       val jdbcPassword = "<SQL DB ADMIN PWD>"
       val jdbcHostname = "<SQL SERVER NAME HOSTING SDL DB>" //typically, this is in the form or servername.database.windows.net
       val jdbcPort = 1433
       val jdbcDatabase ="<AZURE SQL DB NAME>"

    Stiskněte **SHIFT+ENTER** a spusťte kód v buňce.  

2. Následující fragment kódu vytvoří adresu URL JDBC, který můžete předat do dataframe Spark vytvoří rozhraní API `Properties` objekt pro uložení parametry. Vložte fragment kódu buňky a stiskněte **SHIFT + ENTER** ke spuštění.

       import java.util.Properties

       val jdbc_url = s"jdbc:sqlserver://${jdbcHostname}:${jdbcPort};database=${jdbcDatabase};encrypt=true;trustServerCertificate=false;hostNameInCertificate=*.database.windows.net;loginTimeout=60;"
       val connectionProperties = new Properties()
       connectionProperties.put("user", s"${jdbcUsername}")
       connectionProperties.put("password", s"${jdbcPassword}")

3. Následující fragment kódu extrahuje schéma dat v HVAC.csv a používá schéma Pokud chcete načíst data ze souboru CSV v dataframe, `readDf`. Vložte fragment kódu buňky a stiskněte **SHIFT + ENTER** ke spuštění.

       val userSchema = spark.read.option("header", "true").csv("wasbs:///HdiSamples/HdiSamples/SensorSampleData/hvac/HVAC.csv").schema
       val readDf = spark.read.format("csv").schema(userSchema).load("wasbs:///HdiSamples/HdiSamples/SensorSampleData/hvac/HVAC.csv")

4. Použití `readDf` dataframe vytvořit dočasné tabulky `temphvactable`. K vytvoření tabulky hive, pak použít dočasnou tabulku `hvactable_hive`.

       readDf.createOrReplaceTempView("temphvactable")
       spark.sql("create table hvactable_hive as select * from temphvactable")

5. Nakonec použijte tabulku hive a vytvořte tabulku v databázi Azure SQL. Následující fragment vytváří `hvactable` v databázi Azure SQL.

       spark.table("hvactable_hive").write.jdbc(jdbc_url, "hvactable", connectionProperties)

6. Připojení k databázi Azure SQL pomocí aplikace SSMS a ověřte, že vidíte `dbo.hvactable` existuje.

    a. Spuštění aplikace SSMS a připojení k databázi Azure SQL tím, že poskytuje podrobné informace o připojení, jak je vidět na tomto snímku obrazovky.

    ![Připojit k databázi SQL pomocí aplikace SSMS](./media/apache-spark-connect-to-sql-database/connect-to-sql-db-ssms.png "připojit k databázi SQL pomocí aplikace SSMS")

    b. Z Průzkumník objektů rozbalte databázi Azure SQL a uzel tabulky zobrazíte **dbo.hvactable** vytvořili.

    ![Připojit k databázi SQL pomocí aplikace SSMS](./media/apache-spark-connect-to-sql-database/connect-to-sql-db-ssms-locate-table.png "připojit k databázi SQL pomocí aplikace SSMS")

## <a name="stream-data-into-azure-sql-database"></a>Datový proud dat do databáze Azure SQL

V této části jsme Streamovat data do **hvactable** , že jste již vytvořili v Azure SQL database v předchozí části.

1. Jako první krok, ověřte, zda v žádné záznamy **hvactable**. Pomocí aplikace SSMS, spusťte následující dotaz na tabulku.

       DELETE FROM [dbo].[hvactable]

2. Vytvořte nový poznámkový blok Jupyter v clusteru HDInsight Spark. V buňce kódu vložte následující fragment a potom stiskněte klávesu **SHIFT + ENTER**:

       import org.apache.spark.sql._
       import org.apache.spark.sql.types._
       import org.apache.spark.sql.functions._
       import org.apache.spark.sql.streaming._
       import java.sql.{Connection,DriverManager,ResultSet}

3. Jsme Streamovat data z **HVAC.csv** do hvactable. Je k dispozici v clusteru na soubor HVAC.csv */HdiSamples/HdiSamples/SensorSampleData/TVK/*. V následující fragment kódu jsme nejprve get pro schéma data, která mají být prostřednictvím datového proudu. Poté vytvoříme streamování dataframe použití tohoto schématu. Vložte fragment kódu buňky a stiskněte **SHIFT + ENTER** ke spuštění.

       val userSchema = spark.read.option("header", "true").csv("wasbs:///HdiSamples/HdiSamples/SensorSampleData/hvac/HVAC.csv").schema
       val readStreamDf = spark.readStream.schema(userSchema1).csv("wasbs:///HdiSamples/HdiSamples/SensorSampleData/hvac/") 
       readStreamDf.printSchema

4. Výstup ukazuje schéma **HVAC.csv**. **Hvactable** má také stejné schéma. Výstup obsahuje seznam sloupců v tabulce.

    ![Schéma tabulky](./media/apache-spark-connect-to-sql-database/schema-of-table.png "schéma tabulky")

5. Nakonec použijte následující fragment kódu ke čtení dat z HVAC.csv a Streamovat ho do **hvactable** v databázi Azure SQL. Vložte fragment v buňce kódu, zástupné hodnoty nahraďte hodnotami pro vaši databázi Azure SQL a potom stiskněte klávesu **SHIFT + ENTER** ke spuštění.

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

6. Ověřte, že data je přenášen do **hvactable** spuštěním následujícího dotazu v serveru SQL Server Management Studio (SSMS). Při spuštění dotazu, zobrazuje počet řádků ve vzestupném tabulky.

        SELECT COUNT(*) FROM hvactable

## <a name="next-steps"></a>Další postup

* [Použití clusteru HDInsight Spark k analýze dat v Data Lake Store](apache-spark-use-with-data-lake-store.md)
* [Proces strukturovaná streamovaných událostí pomocí EventHub](apache-spark-eventhub-structured-streaming.md)
* [Používejte Spark strukturovaná streamování s Kafka v HDInsight](../hdinsight-apache-kafka-spark-structured-streaming.md)
