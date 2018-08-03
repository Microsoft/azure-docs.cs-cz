---
title: Nainstalovat Presto v clusterech Azure HDInsight s Linuxem | Dokumentace Microsoftu
description: Zjistěte, jak nainstalovat Presto a Airpal v clusterech Hadoop využívající systém Linux HDInsight pomocí skriptových akcí.
services: hdinsight
documentationcenter: ''
author: nitinme
manager: jhubbard
editor: cgronlun
ms.service: hdinsight
ms.custom: hdinsightactive
ms.devlang: na
ms.topic: conceptual
ms.date: 02/21/2018
ms.author: nitinme
ms.openlocfilehash: 26b5072cc259462c91530752a26a961d8aab4b7b
ms.sourcegitcommit: 1d850f6cae47261eacdb7604a9f17edc6626ae4b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/02/2018
ms.locfileid: "39429818"
---
# <a name="install-and-use-presto-on-hdinsight-hadoop-clusters"></a>Nainstalovat a používat Presto na clusterech HDInsight Hadoop

V tomto dokumentu se dozvíte, jak nainstalovat Presto na clusterech HDInsight Hadoop pomocí skriptových akcí. Také se dozvíte, jak nainstalovat Airpal do existujícího clusteru HDInsight Presto.

> [!IMPORTANT]
> Kroky v tomto dokumentu vyžadují **cluster HDInsight 3.5 Hadoop** , který využívá systém Linux. HDInsight od verze 3.4 výše používá výhradně operační systém Linux. Další informace najdete v tématu [HDInsight verze](hdinsight-component-versioning.md).

