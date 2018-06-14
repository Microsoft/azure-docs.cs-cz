---
title: Sada nástrojů pro kognitivní s Azure HDInsight Spark pro přímý learning | Microsoft Docs
description: Zjistěte, jak lze použít model natrénujete hloubkové learning kognitivní nástrojů Microsoft pro datovou sadu pomocí rozhraní API Python Spark v clusteru Azure HDInsight Spark.
services: hdinsight
documentationcenter: ''
author: mumian
manager: jhubbard
editor: cgronlun
tags: azure-portal
ms.service: hdinsight
ms.custom: hdinsightactive
ms.devlang: na
ms.topic: conceptual
ms.date: 11/28/2017
ms.author: jgao
ms.openlocfilehash: 7afb891642e3e53da5eb1e17ee654fb5fb42c313
ms.sourcegitcommit: 1362e3d6961bdeaebed7fb342c7b0b34f6f6417a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/18/2018
ms.locfileid: "31518531"
---
# <a name="use-microsoft-cognitive-toolkit-deep-learning-model-with-azure-hdinsight-spark-cluster"></a>Použít Microsoft kognitivní Toolkit hloubkové učení modelu s clusteru Azure HDInsight Spark

V tomto článku proveďte následující kroky.

1. Spuštění vlastního skriptu k instalaci Microsoft kognitivní Toolkit v clusteru Azure HDInsight Spark.

