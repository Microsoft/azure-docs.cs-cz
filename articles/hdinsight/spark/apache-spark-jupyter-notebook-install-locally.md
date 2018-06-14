---
title: Instalace Jupyter místně a připojení ke clusteru Azure HDInsight Spark | Microsoft Docs
description: Naučte se nainstalovat Poznámkový blok Jupyter místně na váš počítač a připojte ho k cluster Apache Spark v Azure HDInsight.
services: hdinsight
documentationcenter: ''
author: nitinme
manager: jhubbard
editor: cgronlun
tags: azure-portal
ms.assetid: 48593bdf-4122-4f2e-a8ec-fdc009e47c16
ms.service: hdinsight
ms.custom: hdinsightactive
ms.devlang: na
ms.topic: conceptual
ms.date: 11/28/2017
ms.author: nitinme
ms.openlocfilehash: eea61586054f34142d77f16333fe70a66d95d529
ms.sourcegitcommit: 1362e3d6961bdeaebed7fb342c7b0b34f6f6417a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/18/2018
ms.locfileid: "31528354"
---
# <a name="install-jupyter-notebook-on-your-computer-and-connect-to-apache-spark-on-hdinsight"></a>Do počítače nainstalovat Poznámkový blok Jupyter a připojte se k Apache Spark v HDInsight

