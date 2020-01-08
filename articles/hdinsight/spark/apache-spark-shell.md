---
title: Používání interaktivního prostředí Spark v Azure HDInsight
description: Interaktivní prostředí Spark nabízí proces čtení a spouštění pro spouštění příkazů Spark po jednom a prohlížení výsledků.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.custom: hdinsightactive
ms.date: 12/12/2019
ms.openlocfilehash: f088b8210b8170d22e84d131f0a72f5f8caa3b92
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/25/2019
ms.locfileid: "75435224"
---
# <a name="run-apache-spark-from-the-spark-shell"></a>Spuštění Apache Spark z prostředí Spark

Interaktivní [Apache Spark](https://spark.apache.org/) shell poskytuje prostředí REPL (čtení – spouštění-tisk) pro spouštění příkazů Spark po jednom a prohlížení výsledků. Tento proces je užitečný pro vývoj a ladění. Spark poskytuje jedno prostředí pro každý z podporovaných jazyků: Scala, Python a R.

## <a name="run-an-apache-spark-shell"></a>Spuštění prostředí Apache Spark

1. Připojte se ke clusteru pomocí [příkazu SSH](../hdinsight-hadoop-linux-use-ssh-unix.md) . Níže uvedený příkaz upravte tak, že ho nahradíte názvem clusteru a pak zadáte tento příkaz:

    ```cmd
    ssh sshuser@CLUSTERNAME-ssh.azurehdinsight.net
    ```

1. Spark nabízí prostředí pro Scala (Spark-Shell) a Python (pyspark). V relaci SSH zadejte jeden z následujících příkazů:

    ```bash
    spark-shell
    pyspark
    ```

    Nyní můžete zadat příkazy Spark v příslušném jazyce.

1. Několik základních ukázkových příkazů:

    ```scala
    // Load data
    var data = spark.read.format("csv").option("header", "true").option("inferSchema", "true").load("/HdiSamples/HdiSamples/SensorSampleData/building/building.csv")

    // Show data
    data.show()

    // Select certain columns
    data.select($"BuildingID", $"Country").show(10)

    // exit shell
    :q
    ```

## <a name="sparksession-and-sparkcontext-instances"></a>Instance SparkSession a SparkContext

Ve výchozím nastavení platí, že když spustíte prostředí Spark, instance SparkSession a SparkContext se automaticky vytvoří.

Pokud chcete získat přístup k instanci SparkSession, zadejte `spark`. Pokud chcete získat přístup k instanci SparkContext, zadejte `sc`.

## <a name="important-shell-parameters"></a>Důležité parametry prostředí

Příkaz Spark Shell (`spark-shell`nebo `pyspark`) podporuje mnoho parametrů příkazového řádku. Pokud chcete zobrazit úplný seznam parametrů, spusťte prostředí Spark s přepínačem `--help`. Některé z těchto parametrů se můžou vztahovat jenom na `spark-submit`, které se v prostředí Spark zalomí.

| – přepínač | description | Příklad |
| --- | --- | --- |
| --hlavní MASTER_URL | Určuje hlavní adresu URL. Ve službě HDInsight je tato hodnota vždycky `yarn`. | `--master yarn`|
| --JAR JAR_LIST | Seznam místních jar oddělený čárkami, který se má zahrnout do ovladačů a prováděcích tříd V HDInsight se tento seznam skládá z cest k výchozímu systému souborů ve Azure Storage nebo Data Lake Storage. | `--jars /path/to/examples.jar` |
| --balíčky MAVEN_COORDS | Čárkami oddělený seznam souřadnic mavenů jar, které se mají zahrnout do ovladače a prováděcích tříd Vyhledá místní úložiště Maven a potom Maven střed a všechna další vzdálená úložiště, která jsou určená `--repositories`. Formát souřadnic je *GroupID*:*artifactId*:*Version*. | `--packages "com.microsoft.azure:azure-eventhubs:0.14.0"`|
| --py-seznam souborů | Jenom pro Python se seznam souborů. zip,. vaječný nebo. py oddělený čárkami, které se mají umístit na PYTHONPATH. | `--pyfiles "samples.py"` |

## <a name="next-steps"></a>Další kroky

- Přehled najdete v tématu [Úvod do Apache Spark ve službě Azure HDInsight](apache-spark-overview.md) .
- Pokud chcete pracovat s clustery Spark a SparkSQL, přečtěte si téma [Vytvoření clusteru Apache Spark ve službě Azure HDInsight](apache-spark-jupyter-spark-sql.md) .
- Podívejte [se, co je Apache Spark strukturované streamování?](apache-spark-streaming-overview.md) pro psaní aplikací, které zpracovávají streamovaná data pomocí Sparku.
