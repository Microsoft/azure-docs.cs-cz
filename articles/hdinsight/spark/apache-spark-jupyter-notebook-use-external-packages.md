---
title: Použití vlastních balíčků Maven s Jupyter ve Sparku – Azure HDInsight
description: Podrobné pokyny, jak nakonfigurovat Jupyter poznámkové bloky dostupné v clusterech HDInsight Spark pro použití vlastních balíčků Maven.
ms.service: hdinsight
ms.topic: how-to
ms.custom: hdinsightactive
ms.date: 11/22/2019
ms.openlocfilehash: 3ddfdfdfe10d5b6ea7c2d5cd99d325564163c0dd
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/30/2021
ms.locfileid: "104866008"
---
# <a name="use-external-packages-with-jupyter-notebooks-in-apache-spark-clusters-on-hdinsight"></a>Použití externích balíčků s Jupyter poznámkovým blokům v clusterech s Apache Spark v HDInsight

Naučte se, jak nakonfigurovat [Jupyter notebook](https://jupyter.org/) v clusteru Apache Spark ve službě HDInsight tak, aby používaly externí balíčky Apache **Maven** poskytované komunitou, které nejsou zahrnuté do clusteru.

Úplný seznam dostupných balíčků můžete vyhledat v [úložišti Maven](https://search.maven.org/) . Můžete také získat seznam dostupných balíčků z jiných zdrojů. Například úplný seznam balíčků, které jsou součástí komunity, je k dispozici v [balíčcích Spark](https://spark-packages.org/).

V tomto článku se dozvíte, jak pomocí Jupyter Notebook balíčku [Spark-CSV](https://search.maven.org/#artifactdetails%7Ccom.databricks%7Cspark-csv_2.10%7C1.4.0%7Cjar) .

## <a name="prerequisites"></a>Požadavky

* Cluster Apache Spark ve službě HDInsight. Pokyny najdete v tématu [Vytváření clusterů Apache Spark ve službě Azure HDInsight](apache-spark-jupyter-spark-sql.md).

* Znalost používání poznámkových bloků Jupyter se Sparkem ve službě HDInsight. Další informace najdete v tématech [načtení dat a spuštění dotazů s Apache Spark v HDInsight](./apache-spark-load-data-run-query.md).

* [Schéma identifikátoru URI](../hdinsight-hadoop-linux-information.md#URI-and-scheme) pro primární úložiště clusterů. To Azure Storage pro `wasb://` `abfs://` Azure Data Lake Storage Gen2 nebo `adl://` pro Azure Data Lake Storage Gen1. Pokud je pro Azure Storage nebo Data Lake Storage Gen2 povolený zabezpečený přenos, identifikátor URI by byl `wasbs://` nebo v `abfss://` uvedeném pořadí taky [zabezpečený přenos](../../storage/common/storage-require-secure-transfer.md).

## <a name="use-external-packages-with-jupyter-notebooks"></a>Použití externích balíčků s Jupyter poznámkovým blokem

1. Přejděte na `https://CLUSTERNAME.azurehdinsight.net/jupyter` místo, kde `CLUSTERNAME` je název vašeho clusteru Spark.

1. Vytvořte nový poznámkový blok. Vyberte **Nový** a pak vyberte **Spark**.

    :::image type="content" source="./media/apache-spark-jupyter-notebook-use-external-packages/hdinsight-spark-create-notebook.png " alt-text="Vytvořit nový Jupyter Notebook Spark" border="true":::

1. Nový poznámkový blok se vytvoří a otevře s názvem Untitled.pynb. Vyberte název poznámkového bloku v horní části a zadejte popisný název.

    :::image type="content" source="./media/apache-spark-jupyter-notebook-use-external-packages/hdinsight-spark-name-notebook.png " alt-text="Zadání názvu poznámkového bloku" border="true":::

1. `%%configure`K nakonfigurování poznámkového bloku pro použití externího balíčku použijete Magic. V poznámkových blocích, které používají externí balíčky, se ujistěte, že jste volali `%%configure` Magic do první buňky kódu. Tím se zajistí, že jádro je nakonfigurované na použití balíčku před spuštěním relace.

    >[!IMPORTANT]  
    >Pokud zapomenete nakonfigurovat jádro v první buňce, můžete použít `%%configure` `-f` parametr s parametrem, který ale bude restartovat relaci a veškerý průběh bude ztracen.

    | Verze HDInsight | Příkaz |
    |-------------------|---------|
    | Pro HDInsight 3,5 a HDInsight 3,6 | `%%configure`<br>`{ "conf": {"spark.jars.packages": "com.databricks:spark-csv_2.11:1.5.0" }}`|
    |Pro HDInsight 3,3 a HDInsight 3,4 | `%%configure` <br>`{ "packages":["com.databricks:spark-csv_2.10:1.4.0"] }`|

1. Výše uvedený fragment kódu očekává souřadnice Maven pro externí balíček v centrálním úložišti Maven. V tomto fragmentu kódu `com.databricks:spark-csv_2.11:1.5.0` je souřadnice Maven balíčku **Spark-CSV** . Tady je postup, jak sestavit souřadnice balíčku.

    a. Vyhledejte balíček v úložišti Maven. V tomto článku používáme [Spark-CSV](https://mvnrepository.com/artifact/com.databricks/spark-csv).

    b. Z úložiště Shromážděte hodnoty pro ID **skupiny**, **ArtifactId** a **verzi**. Ujistěte se, že hodnoty, které shromáždíte, odpovídají vašemu clusteru. V tomto případě používáme balíček Scala 2,11 a Spark 1.5.0, ale v clusteru možná budete muset vybrat jiné verze pro příslušnou verzi Scala nebo Sparku. Verzi Scala můžete v clusteru zjistit spuštěním `scala.util.Properties.versionString` v jádru Spark Jupyter nebo v Sparku odeslat. Verzi Sparku v clusteru můžete zjistit spuštěním `sc.version` na poznámkových blocích Jupyter.

    :::image type="content" source="./media/apache-spark-jupyter-notebook-use-external-packages/use-external-packages-with-jupyter.png " alt-text="Použití externích balíčků s Jupyter Notebook" border="true":::

    c. Zřetězí tři hodnoty oddělené dvojtečkou (**:**).

    ```scala
    com.databricks:spark-csv_2.11:1.5.0
    ```

1. Spusťte buňku Code s `%%configure` Magic. Tím se nakonfiguruje základní Livy relace tak, aby používala balíček, který jste zadali. V následujících buňkách poznámkového bloku teď můžete použít balíček, jak vidíte níže.

    ```scala
    val df = spark.read.format("com.databricks.spark.csv").
    option("header", "true").
    option("inferSchema", "true").
    load("wasb:///HdiSamples/HdiSamples/SensorSampleData/hvac/HVAC.csv")
    ```

    Pro HDInsight 3,4 a níže byste měli použít následující fragment kódu.

    ```scala
    val df = sqlContext.read.format("com.databricks.spark.csv").
    option("header", "true").
    option("inferSchema", "true").
    load("wasb:///HdiSamples/HdiSamples/SensorSampleData/hvac/HVAC.csv")
    ```

1. Potom můžete spustit fragmenty, jako je například znázorněno níže, a zobrazit tak data z datového rámce, který jste vytvořili v předchozím kroku.

    ```scala
    df.show()
   
    df.select("Time").count()
    ```

## <a name="see-also"></a><a name="seealso"></a>Viz také

* [Přehled: Apache Spark v Azure HDInsight](apache-spark-overview.md)

### <a name="scenarios"></a>Scénáře

* [Apache Spark s BI: provádějte interaktivní analýzy dat pomocí Sparku v HDInsight pomocí nástrojů BI.](apache-spark-use-bi-tools.md)
* [Apache Spark s Machine Learning: pomocí Sparku v HDInsight můžete analyzovat teplotu budovy pomocí dat TVK.](apache-spark-ipython-notebook-machine-learning.md)
* [Apache Spark s Machine Learning: pomocí Sparku v HDInsight předpovídat výsledky kontroly potravin](apache-spark-machine-learning-mllib-ipython.md)
* [Analýza webového protokolu pomocí Apache Spark ve službě HDInsight](apache-spark-custom-library-website-log-analysis.md)

### <a name="create-and-run-applications"></a>Vytvoření a spouštění aplikací

* [Vytvoření samostatné aplikace pomocí Scala](apache-spark-create-standalone-application.md)
* [Vzdálené spouštění úloh na clusteru Apache Spark s využitím Apache Livy](apache-spark-livy-rest-interface.md)

### <a name="tools-and-extensions"></a>Nástroje a rozšíření

* [Použití externích balíčků Pythonu s Jupyter poznámkovým blokům v clusterech s Apache Spark v HDInsight Linux](apache-spark-python-package-installation.md)
* [Modul plug-in nástroje HDInsight pro IntelliJ IDEA pro vytvoření a odesílání aplikací Spark Scala](apache-spark-intellij-tool-plugin.md)
* [Použití modulu plug-in nástrojů HDInsight pro IntelliJ NÁPADu při vzdáleném ladění aplikací Apache Spark](apache-spark-intellij-tool-plugin-debug-jobs-remotely.md)
* [Použití poznámkových bloků Apache Zeppelin s clusterem Apache Spark v HDInsight](apache-spark-zeppelin-notebook.md)
* [Jádra dostupná pro Jupyter Notebook v clusteru Apache Spark pro HDInsight](apache-spark-jupyter-notebook-kernels.md)
* [Nainstalujte do počítače Jupyter a připojte ho ke clusteru HDInsight Spark](apache-spark-jupyter-notebook-install-locally.md)

### <a name="manage-resources"></a>Správa prostředků

* [Správa prostředků v clusteru Apache Spark v Azure HDInsight](apache-spark-resource-manager.md)
* [Sledování a ladění úloh spuštěných v clusteru serveru Apache Spark v HDInsight](apache-spark-job-debugging.md)