2. Nahrajte do clusteru Spark chcete zjistit, jak použít modulu trained model Microsoft kognitivní Toolkit hloubkové learning do souborů k účtu úložiště Azure Blob pomocí poznámkového bloku Jupyter [API Python Spark (PySpark)](https://spark.apache.org/docs/0.9.0/python-programming-guide.html)

## <a name="prerequisites"></a>Požadavky

* **Předplatné Azure**. Než začnete tento kurz, musíte mít předplatné Azure. Přečtěte si téma [Bezplatné vytvoření účtu Microsoft Azure ještě dnes](https://azure.microsoft.com/free).

* **Cluster Azure HDInsight Spark**. V tomto článku vytvořte cluster Spark 2.0. Pokyny najdete v tématu [cluster vytvořit Apache Spark v Azure HDInsight](apache-spark-jupyter-spark-sql.md).

## <a name="how-does-this-solution-flow"></a>Jak toto řešení tok?

Toto řešení je rozděleno mezi v tomto článku a který v rámci tohoto kurzu nahrajete poznámkového bloku Jupyter. V tomto článku je provést následující kroky:

* Spuštěním skriptu akce na clusteru HDInsight Spark instalace Microsoft kognitivní Toolkit a balíčků Python.
* Nahrajte poznámkového bloku Jupyter, který spouští řešení do clusteru HDInsight Spark.

Následující zbývající kroky jsou popsané v poznámkového bloku Jupyter.

- Načíst ukázková bitové kopie do Spark Resiliant distribuované datové sady nebo RDD
   - Načtení moduly a definování přednastavení
   - Stáhnout datovou sadu místně na clusteru Spark
   - Převést datová sada RDD
- Stanovení skóre obrázky pomocí modulu trained model kognitivní Toolkit
   - Stahovat do clusteru Spark pro cvičný model kognitivní Toolkit
   - Definování funkcí, které má být používána uzlů pracovního procesu
   - Stanovení skóre bitové kopie na uzly pracovního procesu
   - Vyhodnocení přesnosti modelu


## <a name="install-microsoft-cognitive-toolkit"></a>Nainstalovat sadu Microsoft kognitivní Toolkit

Sada nástrojů pro kognitivní můžete nainstalovat na clusteru Spark pomocí akce skriptu. Akce skriptu používá vlastní skripty k instalaci součásti v clusteru, které nejsou k dispozici ve výchozím nastavení. Pomocí HDInsight .NET SDK, nebo pomocí prostředí Azure PowerShell, můžete použít vlastní skript z portálu Azure. Tento skript můžete použít také k instalaci sady nástrojů buď jako součást vytváření clusteru, nebo po nastavení a spuštění clusteru. 

V tomto článku používáme portálu k instalaci sady nástrojů, po vytvoření clusteru. Další způsoby vlastního skriptu, najdete v části [HDInsight přizpůsobit clustery pomocí akce skriptu](../hdinsight-hadoop-customize-cluster-linux.md).

### <a name="using-the-azure-portal"></a>Použití Portálu Azure

Pokyny o tom, jak pomocí portálu Azure ke spuštění akce skriptu najdete v tématu [HDInsight přizpůsobit clustery pomocí akce skriptu](../hdinsight-hadoop-customize-cluster-linux.md#use-a-script-action-during-cluster-creation). Ujistěte se, že zadáte následující vstupy pro instalaci Microsoft kognitivní Toolkit.

* Zadejte hodnotu pro název akce skriptu.

* Pro **Bash skriptu URI**, zadejte `https://raw.githubusercontent.com/Azure-Samples/hdinsight-pyspark-cntk-integration/master/cntk-install.sh`.

* Zajistěte, aby spuštění skriptu pouze na head a pracovní uzly a zrušte zaškrtnutí všech políček.

* Klikněte na možnost **Vytvořit**.

## <a name="upload-the-jupyter-notebook-to-azure-hdinsight-spark-cluster"></a>Nahrajte do clusteru Azure HDInsight Spark poznámkového bloku Jupyter

Pokud chcete používat Microsoft kognitivní Toolkit s clusteru Azure HDInsight Spark, je nutné načíst poznámkového bloku Jupyter **CNTK_model_scoring_on_Spark_walkthrough.ipynb** do clusteru Azure HDInsight Spark. Tento poznámkový blok je k dispozici na webu GitHub na [ https://github.com/Azure-Samples/hdinsight-pyspark-cntk-integration ](https://github.com/Azure-Samples/hdinsight-pyspark-cntk-integration).

1. Naklonujte úložiště GitHub [ https://github.com/Azure-Samples/hdinsight-pyspark-cntk-integration ](https://github.com/Azure-Samples/hdinsight-pyspark-cntk-integration). Pokyny ke klonování najdete v tématu [klonování úložiště](https://help.github.com/articles/cloning-a-repository/).

2. Z portálu Azure, otevřete okno clusteru Spark, které jste už zřízené, klikněte na tlačítko **řídicí panel clusteru**a potom klikněte na **Poznámkový blok Jupyter**.

    Můžete také spustit poznámkového bloku Jupyter tak, že přejdete na adresu URL `https://<clustername>.azurehdinsight.net/jupyter/`. Nahraďte \<clustername > s názvem clusteru HDInsight.

3. Z poznámkového bloku Jupyter, klikněte na tlačítko **nahrát** v pravém horním rohu a pak přejděte do umístění, které jste naklonovali úložiště GitHub.

    ![Nahrát Poznámkový blok Jupyter ke clusteru Azure HDInsight Spark](./media/apache-spark-microsoft-cognitive-toolkit/hdinsight-microsoft-cognitive-toolkit-load-jupyter-notebook.png "Poznámkový blok Jupyter nahrát do clusteru Azure HDInsight Spark")

4. Klikněte na tlačítko **nahrát** znovu.

5. Po odeslání poznámkového bloku, klikněte na název poznámkového bloku a pak postupujte podle pokynů v samotné poznámkového bloku o tom, jak načíst sadu dat a proveďte tento kurz.

## <a name="see-also"></a>Další informace najdete v tématech
* [Přehled: Apache Spark v Azure HDInsight](apache-spark-overview.md)

### <a name="scenarios"></a>Scénáře
* [Spark s BI: Provádějte interaktivní analýzy dat pomocí Sparku v HDInsight pomocí nástrojů BI](apache-spark-use-bi-tools.md)
* [Spark s Machine Learning: Používejte Spark v HDInsight pro analýzu teploty v budově pomocí dat HVAC](apache-spark-ipython-notebook-machine-learning.md)
* [Spark s Machine Learning: Používejte Spark v HDInsight k předpovědím výsledků kontrol potravin](apache-spark-machine-learning-mllib-ipython.md)
* [Analýza protokolu webu pomocí Sparku v HDInsight](apache-spark-custom-library-website-log-analysis.md)
* [Analýza dat telemetrie Application Insights pomocí Sparku v HDInsight](apache-spark-analyze-application-insight-logs.md)

### <a name="create-and-run-applications"></a>Vytvoření a spouštění aplikací
* [Vytvoření samostatné aplikace pomocí Scala](apache-spark-create-standalone-application.md)
* [Vzdálené spouštění úloh na clusteru Sparku pomocí Livy](apache-spark-livy-rest-interface.md)

### <a name="tools-and-extensions"></a>Nástroje a rozšíření
* [Modul plug-in nástroje HDInsight pro IntelliJ IDEA pro vytvoření a odesílání aplikací Spark Scala](apache-spark-intellij-tool-plugin.md)
* [Použití modulu plug-in nástroje HDInsight pro IntelliJ IDEA pro vzdálené ladění aplikací Spark](apache-spark-intellij-tool-plugin-debug-jobs-remotely.md)
* [Použití poznámkových bloků Zeppelin s clusterem Sparku v HDInsight](apache-spark-zeppelin-notebook.md)
* [Jádra dostupná pro poznámkový blok Jupyter v clusteru Sparku pro HDInsight](apache-spark-jupyter-notebook-kernels.md)
* [Použití externích balíčků s poznámkovými bloky Jupyter](apache-spark-jupyter-notebook-use-external-packages.md)
* [Instalace Jupyteru do počítače a připojení ke clusteru HDInsight Spark](apache-spark-jupyter-notebook-install-locally.md)

### <a name="manage-resources"></a>Správa prostředků
* [Správa prostředků v clusteru Apache Spark v Azure HDInsight](apache-spark-resource-manager.md)
* [Sledování a ladění úloh spuštěných v clusteru Apache Spark v HDInsight](apache-spark-job-debugging.md)

[hdinsight-versions]: hdinsight-component-versioning.md
[hdinsight-upload-data]: hdinsight-upload-data.md
[hdinsight-storage]: hdinsight-hadoop-use-blob-storage.md

[azure-purchase-options]: http://azure.microsoft.com/pricing/purchase-options/
[azure-member-offers]: http://azure.microsoft.com/pricing/member-offers/
[azure-free-trial]: http://azure.microsoft.com/pricing/free-trial/
[azure-create-storageaccount]:../../storage/common/storage-create-storage-account.md
