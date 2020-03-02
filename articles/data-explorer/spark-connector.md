---
title: Pomocí konektoru služby Azure Průzkumník dat můžete Apache Spark přesouvat data mezi clustery Azure Průzkumník dat a Spark.
description: V tomto tématu se dozvíte, jak přesouvat data mezi clustery Azure Průzkumník dat a Apache Spark.
author: orspod
ms.author: orspodek
ms.reviewer: michazag
ms.service: data-explorer
ms.topic: conceptual
ms.date: 1/14/2020
ms.openlocfilehash: b358287664ac6d6a3b641e1ab63073810ceb4c40
ms.sourcegitcommit: 5192c04feaa3d1bd564efe957f200b7b1a93a381
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/02/2020
ms.locfileid: "78208581"
---
# <a name="azure-data-explorer-connector-for-apache-spark"></a>Konektor Azure Průzkumník dat pro Apache Spark

[Apache Spark](https://spark.apache.org/) je jednotný analytický modul pro zpracování velkých objemů dat. Azure Průzkumník dat je rychlá plně spravovaná služba analýzy dat pro analýzu velkých objemů dat v reálném čase. 

Konektor Azure Průzkumník dat pro Spark je [otevřený zdrojový projekt](https://github.com/Azure/azure-kusto-spark) , který se dá spustit na jakémkoli clusteru Spark. Implementuje zdroj dat a datovou jímku pro přesouvání dat napříč clustery Azure Průzkumník dat a Spark. Pomocí Azure Průzkumník dat a Apache Spark můžete vytvářet rychlé a škálovatelné aplikace zaměřené na scénáře řízené daty. Například Machine Learning (ML), extrakce-transformace-Load (ETL) a Log Analytics. Pomocí konektoru se Azure Průzkumník dat stal platným úložištěm dat pro standardní operace zdroje a jímky Sparku, jako je například zápis, čtení a writeStream.

Do služby Azure Průzkumník dat můžete zapisovat buď v režimu dávek, nebo v režimu streamování. Čtení z Azure Průzkumník dat podporuje vyřazení sloupců a přenos predikátu směrem dolů, který filtruje data v Azure Průzkumník dat a snižuje objem přenesených dat.

Toto téma popisuje, jak nainstalovat a nakonfigurovat konektor Azure Průzkumník dat Spark a přesunout data mezi clustery Azure Průzkumník dat a Apache Spark.

> [!NOTE]
> I když některé z následujících příkladů odkazují na cluster [Azure Databricks](https://docs.azuredatabricks.net/) Spark, služba Azure Průzkumník dat Spark Connector nepoužívá přímé závislosti na datacihlech ani na jiné distribuci Spark.

## <a name="prerequisites"></a>Požadavky

* [Vytvoření clusteru a databáze Azure Průzkumník dat](/azure/data-explorer/create-cluster-database-portal) 
* Vytvoření clusteru Spark
* Nainstalovat knihovnu konektorů Azure Průzkumník dat:
    * Předem připravené knihovny pro [Spark 2,4, Scala 2,11](https://github.com/Azure/azure-kusto-spark/releases) 
    * [Úložiště Maven](https://mvnrepository.com/artifact/com.microsoft.azure.kusto/spark-kusto-connector)
* [Maven 3. x](https://maven.apache.org/download.cgi) nainstalováno

> [!TIP]
> jsou podporovány také verze 2.3. x, ale mohou vyžadovat některé změny v závislosti pom. XML.

## <a name="how-to-build-the-spark-connector"></a>Postup sestavení konektoru Spark

> [!NOTE]
> Tento krok je nepovinný. Pokud používáte předem připravené knihovny, přečtěte si [instalaci clusteru Spark](#spark-cluster-setup).

### <a name="build-prerequisites"></a>Předpoklady sestavení

1. Nainstalujte knihovny uvedené v části [závislosti](https://github.com/Azure/azure-kusto-spark#dependencies) včetně následujících [KUSTO knihoven Java SDK](/azure/kusto/api/java/kusto-java-client-library) :
    * [Kusto data Client](https://mvnrepository.com/artifact/com.microsoft.azure.kusto/kusto-data)
    * [Klient ingestování Kusto](https://mvnrepository.com/artifact/com.microsoft.azure.kusto/kusto-ingest)

1. Pokud chcete sestavit konektor Sparku, podívejte se na [Tento zdroj](https://github.com/Azure/azure-kusto-spark) .

1. Pro aplikace Scala/Java s použitím definicí projektů Maven propojte aplikaci s následujícím artefaktem (nejnovější verze se může lišit):
    
    ```Maven
       <dependency>
         <groupId>com.microsoft.azure</groupId>
         <artifactId>spark-kusto-connector</artifactId>
         <version>1.1.0</version>
       </dependency>
    ```

### <a name="build-commands"></a>Příkazy sestavení

Sestavení jar a spuštění všech testů:

```
mvn clean package
```

Pokud chcete sestavit jar, spusťte všechny testy a nainstalujte jar do místního úložiště Maven:

```
mvn clean install
```

Další informace najdete v tématu [použití konektoru](https://github.com/Azure/azure-kusto-spark#usage).

## <a name="spark-cluster-setup"></a>Instalace clusteru Spark

> [!NOTE]
> Při provádění následujících kroků doporučujeme použít nejnovější verzi služby Azure Průzkumník dat Spark Connector.

1. Nakonfigurujte následující nastavení clusteru Spark na základě Azure Databricks clusteru pomocí Spark 2.4.4 a Scala 2,11:

    ![Nastavení clusteru datacihly](media/spark-connector/databricks-cluster.png)
    
1. Nainstalujte nejnovější knihovnu Spark-kusto-Connector z Maven:
    
    ![](media/spark-connector/db-libraries-view.png) importovat knihovny ![vyberte Spark-Kusto-Connector](media/spark-connector/db-dependencies.png)

1. Ověřte, že jsou nainstalované všechny požadované knihovny:

    ![Ověřit nainstalované knihovny](media/spark-connector/db-libraries-view.png)

1. Pro instalaci pomocí souboru JAR ověřte, zda byly nainstalovány další závislosti:

    ![Přidat závislosti](media/spark-connector/db-not-maven.png)

## <a name="authentication"></a>Ověřování

Konektor Azure Průzkumník dat Spark umožňuje ověřování pomocí služby Azure Active Directory (Azure AD) pomocí jedné z následujících metod:
* [Aplikace Azure AD](#azure-ad-application-authentication)
* [Přístupový token Azure AD](https://github.com/Azure/azure-kusto-spark/blob/dev/docs/Authentication.md#direct-authentication-with-access-token)
* [Ověřování zařízení](https://github.com/Azure/azure-kusto-spark/blob/dev/docs/Authentication.md#device-authentication) (pro jiné než produkční scénáře)
* [Azure Key Vault](https://github.com/Azure/azure-kusto-spark/blob/dev/docs/Authentication.md#key-vault) pro přístup k prostředku Key Vault nainstalujte balíček Azure-webtrezoru a zadejte přihlašovací údaje aplikace.

### <a name="azure-ad-application-authentication"></a>Ověřování aplikací Azure AD

Ověřování aplikací Azure AD je nejjednodušší a nejběžnější metoda ověřování a doporučuje se pro konektor Azure Průzkumník dat Spark.

|Vlastnosti  |Popis  |
|---------|---------|
|**KUSTO_AAD_CLIENT_ID**     |   Identifikátor aplikace (klienta) služby Azure AD.      |
|**KUSTO_AAD_AUTHORITY_ID**     |  Ověřovací autorita Azure AD. ID adresáře Azure AD (tenant).        |
|**KUSTO_AAD_CLIENT_PASSWORD**    |    Aplikační klíč služby Azure AD pro klienta.     |

### <a name="azure-data-explorer-privileges"></a>Oprávnění pro Azure Průzkumník dat

Pro cluster Azure Průzkumník dat udělte následující oprávnění:

* Pro čtení (zdroje dat) musí mít identita Azure AD oprávnění *prohlížeče* k cílové databázi nebo oprávnění *správce* cílové tabulky.
* Pro zápis (datová jímka) *musí mít identita* Azure AD v cílové databázi oprávnění ingestovat. Aby bylo možné vytvářet nové tabulky, musí mít také *uživatelská* oprávnění k cílové databázi. Pokud cílová tabulka již existuje, musíte v cílové tabulce nakonfigurovat oprávnění *správce* .
 
Další informace o rolích zabezpečení Azure Průzkumník dat najdete v tématu [autorizace na základě rolí](/azure/kusto/management/access-control/role-based-authorization). Informace o správě rolí zabezpečení najdete v tématu [Správa rolí zabezpečení](/azure/kusto/management/security-roles).

## <a name="spark-sink-writing-to-azure-data-explorer"></a>Jímka Sparku: zápis do Azure Průzkumník dat

1. Nastavit parametry jímky:

     ```scala
    val KustoSparkTestAppId = dbutils.secrets.get(scope = "KustoDemos", key = "KustoSparkTestAppId")
    val KustoSparkTestAppKey = dbutils.secrets.get(scope = "KustoDemos", key = "KustoSparkTestAppKey")
 
    val appId = KustoSparkTestAppId
    val appKey = KustoSparkTestAppKey
    val authorityId = "72f988bf-86f1-41af-91ab-2d7cd011db47" // Optional - defaults to microsoft.com
    val cluster = "Sparktest.eastus2"
    val database = "TestDb"
    val table = "StringAndIntTable"
    ```

1. Zápis Spark dataframe do Azure Průzkumník dat clusteru jako služby Batch:

    ```scala
    import com.microsoft.kusto.spark.datasink.KustoSinkOptions
    import org.apache.spark.sql.{SaveMode, SparkSession}

    df.write
      .format("com.microsoft.kusto.spark.datasource")
      .option(KustoSinkOptions.KUSTO_CLUSTER, cluster)
      .option(KustoSinkOptions.KUSTO_DATABASE, database)
      .option(KustoSinkOptions.KUSTO_TABLE, "Demo3_spark")
      .option(KustoSinkOptions.KUSTO_AAD_CLIENT_ID, appId)
      .option(KustoSinkOptions.KUSTO_AAD_CLIENT_PASSWORD, appKey)
      .option(KustoSinkOptions.KUSTO_AAD_AUTHORITY_ID, authorityId)
      .option(KustoSinkOptions.KUSTO_TABLE_CREATE_OPTIONS, "CreateIfNotExist")
      .mode(SaveMode.Append)
      .save()  
    ```
    
   Nebo použijte zjednodušenou syntaxi:
   
    ```scala
         import com.microsoft.kusto.spark.datasink.SparkIngestionProperties
         import com.microsoft.kusto.spark.sql.extension.SparkExtension._
         
         val sparkIngestionProperties = Some(new SparkIngestionProperties()) // Optional, use None if not needed
         df.write.kusto(cluster, database, table, conf, sparkIngestionProperties)
    ```
   
1. Zapisovat streamovaná data:

    ```scala    
    import org.apache.spark.sql.streaming.Trigger
    import java.util.concurrent.TimeUnit
    import java.util.concurrent.TimeUnit
    import org.apache.spark.sql.streaming.Trigger

    // Set up a checkpoint and disable codeGen. 
    spark.conf.set("spark.sql.streaming.checkpointLocation", "/FileStore/temp/checkpoint")
        
    // Write to a Kusto table from a streaming source
    val kustoQ = df
          .writeStream
          .format("com.microsoft.kusto.spark.datasink.KustoSinkProvider")
          .options(conf) 
          .option(KustoSinkOptions.KUSTO_WRITE_ENABLE_ASYNC, "true") // Optional, better for streaming, harder to handle errors
          .trigger(Trigger.ProcessingTime(TimeUnit.SECONDS.toMillis(10))) // Sync this with the ingestionBatching policy of the database
          .start()
    ```

## <a name="spark-source-reading-from-azure-data-explorer"></a>Zdroj Spark: čtení z Azure Průzkumník dat

1. Při čtení [malých objemů dat](/azure/kusto/concepts/querylimits)definujte dotaz na data:

    ```scala
    import com.microsoft.kusto.spark.datasource.KustoSourceOptions
    import org.apache.spark.SparkConf
    import org.apache.spark.sql._
    import com.microsoft.azure.kusto.data.ClientRequestProperties

    val query = s"$table | where (ColB % 1000 == 0) | distinct ColA"
    val conf: Map[String, String] = Map(
          KustoSourceOptions.KUSTO_AAD_CLIENT_ID -> appId,
          KustoSourceOptions.KUSTO_AAD_CLIENT_PASSWORD -> appKey
        )

    val df = spark.read.format("com.microsoft.kusto.spark.datasource").
      options(conf).
      option(KustoSourceOptions.KUSTO_QUERY, query).
      option(KustoSourceOptions.KUSTO_DATABASE, database).
      option(KustoSourceOptions.KUSTO_CLUSTER, cluster).
      load()

    // Simplified syntax flavor
    import com.microsoft.kusto.spark.sql.extension.SparkExtension._
    
    val cpr: Option[ClientRequestProperties] = None // Optional
    val df2 = spark.read.kusto(cluster, database, query, conf, cpr)
    display(df2)
    ```

1. Volitelné **: Pokud zadáte přechodné** úložiště objektů BLOB (a ne Azure Průzkumník dat), vytvoří se objekty BLOB v rámci zodpovědnosti volajícího. To zahrnuje zřizování úložiště, střídání přístupových klíčů a odstraňování přechodných artefaktů. 
    Modul KustoBlobStorageUtils obsahuje pomocné funkce pro odstraňování objektů BLOB založených na souřadnicích účtu a kontejneru a přihlašovacích údajích k účtu nebo na celé adrese URL SAS s oprávněním zapisovat, číst a zobrazit seznam. Když už odpovídající RDD není potřeba, každá transakce ukládá dočasné artefakty objektů BLOB v samostatném adresáři. Tento adresář se zachycuje jako součást protokolů informací pro čtení-transakce hlášených v uzlu ovladače Spark.

    ```scala
    // Use either container/account-key/account name, or container SaS
    val container = dbutils.secrets.get(scope = "KustoDemos", key = "blobContainer")
    val storageAccountKey = dbutils.secrets.get(scope = "KustoDemos", key = "blobStorageAccountKey")
    val storageAccountName = dbutils.secrets.get(scope = "KustoDemos", key = "blobStorageAccountName")
    // val storageSas = dbutils.secrets.get(scope = "KustoDemos", key = "blobStorageSasUrl")
    ```

    V příkladu výše se Key Vault nepoužívá přes rozhraní konektoru. používá se jednodušší metoda použití tajných kódů datacihly.

1. Přečtěte si z Azure Průzkumník dat.

    * Pokud **zadáte přechodné** úložiště objektů blob, přečtěte si z Azure Průzkumník dat následujícím způsobem:

        ```scala
         val conf3 = Map(
              KustoSourceOptions.KUSTO_AAD_CLIENT_ID -> appId,
              KustoSourceOptions.KUSTO_AAD_CLIENT_PASSWORD -> appKey
              KustoSourceOptions.KUSTO_BLOB_STORAGE_SAS_URL -> storageSas)
        val df2 = spark.read.kusto(cluster, database, "ReallyBigTable", conf3)
        
        val dfFiltered = df2
          .where(df2.col("ColA").startsWith("row-2"))
          .filter("ColB > 12")
          .filter("ColB <= 21")
          .select("ColA")
        
        display(dfFiltered)
        ```

    * Pokud **azure Průzkumník dat** poskytuje přechodné úložiště objektů blob, přečtěte si z Azure Průzkumník dat následujícím způsobem:
    
        ```scala
        val dfFiltered = df2
          .where(df2.col("ColA").startsWith("row-2"))
          .filter("ColB > 12")
          .filter("ColB <= 21")
          .select("ColA")
        
        display(dfFiltered)
        ```

## <a name="next-steps"></a>Další kroky

* Další informace o [konektoru Azure Průzkumník dat Spark](https://github.com/Azure/azure-kusto-spark/tree/master/docs)
* [Ukázka kódu pro Java a Python](https://github.com/Azure/azure-kusto-spark/tree/master/samples/src/main)
