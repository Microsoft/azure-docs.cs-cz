---
title: Používání interaktivního prostředí Spark v Azure HDInsight
description: Interaktivní prostředí Spark nabízí proces čtení a spouštění pro spouštění příkazů Spark po jednom a prohlížení výsledků.
ms.service: hdinsight
ms.topic: how-to
ms.custom: hdinsightactive
ms.date: 02/10/2020
ms.openlocfilehash: 324852a967b5de015a9b1e9b465d4b4703e573cb
ms.sourcegitcommit: 2f9f306fa5224595fa5f8ec6af498a0df4de08a8
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/28/2021
ms.locfileid: "98929693"
---
# <a name="run-apache-spark-from-the-spark-shell"></a>Spuštění Apache Spark z prostředí Spark

Interaktivní [Apache Spark](https://spark.apache.org/) shell poskytuje prostředí REPL (čtení – spouštění-tisk) pro spouštění příkazů Spark po jednom a prohlížení výsledků. Tento proces je užitečný pro vývoj a ladění. Spark poskytuje jedno prostředí pro každý z podporovaných jazyků: Scala, Python a R.

## <a name="run-an-apache-spark-shell"></a>Spuštění prostředí Apache Spark

1. Připojte se ke clusteru pomocí [příkazu SSH](../hdinsight-hadoop-linux-use-ssh-unix.md) . Níže uvedený příkaz upravte tak, že ho nahradíte názvem clusteru a pak zadáte tento příkaz:

    ```cmd
    ssh sshuser@CLUSTERNAME-ssh.azurehdinsight.net
    ```

1. Spark nabízí prostředí pro Scala (Spark-Shell) a Python (pyspark). V relaci SSH zadejte *jeden* z následujících příkazů:

    ```bash
    spark-shell

    # Optional configurations
    # spark-shell --num-executors 4 --executor-memory 4g --executor-cores 2 --driver-memory 8g --driver-cores 4
    ```

    ```bash
    pyspark

    # Optional configurations
    # pyspark --num-executors 4 --executor-memory 4g --executor-cores 2 --driver-memory 8g --driver-cores 4
    ```

    Pokud máte v úmyslu použít jakoukoli volitelnou konfiguraci, ujistěte se, že jste si před [Apache Spark OutOfMemoryError výjimku](./apache-spark-troubleshoot-outofmemory.md).

1. Několik základních ukázkových příkazů. Vyberte příslušný jazyk:

    ```spark-shell
    val textFile = spark.read.textFile("/example/data/fruits.txt")
    textFile.first()
    textFile.filter(line => line.contains("apple")).show()
    ```

    ```pyspark
    textFile = spark.read.text("/example/data/fruits.txt")
    textFile.first()
    textFile.filter(textFile.value.contains("apple")).show()
    ```

1. Dotazování na soubor CSV Všimněte si, že následující jazyk funguje pro `spark-shell` a `pyspark` .

    ```scala
    spark.read.csv("/HdiSamples/HdiSamples/SensorSampleData/building/building.csv").show()
    ```

1. Dotazování na soubor CSV a uložení výsledků do proměnné:

    ```spark-shell
    var data = spark.read.format("csv").option("header", "true").option("inferSchema", "true").load("/HdiSamples/HdiSamples/SensorSampleData/building/building.csv")
    ```

    ```pyspark
    data = spark.read.format("csv").option("header", "true").option("inferSchema", "true").load("/HdiSamples/HdiSamples/SensorSampleData/building/building.csv")
    ```

1. Výsledky zobrazení:

    ```spark-shell
    data.show()
    data.select($"BuildingID", $"Country").show(10)
    ```

    ```pyspark
    data.show()
    data.select("BuildingID", "Country").show(10)
    ```

1. Ukončit

    ```spark-shell
    :q
    ```

    ```pyspark
    exit()
    ```

## <a name="sparksession-and-sparkcontext-instances"></a>Instance SparkSession a SparkContext

Ve výchozím nastavení platí, že když spustíte prostředí Spark, instance SparkSession a SparkContext se automaticky vytvoří.

Pokud chcete získat přístup k instanci SparkSession, zadejte `spark` . Pokud chcete získat přístup k instanci SparkContext, zadejte `sc` .

## <a name="important-shell-parameters"></a>Důležité parametry prostředí

Příkaz Spark Shell ( `spark-shell` , nebo `pyspark` ) podporuje mnoho parametrů příkazového řádku. Pokud chcete zobrazit úplný seznam parametrů, spusťte prostředí Spark s přepínačem `--help` . Některé z těchto parametrů se můžou vztahovat jenom na `spark-submit` , které prostředí Spark zalomí.

| switch | description | příklad |
| --- | --- | --- |
| --hlavní MASTER_URL | Určuje hlavní adresu URL. Ve službě HDInsight je tato hodnota vždy `yarn` . | `--master yarn`|
| --JAR JAR_LIST | Seznam místních jar oddělený čárkami, který se má zahrnout do ovladačů a prováděcích tříd V HDInsight se tento seznam skládá z cest k výchozímu systému souborů ve Azure Storage nebo Data Lake Storage. | `--jars /path/to/examples.jar` |
| --balíčky MAVEN_COORDS | Čárkami oddělený seznam souřadnic mavenů jar, které se mají zahrnout do ovladače a prováděcích tříd Vyhledá místní úložiště Maven a potom Maven střed a pak všechna další vzdálená úložiště, která jsou určená pomocí `--repositories` . Formát souřadnic je *GroupID*:*artifactId*:*Version*. | `--packages "com.microsoft.azure:azure-eventhubs:0.14.0"`|
| --py-seznam souborů | Jenom pro Python se seznam souborů. zip,. vaječný nebo. py oddělený čárkami, které se mají umístit na PYTHONPATH. | `--pyfiles "samples.py"` |

## <a name="next-steps"></a>Další kroky

- Přehled najdete v tématu [Úvod do Apache Spark ve službě Azure HDInsight](apache-spark-overview.md) .
- Pokud chcete pracovat s clustery Spark a SparkSQL, přečtěte si téma [Vytvoření clusteru Apache Spark ve službě Azure HDInsight](apache-spark-jupyter-spark-sql.md) .
- Podívejte [se, co je Apache Spark strukturované streamování?](apache-spark-streaming-overview.md) pro psaní aplikací, které zpracovávají streamovaná data pomocí Sparku.
