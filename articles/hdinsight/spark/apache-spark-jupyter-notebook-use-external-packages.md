---
title: Vlastní balíčky Maven pomocí Jupyter ve Sparku v Azure HDInsight | Microsoft Docs
description: Podrobné pokyny o tom, jak nakonfigurovat k dispozici poznámkové bloky Jupyter s clustery HDInsight Spark použití vlastní Maven balíčků.
services: hdinsight
documentationcenter: ''
author: nitinme
manager: jhubbard
editor: cgronlun
tags: azure-portal
ms.assetid: 2a8bc545-064e-436f-8b5f-e67c26cfbf98
ms.service: hdinsight
ms.custom: hdinsightactive
ms.devlang: na
ms.topic: conceptual
ms.date: 01/09/2018
ms.author: nitinme
ms.openlocfilehash: dfecdd5c9399c1bd6eb021f097481e3c73f699ad
ms.sourcegitcommit: 1362e3d6961bdeaebed7fb342c7b0b34f6f6417a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/18/2018
ms.locfileid: "31520819"
---
# <a name="use-external-packages-with-jupyter-notebooks-in-apache-spark-clusters-on-hdinsight"></a>Použijte externí balíčky s poznámkovými bloky Jupyter v clusterech Apache Spark v HDInsight
> [!div class="op_single_selector"]
> * [Pomocí buňky magic](apache-spark-jupyter-notebook-use-external-packages.md)
> * [Pomocí akce skriptu](apache-spark-python-package-installation.md)
>
>

Naučte se konfigurovat poznámkového bloku Jupyter v clusteru Apache Spark v HDInsight používat externí, komunity podílí **maven** balíčky, které nejsou zahrnuty v clusteru se na pole. 

