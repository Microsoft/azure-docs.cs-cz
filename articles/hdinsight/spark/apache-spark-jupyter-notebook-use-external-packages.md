---
title: Vlastní balíčky Maven pomocí Jupyter ve Sparku v Azure HDInsight
description: Podrobné pokyny ke konfiguraci k dispozici poznámkové bloky Jupyter s clustery HDInsight Spark používat vlastní balíčky Maven.
services: hdinsight
author: jasonwhowell
editor: jasonwhowell
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 01/09/2018
ms.author: jasonh
ms.openlocfilehash: 51099f64546acc6f18269b2e7ec05106bb3baa2d
ms.sourcegitcommit: 35ceadc616f09dd3c88377a7f6f4d068e23cceec
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/08/2018
ms.locfileid: "39622027"
---
# <a name="use-external-packages-with-jupyter-notebooks-in-apache-spark-clusters-on-hdinsight"></a>Použití externích balíčků s poznámkovými bloky Jupyter v clusterech Apache Spark v HDInsight
> [!div class="op_single_selector"]
> * [Využitím magických příkazů](apache-spark-jupyter-notebook-use-external-packages.md)
> * [Pomocí akce skriptu](apache-spark-python-package-installation.md)
>
>

Další informace o konfiguraci Poznámkový blok Jupyter v clusteru Apache Spark v HDInsight používat externí, komunitou **maven** balíčky, které nejsou součástí clusteru out-of-the-box. 

