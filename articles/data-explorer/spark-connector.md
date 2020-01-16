---
title: Pomocí konektoru služby Azure Průzkumník dat můžete Apache Spark přesouvat data mezi clustery Azure Průzkumník dat a Spark.
description: V tomto tématu se dozvíte, jak přesouvat data mezi clustery Azure Průzkumník dat a Apache Spark.
author: orspod
ms.author: orspodek
ms.reviewer: michazag
ms.service: data-explorer
ms.topic: conceptual
ms.date: 1/14/2020
ms.openlocfilehash: 868e9e068244af91e218d906bee115b58906152f
ms.sourcegitcommit: dbcc4569fde1bebb9df0a3ab6d4d3ff7f806d486
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/15/2020
ms.locfileid: "76027908"
---
# <a name="azure-data-explorer-connector-for-apache-spark-preview"></a>Konektor Azure Průzkumník dat pro Apache Spark (Preview)

[Apache Spark](https://spark.apache.org/) je jednotný analytický modul pro zpracování velkých objemů dat. Azure Průzkumník dat je rychlá plně spravovaná služba analýzy dat pro analýzu velkých objemů dat v reálném čase. 

Konektor Azure Průzkumník dat pro Spark implementuje zdroj dat a datovou jímku pro přesouvání dat napříč clustery Azure Průzkumník dat a Sparkem, aby používaly obě jejich schopnosti. Pomocí Azure Průzkumník dat a Apache Spark můžete vytvářet rychlé a škálovatelné aplikace zaměřené na scénáře řízené daty, jako je Machine Learning (ML), extrakce, transformace-načtení (ETL) a Log Analytics. Zápis do Azure Průzkumník dat lze provést v režimu dávek a streamování.
Čtení z Azure Průzkumník dat podporuje vyřazení sloupců a převod predikátu směrem dolů, což snižuje objem přenesených dat vyfiltrováním dat ve službě Azure Průzkumník dat.

Konektor Azure Průzkumník dat Spark je [otevřený zdrojový projekt](https://github.com/Azure/azure-kusto-spark) , který se dá spustit na jakémkoli clusteru Spark. Konektor služby Azure Průzkumník dat Spark zpřístupňuje Azure Průzkumník dat platné úložiště dat pro standardní operace Spark source a jímky, jako je například zápis, čtení a writeStream. 

> [!NOTE]
> I když některé z následujících příkladů odkazují na cluster [Azure Databricks](https://docs.azuredatabricks.net/) Spark, služba Azure Průzkumník dat Spark Connector nepoužívá přímé závislosti na datacihlech ani na jiné distribuci Spark.

## <a name="prerequisites"></a>Požadavky

* [Vytvoření clusteru a databáze Azure Průzkumník dat](/azure/data-explorer/create-cluster-database-portal) 
* Vytvoření clusteru Spark
* Nainstalujte knihovnu konektorů služby Azure Průzkumník dat a knihovny, které jsou uvedené v [závislosti](https://github.com/Azure/azure-kusto-spark#dependencies) , včetně následujících knihoven [Java SDK Kusto](/azure/kusto/api/java/kusto-java-client-library) :
    * [Kusto data Client](https://mvnrepository.com/artifact/com.microsoft.azure.kusto/kusto-data)
    * [Klient ingestování Kusto](https://mvnrepository.com/artifact/com.microsoft.azure.kusto/kusto-ingest)
* Předem připravené knihovny pro [Spark 2,4, Scala 2,11](https://github.com/Azure/azure-kusto-spark/releases) a [úložiště Maven](https://mvnrepository.com/artifact/com.microsoft.azure.kusto/spark-kusto-connector)

## <a name="how-to-build-the-spark-connector"></a>Postup sestavení konektoru Spark

Konektor Spark se dá sestavovat ze [zdrojů](https://github.com/Azure/azure-kusto-spark) , jak je popsáno níže.

> [!NOTE]
> Tento krok je volitelný. Pokud používáte předem připravené knihovny, přečtěte si [instalaci clusteru Spark](#spark-cluster-setup).

### <a name="build-prerequisites"></a>Předpoklady sestavení

* Nainstalovaná sada Java 1,8 SDK
* [Maven 3. x](https://maven.apache.org/download.cgi) nainstalováno
* Apache Spark verze 2.4.0 nebo vyšší

> [!TIP]
> jsou podporovány také verze 2.3. x, ale mohou vyžadovat některé změny v závislosti pom. XML.

Pro aplikace Scala/Java s použitím definicí projektů Maven propojte aplikaci s následujícím artefaktem (nejnovější verze se může lišit):

```Maven
   <dependency>
     <groupId>com.microsoft.azure</groupId>
     <artifactId>spark-kusto-connector</artifactId>
     <version>1.0.0-Beta-02</version>
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
> Při provádění následujících kroků se doporučuje použít nejnovější verzi služby Azure Průzkumník dat Spark Connector:

1. Nastavte následující nastavení clusteru Spark na základě Azure Databricks clusteru pomocí Spark 2.4.4 a Scala 2,11: 

    ![Nastavení clusteru datacihly](media/spark-connector/databricks-cluster.png)
    
1. Nainstalujte nejnovější knihovnu Spark-kusto-Connector z Maven:

    ![Importovat knihovnu Azure Průzkumník dat](media/spark-connector/db-create-library.png)

1. Ověřte, že jsou nainstalované všechny požadované knihovny:

    ![Ověřit nainstalované knihovny](media/spark-connector/db-libraries-view.png)

## <a name="authentication"></a>Ověření

Konektor Azure Průzkumník dat Spark umožňuje ověřování pomocí služby Azure Active Directory (Azure AD) pomocí [aplikace Azure AD](#azure-ad-application-authentication), [přístupového tokenu Azure AD](https://github.com/Azure/azure-kusto-spark/blob/dev/docs/Authentication.md#direct-authentication-with-access-token), [ověřování zařízení](https://github.com/Azure/azure-kusto-spark/blob/dev/docs/Authentication.md#device-authentication) (pro neprodukční scénáře) nebo [Azure Key Vault](https://github.com/Azure/azure-kusto-spark/blob/dev/docs/Authentication.md#key-vault). Uživatel musí nainstalovat balíček Azure-webtrezoru a zadat přihlašovací údaje aplikace pro přístup k prostředku Key Vault.

### <a name="azure-ad-application-authentication"></a>Ověřování aplikací Azure AD

Většina jednoduchých a běžných metod ověřování. Tato metoda se doporučuje pro použití konektoru Azure Průzkumník dat Spark.

|Vlastnosti  |Popis  |
|---------|---------|
|**KUSTO_AAD_CLIENT_ID**     |   Identifikátor aplikace (klienta) služby Azure AD.      |
|**KUSTO_AAD_AUTHORITY_ID**     |  Ověřovací autorita Azure AD. ID adresáře Azure AD (tenant).        |
|**KUSTO_AAD_CLIENT_PASSWORD**    |    Aplikační klíč služby Azure AD pro klienta.     |

### <a name="azure-data-explorer-privileges"></a>Oprávnění pro Azure Průzkumník dat

V clusteru Azure Průzkumník dat musí být udělena následující oprávnění:

* Pro čtení (zdroj dat) musí mít aplikace služby Azure AD oprávnění *prohlížeče* v cílové databázi nebo oprávnění *správce* cílové tabulky.
* Pro psaní (jímka dat) musí aplikace Azure *AD mít v* cílové databázi oprávnění pro ingestování. Aby bylo možné vytvářet nové tabulky, musí mít také *uživatelská* oprávnění k cílové databázi. Pokud cílová tabulka již existuje, lze nakonfigurovat oprávnění *správce* v cílové tabulce.
 
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

    // Set up a checkpoint and disable codeGen. Set up a checkpoint and disable codeGen as a workaround for an known issue 
    spark.conf.set("spark.sql.streaming.checkpointLocation", "/FileStore/temp/checkpoint")
    spark.conf.set("spark.sql.codegen.wholeStage","false") // Use in case a NullPointerException is thrown inside codegen iterator
    
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

1. Při čtení malých objemů dat definujte dotaz na data:

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

1. Při čtení velkých objemů dat je nutné zadat přechodné úložiště objektů BLOB. Zadejte klíč SAS kontejneru úložiště nebo název účtu úložiště, klíč účtu a název kontejneru. Tento krok se vyžaduje jenom pro aktuální verzi Preview konektoru Spark.

    ```scala
    // Use either container/account-key/account name, or container SaS
    val container = dbutils.secrets.get(scope = "KustoDemos", key = "blobContainer")
    val storageAccountKey = dbutils.secrets.get(scope = "KustoDemos", key = "blobStorageAccountKey")
    val storageAccountName = dbutils.secrets.get(scope = "KustoDemos", key = "blobStorageAccountName")
    // val storageSas = dbutils.secrets.get(scope = "KustoDemos", key = "blobStorageSasUrl")
    ```

    V předchozím příkladu nepřistupujeme k Key Vault pomocí rozhraní konektoru. Alternativně používáme jednodušší způsob používání tajných kódů datacihly.

1. Číst z Azure Průzkumník dat:

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

## <a name="next-steps"></a>Další kroky

* Další informace o [konektoru Azure Průzkumník dat Spark](https://github.com/Azure/azure-kusto-spark/tree/master/docs)
* [Ukázka kódu](https://github.com/Azure/azure-kusto-spark/tree/master/samples/src/main)

