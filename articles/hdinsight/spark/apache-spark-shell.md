---
title: Používání prostředí interaktivní Spark v Azure HDInsight | Microsoft Docs
description: Interaktivní prostředí Spark probíhá proces pro čtení provést tisk pro spouštění příkazů jeden Spark v čase a zobrazit důsledky.
services: hdinsight
documentationcenter: ''
tags: azure-portal
author: maxluk
manager: jhubbard
editor: cgronlun
ms.assetid: ''
ms.service: hdinsight
ms.custom: hdinsightactive
ms.devlang: na
ms.topic: conceptual
ms.date: 01/09/2018
ms.author: maxluk
ms.openlocfilehash: 1d3d9d228e86df2811b92a0fcaa235f9d8c58d3f
ms.sourcegitcommit: 1362e3d6961bdeaebed7fb342c7b0b34f6f6417a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/18/2018
---
# <a name="run-spark-from-the-spark-shell"></a>Spusťte Spark v prostředí Shell Spark

Interaktivní prostředí Spark poskytuje prostředí REPL (čtení provést tisk smyčky) pro spuštění příkazů jeden Spark v čase a zobrazit důsledky. Tento proces je užitečný pro vývoj a ladění. Spark poskytuje jeden prostředí pro každý z jeho podporovaných jazyků: Scala, Python a R.

## <a name="get-to-a-spark-shell-with-ssh"></a>Přístup k prostředí Spark pomocí protokolu SSH

Přístup k prostředí Spark v HDInsight připojením k primární hlavního uzlu v clusteru pomocí protokolu SSH:

     ssh <sshusername>@<clustername>-ssh.azurehdinsight.net

Dokončení příkazu SSH pro váš cluster můžete získat z portálu Azure:

1. Přihlaste se k portálu [Azure Portal](https://portal.azure.com).
2. Přejděte do podokna pro váš cluster HDInsight Spark.
3. Vyberte Secure Shell (SSH).

    ![HDInsight poddokně na portálu Azure](./media/apache-spark-shell/hdinsight-spark-blade.png)

4. Zkopírujte zobrazené příkazu SSH a spusťte v terminálu.

    ![HDInsight SSH poddokně na portálu Azure](./media/apache-spark-shell/hdinsight-spark-ssh-blade.png)

Podrobnosti o používání SSH pro připojení k HDInsight najdete v tématu [použití SSH s HDInsight](../hdinsight-hadoop-linux-use-ssh-unix.md).

## <a name="run-a-spark-shell"></a>Spusťte prostředí Spark

Spark poskytuje nutný pro Scala (spark prostředí), Python (pyspark) a R (sparkR). V relaci SSH v hlavního uzlu clusteru HDInsight zadejte jednu z následujících příkazů:

    ./bin/spark-shell
    ./bin/pyspark
    ./bin/sparkR

Teď můžete zadat příkazy Spark v příslušný jazyk.

## <a name="sparksession-and-sparkcontext-instances"></a>SparkSession a SparkContext instancí

Ve výchozím nastavení při spuštění prostředí Spark, instance SparkSession a SparkContext jsou automaticky vytvářeny instance za vás.

Chcete-li získat přístup k instanci SparkSession, zadejte `spark`. Chcete-li získat přístup k instanci SparkContext, zadejte `sc`.

## <a name="important-shell-parameters"></a>Parametry důležité prostředí

Příkaz prostředí Spark (`spark-shell`, `pyspark`, nebo `sparkR`) podporuje mnoho parametrů příkazového řádku. Pokud chcete zobrazit úplný seznam parametrů, spusťte prostředí Spark s přepínačem `--help`. Všimněte si, že některé z těchto parametrů může platit pouze pro `spark-submit`, který zabalí prostředí Spark.

| přepínače | description | Příklad |
| --- | --- | --- |
| --hlavní MASTER_URL | Určuje hlavní adresu URL. V prostředí HDInsight, tato hodnota je vždycky `yarn`. | `--master yarn`|
| --jars JAR_LIST | Textový soubor s oddělovači seznam místních souborů JAR zahrnout na ovladače a vykonavatele cesty pro třídy. V prostředí HDInsight tento seznam se skládá z cesty na výchozí systém souborů v Azure Storage nebo Data Lake Store. | `--jars /path/to/examples.jar` |
| – balíčky MAVEN_COORDS | Čárkami oddělený seznam maven souřadnice JAR zahrnout na ovladače a vykonavatele cesty pro třídy. Vyhledá maven místní úložiště, pak střed maven, pak všechny další vzdáleného úložiště zadaný `--repositories`. Formát souřadnice je *groupId*:*artifactId*:*verze*. | `--packages "com.microsoft.azure:azure-eventhubs:0.14.0"`|
| SEZNAM py – soubory | Pro jazyk Python pouze seznam .zip, .egg nebo .py souborů k umístění na PYTHONPATH oddělených čárkami. | `--pyfiles "samples.py"` |

## <a name="next-steps"></a>Další postup

- V tématu [Úvod do Spark v Azure HDInsight](apache-spark-overview.md) Přehled.
- V tématu [vytvářet cluster Apache Spark v Azure HDInsight](apache-spark-jupyter-spark-sql.md) pro práci s clustery Spark a SparkSQL.
- V tématu [novinky strukturované datové proudy Spark?](apache-spark-streaming-overview.md) pro psaní aplikací, které zpracovávají streamování dat pomocí Spark.

