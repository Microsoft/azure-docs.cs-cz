---
title: Použití interaktivního prostředí Spark v Azure HDInsight
description: Interaktivní prostředí Spark poskytuje proces čtení a spuštění tisku pro spouštění příkazů Spark jeden po druhém a zobrazení výsledků.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.custom: hdinsightactive
ms.date: 02/10/2020
ms.openlocfilehash: f8737f645df2aefbf9ce544199f0cc45ce6a3d60
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "77162799"
---
# <a name="run-apache-spark-from-the-spark-shell"></a>Spusťte Apache Spark ze Spark Shellu

Interaktivní [prostředí Apache Spark](https://spark.apache.org/) poskytuje prostředí REPL (smyčka čtení a spuštění a tisku) pro spouštění příkazů Spark jeden po druhém a zobrazení výsledků. Tento proces je užitečný pro vývoj a ladění. Spark poskytuje jeden shell pro každý z podporovaných jazyků: Scala, Python a R.

## <a name="run-an-apache-spark-shell"></a>Spuštění prostředí Apache Spark

1. Pomocí [příkazu ssh](../hdinsight-hadoop-linux-use-ssh-unix.md) se připojte ke clusteru. Upravte níže uvedený příkaz nahrazením názvu clusteru názvem clusteru a zadejte příkaz:

    ```cmd
    ssh sshuser@CLUSTERNAME-ssh.azurehdinsight.net
    ```

1. Spark poskytuje prostředí pro Scala (spark-shell) a Python (pyspark). V relaci SSH zadejte *jeden* z následujících příkazů:

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

    Pokud máte v úmyslu použít libovolnou volitelnou konfiguraci, ujistěte se, že nejprve zkontrolujte [výjimku OutOfMemoryError pro Apache Spark](./apache-spark-troubleshoot-outofmemory.md).

1. Několik základních příkladů příkazů. Vyberte příslušný jazyk:

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

1. Dotaz na soubor CSV. Všimněte si, `spark-shell` že `pyspark`níže uvedený jazyk funguje pro a .

    ```scala
    spark.read.csv("/HdiSamples/HdiSamples/SensorSampleData/building/building.csv").show()
    ```

1. Dotaz na soubor CSV a výsledky úložiště v proměnné:

    ```spark-shell
    var data = spark.read.format("csv").option("header", "true").option("inferSchema", "true").load("/HdiSamples/HdiSamples/SensorSampleData/building/building.csv")
    ```

    ```pyspark
    data = spark.read.format("csv").option("header", "true").option("inferSchema", "true").load("/HdiSamples/HdiSamples/SensorSampleData/building/building.csv")
    ```

1. Zobrazit výsledky:

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

Ve výchozím nastavení při spuštění prostředí Spark, instance SparkSession a SparkContext jsou automaticky instance pro vás.

Chcete-li získat přístup `spark`k instanci SparkSession, zadejte . Chcete-li získat přístup `sc`k instanci SparkContext, zadejte .

## <a name="important-shell-parameters"></a>Důležité parametry skořepiny

Příkaz Prostředí Spark`spark-shell`( `pyspark`, nebo ) podporuje mnoho parametrů příkazového řádku. Chcete-li zobrazit úplný seznam parametrů, spusťte `--help`Prostředí Spark s přepínačem . Některé z těchto parametrů se `spark-submit`mohou vztahovat pouze na , které zalamování prostředí Spark.

| switch | description | příklad |
| --- | --- | --- |
| --mistr MASTER_URL | Určuje adresu URL hlavního serveru. V HDInsight tato hodnota `yarn`je vždy . | `--master yarn`|
| --sklenice JAR_LIST | Seznam místních sklenic oddělených čárkami, které mají být zahrnuty do cest pro třídy ovladače a prováděcího modulu. V HDInsight tento seznam se skládá z cest k výchozímu souborovému systému v Azure Storage nebo Data Lake Storage. | `--jars /path/to/examples.jar` |
| --balíčky MAVEN_COORDS | Čárka-oddělený seznam maven souřadnic sklenic zahrnout na řidiče a vykonavatele třídycesty. Prohledá místní maven repo, pak maven central, pak `--repositories`všechny další vzdálené repozitáře zadané s . Formát souřadnic je *groupId*:*artifactId*:*version*. | `--packages "com.microsoft.azure:azure-eventhubs:0.14.0"`|
| --py-files LIST --py-files LIST --py-files LIST --p | Pouze pro Python seznam souborů .zip, .egg nebo .py oddělených čárkami, který chcete umístit na PYTHONPATH. | `--pyfiles "samples.py"` |

## <a name="next-steps"></a>Další kroky

- Přehled [najdete v článku Úvod do Apache Spark na Azure HDInsight.](apache-spark-overview.md)
- Viz [Vytvoření clusteru Apache Spark ve Službě Azure HDInsight](apache-spark-jupyter-spark-sql.md) pro práci s clustery Spark a SparkSQL.
- Podívejte [se, co je Apache Spark Structured Streaming?](apache-spark-streaming-overview.md)
