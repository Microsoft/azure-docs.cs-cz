---
title: Pomocí konektoru Azure Data Explorer pro Apache Spark můžete přesouvat data mezi clustery Azure Data Explorer a Spark.
description: Toto téma ukazuje, jak přesouvat data mezi Clustery Azure Data Explorer a Apache Spark.
author: orspod
ms.author: orspodek
ms.reviewer: michazag
ms.service: data-explorer
ms.topic: conceptual
ms.date: 1/14/2020
ms.openlocfilehash: b358287664ac6d6a3b641e1ab63073810ceb4c40
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "78208581"
---
# <a name="azure-data-explorer-connector-for-apache-spark"></a>Konektor Průzkumníka dat Azure pro Apache Spark

[Apache Spark](https://spark.apache.org/) je jednotný analytický modul pro rozsáhlé zpracování dat. Azure Data Explorer je rychlá a plně spravovaná služba pro analýzu dat v reálném čase na velkých objemech dat. 

Konektor Průzkumníka dat Azure pro Spark je [projekt s otevřeným zdrojovým kódem,](https://github.com/Azure/azure-kusto-spark) který se dá spouštět v libovolném clusteru Spark. Implementuje zdroj dat a jímka dat pro přesun dat přes Azure Data Explorer a Spark clustery. Pomocí Azure Data Explorer a Apache Spark můžete vytvářet rychlé a škálovatelné aplikace zaměřené na scénáře založené na datech. Například strojové učení (ML), extrahovat transformace-zatížení (ETL) a Log Analytics. Pomocí konektoru se Azure Data Explorer stane platným úložištěm dat pro standardní operace zdroje A jímky Spark, jako je zápis, čtení a zápis.

Do Průzkumníka dat Azure můžete zapisovat v dávkovém režimu nebo v režimu streamování. Čtení z Azure Data Explorer podporuje column prořezávání a predikát pushdown, který filtruje data v Průzkumníku dat Azure, snížení objemu přenesených dat.

Toto téma popisuje, jak nainstalovat a nakonfigurovat konektor Azure Data Explorer Spark a přesouvat data mezi clustery Azure Data Explorer a Apache Spark.

> [!NOTE]
> I když některé z níže uvedených příkladů odkazují na cluster [Azure Databricks](https://docs.azuredatabricks.net/) Spark, konektor Azure Data Explorer Spark nepřijímá přímé závislosti na Databricks nebo jiné distribuci Spark.

## <a name="prerequisites"></a>Požadavky

* [Vytvoření clusteru a databáze Průzkumníka dat Azure](/azure/data-explorer/create-cluster-database-portal) 
* Vytvoření clusteru Spark
* Instalace knihovny konektorů Průzkumníka dat Azure:
    * Předem sestavené knihovny pro [Spark 2.4, Scala 2.11](https://github.com/Azure/azure-kusto-spark/releases) 
    * [Maven repo](https://mvnrepository.com/artifact/com.microsoft.azure.kusto/spark-kusto-connector)
* [Maven 3.x](https://maven.apache.org/download.cgi) nainstalován

> [!TIP]
> Podporovány jsou také verze 2.3.x, ale mohou vyžadovat určité změny v závislostech pom.xml.

## <a name="how-to-build-the-spark-connector"></a>Jak vytvořit konektor Spark

> [!NOTE]
> Tento krok je volitelný. Pokud používáte předem vytvořené knihovny, přejděte na [nastavení clusteru Spark](#spark-cluster-setup).

### <a name="build-prerequisites"></a>Sestavení předpokladů

1. Nainstalujte knihovny uvedené v [závislostech,](https://github.com/Azure/azure-kusto-spark#dependencies) včetně následujících knihoven [Kusto Java SDK:](/azure/kusto/api/java/kusto-java-client-library)
    * [Datový klient Kusto](https://mvnrepository.com/artifact/com.microsoft.azure.kusto/kusto-data)
    * [Klient Kusto Ingest](https://mvnrepository.com/artifact/com.microsoft.azure.kusto/kusto-ingest)

1. Naleznete v [tomto zdroji](https://github.com/Azure/azure-kusto-spark) pro vytváření konektoru Spark.

1. U aplikací Scala/Java používajících definice projektu Maven propojte aplikaci s následujícím artefaktem (nejnovější verze se může lišit):
    
    ```Maven
       <dependency>
         <groupId>com.microsoft.azure</groupId>
         <artifactId>spark-kusto-connector</artifactId>
         <version>1.1.0</version>
       </dependency>
    ```

### <a name="build-commands"></a>Vytvářet příkazy

Chcete-li vytvořit nádobu a spustit všechny testy:

```
mvn clean package
```

Chcete-li vytvořit nádobu, spusťte všechny testy a nainstalujte nádobu do místního úložiště Maven:

```
mvn clean install
```

Další informace naleznete v tématu [použití konektoru](https://github.com/Azure/azure-kusto-spark#usage).

## <a name="spark-cluster-setup"></a>Nastavení podskřípěče clusteru

> [!NOTE]
> Při provádění následujících kroků doporučujeme použít nejnovější verzi konektoru Azure Data Explorer Spark.

1. Nakonfigurujte následující nastavení clusteru Spark na základě clusteru Azure Databricks pomocí Spark 2.4.4 a Scala 2.11:

    ![Nastavení clusteru Databricks](media/spark-connector/databricks-cluster.png)
    
1. Nainstalujte nejnovější knihovnu spark-kusto-connector od společnosti Maven:
    
    ![Import knihoven](media/spark-connector/db-libraries-view.png) ![Vyberte spark-kusto-konektor](media/spark-connector/db-dependencies.png)

1. Ověřte, zda jsou nainstalovány všechny požadované knihovny:

    ![Ověření nainstalovaných knihoven](media/spark-connector/db-libraries-view.png)

1. Při instalaci pomocí souboru JAR ověřte, zda byly nainstalovány další závislosti:

    ![Přidání závislostí](media/spark-connector/db-not-maven.png)

## <a name="authentication"></a>Ověřování

Konektor Azure Data Explorer Spark umožňuje ověření pomocí Služby Azure Active Directory (Azure AD) pomocí jedné z následujících metod:
* [Aplikace Azure AD](#azure-ad-application-authentication)
* [Přístupový token Azure AD](https://github.com/Azure/azure-kusto-spark/blob/dev/docs/Authentication.md#direct-authentication-with-access-token)
* [Ověřování zařízení](https://github.com/Azure/azure-kusto-spark/blob/dev/docs/Authentication.md#device-authentication) (pro scénáře bez produkčního prostředí)
* [Trezor klíčů Azure:](https://github.com/Azure/azure-kusto-spark/blob/dev/docs/Authentication.md#key-vault) Chcete-li získat přístup k prostředku trezoru klíčů, nainstalujte balíček azure-keyvault a poskytněte přihlašovací údaje aplikace.

### <a name="azure-ad-application-authentication"></a>Ověřování aplikací Azure AD

Ověřování aplikací Azure AD je nejjednodušší a nejběžnější metoda ověřování a doporučuje se pro konektor Azure Data Explorer Spark.

|Vlastnosti  |Popis  |
|---------|---------|
|**KUSTO_AAD_CLIENT_ID**     |   Identifikátor aplikace (klienta) Azure AD.      |
|**KUSTO_AAD_AUTHORITY_ID**     |  Azure AD ověřování autority. ID adresáře Azure AD (tenanta).        |
|**KUSTO_AAD_CLIENT_PASSWORD**    |    Klíč aplikace Azure AD pro klienta.     |

### <a name="azure-data-explorer-privileges"></a>Oprávnění Průzkumníka dat Azure

Udělte v clusteru Průzkumníka dat Azure následující oprávnění:

* Pro čtení (zdroj dat) musí mít identita Azure AD oprávnění *prohlížeče* v cílové databázi nebo oprávnění *správce* v cílové tabulce.
* Pro zápis (jímky dat), identity Azure AD musí mít oprávnění *ingestor* v cílové databázi. Musí mít také *uživatelská* oprávnění v cílové databázi k vytvoření nových tabulek. Pokud cílová tabulka již existuje, je nutné nakonfigurovat oprávnění *správce* v cílové tabulce.
 
Další informace o hlavních rolích Průzkumníka dat Azure najdete v [tématu autorizace založená na rolích](/azure/kusto/management/access-control/role-based-authorization). Správa rolí zabezpečení naleznete v [tématu Správa rolí zabezpečení](/azure/kusto/management/security-roles).

## <a name="spark-sink-writing-to-azure-data-explorer"></a>Jímka Spark: zápis do Azure Data Exploreru

1. Nastavte parametry jímky:

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

1. Zápis datového rámce Spark do clusteru Průzkumníka dat Azure jako dávka:

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
   
1. Zápis dat datových proudů:

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

## <a name="spark-source-reading-from-azure-data-explorer"></a>Zdroj Spark: čtení z Průzkumníka dat Azure

1. Při čtení [malých objemů dat](/azure/kusto/concepts/querylimits)definujte datový dotaz:

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

1. Volitelné: **Pokud** zadáte přechodné úložiště objektů blob (a ne Azure Data Explorer), objekty BLOB jsou vytvořeny jsou pod odpovědností volajícího. To zahrnuje zřizování úložiště, otáčení přístupových klíčů a odstranění přechodných artefaktů. 
    Modul KustoBlobStorageUtils obsahuje pomocné funkce pro odstranění objektů BLOB na základě souřadnic účtu a kontejneru a přihlašovacích údajů účtu nebo úplné adresy URL SAS s oprávněními k zápisu, čtení a seznamu. Pokud odpovídající RDD již není potřeba, každá transakce ukládá přechodné artefakty objektů blob v samostatném adresáři. Tento adresář je zachycen jako součást protokolů informací o transakcích čtení hlášených v uzlu Ovladače Spark.

    ```scala
    // Use either container/account-key/account name, or container SaS
    val container = dbutils.secrets.get(scope = "KustoDemos", key = "blobContainer")
    val storageAccountKey = dbutils.secrets.get(scope = "KustoDemos", key = "blobStorageAccountKey")
    val storageAccountName = dbutils.secrets.get(scope = "KustoDemos", key = "blobStorageAccountName")
    // val storageSas = dbutils.secrets.get(scope = "KustoDemos", key = "blobStorageSasUrl")
    ```

    Ve výše uvedeném příkladu není k trezoru klíčů přístup pomocí rozhraní konektoru; používá se jednodušší metoda použití tajných kódů Databricks.

1. Čtení z Průzkumníka dat Azure.

    * Pokud **poskytujete** přechodné úložiště objektů blob, přečtěte si z Azure Data Explorer takto:

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

    * Pokud **Azure Data Explorer** poskytuje přechodné úložiště objektů blob, přečtěte si z Průzkumníka dat Azure takto:
    
        ```scala
        val dfFiltered = df2
          .where(df2.col("ColA").startsWith("row-2"))
          .filter("ColB > 12")
          .filter("ColB <= 21")
          .select("ColA")
        
        display(dfFiltered)
        ```

## <a name="next-steps"></a>Další kroky

* Další informace o [konektoru Azure Data Explorer Spark Connector](https://github.com/Azure/azure-kusto-spark/tree/master/docs)
* [Ukázkový kód pro Javu a Python](https://github.com/Azure/azure-kusto-spark/tree/master/samples/src/main)
