---
title: Microsoft Cognitive Toolkit s Apache Spark – Azure HDInsight
description: Zjistěte, jak lze trénovaný model hlubokého učení Microsoft Cognitive Toolkit použít na datovou sadu pomocí rozhraní Api Spark Python v clusteru Azure HDInsight Spark.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.custom: hdinsightactive
ms.date: 01/14/2020
ms.openlocfilehash: 1933db624dfef2ffa747ecb043be6730b6b884b5
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "78206550"
---
# <a name="use-microsoft-cognitive-toolkit-deep-learning-model-with-azure-hdinsight-spark-cluster"></a>Použití modelu hloubkového učení Microsoft Cognitive Toolkit s clusterem Azure HDInsight Spark

V tomto článku provedete následující kroky.

1. Spusťte vlastní skript a nainstalujte [sadu Microsoft Cognitive Toolkit](https://docs.microsoft.com/cognitive-toolkit/) do clusteru Azure HDInsight Spark.

2. Nahrajte [poznámkový blok Jupyter](https://jupyter.org/) do clusteru [Apache Spark](https://spark.apache.org/) a zjistěte, jak použít trénovaný model hlubokého učení Microsoft Cognitive Toolkit na soubory v účtu úložiště objektů blob Azure pomocí rozhraní Api Spark [Pythonu (PySpark).](https://spark.apache.org/docs/latest/api/python/index.html)

## <a name="prerequisites"></a>Požadavky

* Cluster Apache Spark ve službě HDInsight. Viz [Vytvoření clusteru Apache Spark](./apache-spark-jupyter-spark-sql-use-portal.md).

* Znalost používání poznámkových bloků Jupyter se Sparkem ve službě HDInsight. Další informace najdete v [tématu Načítání dat a spouštění dotazů pomocí Apache Spark na HDInsight .](./apache-spark-load-data-run-query.md)

## <a name="how-does-this-solution-flow"></a>Jak tok tohoto řešení?

Toto řešení je rozděleno mezi tento článek a poznámkový blok Jupyter, který nahrajete jako součást tohoto článku. V tomto článku provedete následující kroky:

* Spusťte akci skriptu v clusteru HDInsight Spark a nainstalujte balíčky Microsoft Cognitive Toolkit a Python.
* Nahrajte poznámkový blok Jupyter, který spouští řešení do clusteru HDInsight Spark.

Následující zbývající kroky jsou popsány v poznámkovém bloku Jupyter.

* Načtěte ukázkové obrazy do distribuované datové sady odolné vůči sísním nebo rdd.
  * Načtěte moduly a definujte přednastavení.
  * Stáhněte si datovou sadu místně v clusteru Spark.
  * Převeďte datovou sadu na RDD.
* Skóre obrázky pomocí trénované cognitive toolkit model.
  * Stáhněte si trénovaný model sady Cognitive Toolkit do clusteru Spark.
  * Definujte funkce, které mají být používány pracovními uzly.
  * Skóre obrázky na pracovní uzly.
  * Vyhodnoťte přesnost modelu.

## <a name="install-microsoft-cognitive-toolkit"></a>Instalace sady Microsoft Cognitive Toolkit

Sadu Microsoft Cognitive Toolkit můžete nainstalovat do clusteru Spark pomocí akce skriptu. Akce skriptu používá vlastní skripty k instalaci součástí do clusteru, které nejsou ve výchozím nastavení k dispozici. Vlastní skript můžete použít z webu Azure Portal, pomocí hdinsight .NET SDK nebo pomocí Azure PowerShellu. Pomocí skriptu můžete také nainstalovat sadu nástrojů buď jako součást vytváření clusteru, nebo po spuštění clusteru.

V tomto článku používáme portál k instalaci sady nástrojů po vytvoření clusteru. Další způsoby spuštění vlastního skriptu naleznete v [tématu Přizpůsobení clusterů HDInsight pomocí akce skriptu](../hdinsight-hadoop-customize-cluster-linux.md).

### <a name="using-the-azure-portal"></a>Použití webu Azure Portal

Pokyny ke spuštění akce skriptu pomocí portálu Azure najdete v [tématu Přizpůsobení clusterů HDInsight pomocí akce skriptu](../hdinsight-hadoop-customize-cluster-linux.md#script-action-during-cluster-creation). Ujistěte se, že jste poskytli následující vstupy pro instalaci sady Microsoft Cognitive Toolkit. Pro akci skriptu použijte následující hodnoty:

|Vlastnost |Hodnota |
|---|---|
|Typ skriptu|- Vlastní|
|Name (Název)| Instalace MCT|
|Bash skript URI|`https://raw.githubusercontent.com/Azure-Samples/hdinsight-pyspark-cntk-integration/master/cntk-install.sh`|
|Typ uzlu(y):|Hlava, Pracovník|
|Parametry|Žádný|

## <a name="upload-the-jupyter-notebook-to-azure-hdinsight-spark-cluster"></a>Nahrání poznámkového bloku Jupyter do clusteru Azure HDInsight Spark

Pokud chcete používat sadu Microsoft Cognitive Toolkit s clusterem Azure HDInsight Spark, musíte načíst poznámkový blok Jupyter **CNTK_model_scoring_on_Spark_walkthrough.ipynb** do clusteru Azure HDInsight Spark. Tento poznámkový blok je [https://github.com/Azure-Samples/hdinsight-pyspark-cntk-integration](https://github.com/Azure-Samples/hdinsight-pyspark-cntk-integration)k dispozici na GitHubu na adrese .

1. Stáhnout a [https://github.com/Azure-Samples/hdinsight-pyspark-cntk-integration](https://github.com/Azure-Samples/hdinsight-pyspark-cntk-integration)rozbalit .

1. Z webového prohlížeče `https://CLUSTERNAME.azurehdinsight.net/jupyter`přejděte `CLUSTERNAME` na , kde je název clusteru.

1. V poznámkovém bloku Jupyter vyberte **Nahrát** v pravém horním `CNTK_model_scoring_on_Spark_walkthrough.ipynb`rohu a pak přejděte na stáhnout a vyberte soubor .

    ![Nahrání poznámkového bloku Jupyter do clusteru Azure HDInsight Spark](./media/apache-spark-microsoft-cognitive-toolkit/hdinsight-microsoft-cognitive-toolkit-load-jupyter-notebook.png "Nahrání poznámkového bloku Jupyter do clusteru Azure HDInsight Spark")

1. Vyberte **Znovu nahrát.**

1. Po nahrání poznámkového bloku klikněte na název poznámkového bloku a postupujte podle pokynů v samotném poznámkovém bloku o tom, jak načíst sadu dat a provést článek.

## <a name="see-also"></a>Viz také

* [Přehled: Apache Spark v Azure HDInsight](apache-spark-overview.md)

### <a name="scenarios"></a>Scénáře

* [Apache Spark s BI: Provádění interaktivní analýzy dat pomocí Spark v HDInsightu pomocí nástrojů BI](apache-spark-use-bi-tools.md)
* [Apache Spark se strojovým učením: Použijte Spark v HDInsightu pro analýzu teploty budovy pomocí dat HVAC](apache-spark-ipython-notebook-machine-learning.md)
* [Apache Spark s machine learningem: Využijte Spark v HDInsightu k předvídání výsledků kontroly potravin](apache-spark-machine-learning-mllib-ipython.md)
* [Analýza protokolu webových stránek pomocí Apache Spark v HDInsight](apache-spark-custom-library-website-log-analysis.md)
* [Analýza telemetrických dat Application Insight pomocí Apache Spark v HDInsightu](apache-spark-analyze-application-insight-logs.md)

### <a name="create-and-run-applications"></a>Vytvoření a spouštění aplikací

* [Vytvoření samostatné aplikace pomocí Scala](apache-spark-create-standalone-application.md)
* [Spouštění úloh na dálku v clusteru Apache Spark pomocí Apache Livy](apache-spark-livy-rest-interface.md)

### <a name="tools-and-extensions"></a>Nástroje a rozšíření

* [Modul plug-in nástroje HDInsight pro IntelliJ IDEA pro vytvoření a odesílání aplikací Spark Scala](apache-spark-intellij-tool-plugin.md)
* [Použití HDInsight Tools Plugin pro IntelliJ IDEA k ladění aplikací Apache Spark na dálku](apache-spark-intellij-tool-plugin-debug-jobs-remotely.md)
* [Používejte notebooky Apache Zeppelin s clusterem Apache Spark na HDInsightu](apache-spark-zeppelin-notebook.md)
* [Jádra dostupná pro notebook Jupyter v clusteru Apache Spark pro HDInsight](apache-spark-jupyter-notebook-kernels.md)
* [Použijte externí balíčky s poznámkovými bloky Jupyter](apache-spark-jupyter-notebook-use-external-packages.md)
* [Nainstalujte do počítače Jupyter a připojte ho ke clusteru HDInsight Spark](apache-spark-jupyter-notebook-install-locally.md)

### <a name="manage-resources"></a>Správa prostředků

* [Správa prostředků v clusteru Apache Spark v Azure HDInsight](apache-spark-resource-manager.md)
* [Sledování a ladění úloh spuštěných v clusteru serveru Apache Spark v HDInsight](apache-spark-job-debugging.md)
