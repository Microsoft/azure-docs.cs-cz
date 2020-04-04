---
title: Nainstalujte Jupyter místně a připojte se ke Sparku v Azure HDInsight
description: Přečtěte si, jak nainstalovat notebook Jupyter místně do počítače a připojit ho ke clusteru Apache Spark.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.custom: hdinsightactive
ms.date: 04/02/2020
ms.openlocfilehash: 1d044ddaea0a2c7a1d489523cc9aa4515df0728a
ms.sourcegitcommit: d597800237783fc384875123ba47aab5671ceb88
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/03/2020
ms.locfileid: "80632660"
---
# <a name="install-jupyter-notebook-on-your-computer-and-connect-to-apache-spark-on-hdinsight"></a>Nainstalujte si do počítače notebook Jupyter a připojte se k Apache Spark na HDInsightu

V tomto článku se dozvíte, jak nainstalovat jupyterový notebook s vlastními jádry PySpark (pro Python) a Apache Spark (pro Scala) s magií Spark. Potom připojíte poznámkový blok ke clusteru HDInsight.

Existují čtyři klíčové kroky spojené s instalací Jupyteru a připojením k Apache Spark na HDInsight.

* Konfigurace clusteru Spark.
* Nainstalujte notebook Jupyter.
* Nainstalujte jádra PySpark a Spark s magií Spark.
* Nakonfigurujte kouzlo Spark pro přístup ke clusteru Spark na HDInsightu.

Další informace o vlastních jádrech a magii Spark najdete [v tématu Jádra dostupná pro notebooky Jupyter s clustery Apache Spark Linux na HDInsightu](apache-spark-jupyter-notebook-kernels.md).

## <a name="prerequisites"></a>Požadavky

* Cluster Apache Spark ve službě HDInsight. Pokyny najdete v tématu [Vytváření clusterů Apache Spark ve službě Azure HDInsight](apache-spark-jupyter-spark-sql.md). Místní poznámkový blok se připojuje ke clusteru HDInsight.

* Znalost používání poznámkových bloků Jupyter se Sparkem ve službě HDInsight.

## <a name="install-jupyter-notebook-on-your-computer"></a>Instalace notebooku Jupyter do počítače