Můžete vyhledávat [úložiště Maven](http://search.maven.org/) pro úplný seznam balíčků, které jsou k dispozici. Seznam dostupných balíčků můžete získat také z jiných zdrojů. Například je k dispozici na úplný seznam balíčků z komunity [Spark balíčky](http://spark-packages.org/).

V tomto článku se dozvíte, jak používat [spark csv](http://search.maven.org/#artifactdetails%7Ccom.databricks%7Cspark-csv_2.10%7C1.4.0%7Cjar) balíček s poznámkovým blokem Jupyter.

## <a name="prerequisites"></a>Požadavky
Musíte mít následující:

* Cluster Apache Spark ve službě HDInsight. Pokyny najdete v tématu [Vytváření clusterů Apache Spark ve službě Azure HDInsight](apache-spark-jupyter-spark-sql.md).

## <a name="use-external-packages-with-jupyter-notebooks"></a>Použijte externí balíčky s poznámkovými bloky Jupyter
1. Z [Portálu Azure](https://portal.azure.com/) z úvodního panelu klikněte na dlaždici pro váš cluster Spark (pokud je připnutý na úvodní panel). Můžete také přejít na cluster pod položkou **Procházet vše** > **Clustery HDInsight**.   

1. Z okna clusteru Spark klikněte na tlačítko **Rychlé odkazy** a pak z okna **Řídicí panel clusteru** klikněte na tlačítko **Poznámkový blok Jupyter**. Po vyzvání zadejte přihlašovací údaje správce clusteru.

    > [!NOTE]
    > Může také otevřít poznámkový blok Jupyter pro váš cluster tak, že otevřete následující adresu URL v prohlížeči. Nahraďte **CLUSTERNAME** názvem clusteru:
    > 
    > `https://CLUSTERNAME.azurehdinsight.net/jupyter`
    > 

1. Vytvořte nový poznámkový blok. Klikněte na tlačítko **nový**a potom klikněte na tlačítko **Spark**.
   
    ![Vytvoření nového poznámkového bloku Jupyter](./media/apache-spark-jupyter-notebook-use-external-packages/hdinsight-spark-create-notebook.png "Vytvoření nového poznámkového bloku Jupyter")

1. Nový poznámkový blok se vytvoří a otevře s názvem Untitled.pynb. Klikněte na název poznámkového bloku v horní části a zadejte popisný název.
   
    ![Zadání názvu poznámkového bloku](./media/apache-spark-jupyter-notebook-use-external-packages/hdinsight-spark-name-notebook.png "Zadání názvu poznámkového bloku")

1. Budete používat `%%configure` magic nakonfigurujte ho na použití externího balíčku. V poznámkových bloků, které používají externí balíčky, ujistěte se, že zavoláte `%%configure` magic v první buňky kódu. Tím se zajistí, že jádra je nakonfigurován pro použití balíčku před spuštěním relace.

    >[!IMPORTANT] 
    >Pokud zapomenete konfigurace jádra do první buňky, můžete použít `%%configure` s `-f` parametr, ale bude restartovat relaci a všechna průběh se ztratí.

    | Verze HDInsight | Příkaz |
    |-------------------|---------|
    |Pro HDInsight 3.3 a HDInsight 3.4 | `%%configure` <br>`{ "packages":["com.databricks:spark-csv_2.10:1.4.0"] }`|
    | Pro HDInsight 3.5 a 3.6 HDInsight | `%%configure`<br>`{ "conf": {"spark.jars.packages": "com.databricks:spark-csv_2.10:1.4.0" }}`|

1. Fragmentu kódu uvedeném výše předpokládá, že souřadnice maven pro externího balíčku do centrálního úložiště Maven. V tomto fragmentu kódu `com.databricks:spark-csv_2.10:1.4.0` je souřadnice maven **spark csv** balíčku. Zde je, jak vytvořit souřadnice pro balíček.
   
    a. Vyhledejte balíček úložiště Maven. Pro účely tohoto kurzu používáme [spark csv](http://search.maven.org/#artifactdetails%7Ccom.databricks%7Cspark-csv_2.10%7C1.4.0%7Cjar).
   
    b. Z úložiště, shromážděte hodnoty **GroupId**, **ArtifactId**, a **verze**. Ujistěte se, že hodnoty, které shromáždíte odpovídá vašeho clusteru. V tomto případě používáme Scala 2.10 a Spark 1.4.0 balíček, ale budete muset vybrat různé verze pro příslušné Scala nebo verzi Sparku ve vašem clusteru. Můžete zjistit verzi Scala v clusteru spuštěním `scala.util.Properties.versionString` jádra Spark Jupyter nebo odeslat Spark. Můžete zjistit verzi Sparku ve vašem clusteru spuštěním `sc.version` v poznámkových blocích Jupyter.
   
    ![Použití externích balíčků s Poznámkový blok Jupyter](./media/apache-spark-jupyter-notebook-use-external-packages/use-external-packages-with-jupyter.png "použití externích balíčků s Poznámkový blok Jupyter")
   
    c. Zřetězit tři hodnoty oddělené dvojtečkou (**:**).
   
        com.databricks:spark-csv_2.10:1.4.0

1. Spuštění buňky kódu s `%%configure` magic. Tím se nakonfiguruje základní relace Livy pomocí balíčku, který jste zadali. V dalších buňky v poznámkovém bloku můžete nyní používat balíček, jak je znázorněno níže.
   
        val df = sqlContext.read.format("com.databricks.spark.csv").
        option("header", "true").
        option("inferSchema", "true").
        load("wasb:///HdiSamples/HdiSamples/SensorSampleData/hvac/HVAC.csv")

    Pro HDInsight 3.6 měli byste použít následující fragment kódu.

        val df = spark.read.format("com.databricks.spark.csv").
        option("header", "true").
        option("inferSchema", "true").
        load("wasb:///HdiSamples/HdiSamples/SensorSampleData/hvac/HVAC.csv")

1. Pak spustíte fragmenty kódu, jako je třeba je znázorněno níže, chcete-li zobrazit data z datového rámce, kterou jste vytvořili v předchozím kroku.
   
        df.show()
   
        df.select("Time").count()

## <a name="seealso"></a>Viz také
* [Přehled: Apache Spark v Azure HDInsight](apache-spark-overview.md)

### <a name="scenarios"></a>Scénáře
* [Spark s BI: Provádějte interaktivní analýzy dat pomocí Sparku v HDInsight pomocí nástrojů BI](apache-spark-use-bi-tools.md)
* [Spark s Machine Learning: Používejte Spark v HDInsight pro analýzu teploty v budově pomocí dat HVAC](apache-spark-ipython-notebook-machine-learning.md)
* [Spark s Machine Learning: Používejte Spark v HDInsight k předpovědím výsledků kontrol potravin](apache-spark-machine-learning-mllib-ipython.md)
* [Analýza protokolu webu pomocí Sparku v HDInsight](apache-spark-custom-library-website-log-analysis.md)

### <a name="create-and-run-applications"></a>Vytvoření a spouštění aplikací
* [Vytvoření samostatné aplikace pomocí Scala](apache-spark-create-standalone-application.md)
* [Vzdálené spouštění úloh na clusteru Sparku pomocí Livy](apache-spark-livy-rest-interface.md)

### <a name="tools-and-extensions"></a>Nástroje a rozšíření

* [Použití pythonu externí balíčky s poznámkovými bloky Jupyter v clusterech Apache Spark v HDInsight Linux](apache-spark-python-package-installation.md)
* [Modul plug-in nástroje HDInsight pro IntelliJ IDEA pro vytvoření a odesílání aplikací Spark Scala](apache-spark-intellij-tool-plugin.md)
* [Použití modulu plug-in nástroje HDInsight pro IntelliJ IDEA pro vzdálené ladění aplikací Spark](apache-spark-intellij-tool-plugin-debug-jobs-remotely.md)
* [Použití poznámkových bloků Zeppelin s clusterem Sparku v HDInsight](apache-spark-zeppelin-notebook.md)
* [Jádra dostupná pro poznámkový blok Jupyter v clusteru Sparku pro HDInsight](apache-spark-jupyter-notebook-kernels.md)
* [Instalace Jupyteru do počítače a připojení ke clusteru HDInsight Spark](apache-spark-jupyter-notebook-install-locally.md)

### <a name="manage-resources"></a>Správa prostředků
* [Správa prostředků v clusteru Apache Spark v Azure HDInsight](apache-spark-resource-manager.md)
* [Sledování a ladění úloh spuštěných v clusteru Apache Spark v HDInsight](apache-spark-job-debugging.md)
