---
title: Používání interaktivního prostředí Spark v Azure HDInsight
description: Interaktivní prostředí Spark nabízí proces čtení a spouštění pro spouštění příkazů Spark po jednom a prohlížení výsledků.
ms.service: hdinsight
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 01/09/2018
ms.openlocfilehash: 7aac2812787a7c14d99377754a4f85e699ef3f09
ms.sourcegitcommit: a874064e903f845d755abffdb5eac4868b390de7
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/24/2019
ms.locfileid: "68441895"
---
# <a name="run-apache-spark-from-the-spark-shell"></a>Spuštění Apache Spark z prostředí Spark

Interaktivní [Apache Spark](https://spark.apache.org/) shell poskytuje prostředí REPL (čtení – spouštění-tisk) pro spouštění příkazů Spark po jednom a prohlížení výsledků. Tento proces je užitečný pro vývoj a ladění. Spark poskytuje jedno prostředí pro každý z podporovaných jazyků: Scala, Python a R.

## <a name="get-to-an-apache-spark-shell-with-ssh"></a>Získat Apache Spark prostředí pomocí SSH

Přístup k prostředí Apache Spark v HDInsight připojením k primárnímu hlavnímu uzlu clusteru pomocí SSH:

     ssh <sshusername>@<clustername>-ssh.azurehdinsight.net

Úplný příkaz SSH pro váš cluster můžete získat z Azure Portal:

1. Přihlaste se k webu [Azure Portal](https://portal.azure.com).
2. Přejděte do podokna pro cluster HDInsight Spark.
3. Vyberte Secure Shell (SSH).

    ![Podokno HDInsight v Azure Portal](./media/apache-spark-shell/hdinsight-spark-blade.png)

4. Zkopírujte zobrazený příkaz SSH a spusťte ho v terminálu.

    ![Podokno SSH protokolu HDInsight v Azure Portal](./media/apache-spark-shell/hdinsight-spark-ssh-blade.png)

Podrobnosti o použití SSH pro připojení ke službě HDInsight najdete v tématu [Použití SSH se službou HDInsight](../hdinsight-hadoop-linux-use-ssh-unix.md).

## <a name="run-an-apache-spark-shell"></a>Spuštění prostředí Apache Spark

Spark nabízí prostředí pro Scala (Spark-Shell), Python (pyspark) a R (Spark). V relaci SSH v hlavním uzlu clusteru HDInsight zadejte jeden z následujících příkazů:

    ./bin/spark-shell
    ./bin/pyspark
    ./bin/sparkR

Nyní můžete zadat příkazy Spark v příslušném jazyce.

## <a name="sparksession-and-sparkcontext-instances"></a>Instance SparkSession a SparkContext

Ve výchozím nastavení platí, že když spustíte prostředí Spark, instance SparkSession a SparkContext se automaticky vytvoří.

Pokud chcete získat přístup k instanci SparkSession `spark`, zadejte. Pokud chcete získat přístup k instanci SparkContext `sc`, zadejte.

## <a name="important-shell-parameters"></a>Důležité parametry prostředí

Příkaz Spark Shell (`spark-shell`, `pyspark`nebo `sparkR`) podporuje mnoho parametrů příkazového řádku. Pokud chcete zobrazit úplný seznam parametrů, spusťte prostředí Spark s přepínačem `--help`. Všimněte si, že některé z těchto parametrů se mohou `spark-submit`vztahovat pouze na, které prostředí Spark balí.

| – přepínač | description | Příklad |
| --- | --- | --- |
| --Master MASTER_URL | Určuje hlavní adresu URL. Ve službě HDInsight je tato hodnota vždy `yarn`. | `--master yarn`|
| --JAR JAR_LIST | Seznam místních jar oddělený čárkami, který se má zahrnout do ovladačů a prováděcích tříd V HDInsight se tento seznam skládá z cest k výchozímu systému souborů ve Azure Storage nebo Data Lake Storage. | `--jars /path/to/examples.jar` |
| --balíčky MAVEN_COORDS | Čárkami oddělený seznam souřadnic mavenů jar, které se mají zahrnout do ovladače a prováděcích tříd Vyhledá místní úložiště Maven a potom Maven střed a pak všechna další vzdálená úložiště, která jsou `--repositories`určená pomocí. Formát souřadnic je *GroupID*:*artifactId*:*Version*. | `--packages "com.microsoft.azure:azure-eventhubs:0.14.0"`|
| --py-seznam souborů | Jenom pro Python se seznam souborů. zip,. vaječný nebo. py oddělený čárkami, které se mají umístit na PYTHONPATH. | `--pyfiles "samples.py"` |

## <a name="next-steps"></a>Další postup

- Přehled najdete v tématu [Úvod do Apache Spark ve službě Azure HDInsight](apache-spark-overview.md) .
- Pokud chcete pracovat s clustery Spark a SparkSQL, přečtěte si téma [Vytvoření clusteru Apache Spark ve službě Azure HDInsight](apache-spark-jupyter-spark-sql.md) .
- Podívejte [se, co je Apache Spark strukturované streamování?](apache-spark-streaming-overview.md) pro psaní aplikací, které zpracovávají streamovaná data pomocí Sparku.
