---
title: Analýza protokolů Application Insights pomocí Sparku – Azure HDInsight
description: Zjistěte, jak exportovat protokoly Application Insights do úložiště objektů blob a pak analyzovat protokoly pomocí Spark v HDInsight.
services: hdinsight
author: jasonwhowell
ms.author: jasonh
editor: jasonwhowell
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 05/09/2018
ms.openlocfilehash: 60d837737b1b196ebc83fac4165905218e0f3034
ms.sourcegitcommit: 35ceadc616f09dd3c88377a7f6f4d068e23cceec
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/08/2018
ms.locfileid: "39621959"
---
# <a name="analyze-application-insights-telemetry-logs-with-spark-on-hdinsight"></a>Analýza protokolů telemetrie Application Insights pomocí Sparku v HDInsight

Zjistěte, jak používat Spark v HDInsight k analýze dat telemetrie Application Insights.

[Visual Studio Application Insights](../../application-insights/app-insights-overview.md) je analytická služba, která monitoruje webové aplikace. Telemetrická data generovaná službou Application Insights je možné exportovat do služby Azure Storage. Jakmile jsou data ve službě Azure Storage, HDInsight je možné ho analyzovat.

## <a name="prerequisites"></a>Požadavky

* Aplikace, který je nakonfigurován pro použití služby Application Insights.

