---
title: Akce skriptu pro balíčky Pythonu s Jupyter ve službě Azure HDInsight
description: Podrobné pokyny, jak pomocí akce skriptu nakonfigurovat Jupyter poznámkové bloky dostupné v clusterech HDInsight Spark, aby používaly externí balíčky Pythonu.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.date: 11/05/2019
ms.openlocfilehash: e344035f05e192de1779a60fc99a7e0144566654
ms.sourcegitcommit: 609d4bdb0467fd0af40e14a86eb40b9d03669ea1
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/06/2019
ms.locfileid: "73682185"
---
# <a name="script-action-to-install-external-python-packages-for-jupyter-notebooks-in-apache-spark-on-hdinsight"></a>Akce skriptu pro instalaci externích balíčků Pythonu pro Jupyter poznámkové bloky v Apache Spark ve službě HDInsight

> [!div class="op_single_selector"]
> * [Použití buňky Magic](apache-spark-jupyter-notebook-use-external-packages.md)
> * [Pomocí akce skriptu](apache-spark-python-package-installation.md)

Naučte se, jak pomocí akcí skriptů nakonfigurovat cluster [Apache Spark](https://spark.apache.org/) ve službě HDInsight tak, aby používal externí balíčky **Pythonu** , které se podílejí na komunitě, které nejsou zahrnuté do clusteru.

> [!NOTE]  
> Jupyter Poznámkový blok můžete nakonfigurovat také pomocí `%%configure` Magic pro použití externích balíčků. Pokyny najdete v tématu [použití externích balíčků s Jupyter poznámkovým blokům v Apache Spark clusterech v HDInsight](apache-spark-jupyter-notebook-use-external-packages.md).

Úplný seznam balíčků, které jsou k dispozici, můžete vyhledat v [indexu balíčku](https://pypi.python.org/pypi) . Můžete také získat seznam dostupných balíčků z jiných zdrojů. Balíčky, které jsou k dispozici například, můžete nainstalovat pomocí [conda-zfalšovat](https://conda-forge.org/feedstocks/).

V tomto článku se dozvíte, jak nainstalovat balíček [TensorFlow](https://www.tensorflow.org/) pomocí akce skriptu v clusteru a jak ho použít jako příklad prostřednictvím poznámkového bloku Jupyter.

## <a name="prerequisites"></a>Požadavky

* Předplatné Azure. Viz [Získání bezplatné zkušební verze Azure](https://azure.microsoft.com/documentation/videos/get-azure-free-trial-for-testing-hadoop-in-hdinsight/).

* Cluster Apache Spark ve službě HDInsight. Pokyny najdete v tématu [Vytváření clusterů Apache Spark ve službě Azure HDInsight](apache-spark-jupyter-spark-sql.md).

   > [!NOTE]  
   > Pokud ještě nemáte cluster Spark v HDInsight Linux, můžete spustit akce skriptu během vytváření clusteru. Podívejte se na dokumentaci, [Jak používat akce vlastního skriptu](https://docs.microsoft.com/azure/hdinsight/hdinsight-hadoop-customize-cluster-linux).

## <a name="support-for-open-source-software-used-on-hdinsight-clusters"></a>Podpora open source softwaru používaného v clusterech HDInsight

Služba Microsoft Azure HDInsight používá ekosystém open source technologií vytvořených v Apache Hadoop. Microsoft Azure poskytuje obecnou úroveň podpory pro open source technologie. Další informace najdete na [webu věnovaném nejčastějším dotazům k podpoře Azure](https://azure.microsoft.com/support/faq/). Služba HDInsight poskytuje další úroveň podpory pro integrované součásti.

Existují dva typy open source komponent, které jsou k dispozici ve službě HDInsight:

* **Předdefinované komponenty** – tyto komponenty jsou předem nainstalovány v clusterech HDInsight a poskytují základní funkce clusteru. Do této kategorie patří například Apache Hadoop nitě ResourceManager Apache Hive, Mahout dotazovací jazyk (HiveQL) a knihovna. Úplný seznam součástí clusteru je k dispozici v [části Co je nového ve verzích Apache Hadoop clusteru poskytovaných službou HDInsight](https://docs.microsoft.com/azure/hdinsight/hdinsight-component-versioning).
* **Vlastní komponenty** – jako uživatel clusteru můžete nainstalovat nebo použít ve svých úlohách libovolnou komponentu dostupnou ve komunitě nebo vytvořenou vámi.

> [!IMPORTANT]
> Součásti dodávané s clusterem HDInsight jsou plně podporované. Podpora Microsoftu pomáhá izolovat a řešit problémy související s těmito součástmi.
>
> Vlastní komponenty získají komerčně přiměřenou podporu, která vám může pomoct s dalším řešením tohoto problému. Tato podpora může být schopná vyřešit problém nebo může požádat o zapojení dostupných kanálů pro technologie Open Source, kde se nachází hloubkové odbornosti pro danou technologii. Například existuje mnoho webů komunity, které lze použít, například [Fórum MSDN pro HDInsight](https://social.msdn.microsoft.com/Forums/azure/home?forum=hdinsight), [https://stackoverflow.com](https://stackoverflow.com). Projekty Apache také obsahují projektové weby na [https://apache.org](https://apache.org), například: [Hadoop](https://hadoop.apache.org/).

## <a name="use-external-packages-with-jupyter-notebooks"></a>Použijte externí balíčky s poznámkovými bloky Jupyter

1. Z [Azure Portal](https://portal.azure.com/)přejděte na svůj cluster.  

2. Zvolte vybraný cluster, v levém podokně v části **Nastavení**vyberte **akce skriptů**.

3. Vyberte **+ Odeslat novou**.

4. Do okna **Odeslat skript akce** zadejte následující hodnoty:  

    |Parametr | Hodnota |
    |---|---|
    |Typ skriptu | Z rozevíracího seznamu vyberte **-vlastní** .|
    |Název |Do textového pole zadejte `tensorflow`.|
    |Identifikátor URI skriptu bash |Do textového pole zadejte `https://hdiconfigactions.blob.core.windows.net/linuxtensorflow/tensorflowinstall.sh`. |
    |Typ (typy) uzlů | Zaškrtněte políčka **vedoucí**a **pracovní proces** . |

    `tensorflowinstall.sh` obsahuje následující příkazy:

    ```bash
    #!/usr/bin/env bash
    /usr/bin/anaconda/bin/conda install -c conda-forge tensorflow
    ```

5. Vyberte **Vytvořit**.  Podívejte se na dokumentaci, [Jak používat akce vlastního skriptu](../hdinsight-hadoop-customize-cluster-linux.md).

6. Počkejte, než se skript dokončí.  V podokně **akce skriptu** se zobrazí stav **nové akce skriptu, které je možné odeslat po dokončení aktuální operace clusteru** během provádění skriptu.  Indikátor průběhu lze zobrazit v okně **operací na pozadí** uživatelského rozhraní Ambari.

7. Otevřete Poznámkový blok PySpark Jupyter.  Postup najdete v tématu [vytvoření poznámkového bloku Jupyter v Spark HDInsight](./apache-spark-jupyter-notebook-kernels.md#create-a-jupyter-notebook-on-spark-hdinsight) .

    ![Vytvoření nového poznámkového bloku Jupyter](./media/apache-spark-python-package-installation/hdinsight-spark-create-notebook.png "Vytvoření nového poznámkového bloku Jupyter")

8. Nyní se `import tensorflow` a spustíte příklad Hello World. Zadejte následující kód:

    ```
    import tensorflow as tf
    hello = tf.constant('Hello, TensorFlow!')
    sess = tf.Session()
    print(sess.run(hello))
    ```

    Výsledek bude vypadat takto:
    
    ![TensorFlow provádění kódu](./media/apache-spark-python-package-installation/tensorflow-execution.png "Spustit TensorFlow kód")

> [!NOTE]  
> V clusteru jsou dvě instalace Pythonu. Spark bude používat instalaci Anaconda Pythonu, která se nachází na `/usr/bin/anaconda/bin` a ve výchozím nastavení bude prostředí Python 2,7. Pokud chcete používat Python 3. x a instalovat balíčky v jádru PySpark3, použijte pro toto prostředí cestu ke spustitelnému souboru `conda` a pomocí parametru `-n` určete prostředí. Například příkaz `/usr/bin/anaconda/envs/py35/bin/conda install -c conda-forge ggplot -n py35`nainstaluje balíček `ggplot` do prostředí Python 3,5 pomocí kanálu `conda-forge`.

## <a name="seealso"></a>Viz také

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

* [Použití externích balíčků s Jupyter poznámkovým blokům v clusterech s Apache Spark v HDInsight](apache-spark-jupyter-notebook-use-external-packages.md)
* [Modul plug-in nástroje HDInsight pro IntelliJ IDEA pro vytvoření a odesílání aplikací Spark Scala](apache-spark-intellij-tool-plugin.md)
* [Použití modulu plug-in nástrojů HDInsight pro IntelliJ NÁPADu při vzdáleném ladění aplikací Apache Spark](apache-spark-intellij-tool-plugin-debug-jobs-remotely.md)
* [Použití poznámkových bloků Apache Zeppelin s clusterem Apache Spark v HDInsight](apache-spark-zeppelin-notebook.md)
* [Jádra dostupná pro Poznámkový blok Jupyter v clusteru Apache Spark pro HDInsight](apache-spark-jupyter-notebook-kernels.md)
* [Instalace Jupyteru do počítače a připojení ke clusteru HDInsight Spark](apache-spark-jupyter-notebook-install-locally.md)

### <a name="manage-resources"></a>Správa prostředků

* [Správa prostředků v clusteru Apache Spark v Azure HDInsight](apache-spark-resource-manager.md)
* [Sledování a ladění úloh spuštěných v clusteru Apache Spark v HDInsight](apache-spark-job-debugging.md)