V tomto článku a zjistěte, jak nainstalovat Poznámkový blok Jupyter, s vlastní PySpark (pro jazyk Python) a jádra Spark (pro Scala) s Spark magic a připojení ke clusteru HDInsight poznámkového bloku. Může být několik důvodů, proč nainstalovat Jupyter v místním počítači a může být také některé běžné problémy. Další informace o to, najdete v části [Proč v mém počítači nainstalujte Jupyter](#why-should-i-install-jupyter-on-my-computer) na konci tohoto článku.

Účastnící se instalace Jupyter a Spark magic v počítači jsou tři klíčové kroky.

* Nainstalujte poznámkového bloku Jupyter
* Instalace jádra PySpark a Spark pomocí Spark magic
* Konfigurace magic Spark pro přístup ke clusteru Spark v HDInsight

Další informace o vlastních jádrech a k dispozici pro poznámkové bloky Jupyter s clusterem HDInsight Spark magic najdete v tématu [jádra dostupná pro poznámkové bloky Jupyter s Apache Spark Linux clusterů v HDInsight](apache-spark-jupyter-notebook-kernels.md).

## <a name="prerequisites"></a>Požadavky
Nejsou zde uvedené předpoklady pro instalaci Jupyter. Jedná se o připojení poznámkového bloku Jupyter do clusteru HDInsight, po instalaci poznámkového bloku.

* Předplatné Azure. Viz [Získání bezplatné zkušební verze Azure](https://azure.microsoft.com/documentation/videos/get-azure-free-trial-for-testing-hadoop-in-hdinsight/).
* Cluster Apache Spark v HDInsight. Pokyny najdete v tématu [clusterů vytvořit Apache Spark v Azure HDInsight](apache-spark-jupyter-spark-sql.md).

## <a name="install-jupyter-notebook-on-your-computer"></a>Do počítače nainstalovat poznámkového bloku Jupyter

Python je třeba nainstalovat před instalací poznámkové bloky Jupyter. Python a Jupyter jsou k dispozici jako součást [Anaconda distribuční](https://www.continuum.io/downloads). Při instalaci Anaconda instalujete distribuční jazyka Python. Po instalaci Anaconda přidáte Jupyter instalaci spuštěním příslušnými příkazy.

1. Stažení [instalační program Anaconda](https://www.continuum.io/downloads) pro vaše platformy a spusťte instalaci. Při spuštění Průvodce instalací, ujistěte se, že vyberete možnost Přidat Anaconda do vaše proměnná PATH.
2. Spusťte následující příkaz pro instalaci Jupyter.

        conda install jupyter

    Další informace o instalaci Jupyter najdete v tématu [Jupyter instalaci pomocí Anaconda](http://jupyter.readthedocs.io/en/latest/install.html).

## <a name="install-the-kernels-and-spark-magic"></a>Instalace jádra a Spark magic

Pokyny k instalaci Spark magic jádra PySpark a Spark, postupujte podle pokynů instalace [sparkmagic dokumentace](https://github.com/jupyter-incubator/sparkmagic#installation) na Githubu. Prvním krokem v dokumentaci magic Spark se žádostí o instalaci Spark magic. Nahraďte tento první krok v odkazu pomocí následujících příkazů, v závislosti na verzi clusteru HDInsight se připojí k. Potom postupujte podle zbývajících kroků v dokumentaci magic Spark. Pokud chcete nainstalovat jádrech jiné, je třeba provést krok 3 v části Spark magic instalační pokyny.

* Pro clustery v3.4 nainstalujte sparkmagic 0.2.3 spuštěním `pip install sparkmagic==0.2.3`

* Pro clustery v3.5 a v3.6 nainstalujte sparkmagic 0.11.2 spuštěním `pip install sparkmagic==0.11.2`

## <a name="configure-spark-magic-to-connect-to-hdinsight-spark-cluster"></a>Konfigurace magic Spark se připojit ke clusteru HDInsight Spark

V této části nakonfigurujete magic Spark, který jste dříve nainstalovali k připojení ke clusteru Apache Spark, musí již jste vytvořili v Azure HDInsight.

1. Informace o konfiguraci Jupyter je obvykle uložen v domovském adresáři uživatele. Chcete-li vyhledat domovského adresáře na jakékoli platformě operačního systému, zadejte následující příkazy.

    Spusťte prostředí Python. V okně příkazového řádku zadejte následující příkaz:

        python

    V prostředí Python zadejte následující příkaz a zjistěte, k domovskému adresáři.

        import os
        print(os.path.expanduser('~'))

2. Přejděte do domovského adresáře a vytvořte složku s názvem **.sparkmagic** Pokud ještě neexistuje.
3. Ve složce, vytvořte soubor s názvem **config.json** a přidejte následující fragment kódu JSON je uvnitř.

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

4. SUBSTITUTE **{USERNAME}**, **{CLUSTERDNSNAME}**, a **{BASE64ENCODEDPASSWORD}** s příslušnými hodnotami. Můžete použít několik nástrojů programovacího jazyka oblíbených nebo online ke generování hesla kódováním base64 pro vlastní heslo.

5. Nakonfigurovat správné nastavení prezenčního signálu v `config.json`. Měli byste přidat tato nastavení na stejné úrovni jako `kernel_python_credentials` a `kernel_scala_credentials` fragmenty vaší přidaných výše. Příklad jak a kde se mají přidat nastavení prezenčního signálu, najdete [ukázka config.json](https://github.com/jupyter-incubator/sparkmagic/blob/master/sparkmagic/example_config.json).

    * Pro `sparkmagic 0.2.3` (clusterů v3.4), zahrnují:

            "should_heartbeat": true,
            "heartbeat_refresh_seconds": 5,
            "heartbeat_retry_seconds": 1

    * Pro `sparkmagic 0.11.2` (v3.5 clustery a v3.6), zahrnují:

            "heartbeat_refresh_seconds": 5,
            "livy_server_heartbeat_timeout_seconds": 60,
            "heartbeat_retry_seconds": 1

    >[!TIP]
    >Ujistěte se, že nejsou neuniknou relací jsou odesílány prezenčních signálů. Když počítač přejde do režimu spánku nebo je vypnutý, není odeslat prezenční signál, výsledkem relace se vyčistit. Pro clustery v3.4, pokud chcete zakázat toto chování můžete nastavit konfigurace Livy `livy.server.interactive.heartbeat.timeout` k `0` z uživatelského rozhraní Ambari. Pro clustery v3.5 Pokud není nastaveno výše, 3.5 Konfigurace relace nebudou odstraněna.

6. Spusťte Jupyter. Použijte následující příkaz z příkazového řádku.

        jupyter notebook

7. Ověřte, zda se můžete připojit ke clusteru pomocí poznámkového bloku Jupyter a, které můžete použít k dispozici Spark magic s jádrech. Proveďte následující kroky.

    a. Vytvořte nový poznámkový blok. V pravém rohu, klikněte na **nový**. Měli byste vidět jádra výchozí **Python2** a dvě nové jádra, které nainstalujete, **PySpark** a **Spark**. Klikněte na tlačítko **PySpark**.

    ![Jádra v Poznámkový blok Jupyter](./media/apache-spark-jupyter-notebook-install-locally/jupyter-kernels.png "jádra v poznámkového bloku Jupyter")

    b. Spusťte následující fragment kódu.

        %%sql
        SELECT * FROM hivesampletable LIMIT 5

    Pokud se můžete úspěšně načíst výstup, je otestovat připojení ke clusteru HDInsight.

    >[!TIP]
    >Pokud chcete aktualizovat konfiguraci Poznámkový blok pro připojení do jiného clusteru, aktualizujte config.json nové sady hodnot, jak je znázorněno v kroku 3 výše.

## <a name="why-should-i-install-jupyter-on-my-computer"></a>Proč nainstalovat Jupyter v mém počítači?
Může být z mnoha důvodů, proč můžete chtít nainstalovat Jupyter na váš počítač a připojte ho k cluster Spark v HDInsight.

* I když poznámkové bloky Jupyter jsou již k dispozici pro cluster Spark v Azure HDInsight, instalace Jupyter ve vašem počítači získáte možnost vytvořit poznámkové bloky místně, aplikaci otestovat s spuštěného clusteru a potom odeslat poznámkové bloky do clusteru. Pokud chcete nahrát poznámkových bloků do clusteru, můžete buď odešlete pomocí poznámkového bloku Jupyter, který běží nebo clusteru nebo uložíte do složky /HdiNotebooks v účtu úložiště, který je přidružen ke clusteru. Další informace o tom, jak jsou poznámkových bloků uložené v clusteru najdete v tématu [úložiště poznámkové bloky Jupyter](apache-spark-jupyter-notebook-kernels.md#where-are-the-notebooks-stored)?
* Díky poznámkových bloků, která je k dispozici místně, můžete připojit k jiné clustery Spark podle požadavků vaší aplikace.
* GitHub můžete použít k implementaci systému správy zdrojů a mají Správa verzí pro poznámkových bloků. Můžete taky nechat spolupráce prostředí, kde můžete pracovat více uživatelů se stejným poznámkového bloku.
* Můžete pracovat s poznámkových bloků místně bez nutnosti i cluster s podporou. Potřebujete jenom clusteru k testování poznámkové bloky proti, nechcete spravovat ručně poznámkové bloky nebo vývojovém prostředí.
* Může být snadněji nakonfigurovat vlastní místní vývojové prostředí, než je konfigurace instalace Jupyter v clusteru.  Můžete využít výhod všech software, který jste nainstalovali místně bez konfigurace jeden nebo více vzdálené clusterů.

> [!WARNING]
> S Jupyter nainstalovaná v místním počítači můžete více uživatelů ve stejném clusteru Spark spustit stejné poznámkového bloku, ve stejnou dobu. V takovém případě se vytvoří více Livy relací. Pokud narazíte na problém a chcete ladit, bude, že složité úlohy sledování relace které Livy patří do které uživatel.
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
