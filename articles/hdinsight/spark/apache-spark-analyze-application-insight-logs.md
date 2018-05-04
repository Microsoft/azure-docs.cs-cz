---
title: Analýza protokolů přehled aplikace s Spark - Azure HDInsight | Microsoft Docs
description: Další informace o exportu aplikace přehledy protokoluje události do úložiště objektů blob a potom analyzovat protokoly s Spark v HDInsight.
services: hdinsight
documentationcenter: ''
author: Blackmist
manager: jhubbard
editor: cgronlun
ms.assetid: 883beae6-9839-45b5-94f7-7eb0f4534ad5
ms.service: hdinsight
ms.custom: hdinsightactive
ms.devlang: na
ms.topic: conceptual
ms.date: 02/12/2018
ms.author: larryfr
ms.openlocfilehash: ade497ecbd09d86519bb6233250a0f3f477d213f
ms.sourcegitcommit: 1362e3d6961bdeaebed7fb342c7b0b34f6f6417a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/18/2018
---
# <a name="analyze-application-insights-telemetry-logs-with-spark-on-hdinsight"></a>Analýza protokolů telemetrie Application Insights pomocí Spark v HDInsight

Naučte se používat Spark v HDInsight k analýze přehled aplikace telemetrická data.

[Visual Studio Application Insights](../../application-insights/app-insights-overview.md) je služba analýzy, která monitoruje webové aplikace. Telemetrická data generované Application Insights je možné exportovat do služby Azure Storage. Jakmile jsou data ve službě Azure Storage, HDInsight lze použít k analýze ho.

## <a name="prerequisites"></a>Požadavky

* Aplikace, která je konfigurovaná pro používání Application Insights.

