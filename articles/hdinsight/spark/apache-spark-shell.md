---
title: Použití prostředí interaktivní Sparku v Azure HDInsight
description: Interaktivní prostředí Sparku poskytuje proces pro čtení spusťte print pro spouštění Spark příkazy jeden po druhém a výsledky vidíme.
services: hdinsight
ms.service: hdinsight
author: maxluk
ms.author: maxluk
ms.reviewer: jasonh
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 01/09/2018
ms.openlocfilehash: f8c296fddaafb8364cc5e0f07b54d850606322d9
ms.sourcegitcommit: 56d20d444e814800407a955d318a58917e87fe94
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/29/2018
ms.locfileid: "52581421"
---
# <a name="run-apache-spark-from-the-spark-shell"></a>Apache Spark spouštět z prostředí Sparku

Interaktivní [Apache Spark](https://spark.apache.org/) poskytuje prostředí pro spouštění Spark příkazy jeden po druhém a výsledky vidíme dispozici prostředí REPL (čtení spuštění tisk smyčky). Tento proces je užitečný pro vývoj a ladění. Spark poskytuje jeden prostředí pro každý z jeho podporované jazyky: Scala, Python a R.

## <a name="get-to-an-apache-spark-shell-with-ssh"></a>Přístup k prostředí Apache Sparku pomocí protokolu SSH

Připojení k primárnímu hlavnímu uzlu clusteru pomocí SSH pro přístup prostředí Apache Spark v HDInsight:

     ssh <sshusername>@<clustername>-ssh.azurehdinsight.net

Získáte úplný příkaz SSH pro váš cluster na webu Azure Portal:

1. Přihlaste se k webu [Azure Portal](https://portal.azure.com).
2. Přejděte do podokna pro váš cluster HDInsight Spark.
3. Vyberte Secure Shell (SSH).

    ![HDInsight podokně webu Azure Portal](./media/apache-spark-shell/hdinsight-spark-blade.png)

4. Zkopírujte zobrazený příkazu SSH a spusťte v terminálu.

    ![HDInsight SSH podokně webu Azure Portal](./media/apache-spark-shell/hdinsight-spark-ssh-blade.png)

Podrobnosti o použití SSH pro připojení k HDInsight najdete v tématu [použití SSH se službou HDInsight](../hdinsight-hadoop-linux-use-ssh-unix.md).

## <a name="run-an-apache-spark-shell"></a>Spusťte prostředí Apache Sparku

Spark poskytuje prostředí pro Scala (spark-shell), Pythonu (pyspark) a R (sparkR). V relaci SSH na hlavní uzel clusteru HDInsight zadejte jeden z následujících příkazů:

    ./bin/spark-shell
    ./bin/pyspark
    ./bin/sparkR

Nyní můžete zadat příkazy Sparku v příslušném jazyce.

## <a name="sparksession-and-sparkcontext-instances"></a>Instance SparkSession a SparkContext

Ve výchozím nastavení při spuštění prostředí Sparku instance SparkSession a SparkContext jsou automaticky vytvořeny za vás.

Chcete-li získat přístup k instanci SparkSession, zadejte `spark`. Chcete-li získat přístup k instanci SparkContext, zadejte `sc`.

## <a name="important-shell-parameters"></a>Parametry důležité prostředí

Příkaz prostředí Sparku (`spark-shell`, `pyspark`, nebo `sparkR`) podporuje mnoho parametrů příkazového řádku. Pokud chcete zobrazit úplný seznam parametrů, spusťte prostředí Sparku s přepínačem `--help`. Všimněte si, že některé z těchto parametrů může platit pouze pro `spark-submit`, která zabalí prostředí Spark.

| Přepínač | description | Příklad |
| --- | --- | --- |
| --hlavní MASTER_URL | Určuje hlavní adresy URL. V HDInsight, tato hodnota je vždy `yarn`. | `--master yarn`|
| --jars JAR_LIST | Čárkami oddělený seznam místní kromě souborů JAR mají zobrazit na ovladač a prováděcí modul třídám. V HDInsight se tento seznam skládá z cesty na výchozí systém souborů v Azure Storage nebo Azure Data Lake Store. | `--jars /path/to/examples.jar` |
| – balíčky MAVEN_COORDS | Čárkami oddělený seznam souřadnice maven kromě souborů JAR mají zobrazit na ovladač a prováděcí modul třídám. Vyhledá maven místní úložiště a pak centrální maven, pak žádné další vzdálených úložištích zadaným `--repositories`. Formát pro souřadnice *groupId*:*artifactId*:*verze*. | `--packages "com.microsoft.azure:azure-eventhubs:0.14.0"`|
| SEZNAM py – soubory | Pro jazyk Python pouze čárkou oddělený seznam souborů .py, .zip nebo .egg umístit na PYTHONPATH. | `--pyfiles "samples.py"` |

## <a name="next-steps"></a>Další postup

- Zobrazit [Úvod k Apache Sparku v Azure HDInsight](apache-spark-overview.md) Přehled.
- Zobrazit [vytvořit cluster Apache Spark v Azure HDInsight](apache-spark-jupyter-spark-sql.md) pro práci s clustery Spark a SparkSQL.
- Zobrazit [novinky strukturovaného streamu Apache Spark?](apache-spark-streaming-overview.md) pro psaní aplikací, které zpracovávají datové proudy dat pomocí Sparku.

