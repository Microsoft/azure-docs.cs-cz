---
title: Akce skriptu pro balíčky Pythonu s Jupyter ve službě Azure HDInsight
description: Podrobné pokyny, jak pomocí akce skriptu nakonfigurovat Jupyter poznámkové bloky dostupné v clusterech HDInsight Spark, aby používaly externí balíčky Pythonu.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.date: 11/19/2019
ms.openlocfilehash: 109ac20d8a3d3dc87b4a83165c0e6c24808c1340
ms.sourcegitcommit: ce4a99b493f8cf2d2fd4e29d9ba92f5f942a754c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/28/2019
ms.locfileid: "75529639"
---
# <a name="safely-manage-python-environment-on-azure-hdinsight-using-script-action"></a>Zabezpečená správa prostředí Pythonu v Azure HDInsightu s využitím akce skriptu

> [!div class="op_single_selector"]
> * [Použití buňky Magic](apache-spark-jupyter-notebook-use-external-packages.md)
> * [Pomocí akce skriptu](apache-spark-python-package-installation.md)

HDInsight obsahuje dvě vestavěné Instalace Pythonu v clusteru Spark, Anaconda Python 2,7 a Python 3,5. V některých případech si zákazníci musí přizpůsobit prostředí Python, jako je instalace externích balíčků Pythonu nebo jiné verze Pythonu. V tomto článku se seznámíme s osvědčenými postupy pro bezpečnou správu prostředí Pythonu pro cluster [Apache Spark](https://spark.apache.org/) v HDInsight.

## <a name="prerequisites"></a>Požadavky

* Předplatné Azure. Viz [Získání bezplatné zkušební verze Azure](https://azure.microsoft.com/documentation/videos/get-azure-free-trial-for-testing-hadoop-in-hdinsight/).

* Cluster Apache Spark ve službě HDInsight. Pokyny najdete v tématu [Vytváření clusterů Apache Spark ve službě Azure HDInsight](apache-spark-jupyter-spark-sql.md).

   > [!NOTE]  
   > Pokud ještě nemáte cluster Spark v HDInsight Linux, můžete spustit akce skriptu během vytváření clusteru. Podívejte se na dokumentaci, [Jak používat akce vlastního skriptu](https://docs.microsoft.com/azure/hdinsight/hdinsight-hadoop-customize-cluster-linux).

## <a name="support-for-open-source-software-used-on-hdinsight-clusters"></a>Podpora open source softwaru používaného v clusterech HDInsight

Služba Microsoft Azure HDInsight používá ekosystém open source technologií vytvořených v Apache Hadoop. Microsoft Azure poskytuje obecnou úroveň podpory pro open source technologie. Další informace najdete na [webu věnovaném nejčastějším dotazům k podpoře Azure](https://azure.microsoft.com/support/faq/). Služba HDInsight poskytuje další úroveň podpory pro integrované součásti.

Existují dva typy open source komponent, které jsou k dispozici ve službě HDInsight:

* **Předdefinované komponenty** – tyto komponenty jsou předem nainstalovány v clusterech HDInsight a poskytují základní funkce clusteru. Do této kategorie patří například Apache Hadoop nitě Správce prostředků, HiveQL (Apache Hive Query Language) a Mahout Library. Úplný seznam součástí clusteru je k dispozici v [části Co je nového ve verzích Apache Hadoop clusteru poskytovaných službou HDInsight](https://docs.microsoft.com/azure/hdinsight/hdinsight-component-versioning).
* **Vlastní komponenty** – jako uživatel clusteru můžete nainstalovat nebo použít ve svých úlohách libovolnou komponentu dostupnou ve komunitě nebo vytvořenou vámi.

> [!IMPORTANT]
> Součásti dodávané s clusterem HDInsight jsou plně podporované. Podpora Microsoftu pomáhá izolovat a řešit problémy související s těmito součástmi.
>
> Vlastní komponenty získají komerčně přiměřenou podporu, která vám může pomoct s dalším řešením tohoto problému. Tato podpora může být schopná vyřešit problém nebo může požádat o zapojení dostupných kanálů pro technologie Open Source, kde se nachází hloubkové odbornosti pro danou technologii. Například existuje mnoho webů komunity, které lze použít, například [Fórum MSDN pro HDInsight](https://social.msdn.microsoft.com/Forums/azure/home?forum=hdinsight), [https://stackoverflow.com](https://stackoverflow.com). Projekty Apache také obsahují projektové weby na [https://apache.org](https://apache.org), například: [Hadoop](https://hadoop.apache.org/).

## <a name="understand-default-python-installation"></a>Principy výchozí instalace Pythonu

Cluster HDInsight Spark se vytvoří s instalací Anaconda. V clusteru jsou dvě instalace Pythonu, Anaconda Python 2,7 a Python 3,5. Následující tabulka ukazuje výchozí nastavení Pythonu pro Spark, Livy a Jupyter.

| |Python 2.7|Python 3,5|
|----|----|----|
|Cesta|/usr/bin/anaconda/bin|/usr/bin/anaconda/envs/py35/bin|
|Spark|Výchozí nastavení je 2,7|Nevztahuje se|
|Livy|Výchozí nastavení je 2,7|Nevztahuje se|
|Jupyter|Jádro PySpark|Jádro PySpark3|

## <a name="safely-install-external-python-packages"></a>Bezpečně instalovat externí balíčky Pythonu

Cluster HDInsight závisí na integrovaném prostředí Pythonu, Python 2,7 a Python 3,5. Přímá instalace vlastních balíčků v těchto výchozích předdefinovaných prostředích může způsobit neočekávané změny verze knihovny a další rozdělení clusteru. Aby bylo možné bezpečně nainstalovat vlastní externí balíčky Pythonu pro aplikace Spark, postupujte podle následujících kroků.

1. Vytvořte virtuální prostředí Python pomocí conda. Virtuální prostředí poskytuje izolovaný prostor pro vaše projekty, aniž by došlo k porušení dalších. Při vytváření virtuálního prostředí Python můžete určit verzi Pythonu, kterou chcete použít. Všimněte si, že stále potřebujete vytvořit virtuální prostředí, i když byste chtěli používat Python 2,7 a 3,5. Tím se zajistěte, aby výchozí prostředí clusteru nepodařilo přerušito. Spustí v clusteru akce skriptu pro všechny uzly s níže uvedeným skriptem, aby se vytvořilo virtuální prostředí Pythonu. 

    -   `--prefix` Určuje cestu, ve které virtuální prostředí conda žije. V závislosti na zadané cestě je potřeba změnit několik konfigurací. V tomto příkladu používáme py35new, protože cluster má už existující virtuální prostředí s názvem py35.
    -   `python=` určuje verzi Pythonu pro virtuální prostředí. V tomto příkladu používáme verzi 3,5, což je stejná verze jako v clusteru. K vytvoření virtuálního prostředí můžete také použít jiné verze Pythonu.
    -   `anaconda` určuje package_spec jako Anaconda pro instalaci balíčků Anaconda ve virtuálním prostředí.
    
    ```bash
    sudo /usr/bin/anaconda/bin/conda create --prefix /usr/bin/anaconda/envs/py35new python=3.5 anaconda --yes 
    ```

2. V případě potřeby nainstalujte v vytvořeném virtuálním prostředí externí balíčky Pythonu. Spustí v clusteru akce skriptu pro všechny uzly s níže uvedeným skriptem pro instalaci externích balíčků Pythonu. Aby bylo možné zapisovat soubory do složky virtuálního prostředí, musíte mít sudo oprávnění.

    Úplný seznam balíčků, které jsou k dispozici, můžete vyhledat v [indexu balíčku](https://pypi.python.org/pypi) . Můžete také získat seznam dostupných balíčků z jiných zdrojů. Balíčky, které jsou k dispozici například, můžete nainstalovat pomocí [conda-zfalšovat](https://conda-forge.org/feedstocks/).

    -   `seaborn` je název balíčku, který chcete nainstalovat.
    -   `-n py35new` zadejte název virtuálního prostředí, který se právě vytvoří. Ujistěte se, že název je odpovídajícím způsobem změněn na základě vytvoření virtuálního prostředí.

    ```bash
    sudo /usr/bin/anaconda/bin/conda install seaborn -n py35new --yes
    ```

    Pokud neznáte název virtuálního prostředí, můžete SSH na hlavní uzel clusteru a spustit `/usr/bin/anaconda/bin/conda info -e` pro zobrazení všech virtuálních prostředí.

3. Změňte konfiguraci Sparku a Livy a najeďte na vytvořené virtuální prostředí.

    1. Otevřete uživatelské rozhraní Ambari, klikněte na stránku Spark2, na kartu konfigurace.
    
        ![Změna konfigurace Sparku a Livy prostřednictvím Ambari](./media/apache-spark-python-package-installation/ambari-spark-and-livy-config.png)
 
    2. Rozbalte rozšířené livy2-ENV, v dolní části přidejte níže uvedené příkazy. Pokud jste nainstalovali virtuální prostředí s jinou předponou, změňte cestu odpovídajícím způsobem.

        ```
        export PYSPARK_PYTHON=/usr/bin/anaconda/envs/py35new/bin/python
        export PYSPARK_DRIVER_PYTHON=/usr/bin/anaconda/envs/py35new/bin/python
        ```

        ![Změna konfigurace Livy prostřednictvím Ambari](./media/apache-spark-python-package-installation/ambari-livy-config.png)

    3. Rozbalte položku Advanced spark2-ENV, v dolní části nahraďte existující příkaz Export PYSPARK_PYTHON. Pokud jste nainstalovali virtuální prostředí s jinou předponou, změňte cestu odpovídajícím způsobem.

        ```
        export PYSPARK_PYTHON=${PYSPARK_PYTHON:-/usr/bin/anaconda/envs/py35new/bin/python}
        ```

        ![Změna konfigurace Sparku prostřednictvím Ambari](./media/apache-spark-python-package-installation/ambari-spark-config.png)

    4. Uložte změny a restartujte ovlivněné služby. Tyto změny vyžadují restartování služby Spark2. Uživatelské rozhraní Ambari vás vyzve k vyžadovanámu restartování, kliknutím na restartovat restartujte všechny ovlivněné služby.

        ![Změna konfigurace Sparku prostřednictvím Ambari](./media/apache-spark-python-package-installation/ambari-restart-services.png)
 
4.  Pokud chcete použít nové vytvořené virtuální prostředí v Jupyter. Je potřeba změnit konfigurace Jupyter a restartovat Jupyter. Spusťte akce skriptu na všech uzlech hlaviček pomocí příkazu níže, aby odkazovaly Jupyter na nové vytvořené virtuální prostředí. Nezapomeňte upravit cestu k předponě, kterou jste zadali pro vaše virtuální prostředí. Po spuštění této akce skriptu restartujte službu Jupyter prostřednictvím uživatelského rozhraní Ambari, aby tato změna byla dostupná.

    ```
    sudo sed -i '/python3_executable_path/c\ \"python3_executable_path\" : \"/usr/bin/anaconda/envs/py35new/bin/python3\"' /home/spark/.sparkmagic/config.json
    ```

    Prostředí Pythonu v Jupyter Notebook můžete Double potvrdit spuštěním následujícího kódu:

    ![Podívejte se na verzi Pythonu v Jupyter Notebook](./media/apache-spark-python-package-installation/check-python-version-in-jupyter.png)

## <a name="known-issue"></a>Známý problém

Je známá chyba pro Anaconda verze 4.7.11 a 4.7.12. Pokud se vám akce se skripty dokončí při `"Collecting package metadata (repodata.json): ...working..."` a selhání s `"Python script has been killed due to timeout after waiting 3600 secs"`. [Tento skript](https://gregorysfixes.blob.core.windows.net/public/fix-conda.sh) si můžete stáhnout a spustit jako akce skriptu na všech uzlech, aby se problém vyřešil.

Ke kontrole verze Anaconda můžete použít SSH na uzel hlavičky clusteru a spustit `/usr/bin/anaconda/bin/conda --v`.

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