## <a name="what-is-presto"></a>Co je Presto?
[Presto](https://prestodb.io/overview.html) rychlé distribuované dotazovací modul SQL pro velké objemy dat je. Presto je vhodný pro interaktivní dotazování petabajty dat. Podrobnosti o součásti Presto a jak pracují společně, naleznete v tématu [Presto koncepty](https://github.com/prestodb/presto/blob/master/presto-docs/src/main/sphinx/overview/concepts.rst).

> [!WARNING]
> Součásti, které jsou součástí clusteru HDInsight jsou plně podporované a Microsoft Support pomáhá izolovat a vyřešit problémy týkající se těchto součástí.
> 
> Vlastní komponenty, jako je například Presto, přijímat obchodně přiměřenou podporu můžete-li dále řešit tento problém. To může vést řeší problém nebo s výzvou k zapojení dostupné kanály pro open source technologie, ve kterých se nachází rozsáhlé znalosti pro tuto technologii. Existuje například mnoho komunitním webům, které lze použít jako: [fórum na webu MSDN pro HDInsight](https://social.msdn.microsoft.com/Forums/azure/home?forum=hdinsight), [ http://stackoverflow.com ](http://stackoverflow.com). Také projektů Apache mít projektovým webům na [ http://apache.org ](http://apache.org), například: [Hadoop](http://hadoop.apache.org/).
> 
> 


## <a name="install-presto-using-script-action"></a>Nainstalovat Presto pomocí akce skriptu

Tato část obsahuje informace o tom, jak pomocí vzorového skriptu při vytváření nového clusteru pomocí webu Azure portal. 

1. Zahajte zřizování clusteru pomocí kroků v [clustery HDInsight se systémem Linux](hdinsight-hadoop-create-linux-clusters-portal.md). Ujistěte se, že vytváření clusteru pomocí **vlastní** clusteru vytváření toku. Cluster musí splňovat následující požadavky.

    * Musí být clusteru Hadoop s HDInsight verze 3.6.

    * Jako úložiště dat musí používat Azure Storage. Použití Presto do clusteru, který používá Azure Data Lake Store jako možnost úložiště se ještě nepodporuje. 

    ![Vytvoření clusteru HDInsight pomocí vlastních možností](./media/hdinsight-hadoop-install-presto/hdinsight-install-custom.png)

2. Na **upřesňující nastavení** vyberte **akcí skriptů**a zadejte následující informace:
   
   * **NÁZEV**: Zadejte popisný název akce skriptu.
   * **Identifikátor URI skriptu Bash:**`https://raw.githubusercontent.com/hdinsight/presto-hdinsight/master/installpresto.sh`
   * **HLAVNÍ**: Zaškrtněte tuto možnost
   * **PRACOVNÍK**: Zaškrtněte tuto možnost
   * **ZOOKEEPER**: zrušte zaškrtnutí tohoto políčka
   * **Parametry**: Toto pole nechat prázdné


3. V dolní části **akcí skriptů** oblast, klikněte na tlačítko **vyberte** tlačítko, čímž konfiguraci uložíte. Nakonec klikněte na tlačítko **vyberte** tlačítko v dolní části **Upřesnit nastavení** oblast, kterou chcete uložit informace o konfiguraci.

4. Pokračovat zřizování clusteru, jak je popsáno v [clustery HDInsight se systémem Linux](hdinsight-hadoop-create-linux-clusters-portal.md).

    > [!NOTE]
    > Prostředí Azure PowerShell, rozhraní příkazového řádku Azure, sady HDInsight .NET SDK nebo šablony Azure Resource Manageru je také možné použití akce skriptu. Akce se skripty můžete také použít k už běží clustery. Další informace najdete v tématu [HDInsight přizpůsobit clustery pomocí skriptových akcí](hdinsight-hadoop-customize-cluster-linux.md).
    > 
    > 

## <a name="use-presto-with-hdinsight"></a>Použití Presta s HDInsight

Pro práci s Presto v clusteru služby HDInsight, postupujte následovně:

1. Připojte se ke clusteru HDInsight pomocí protokolu SSH:
   
        ssh USERNAME@CLUSTERNAME-ssh.azurehdinsight.net
   
    Další informace najdete v tématu [Použití SSH se službou HDInsight](hdinsight-hadoop-linux-use-ssh-unix.md).
     

2. Začněte Presto prostředí pomocí následujícího příkazu.
   
        presto --schema default

3. Spustit dotaz na ukázkovou tabulku **hivesampletable**, která je k dispozici na všech clusterech HDInsight ve výchozím nastavení.
   
        select count (*) from hivesampletable;
   
    Ve výchozím nastavení [Hive](https://prestodb.io/docs/current/connector/hive.html) a [TPCH](https://prestodb.io/docs/current/connector/tpch.html) konektory pro Presto jsou už nakonfigurovaná. Konektor Hive je nakonfigurován pro použití Hive instalace výchozí instalaci, takže všechny tabulky z podregistru budou automaticky viditelná v Presto.

    Další informace najdete v tématu [Presto dokumentaci](https://prestodb.io/docs/current/index.html).

## <a name="use-airpal-with-presto"></a>Pomocí Airpal Presta

[Airpal](https://github.com/airbnb/airpal#airpal) je open source dotazů založených na webové rozhraní pro Presto. Další informace o Airpal najdete v tématu [Airpal dokumentaci](https://github.com/airbnb/airpal#airpal).

K instalaci Airpal na hraničním uzlu, postupujte následovně:

1. Pomocí SSH se připojte k hlavnímu uzlu clusteru HDInsight, která má nainstalovanou Presto:
   
        ssh USERNAME@CLUSTERNAME-ssh.azurehdinsight.net
   
    Další informace najdete v tématu [Použití SSH se službou HDInsight](hdinsight-hadoop-linux-use-ssh-unix.md).

2. Jakmile se připojíte, spusťte následující příkaz.

        sudo slider registry  --name presto1 --getexp presto 
   
    Zobrazí výstup podobný následující JSON:

        {
            "coordinator_address" : [ {
                "value" : "10.0.0.12:9090",
                "level" : "application",
                "updatedTime" : "Mon Apr 03 20:13:41 UTC 2017"
        } ]

3. Z výstupu, poznamenejte si hodnotu pro **hodnotu** vlastnost. Tuto hodnotu budete potřebovat při instalaci Airpal na koncového uzlu clusteru. Ve výstupu výš, je hodnota, která budete potřebovat **10.0.0.12:9090**.

4. Použití šablony **[tady](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2Fhdinsight%2Fpresto-hdinsight%2Fmaster%2Fairpal-deploy.json)** k vytvoření koncového uzlu clusteru HDInsight a zadejte hodnoty, jak je znázorněno na následujícím snímku obrazovky.

    ![HDInsight nainstalovat Airpal Presto clusteru](./media/hdinsight-hadoop-install-presto/hdinsight-install-airpal.png)

5. Klikněte na **Koupit**.

6. Až změny se použijí ke konfiguraci clusteru, dostanete Airpal webové rozhraní, pomocí následujících kroků.

    1. V dialogovém okně clusteru klikněte na **aplikací**.

        ![HDInsight spuštění Airpal Presto clusteru](./media/hdinsight-hadoop-install-presto/hdinsight-presto-launch-airpal.png)

    2. Z **nainstalované aplikace** oblast, klikněte na tlačítko **portál** proti airpal.

        ![HDInsight spuštění Airpal Presto clusteru](./media/hdinsight-hadoop-install-presto/hdinsight-presto-launch-airpal-1.png)

    3. Po zobrazení výzvy zadejte přihlašovací údaje správce, které jste zadali při vytváření clusteru HDInsight Hadoop.

## <a name="customize-a-presto-installation-on-hdinsight-cluster"></a>Přizpůsobení Presto instalace v clusteru HDInsight

K přizpůsobení instalace, použijte následující postup:

1. Pomocí SSH se připojte k hlavnímu uzlu clusteru HDInsight, která má nainstalovanou Presto:
   
        ssh USERNAME@CLUSTERNAME-ssh.azurehdinsight.net
   
    Další informace najdete v tématu [Použití SSH se službou HDInsight](hdinsight-hadoop-linux-use-ssh-unix.md).

2. Proveďte požadované změny konfigurace v souboru `/var/lib/presto/presto-hdinsight-master/appConfig-default.json`. Další informace o konfiguraci Presto, naleznete v tématu [Presto konfigurace pro clustery založené na YARN](https://prestodb.io/presto-yarn/installation-yarn-configuration-options.html).

3. Zastavit a ukončit aktuální spuštěné instance Presto.

        sudo slider stop presto1 --force
        sudo slider destroy presto1 --force

4. Spusťte novou instanci třídy Presto vlastními změnami.

       sudo slider create presto1 --template /var/lib/presto/presto-hdinsight-master/appConfig-default.json --resources /var/lib/presto/presto-hdinsight-master/resources-default.json

5. Počkejte, nové instance bude připravená a poznamenejte si adresu presto koordinátora.


       sudo slider registry --name presto1 --getexp presto

## <a name="generate-benchmark-data-for-hdinsight-clusters-that-run-presto"></a>Generovat data srovnávacích testů pro clustery HDInsight se spuštěnou technologií Presto

Oborový standard pro měření výkonu mnoho rozhodnutí podpora systémů, včetně systémy velkých objemů dat je TPC-DS. Presto můžete použít pro generování dat a vyhodnotit jejím srovnání s vlastními daty srovnávací test HDInsight. Další informace najdete v tématu [tady](https://github.com/hdinsight/tpcds-datagen-as-hive-query/blob/master/README.md).



## <a name="see-also"></a>Další informace najdete v tématech
* [Nainstalovat a používat rozhraní Hue v clusterech HDInsight](hdinsight-hadoop-hue-linux.md). Odstín, který je webové uživatelské rozhraní, které umožňuje snadno vytvářet, spouštět a uložit Pigu a Hivu existující úlohy.

* [Clustery HDInsight nainstalovat Giraph](hdinsight-hadoop-giraph-install-linux.md). Clustery HDInsight Hadoop nainstalovat Giraph pomocí přizpůsobení clusteru. Giraph umožňuje provádět zpracování s použitím Hadoopu grafů a jde použít s Azure HDInsight.

* [Nainstalovat Solr na clusterech HDInsight](hdinsight-hadoop-solr-install-linux.md). Clusterů systému HDInsight Hadoop nainstalovat Solr pomocí přizpůsobení clusteru. Solr umožňuje provádět operace výkonné hledání na uložená data.

[hdinsight-install-r]: hdinsight-hadoop-r-scripts-linux.md
[hdinsight-cluster-customize]: hdinsight-hadoop-customize-cluster-linux.md
