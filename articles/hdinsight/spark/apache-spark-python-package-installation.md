---
title: Akce skriptu – instalace Pythonu balíčků s Jupyterem v Azure HDInsight
description: Podrobné pokyny o tom, jak konfigurace k dispozici poznámkové bloky Jupyter s clustery HDInsight Spark pomocí skriptových akcí můžete balíčky pythonu externí.
services: hdinsight
author: jasonwhowell
editor: jasonwhowell
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 01/09/2018
ms.author: jasonh
ms.openlocfilehash: 36e727a59b91303c8c62c5525f72c328e2792ad6
ms.sourcegitcommit: 35ceadc616f09dd3c88377a7f6f4d068e23cceec
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/08/2018
ms.locfileid: "39619171"
---
# <a name="use-script-action-to-install-external-python-packages-for-jupyter-notebooks-in-apache-spark-clusters-on-hdinsight"></a>Použití akce skriptu k instalaci externích balíčků Python pro poznámkové bloky Jupyter v clusterech Apache Spark v HDInsight
> [!div class="op_single_selector"]
> * [Využitím magických příkazů](apache-spark-jupyter-notebook-use-external-packages.md)
> * [Pomocí akce skriptu](apache-spark-python-package-installation.md)
>
>

Zjistěte, jak pomocí skriptových akcí můžete nakonfigurovat cluster Apache Spark v HDInsight (Linux) používat externí, komunitou **python** balíčky, které nejsou součástí clusteru out-of-the-box.

> [!NOTE]
> Můžete také nakonfigurovat poznámkového bloku Jupyter s použitím `%%configure` magic použití externích balíčků. Pokyny najdete v tématu [použití externích balíčků s poznámkovými bloky Jupyter v clusterech Apache Spark v HDInsight](apache-spark-jupyter-notebook-use-external-packages.md).
> 
> 

Můžete vyhledávat [indexu balíčků](https://pypi.python.org/pypi) pro úplný seznam balíčků, které jsou k dispozici. Seznam dostupných balíčků můžete získat také z jiných zdrojů. Například můžete nainstalovat balíčky k dispozici prostřednictvím [Anaconda](https://docs.continuum.io/anaconda/pkg-docs) nebo [conda vytvoření](https://conda-forge.org/feedstocks/).

V tomto článku se dozvíte, jak nainstalovat [TensorFlow](https://www.tensorflow.org/) balíček pomocí akce skriptu na clusteru a jeho použití pomocí poznámkového bloku Jupyter.

## <a name="prerequisites"></a>Požadavky
Musíte mít následující:

* Předplatné Azure. Viz [Získání bezplatné zkušební verze Azure](https://azure.microsoft.com/documentation/videos/get-azure-free-trial-for-testing-hadoop-in-hdinsight/).
* Cluster Apache Spark ve službě HDInsight. Pokyny najdete v tématu [Vytváření clusterů Apache Spark ve službě Azure HDInsight](apache-spark-jupyter-spark-sql.md).

   > [!NOTE]
   > Pokud již nemáte cluster Spark v HDInsight Linux, můžete spustit skript akce při vytváření clusteru. Nahlédněte do dokumentace na [jak použít vlastní skript akce](https://docs.microsoft.com/azure/hdinsight/hdinsight-hadoop-customize-cluster-linux).
   > 
   > 

## <a name="use-external-packages-with-jupyter-notebooks"></a>Použijte externí balíčky s poznámkovými bloky Jupyter

1. Z [Portálu Azure](https://portal.azure.com/) z úvodního panelu klikněte na dlaždici pro váš cluster Spark (pokud je připnutý na úvodní panel). Můžete také přejít na cluster pod položkou **Procházet vše** > **Clustery HDInsight**.   

2. Z okna clusteru Spark klikněte na tlačítko **akcí skriptů** v levém podokně. Spuštění vlastní akce, který se instaluje TensorFlow hlavní uzly a uzly pracovního procesu. Skriptu bash můžete odkazovat z: https://hdiconfigactions.blob.core.windows.net/linuxtensorflow/tensorflowinstall.sh nahlédněte do dokumentace na [jak použít vlastní skript akce](https://docs.microsoft.com/azure/hdinsight/hdinsight-hadoop-customize-cluster-linux).

   > [!NOTE]
   > Existují dva python instalací v clusteru. Spark použije nachází v instalaci pythonu Anaconda `/usr/bin/anaconda/bin`. Odkazovat na instalaci ve vaší vlastní akce prostřednictvím `/usr/bin/anaconda/bin/pip` a `/usr/bin/anaconda/bin/conda`.
   > 
   > 

3. Otevřete Poznámkový blok PySpark Jupyter

    ![Vytvoření nového poznámkového bloku Jupyter](./media/apache-spark-python-package-installation/hdinsight-spark-create-notebook.png "Vytvoření nového poznámkového bloku Jupyter")

4. Nový poznámkový blok se vytvoří a otevře s názvem Untitled.pynb. Klikněte na název poznámkového bloku v horní části a zadejte popisný název.

    ![Zadání názvu poznámkového bloku](./media/apache-spark-python-package-installation/hdinsight-spark-name-notebook.png "Zadání názvu poznámkového bloku")

5. Teď budete `import tensorflow` a spustit příklad hello world. 

    Kód ke kopírování:

        import tensorflow as tf
        hello = tf.constant('Hello, TensorFlow!')
        sess = tf.Session()
        print(sess.run(hello))

    Výsledek vypadá takto:
    
    ![Spuštění kódu TensorFlow](./media/apache-spark-python-package-installation/execution.png "TensorFlow spuštění kódu")

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
* [Použití externích balíčků s poznámkovými bloky Jupyter v clusterech Apache Spark v HDInsight](apache-spark-jupyter-notebook-use-external-packages.md)
* [Modul plug-in nástroje HDInsight pro IntelliJ IDEA pro vytvoření a odesílání aplikací Spark Scala](apache-spark-intellij-tool-plugin.md)
* [Použití modulu plug-in nástroje HDInsight pro IntelliJ IDEA pro vzdálené ladění aplikací Spark](apache-spark-intellij-tool-plugin-debug-jobs-remotely.md)
* [Použití poznámkových bloků Zeppelin s clusterem Sparku v HDInsight](apache-spark-zeppelin-notebook.md)
* [Jádra dostupná pro poznámkový blok Jupyter v clusteru Sparku pro HDInsight](apache-spark-jupyter-notebook-kernels.md)
* [Instalace Jupyteru do počítače a připojení ke clusteru HDInsight Spark](apache-spark-jupyter-notebook-install-locally.md)

### <a name="manage-resources"></a>Správa prostředků
* [Správa prostředků v clusteru Apache Spark v Azure HDInsight](apache-spark-resource-manager.md)
* [Sledování a ladění úloh spuštěných v clusteru Apache Spark v HDInsight](apache-spark-job-debugging.md)
