---
title: Použití konektoru Průzkumník dat Azure pro Apache Spark pro přesun dat mezi clustery Průzkumník dat Azure a Spark.
description: Toto téma ukazuje, jak přesunovat data mezi clustery Průzkumník dat Azure a Apache Sparku.
author: orspod
ms.author: orspodek
ms.reviewer: michazag
ms.service: data-explorer
ms.topic: conceptual
ms.date: 4/29/2019
ms.openlocfilehash: 8cb1489a0663556f9dd9e6026a036df2468d656d
ms.sourcegitcommit: 2028fc790f1d265dc96cf12d1ee9f1437955ad87
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/30/2019
ms.locfileid: "64928329"
---
# <a name="azure-data-explorer-connector-for-apache-spark-preview"></a>Průzkumník Azure datový konektor pro Apache Spark (Preview)

[Apache Spark](https://spark.apache.org/) je jednotný analytický modul pro zpracování rozsáhlých dat. Průzkumník služby Azure Data je rychlé a plně spravované datové služby analytics pro analýzu v reálném čase na velké objemy dat. 

Konektor Azure Průzkumník dat pro Spark implementuje zdroj dat a datová jímka pro přesun dat napříč clustery Průzkumník dat Azure a Sparku, chcete-li použít jejich funkce. Pomocí Průzkumníku dat Azure a Apache Sparku, mohou vytvářet rychlé a škálovatelné aplikace využívající scénáře, jako je machine learning (ML), extrakce, transformace a načítání (ETL) a Log Analytics na základě dat. Zápis do Průzkumníku dat Azure můžete udělat v batch a režim tvorby datového proudu.
Čtení z Průzkumníku dat Azure podporuje vyřazení sloupce a predikátu přenosu směrem dolů, což snižuje objem přenášených dat pomocí filtrování dat v Průzkumníku dat Azure.

Konektor Spark Průzkumník dat Azure je [projekt open source](https://github.com/Azure/azure-kusto-spark) , který můžete spustit jakéhokoli jiného clusteru Spark.

> [!NOTE]
> I když některé z níže uvedených příkladech odkazovat [Azure Databricks](https://docs.azuredatabricks.net/) clusteru Spark, konektor Spark Průzkumník dat Azure se nedají přímých závislostí v Databricks nebo jakýkoli jiný distribuční Spark.

## <a name="prerequisites"></a>Požadavky

* [Vytvoření clusteru Průzkumník dat Azure a databáze](/azure/data-explorer/create-cluster-database-portal) 
* Vytvoření clusteru Spark
* Instalace Průzkumníka služby Azure Data knihovna konektorů a knihoven uvedené v [závislosti](https://github.com/Azure/azure-kusto-spark#dependencies) včetně následujících [sady Java SDK pro Kusto](/azure/kusto/api/java/kusto-java-client-library) knihovny:
    * [Klient datové Kusto](https://mvnrepository.com/artifact/com.microsoft.azure.kusto/kusto-data)
    * [Kusto Ingest Client](https://mvnrepository.com/artifact/com.microsoft.azure.kusto/kusto-ingest)
* Předem připravené knihovny pro [Spark 2.4, Scala 2.11](https://github.com/Azure/azure-kusto-spark/releases)

## <a name="how-to-build-the-spark-connector"></a>Jak vytvořit konektor Spark

Konektor Spark je možné vytvořit z [zdroje](https://github.com/Azure/azure-kusto-spark) uvedené níže.

> [!NOTE]
> Tento krok je volitelný. Pokud použijete předdefinované knihovny přejděte na [nastavení clusteru Spark](#spark-cluster-setup).

### <a name="build-prerequisites"></a>Požadavky na sestavení

* Sada Java 1.8 SDK
* [Maven 3.x](https://maven.apache.org/download.cgi) nainstalovaná
* Apache Spark verze 2.4.0 nebo vyšší

> [!TIP]
> verze 2.3.x jsou také podporovány, ale můžou vyžadovat některé změny v pom.xml závislosti.

Pro aplikace Scala a Java pomocí Mavenu projektu definice, propojení vaší aplikace pomocí následujícího artefaktu (nejnovější verze se může lišit):

```Maven
   <dependency>
     <groupId>com.microsoft.azure</groupId>
     <artifactId>spark-kusto-connector</artifactId>
     <version>1.0.0-Beta-02</version>
   </dependency>
```

### <a name="build-commands"></a>Příkazy sestavení

Na soubor jar sestavit a spustit všechny testy:

```
mvn clean package
```

Pokud chcete vytvořit soubor jar, spustit všechny testy a nainstalujte soubor jar do místního úložiště Maven:

```
mvn clean install
```

Další informace najdete v tématu [konektor využití](https://github.com/Azure/azure-kusto-spark#usage).

## <a name="spark-cluster-setup"></a>Nastavení clusteru Spark

> [!NOTE]
> Doporučujeme používat nejnovější verzi konektoru Spark Průzkumník dat Azure při provádění následujících kroků:

1. Nastavte následující Spark nastavení clusteru, založené na Azure Databricks pro cluster Spark 2.4 a Scala 2.11: 

    ![Nastavení clusteru Databricks](media/spark-connector/databricks-cluster.png)

1. Knihovna konektorů Průzkumník dat Azure import:

    ![Knihovna importů Průzkumník dat Azure](media/spark-connector/db-create-library.png)

1. Přidejte další závislosti:

    ![Přidat závislosti](media/spark-connector/db-dependencies.png)

    > [!TIP]
    > Najít verzi správné java pro každou verzi Sparku [tady](https://github.com/Azure/azure-kusto-spark#dependencies).

1. Ověřte, že všechny požadované, že jsou nainstalované knihovny:

    ![Ověřte instalaci knihoven](media/spark-connector/db-libraries-view.png)

## <a name="authentication"></a>Authentication

Konektor Spark Průzkumník dat Azure umožňuje ověřovat pomocí Azure Active Directory (Azure AD) [aplikaci Azure AD](#azure-ad-application-authentication), [přístupový token Azure AD](https://github.com/Azure/azure-kusto-spark/blob/dev/docs/Authentication.md#direct-authentication-with-access-token), [ověřování zařízení ](https://github.com/Azure/azure-kusto-spark/blob/dev/docs/Authentication.md#device-authentication) (pro scénáře li se o neprodukční), nebo [Azure Key Vault](https://github.com/Azure/azure-kusto-spark/blob/dev/docs/Authentication.md#key-vault). Uživatel musí instalaci balíčku azure Key vaultu a zadejte přihlašovací údaje aplikace pro přístup k prostředku služby Key Vault.

### <a name="azure-ad-application-authentication"></a>Ověřování Azure AD aplikace

Většina jednoduché a běžné ověřování. Tato metoda se doporučuje pro použití konektoru Spark Průzkumník dat Azure.

|Vlastnosti  |Popis  |
|---------|---------|
|**KUSTO_AAD_CLIENT_ID**     |   Identifikátor aplikace (klient) Azure AD.      |
|**KUSTO_AAD_AUTHORITY_ID**     |  Autorita ověřování Azure AD. ID Azure AD Directory (tenant).        |
|**KUSTO_AAD_CLIENT_PASSWORD**    |    Klíč aplikace Azure AD pro klienta.     |

### <a name="azure-data-explorer-privileges"></a>Oprávnění Průzkumník dat Azure

Průzkumník clusteru služby Azure Data musí udělit následující oprávnění:

* Pro čtení (zdroj dat), musí mít aplikaci Azure AD *prohlížeč* oprávnění na cílové databázi nebo *správce* oprávnění v cílové tabulce.
* Pro zápis (datová jímka), musí mít aplikaci Azure AD *přijímač* oprávnění na cílové databázi. Musí mít rovněž *uživatele* oprávnění na cílovou databázi a vytvoření nových tabulek. Pokud cílová tabulka již existuje, *správce* lze nastavit oprávnění v cílové tabulce.
 
Další informace o rolích instančních Průzkumník dat Azure najdete v tématu [autorizace na základě rolí](/azure/kusto/management/access-control/role-based-authorization). Role zabezpečení, přečtěte si téma [správu role zabezpečení](/azure/kusto/management/security-roles).

## <a name="spark-sink-writing-to-azure-data-explorer"></a>Spark jímku: Zápis do Průzkumníku dat Azure

1. Nastavení jímky parametry:

     ```scala
    val KustoSparkTestAppId = dbutils.secrets.get(scope = "KustoDemos", key = "KustoSparkTestAppId")
    val KustoSparkTestAppKey = dbutils.secrets.get(scope = "KustoDemos", key = "KustoSparkTestAppKey")
 
    val appId = KustoSparkTestAppId
    val appKey = KustoSparkTestAppKey
    val authorityId = "72f988bf-86f1-41af-91ab-2d7cd011db47"
    val cluster = "Sparktest.eastus2"
    val database = "TestDb"
    val table = "StringAndIntTable"
    ```

1. Spark DataFrame zapište do clusteru Průzkumník dat Azure jako služby batch:

    ```scala
    df.write
      .format("com.microsoft.kusto.spark.datasource")
      .option(KustoOptions.KUSTO_CLUSTER, cluster)
      .option(KustoOptions.KUSTO_DATABASE, database)
      .option(KustoOptions.KUSTO_TABLE, table)
      .option(KustoOptions.KUSTO_AAD_CLIENT_ID, appId)
      .option(KustoOptions.KUSTO_AAD_CLIENT_PASSWORD, appKey) 
      .option(KustoOptions.KUSTO_AAD_AUTHORITY_ID, authorityId)
      .save()
    ```

1. Zápis datových proudů dat:

    ```scala    
    import org.apache.spark.sql.streaming.Trigger
    import java.util.concurrent.TimeUnit
    
    // Set up a checkpoint and disable codeGen. Set up a checkpoint and disable codeGen as a workaround for an known issue 
    spark.conf.set("spark.sql.streaming.checkpointLocation", "/FileStore/temp/checkpoint")
    spark.conf.set("spark.sql.codegen.wholeStage","false")
    
    // Write to a Kusto table fro streaming source
    val kustoQ = csvDf
          .writeStream
          .format("com.microsoft.kusto.spark.datasink.KustoSinkProvider")
          .options(Map(
            KustoOptions.KUSTO_CLUSTER -> cluster,
            KustoOptions.KUSTO_TABLE -> table,
            KustoOptions.KUSTO_DATABASE -> database,
            KustoOptions.KUSTO_AAD_CLIENT_ID -> appId,
            KustoOptions.KUSTO_AAD_CLIENT_PASSWORD -> appKey,
            KustoOptions.KUSTO_AAD_AUTHORITY_ID -> authorityId))
          .trigger(Trigger.Once)
    
    kustoQ.start().awaitTermination(TimeUnit.MINUTES.toMillis(8))
    ```

## <a name="spark-source-reading-from-azure-data-explorer"></a>Spark zdroj: Čtení z Průzkumníku dat Azure

1. Při čtení malých objemů dat, definování dotazu na data:

    ```scala
    val conf: Map[String, String] = Map(
          KustoOptions.KUSTO_AAD_CLIENT_ID -> appId,
          KustoOptions.KUSTO_AAD_CLIENT_PASSWORD -> appKey,
          KustoOptions.KUSTO_QUERY -> s"$table | where (ColB % 1000 == 0) | distinct ColA"      
        )
    
    // Simplified syntax flavor
    import org.apache.spark.sql._
    import com.microsoft.kusto.spark.sql.extension.SparkExtension._
    import org.apache.spark.SparkConf
    
    val df = spark.read.kusto(cluster, database, "", conf)
    display(df)
    ```

1. Při čtení velkých objemů dat, musí být zadaný přechodný objekt blob úložiště. Zadejte klíč SAS pro kontejner úložiště, nebo název účtu úložiště, klíč účtu a název kontejneru. 

    ```scala
    // Use either container/account-key/account name, or container SaS
    val container = dbutils.secrets.get(scope = "KustoDemos", key = "blobContainer")
    val storageAccountKey = dbutils.secrets.get(scope = "KustoDemos", key = "blobStorageAccountKey")
    val storageAccountName = dbutils.secrets.get(scope = "KustoDemos", key = "blobStorageAccountName")
    // val storageSas = dbutils.secrets.get(scope = "KustoDemos", key = "blobStorageSasUrl")
    ```

    V předchozím příkladu jsme nemáte přístup ke Key Vault pomocí konektoru rozhraní. Také jsme použít jednodušší metodu použití tajných kódů Databricks.

1. Čtení z Průzkumníku dat Azure:

    ```scala
    val df2 = spark.read.kusto(cluster, database, "ReallyBigTable", conf3)
    
    val dfFiltered = df2
      .where(df2.col("ColA").startsWith("row-2"))
      .filter("ColB > 12")
      .filter("ColB <= 21")
      .select("ColA")
    
    display(dfFiltered)
    ```
