---
title: Instalace Jupyteru místně a připojení ke Sparku v Azure HDInsight
description: Zjistěte, jak místně do počítače nainstalovat Poznámkový blok Jupyter a připojte ho ke clusteru Apache Spark.
ms.service: hdinsight
author: hrasheed-msft
ms.reviewer: jasonh
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 06/06/2019
ms.author: hrasheed
ms.openlocfilehash: 489685485af4e3c8868f7e0281d2f81464a166f6
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/13/2019
ms.locfileid: "67066180"
---
# <a name="install-jupyter-notebook-on-your-computer-and-connect-to-apache-spark-on-hdinsight"></a>Na váš počítač nainstalovat Poznámkový blok Jupyter a připojte se k Apache Spark v HDInsight

V tomto článku se dozvíte, jak instalace Poznámkový blok Jupyter s vlastní PySpark (pro jazyk Python) a Apache Sparku (pro Scala) jader s Spark magic a připojení ke clusteru HDInsight poznámkového bloku. Může existovat několik důvodů, proč instalace Jupyteru do místního počítače a může být také některé běžné problémy. Další informace o to, najdete v části [proč by instalace Jupyteru do počítače](#why-should-i-install-jupyter-on-my-computer) na konci tohoto článku.

Při instalaci Jupyter a připojení k Apache Spark v HDInsight jsou čtyři klíčové kroky.

* Konfigurace clusteru Spark.
* Instalace aplikace Jupyter notebook.
* Instalace jádra PySpark a Spark s kouzlem Spark.
* Nakonfigurujte magic Spark pro přístup ke clusteru Spark v HDInsight.

Další informace o jádrech vlastní a k dispozici pro poznámkové bloky Jupyter s clusterem HDInsight Spark magic, naleznete v tématu [jádra dostupná pro poznámkové bloky Jupyter s Apache Spark Linux clusterů v HDInsight](apache-spark-jupyter-notebook-kernels.md).

## <a name="prerequisites"></a>Požadavky

Požadavky uvedené v tomto poli nejsou pro instalaci Jupyter. Jedná se o připojení Poznámkový blok Jupyter ke clusteru služby HDInsight po instalaci poznámkového bloku.

* Cluster Apache Spark ve službě HDInsight. Pokyny najdete v tématu [Vytváření clusterů Apache Spark ve službě Azure HDInsight](apache-spark-jupyter-spark-sql.md).

## <a name="install-jupyter-notebook-on-your-computer"></a>Na váš počítač nainstalovat aplikace Jupyter notebook

Před instalací poznámkové bloky Jupyter, je nutné nainstalovat Python. [Distribuce Anacondy](https://www.anaconda.com/download/) nainstaluje, Python a Poznámkový blok Jupyter.

Stáhněte si [instalační program Anaconda](https://www.anaconda.com/download/) pro platformu a spusťte instalační program. Při spuštění Průvodce instalací, ujistěte se, zda že jste vybrali možnost Přidat Anaconda do proměnné PATH.  Viz také [instalace Jupyteru pomocí Anaconda](https://jupyter.readthedocs.io/en/latest/install.html).

## <a name="install-spark-magic"></a>Nainstalovat Spark magic

1. Zadejte jednu z následujících příkazů nainstalujte Spark magic. Viz také [sparkmagic dokumentaci](https://github.com/jupyter-incubator/sparkmagic#installation).

    |Verze clusteru | příkaz instalovat |
    |---|---|
    |V3.6 a 3.5 |`pip install sparkmagic==0.12.7`|
    |verzi 3.4|`pip install sparkmagic==0.2.3`|

1. Zajištění `ipywidgets` je správně nainstalován spuštěním následujícího příkazu:

    ```cmd
    jupyter nbextension enable --py --sys-prefix widgetsnbextension
    ```

## <a name="install-pyspark-and-spark-kernels"></a>Instalace jádra PySpark a Spark

1. Určete, kam `sparkmagic` je nainstalovaná tak, že zadáte následující příkaz:

    ```cmd
    pip show sparkmagic
    ```

    Poté změňte pracovní adresář na umístění identifikovat pomocí výše uvedeného příkazu.

1. Z nového pracovního adresáře zadejte jednu nebo více z následujících příkazů nainstalujte požadované kernel(s):

    |Jádra | Příkaz |
    |---|---|
    |Spark|`jupyter-kernelspec install sparkmagic/kernels/sparkkernel`|
    |SparkR|`jupyter-kernelspec install sparkmagic/kernels/sparkrkernel`|
    |PySpark|`jupyter-kernelspec install sparkmagic/kernels/pysparkkernel`|
    |PySpark3|`jupyter-kernelspec install sparkmagic/kernels/pyspark3kernel`|

1. Volitelné. Zadejte následující příkaz k povolení rozšíření serveru:

    ```cmd
    jupyter serverextension enable --py sparkmagic
    ```

## <a name="configure-spark-magic-to-connect-to-hdinsight-spark-cluster"></a>Konfigurace magic Spark pro připojení ke clusteru HDInsight Spark

V této části nakonfigurujete Kouzlo Spark, který jste dříve nainstalovali pro připojení ke clusteru Apache Spark.

1. Spusťte prostředí Python pomocí následujícího příkazu:

    ```cmd
    python
    ```

2. Informace o konfiguraci Jupyter je obvykle uložen do domovského adresáře uživatele. Zadejte následující příkaz k identifikaci domovský adresář a vytvořte složku s názvem **.sparkmagic**.  Úplná cesta bude výstupem.

    ```python
    import os
    path = os.path.expanduser('~') + "\\.sparkmagic"
    os.makedirs(path)
    print(path)
    exit()
    ```

3. Ve složce `.sparkmagic`, vytvořte soubor s názvem **config.json** a přidejte následující fragment kódu JSON dovnitř.  

    ```json
    {
      "kernel_python_credentials" : {
        "username": "{USERNAME}",
        "base64_password": "{BASE64ENCODEDPASSWORD}",
        "url": "https://{CLUSTERDNSNAME}.azurehdinsight.net/livy"
      },

      "kernel_scala_credentials" : {
        "username": "{USERNAME}",
        "base64_password": "{BASE64ENCODEDPASSWORD}",
        "url": "https://{CLUSTERDNSNAME}.azurehdinsight.net/livy"
      },

      "heartbeat_refresh_seconds": 5,
      "livy_server_heartbeat_timeout_seconds": 60,
      "heartbeat_retry_seconds": 1
    }
    ```

4. Proveďte následující změny do souboru:

    |Hodnoty v šabloně | Nová hodnota |
    |---|---|
    |{USERNAME}|Přihlášení ke clusteru, výchozí hodnota je `admin`.|
    |{NÁZEV_DNS_CLUSTERU}|Název clusteru|
    |{BASE64ENCODEDPASSWORD}|Kódováním Base 64 hesla pro vaše vlastní heslo.  Můžete vygenerovat heslo ve formátu base64 v [ https://www.url-encode-decode.com/base64-encode-decode/ ](https://www.url-encode-decode.com/base64-encode-decode/).|
    |`"livy_server_heartbeat_timeout_seconds": 60`|Používáte-li zachovat `sparkmagic 0.12.7` (clusterů v3.5 a v3.6).  Pokud používáte `sparkmagic 0.2.3` (clusterů verzi 3.4), nahraďte `"should_heartbeat": true`.|

    Zobrazí se úplný ukázkový soubor v [ukázka config.json](https://github.com/jupyter-incubator/sparkmagic/blob/master/sparkmagic/example_config.json).

   > [!TIP]  
   > Ujistěte se, že nedošlo k úniku relací jsou odesílány prezenční signály. Když počítač přejde do režimu spánku nebo je vypnutý, nejsou zasílány prezenčního signálu, což vede k relaci se vyčistí. Pro clustery verzi 3.4, pokud chcete toto chování zakázat, můžete nastavit konfiguraci Livy `livy.server.interactive.heartbeat.timeout` k `0` z uživatelského rozhraní Ambari. Pro clustery v3.5 Pokud nenastavíte výše, verze 3.5 Konfigurace relace se neodstraní.

5. Začněte Jupyter. Použijte následující příkaz z příkazového řádku.

    ```cmd
    jupyter notebook
    ```

6. Ověřte, že můžete použít k dispozici Spark magic s jádrech. Proveďte následující kroky.

    a. Vytvořte nový poznámkový blok. V pravém rohu vyberte **nový**. Měli byste vidět výchozí jádra **Python 2** nebo **Python 3** a jádrech jste nainstalovali. Skutečné hodnoty se mohou lišit v závislosti na vybrané možnosti instalace.  Vyberte **PySpark**.

    ![Jádra v poznámkovém bloku Jupyter](./media/apache-spark-jupyter-notebook-install-locally/jupyter-kernels.png "jádra v poznámkovém bloku Jupyter")

    > [!IMPORTANT]  
    > Po výběru **nový** zkontrolujte prostředí, aby všechny chyby.  Pokud se zobrazí chyba `TypeError: __init__() got an unexpected keyword argument 'io_loop'` může se jednat o známý problém s některými verzemi tornádu.  Pokud ano, zastavte jádra a poté downgradovat tornádu instalaci pomocí následujícího příkazu: `pip install tornado==4.5.3`.

    b. Spusťte následující fragment kódu.

    ```sql
    %%sql
    SELECT * FROM hivesampletable LIMIT 5
    ```  

    Pokud můžete úspěšně načíst výstup, je otestovat připojení ke clusteru HDInsight.

    Pokud chcete aktualizovat konfiguraci poznámkového bloku pro připojení do jiného clusteru, aktualizujte config.json s novou sadu hodnot, jak je uvedeno v kroku 3 výše.

## <a name="why-should-i-install-jupyter-on-my-computer"></a>Proč instalace Jupyteru do počítače?

Může existovat několik důvodů, proč můžete chtít nainstalovat do počítače Jupyter a připojte ho ke clusteru Apache Spark v HDInsight.

* I když poznámkové bloky Jupyter jsou už k dispozici pro cluster Spark v Azure HDInsight, instalace Jupyteru do počítače poskytuje možnost vytvořit místně vašich poznámkových bloků, test aplikace proti spuštěný cluster a pak nahrajte poznámkové bloky do clusteru. K nahrání poznámkových bloků do clusteru, můžete nahrát je pomocí poznámkového bloku Jupyter, na kterém běží nebo cluster nebo je uložit do složky /HdiNotebooks v účtu úložiště přidruženého clusteru. Další informace o tom, jak jsou poznámkové bloky uložené v clusteru, naleznete v tématu [umístění ukládaných poznámkové bloky Jupyter](apache-spark-jupyter-notebook-kernels.md#where-are-the-notebooks-stored)?
* S poznámkovými bloky k dispozici místně, můžete připojit k jiné clustery Spark podle požadavků vašich aplikací.
* Githubu můžete implementovat systém správy zdrojového kódu a správy verzí pro poznámkových bloků. Je také možné prostředí vytvářené společnými silami místo, kde více uživatelů může pracovat s stejného poznámkového bloku.
* Můžete pracovat s poznámkovými bloky místně i bez clusteru. Potřebujete jenom cluster k testování vašich poznámkových bloků, nechcete spravovat ručně vašich poznámkových bloků nebo vývojové prostředí.
* Může být jednodušší konfigurovat místní vývojové prostředí, než je konfigurace instalace Jupyter v clusteru.  Můžete využít výhod veškerý software, který jste si nainstalovali místně bez konfigurace jeden nebo více vzdálených clusterů.

> [!WARNING]  
> S Jupyterem nainstalované na místním počítači poběží více uživatelů stejného poznámkového bloku na stejném clusteru Spark ve stejnou dobu. V takovém případě se vytvoří víc relací Livy. Pokud narazíte na problém a chcete ladit, který bude, že složité úlohy sledování relace Livy patří který uživatel.  

## <a name="next-steps"></a>Další postup

* [Přehled: Apache Spark v Azure HDInsight](apache-spark-overview.md)
* [Apache Spark s BI: Provádějte interaktivní analýzy dat pomocí Sparku v HDInsight pomocí nástrojů BI](apache-spark-use-bi-tools.md)
* [Apache Spark s Machine Learning: Použití Sparku v HDInsight pro analýzu stavební teploty pomocí dat HVAC](apache-spark-ipython-notebook-machine-learning.md)