* Znalost vytvoření clusteru HDInsight se systémem Linux. Další informace najdete v tématu [vytvořit Spark v HDInsight](apache-spark-jupyter-spark-sql.md).

  > [!IMPORTANT]
  > Kroky v tomto dokumentu vyžadují clusteru služby HDInsight, který používá Linux. HDInsight od verze 3.4 výše používá výhradně operační systém Linux. Další informace najdete v tématu [Vyřazení prostředí HDInsight ve Windows](../hdinsight-component-versioning.md#hdinsight-windows-retirement).

* Webový prohlížeč.

Vývoj a testování tohoto dokumentu se používaly v následujících zdrojích informací:

* Application Insights telemetrická data byla generována pomocí [webové aplikace Node.js nakonfigurované na používání Application Insights](../../application-insights/app-insights-nodejs.md).

* K analýze dat byl použit systémem Linux Spark v HDInsight clusteru verze 3.5.

## <a name="architecture-and-planning"></a>Plánování a architektura

Následující diagram znázorňuje architektura služby tohoto příkladu:

![Diagram zobrazující dat odesílaných z Application Insights do úložiště objektů blob, pak zpracovávaných Spark v HDInsight](./media/apache-spark-analyze-application-insight-logs/appinsightshdinsight.png)

### <a name="azure-storage"></a>Úložiště Azure

Application Insights se dá nakonfigurovat nepřetržitě exportovat informace telemetrická data do objektů BLOB. HDInsight pak můžete číst data uložená v objekty BLOB. Existuje však několik požadavků, které je třeba postupovat podle:

* **Umístění**: Pokud účet úložiště a HDInsight jsou v různých umístěních, se může prodloužit latence. Taky zvýší náklady, jako odchozí poplatky za použití k datům přesun mezi oblastmi.

    > [!WARNING]
    > Použití účtu úložiště v jiném umístění než HDInsight není podporováno.

* **Typ objektu BLOB**: HDInsight podporuje pouze objekty BLOB bloku. Aplikace výchozí Insights používá objekty BLOB bloku, proto by měly fungovat ve výchozím nastavení s HDInsight.

Informace o přidání úložiště k existujícímu clusteru najdete v tématu [přidat další účty úložiště](../hdinsight-hadoop-add-storage.md) dokumentu.

### <a name="data-schema"></a>Schéma dat

Poskytuje služby Application Insights [Exportovat datový model](../../application-insights/app-insights-export-data-model.md) informace pro tento formát dat telemetrie exportovány do objektů BLOB. Kroky v tomto dokumentu používají Spark SQL pro práci s daty. Spark SQL můžete automaticky generovat schéma JSON struktuře dat zaznamenaných funkcí Application Insights.

## <a name="export-telemetry-data"></a>Exportovat data telemetrie

Postupujte podle kroků v [nakonfigurovat nepřetržité exportovat](../../application-insights/app-insights-export-telemetry.md) ke konfiguraci vaší Application Insights pro export telemetrické informace do objektu blob úložiště Azure.

## <a name="configure-hdinsight-to-access-the-data"></a>Konfigurace HDInsight k přístupu k datům

Při vytváření clusteru služby HDInsight, přidejte účet úložiště při vytváření clusteru.

K přidání účtu úložiště Azure ve stávajícím clusteru, použijte informace v [přidat další účty úložiště](../hdinsight-hadoop-add-storage.md) dokumentu.

## <a name="analyze-the-data-pyspark"></a>Analyzovat data: PySpark

1. Z [portál Azure](https://portal.azure.com), vyberte vaše Spark v clusteru HDInsight. Z **rychlé odkazy** vyberte **řídicí panely clusteru**a potom vyberte **Poznámkový blok Jupyter** z části Dashboard__ clusteru.

    ![Řídicí panely clusteru](./media/apache-spark-analyze-application-insight-logs/clusterdashboards.png)

2. V pravém horním rohu stránky Jupyter vyberte **nový**a potom **PySpark**. Otevře novou kartu prohlížeče obsahující poznámkového bloku Jupyter na základě Python.

3. V prvním poli (nazývá **buňky**) na stránce, zadejte následující text:

   ```python
   sc._jsc.hadoopConfiguration().set('mapreduce.input.fileinputformat.input.dir.recursive', 'true')
   ```

    Tento kód konfiguruje Spark rekurzivně přístupu strukturu adresáře pro vstupní data. Telemetrii Application Insights je zaznamenána do do struktury adresářů podobně jako `/{telemetry type}/YYYY-MM-DD/{##}/`.

4. Použití **SHIFT + ENTER** spustit kód. Na levé straně buňky '\*, zobrazí se v hranatých závorkách indikující, že kód v této buňce je spouštěna. Po jeho dokončení "\*' změny číslo a výstup podobný tento text se zobrazí pod buňky:

        Creating SparkContext as 'sc'

        ID    YARN Application ID    Kind    State    Spark UI    Driver log    Current session?
        3    application_1468969497124_0001    pyspark    idle    Link    Link    ✔

        Creating HiveContext as 'sqlContext'
        SparkContext and HiveContext created. Executing user code ...
5. První z nich bude vytvořen nový buňku. Zadejte následující text do nové buňky. Nahraďte `CONTAINER` a `STORAGEACCOUNT` s názvem účtu úložiště Azure a název kontejneru objektu blob, který obsahuje data Application Insights.

   ```python
   %%bash
   hdfs dfs -ls wasb://CONTAINER@STORAGEACCOUNT.blob.core.windows.net/
   ```

    Použití **SHIFT + ENTER** provést tuto buňku. Zobrazí výsledek podobná následující text:

        Found 1 items
        drwxrwxrwx   -          0 1970-01-01 00:00 wasb://appinsights@contosostore.blob.core.windows.net/contosoappinsights_2bededa61bc741fbdee6b556571a4831

    Cesta wasb vrátil je umístění data telemetrie Application Insights. Změna `hdfs dfs -ls` řádek v buňce na použití cesty wasb vrátil a pak použijte **SHIFT + ENTER** buňky spustit znovu. Tentokrát výsledky by měl zobrazit adresáře, které obsahují telemetrická data.

   > [!NOTE]
   > Pro zbývající kroky v této části `wasb://appinsights@contosostore.blob.core.windows.net/contosoappinsights_{ID}/Requests` adresáře byl použit. Struktury adresářů se může lišit.

6. V následující buňky, zadejte následující kód: Nahraďte `WASB_PATH` s cestou z předchozího kroku.

   ```python
   jsonFiles = sc.textFile('WASB_PATH')
   jsonData = sqlContext.read.json(jsonFiles)
   ```

    Tento kód vytvoří dataframe z soubory JSON exportované sadou procesu průběžné exportu. Použití **SHIFT + ENTER** spustit tuto buňku.
7. V následující buňky zadejte a spusťte následující příkaz a zobrazí schéma Spark vytvořené pro soubory JSON:

   ```python
   jsonData.printSchema()
   ```

    Schéma pro každý typ telemetrie se liší. Následující příklad je schéma, který se vygeneruje pro webové žádosti (data uložená v `Requests` podadresáři):

        root
        |-- context: struct (nullable = true)
        |    |-- application: struct (nullable = true)
        |    |    |-- version: string (nullable = true)
        |    |-- custom: struct (nullable = true)
        |    |    |-- dimensions: array (nullable = true)
        |    |    |    |-- element: string (containsNull = true)
        |    |    |-- metrics: array (nullable = true)
        |    |    |    |-- element: string (containsNull = true)
        |    |-- data: struct (nullable = true)
        |    |    |-- eventTime: string (nullable = true)
        |    |    |-- isSynthetic: boolean (nullable = true)
        |    |    |-- samplingRate: double (nullable = true)
        |    |    |-- syntheticSource: string (nullable = true)
        |    |-- device: struct (nullable = true)
        |    |    |-- browser: string (nullable = true)
        |    |    |-- browserVersion: string (nullable = true)
        |    |    |-- deviceModel: string (nullable = true)
        |    |    |-- deviceName: string (nullable = true)
        |    |    |-- id: string (nullable = true)
        |    |    |-- osVersion: string (nullable = true)
        |    |    |-- type: string (nullable = true)
        |    |-- location: struct (nullable = true)
        |    |    |-- city: string (nullable = true)
        |    |    |-- clientip: string (nullable = true)
        |    |    |-- continent: string (nullable = true)
        |    |    |-- country: string (nullable = true)
        |    |    |-- province: string (nullable = true)
        |    |-- operation: struct (nullable = true)
        |    |    |-- name: string (nullable = true)
        |    |-- session: struct (nullable = true)
        |    |    |-- id: string (nullable = true)
        |    |    |-- isFirst: boolean (nullable = true)
        |    |-- user: struct (nullable = true)
        |    |    |-- anonId: string (nullable = true)
        |    |    |-- isAuthenticated: boolean (nullable = true)
        |-- internal: struct (nullable = true)
        |    |-- data: struct (nullable = true)
        |    |    |-- documentVersion: string (nullable = true)
        |    |    |-- id: string (nullable = true)
        |-- request: array (nullable = true)
        |    |-- element: struct (containsNull = true)
        |    |    |-- count: long (nullable = true)
        |    |    |-- durationMetric: struct (nullable = true)
        |    |    |    |-- count: double (nullable = true)
        |    |    |    |-- max: double (nullable = true)
        |    |    |    |-- min: double (nullable = true)
        |    |    |    |-- sampledValue: double (nullable = true)
        |    |    |    |-- stdDev: double (nullable = true)
        |    |    |    |-- value: double (nullable = true)
        |    |    |-- id: string (nullable = true)
        |    |    |-- name: string (nullable = true)
        |    |    |-- responseCode: long (nullable = true)
        |    |    |-- success: boolean (nullable = true)
        |    |    |-- url: string (nullable = true)
        |    |    |-- urlData: struct (nullable = true)
        |    |    |    |-- base: string (nullable = true)
        |    |    |    |-- hashTag: string (nullable = true)
        |    |    |    |-- host: string (nullable = true)
        |    |    |    |-- protocol: string (nullable = true)
8. Použijte následující postupy k registraci dataframe jako dočasné tabulky a spouštění dotazů na data:

   ```python
   jsonData.registerTempTable("requests")
   df = sqlContext.sql("select context.location.city from requests where context.location.city is not null")
   df.show()
   ```

    Tento dotaz vrací informace o městě pro horní 20 záznamy, kde context.location.city není null.

   > [!NOTE]
   > Struktura kontextu je součástí všech telemetrických dat zaznamenaných funkcí Application Insights. Element města nemusí vložené do protokolů. Schéma slouží k identifikaci další prvky s možností dotazu, které mohou obsahovat data pro svoje protokoly.

    Tento dotaz vrací informace podobná následující text:

        +---------+
        |     city|
        +---------+
        | Bellevue|
        |  Redmond|
        |  Seattle|
        |Charlotte|
        ...
        +---------+

## <a name="analyze-the-data-scala"></a>Analyzovat data: Scala

1. Z [portál Azure](https://portal.azure.com), vyberte vaše Spark v clusteru HDInsight. Z **rychlé odkazy** vyberte **řídicí panely clusteru**a potom vyberte **Poznámkový blok Jupyter** z části Dashboard__ clusteru.

    ![Řídicí panely clusteru](./media/apache-spark-analyze-application-insight-logs/clusterdashboards.png)
2. V pravém horním rohu stránky Jupyter vyberte **nový**a potom **Scala**. Zobrazí se na nové záložce prohlížeče obsahující na základě Scala poznámkového bloku Jupyter.
3. V prvním poli (nazývá **buňky**) na stránce, zadejte následující text:

   ```scala
   sc.hadoopConfiguration.set("mapreduce.input.fileinputformat.input.dir.recursive", "true")
   ```

    Tento kód konfiguruje Spark rekurzivně přístupu strukturu adresáře pro vstupní data. Telemetrii Application Insights je zaznamenána do do struktury adresářů podobná `/{telemetry type}/YYYY-MM-DD/{##}/`.

4. Použití **SHIFT + ENTER** spustit kód. Na levé straně buňky '\*, zobrazí se v hranatých závorkách indikující, že kód v této buňce je spouštěna. Po jeho dokončení "\*' změny číslo a výstup podobný tento text se zobrazí pod buňky:

        Creating SparkContext as 'sc'

        ID    YARN Application ID    Kind    State    Spark UI    Driver log    Current session?
        3    application_1468969497124_0001    spark    idle    Link    Link    ✔

        Creating HiveContext as 'sqlContext'
        SparkContext and HiveContext created. Executing user code ...
5. První z nich bude vytvořen nový buňku. Zadejte následující text do nové buňky. Nahraďte `CONTAINER` a `STORAGEACCOUNT` s názvem účtu úložiště Azure a název kontejneru objektu blob, který obsahuje Application Insights protokoly.

   ```scala
   %%bash
   hdfs dfs -ls wasb://CONTAINER@STORAGEACCOUNT.blob.core.windows.net/
   ```

    Použití **SHIFT + ENTER** provést tuto buňku. Zobrazí výsledek podobná následující text:

        Found 1 items
        drwxrwxrwx   -          0 1970-01-01 00:00 wasb://appinsights@contosostore.blob.core.windows.net/contosoappinsights_2bededa61bc741fbdee6b556571a4831

    Cesta wasb vrátil je umístění data telemetrie Application Insights. Změna `hdfs dfs -ls` řádek v buňce na použití cesty wasb vrátil a pak použijte **SHIFT + ENTER** buňky spustit znovu. Tentokrát výsledky by měl zobrazit adresáře, které obsahují telemetrická data.

   > [!NOTE]
   > Pro zbývající kroky v této části `wasb://appinsights@contosostore.blob.core.windows.net/contosoappinsights_{ID}/Requests` adresáře byl použit. Tento adresář neexistuje, není-li telemetrická data pro webovou aplikaci.

6. V následující buňky, zadejte následující kód: Nahraďte `WASB\_PATH` s cestou z předchozího kroku.

   ```scala
   var jsonFiles = sc.textFile('WASB_PATH')
   val sqlContext = new org.apache.spark.sql.SQLContext(sc)
   var jsonData = sqlContext.read.json(jsonFiles)
   ```

    Tento kód vytvoří dataframe z soubory JSON exportované sadou procesu průběžné exportu. Použití **SHIFT + ENTER** spustit tuto buňku.

7. V následující buňky zadejte a spusťte následující příkaz a zobrazí schéma Spark vytvořené pro soubory JSON:

   ```scala
   jsonData.printSchema
   ```

    Schéma pro každý typ telemetrie se liší. Následující příklad je schéma, který se vygeneruje pro webové žádosti (data uložená v `Requests` podadresáři):

        root
        |-- context: struct (nullable = true)
        |    |-- application: struct (nullable = true)
        |    |    |-- version: string (nullable = true)
        |    |-- custom: struct (nullable = true)
        |    |    |-- dimensions: array (nullable = true)
        |    |    |    |-- element: string (containsNull = true)
        |    |    |-- metrics: array (nullable = true)
        |    |    |    |-- element: string (containsNull = true)
        |    |-- data: struct (nullable = true)
        |    |    |-- eventTime: string (nullable = true)
        |    |    |-- isSynthetic: boolean (nullable = true)
        |    |    |-- samplingRate: double (nullable = true)
        |    |    |-- syntheticSource: string (nullable = true)
        |    |-- device: struct (nullable = true)
        |    |    |-- browser: string (nullable = true)
        |    |    |-- browserVersion: string (nullable = true)
        |    |    |-- deviceModel: string (nullable = true)
        |    |    |-- deviceName: string (nullable = true)
        |    |    |-- id: string (nullable = true)
        |    |    |-- osVersion: string (nullable = true)
        |    |    |-- type: string (nullable = true)
        |    |-- location: struct (nullable = true)
        |    |    |-- city: string (nullable = true)
        |    |    |-- clientip: string (nullable = true)
        |    |    |-- continent: string (nullable = true)
        |    |    |-- country: string (nullable = true)
        |    |    |-- province: string (nullable = true)
        |    |-- operation: struct (nullable = true)
        |    |    |-- name: string (nullable = true)
        |    |-- session: struct (nullable = true)
        |    |    |-- id: string (nullable = true)
        |    |    |-- isFirst: boolean (nullable = true)
        |    |-- user: struct (nullable = true)
        |    |    |-- anonId: string (nullable = true)
        |    |    |-- isAuthenticated: boolean (nullable = true)
        |-- internal: struct (nullable = true)
        |    |-- data: struct (nullable = true)
        |    |    |-- documentVersion: string (nullable = true)
        |    |    |-- id: string (nullable = true)
        |-- request: array (nullable = true)
        |    |-- element: struct (containsNull = true)
        |    |    |-- count: long (nullable = true)
        |    |    |-- durationMetric: struct (nullable = true)
        |    |    |    |-- count: double (nullable = true)
        |    |    |    |-- max: double (nullable = true)
        |    |    |    |-- min: double (nullable = true)
        |    |    |    |-- sampledValue: double (nullable = true)
        |    |    |    |-- stdDev: double (nullable = true)
        |    |    |    |-- value: double (nullable = true)
        |    |    |-- id: string (nullable = true)
        |    |    |-- name: string (nullable = true)
        |    |    |-- responseCode: long (nullable = true)
        |    |    |-- success: boolean (nullable = true)
        |    |    |-- url: string (nullable = true)
        |    |    |-- urlData: struct (nullable = true)
        |    |    |    |-- base: string (nullable = true)
        |    |    |    |-- hashTag: string (nullable = true)
        |    |    |    |-- host: string (nullable = true)
        |    |    |    |-- protocol: string (nullable = true)

8. Použijte následující postupy k registraci dataframe jako dočasné tabulky a spouštění dotazů na data:

   ```scala
   jsonData.registerTempTable("requests")
   var city = sqlContext.sql("select context.location.city from requests where context.location.city is not null limit 10").show()
   ```

    Tento dotaz vrací informace o městě pro horní 20 záznamy, kde context.location.city není null.

   > [!NOTE]
   > Struktura kontextu je součástí všech telemetrických dat zaznamenaných funkcí Application Insights. Element města nemusí vložené do protokolů. Schéma slouží k identifikaci další prvky s možností dotazu, které mohou obsahovat data pro svoje protokoly.
   >
   >

    Tento dotaz vrací informace podobná následující text:

        +---------+
        |     city|
        +---------+
        | Bellevue|
        |  Redmond|
        |  Seattle|
        |Charlotte|
        ...
        +---------+

## <a name="next-steps"></a>Další postup

Další příklady použití Spark pro práci s daty a služby v Azure najdete v následujících dokumentech:

* [Spark s BI: Provádějte interaktivní analýzy dat pomocí Sparku v HDInsight pomocí nástrojů BI](apache-spark-use-bi-tools.md)
* [Spark s Machine Learning: Používejte Spark v HDInsight pro analýzu teploty v budově pomocí dat HVAC](apache-spark-ipython-notebook-machine-learning.md)
* [Spark s Machine Learning: Používejte Spark v HDInsight k předpovědím výsledků kontrol potravin](apache-spark-machine-learning-mllib-ipython.md)
* [Analýza protokolu webu pomocí Sparku v HDInsight](apache-spark-custom-library-website-log-analysis.md)

Informace o vytváření a spouštění aplikací Spark najdete v následujících dokumentech:

* [Vytvoření samostatné aplikace pomocí Scala](apache-spark-create-standalone-application.md)
* [Vzdálené spouštění úloh na clusteru Sparku pomocí Livy](apache-spark-livy-rest-interface.md)
