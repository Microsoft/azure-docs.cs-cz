---
title: Akce skriptu pro balíčky Pythonu s Jupyter ve službě Azure HDInsight
description: Podrobné pokyny, jak pomocí akce skriptu nakonfigurovat Jupyter poznámkové bloky dostupné v clusterech HDInsight Spark, aby používaly externí balíčky Pythonu.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: how-to
ms.custom: seoapr2020, devx-track-python
ms.date: 04/29/2020
ms.openlocfilehash: dc1da641ba628cef92250549c1c6b6482cf18b51
ms.sourcegitcommit: d767156543e16e816fc8a0c3777f033d649ffd3c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/26/2020
ms.locfileid: "92547329"
---
# <a name="safely-manage-python-environment-on-azure-hdinsight-using-script-action"></a>Zabezpečená správa prostředí Pythonu v Azure HDInsightu s využitím akce skriptu

HDInsight obsahuje dvě vestavěné Instalace Pythonu v clusteru Spark, Anaconda Python 2,7 a Python 3,5. Zákazníci možná budou muset přizpůsobit prostředí Pythonu, jako je instalace externích balíčků Pythonu. Tady uvádíme osvědčený postup pro bezpečnou správu prostředí Pythonu pro Apache Spark clustery v HDInsight.

## <a name="prerequisites"></a>Požadavky

Cluster Apache Spark ve službě HDInsight. Pokyny najdete v tématu [Vytváření clusterů Apache Spark ve službě Azure HDInsight](apache-spark-jupyter-spark-sql.md). Pokud ještě nemáte cluster Spark ve službě HDInsight, můžete spustit akce skriptu během vytváření clusteru. Podívejte se na dokumentaci, [Jak používat akce vlastního skriptu](../hdinsight-hadoop-customize-cluster-linux.md).

## <a name="support-for-open-source-software-used-on-hdinsight-clusters"></a>Podpora open source softwaru používaného v clusterech HDInsight

