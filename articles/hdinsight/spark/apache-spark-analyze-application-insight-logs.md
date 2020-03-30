---
title: Analýza protokolů Application Insight pomocí Spark – Azure HDInsight
description: Zjistěte, jak exportovat protokoly Application Insight do úložiště objektů blob a pak je analyzovat pomocí Spark na HDInsightu.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.custom: hdinsightactive
ms.date: 12/17/2019
ms.openlocfilehash: 6fd7682f56fbe446904a4acdb39e78525f2523a8
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "75435239"
---
# <a name="analyze-application-insights-telemetry-logs-with-apache-spark-on-hdinsight"></a>Analýza telemetrických protokolů Application Insights pomocí Apache Spark na HDInsightu

Přečtěte si, jak pomocí [Apache Spark](https://spark.apache.org/) na HDInsight analyzovat telemetrická data Application Insight.

[Visual Studio Application Insights](../../azure-monitor/app/app-insights-overview.md) je analytická služba, která monitoruje vaše webové aplikace. Telemetrická data generovaná pomocí Application Insights se dají exportovat do Azure Storage. Jakmile jsou data ve službě Azure Storage, HDInsight se dá použít k jejich analýze.

## <a name="prerequisites"></a>Požadavky

* Aplikace, která je nakonfigurována pro použití Application Insights.

* Seznamte se s vytvářením clusteru HDInsight založeného na Linuxu. Další informace najdete [v tématu Vytvoření Apache Spark na HDInsight](apache-spark-jupyter-spark-sql.md).

* Webový prohlížeč.

Při vývoji a testování tohoto dokumentu byly použity následující zdroje:

* Telemetrická data Application Insights byla generována pomocí [webové aplikace Node.js nakonfigurované pro použití Application Insights](../../azure-monitor/app/nodejs.md).

* K analýze dat se používala Spark na linuxovém panelu 3.5 verze 3.5.

## <a name="architecture-and-planning"></a>Architektura a plánování

Následující diagram znázorňuje architekturu služeb v tomto příkladu:

![Data plynoucí z Application Insights do úložiště objektů blob, pak Spark](./media/apache-spark-analyze-application-insight-logs/application-insights.png)

### <a name="azure-storage"></a>Azure Storage

Application Insights lze nakonfigurovat tak, aby průběžně exportovat telemetrické informace do objektů BLOB. HDInsight pak můžete číst data uložená v objektech BLOB. Existují však některé požadavky, které musíte dodržovat:

* **Umístění:** Pokud účet úložiště a HDInsight jsou v různých umístěních, může zvýšit latenci. Zvyšuje také náklady, protože poplatky za odchozí přenos se vztahují na data pohybující se mezi oblastmi.

    > [!WARNING]  
    > Použití účtu úložiště v jiném umístění než HDInsight není podporováno.

* **Typ objektu blob**: HDInsight podporuje pouze objekty BLOB bloku. Application Insights výchozí použití objektů BLOB bloku, takže by měl fungovat ve výchozím nastavení s HDInsight.

Informace o přidání úložiště do existujícího clusteru najdete v dokumentu [Přidat další účty úložiště.](../hdinsight-hadoop-add-storage.md)

### <a name="data-schema"></a>Schéma dat

Application Insights poskytuje informace o [exportu datového modelu](../../azure-monitor/app/export-data-model.md) pro formát telemetrických dat exportovaný do objektů BLOB. Kroky v tomto dokumentu používají Spark SQL pro práci s daty. Spark SQL může automaticky generovat schéma pro datovou strukturu JSON protokolovanou aplikací Insights.

## <a name="export-telemetry-data"></a>Export telemetrických dat

Postupujte podle pokynů v [části Konfigurace průběžného exportu](../../azure-monitor/app/export-telemetry.md) a nakonfigurujte přehledy aplikací pro export informací o telemetrii do objektu blob úložiště Azure.

## <a name="configure-hdinsight-to-access-the-data"></a>Konfigurace hdinsightu pro přístup k datům

Pokud vytváříte cluster HDInsight, přidejte účet úložiště během vytváření clusteru.

Pokud chcete přidat účet úložiště Azure do existujícího clusteru, použijte informace v dokumentu [Přidat další účty úložiště.](../hdinsight-hadoop-add-storage.md)

## <a name="analyze-the-data-pyspark"></a>Analýza dat: PySpark

1. Z webového prohlížeče `https://CLUSTERNAME.azurehdinsight.net/jupyter` přejděte na místo, kde cluster CLUSTERNAME je název clusteru.

2. V pravém horním rohu stránky Jupyter vyberte **Nový**a potom **PySpark**. Otevře se nová karta prohlížeče obsahující poznámkový blok Jupyter založený na Pythonu.

3. Do prvního pole (nazývaného **buňka)** na stránce zadejte následující text:

   ```python
   sc._jsc.hadoopConfiguration().set('mapreduce.input.fileinputformat.input.dir.recursive', 'true')
   ```

    Tento kód konfiguruje Spark pro rekurzivní přístup ke struktuře adresáře pro vstupní data. Telemetrie Application Insights je zaznamenána do `/{telemetry type}/YYYY-MM-DD/{##}/`adresářové struktury podobné .

4. Kód spusťte pomocí **kláves SHIFT+ENTER.** Na levé straně buňky se\*mezi závorkami zobrazí " " označující, že kód v této buňce je prováděn. Po dokončení se pod\*buňkou zobrazí " ' změny na číslo a výstup podobný následujícímu textu:

        Creating SparkContext as 'sc'

        ID    YARN Application ID    Kind    State    Spark UI    Driver log    Current session?
        3    application_1468969497124_0001    pyspark    idle    Link    Link    ✔

        Creating HiveContext as 'sqlContext'
        SparkContext and HiveContext created. Executing user code ...

5. Pod první buňkou se vytvoří nová buňka. Do nové buňky zadejte následující text. Nahraďte `CONTAINER` a `STORAGEACCOUNT` s názvem účtu Azure Storage a název kontejneru objektů blob, který obsahuje data Application Insights.

   ```python
   %%bash
   hdfs dfs -ls wasbs://CONTAINER@STORAGEACCOUNT.blob.core.windows.net/
   ```

    K provedení této buňky použijte **klávesu SHIFT+ENTER.** Zobrazí se výsledek podobný následujícímu textu:

        Found 1 items
        drwxrwxrwx   -          0 1970-01-01 00:00 wasbs://appinsights@contosostore.blob.core.windows.net/contosoappinsights_2bededa61bc741fbdee6b556571a4831

    Vrácená cesta služby wasbs je umístění telemetrických dat Application Insights. Změňte `hdfs dfs -ls` řádek v buňce tak, aby používal vrácenou cestu wasbs, a potom buňku znovu spusťte pomocí **kláves SHIFT+ENTER.** Tentokrát výsledky by měly zobrazit adresáře, které obsahují telemetrická data.

   > [!NOTE]  
   > Pro zbývající kroky v této části `wasbs://appinsights@contosostore.blob.core.windows.net/contosoappinsights_{ID}/Requests` byl adresář použit. Adresářová struktura se může lišit.

6. V další buňce zadejte `WASB_PATH` následující kód: Nahraďte cestou z předchozího kroku.

   ```python
   jsonFiles = sc.textFile('WASB_PATH')
   jsonData = sqlContext.read.json(jsonFiles)
   ```

    Tento kód vytvoří datový rámec ze souborů JSON exportovaných procesem průběžného exportu. Ke spuštění této buňky použijte **klávesu SHIFT+ENTER.**
7. V další buňce zadejte a spusťte následující, chcete-li zobrazit schéma, které Spark vytvořil pro soubory JSON:

   ```python
   jsonData.printSchema()
   ```

    Schéma pro každý typ telemetrie se liší. Následující příklad je schéma, které je generováno pro webové požadavky `Requests` (data uložená v podadresáři):

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

8. Pomocí následujícího příkazu zaregistrujte datový rámec jako dočasnou tabulku a spusťte dotaz na data:

   ```python
   jsonData.registerTempTable("requests")
   df = sqlContext.sql("select context.location.city from requests where context.location.city is not null")
   df.show()
   ```

    Tento dotaz vrátí informace o městě pro 20 nejlepších záznamů, kde context.location.city není null.

   > [!NOTE]  
   > Kontextová struktura je k dispozici ve všech telemetrických protokolovaných Application Insights. Prvek města nemusí být naplněn v protokolech. Pomocí schématu můžete identifikovat další prvky, které mohou dotaz, které mohou obsahovat data pro protokoly.

    Tento dotaz vrátí informace podobné následujícímu textu:

        +---------+
        |     city|
        +---------+
        | Bellevue|
        |  Redmond|
        |  Seattle|
        |Charlotte|
        ...
        +---------+

## <a name="analyze-the-data-scala"></a>Analýza dat: Scala

1. Z webového prohlížeče `https://CLUSTERNAME.azurehdinsight.net/jupyter` přejděte na místo, kde cluster CLUSTERNAME je název clusteru.

2. V pravém horním rohu stránky Jupyter vyberte **Nový**a pak **Scala**. Zobrazí se nová karta prohlížeče obsahující poznámkový blok Jupyter založený na škále.

3. Do prvního pole (nazývaného **buňka)** na stránce zadejte následující text:

   ```scala
   sc.hadoopConfiguration.set("mapreduce.input.fileinputformat.input.dir.recursive", "true")
   ```

    Tento kód konfiguruje Spark pro rekurzivní přístup ke struktuře adresáře pro vstupní data. Telemetrie Application Insights je zaznamenána `/{telemetry type}/YYYY-MM-DD/{##}/`do adresářové struktury podobné .

4. Kód spusťte pomocí **kláves SHIFT+ENTER.** Na levé straně buňky se\*mezi závorkami zobrazí " " označující, že kód v této buňce je prováděn. Po dokončení se pod\*buňkou zobrazí " ' změny na číslo a výstup podobný následujícímu textu:

        Creating SparkContext as 'sc'

        ID    YARN Application ID    Kind    State    Spark UI    Driver log    Current session?
        3    application_1468969497124_0001    spark    idle    Link    Link    ✔

        Creating HiveContext as 'sqlContext'
        SparkContext and HiveContext created. Executing user code ...

5. Pod první buňkou se vytvoří nová buňka. Do nové buňky zadejte následující text. Nahraďte `CONTAINER` a `STORAGEACCOUNT` s názvem účtu Azure Storage a název kontejneru objektů blob, který obsahuje protokoly Application Insights.

   ```scala
   %%bash
   hdfs dfs -ls wasbs://CONTAINER@STORAGEACCOUNT.blob.core.windows.net/
   ```

    K provedení této buňky použijte **klávesu SHIFT+ENTER.** Zobrazí se výsledek podobný následujícímu textu:

        Found 1 items
        drwxrwxrwx   -          0 1970-01-01 00:00 wasbs://appinsights@contosostore.blob.core.windows.net/contosoappinsights_2bededa61bc741fbdee6b556571a4831

    Vrácená cesta služby wasbs je umístění telemetrických dat Application Insights. Změňte `hdfs dfs -ls` řádek v buňce tak, aby používal vrácenou cestu wasbs, a potom buňku znovu spusťte pomocí **kláves SHIFT+ENTER.** Tentokrát výsledky by měly zobrazit adresáře, které obsahují telemetrická data.

   > [!NOTE]  
   > Pro zbývající kroky v této části `wasbs://appinsights@contosostore.blob.core.windows.net/contosoappinsights_{ID}/Requests` byl adresář použit. Tento adresář nemusí existovat, pokud vaše telemetrická data jsou pro webovou aplikaci.

6. V další buňce zadejte `WASB\_PATH` následující kód: Nahraďte cestou z předchozího kroku.

   ```scala
   var jsonFiles = sc.textFile('WASB_PATH')
   val sqlContext = new org.apache.spark.sql.SQLContext(sc)
   var jsonData = sqlContext.read.json(jsonFiles)
   ```

    Tento kód vytvoří datový rámec ze souborů JSON exportovaných procesem průběžného exportu. Ke spuštění této buňky použijte **klávesu SHIFT+ENTER.**

7. V další buňce zadejte a spusťte následující, chcete-li zobrazit schéma, které Spark vytvořil pro soubory JSON:

   ```scala
   jsonData.printSchema
   ```

    Schéma pro každý typ telemetrie se liší. Následující příklad je schéma, které je generováno pro webové požadavky `Requests` (data uložená v podadresáři):

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

8. Pomocí následujícího příkazu zaregistrujte datový rámec jako dočasnou tabulku a spusťte dotaz na data:

   ```scala
   jsonData.registerTempTable("requests")
   var city = sqlContext.sql("select context.location.city from requests where context.location.city isn't null limit 10").show()
   ```

    Tento dotaz vrátí informace o městě pro 20 nejlepších záznamů, kde context.location.city není null.

   > [!NOTE]  
   > Kontextová struktura je k dispozici ve všech telemetrických protokolovaných Application Insights. Prvek města nemusí být naplněn v protokolech. Pomocí schématu můžete identifikovat další prvky, které mohou dotaz, které mohou obsahovat data pro protokoly.

    Tento dotaz vrátí informace podobné následujícímu textu:

        +---------+
        |     city|
        +---------+
        | Bellevue|
        |  Redmond|
        |  Seattle|
        |Charlotte|
        ...
        +---------+

## <a name="next-steps"></a>Další kroky

Další příklady práce s daty a službami v Azure pomocí Apache Spark najdete v následujících dokumentech:

* [Apache Spark s BI: Provádění interaktivní analýzy dat pomocí Spark v HDInsightu pomocí nástrojů BI](apache-spark-use-bi-tools.md)
* [Apache Spark se strojovým učením: Použijte Spark v HDInsightu pro analýzu teploty budovy pomocí dat HVAC](apache-spark-ipython-notebook-machine-learning.md)
* [Apache Spark s machine learningem: Využijte Spark v HDInsightu k předvídání výsledků kontroly potravin](apache-spark-machine-learning-mllib-ipython.md)
* [Analýza protokolu webových stránek pomocí Apache Spark v HDInsight](apache-spark-custom-library-website-log-analysis.md)

Informace o vytváření a spouštění aplikací Spark najdete v následujících dokumentech:

* [Vytvoření samostatné aplikace pomocí Scala](apache-spark-create-standalone-application.md)
* [Spouštění úloh na dálku v clusteru Apache Spark pomocí Livy](apache-spark-livy-rest-interface.md)
