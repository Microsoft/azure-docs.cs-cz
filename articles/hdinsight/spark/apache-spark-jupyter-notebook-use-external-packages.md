---
title: Použití vlastních balíčků Maven s Jupyterem ve Sparku – Azure HDInsight
description: Podrobné pokyny, jak nakonfigurovat poznámkové bloky Jupyter dostupné v clusterech HDInsight Spark tak, aby používaly vlastní balíčky Maven.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.custom: hdinsightactive
ms.date: 11/22/2019
ms.openlocfilehash: cec94b2ecb18bc9e8cceb24a21967a3c829d78a5
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "74561737"
---
# <a name="use-external-packages-with-jupyter-notebooks-in-apache-spark-clusters-on-hdinsight"></a>Používejte externí balíčky s notebooky Jupyter v clusterech Apache Spark na HDInsightu

> [!div class="op_single_selector"]
> * [Použití buněčné magie](apache-spark-jupyter-notebook-use-external-packages.md)
> * [Použití akce skriptu](apache-spark-python-package-installation.md)

Přečtěte si, jak nakonfigurovat [poznámkový blok Jupyter](https://jupyter.org/) v clusteru Apache Spark na HDInsightu tak, aby používal externí balíčky Apache **maven,** které nejsou součástí clusteru, a to imavně.

V [úložišti Maven](https://search.maven.org/) můžete vyhledat úplný seznam dostupných balíčků. Můžete také získat seznam dostupných balíčků z jiných zdrojů. Úplný seznam balíčků, do kterých přispěla komunita, je například k dispozici na [sparku Packages](https://spark-packages.org/).

V tomto článku se dozvíte, jak používat [balíček spark-csv](https://search.maven.org/#artifactdetails%7Ccom.databricks%7Cspark-csv_2.10%7C1.4.0%7Cjar) s poznámkovým blokem Jupyter.

## <a name="prerequisites"></a>Požadavky

* Cluster Apache Spark ve službě HDInsight. Pokyny najdete v tématu [Vytváření clusterů Apache Spark ve službě Azure HDInsight](apache-spark-jupyter-spark-sql.md).

* Znalost používání poznámkových bloků Jupyter se Sparkem ve službě HDInsight. Další informace najdete v [tématu Načítání dat a spouštění dotazů pomocí Apache Spark na HDInsight .](./apache-spark-load-data-run-query.md)

* [Schéma URI](../hdinsight-hadoop-linux-information.md#URI-and-scheme) pro primární úložiště clusterů. To by `wasb://` bylo pro `abfs://` Azure Storage, pro `adl://` Azure Data Lake Storage Gen2 nebo pro Azure Data Lake Storage Gen1. Pokud je zabezpečený přenos povolen pro Azure Storage nebo `wasbs://` `abfss://`Data Lake Storage Gen2, identifikátor URI by byl nebo , respektive Viz také [zabezpečený přenos](../../storage/common/storage-require-secure-transfer.md).

## <a name="use-external-packages-with-jupyter-notebooks"></a>Použijte externí balíčky s poznámkovými bloky Jupyter

1. Přejděte `https://CLUSTERNAME.azurehdinsight.net/jupyter` `CLUSTERNAME` na místo, kde je název clusteru Spark.

1. Vytvořte nový poznámkový blok. Vyberte **Nový**a pak vyberte **Spark**.

    ![Vytvoření nového notebooku Spark Jupyter](./media/apache-spark-jupyter-notebook-use-external-packages/hdinsight-spark-create-notebook.png "Vytvoření nového poznámkového bloku Jupyter")

1. Nový poznámkový blok se vytvoří a otevře s názvem Untitled.pynb. Vhorní části vyberte název poznámkového bloku a zadejte popisný název.

    ![Zadání názvu poznámkového bloku](./media/apache-spark-jupyter-notebook-use-external-packages/hdinsight-spark-name-notebook.png "Zadání názvu poznámkového bloku")

1. Kouzlo použijete `%%configure` k konfiguraci poznámkového bloku tak, aby používal externí balíček. V poznámkových blocích, které používají `%%configure` externí balíčky, ujistěte se, že voláte kouzlo v první buňce kódu. Tím je zajištěno, že jádro je nakonfigurováno pro použití balíčku před zahájením relace.

    >[!IMPORTANT]  
    >Pokud zapomenete nakonfigurovat jádro v první buňce, můžete použít `%%configure` parametr with, `-f` ale to restartuje relaci a veškerý pokrok bude ztracen.

    | VERZE HDInsight | Příkaz |
    |-------------------|---------|
    | Pro HDInsight 3.5 a HDInsight 3.6 | `%%configure`<br>`{ "conf": {"spark.jars.packages": "com.databricks:spark-csv_2.11:1.5.0" }}`|
    |Pro HDInsight 3.3 a HDInsight 3.4 | `%%configure` <br>`{ "packages":["com.databricks:spark-csv_2.10:1.4.0"] }`|

1. Výše uvedený fragment očekává maven souřadnice pro externí balíček v Maven Central Repository. V tomto úryvku `com.databricks:spark-csv_2.11:1.5.0` je maven souřadnice pro **spark-csv** balíček. Zde je návod, jak vytvořit souřadnice pro balíček.

    a. Vyhledejte balíček v úložišti Maven. Pro tento článek používáme [spark-csv](https://mvnrepository.com/artifact/com.databricks/spark-csv).

    b. Z úložiště shromážděte hodnoty **groupId**, **ArtifactId**a **Version**. Ujistěte se, že hodnoty, které shromažďujete, odpovídají vašemu clusteru. V tomto případě používáme balíček Scala 2.11 a Spark 1.5.0, ale možná budete muset vybrat různé verze pro příslušnou verzi Scala nebo Spark ve vašem clusteru. Verzi Scaly ve vašem clusteru zjistíte spuštěním `scala.util.Properties.versionString` v jádře Spark Jupyter nebo na Spark submit. Verzi Spark ve vašem clusteru zjistíte spuštěním `sc.version` na poznámkových blocích Jupyter.

    ![Použití externích balíčků s notebookem Jupyter](./media/apache-spark-jupyter-notebook-use-external-packages/use-external-packages-with-jupyter.png "Použití externích balíčků s notebookem Jupyter")

    c. Zřetězit tři hodnoty oddělené dvojtečkou (**:**).

        com.databricks:spark-csv_2.11:1.5.0

1. Spusťte kódovou `%%configure` buňku s magií. Tím nakonfigurujete základní relaci Livy tak, aby používala balíček, který jste zadali. V následujících buňkách v poznámkovém bloku můžete nyní použít balíček, jak je znázorněno níže.

        val df = spark.read.format("com.databricks.spark.csv").
        option("header", "true").
        option("inferSchema", "true").
        load("wasb:///HdiSamples/HdiSamples/SensorSampleData/hvac/HVAC.csv")

    Pro HDInsight 3.4 a níže byste měli použít následující úryvek.

        val df = sqlContext.read.format("com.databricks.spark.csv").
        option("header", "true").
        option("inferSchema", "true").
        load("wasb:///HdiSamples/HdiSamples/SensorSampleData/hvac/HVAC.csv")

1. Potom můžete spustit výstřižky, jak je znázorněno níže, a zobrazit tak data z datového rámce, který jste vytvořili v předchozím kroku.

        df.show()
   
        df.select("Time").count()

## <a name="see-also"></a><a name="seealso"></a>Viz také

* [Přehled: Apache Spark v Azure HDInsight](apache-spark-overview.md)

### <a name="scenarios"></a>Scénáře

* [Apache Spark s BI: Provádění interaktivní analýzy dat pomocí Spark v HDInsightu pomocí nástrojů BI](apache-spark-use-bi-tools.md)
* [Apache Spark se strojovým učením: Použijte Spark v HDInsightu pro analýzu teploty budovy pomocí dat HVAC](apache-spark-ipython-notebook-machine-learning.md)
* [Apache Spark s machine learningem: Využijte Spark v HDInsightu k předvídání výsledků kontroly potravin](apache-spark-machine-learning-mllib-ipython.md)
* [Analýza protokolu webových stránek pomocí Apache Spark v HDInsight](apache-spark-custom-library-website-log-analysis.md)

### <a name="create-and-run-applications"></a>Vytvoření a spouštění aplikací

* [Vytvoření samostatné aplikace pomocí Scala](apache-spark-create-standalone-application.md)
* [Spouštění úloh na dálku v clusteru Apache Spark pomocí Apache Livy](apache-spark-livy-rest-interface.md)

### <a name="tools-and-extensions"></a>Nástroje a rozšíření

* [Používejte externí balíčky pythonu s notebooky Jupyter v clusterech Apache Spark na HDInsight Linuxu](apache-spark-python-package-installation.md)
* [Modul plug-in nástroje HDInsight pro IntelliJ IDEA pro vytvoření a odesílání aplikací Spark Scala](apache-spark-intellij-tool-plugin.md)
* [Použití HDInsight Tools Plugin pro IntelliJ IDEA k ladění aplikací Apache Spark na dálku](apache-spark-intellij-tool-plugin-debug-jobs-remotely.md)
* [Používejte notebooky Apache Zeppelin s clusterem Apache Spark na HDInsightu](apache-spark-zeppelin-notebook.md)
* [Jádra dostupná pro notebook Jupyter v clusteru Apache Spark pro HDInsight](apache-spark-jupyter-notebook-kernels.md)
* [Nainstalujte do počítače Jupyter a připojte ho ke clusteru HDInsight Spark](apache-spark-jupyter-notebook-install-locally.md)

### <a name="manage-resources"></a>Správa prostředků

* [Správa prostředků v clusteru Apache Spark v Azure HDInsight](apache-spark-resource-manager.md)
* [Sledování a ladění úloh spuštěných v clusteru serveru Apache Spark v HDInsight](apache-spark-job-debugging.md)