Služba Microsoft Azure HDInsight používá prostředí Open Source technologií vytvořených v Apache Hadoop. Microsoft Azure poskytuje obecnou úroveň podpory pro open source technologie. Další informace najdete na [webu věnovaném nejčastějším dotazům k podpoře Azure](https://azure.microsoft.com/support/faq/). Služba HDInsight poskytuje další úroveň podpory pro integrované součásti.

Existují dva typy open source komponent, které jsou k dispozici ve službě HDInsight:

|Komponenta |Popis |
|---|---|
|Integrované|Tyto komponenty jsou předem nainstalované v clusterech HDInsight a poskytují základní funkce clusteru. Do této kategorie patří například Apache Hadoop nitě Správce prostředků, HiveQL (Apache Hive Query Language) a Mahout Library. Úplný seznam součástí clusteru je k dispozici v [části Co je nového ve verzích Apache Hadoop clusteru poskytovaných službou HDInsight](../hdinsight-component-versioning.md).|
|Vlastní|Vy, jako uživatel clusteru, můžete nainstalovat nebo používat ve svých úlohách libovolnou komponentu dostupnou ve komunitě nebo vytvořenou vámi.|

> [!IMPORTANT]
> Součásti dodávané s clusterem HDInsight jsou plně podporované. Podpora Microsoftu pomáhá izolovat a řešit problémy související s těmito součástmi.
>
> Vlastní komponenty získají komerčně přiměřenou podporu, která vám může pomoct s dalším řešením tohoto problému. Tato podpora může být schopná vyřešit problém nebo může požádat o zapojení dostupných kanálů pro technologie Open Source, kde se nachází hloubkové odbornosti pro danou technologii. Například existuje mnoho webů komunity, které lze použít, například: [Microsoft Q&stránku s otázkou pro HDInsight](/answers/topics/azure-hdinsight.html), `https://stackoverflow.com` . Projekty Apache také obsahují projektové weby `https://apache.org` .

## <a name="understand-default-python-installation"></a>Principy výchozí instalace Pythonu

Cluster HDInsight Spark se vytvoří s instalací Anaconda. V clusteru jsou dvě instalace Pythonu, Anaconda Python 2,7 a Python 3,5. Následující tabulka ukazuje výchozí nastavení Pythonu pro Spark, Livy a Jupyter.

|Nastavení |Python 2,7|Python 3,5|
|----|----|----|
|Cesta|/usr/bin/anaconda/bin|/usr/bin/anaconda/envs/py35/bin|
|Verze Sparku|Výchozí nastavení je 2,7|Můžete změnit konfiguraci na 3,5.|
|Verze Livy|Výchozí nastavení je 2,7|Můžete změnit konfiguraci na 3,5.|
|Jupyter|Jádro PySpark|Jádro PySpark3|

## <a name="safely-install-external-python-packages"></a>Bezpečně instalovat externí balíčky Pythonu

Cluster HDInsight závisí na integrovaném prostředí Pythonu, Python 2,7 a Python 3,5. Přímá instalace vlastních balíčků v těchto výchozích předdefinovaných prostředích může způsobit neočekávané změny verze knihovny. A dále přerušit cluster. Pokud chcete bezpečně nainstalovat vlastní externí balíčky Pythonu pro vaše aplikace Spark, postupujte podle následujících kroků.

1. Vytvořte virtuální prostředí Python pomocí conda. Virtuální prostředí poskytuje izolovaný prostor pro vaše projekty, aniž by došlo k porušení dalších. Při vytváření virtuálního prostředí Python můžete určit verzi Pythonu, kterou chcete použít. I přesto musíte vytvořit virtuální prostředí, i když byste chtěli používat Python 2,7 a 3,5. Tento požadavek zajistí, aby výchozí prostředí clusteru nepodařilo přerušito. Spustí v clusteru akce skriptu pro všechny uzly s níže uvedeným skriptem, aby se vytvořilo virtuální prostředí Pythonu.

    -   `--prefix` Určuje cestu, kde virtuální prostředí conda žije. V závislosti na zadané cestě je potřeba změnit několik konfigurací. V tomto příkladu používáme py35new, protože cluster má už existující virtuální prostředí s názvem py35.
    -   `python=` Určuje verzi Pythonu pro virtuální prostředí. V tomto příkladu používáme verzi 3,5, což je stejná verze jako v clusteru. K vytvoření virtuálního prostředí můžete také použít jiné verze Pythonu.
    -   `anaconda` Určuje package_spec jako Anaconda pro instalaci balíčků Anaconda ve virtuálním prostředí.
    
    ```bash
    sudo /usr/bin/anaconda/bin/conda create --prefix /usr/bin/anaconda/envs/py35new python=3.5 anaconda --yes
    ```

2. V případě potřeby nainstalujte v vytvořeném virtuálním prostředí externí balíčky Pythonu. Spustí v clusteru akce skriptu pro všechny uzly s níže uvedeným skriptem pro instalaci externích balíčků Pythonu. Aby bylo možné zapisovat soubory do složky virtuálního prostředí, musíte mít oprávnění sudo.

    Vyhledejte v [indexu balíčku](https://pypi.python.org/pypi) úplný seznam balíčků, které jsou k dispozici. Můžete také získat seznam dostupných balíčků z jiných zdrojů. Balíčky, které jsou k dispozici například, můžete nainstalovat pomocí [conda-zfalšovat](https://conda-forge.org/feedstocks/).

    Použijte níže uvedený příkaz, pokud chcete nainstalovat knihovnu s nejnovější verzí:

    - Použít conda kanál:

        -   `seaborn` je název balíčku, který chcete nainstalovat.
        -   `-n py35new` Zadejte název virtuálního prostředí, který se právě vytvoří. Ujistěte se, že název je odpovídajícím způsobem změněn na základě vytvoření virtuálního prostředí.

        ```bash
        sudo /usr/bin/anaconda/bin/conda install seaborn -n py35new --yes
        ```

    - Nebo použijte úložiště PyPi, změňte `seaborn` a `py35new` odpovídající:
        ```bash
        sudo /usr/bin/anaconda/envs/py35new/bin/pip install seaborn
        ```

    Použijte níže uvedený příkaz, pokud chcete nainstalovat knihovnu s konkrétní verzí:

    - Použít conda kanál:

        -   `numpy=1.16.1` je název a verze balíčku, které byste chtěli nainstalovat.
        -   `-n py35new` Zadejte název virtuálního prostředí, který se právě vytvoří. Ujistěte se, že název je odpovídajícím způsobem změněn na základě vytvoření virtuálního prostředí.

        ```bash
        sudo /usr/bin/anaconda/bin/conda install numpy=1.16.1 -n py35new --yes
        ```

    - Nebo použijte úložiště PyPi, změňte `numpy==1.16.1` a `py35new` odpovídající:

        ```bash
        sudo /usr/bin/anaconda/envs/py35new/bin/pip install numpy==1.16.1
        ```

    Pokud neznáte název virtuálního prostředí, můžete SSH pro hlavní uzel clusteru a spustit `/usr/bin/anaconda/bin/conda info -e` pro zobrazení všech virtuálních prostředí.

3. Změňte konfiguraci Sparku a Livy a najeďte na vytvořené virtuální prostředí.

    1. Otevřete uživatelské rozhraní Ambari, klikněte na stránku Spark2, na kartu konfigurace.

        ![Změna konfigurace Sparku a Livy prostřednictvím Ambari](./media/apache-spark-python-package-installation/ambari-spark-and-livy-config.png)

    2. Rozbalte rozšířené livy2-ENV, v dolní části přidejte níže uvedené příkazy. Pokud jste nainstalovali virtuální prostředí s jinou předponou, změňte cestu odpovídajícím způsobem.

        ```bash
        export PYSPARK_PYTHON=/usr/bin/anaconda/envs/py35new/bin/python
        export PYSPARK_DRIVER_PYTHON=/usr/bin/anaconda/envs/py35new/bin/python
        ```

        ![Změna konfigurace Livy prostřednictvím Ambari](./media/apache-spark-python-package-installation/ambari-livy-config.png)

    3. Rozbalte položku Advanced spark2-ENV, v dolní části nahraďte existující příkaz Export PYSPARK_PYTHON. Pokud jste nainstalovali virtuální prostředí s jinou předponou, změňte cestu odpovídajícím způsobem.

        ```bash
        export PYSPARK_PYTHON=${PYSPARK_PYTHON:-/usr/bin/anaconda/envs/py35new/bin/python}
        ```

        ![Změna konfigurace Sparku prostřednictvím Ambari](./media/apache-spark-python-package-installation/ambari-spark-config.png)

    4. Uložte změny a restartujte ovlivněné služby. Tyto změny vyžadují restartování služby Spark2. Uživatelské rozhraní Ambari vás vyzve k vyžadovanámu restartování, kliknutím na restartovat restartujte všechny ovlivněné služby.

        ![Restartovat služby](./media/apache-spark-python-package-installation/ambari-restart-services.png)

4. Pokud chcete použít nové vytvořené virtuální prostředí v Jupyter. Změňte konfigurace Jupyter a restartujte Jupyter. Spusťte akce skriptu na všech uzlech hlaviček pomocí příkazu níže, aby odkazovaly Jupyter na nové vytvořené virtuální prostředí. Nezapomeňte upravit cestu k předponě, kterou jste zadali pro vaše virtuální prostředí. Po spuštění této akce skriptu restartujte službu Jupyter prostřednictvím uživatelského rozhraní Ambari, aby tato změna byla dostupná.

    ```bash
    sudo sed -i '/python3_executable_path/c\ \"python3_executable_path\" : \"/usr/bin/anaconda/envs/py35new/bin/python3\"' /home/spark/.sparkmagic/config.json
    ```

    Prostředí Pythonu v Jupyter Notebook můžete Double potvrdit spuštěním následujícího kódu:

    ![Podívejte se na verzi Pythonu v Jupyter Notebook](./media/apache-spark-python-package-installation/check-python-version-in-jupyter.png)

## <a name="known-issue"></a>Známý problém

K dispozici je známá chyba pro Anaconda verze `4.7.11` , `4.7.12` a `4.8.0` . Pokud uvidíte, že akce skriptů přestanou reagovat při `"Collecting package metadata (repodata.json): ...working..."` selhání s `"Python script has been killed due to timeout after waiting 3600 secs"` . [Tento skript](https://gregorysfixes.blob.core.windows.net/public/fix-conda.sh) si můžete stáhnout a spustit jako akce skriptu na všech uzlech, aby se problém vyřešil.

Ke kontrole verze Anaconda můžete použít SSH pro uzel záhlaví clusteru a spustit `/usr/bin/anaconda/bin/conda --v` .

## <a name="next-steps"></a>Další kroky

* [Přehled: Apache Spark v Azure HDInsight](apache-spark-overview.md)
* [Externí balíčky s Jupyter poznámkovým blokem v Apache Spark](apache-spark-jupyter-notebook-use-external-packages.md)
* [Sledování a ladění úloh spuštěných v clusteru serveru Apache Spark v HDInsight](apache-spark-job-debugging.md)