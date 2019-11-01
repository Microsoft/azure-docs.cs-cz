---
title: Microsoft Cognitive Toolkit s Apache Spark – Azure HDInsight
description: Seznamte se s tím, jak se dá získat školicí Microsoft Cognitive Toolkit model hloubkového učení použít pro datovou sadu pomocí rozhraní Spark Python API v clusteru Azure HDInsight Spark.
author: hrasheed-msft
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 11/28/2017
ms.author: hrasheed
ms.openlocfilehash: 0f4172c7a5b287c85c0548c7fe9812305a1ee1e6
ms.sourcegitcommit: 3486e2d4eb02d06475f26fbdc321e8f5090a7fac
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/31/2019
ms.locfileid: "73241530"
---
# <a name="use-microsoft-cognitive-toolkit-deep-learning-model-with-azure-hdinsight-spark-cluster"></a>Použití Microsoft Cognitive Toolkit modelu hloubkového učení s clusterem Azure HDInsight Spark

V tomto článku provedete následující kroky.

1. Spusťte vlastní skript, který nainstaluje [Microsoft Cognitive Toolkit](https://www.microsoft.com/en-us/cognitive-toolkit/) do clusteru Azure HDInsight Spark.

2. Nahrajte [Jupyter notebook](https://jupyter.org/) do clusteru [Apache Spark](https://spark.apache.org/) , abyste zjistili, jak použít školicí model Microsoft Cognitive Toolkit hloubkového učení do souborů v účtu Azure Blob Storage pomocí [rozhraní Spark Python API (PySpark)](https://spark.apache.org/docs/0.9.0/python-programming-guide.html) .

## <a name="prerequisites"></a>Předpoklady

* **Předplatné Azure**. Než začnete tento článek, musíte mít předplatné Azure. Přečtěte si téma [Bezplatné vytvoření účtu Microsoft Azure ještě dnes](https://azure.microsoft.com/free).

* **Cluster Azure HDInsight Spark**. V tomto článku vytvořte cluster Spark 2,0. Pokyny najdete v tématu [Vytvoření clusteru Apache Spark ve službě Azure HDInsight](apache-spark-jupyter-spark-sql.md).

## <a name="how-does-this-solution-flow"></a>Jak toto řešení pokračuje?

Toto řešení je rozdělené mezi Tento článek a Jupyter Poznámkový blok, který jste nahráli v rámci tohoto článku. V tomto článku provedete následující kroky:

* Spusťte akci skriptu v clusteru HDInsight Spark a nainstalujte Microsoft Cognitive Toolkit a balíčky Pythonu.
* Nahrajte Poznámkový blok Jupyter, který spouští řešení, do clusteru HDInsight Spark.

Do poznámkového bloku Jupyter se zabývá následující zbývající kroky.

- Načtěte vzorové obrázky do distribuované datové sady Spark Resiliant nebo RDD.
   - Načtěte moduly a definujte přednastavení.
   - Stáhněte si datovou sadu lokálně na clusteru Spark.
   - Převést datovou sadu na RDD.
- Vyhodnotit obrázky pomocí trained Cognitive Toolkit modelu.
   - Stáhněte si vyškolený Cognitive Toolkit model do clusteru Spark.
   - Definujte funkce, které budou používány pracovními uzly.
   - Skóre obrázků v pracovních uzlech.
   - Vyhodnotit přesnost modelu.


## <a name="install-microsoft-cognitive-toolkit"></a>Nainstalovat Microsoft Cognitive Toolkit

Můžete nainstalovat Microsoft Cognitive Toolkit v clusteru Spark pomocí akce skriptu. Akce skriptu používá vlastní skripty k instalaci komponent do clusteru, které nejsou ve výchozím nastavení k dispozici. Vlastní skript můžete použít z Azure Portal, pomocí sady HDInsight .NET SDK nebo pomocí Azure PowerShell. Skript můžete také použít k instalaci sady nástrojů buď jako součást vytváření clusteru, nebo po zprovoznění a spuštění clusteru. 

V tomto článku používáme portál k instalaci sady Toolkit po vytvoření clusteru. Další způsoby, jak spustit vlastní skript, najdete v tématu [Přizpůsobení clusterů HDInsight pomocí akce skriptu](../hdinsight-hadoop-customize-cluster-linux.md).

### <a name="using-the-azure-portal"></a>Použití webu Azure Portal

Pokyny, jak pomocí Azure Portal spustit akci skriptu, najdete v tématu [Přizpůsobení clusterů HDInsight pomocí akce skriptu](../hdinsight-hadoop-customize-cluster-linux.md#use-a-script-action-during-cluster-creation). Ujistěte se, že zadáváte následující vstupy pro instalaci Microsoft Cognitive Toolkit.

* Zadejte hodnotu pro název akce skriptu.

* V případě **identifikátoru URI bash skriptu**zadejte `https://raw.githubusercontent.com/Azure-Samples/hdinsight-pyspark-cntk-integration/master/cntk-install.sh`.

* Ujistěte se, že jste spustili skript jenom na uzlech hlav a Worker, a zrušte zaškrtnutí všech ostatních políček.

* Klikněte na **Vytvořit**.

## <a name="upload-the-jupyter-notebook-to-azure-hdinsight-spark-cluster"></a>Nahrání poznámkového bloku Jupyter do clusteru Azure HDInsight Spark

Chcete-li použít Microsoft Cognitive Toolkit s clusterem Azure HDInsight Spark, je nutné do clusteru Azure HDInsight Spark načíst **CNTK_model_scoring_on_Spark_walkthrough. ipynb** Poznámkový blok Jupyter. Tento Poznámkový blok je k dispozici na GitHubu na adrese [https://github.com/Azure-Samples/hdinsight-pyspark-cntk-integration](https://github.com/Azure-Samples/hdinsight-pyspark-cntk-integration).

1. Naklonujte [https://github.com/Azure-Samples/hdinsight-pyspark-cntk-integration](https://github.com/Azure-Samples/hdinsight-pyspark-cntk-integration)úložiště GitHub. Pokyny k klonování najdete v tématu [klonování úložiště](https://help.github.com/articles/cloning-a-repository/).

2. V Azure Portal otevřete okno clusteru Spark, které jste už zřídili, klikněte na **řídicí panel clusteru**a pak klikněte na **Poznámkový blok Jupyter**.

    Poznámkový blok Jupyter můžete také spustit tak, že přejdete na adresu URL `https://<clustername>.azurehdinsight.net/jupyter/`. > \<název_clusteru nahraďte názvem vašeho clusteru HDInsight.

3. V poznámkovém bloku Jupyter klikněte na **nahrát** v pravém horním rohu a pak přejděte do umístění, kam jste naklonoval úložiště GitHub.

    ![Nahrání poznámkového bloku Jupyter do clusteru Azure HDInsight Spark](./media/apache-spark-microsoft-cognitive-toolkit/hdinsight-microsoft-cognitive-toolkit-load-jupyter-notebook.png "Nahrání poznámkového bloku Jupyter do clusteru Azure HDInsight Spark")

4. Znovu klikněte na **nahrát** .

5. Po nahrání poznámkového bloku klikněte na název poznámkového bloku a pak postupujte podle pokynů v samotném poznámkovém bloku, jak načíst datovou sadu a provést tento článek.

## <a name="see-also"></a>Další informace najdete v tématech
* [Přehled: Apache Spark v Azure HDInsight](apache-spark-overview.md)

### <a name="scenarios"></a>Scénáře
* [Apache Spark s BI: provádějte interaktivní analýzy dat pomocí Sparku v HDInsight pomocí nástrojů BI.](apache-spark-use-bi-tools.md)
* [Apache Spark s Machine Learning: pomocí Sparku v HDInsight můžete analyzovat teplotu budovy pomocí dat TVK.](apache-spark-ipython-notebook-machine-learning.md)
* [Apache Spark s Machine Learning: pomocí Sparku v HDInsight předpovídat výsledky kontroly potravin](apache-spark-machine-learning-mllib-ipython.md)
* [Analýza webového protokolu pomocí Apache Spark ve službě HDInsight](apache-spark-custom-library-website-log-analysis.md)
* [Analýza dat telemetrie Application Insight pomocí Apache Spark ve službě HDInsight](apache-spark-analyze-application-insight-logs.md)

### <a name="create-and-run-applications"></a>Vytvoření a spouštění aplikací
* [Vytvoření samostatné aplikace pomocí Scala](apache-spark-create-standalone-application.md)
* [Vzdálené spouštění úloh na clusteru Apache Spark s využitím Apache Livy](apache-spark-livy-rest-interface.md)

### <a name="tools-and-extensions"></a>Nástroje a rozšíření
* [Modul plug-in nástroje HDInsight pro IntelliJ IDEA pro vytvoření a odesílání aplikací Spark Scala](apache-spark-intellij-tool-plugin.md)
* [Použití modulu plug-in nástrojů HDInsight pro IntelliJ NÁPADu při vzdáleném ladění aplikací Apache Spark](apache-spark-intellij-tool-plugin-debug-jobs-remotely.md)
* [Použití poznámkových bloků Apache Zeppelin s clusterem Apache Spark v HDInsight](apache-spark-zeppelin-notebook.md)
* [Jádra dostupná pro Poznámkový blok Jupyter v clusteru Apache Spark pro HDInsight](apache-spark-jupyter-notebook-kernels.md)
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