* Znalost vytvoření clusteru HDInsight se systémem Linux. Další informace najdete v tématu [vytvořit Spark v HDInsight](apache-spark-jupyter-spark-sql.md).

  > [!IMPORTANT]
  > Kroky v tomto dokumentu vyžadují cluster HDInsight s Linuxem. HDInsight od verze 3.4 výše používá výhradně operační systém Linux. Další informace najdete v tématu [Vyřazení prostředí HDInsight ve Windows](../hdinsight-component-versioning.md#hdinsight-windows-retirement).

* Webový prohlížeč.

Na následujících odkazech byly použity při vývoji a testování v tomto dokumentu:

* Data telemetrie Application Insights se vygeneroval pomocí [webové aplikace Node.js konfigurován pro použití služby Application Insights](../../application-insights/app-insights-nodejs.md).

* K analýze dat byla použita založených na Linuxu Spark ve verzi clusteru HDInsight 3.5.

## <a name="architecture-and-planning"></a>Plánování a architektura

Následující diagram znázorňuje architekturu služby v tomto příkladu:

![Diagram znázorňující dat odesílaných ze služby Application Insights do úložiště objektů blob, pak jsou zpracovávány Spark v HDInsight](./media/apache-spark-analyze-application-insight-logs/appinsightshdinsight.png)

### <a name="azure-storage"></a>Úložiště Azure

Je možné nakonfigurovat Application Insights můžete průběžně exportovat informace o telemetrii do objektů BLOB. HDInsight může pak si můžete přečíst data uložená v objektech BLOB. Existují však některé požadavky, které je nutné postupovat podle:

* **Umístění**: Pokud účet úložiště a HDInsight jsou v různých umístěních, můžou zvýšit latenci. Zvyšuje také úroveň nákladů, jako výchozí přenos dat, poplatky se použijí pro data přesouvaná mezi oblastmi.

    > [!WARNING]
    > Použití účtu úložiště v jiném umístění než HDInsight není podporována.

* **Typ blobu**: HDInsight podporuje jenom objekty BLOB bloku. Application Insights výchozí hodnota je používá objekty BLOB bloku, takže by měla fungovat ve výchozím nastavení s HDInsight.

Informace o přidání úložiště do existujícího clusteru, najdete v článku [přidání dalších účtů úložiště](../hdinsight-hadoop-add-storage.md) dokumentu.

### <a name="data-schema"></a>Schéma dat

Application Insights poskytuje [export datového modelu](../../application-insights/app-insights-export-data-model.md) informace pro formát telemetrická data exportovat do objektů BLOB. Kroky v tomto dokumentu pomocí Spark SQL pro práci s daty. Spark SQL může automaticky generovat schéma pro datová struktura JSON zaznamenaných nástrojem Application Insights.

## <a name="export-telemetry-data"></a>Exportovat data telemetrie

Postupujte podle kroků v [konfiguraci průběžného exportu](../../application-insights/app-insights-export-telemetry.md) Konfigurace informací o aplikacích exportovat informace o telemetrii do objektu blob služby Azure storage.

## <a name="configure-hdinsight-to-access-the-data"></a>Konfigurace HDInsight pro přístup k datům

Při vytváření clusteru služby HDInsight, přidání účtu úložiště při vytváření clusteru.

Pro přidání účtu úložiště Azure do existujícího clusteru, použijte informace v [přidat další účty úložiště](../hdinsight-hadoop-add-storage.md) dokumentu.

## <a name="analyze-the-data-pyspark"></a>Analyzujte data: PySpark

1. Z [webu Azure portal](https://portal.azure.com), vyberte v clusteru HDInsight Spark. Z **rychlé odkazy** vyberte **řídicí panely clusteru**a pak vyberte **Poznámkový blok Jupyter** z části Dashboard__ clusteru.

    ![Řídicí panely clusteru](./media/apache-spark-analyze-application-insight-logs/clusterdashboards.png)

2. V pravém horním rohu stránky Jupyter, vyberte **nový**a potom **PySpark**. Otevře se nová karta prohlížeče obsahující poznámkového bloku Jupyter založenou na jazyce Python.

3. V prvním poli (volá se **buňky**) na stránce, zadejte následující text:

   ```python
   sc._jsc.hadoopConfiguration().set('mapreduce.input.fileinputformat.input.dir.recursive', 'true')
   ```

    Tento kód nakonfiguruje Spark, přístup rekurzivně adresářovou strukturu pro vstupní data. Telemetrie Application Insights je zaznamenána adresářovou strukturu podobně jako `/{telemetry type}/YYYY-MM-DD/{##}/`.

4. Použití **SHIFT + ENTER** spuštění kódu. Na levé straně na buňku "\*" se zobrazí mezi hranaté závorky k označení, že kód v této buňce se zpracovává. Po dokončení "\*" pod buňce se zobrazí změny číslo a výstup podobný následujícímu textu:

        Creating SparkContext as 'sc'

        ID    YARN Application ID    Kind    State    Spark UI    Driver log    Current session?
        3    application_1468969497124_0001    pyspark    idle    Link    Link    ✔

        Creating HiveContext as 'sqlContext'
        SparkContext and HiveContext created. Executing user code ...
5. První z nich je vytvořen novou buňku. Zadejte následující text do nové buňky. Nahraďte `CONTAINER` a `STORAGEACCOUNT` s názvem účtu služby Azure storage a název kontejneru objektů blob, který obsahuje data Application Insights.

   ```python
   %%bash
   hdfs dfs -ls wasb://CONTAINER@STORAGEACCOUNT.blob.core.windows.net/
   ```

    Použití **SHIFT + ENTER** ke spuštění této buňky. Zobrazí se výsledek podobný následujícímu textu:

        Found 1 items
        drwxrwxrwx   -          0 1970-01-01 00:00 wasb://appinsights@contosostore.blob.core.windows.net/contosoappinsights_2bededa61bc741fbdee6b556571a4831

    Cesta wasb vrácená je umístění dat telemetrie Application Insights. Změnit `hdfs dfs -ls` řádek v buňce cesta wasb vrátila a pak pomocí **SHIFT + ENTER** buňku spustit znovu. Tentokrát, výsledky by se zobrazit adresáře, které obsahují telemetrická data.

   > [!NOTE]
   > Pro zbývající kroky v této části `wasb://appinsights@contosostore.blob.core.windows.net/contosoappinsights_{ID}/Requests` adresář byl použit. Strukturu může být jiný.

6. V další buňky, zadejte následující kód: nahradit `WASB_PATH` s cestou z předchozího kroku.

   ```python
   jsonFiles = sc.textFile('WASB_PATH')
   jsonData = sqlContext.read.json(jsonFiles)
   ```

    Tento kód vytvoří datový rámec ze souborů JSON exportované sadou procesu průběžný export. Použití **SHIFT + ENTER** spustit tuto buňku.
7. V další buňky zadejte a spusťte následující příkaz a zobrazit tak schéma, které Spark vytvořené pro soubory JSON:

   ```python
   jsonData.printSchema()
   ```

    Schéma pro každý typ telemetrických dat se liší. Následující příklad je schéma, které se generuje pro webové žádosti (data uložená v `Requests` podadresář):

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
8. Použijte následující postupy k registraci datového rámce jako dočasnou tabulku a spuštění dotazu na data:

   ```python
   jsonData.registerTempTable("requests")
   df = sqlContext.sql("select context.location.city from requests where context.location.city is not null")
   df.show()
   ```

    Tento dotaz vrátí informace o městu pro prvních 20 záznamů, kde context.location.city nemá hodnotu null.

   > [!NOTE]
   > Struktura kontextu je k dispozici ve všech telemetrických dat zaznamenaných nástrojem Application Insights. Element město nemusí vyplní v protokolech. Použití schématu pro identifikaci další prvky, které můžete zadávat dotazy, které mohou obsahovat data pro svoje protokoly.

    Tento dotaz vrátit informace podobné následujícímu textu:

        +---------+
        |     city|
        +---------+
        | Bellevue|
        |  Redmond|
        |  Seattle|
        |Charlotte|
        ...
        +---------+

## <a name="analyze-the-data-scala"></a>Analyzujte data: Scala

1. Z [webu Azure portal](https://portal.azure.com), vyberte v clusteru HDInsight Spark. Z **rychlé odkazy** vyberte **řídicí panely clusteru**a pak vyberte **Poznámkový blok Jupyter** z části Dashboard__ clusteru.

    ![Řídicí panely clusteru](./media/apache-spark-analyze-application-insight-logs/clusterdashboards.png)
2. V pravém horním rohu stránky Jupyter, vyberte **nový**a potom **Scala**. Zobrazí se nová karta prohlížeče obsahující poznámkového bloku Jupyter na základě Scala.
3. V prvním poli (volá se **buňky**) na stránce, zadejte následující text:

   ```scala
   sc.hadoopConfiguration.set("mapreduce.input.fileinputformat.input.dir.recursive", "true")
   ```

    Tento kód nakonfiguruje Spark, přístup rekurzivně adresářovou strukturu pro vstupní data. Telemetrie Application Insights je zaznamenána adresářovou strukturu podobný `/{telemetry type}/YYYY-MM-DD/{##}/`.

4. Použití **SHIFT + ENTER** spuštění kódu. Na levé straně na buňku "\*" se zobrazí mezi hranaté závorky k označení, že kód v této buňce se zpracovává. Po dokončení "\*" pod buňce se zobrazí změny číslo a výstup podobný následujícímu textu:

        Creating SparkContext as 'sc'

        ID    YARN Application ID    Kind    State    Spark UI    Driver log    Current session?
        3    application_1468969497124_0001    spark    idle    Link    Link    ✔

        Creating HiveContext as 'sqlContext'
        SparkContext and HiveContext created. Executing user code ...
5. První z nich je vytvořen novou buňku. Zadejte následující text do nové buňky. Nahraďte `CONTAINER` a `STORAGEACCOUNT` s názvem účtu služby Azure storage a název kontejneru objektů blob, který obsahuje Application Insights protokoly.

   ```scala
   %%bash
   hdfs dfs -ls wasb://CONTAINER@STORAGEACCOUNT.blob.core.windows.net/
   ```

    Použití **SHIFT + ENTER** ke spuštění této buňky. Zobrazí se výsledek podobný následujícímu textu:

        Found 1 items
        drwxrwxrwx   -          0 1970-01-01 00:00 wasb://appinsights@contosostore.blob.core.windows.net/contosoappinsights_2bededa61bc741fbdee6b556571a4831

    Cesta wasb vrácená je umístění dat telemetrie Application Insights. Změnit `hdfs dfs -ls` řádek v buňce cesta wasb vrátila a pak pomocí **SHIFT + ENTER** buňku spustit znovu. Tentokrát, výsledky by se zobrazit adresáře, které obsahují telemetrická data.

   > [!NOTE]
   > Pro zbývající kroky v této části `wasb://appinsights@contosostore.blob.core.windows.net/contosoappinsights_{ID}/Requests` adresář byl použit. Tento adresář neexistuje, není-li vaše telemetrická data pro webovou aplikaci.

6. V další buňky, zadejte následující kód: nahradit `WASB\_PATH` s cestou z předchozího kroku.

   ```scala
   var jsonFiles = sc.textFile('WASB_PATH')
   val sqlContext = new org.apache.spark.sql.SQLContext(sc)
   var jsonData = sqlContext.read.json(jsonFiles)
   ```

    Tento kód vytvoří datový rámec ze souborů JSON exportované sadou procesu průběžný export. Použití **SHIFT + ENTER** spustit tuto buňku.

7. V další buňky zadejte a spusťte následující příkaz a zobrazit tak schéma, které Spark vytvořené pro soubory JSON:

   ```scala
   jsonData.printSchema
   ```

    Schéma pro každý typ telemetrických dat se liší. Následující příklad je schéma, které se generuje pro webové žádosti (data uložená v `Requests` podadresář):

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

8. Použijte následující postupy k registraci datového rámce jako dočasnou tabulku a spuštění dotazu na data:

   ```scala
   jsonData.registerTempTable("requests")
   var city = sqlContext.sql("select context.location.city from requests where context.location.city is not null limit 10").show()
   ```

    Tento dotaz vrátí informace o městu pro prvních 20 záznamů, kde context.location.city nemá hodnotu null.

   > [!NOTE]
   > Struktura kontextu je k dispozici ve všech telemetrických dat zaznamenaných nástrojem Application Insights. Element město nemusí vyplní v protokolech. Použití schématu pro identifikaci další prvky, které můžete zadávat dotazy, které mohou obsahovat data pro svoje protokoly.
   >
   >

    Tento dotaz vrátit informace podobné následujícímu textu:

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

Další příklady použití Sparku pro práci s daty a službami v Azure najdete v následujících dokumentech:

* [Spark s BI: Provádějte interaktivní analýzy dat pomocí Sparku v HDInsight pomocí nástrojů BI](apache-spark-use-bi-tools.md)
* [Spark s Machine Learning: Používejte Spark v HDInsight pro analýzu teploty v budově pomocí dat HVAC](apache-spark-ipython-notebook-machine-learning.md)
* [Spark s Machine Learning: Používejte Spark v HDInsight k předpovědím výsledků kontrol potravin](apache-spark-machine-learning-mllib-ipython.md)
* [Analýza protokolu webu pomocí Sparku v HDInsight](apache-spark-custom-library-website-log-analysis.md)

Informace o vytváření a spouštění aplikací Spark najdete v následujících dokumentech:

* [Vytvoření samostatné aplikace pomocí Scala](apache-spark-create-standalone-application.md)
* [Vzdálené spouštění úloh na clusteru Sparku pomocí Livy](apache-spark-livy-rest-interface.md)
