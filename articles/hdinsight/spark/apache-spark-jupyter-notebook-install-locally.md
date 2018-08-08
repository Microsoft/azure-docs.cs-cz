---
title: Instalace Jupyteru místně a připojení ke Sparku v Azure HDInsight
description: Zjistěte, jak místně do počítače nainstalovat Poznámkový blok Jupyter a připojte ho ke clusteru Apache Spark.
services: hdinsight
ms.service: hdinsight
author: jasonwhowell
editor: jasonwhowell
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 11/28/2017
ms.author: jasonh
ms.openlocfilehash: 5e1a089f24a3223220b703bd4225e2750c7cae72
ms.sourcegitcommit: 35ceadc616f09dd3c88377a7f6f4d068e23cceec
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/08/2018
ms.locfileid: "39619239"
---
# <a name="install-jupyter-notebook-on-your-computer-and-connect-to-apache-spark-on-hdinsight"></a>Na váš počítač nainstalovat Poznámkový blok Jupyter a připojte se k Apache Spark v HDInsight

V tomto článku se dozvíte, jak instalace Poznámkový blok Jupyter s vlastní PySpark (pro jazyk Python) a Spark (pro Scala) jader s Spark magic a připojení ke clusteru HDInsight poznámkového bloku. Může existovat několik důvodů, proč instalace Jupyteru do místního počítače a může být také některé běžné problémy. Další informace o to, najdete v části [proč by instalace Jupyteru do počítače](#why-should-i-install-jupyter-on-my-computer) na konci tohoto článku.

Při instalaci do počítače Jupyter a Spark magic jsou tři klíčové kroky.

* Instalace aplikace Jupyter notebook
* Instalace jádra PySpark a Spark s kouzlem Spark
* Konfigurace magic Spark pro přístup ke clusteru Spark v HDInsight

Další informace o jádrech vlastní a k dispozici pro poznámkové bloky Jupyter s clusterem HDInsight Spark magic, naleznete v tématu [jádra dostupná pro poznámkové bloky Jupyter s Apache Spark Linux clusterů v HDInsight](apache-spark-jupyter-notebook-kernels.md).

## <a name="prerequisites"></a>Požadavky
Požadavky uvedené v tomto poli nejsou pro instalaci Jupyter. Jedná se o připojení Poznámkový blok Jupyter ke clusteru služby HDInsight po instalaci poznámkového bloku.

* Předplatné Azure. Viz [Získání bezplatné zkušební verze Azure](https://azure.microsoft.com/documentation/videos/get-azure-free-trial-for-testing-hadoop-in-hdinsight/).
* Cluster Apache Spark ve službě HDInsight. Pokyny najdete v tématu [Vytváření clusterů Apache Spark ve službě Azure HDInsight](apache-spark-jupyter-spark-sql.md).

## <a name="install-jupyter-notebook-on-your-computer"></a>Na váš počítač nainstalovat aplikace Jupyter notebook

Před instalací poznámkové bloky Jupyter, je nutné nainstalovat Python. Python a Jupyter je k dispozici jako součást [distribuce Anacondy](https://www.continuum.io/downloads). Při instalaci Anacondy nainstalujete distribuci jazyka Python. Po instalaci Anacondy přidáte instalace Jupyteru spuštěním příslušnými příkazy.

1. Stáhněte si [instalační program Anaconda](https://www.continuum.io/downloads) pro platformu a spusťte instalační program. Při spuštění Průvodce instalací, ujistěte se, zda že jste vybrali možnost Přidat Anaconda do proměnné PATH.
1. Spusťte následující příkaz k instalaci aplikace Jupyter.

        conda install jupyter

    Další informace o instalaci Jupyter, naleznete v tématu [instalace Jupyteru pomocí Anaconda](http://jupyter.readthedocs.io/en/latest/install.html).

## <a name="install-the-kernels-and-spark-magic"></a>Instalace jádra a Spark magic

Pokyny o tom, jak nainstalovat Spark magic jádra PySpark a Spark, postupujte podle pokynů k instalaci v [sparkmagic dokumentaci](https://github.com/jupyter-incubator/sparkmagic#installation) na Githubu. Prvním krokem v dokumentaci magic Spark žádostí o instalaci Spark magic. Pomocí následujících příkazů nahraďte tento první krok v odkazu, v závislosti na verzi clusteru HDInsight se připojíte k. Potom postupujte podle pokynů v dokumentaci magic Spark. Pokud chcete nainstalovat jiný jádrech, je nutné provést krok 3 v části Spark magic instalační pokyny.

* Pro clustery verzi 3.4 nainstalujte sparkmagic 0.2.3 spuštěním `pip install sparkmagic==0.2.3`

* Pro clustery v3.5 a v3.6 nainstalujte sparkmagic 0.11.2 spuštěním `pip install sparkmagic==0.11.2`

## <a name="configure-spark-magic-to-connect-to-hdinsight-spark-cluster"></a>Konfigurace magic Spark pro připojení ke clusteru HDInsight Spark

V této části nakonfigurujete Kouzlo Spark, který jste dříve nainstalovali pro připojení ke clusteru Apache Spark, který musí již jste vytvořili v Azure HDInsight.

1. Informace o konfiguraci Jupyter je obvykle uložen do domovského adresáře uživatele. Chcete-li vyhledat domovského adresáře na libovolné platformě operačního systému, zadejte následující příkazy.

    Spusťte prostředí Pythonu. Na příkazovém řádku zadejte následující příkaz:

        python

    V prostředí Python zadejte následující příkaz a zjistěte domovský adresář.

        import os
        print(os.path.expanduser('~'))

1. Přejděte do domovského adresáře a vytvořte složku s názvem **.sparkmagic** Pokud ještě neexistuje.
1. Ve složce vytvořte soubor s názvem **config.json** a přidejte následující fragment kódu JSON dovnitř.

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
          }
        }

1. Náhradní **{USERNAME}**, **{NÁZEV_DNS_CLUSTERU}**, a **{BASE64ENCODEDPASSWORD}** příslušnými hodnotami. Celou řadou nástrojů ve vašem oblíbeném programovacím jazyce nebo online můžete použít ke generování hesla kódovanou jako base64 pro vaše vlastní heslo.

1. Nakonfigurovat správné nastavení prezenčního signálu v `config.json`. Měli byste přidat tato nastavení na stejné úrovni jako `kernel_python_credentials` a `kernel_scala_credentials` fragmenty vaše přidaných výše. Příklad toho, jak a kam přidat nastavení prezenčního signálu, najdete v tomto [ukázka config.json](https://github.com/jupyter-incubator/sparkmagic/blob/master/sparkmagic/example_config.json).

    * Pro `sparkmagic 0.2.3` (clusterů verzi 3.4), zahrnují:

            "should_heartbeat": true,
            "heartbeat_refresh_seconds": 5,
            "heartbeat_retry_seconds": 1

    * Pro `sparkmagic 0.11.2` (v3.5 clusterů a v3.6), zahrnují:

            "heartbeat_refresh_seconds": 5,
            "livy_server_heartbeat_timeout_seconds": 60,
            "heartbeat_retry_seconds": 1

    >[!TIP]
    >Ujistěte se, že nedošlo k úniku relací jsou odesílány prezenční signály. Když počítač přejde do režimu spánku nebo je vypnutý, nejsou zasílány prezenčního signálu, což vede k relaci se vyčistí. Pro clustery verzi 3.4, pokud chcete toto chování zakázat, můžete nastavit konfiguraci Livy `livy.server.interactive.heartbeat.timeout` k `0` z uživatelského rozhraní Ambari. Pro clustery v3.5 Pokud nenastavíte výše, verze 3.5 Konfigurace relace se neodstraní.

1. Začněte Jupyter. Použijte následující příkaz z příkazového řádku.

        jupyter notebook

1. Ověřte, zda se můžete připojit ke clusteru pomocí poznámkového bloku Jupyter a, které můžete použít k dispozici Spark magic s jádrech. Proveďte následující kroky.

    a. Vytvořte nový poznámkový blok. V pravém rohu klikněte na **nový**. Měli byste vidět výchozí jádra **Python2** a dva nové jader, které nainstalujete, **PySpark** a **Spark**. Klikněte na tlačítko **PySpark**.

    ![Jádra v poznámkovém bloku Jupyter](./media/apache-spark-jupyter-notebook-install-locally/jupyter-kernels.png "jádra v poznámkovém bloku Jupyter")

    b. Spusťte následující fragment kódu.

        %%sql
        SELECT * FROM hivesampletable LIMIT 5

    Pokud můžete úspěšně načíst výstup, je otestovat připojení ke clusteru HDInsight.

    >[!TIP]
    >Pokud chcete aktualizovat konfiguraci poznámkového bloku pro připojení do jiného clusteru, aktualizujte config.json s novou sadu hodnot, jak je znázorněno v kroku 3 výše.

## <a name="why-should-i-install-jupyter-on-my-computer"></a>Proč instalace Jupyteru do počítače?
Může existovat několik důvodů, proč můžete chtít nainstalovat do počítače Jupyter a připojte ho ke clusteru Spark v HDInsight.

* I když poznámkové bloky Jupyter jsou už k dispozici pro cluster Spark v Azure HDInsight, instalace Jupyteru do počítače poskytuje možnost vytvořit místně vašich poznámkových bloků, test aplikace proti spuštěný cluster a pak nahrajte poznámkové bloky do clusteru. K nahrání poznámkových bloků do clusteru, můžete nahrát je pomocí poznámkového bloku Jupyter, na kterém běží nebo cluster nebo je uložit do složky /HdiNotebooks v účtu úložiště přidruženého clusteru. Další informace o tom, jak jsou poznámkové bloky uložené v clusteru, naleznete v tématu [umístění ukládaných poznámkové bloky Jupyter](apache-spark-jupyter-notebook-kernels.md#where-are-the-notebooks-stored)?
* S poznámkovými bloky k dispozici místně, můžete připojit k jiné clustery Spark podle požadavků vašich aplikací.
* Githubu můžete implementovat systém správy zdrojového kódu a správy verzí pro poznámkových bloků. Je také možné prostředí vytvářené společnými silami místo, kde více uživatelů může pracovat s stejného poznámkového bloku.
* Můžete pracovat s poznámkovými bloky místně i bez clusteru. Potřebujete jenom cluster k testování vašich poznámkových bloků, nechcete spravovat ručně vašich poznámkových bloků nebo vývojové prostředí.
* Může být jednodušší konfigurovat místní vývojové prostředí, než je konfigurace instalace Jupyter v clusteru.  Můžete využít výhod veškerý software, který jste si nainstalovali místně bez konfigurace jeden nebo více vzdálených clusterů.

> [!WARNING]
> S Jupyterem nainstalované na místním počítači poběží více uživatelů stejného poznámkového bloku na stejném clusteru Spark ve stejnou dobu. V takovém případě se vytvoří víc relací Livy. Pokud narazíte na problém a chcete ladit, který bude, že složité úlohy sledování relace Livy patří který uživatel.
>
>

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
* [Modul plug-in nástroje HDInsight pro IntelliJ IDEA pro vytvoření a odesílání aplikací Spark Scala](apache-spark-intellij-tool-plugin.md)
* [Použití modulu plug-in nástroje HDInsight pro IntelliJ IDEA pro vzdálené ladění aplikací Spark](apache-spark-intellij-tool-plugin-debug-jobs-remotely.md)
* [Použití poznámkových bloků Zeppelin s clusterem Sparku v HDInsight](apache-spark-zeppelin-notebook.md)
* [Jádra dostupná pro poznámkový blok Jupyter v clusteru Sparku pro HDInsight](apache-spark-jupyter-notebook-kernels.md)
* [Použití externích balíčků s poznámkovými bloky Jupyter](apache-spark-jupyter-notebook-use-external-packages.md)

### <a name="manage-resources"></a>Správa prostředků
* [Správa prostředků v clusteru Apache Spark v Azure HDInsight](apache-spark-resource-manager.md)
* [Sledování a ladění úloh spuštěných v clusteru Apache Spark v HDInsight](apache-spark-job-debugging.md)
