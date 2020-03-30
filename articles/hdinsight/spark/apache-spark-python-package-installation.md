---
title: Akce skriptu pro balíčky Pythonu s Jupyterem v Azure HDInsight
description: Podrobné pokyny, jak pomocí akce skriptu nakonfigurovat poznámkové bloky Jupyter, které jsou k dispozici v clusterech HDInsight Spark, aby používaly externí balíčky pythonu.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.date: 03/16/2020
ms.openlocfilehash: 659af8b85cb3736d663e79676b04af8041aeabfb
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "80129608"
---
# <a name="safely-manage-python-environment-on-azure-hdinsight-using-script-action"></a>Zabezpečená správa prostředí Pythonu v Azure HDInsightu s využitím akce skriptu

> [!div class="op_single_selector"]
> * [Použití buněčné magie](apache-spark-jupyter-notebook-use-external-packages.md)
> * [Použití akce skriptu](apache-spark-python-package-installation.md)

HDInsight má dvě vestavěné instalace Pythonu v clusteru Spark, Anaconda Python 2.7 a Python 3.5. V některých případech zákazníci potřebují přizpůsobit prostředí Pythonu, jako je instalace externích balíčků Pythonu nebo jiné verze Pythonu. V tomto článku ukazujeme osvědčený postup bezpečné správy prostředí Pythonu pro cluster [Apache Spark](./apache-spark-overview.md) na HDInsight.

## <a name="prerequisites"></a>Požadavky

Cluster Apache Spark ve službě HDInsight. Pokyny najdete v tématu [Vytváření clusterů Apache Spark ve službě Azure HDInsight](apache-spark-jupyter-spark-sql.md). Pokud ještě nemáte cluster Spark na HDInsight, můžete spustit akce skriptu během vytváření clusteru. Navštivte dokumentaci [o použití vlastních akcí skriptu](../hdinsight-hadoop-customize-cluster-linux.md).

## <a name="support-for-open-source-software-used-on-hdinsight-clusters"></a>Podpora softwaru s otevřeným zdrojovým kódem používaného v clusterech HDInsight

