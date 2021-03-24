---
title: Analýza protokolů Application Insight pomocí Sparku – Azure HDInsight
description: Naučte se exportovat protokoly Application Insights do úložiště objektů BLOB a pak analyzovat protokoly pomocí Sparku v HDInsight.
ms.service: hdinsight
ms.topic: how-to
ms.custom: hdinsightactive
ms.date: 12/17/2019
ms.openlocfilehash: 9ae06565039e53c0096ff0541f7b28a5b041d9e8
ms.sourcegitcommit: 42e4f986ccd4090581a059969b74c461b70bcac0
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/23/2021
ms.locfileid: "104864546"
---
# <a name="analyze-application-insights-telemetry-logs-with-apache-spark-on-hdinsight"></a>Analýza protokolů telemetrie Application Insights s využitím Apache Spark ve službě HDInsight

Naučte se, jak pomocí [Apache Spark](https://spark.apache.org/) v HDInsight analyzovat data z telemetrie Application Insight.

[Visual Studio Application Insights](../../azure-monitor/app/app-insights-overview.md) je analytická služba, která monitoruje vaše webové aplikace. Data telemetrie generovaná pomocí Application Insights lze exportovat do Azure Storage. Jakmile jsou data v Azure Storage, HDInsight je možné použít k její analýze.

## <a name="prerequisites"></a>Předpoklady

* Aplikace, která je nakonfigurována pro použití Application Insights.

* Znalost vytváření clusteru HDInsight se systémem Linux. Další informace najdete v tématu [vytvoření Apache Spark ve službě HDInsight](apache-spark-jupyter-spark-sql.md).

* Webový prohlížeč.

Při vývoji a testování tohoto dokumentu byly použity následující prostředky:

* Data telemetrie Application Insights byla vygenerována pomocí [Node.js webové aplikace nakonfigurované pro použití Application Insights](../../azure-monitor/app/nodejs.md).

* Pro analýzu dat byl použit systém Linux Spark v clusteru HDInsight verze 3,5.

## <a name="architecture-and-planning"></a>Architektura a plánování

Následující diagram znázorňuje architekturu služby tohoto příkladu:

:::image type="content" source="./media/apache-spark-analyze-application-insight-logs/application-insights.png" alt-text="Tok dat z Application Insights do úložiště objektů blob, Spark" border="false":::

### <a name="azure-storage"></a>Azure Storage

Application Insights lze nakonfigurovat tak, aby průběžně exportovali informace telemetrie do objektů BLOB. HDInsight pak může číst data uložená v objektech blob. Existují však některé požadavky, které je třeba provést:

* **Umístění**: Pokud je účet úložiště a HDInsight v různých umístěních, může dojít ke zvýšení latence. Zvyšuje také náklady, protože poplatky za výstupní data se pohybují mezi oblastmi.

    > [!WARNING]  
    > Použití účtu úložiště v jiném umístění než HDInsight se nepodporuje.

* **Typ objektu BLOB**: HDInsight podporuje jenom objekty blob bloku. Application Insights používá výchozí hodnotu pro použití objektů blob bloku, takže by měl ve výchozím nastavení fungovat v HDInsight.

Informace o přidání úložiště do existujícího clusteru najdete v dokumentu [Přidání dalších účtů úložiště](../hdinsight-hadoop-add-storage.md) .

### <a name="data-schema"></a>Schéma dat

Application Insights poskytuje informace o [modelu exportu dat](../../azure-monitor/app/export-data-model.md) pro formát dat telemetrie exportovaný do objektů BLOB. Kroky v tomto dokumentu používají Spark SQL pro práci s daty. Spark SQL může automaticky generovat schéma pro strukturu dat JSON přihlášenou Application Insights.

## <a name="export-telemetry-data"></a>Exportovat data telemetrie

Postupujte podle kroků v části [Konfigurace průběžného exportu](../../azure-monitor/app/export-telemetry.md) a nakonfigurujte Application Insights k exportu informací o telemetrie do objektu blob Azure Storage.

## <a name="configure-hdinsight-to-access-the-data"></a>Konfigurace HDInsight pro přístup k datům

Pokud vytváříte cluster HDInsight, přidejte během vytváření clusteru účet úložiště.

Pokud chcete účet Azure Storage přidat do existujícího clusteru, použijte informace v dokumentu [Přidání dalších účtů úložiště](../hdinsight-hadoop-add-storage.md) .

## <a name="analyze-the-data-pyspark"></a>Analyzovat data: PySpark

1. Ve webovém prohlížeči přejděte na místo, `https://CLUSTERNAME.azurehdinsight.net/jupyter` kde název_clusteru je název vašeho clusteru.

2. V pravém horním rohu stránky Jupyter vyberte **Nový** a pak **PySpark**. Otevře se nová karta prohlížeče obsahující Jupyter Notebook založenou na Pythonu.

3. Do prvního pole (označovaného jako **buňka**) na stránce zadejte následující text:

   ```python
   sc._jsc.hadoopConfiguration().set('mapreduce.input.fileinputformat.input.dir.recursive', 'true')
   ```

    Tento kód nakonfiguruje Spark k rekurzivnímu přístupu ke struktuře adresářů pro vstupní data. Application Insights telemetrie se do adresářové struktury přihlásí podobně jako `/{telemetry type}/YYYY-MM-DD/{##}/` .

4. Kód spustíte **stisknutím SHIFT + ENTER** . Na levé straně buňky \* se zobrazí znak "" v závorkách, který označuje, že kód v této buňce je právě spuštěn. Po dokončení se \* text změní na číslo a výstup podobný následujícímu textu se zobrazí pod buňkou:

    ```output
    Creating SparkContext as 'sc'

    ID    YARN Application ID    Kind    State    Spark UI    Driver log    Current session?
    3    application_1468969497124_0001    pyspark    idle    Link    Link    ✔

    Creating HiveContext as 'sqlContext'
    SparkContext and HiveContext created. Executing user code ...
    ```

5. Nová buňka se vytvoří pod první. Do nové buňky zadejte následující text. Nahraďte `CONTAINER` a názvem `STORAGEACCOUNT` účtu Azure Storage a názvem kontejneru objektů blob, který obsahuje Application Insights data.

   ```python
   %%bash
   hdfs dfs -ls wasbs://CONTAINER@STORAGEACCOUNT.blob.core.windows.net/
   ```

    Tuto buňku spustíte stisknutím **SHIFT + ENTER** . Zobrazí se výsledek podobný následujícímu textu:

    ```output
    Found 1 items
    drwxrwxrwx   -          0 1970-01-01 00:00 wasbs://appinsights@contosostore.blob.core.windows.net/contosoappinsights_2bededa61bc741fbdee6b556571a4831
    ```

    Vrácená cesta wasbs je umístění dat telemetrie Application Insights. Změňte `hdfs dfs -ls` řádek v buňce, abyste použili vrácenou cestu wasbs a pak stisknutím **klávesy SHIFT + ENTER** znovu spusťte tuto buňku. Tentokrát by se měly zobrazit adresáře, které obsahují data telemetrie.

   > [!NOTE]  
   > V případě zbývajících kroků v této části se `wasbs://appinsights@contosostore.blob.core.windows.net/contosoappinsights_{ID}/Requests` použil adresář. Vaše adresářová struktura se může lišit.

6. Do další buňky zadejte následující kód: Nahraďte cestou k `WASB_PATH` předchozímu kroku.

   ```python
   jsonFiles = sc.textFile('WASB_PATH')
   jsonData = sqlContext.read.json(jsonFiles)
   ```

    Tento kód vytvoří datový rámec ze souborů JSON exportovaných procesem průběžného exportu. Tuto buňku spustíte stisknutím **SHIFT + ENTER** .
7. V další buňce zadejte a spusťte následující příkaz pro zobrazení schématu, které Spark vytvořil pro soubory JSON:

   ```python
   jsonData.printSchema()
   ```

    Schéma pro každý typ telemetrie se liší. Následující příklad je schéma, které je vygenerováno pro webové požadavky (data uložená v `Requests` podadresáři):

    ```output
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
    ```

8. Pomocí následujícího příkazu zaregistrujte datový rámec jako dočasnou tabulku a spusťte dotaz na data:

   ```python
   jsonData.registerTempTable("requests")
   df = sqlContext.sql("select context.location.city from requests where context.location.city is not null")
   df.show()
   ```

    Tento dotaz vrátí informace o městě pro prvních 20 záznamů, kde Context. Location. City není null.

   > [!NOTE]  
   > Struktura kontextu je přítomna ve všech telemetrii protokolovaných Application Insights. Do protokolů se nesmí naplnit element City. Použijte schéma k identifikaci dalších prvků, které můžete použít k dotazování, které mohou obsahovat data pro vaše protokoly.

    Tento dotaz vrátí informace podobné následujícímu textu:

    ```output
    +---------+
    |     city|
    +---------+
    | Bellevue|
    |  Redmond|
    |  Seattle|
    |Charlotte|
    ...
    +---------+
    ```

## <a name="analyze-the-data-scala"></a>Analyzovat data: Scala

1. Ve webovém prohlížeči přejděte na místo, `https://CLUSTERNAME.azurehdinsight.net/jupyter` kde název_clusteru je název vašeho clusteru.

2. V pravém horním rohu stránky Jupyter vyberte **Nový** a pak **Scala**. Zobrazí se nová karta prohlížeče obsahující Jupyter Notebook na základě Scala.

3. Do prvního pole (označovaného jako **buňka**) na stránce zadejte následující text:

   ```scala
   sc.hadoopConfiguration.set("mapreduce.input.fileinputformat.input.dir.recursive", "true")
   ```

    Tento kód nakonfiguruje Spark k rekurzivnímu přístupu ke struktuře adresářů pro vstupní data. Application Insights telemetrie se do adresářové struktury přihlásí podobně jako `/{telemetry type}/YYYY-MM-DD/{##}/` .

4. Kód spustíte **stisknutím SHIFT + ENTER** . Na levé straně buňky \* se zobrazí znak "" v závorkách, který označuje, že kód v této buňce je právě spuštěn. Po dokončení se \* text změní na číslo a výstup podobný následujícímu textu se zobrazí pod buňkou:

    ```output
    Creating SparkContext as 'sc'

    ID    YARN Application ID    Kind    State    Spark UI    Driver log    Current session?
    3    application_1468969497124_0001    spark    idle    Link    Link    ✔

    Creating HiveContext as 'sqlContext'
    SparkContext and HiveContext created. Executing user code ...
    ```

5. Nová buňka se vytvoří pod první. Do nové buňky zadejte následující text. Nahraďte `CONTAINER` a názvem `STORAGEACCOUNT` účtu Azure Storage a názvem kontejneru objektů blob, který obsahuje protokoly Application Insights.

   ```scala
   %%bash
   hdfs dfs -ls wasbs://CONTAINER@STORAGEACCOUNT.blob.core.windows.net/
   ```

    Tuto buňku spustíte stisknutím **SHIFT + ENTER** . Zobrazí se výsledek podobný následujícímu textu:

    ```output
    Found 1 items
    drwxrwxrwx   -          0 1970-01-01 00:00 wasbs://appinsights@contosostore.blob.core.windows.net/contosoappinsights_2bededa61bc741fbdee6b556571a4831
    ```

    Vrácená cesta wasbs je umístění dat telemetrie Application Insights. Změňte `hdfs dfs -ls` řádek v buňce, abyste použili vrácenou cestu wasbs a pak stisknutím **klávesy SHIFT + ENTER** znovu spusťte tuto buňku. Tentokrát by se měly zobrazit adresáře, které obsahují data telemetrie.

   > [!NOTE]  
   > V případě zbývajících kroků v této části se `wasbs://appinsights@contosostore.blob.core.windows.net/contosoappinsights_{ID}/Requests` použil adresář. Tento adresář možná neexistuje, pokud nejsou data telemetrie pro webovou aplikaci.

6. Do další buňky zadejte následující kód: Nahraďte cestou k `WASB\_PATH` předchozímu kroku.

   ```scala
   var jsonFiles = sc.textFile('WASB_PATH')
   val sqlContext = new org.apache.spark.sql.SQLContext(sc)
   var jsonData = sqlContext.read.json(jsonFiles)
   ```

    Tento kód vytvoří datový rámec ze souborů JSON exportovaných procesem průběžného exportu. Tuto buňku spustíte stisknutím **SHIFT + ENTER** .

7. V další buňce zadejte a spusťte následující příkaz pro zobrazení schématu, které Spark vytvořil pro soubory JSON:

   ```scala
   jsonData.printSchema
   ```

    Schéma pro každý typ telemetrie se liší. Následující příklad je schéma, které je vygenerováno pro webové požadavky (data uložená v `Requests` podadresáři):

    ```output
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
    ```

8. Pomocí následujícího příkazu zaregistrujte datový rámec jako dočasnou tabulku a spusťte dotaz na data:

   ```scala
   jsonData.registerTempTable("requests")
   var city = sqlContext.sql("select context.location.city from requests where context.location.city isn't null limit 10").show()
   ```

    Tento dotaz vrátí informace o městě pro prvních 20 záznamů, kde Context. Location. City není null.

   > [!NOTE]  
   > Struktura kontextu je přítomna ve všech telemetrii protokolovaných Application Insights. Do protokolů se nesmí naplnit element City. Použijte schéma k identifikaci dalších prvků, které můžete použít k dotazování, které mohou obsahovat data pro vaše protokoly.

    Tento dotaz vrátí informace podobné následujícímu textu:

    ```output
    +---------+
    |     city|
    +---------+
    | Bellevue|
    |  Redmond|
    |  Seattle|
    |Charlotte|
    ...
    +---------+
    ```

## <a name="next-steps"></a>Další kroky

Další příklady použití Apache Spark pro práci s daty a službami v Azure najdete v následujících dokumentech:

* [Apache Spark s BI: provádějte interaktivní analýzy dat pomocí Sparku v HDInsight pomocí nástrojů BI.](apache-spark-use-bi-tools.md)
* [Apache Spark s Machine Learning: pomocí Sparku v HDInsight můžete analyzovat teplotu budovy pomocí dat TVK.](apache-spark-ipython-notebook-machine-learning.md)
* [Apache Spark s Machine Learning: pomocí Sparku v HDInsight předpovídat výsledky kontroly potravin](apache-spark-machine-learning-mllib-ipython.md)
* [Analýza webového protokolu pomocí Apache Spark ve službě HDInsight](apache-spark-custom-library-website-log-analysis.md)

Informace o vytváření a spouštění aplikací Spark najdete v následujících dokumentech:

* [Vytvoření samostatné aplikace pomocí Scala](apache-spark-create-standalone-application.md)
* [Vzdálené spouštění úloh na clusteru Apache Spark pomocí Livy](apache-spark-livy-rest-interface.md)