Můžete hledat [Maven úložiště](http://search.maven.org/) úplný seznam balíčků, které jsou k dispozici. Seznam dostupných balíčků můžete také získat z jiných zdrojů. Například je k dispozici úplný seznam balíčků podílí komunity [Spark balíčky](http://spark-packages.org/).

V tomto článku se dozvíte, jak používat [spark csv](http://search.maven.org/#artifactdetails%7Ccom.databricks%7Cspark-csv_2.10%7C1.4.0%7Cjar) balíček s poznámkovým blokem Jupyter.

## <a name="prerequisites"></a>Požadavky
Musíte mít následující:

* Cluster Apache Spark v HDInsight. Pokyny najdete v tématu [clusterů vytvořit Apache Spark v Azure HDInsight](apache-spark-jupyter-spark-sql.md).

## <a name="use-external-packages-with-jupyter-notebooks"></a>Použijte externí balíčky s poznámkovými bloky Jupyter
1. Z [Portálu Azure](https://portal.azure.com/) z úvodního panelu klikněte na dlaždici pro váš cluster Spark (pokud je připnutý na úvodní panel). Můžete také přejít na cluster pod položkou **Procházet vše** > **Clustery HDInsight**.   

2. Z okna clusteru Spark klikněte na tlačítko **Rychlé odkazy** a pak z okna **Řídicí panel clusteru** klikněte na tlačítko **Poznámkový blok Jupyter**. Po vyzvání zadejte přihlašovací údaje správce clusteru.

    > [!NOTE]
    > Může také otevřít poznámkový blok Jupyter pro váš cluster tak, že otevřete následující adresu URL v prohlížeči. Nahraďte **CLUSTERNAME** názvem clusteru:
    > 
    > `https://CLUSTERNAME.azurehdinsight.net/jupyter`
    > 

3. Vytvořte nový poznámkový blok. Klikněte na tlačítko **nový**a potom klikněte na **Spark**.
   
    ![Vytvoření nového poznámkového bloku Jupyter](./media/apache-spark-jupyter-notebook-use-external-packages/hdinsight-spark-create-notebook.png "Vytvoření nového poznámkového bloku Jupyter")

4. Nový poznámkový blok se vytvoří a otevře s názvem Untitled.pynb. Klikněte na název poznámkového bloku v horní části a zadejte popisný název.
   
    ![Zadání názvu poznámkového bloku](./media/apache-spark-jupyter-notebook-use-external-packages/hdinsight-spark-name-notebook.png "Zadání názvu poznámkového bloku")

5. Budete používat `%%configure` magic konfigurace poznámkového bloku na externí balíček použít. Ujistěte se, zavoláte v poznámkových bloků, které používají externí balíčky, `%%configure` magic v první buňky kódu. Tím se zajistí, že jádra je nakonfigurované na použití balíčku před zahájením relace.

    >[!IMPORTANT] 
    >Pokud zapomenete nakonfigurovat jádra v první buňky, můžete použít `%%configure` s `-f` parametr, ale který restartuje relace a všechny průběh budou ztraceny.

    | HDInsight verze | Příkaz |
    |-------------------|---------|
    |Pro HDInsight 3.3 a HDInsight 3.4 | `%%configure` <br>`{ "packages":["com.databricks:spark-csv_2.10:1.4.0"] }`|
    | Pro HDInsight 3.5 a HDInsight 3.6 | `%%configure`<br>`{ "conf": {"spark.jars.packages": "com.databricks:spark-csv_2.10:1.4.0" }}`|

6. Výše uvedeném fragmentu očekává souřadnice maven pro externí balíček v centrálním úložišti Maven. V této fragmentu kódu `com.databricks:spark-csv_2.10:1.4.0` je souřadnice maven **spark csv** balíčku. Zde je, jak vytvořit souřadnice pro balíček.
   
    a. Najděte balíček v úložišti Maven. V tomto kurzu používáme [spark csv](http://search.maven.org/#artifactdetails%7Ccom.databricks%7Cspark-csv_2.10%7C1.4.0%7Cjar).
   
    b. Z úložiště, shromážděte hodnoty **GroupId**, **ArtifactId**, a **verze**. Ujistěte se, že hodnoty, které shromáždíte odpovídat vašeho clusteru. V tomto případě používáme Scala 2.10 a Spark 1.4.0 balíček, ale budete muset vybrat různé verze pro příslušné Scala nebo Spark verze v clusteru. Můžete zjistit verzi Scala v clusteru tak, že spustíte `scala.util.Properties.versionString` na Spark Jupyter jádra nebo odeslání Spark. Můžete zjistit verzi Spark v clusteru tak, že spustíte `sc.version` v poznámkových blocích Jupyter.
   
    ![Použijte externí balíčky s Poznámkový blok Jupyter](./media/apache-spark-jupyter-notebook-use-external-packages/use-external-packages-with-jupyter.png "použijte externí balíčky s poznámkového bloku Jupyter")
   
    c. Řetězení tři hodnoty oddělené dvojtečkou (**:**).
   
        com.databricks:spark-csv_2.10:1.4.0

7. Spustit buňky kódu pomocí `%%configure` magic. Tím nakonfigurujete základní relace Livy k použití balíčku, který jste zadali. V následujících buněk v poznámkovém bloku můžete nyní používat balíčku, jak je uvedeno níže.
   
        val df = sqlContext.read.format("com.databricks.spark.csv").
        option("header", "true").
        option("inferSchema", "true").
        load("wasb:///HdiSamples/HdiSamples/SensorSampleData/hvac/HVAC.csv")

    Pro HDInsight 3.6 měli byste použít následující fragment kódu.

        val df = spark.read.format("com.databricks.spark.csv").
        option("header", "true").
        option("inferSchema", "true").
        load("wasb:///HdiSamples/HdiSamples/SensorSampleData/hvac/HVAC.csv")

8. Potom můžete spustit fragmenty kódu, jako vidíte níže, chcete-li zobrazit data z dataframe jste vytvořili v předchozím kroku.
   
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

* [Používat externí python balíčky s poznámkovými bloky Jupyter v clusterech Apache Spark na HDInsight Linux](apache-spark-python-package-installation.md)
* [Modul plug-in nástroje HDInsight pro IntelliJ IDEA pro vytvoření a odesílání aplikací Spark Scala](apache-spark-intellij-tool-plugin.md)
* [Použití modulu plug-in nástroje HDInsight pro IntelliJ IDEA pro vzdálené ladění aplikací Spark](apache-spark-intellij-tool-plugin-debug-jobs-remotely.md)
* [Použití poznámkových bloků Zeppelin s clusterem Sparku v HDInsight](apache-spark-zeppelin-notebook.md)
* [Jádra dostupná pro poznámkový blok Jupyter v clusteru Sparku pro HDInsight](apache-spark-jupyter-notebook-kernels.md)
* [Instalace Jupyteru do počítače a připojení ke clusteru HDInsight Spark](apache-spark-jupyter-notebook-install-locally.md)

### <a name="manage-resources"></a>Správa prostředků
* [Správa prostředků v clusteru Apache Spark v Azure HDInsight](apache-spark-resource-manager.md)
* [Sledování a ladění úloh spuštěných v clusteru Apache Spark v HDInsight](apache-spark-job-debugging.md)