Nainstalujte Python před instalací jupyterových notebooků. Distribuce [Anaconda](https://www.anaconda.com/download/) nainstaluje oba, Python a Jupyter Notebook.

Stáhněte si [instalační program Anaconda](https://www.anaconda.com/download/) pro vaši platformu a spusťte nastavení. Při spuštění průvodce instalací nezapomeňte vybrat možnost přidání anakondy do proměnné PATH.  Viz také [Instalace Jupyter pomocí Anaconda](https://jupyter.readthedocs.io/en/latest/install.html).

## <a name="install-spark-magic"></a>Instalace Spark magie

1. Zadejte jeden z níže uvedených příkazů pro instalaci Spark magic. Viz také [dokumentace sparkmagic](https://github.com/jupyter-incubator/sparkmagic#installation).

    |Verze clusteru | Nainstalovat, příkaz |
    |---|---|
    |v3.6 a v3.5 |`pip install sparkmagic==0.13.1`|
    |v3.4|`pip install sparkmagic==0.2.3`|

1. Pomocí `ipywidgets` následujícího příkazu se ujistěte, že je správně nainstalován následující příkaz:

    ```cmd
    jupyter nbextension enable --py --sys-prefix widgetsnbextension
    ```

## <a name="install-pyspark-and-spark-kernels"></a>Instalace jader PySpark a Spark

1. Zjistěte, kde `sparkmagic` je nainstalován zadáním následujícího příkazu:

    ```cmd
    pip show sparkmagic
    ```

    Poté změňte svůj pracovní adresář na **umístění** označené výše uvedeným příkazem.

1. Z nového pracovního adresáře zadejte jeden nebo více níže uvedených příkazů a nainstalujte tak hledané jádro(

    |Kernel | Příkaz |
    |---|---|
    |Spark|`jupyter-kernelspec install sparkmagic/kernels/sparkkernel`|
    |SparkR|`jupyter-kernelspec install sparkmagic/kernels/sparkrkernel`|
    |PySpark (PySka|`jupyter-kernelspec install sparkmagic/kernels/pysparkkernel`|
    |PySpark3|`jupyter-kernelspec install sparkmagic/kernels/pyspark3kernel`|

1. Nepovinný parametr. Chcete-li povolit rozšíření serveru, zadejte následující příkaz:

    ```cmd
    jupyter serverextension enable --py sparkmagic
    ```

## <a name="configure-spark-magic-to-connect-to-hdinsight-spark-cluster"></a>Konfigurace magie Spark pro připojení ke clusteru HDInsight Spark

V této části nakonfigurujete magii Spark, kterou jste nainstalovali dříve pro připojení ke clusteru Apache Spark.

1. Spusťte prostředí Pythonu pomocí následujícího příkazu:

    ```cmd
    python
    ```

2. Informace o konfiguraci Jupyter je obvykle uloženv domovském adresáři uživatelů. Chcete-li identifikovat domovský adresář, zadejte následující příkaz a vytvořte složku nazvanou ** \.sparkmagic**.  Úplná cesta bude výstupem.

    ```python
    import os
    path = os.path.expanduser('~') + "\\.sparkmagic"
    os.makedirs(path)
    print(path)
    exit()
    ```

3. Ve složce `.sparkmagic`vytvořte soubor s názvem **config.json** a přidejte do ní následující úryvek JSON.  

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

      "custom_headers" : {
        "X-Requested-By": "livy"
      },

      "heartbeat_refresh_seconds": 5,
      "livy_server_heartbeat_timeout_seconds": 60,
      "heartbeat_retry_seconds": 1
    }
    ```

4. Proveďte následující úpravy souboru:

    |Hodnota šablony | Nová hodnota |
    |---|---|
    |{UŽIVATELSKÉ JMÉNO}|Přihlášení clusteru, `admin`výchozí hodnota je .|
    |{NÁZEV_CLUSTERDNSNAME}|Název clusteru|
    |{BASE64ENCODEDPASSWORD}|Základní heslo 64 kódované pro vaše skutečné heslo.  Můžete vygenerovat base64 [https://www.url-encode-decode.com/base64-encode-decode/](https://www.url-encode-decode.com/base64-encode-decode/)heslo na .|
    |`"livy_server_heartbeat_timeout_seconds": 60`|Pokud používáte `sparkmagic 0.12.7` (clustery v3.5 a v3.6).  Pokud `sparkmagic 0.2.3` používáte (clustery v3.4), nahraďte . `"should_heartbeat": true`|

    Úplný ukázkový soubor můžete zobrazit na [ukázkovém souboru config.json](https://github.com/jupyter-incubator/sparkmagic/blob/master/sparkmagic/example_config.json).

   > [!TIP]  
   > Prezenční signály jsou odesílány, aby bylo zajištěno, že relace nejsou neunikly. Když počítač přejde do režimu spánku nebo je vypnut, prezenční signál není odeslán, výsledkem je vyčištění relace. Pro clustery v3.4, pokud chcete zakázat toto chování, můžete `livy.server.interactive.heartbeat.timeout` `0` nastavit Livy config z ui Ambari. Pokud pro clustery v3.5 nenastavíte výše uvedenou konfiguraci 3.5, relace nebude odstraněna.

5. Start Jupyter. Použijte následující příkaz z příkazového řádku.

    ```cmd
    jupyter notebook
    ```

6. Ověřte, zda můžete použít kouzlo Spark dostupné s jádry. Proveďte následující kroky.

    a. Vytvořte nový poznámkový blok. V pravém rohu vyberte **Nový**. Měli byste vidět výchozí jádro **Python 2** nebo **Python 3** a jádra, která jste nainstalovali. Skutečné hodnoty se mohou lišit v závislosti na možnostech instalace.  Vyberte **Možnost PySpark**.

    ![Dostupná jádra v jupyterovém notebooku](./media/apache-spark-jupyter-notebook-install-locally/jupyter-kernels-notebook.png "Jádra v jupyterovém notebooku")

    > [!IMPORTANT]  
    > Po výběru **nové** zkontrolujte shell pro případné chyby.  Pokud se zobrazí `TypeError: __init__() got an unexpected keyword argument 'io_loop'` chyba, může se u vás vyskytnat známý problém s určitými verzemi Tornado.  Pokud ano, zastavte jádro a potom downgrade `pip install tornado==4.5.3`instalaci Tornado s následujícím příkazem: .

    b. Spusťte následující fragment kódu.

    ```sql
    %%sql
    SELECT * FROM hivesampletable LIMIT 5
    ```  

    Pokud můžete úspěšně načíst výstup, vaše připojení ke clusteru HDInsight je testováno.

    Chcete-li aktualizovat konfiguraci poznámkového bloku pro připojení k jinému clusteru, aktualizujte soubor config.json novou sadou hodnot, jak je znázorněno v kroku 3 výše.

## <a name="why-should-i-install-jupyter-on-my-computer"></a>Proč bych měl nainstalovat Jupyter na mém počítači?

Důvody pro instalaci Jupyteru do počítače a jeho připojení k clusteru Apache Spark na HDInsightu:

* Poskytuje možnost vytvořit poznámkové bloky místně, otestovat aplikaci proti spuštěnému clusteru a potom je nahrát do clusteru. Chcete-li poznámkové bloky nahrát do clusteru, můžete je buď nahrát pomocí spuštěného poznámkového bloku Jupyter nebo clusteru, nebo je uložit do `/HdiNotebooks` složky v účtu úložiště přidruženém ke clusteru. Další informace o tom, jak jsou poznámkové bloky uloženy v clusteru, najdete v tématu [Kde jsou uloženy poznámkové bloky Jupyter?](apache-spark-jupyter-notebook-kernels.md#where-are-the-notebooks-stored)
* Díky místním poznámkovým blokům se můžete připojit k různým clusterům Spark na základě požadavků aplikace.
* GitHub můžete použít k implementaci systému správy zdrojového kódu a mít správu verzí pro poznámkové bloky. Můžete mít také prostředí pro spolupráci, ve kterém může se stejným poznámkovým blokem pracovat více uživatelů.
* S poznámkovými bloky můžete pracovat místně, aniž byste měli cluster. K testování poznámkových bloků potřebujete pouze cluster, nikoli ruční správu poznámkových bloků nebo vývojového prostředí.
* Může být jednodušší nakonfigurovat vlastní místní vývojové prostředí než konfiguraci instalace Jupyter v clusteru.  Můžete využít veškerý software, který jste nainstalovali místně, aniž byste konfigurovali jeden nebo více vzdálených clusterů.

> [!WARNING]  
> S nainstalovaným Jupyterem v místním počítači může více uživatelů spustit stejný poznámkový blok ve stejném clusteru Spark současně. V takové situaci jsou vytvořeny více livy relací. Pokud narazíte na problém a chcete ladit, bude to složitá úloha sledovat, které livy relace patří kterému uživateli.  

## <a name="next-steps"></a>Další kroky

* [Přehled: Apache Spark v Azure HDInsight](apache-spark-overview.md)
* [Apache Spark s BI: Analyzujte data Apache Spark pomocí Power BI v HDInsightu](apache-spark-use-bi-tools.md)
* [Apache Spark se strojovým učením: Použijte Spark v HDInsightu pro analýzu teploty budovy pomocí dat HVAC](apache-spark-ipython-notebook-machine-learning.md)