Služba Microsoft Azure HDInsight využívá ekosystém open source technologií vytvořených kolem Apache Hadoop. Microsoft Azure poskytuje obecnou úroveň podpory pro technologie s otevřeným zdrojovým kódem. Další informace najdete na [webu nejčastějších dotazů podpory Azure](https://azure.microsoft.com/support/faq/). Služba HDInsight poskytuje další úroveň podpory pro vestavěné součásti.

Existují dva typy komponent s otevřeným zdrojovým kódem, které jsou k dispozici ve službě HDInsight:

|Komponenta |Popis |
|---|---|
|Integrované|Tyto součásti jsou předinstalovány v clusterech HDInsight a poskytují základní funkce clusteru. Do této kategorie patří například Správce prostředků Apache Hadoop YARN, dotazovací jazyk Apache Hive (HiveQL) a knihovna Mahout. Úplný seznam součástí clusteru je k dispozici v části [Co je nového ve verzích clusteru Apache Hadoop poskytovaných službou HDInsight](../hdinsight-component-versioning.md).|
|Vlastní|Vy, jako uživatel clusteru, můžete nainstalovat nebo použít ve vaší pracovní zátěži libovolnou komponentu, která je k dispozici v komunitě nebo kterou jste vytvořili.|

> [!IMPORTANT]
> Součásti dodávané s clusterem HDInsight jsou plně podporovány. Podpora společnosti Microsoft pomáhá izolovat a vyřešit problémy související s těmito součástmi.
>
> Vlastní součásti obdrží komerčně přiměřenou podporu, která vám pomůže dále řešit problém. Podpora společnosti Microsoft může být schopna problém vyřešit nebo vás může požádat o zapojení dostupných kanálů pro technologie s otevřeným zdrojovým kódem, kde jsou nalezeny hluboké odborné znalosti pro tuto technologii. Existuje například mnoho komunitních webů, které lze použít, například: [https://stackoverflow.com](https://stackoverflow.com) [Fórum MSDN pro HDInsight](https://social.msdn.microsoft.com/Forums/azure/home?forum=hdinsight). Také Apache projekty [https://apache.org](https://apache.org)mají projektové weby na , například: [Hadoop](https://hadoop.apache.org/).

## <a name="understand-default-python-installation"></a>Principy výchozí instalace Pythonu

Cluster HDInsight Spark se vytváří pomocí instalace Anaconda. V clusteru jsou dvě instalace Pythonu, Anaconda Python 2.7 a Python 3.5. V následující tabulce je uvedeno výchozí nastavení Pythonu pro Spark, Livy a Jupyter.

| |Python 2.7|Python 3.5|
|----|----|----|
|Cesta|/usr/bin/anaconda/bin|/usr/bin/anaconda/envs/py35/bin|
|Spark|Výchozí nastavení na 2,7|Není dostupné.|
|Livy|Výchozí nastavení na 2,7|Není dostupné.|
|Jupyter|Jádro PySpark|Jádro PySpark3|

## <a name="safely-install-external-python-packages"></a>Bezpečná instalace externích balíčků Pythonu

Cluster HDInsight závisí na integrovaném prostředí Pythonu, pythonu 2.7 i Pythonu 3.5. Přímo instalace vlastních balíčků v těchto výchozích předdefinovaných prostředích může způsobit neočekávané změny verze knihovny a dále přerušit cluster. Chcete-li bezpečně nainstalovat vlastní externí balíčky Pythonu pro vaše aplikace Spark, postupujte podle následujících kroků.

1. Vytvořte virtuální prostředí Pythonu pomocí conda. Virtuální prostředí poskytuje izolovaný prostor pro vaše projekty bez porušení ostatních. Při vytváření virtuálního prostředí Pythonu můžete určit verzi pythonu, kterou chcete použít. Všimněte si, že stále potřebujete vytvořit virtuální prostředí, i když chcete použít Python 2.7 a 3.5. Tím zajistíte, že výchozí prostředí clusteru nebude na rušno. Spusťte akce skriptu v clusteru pro všechny uzly s pod skriptem a vytvořte virtuální prostředí Pythonu.

    -   `--prefix`určuje cestu, kde žije virtuální prostředí conda. Existuje několik konfiguračních souborů, které je třeba dále změnit na základě zde zadané cesty. V tomto příkladu používáme py35new, protože cluster má již existující virtuální prostředí s názvem py35.
    -   `python=`určuje verzi Pythonu pro virtuální prostředí. V tomto příkladu používáme verzi 3.5, stejnou verzi jako cluster postavený v jednom. K vytvoření virtuálního prostředí můžete také použít jiné verze Pythonu.
    -   `anaconda`určuje package_spec jako anakonda pro instalaci balíčků Anaconda ve virtuálním prostředí.
    
    ```bash
    sudo /usr/bin/anaconda/bin/conda create --prefix /usr/bin/anaconda/envs/py35new python=3.5 anaconda --yes
    ```

2. V případě potřeby nainstalujte externí balíčky Pythonu do vytvořeného virtuálního prostředí. Spusťte akce skriptů v clusteru pro všechny uzly s níže popsaným skriptem pro instalaci externích balíčků Pythonu. Musíte mít oprávnění sudo zde, aby bylo možné zapisovat soubory do složky virtuálního prostředí.

    Můžete vyhledat [index balíčku](https://pypi.python.org/pypi) pro úplný seznam balíčků, které jsou k dispozici. Můžete také získat seznam dostupných balíčků z jiných zdrojů. Můžete například nainstalovat balíčky, které jsou k dispozici prostřednictvím [conda-forge](https://conda-forge.org/feedstocks/).

    Níže použijte příkaz, pokud chcete nainstalovat knihovnu s nejnovější verzí:

    - Použijte kanál conda:

        -   `seaborn`je název balíčku, který chcete nainstalovat.
        -   `-n py35new`zadejte název virtuálního prostředí, který se právě vytvoří. Nezapomeňte změnit název odpovídajícím způsobem na základě vytvoření virtuálního prostředí.

        ```bash
        sudo /usr/bin/anaconda/bin/conda install seaborn -n py35new --yes
        ```

    - Nebo použijte PyPi repo, změnit `seaborn` a `py35new` odpovídajícím způsobem:
        ```bash
        sudo /usr/bin/anaconda/env/py35new/bin/pip install seaborn
        ```

    Níže použijte příkaz, pokud chcete nainstalovat knihovnu s určitou verzí:

    - Použijte kanál conda:

        -   `numpy=1.16.1`je název balíčku a verze, kterou chcete nainstalovat.
        -   `-n py35new`zadejte název virtuálního prostředí, který se právě vytvoří. Nezapomeňte změnit název odpovídajícím způsobem na základě vytvoření virtuálního prostředí.

        ```bash
        sudo /usr/bin/anaconda/bin/conda install numpy=1.16.1 -n py35new --yes
        ```

    - Nebo použijte PyPi repo, změnit `numpy==1.16.1` a `py35new` odpovídajícím způsobem:

        ```bash
        sudo /usr/bin/anaconda/env/py35new/bin/pip install numpy==1.16.1
        ```

    Pokud neznáte název virtuálního prostředí, můžete SSH do hlavního uzlu `/usr/bin/anaconda/bin/conda info -e` clusteru a spustit zobrazení všech virtuálních prostředí.

3. Změňte konfigurace Spark a Livy a přejděte na vytvořené virtuální prostředí.

    1. Otevřete uzulinu Ambari, přejděte na stránku Spark2, kartu Configs.

        ![Změna Spark a Livy config přes Ambari](./media/apache-spark-python-package-installation/ambari-spark-and-livy-config.png)

    2. Rozbalte Rozšířené livy2-env, přidejte níže příkazy v dolní části. Pokud jste nainstalovali virtuální prostředí s jinou předponou, změňte cestu odpovídajícím způsobem.

        ```
        export PYSPARK_PYTHON=/usr/bin/anaconda/envs/py35new/bin/python
        export PYSPARK_DRIVER_PYTHON=/usr/bin/anaconda/envs/py35new/bin/python
        ```

        ![Změna livy config přes Ambari](./media/apache-spark-python-package-installation/ambari-livy-config.png)

    3. Rozbalte rozšířené spark2-env, nahradit stávající export PYSPARK_PYTHON prohlášení v dolní části. Pokud jste nainstalovali virtuální prostředí s jinou předponou, změňte cestu odpovídajícím způsobem.

        ```
        export PYSPARK_PYTHON=${PYSPARK_PYTHON:-/usr/bin/anaconda/envs/py35new/bin/python}
        ```

        ![Změna konfigurace Spark přes Ambari](./media/apache-spark-python-package-installation/ambari-spark-config.png)

    4. Uložte změny a restartujte služby obsahující tuto chybu. Tyto změny vyžadují restartování služby Spark2. Ambari UI vyzve požadované připomenutí restartování, klepněte na tlačítko Restartovat restartovat všechny ovlivněné služby.

        ![Změna konfigurace Spark přes Ambari](./media/apache-spark-python-package-installation/ambari-restart-services.png)

4. Pokud byste chtěli použít nově vytvořené virtuální prostředí na Jupyter. Musíte změnit Jupyter configs a restartovat Jupyter. Spusťte akce skriptu na všech uzlech záhlaví s příkazem pod, který navede Jupyter a přenese nové vytvořené virtuální prostředí. Nezapomeňte upravit cestu k předponě, kterou jste zadali pro virtuální prostředí. Po spuštění této akce skriptu restartujte službu Jupyter prostřednictvím rozhraní Ambari, aby byla tato změna k dispozici.

    ```bash
    sudo sed -i '/python3_executable_path/c\ \"python3_executable_path\" : \"/usr/bin/anaconda/envs/py35new/bin/python3\"' /home/spark/.sparkmagic/config.json
    ```

    Můžete dvakrát potvrdit prostředí Pythonu v Jupyter Notebook spuštěním níže kódu:

    ![Kontrola verze Pythonu v Jupyter Notebook](./media/apache-spark-python-package-installation/check-python-version-in-jupyter.png)

## <a name="known-issue"></a>Známý problém

Existuje známá chyba pro Anaconda verze 4.7.11, 4.7.12 a 4.8.0. Pokud vidíte, že vaše `"Collecting package metadata (repodata.json): ...working..."` skript `"Python script has been killed due to timeout after waiting 3600 secs"`akce visí na a selhání s . Tento [skript](https://gregorysfixes.blob.core.windows.net/public/fix-conda.sh) můžete stáhnout a spustit jako akce skriptu ve všech uzlech, abyste problém vyřešili.

Chcete-li zkontrolovat verzi Anaconda, můžete SSH do `/usr/bin/anaconda/bin/conda --v`uzlu záhlaví clusteru a spustit .

## <a name="next-steps"></a>Další kroky

* [Přehled: Apache Spark v Azure HDInsight](apache-spark-overview.md)
* [Apache Spark s BI: Provádění interaktivní analýzy dat pomocí Spark v HDInsightu pomocí nástrojů BI](apache-spark-use-bi-tools.md)
* [Správa prostředků v clusteru Apache Spark v Azure HDInsight](apache-spark-resource-manager.md)
* [Sledování a ladění úloh spuštěných v clusteru serveru Apache Spark v HDInsight](apache-spark-job-debugging.md)
