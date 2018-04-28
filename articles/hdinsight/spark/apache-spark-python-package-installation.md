---
title: Skript akce – instalace Python balíčky s Jupyter v Azure HDInsight | Microsoft Docs
description: Podrobné pokyny o tom, jak nakonfigurovat k dispozici poznámkové bloky Jupyter s clustery HDInsight Spark pomocí skriptu akce použití externí python balíčků.
services: hdinsight
documentationcenter: ''
author: nitinme
manager: jhubbard
editor: cgronlun
tags: azure-portal
ms.assetid: 21978b71-eb53-480b-a3d1-c5d428a7eb5b
ms.service: hdinsight
ms.custom: hdinsightactive
ms.devlang: na
ms.topic: conceptual
ms.date: 01/09/2018
ms.author: nitinme
ms.openlocfilehash: 4d9d1e0aaf6a1c0155f9ab74a5e63302635a0c11
ms.sourcegitcommit: 1362e3d6961bdeaebed7fb342c7b0b34f6f6417a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/18/2018
---
# <a name="use-script-action-to-install-external-python-packages-for-jupyter-notebooks-in-apache-spark-clusters-on-hdinsight"></a>Použití akce skriptu k instalaci externích balíčků Python pro poznámkové bloky Jupyter v clusterech Apache Spark v HDInsight
> [!div class="op_single_selector"]
> * [Pomocí buňky magic](apache-spark-jupyter-notebook-use-external-packages.md)
> * [Pomocí akce skriptu](apache-spark-python-package-installation.md)
>
>

Zjistěte, jak pomocí skriptových akcí můžete nakonfigurovat cluster Apache Spark v HDInsight (Linux) používat externí, komunity podílí **python** balíčky, které nejsou zahrnuty v clusteru se na pole.

> [!NOTE]
> Můžete také nakonfigurovat poznámkového bloku Jupyter pomocí `%%configure` magic používat externí balíčky. Pokyny najdete v tématu [použijte externí balíčky s poznámkovými bloky Jupyter v clusterech Apache Spark v HDInsight](apache-spark-jupyter-notebook-use-external-packages.md).
> 
> 

Můžete hledat [indexu balíčků](https://pypi.python.org/pypi) úplný seznam balíčků, které jsou k dispozici. Seznam dostupných balíčků můžete také získat z jiných zdrojů. Například můžete nainstalovat balíčky, které jsou k dispozici prostřednictvím [Anaconda](https://docs.continuum.io/anaconda/pkg-docs) nebo [conda vytvoření](https://conda-forge.org/feedstocks/).

V tomto článku se dozvíte, jak nainstalovat [TensorFlow](https://www.tensorflow.org/) balíček pomocí akce skriptu v clusteru a použít ho pomocí poznámkového bloku Jupyter.

## <a name="prerequisites"></a>Požadavky
Musíte mít následující:

* Předplatné Azure. Viz [Získání bezplatné zkušební verze Azure](https://azure.microsoft.com/documentation/videos/get-azure-free-trial-for-testing-hadoop-in-hdinsight/).
* Cluster Apache Spark v HDInsight. Pokyny najdete v tématu [clusterů vytvořit Apache Spark v Azure HDInsight](apache-spark-jupyter-spark-sql.md).

   > [!NOTE]
   > Pokud už cluster Spark na HDInsight Linux nemáte, můžete spustit skript akce při vytváření clusteru. Najdete v dokumentaci na [postup použít vlastní skript akce](https://docs.microsoft.com/azure/hdinsight/hdinsight-hadoop-customize-cluster-linux).
   > 
   > 

## <a name="use-external-packages-with-jupyter-notebooks"></a>Použijte externí balíčky s poznámkovými bloky Jupyter

1. Z [Portálu Azure](https://portal.azure.com/) z úvodního panelu klikněte na dlaždici pro váš cluster Spark (pokud je připnutý na úvodní panel). Můžete také přejít na cluster pod položkou **Procházet vše** > **Clustery HDInsight**.   

2. Z okna clusteru Spark klikněte na tlačítko **akcí skriptů** v levém podokně. Spuštění vlastní akce, který se instaluje TensorFlow head uzlů a uzly pracovního procesu. Skript bash lze odkazovat z: https://hdiconfigactions.blob.core.windows.net/linuxtensorflow/tensorflowinstall.sh najdete v dokumentaci na [postup použít vlastní skript akce](https://docs.microsoft.com/azure/hdinsight/hdinsight-hadoop-customize-cluster-linux).

   > [!NOTE]
   > Existují dvě python instalace v clusteru. Spark použije instalace python Anaconda nacházející se v `/usr/bin/anaconda/bin`. Referenční instalaci ve vaší vlastní akce prostřednictvím `/usr/bin/anaconda/bin/pip` a `/usr/bin/anaconda/bin/conda`.
   > 
   > 

3. Otevřete Poznámkový blok PySpark Jupyter

    ![Vytvoření nového poznámkového bloku Jupyter](./media/apache-spark-python-package-installation/hdinsight-spark-create-notebook.png "Vytvoření nového poznámkového bloku Jupyter")

4. Nový poznámkový blok se vytvoří a otevře s názvem Untitled.pynb. Klikněte na název poznámkového bloku v horní části a zadejte popisný název.

    ![Zadání názvu poznámkového bloku](./media/apache-spark-python-package-installation/hdinsight-spark-name-notebook.png "Zadání názvu poznámkového bloku")

5. Nyní budete `import tensorflow` a spusťte hello world – ukázka. 

    Kód zkopírovat:

        import tensorflow as tf
        hello = tf.constant('Hello, TensorFlow!')
        sess = tf.Session()
        print(sess.run(hello))

    Výsledek vypadá takto:
    
    ![Provádění kódu TensorFlow](./media/apache-spark-python-package-installation/execution.png "TensorFlow spuštění kódu")

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
* [Použijte externí balíčky s poznámkovými bloky Jupyter v clusterech Apache Spark v HDInsight](apache-spark-jupyter-notebook-use-external-packages.md)
* [Modul plug-in nástroje HDInsight pro IntelliJ IDEA pro vytvoření a odesílání aplikací Spark Scala](apache-spark-intellij-tool-plugin.md)
* [Použití modulu plug-in nástroje HDInsight pro IntelliJ IDEA pro vzdálené ladění aplikací Spark](apache-spark-intellij-tool-plugin-debug-jobs-remotely.md)
* [Použití poznámkových bloků Zeppelin s clusterem Sparku v HDInsight](apache-spark-zeppelin-notebook.md)
* [Jádra dostupná pro poznámkový blok Jupyter v clusteru Sparku pro HDInsight](apache-spark-jupyter-notebook-kernels.md)
* [Instalace Jupyteru do počítače a připojení ke clusteru HDInsight Spark](apache-spark-jupyter-notebook-install-locally.md)

### <a name="manage-resources"></a>Správa prostředků
* [Správa prostředků v clusteru Apache Spark v Azure HDInsight](apache-spark-resource-manager.md)
* [Sledování a ladění úloh spuštěných v clusteru Apache Spark v HDInsight](apache-spark-job-debugging.md)
