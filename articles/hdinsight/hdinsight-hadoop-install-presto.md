---
title: Nainstalujte Presto v clusterech Azure HDInsight Linux | Microsoft Docs
description: Zjistěte, jak nainstalovat Presto a Airpal na systémem Linux HDInsight Hadoop clustery pomocí akcí skriptů.
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
ms.openlocfilehash: 32b7925b7414f00dfdd7d5c8a45b3601bf58942e
ms.sourcegitcommit: 9cdd83256b82e664bd36991d78f87ea1e56827cd
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/16/2018
---
# <a name="install-and-use-presto-on-hdinsight-hadoop-clusters"></a>Na nainstalovat a používat Presto clusterů systému HDInsight Hadoop

V tomto dokumentu zjistěte, jak nainstalovat Presto na clusterů systému HDInsight Hadoop pomocí akce skriptu. Také zjistíte, jak nainstalovat Airpal na stávající cluster Presto HDInsight.

> [!IMPORTANT]
> Kroky v tomto dokumentu vyžadují **clusteru HDInsight 3.5 Hadoop** Linux, který používá. HDInsight od verze 3.4 výše používá výhradně operační systém Linux. Další informace najdete v tématu [HDInsight verze](hdinsight-component-versioning.md).

## <a name="what-is-presto"></a>Co je Presto?
[Presto](https://prestodb.io/overview.html) je rychlé distribuované modul dotazů SQL pro velká data. Presto je vhodná pro interaktivní dotazování petabajty dat. Další informace o součásti Presto a jak pracují společně, najdete v části [Presto koncepty](https://github.com/prestodb/presto/blob/master/presto-docs/src/main/sphinx/overview/concepts.rst).

> [!WARNING]
> Součásti, které jsou součástí clusteru HDInsight jsou plně podporované a Microsoft Support pomůže k izolování a vyřešení problémů týkajících se těchto součástí.
> 
> Vlastní komponenty, například Presto přijímat vyvineme podporu o pomoci při další řešení problému. To může způsobit řešení problému nebo s žádostí o zapojení dostupné kanály pro technologie s otevřeným zdrojem, kterých se nachází hluboké znalosti pro tuto technologii. Například existuje mnoho komunity webů, které lze použít jako: [fórum MSDN pro HDInsight](https://social.msdn.microsoft.com/Forums/azure/home?forum=hdinsight), [ http://stackoverflow.com ](http://stackoverflow.com). Také Apache projekty mají na projektu serverů [ http://apache.org ](http://apache.org), například: [Hadoop](http://hadoop.apache.org/).
> 
> 


## <a name="install-presto-using-script-action"></a>Nainstalujte Presto pomocí akce skriptu

Tato část obsahuje pokyny o tom, jak pomocí vzorového skriptu při vytváření nového clusteru pomocí portálu Azure. 

1. Spuštění zřizování clusteru pomocí kroků v [clustery HDInsight se systémem Linux](hdinsight-hadoop-create-linux-clusters-portal.md). Ujistěte se, vytvořit cluster pomocí **vlastní** postup vytvoření clusteru. Cluster musí splňovat následující požadavky.

    * Musí to být clusteru Hadoop v prostředí HDInsight verze 3.5.

    * Jako úložiště dat musí používat úložiště Azure. Použití Presto v clusteru, který používá Azure Data Lake Store jako možnost úložiště není dosud podporováno. 

    ![Vytvoření clusteru HDInsight pomocí vlastních možností](./media/hdinsight-hadoop-install-presto/hdinsight-install-custom.png)

2. Na **upřesňující nastavení** oblasti, vyberte **akcí skriptů**a zadejte následující informace:
   
   * **NÁZEV**: Zadejte popisný název akce skriptu.
   * **Identifikátor URI skriptu Bash:**`https://raw.githubusercontent.com/hdinsight/presto-hdinsight/master/installpresto.sh`
   * **HEAD**: zaškrtnete tuto možnost,
   * **PRACOVNÍ**: zaškrtnete tuto možnost,
   * **ZOOKEEPER**: zaškrtnutí tohoto políčka zrušte
   * **Parametry**: to pole ponechat prázdné


3. V dolní části **akcí skriptů** oblast, klikněte na tlačítko **vyberte** tlačítko Uložit konfiguraci. Nakonec klikněte na **vyberte** tlačítko v dolní části **Upřesnit nastavení** oblasti uložit informace o konfiguraci.

4. Pokračovat zřizování clusteru, jak je popsáno v [clustery HDInsight se systémem Linux](hdinsight-hadoop-create-linux-clusters-portal.md).

    > [!NOTE]
    > Azure PowerShell, rozhraní příkazového řádku Azure, sady .NET SDK HDInsight nebo šablon Azure Resource Manageru lze také použít skript akce. Můžete taky použít skript akce již clustery spuštěná. Další informace najdete v tématu [HDInsight přizpůsobit clustery pomocí akcí skriptů](hdinsight-hadoop-customize-cluster-linux.md).
    > 
    > 

## <a name="use-presto-with-hdinsight"></a>Použijte Presto s HDInsight

Chcete-li pracovat s Presto v clusteru služby HDInsight, použijte následující kroky:

1. Připojte se ke clusteru HDInsight pomocí protokolu SSH:
   
        ssh USERNAME@CLUSTERNAME-ssh.azurehdinsight.net
   
    Další informace najdete v tématu [Použití SSH se službou HDInsight](hdinsight-hadoop-linux-use-ssh-unix.md).
     

2. Spusťte Presto prostředí pomocí následujícího příkazu.
   
        presto --schema default

3. Spuštění dotazu v tabulce ukázka **hivesampletable**, která je k dispozici na všech clusterech HDInsight ve výchozím nastavení.
   
        select count (*) from hivesampletable;
   
    Ve výchozím nastavení [Hive](https://prestodb.io/docs/current/connector/hive.html) a [TPCH](https://prestodb.io/docs/current/connector/tpch.html) konektory pro Presto jsou již nakonfigurována. Hive konektor je nakonfigurovaný na použití výchozí nainstalován Hive instalace, takže všechny tabulky z podregistru budou automaticky viditelné v Presto.

    Další informace najdete v tématu [Presto dokumentaci](https://prestodb.io/docs/current/index.html).

## <a name="use-airpal-with-presto"></a>Pomocí Airpal Presto

[Airpal](https://github.com/airbnb/airpal#airpal) je dotazu open-source webové rozhraní pro Presto. Další informace o Airpal najdete v tématu [Airpal dokumentaci](https://github.com/airbnb/airpal#airpal).

K instalaci Airpal na uzlu edge použijte následující kroky:

1. Pomocí SSH se připojte k headnode clusteru HDInsight, která má nainstalovanou Presto:
   
        ssh USERNAME@CLUSTERNAME-ssh.azurehdinsight.net
   
    Další informace najdete v tématu [Použití SSH se službou HDInsight](hdinsight-hadoop-linux-use-ssh-unix.md).

2. Jakmile se připojíte, spusťte následující příkaz.

        sudo slider registry  --name presto1 --getexp presto 
   
    Zobrazí výstup podobný následujícím kódu JSON:

        {
            "coordinator_address" : [ {
                "value" : "10.0.0.12:9090",
                "level" : "application",
                "updatedTime" : "Mon Apr 03 20:13:41 UTC 2017"
        } ]

3. Z výstupu, poznamenejte si hodnotu pro **hodnotu** vlastnost. Tuto hodnotu budete potřebovat při instalaci Airpal na edgenode clusteru. Ve výstupu výš, je hodnota, kterou budete potřebovat **10.0.0.12:9090**.

4. Použít šablonu **[sem](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2Fhdinsight%2Fpresto-hdinsight%2Fmaster%2Fairpal-deploy.json)** vytvoření edgenode clusteru HDInsight a zadejte hodnoty, jak je znázorněno na následujícím snímku obrazovky.

    ![Instalace HDInsight Airpal na Presto clusteru](./media/hdinsight-hadoop-install-presto/hdinsight-install-airpal.png)

5. Klikněte na **Koupit**.

6. Jakmile jsou změny v konfiguraci clusteru, můžete přístup k webové rozhraní Airpal pomocí následujících kroků.

    1. V dialogovém okně clusteru, klikněte na **aplikace**.

        ![Spuštění HDInsight Airpal na Presto clusteru](./media/hdinsight-hadoop-install-presto/hdinsight-presto-launch-airpal.png)

    2. Z **nainstalované aplikace** oblast, klikněte na tlačítko **portál** proti airpal.

        ![Spuštění HDInsight Airpal na Presto clusteru](./media/hdinsight-hadoop-install-presto/hdinsight-presto-launch-airpal-1.png)

    3. Po zobrazení výzvy zadejte přihlašovací údaje správce, které jste zadali při vytváření clusteru HDInsight Hadoop.

## <a name="customize-a-presto-installation-on-hdinsight-cluster"></a>Přizpůsobení Presto instalace na clusteru HDInsight

Chcete-li přizpůsobit instalace, použijte následující kroky:

1. Pomocí SSH se připojte k headnode clusteru HDInsight, která má nainstalovanou Presto:
   
        ssh USERNAME@CLUSTERNAME-ssh.azurehdinsight.net
   
    Další informace najdete v tématu [Použití SSH se službou HDInsight](hdinsight-hadoop-linux-use-ssh-unix.md).

2. Provedené změny konfigurace v souboru `/var/lib/presto/presto-hdinsight-master/appConfig-default.json`. Další informace o konfiguraci Presto, najdete v části [Presto konfigurace pro clustery založené na YARN](https://prestodb.io/presto-yarn/installation-yarn-configuration-options.html).

3. Zastavení a ukončení aktuální spuštěnou instanci Presto.

        sudo slider stop presto1 --force
        sudo slider destroy presto1 --force

4. Spusťte novou instanci třídy Presto s úpravami.

       sudo slider create presto1 --template /var/lib/presto/presto-hdinsight-master/appConfig-default.json --resources /var/lib/presto/presto-hdinsight-master/resources-default.json

5. Počkejte, než pro nové instance do připravená a poznamenejte si adresu presto coordinator.


       sudo slider registry --name presto1 --getexp presto

## <a name="generate-benchmark-data-for-hdinsight-clusters-that-run-presto"></a>Generovat srovnávacího testu data pro clustery služby HDInsight se systémem Presto

TPC DS je oborový standard pro měření výkonu systémů mnoho rozhodnutí podpory, včetně systémy velkých objemů dat. Presto můžete použít pro generování dat a vyhodnotit, jak se porovnává s vlastními daty srovnávacího testu HDInsight. Další informace najdete v tématu [zde](https://github.com/hdinsight/tpcds-datagen-as-hive-query/blob/master/README.md).



## <a name="see-also"></a>Další informace najdete v tématech
* [Nainstalovat a používat Hue v clusterech HDInsight](hdinsight-hadoop-hue-linux.md). HUE je webové uživatelské rozhraní, které umožňuje snadné vytvoření, spuštění a uložte Pig a Hive úlohy.

* [Nainstalujte Giraph clustery HDInsight](hdinsight-hadoop-giraph-install-linux.md). Přizpůsobení clusteru použijte k instalaci Giraph clusterů systému HDInsight Hadoop. Giraph umožňuje provádět grafu zpracování pomocí Hadoop a lze použít s Azure HDInsight.

* [Nainstalujte Solr clustery HDInsight](hdinsight-hadoop-solr-install-linux.md). Přizpůsobení clusteru použijte k instalaci Solr clusterů systému HDInsight Hadoop. Solr umožňuje provádět operace výkonné hledání na uložená data.

[hdinsight-install-r]: hdinsight-hadoop-r-scripts-linux.md
[hdinsight-cluster-customize]: hdinsight-hadoop-customize-cluster-linux.md
