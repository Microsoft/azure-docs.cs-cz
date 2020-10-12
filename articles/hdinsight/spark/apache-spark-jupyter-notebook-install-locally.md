---
title: Místní instalace Jupyter a připojení ke Sparku v Azure HDInsight
description: Přečtěte si, jak místně nainstalovat Poznámkový blok Jupyter na počítači a připojit ho ke clusteru Apache Spark.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: how-to
ms.custom: hdinsightactive,seoapr2020, devx-track-python
ms.date: 04/23/2020
ms.openlocfilehash: 96b2e7deff464f00ced4457a514ac833a90bd42d
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "87873887"
---
# <a name="install-jupyter-notebook-on-your-computer-and-connect-to-apache-spark-on-hdinsight"></a>Instalace poznámkového bloku Jupyter na vašem počítači a připojení k Apache Spark v HDInsight

V tomto článku se dozvíte, jak nainstalovat Jupyter Poznámkový blok s vlastními jádry PySpark (for Python) a Apache Spark (pro Scala) pomocí Spark Magic. Pak tento poznámkový blok připojíte ke clusteru HDInsight.

Při instalaci Jupyter a připojení k Apache Spark ve službě HDInsight jsou zahrnuty čtyři klíčové kroky.

* Nakonfigurujte cluster Spark.
* Nainstalujte Poznámkový blok Jupyter.
* Nainstalujte jádra PySpark a Spark pomocí Spark Magic.
* Nakonfigurujte Spark Magic pro přístup ke clusteru Spark v HDInsight.

Další informace o vlastních jádrech a Spark Magic najdete v tématu [jádra dostupná pro poznámkové bloky Jupyter s clustery Apache Spark Linux v HDInsight](apache-spark-jupyter-notebook-kernels.md).

## <a name="prerequisites"></a>Požadavky

* Cluster Apache Spark ve službě HDInsight. Pokyny najdete v tématu [Vytváření clusterů Apache Spark ve službě Azure HDInsight](apache-spark-jupyter-spark-sql.md). Místní Poznámkový blok se připojí ke clusteru HDInsight.

* Znalost používání poznámkových bloků Jupyter se Sparkem ve službě HDInsight.

## <a name="install-jupyter-notebook-on-your-computer"></a>Instalace poznámkového bloku Jupyter do počítače

