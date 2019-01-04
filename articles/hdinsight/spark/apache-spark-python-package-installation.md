---
title: Akce skriptu – instalace Pythonu balíčků s Jupyterem v Azure HDInsight
description: Podrobné pokyny o tom, jak konfigurace k dispozici poznámkové bloky Jupyter s clustery HDInsight Spark pomocí skriptových akcí můžete balíčky pythonu externí.
services: hdinsight
author: hrasheed-msft
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 11/06/2018
ms.author: hrasheed
ms.openlocfilehash: f804cfd693a37099edc22e7f4861d6d7e1af0fc7
ms.sourcegitcommit: e68df5b9c04b11c8f24d616f4e687fe4e773253c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/20/2018
ms.locfileid: "53651109"
---
# <a name="use-script-action-to-install-external-python-packages-for-jupyter-notebooks-in-apache-spark-clusters-on-hdinsight"></a>Použití akce skriptu k instalaci externích balíčků Python pro poznámkové bloky Jupyter v clusterech Apache Spark v HDInsight
> [!div class="op_single_selector"]
> * [Využitím magických příkazů](apache-spark-jupyter-notebook-use-external-packages.md)
> * [Pomocí akce skriptu](apache-spark-python-package-installation.md)

Zjistěte, jak pomocí skriptových akcí můžete nakonfigurovat [Apache Spark](https://spark.apache.org/) clusteru v HDInsight (Linux) používat externí, komunitou **python** balíčky, které nejsou součástí clusteru out-of-the-box.

> [!NOTE]  
> Můžete také nakonfigurovat poznámkového bloku Jupyter s použitím `%%configure` magic použití externích balíčků. Pokyny najdete v tématu [použití externích balíčků s poznámkovými bloky Jupyter v clusterech Apache Spark v HDInsight](apache-spark-jupyter-notebook-use-external-packages.md).

Můžete vyhledávat [indexu balíčků](https://pypi.python.org/pypi) pro úplný seznam balíčků, které jsou k dispozici. Seznam dostupných balíčků můžete získat také z jiných zdrojů. Například můžete nainstalovat balíčky k dispozici prostřednictvím [conda vytvoření](https://conda-forge.org/feedstocks/).

V tomto článku se dozvíte, jak nainstalovat [TensorFlow](https://www.tensorflow.org/) balíček pomocí akce skriptu na clusteru a jeho použití pomocí poznámkového bloku Jupyter jako příklad.

## <a name="prerequisites"></a>Požadavky
Musíte mít následující:

* Předplatné Azure. Viz [Získání bezplatné zkušební verze Azure](https://azure.microsoft.com/documentation/videos/get-azure-free-trial-for-testing-hadoop-in-hdinsight/).
* Cluster Apache Spark ve službě HDInsight. Pokyny najdete v tématu [Vytváření clusterů Apache Spark ve službě Azure HDInsight](apache-spark-jupyter-spark-sql.md).

   > [!NOTE]  
   > Pokud již nemáte cluster Spark v HDInsight Linux, můžete spustit skript akce při vytváření clusteru. Nahlédněte do dokumentace na [jak použít vlastní skript akce](https://docs.microsoft.com/azure/hdinsight/hdinsight-hadoop-customize-cluster-linux).
   
## <a name="support-for-open-source-software-used-on-hdinsight-clusters"></a>Podpora pro open source softwaru používaného v clusterech HDInsight

Služba Microsoft Azure HDInsight využívá ekosystém open source technologií formátovaných kolem Apache Hadoop. Microsoft Azure poskytuje obecné úroveň podpory pro open source technologie. Další informace najdete v tématu **rozsah podpory** část [nejčastější dotazy k podpoře Azure web](https://azure.microsoft.com/support/faq/). Služba HDInsight poskytuje další úroveň podpory pro integrované komponenty.

Existují dva druhy opensourcové komponenty, které jsou k dispozici ve službě HDInsight:

* **Integrované komponenty** – tyto součásti jsou předem nainstalované na clusterech HDInsight a poskytuje základní funkce clusteru. Například Apache Hadoop YARN ResourceManager, dotazovací jazyk Apache Hive (HiveQL) a knihovny Mahout patří do této kategorie. Úplný seznam součástí clusteru je k dispozici v [co je nového ve verzích clusterů systému Apache Hadoop poskytovaných službou HDInsight](https://docs.microsoft.com/azure/hdinsight/hdinsight-component-versioning).
* **Vlastní komponenty** -, jako uživatel clusteru, můžete nainstalovat nebo použít ve vašich úloh žádné součásti k dispozici v komunitě nebo vytvořené vámi.

> [!WARNING]   
> Součásti, které jsou součástí clusteru HDInsight jsou plně podporované. Microsoft Support pomáhá izolovat a vyřešit problémy týkající se těchto součástí.
>
> Vlastní komponenty získat obchodně přiměřenou podporu můžete-li dále řešit tento problém. Podpory Microsoftu může být schopni vyřešit problém nebo že vás může požádat o zapojení dostupné kanály pro open source technologie, ve kterých se nachází rozsáhlé znalosti pro tuto technologii. Existuje například mnoho komunitním webům, které lze použít jako: [Fórum na webu MSDN pro HDInsight](https://social.msdn.microsoft.com/Forums/azure/home?forum=hdinsight), [ https://stackoverflow.com ](https://stackoverflow.com). Také projektů Apache mít projektovým webům na [ https://apache.org ](https://apache.org), například: [Hadoop](https://hadoop.apache.org/).


## <a name="use-external-packages-with-jupyter-notebooks"></a>Použijte externí balíčky s poznámkovými bloky Jupyter

1. Z [Portálu Azure](https://portal.azure.com/) z úvodního panelu klikněte na dlaždici pro váš cluster Spark (pokud je připnutý na úvodní panel). Můžete také přejít na cluster pod položkou **Procházet vše** > **Clustery HDInsight**.   

2. Z okna clusteru Spark klikněte na tlačítko **akcí skriptů** v levém podokně. Použijte typ skriptu "Vlastní" a zadejte popisný název akce skriptu. Spuštění skriptu na **hlavní a pracovní uzly** a parametry pole ponechte prázdné. Skriptu bash můžete odkazovat z: https://hdiconfigactions.blob.core.windows.net/linuxtensorflow/tensorflowinstall.sh Nahlédněte do dokumentace na [jak použít vlastní skript akce](https://docs.microsoft.com/azure/hdinsight/hdinsight-hadoop-customize-cluster-linux).

   > [!NOTE]  
   > Existují dva python instalací v clusteru. Spark použije nachází v instalaci pythonu Anaconda `/usr/bin/anaconda/bin` a použije výchozí nastavení prostředí Pythonu 2.7. Chcete-li použít Python 3.x a nainstalovat balíčky v jádru PySpark3, cestu k `conda` spustitelný soubor pro tohoto prostředí a použití `-n` parametr zadat prostředí. Například příkaz `/usr/bin/anaconda/envs/py35/bin/conda install -c conda-forge ggplot -n py35`, nainstaluje `ggplot` balíček pomocí prostředí Python 3.5 `conda-forge` kanálu.

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
* [Apache Spark s BI: Provádějte interaktivní analýzy dat pomocí Sparku v HDInsight pomocí nástrojů BI](apache-spark-use-bi-tools.md)
* [Apache Spark s Machine Learning: Použití Sparku v HDInsight pro analýzu stavební teploty pomocí dat HVAC](apache-spark-ipython-notebook-machine-learning.md)
* [Apache Spark s Machine Learning: Použití Sparku v HDInsight k předpovědím výsledků kontroly potravin](apache-spark-machine-learning-mllib-ipython.md)
* [Analýza protokolu webu pomocí Apache Spark v HDInsight](apache-spark-custom-library-website-log-analysis.md)

### <a name="create-and-run-applications"></a>Vytvoření a spouštění aplikací
* [Vytvoření samostatné aplikace pomocí Scala](apache-spark-create-standalone-application.md)
* [Vzdálené spouštění úloh na clusteru Apache Spark pomocí Apache Livy](apache-spark-livy-rest-interface.md)

### <a name="tools-and-extensions"></a>Nástroje a rozšíření
* [Použití externích balíčků s poznámkovými bloky Jupyter v clusterech Apache Spark v HDInsight](apache-spark-jupyter-notebook-use-external-packages.md)
* [Modul plug-in nástroje HDInsight pro IntelliJ IDEA pro vytvoření a odesílání aplikací Spark Scala](apache-spark-intellij-tool-plugin.md)
* [Použití modulu plug-in nástroje HDInsight pro IntelliJ IDEA pro vzdálené ladění aplikací Apache Spark](apache-spark-intellij-tool-plugin-debug-jobs-remotely.md)
* [Použití poznámkových bloků Apache Zeppelin s clusterem Apache Spark v HDInsight](apache-spark-zeppelin-notebook.md)
* [Jádra dostupná pro poznámkový blok Jupyter v clusteru Apache Spark pro HDInsight](apache-spark-jupyter-notebook-kernels.md)
* [Instalace Jupyteru do počítače a připojení ke clusteru HDInsight Spark](apache-spark-jupyter-notebook-install-locally.md)

### <a name="manage-resources"></a>Správa prostředků
* [Správa prostředků v clusteru Apache Spark v Azure HDInsight](apache-spark-resource-manager.md)
* [Sledování a ladění úloh spuštěných v clusteru Apache Spark v HDInsight](apache-spark-job-debugging.md)
