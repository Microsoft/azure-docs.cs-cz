---
title: Microsoft Cognitive Toolkit s Azure HDInsight Spark pro obsáhlý learning
description: Zjistěte, jak trénovaného modelu obsáhlého learningu Microsoft Cognitive Toolkit lze použít u datové sady pomocí rozhraní Python API Spark v clusteru Azure HDInsight Spark.
services: hdinsight
author: hrasheed-msft
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 11/28/2017
ms.author: hrasheed
ms.openlocfilehash: 76fc2c9a0864417e380471f8de59d148e0fbf7b4
ms.sourcegitcommit: e68df5b9c04b11c8f24d616f4e687fe4e773253c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/20/2018
ms.locfileid: "53651823"
---
# <a name="use-microsoft-cognitive-toolkit-deep-learning-model-with-azure-hdinsight-spark-cluster"></a>Použití sady Microsoft Cognitive Toolkit hloubkového učení modelu s clusterem Azure HDInsight Spark

V tomto článku proveďte následující kroky.

1. Spustit vlastní skript pro instalaci [Microsoft Cognitive Toolkit](https://www.microsoft.com/en-us/cognitive-toolkit/) v clusteru Azure HDInsight Spark.

2. Nahrát [Poznámkový blok Jupyter](https://jupyter.org/) k [Apache Spark](https://spark.apache.org/) clusteru naleznete v tématu Jak používat trénovaného modelu obsáhlého learningu Microsoft Cognitive Toolkit pro soubory v účtu úložiště objektů Blob v Azure pomocí [ Spark Python API (PySpark)](https://spark.apache.org/docs/0.9.0/python-programming-guide.html)

## <a name="prerequisites"></a>Požadavky

* **Předplatné Azure**. Než začnete tento kurz, musíte mít předplatné Azure. Přečtěte si téma [Bezplatné vytvoření účtu Microsoft Azure ještě dnes](https://azure.microsoft.com/free).

* **Cluster Azure HDInsight Spark**. Pro účely tohoto článku vytvořte cluster Spark 2.0. Pokyny najdete v tématu [clusteru vytvořit Apache Spark v Azure HDInsight](apache-spark-jupyter-spark-sql.md).

## <a name="how-does-this-solution-flow"></a>Jak toto řešení tok?

Toto řešení je rozdělena mezi v tomto článku a poznámkového bloku Jupyter, který nahrajete v rámci tohoto kurzu. V tomto článku proveďte následující kroky:

* Spusťte akci skriptu na clusteru HDInsight Spark, aby instalace sady Microsoft Cognitive Toolkit a balíčky Pythonu.
* Nahrání poznámkového bloku Jupyter, na kterém běží řešení ke clusteru HDInsight Spark.

Následující zbývající kroky jsou popsané v Poznámkový blok Jupyter.

- Načtěte ukázkové obrázky do Spark Resiliant Distributed Dataset nebo RDD.
   - Moduly zatížení a definovat předvolby.
   - Stáhněte datovou sadu místně na clusteru Spark.
   - Převeďte RDD datové sady.
- Skóre imagí pomocí trénovaného modelu Cognitive Toolkit.
   - Stáhněte si trénovaného modelu Cognitive Toolkit ke clusteru Spark.
   - Definování funkcí používané pracovních uzlů.
   - Skóre bitové kopie na pracovních uzlech.
   - Vyzkoušejte přesnost modelu.


## <a name="install-microsoft-cognitive-toolkit"></a>Instalace sady Microsoft Cognitive Toolkit

Microsoft Cognitive Toolkit můžete nainstalovat na clusteru Sparku pomocí skriptových akcí. Akce skriptu vlastních skriptů používá k instalaci součásti v clusteru, které nejsou k dispozici ve výchozím nastavení. Pomocí sady HDInsight .NET SDK nebo pomocí prostředí Azure PowerShell, můžete použít vlastní skript na webu Azure Portal. Skript můžete použít také k instalaci této sady nástrojů buď jako součást vytváření clusteru nebo jakmile je cluster zprovozněný. 

V tomto článku jsme pomocí portálu pro instalaci této sady nástrojů, po vytvoření clusteru. Další způsoby, jak spustit vlastní skript, naleznete v tématu [HDInsight přizpůsobit clustery pomocí akce skriptu](../hdinsight-hadoop-customize-cluster-linux.md).

### <a name="using-the-azure-portal"></a>Použití Portálu Azure

Pokyny o tom, jak pomocí portálu Azure ke spuštění akcí skriptů najdete v tématu [HDInsight přizpůsobit clustery pomocí akce skriptu](../hdinsight-hadoop-customize-cluster-linux.md#use-a-script-action-during-cluster-creation). Ujistěte se, že zadáte následující vstupy k instalaci sady Microsoft Cognitive Toolkit.

* Zadejte hodnotu pro název akce skriptu.

* Pro **URI skriptu Bash**, zadejte `https://raw.githubusercontent.com/Azure-Samples/hdinsight-pyspark-cntk-integration/master/cntk-install.sh`.

* Ujistěte se, že skript spusťte jenom na vedoucí a pracovní uzly a zrušte zaškrtnutí všech políček.

* Klikněte na možnost **Vytvořit**.

## <a name="upload-the-jupyter-notebook-to-azure-hdinsight-spark-cluster"></a>Nahrání poznámkového bloku Jupyter ke clusteru Azure HDInsight Spark

Microsoft Cognitive Toolkit pomocí clusteru Azure HDInsight Spark, je nutné načíst Poznámkový blok Jupyter **CNTK_model_scoring_on_Spark_walkthrough.ipynb** do clusteru Azure HDInsight Spark. Tento poznámkový blok je k dispozici na Githubu v [ https://github.com/Azure-Samples/hdinsight-pyspark-cntk-integration ](https://github.com/Azure-Samples/hdinsight-pyspark-cntk-integration).

1. Naklonujte úložiště GitHub [ https://github.com/Azure-Samples/hdinsight-pyspark-cntk-integration ](https://github.com/Azure-Samples/hdinsight-pyspark-cntk-integration). Pokyny ke klonování najdete v tématu [klonování úložiště](https://help.github.com/articles/cloning-a-repository/).

2. Na webu Azure Portal otevřete okna clusteru Spark, který jste již zřídit, klikněte na tlačítko **řídicí panel clusteru**a potom klikněte na tlačítko **Poznámkový blok Jupyter**.

    Můžete také spustit Poznámkový blok Jupyter tak, že přejdete na adresu URL `https://<clustername>.azurehdinsight.net/jupyter/`. Nahraďte \<Název_clusteru > s názvem vašeho clusteru HDInsight.

3. Ze kterého má Poznámkový blok, klikněte na tlačítko **nahrát** v pravém horním rohu a pak přejděte do umístění, které jste naklonovali úložiště GitHub.

    ![Nahrát Poznámkový blok Jupyter ke clusteru Azure HDInsight Spark](./media/apache-spark-microsoft-cognitive-toolkit/hdinsight-microsoft-cognitive-toolkit-load-jupyter-notebook.png "Poznámkový blok Jupyter nahrát do clusteru Azure HDInsight Spark")

4. Klikněte na tlačítko **nahrát** znovu.

5. Po nahrání poznámkového bloku klikněte na název poznámkového bloku a pak postupujte podle pokynů v samotné Poznámkový blok o tom, jak načíst datové sady a provádět v kurzu.

## <a name="see-also"></a>Další informace najdete v tématech
* [Přehled: Apache Spark v Azure HDInsight](apache-spark-overview.md)

### <a name="scenarios"></a>Scénáře
* [Apache Spark s BI: Provádějte interaktivní analýzy dat pomocí Sparku v HDInsight pomocí nástrojů BI](apache-spark-use-bi-tools.md)
* [Apache Spark s Machine Learning: Použití Sparku v HDInsight pro analýzu stavební teploty pomocí dat HVAC](apache-spark-ipython-notebook-machine-learning.md)
* [Apache Spark s Machine Learning: Použití Sparku v HDInsight k předpovědím výsledků kontroly potravin](apache-spark-machine-learning-mllib-ipython.md)
* [Analýza protokolu webu pomocí Apache Spark v HDInsight](apache-spark-custom-library-website-log-analysis.md)
* [Application Insight analýza dat telemetrie používat Apache Spark v HDInsight](apache-spark-analyze-application-insight-logs.md)

### <a name="create-and-run-applications"></a>Vytvoření a spouštění aplikací
* [Vytvoření samostatné aplikace pomocí Scala](apache-spark-create-standalone-application.md)
* [Vzdálené spouštění úloh na clusteru Apache Spark pomocí Apache Livy](apache-spark-livy-rest-interface.md)

### <a name="tools-and-extensions"></a>Nástroje a rozšíření
* [Modul plug-in nástroje HDInsight pro IntelliJ IDEA pro vytvoření a odesílání aplikací Spark Scala](apache-spark-intellij-tool-plugin.md)
* [Použití modulu plug-in nástroje HDInsight pro IntelliJ IDEA pro vzdálené ladění aplikací Apache Spark](apache-spark-intellij-tool-plugin-debug-jobs-remotely.md)
* [Použití poznámkových bloků Apache Zeppelin s clusterem Apache Spark v HDInsight](apache-spark-zeppelin-notebook.md)
* [Jádra dostupná pro poznámkový blok Jupyter v clusteru Apache Spark pro HDInsight](apache-spark-jupyter-notebook-kernels.md)
* [Použití externích balíčků s poznámkovými bloky Jupyter](apache-spark-jupyter-notebook-use-external-packages.md)
* [Instalace Jupyteru do počítače a připojení ke clusteru HDInsight Spark](apache-spark-jupyter-notebook-install-locally.md)

### <a name="manage-resources"></a>Správa prostředků
* [Správa prostředků v clusteru Apache Spark v Azure HDInsight](apache-spark-resource-manager.md)
* [Sledování a ladění úloh spuštěných v clusteru Apache Spark v HDInsight](apache-spark-job-debugging.md)

[hdinsight-versions]: hdinsight-component-versioning.md
[hdinsight-upload-data]: hdinsight-upload-data.md
[hdinsight-storage]: hdinsight-hadoop-use-blob-storage.md

[azure-purchase-options]: https://azure.microsoft.com/pricing/purchase-options/
[azure-member-offers]: https://azure.microsoft.com/pricing/member-offers/
[azure-free-trial]: https://azure.microsoft.com/pricing/free-trial/
[azure-create-storageaccount]:../../storage/common/storage-create-storage-account.md