Před instalací poznámkových bloků Jupyter nainstalujte Python. [Distribuce Anaconda](https://www.anaconda.com/download/) bude instalovat oba, Python i Jupyter notebook.

Stáhněte si [instalační program Anaconda](https://www.anaconda.com/download/) pro vaši platformu a spusťte instalační program. Při spuštění Průvodce instalací se ujistěte, že jste vybrali možnost Přidat Anaconda do proměnné PATH.  Přečtěte si také článek [instalace Jupyter pomocí Anaconda](https://jupyter.readthedocs.io/en/latest/install.html).

## <a name="install-spark-magic"></a>Instalace Spark Magic

1. Zadejte jeden z následujících příkazů pro instalaci Spark Magic. Viz také [dokumentace k sparkmagic](https://github.com/jupyter-incubator/sparkmagic#installation).

    |Verze clusteru | Příkaz Install |
    |---|---|
    |v 3.6 a v 3.5 |`pip install sparkmagic==0.13.1`|
    |v 3.4|`pip install sparkmagic==0.2.3`|

1. Ujistěte `ipywidgets` se, že je správně nainstalován, spuštěním následujícího příkazu:

    ```cmd
    jupyter nbextension enable --py --sys-prefix widgetsnbextension
    ```

## <a name="install-pyspark-and-spark-kernels"></a>Instalace jader PySpark a Sparku

1. Určete, kam `sparkmagic` se má nainstalovat, zadáním následujícího příkazu:

    ```cmd
    pip show sparkmagic
    ```

    Pak změňte pracovní adresář na **umístění** identifikované pomocí výše uvedeného příkazu.

1. V novém pracovním adresáři zadejte jeden nebo více níže uvedených příkazů, aby bylo možné nainstalovat požadované jádro (y):

    |jádro | Příkaz |
    |---|---|
    |Spark|`jupyter-kernelspec install sparkmagic/kernels/sparkkernel`|
    |SparkR|`jupyter-kernelspec install sparkmagic/kernels/sparkrkernel`|
    |PySpark|`jupyter-kernelspec install sparkmagic/kernels/pysparkkernel`|
    |PySpark3|`jupyter-kernelspec install sparkmagic/kernels/pyspark3kernel`|

1. Nepovinný parametr. Pokud chcete povolit rozšíření serveru, zadejte následující příkaz:

    ```cmd
    jupyter serverextension enable --py sparkmagic
    ```

## <a name="configure-spark-magic-to-connect-to-hdinsight-spark-cluster"></a>Konfigurace Spark Magic pro připojení ke clusteru HDInsight Spark

V této části nakonfigurujete Spark Magic, který jste nainstalovali dříve, abyste se připojili ke clusteru Apache Spark.

1. Spusťte prostředí Python Shell pomocí následujícího příkazu:

    ```cmd
    python
    ```

2. Informace o konfiguraci Jupyter se většinou ukládají do domovského adresáře uživatelů. Zadejte následující příkaz pro identifikaci domovského adresáře a vytvořte složku s názvem ** \. sparkmagic**.  Úplná cesta bude vycházet z výstupu.

    ```python
    import os
    path = os.path.expanduser('~') + "\\.sparkmagic"
    os.makedirs(path)
    print(path)
    exit()
    ```

3. V rámci složky `.sparkmagic` vytvořte soubor s názvem **config.jsna** a přidejte do něj následující fragment kódu JSON.  

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
    |JMEN|Přihlášení clusteru, výchozí hodnota je `admin` .|
    |{CLUSTERDNSNAME}|Název clusteru|
    |{BASE64ENCODEDPASSWORD}|Heslo kódované v kódování Base64 pro vaše skutečné heslo.  Můžete vygenerovat heslo Base64 na adrese [https://www.url-encode-decode.com/base64-encode-decode/](https://www.url-encode-decode.com/base64-encode-decode/) .|
    |`"livy_server_heartbeat_timeout_seconds": 60`|Používejte při použití `sparkmagic 0.12.7` (clustery v 3.5 a v 3.6).  Pokud používáte `sparkmagic 0.2.3` (clustery v 3.4), nahraďte parametr `"should_heartbeat": true` .|

    Úplný ukázkový soubor můžete zobrazit v [ukázce config.jsna](https://github.com/jupyter-incubator/sparkmagic/blob/master/sparkmagic/example_config.json).

   > [!TIP]  
   > Odesílají se prezenční signály, aby se zajistilo, že nedojde k úniku relací. Když počítač přejde do režimu spánku nebo se vypne, prezenční signál se nepošle, takže se vyčistí relace. U clusterů v 3.4, pokud chcete toto chování zakázat, můžete nastavit konfiguraci Livy `livy.server.interactive.heartbeat.timeout` na `0` z uživatelského rozhraní Ambari. Pokud v části clustery v 3.5 nenastavíte výše uvedenou konfiguraci 3,5, relace se neodstraní.

5. Spusťte Jupyter. Z příkazového řádku použijte následující příkaz.

    ```cmd
    jupyter notebook
    ```

6. Ověřte, že můžete používat rozhraní Spark Magic dostupné s jádry. Proveďte následující kroky.

    a. Vytvořte nový poznámkový blok. V pravém horním rohu vyberte **Nový**. Měli byste vidět výchozí jádro **Python 2** nebo **Python 3** a jádra, která jste nainstalovali. Skutečné hodnoty se mohou lišit v závislosti na možnostech instalace.  Vyberte **PySpark**.

    ![Dostupná jádra v poznámkovém bloku Jupyter](./media/apache-spark-jupyter-notebook-install-locally/jupyter-kernels-notebook.png "Jádra v Jupyter poznámkovém bloku")

    > [!IMPORTANT]  
    > Po výběru **nové** kontroly prostředí zkontrolujte případné chyby.  Pokud se zobrazí chyba `TypeError: __init__() got an unexpected keyword argument 'io_loop'` , pravděpodobně došlo k známému problému s některými verzemi Tornado.  Pokud ano, zastavte jádro a pak downgrade instalace Tornado pomocí následujícího příkazu: `pip install tornado==4.5.3` .

    b. Spusťte následující fragment kódu.

    ```sql
    %%sql
    SELECT * FROM hivesampletable LIMIT 5
    ```  

    Pokud můžete výstup úspěšně načíst, otestuje se připojení ke clusteru HDInsight.

    Pokud chcete aktualizovat konfiguraci poznámkového bloku pro připojení k jinému clusteru, aktualizujte config.jsna novou sadu hodnot, jak je znázorněno v kroku 3 výše.

## <a name="why-should-i-install-jupyter-on-my-computer"></a>Proč mám na svém počítači instalovat Jupyter?

Důvody instalace Jupyter do počítače a jeho připojení k Apache Sparkmu clusteru v HDInsight:

* Poskytuje možnost vytvářet místně poznámkové bloky, testovat aplikace proti běžícímu clusteru a pak poznámkové bloky nahrát do clusteru. Pokud chcete nahrát poznámkové bloky do clusteru, můžete je buď nahrát pomocí poznámkového bloku Jupyter, který je spuštěný, nebo v clusteru, nebo je Uložit do `/HdiNotebooks` složky v účtu úložiště přidruženého ke clusteru. Další informace o tom, jak se poznámkové bloky ukládají do clusteru, najdete v tématu [kde jsou uložené poznámkové bloky Jupyter](apache-spark-jupyter-notebook-kernels.md#where-are-the-notebooks-stored)?
* Místně dostupné poznámkové bloky se můžete připojit k různým clusterům Spark na základě požadavku vaší aplikace.
* Pomocí GitHubu můžete implementovat systém správy zdrojového kódu a mít pro poznámkové bloky správu verzí. Můžete mít také prostředí pro spolupráci, ve kterém může více uživatelů pracovat se stejným poznámkovým blokem.
* S poznámkovým blokem můžete pracovat místně, aniž byste museli mít cluster. Cluster budete potřebovat jenom k testování vašich poznámkových bloků proti, ne k ruční správě vašich poznámkových bloků nebo vývojového prostředí.
* Je možné, že bude snazší nakonfigurovat vlastní místní vývojové prostředí, než aby bylo možné konfigurovat instalaci Jupyter v clusteru.  Můžete využít veškerý software, který jste nainstalovali místně, bez konfigurace jednoho nebo více vzdálených clusterů.

> [!WARNING]  
> S Jupyter nainstalovaným v místním počítači může více uživatelů spustit stejný Poznámkový blok současně na stejném clusteru Spark. V takové situaci se vytvoří několik relací Livy. Pokud narazíte na problém a chcete ho ladit, bude to složitá úloha, která bude sledovat, která relace Livy patří uživateli.  

## <a name="next-steps"></a>Další kroky

* [Přehled: Apache Spark v Azure HDInsight](apache-spark-overview.md)
* [Jádra pro Poznámkový blok Jupyter na Apache Spark](apache-spark-jupyter-notebook-kernels.md)
* [Použití externích balíčků s Jupyter poznámkovým blokům v Apache Spark](apache-spark-jupyter-notebook-use-external-packages.md)
