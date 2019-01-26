---
title: Nainstalovat Presto v clusterech Azure HDInsight s Linuxem
description: Zjistěte, jak nainstalovat Presto a Airpal clusterů Hadoop na systému Linux HDInsight pomocí skriptových akcí.
services: hdinsight
author: hrasheed-msft
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 01/01/2019
ms.author: hrasheed
ms.openlocfilehash: 60ff63a049f225886d69c1a89a2930671e533d78
ms.sourcegitcommit: 97d0dfb25ac23d07179b804719a454f25d1f0d46
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/25/2019
ms.locfileid: "54910909"
---
# <a name="install-and-use-presto-on-hadoop-based-hdinsight-clusters"></a>Nainstalovat a používat Presto na clusterech HDInsight se systémem Hadoop

Tento článek vysvětluje, jak nainstalovat Presto na systému Hadoop HDInsight Adobe clustery pomocí skriptových akcí. Také se dozvíte, jak nainstalovat Airpal do existujícího clusteru HDInsight Presto.

HDInsight také nabízí aplikace hvězdice Presto pro clustery systému Apache Hadoop. Další informace najdete v tématu [instalace aplikací třetích stran Apache Hadoop v Azure HDInsight](https://docs.microsoft.com/azure/hdinsight/hdinsight-apps-install-applications).

> [!IMPORTANT]  
> Kroky v tomto článku vyžadují clusteru služby HDInsight 3.5 Hadoop, který využívá systém Linux. Linux je pouze operační systém používaný v HDInsight verze 3.4 a vyšší. Další informace najdete v tématu [HDInsight verze](hdinsight-component-versioning.md).

## <a name="what-is-presto"></a>Co je Presto?
[Presto](https://prestodb.io/overview.html) distribuované fast modul dotazů SQL pro velké objemy dat je. Presto je vhodný pro interaktivní dotazování petabajty dat. Podrobnosti o součásti Presto a jak pracují společně, naleznete v tématu [Presto koncepty](https://github.com/prestodb/presto/blob/master/presto-docs/src/main/sphinx/overview/concepts.rst).

> [!WARNING]  
> Součásti, které jsou součástí clusteru HDInsight jsou plně podporované. Microsoft Support pomáhá izolovat a vyřešit problémy týkající se těchto součástí.
> 
> Vlastní komponenty, jako jsou Presto přijímat obchodně přiměřenou podporu můžete dále řešit tento problém. Tato podpora může problém vyřešit. Nebo můžete být vyzváni k zapojení dostupné kanály pro open source technologie, ve kterých se nachází rozsáhlé znalosti pro tuto technologii. Existuje mnoho komunitním webům, které lze použít. Mezi příklady patří [fórum na webu MSDN pro HDInsight](https://social.msdn.microsoft.com/Forums/azure/home?forum=hdinsight) a [Stack Overflow](https://stackoverflow.com). 
>
> Projektů Apache také mít projektovým webům na [webu Apache](https://apache.org). Příkladem je [Hadoop](https://hadoop.apache.org/).


## <a name="install-presto-by-using-script-actions"></a>Nainstalovat Presto pomocí skriptových akcí

Tato část vysvětluje, jak pomocí vzorového skriptu při vytvoření nového clusteru pomocí webu Azure portal: 

1. Spustit ke zřízení clusteru provedením kroků [vytvoření linuxových clusterech v HDInsight pomocí webu Azure portal](hdinsight-hadoop-create-linux-clusters-portal.md). Ujistěte se, že vytváření clusteru s použitím **vlastní** clusteru vytváření toku. Cluster musí splňovat následující požadavky:

    * Musí být clusteru Hadoop s HDInsight verze 3.6.

    * Jako úložiště dat musí používat Azure Storage. Pomocí Presto v clusteru, který používá Azure Data Lake Storage jako možnost úložiště není dosud.

    ![HDInsight, vlastní (velikost, nastavení, aplikace)](./media/hdinsight-hadoop-install-presto/hdinsight-install-custom.png)

2. V **upřesňující nastavení** vyberte **akcí skriptů**. Zadejte následující informace. Můžete také **nainstalovat Presto** možnost pro typ skriptu:
   
   * **NÁZEV**. Zadejte popisný název akce skriptu.
   * **URI skriptu bash**. `https://raw.githubusercontent.com/hdinsight/presto-hdinsight/master/installpresto.sh`.
   * **HLAVNÍ**. Tuto možnost.
   * **PRACOVNÍK**. Tuto možnost.
   * **ZOOKEEPER**. Toto zaškrtávací políčko nechte prázdné.
   * **PARAMETRY**. Toto pole nechte prázdné.


3. V dolní části **akcí skriptů** oblasti, zvolte **vyberte** tlačítko, čímž konfiguraci uložíte. A konečně, zvolte **vyberte** tlačítko v dolní části **Upřesnit nastavení** oblast, kterou chcete uložit informace o konfiguraci.

4. Zřízení clusteru, jak je popsáno v i nadále [vytvoření linuxových clusterech v HDInsight pomocí webu Azure portal](hdinsight-hadoop-create-linux-clusters-portal.md).

    > [!NOTE]  
    > Prostředí Azure PowerShell, rozhraní příkazového řádku Azure Classic, sady HDInsight .NET SDK nebo šablony Azure Resource Manageru je také možné použití akce skriptu. Akce se skripty můžete také použít k už běží clustery. Další informace najdete v tématu [HDInsight založených na Linuxu přizpůsobit clustery pomocí skriptových akcí](hdinsight-hadoop-customize-cluster-linux.md).
    > 
    > 

## <a name="use-presto-with-hdinsight"></a>Použití Presta s HDInsight

Pro práci s Presto v clusteru služby HDInsight, postupujte následovně:

1. Připojte se ke clusteru HDInsight pomocí SSH:
   
    `ssh USERNAME@CLUSTERNAME-ssh.azurehdinsight.net`
   
    Další informace najdete v tématu [připojení k HDInsight (Apache Hadoop) pomocí protokolu SSH](hdinsight-hadoop-linux-use-ssh-unix.md).
     

2. Spusťte Presto prostředí spuštěním následujícího příkazu:
   
    `presto --schema default`

3. Spustit dotaz na ukázkovou tabulku **hivesampletable**, která je k dispozici na všech clusterech HDInsight ve výchozím nastavení:
   
    `select count (*) from hivesampletable;`
   
    Ve výchozím nastavení [Apache Hive](https://prestodb.io/docs/current/connector/hive.html) a [TPCH](https://prestodb.io/docs/current/connector/tpch.html) konektory pro Presto jsou už nakonfigurovaná. Konektor Hive je nakonfigurován pro použití výchozí instalaci Hive. Proto všechny tabulky z Hive se automaticky zobrazí v Presto.

    Další informace najdete v tématu [Presto dokumentaci](https://prestodb.io/docs/current/index.html).

## <a name="use-airpal-with-presto"></a>Pomocí Airpal Presta

[Airpal](https://github.com/airbnb/airpal#airpal) je open source dotazů založených na webové rozhraní pro Presto. Další informace o Airpal najdete v tématu [Airpal dokumentaci](https://github.com/airbnb/airpal#airpal).

Proveďte následující kroky k instalaci Airpal na hraničním uzlu:

1. Pomocí SSH připojte k hlavnímu uzlu clusteru HDInsight, která má nainstalovanou Presto:
   
    `ssh USERNAME@CLUSTERNAME-ssh.azurehdinsight.net`
   
    Další informace najdete v tématu [připojení k HDInsight (Apache Hadoop) pomocí protokolu SSH](hdinsight-hadoop-linux-use-ssh-unix.md).

2. Jakmile budete hotovi, spusťte následující příkaz:

    `sudo slider registry  --name presto1 --getexp presto` 
   
    Zobrazí výstup podobný následující JSON:

        {
            "coordinator_address" : [ {
                "value" : "10.0.0.12:9090",
                "level" : "application",
                "updatedTime" : "Mon Apr 03 20:13:41 UTC 2017"
        } ]

3. Z výstupu, poznamenejte si hodnotu pro **hodnotu** vlastnost. Tuto hodnotu budete potřebovat při instalaci Airpal na hraničním uzlu clusteru. Ve výstupu předchozí hodnotu, která je nutné je **10.0.0.12:9090**.

4. Použití [Tato šablona](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2Fhdinsight%2Fpresto-hdinsight%2Fmaster%2Fairpal-deploy.json) vytvořit hraničnímu uzlu clusteru HDInsight. Zadejte hodnoty zobrazené na následujícím snímku obrazovky.

    ![Vlastní nasazení](./media/hdinsight-hadoop-install-presto/hdinsight-install-airpal.png)

5. Vyberte **Koupit**.

6. Po aplikování změn v konfiguraci clusteru, přístup k Airpal webové rozhraní pomocí následujících kroků [webu Azure portal](https://portal.azure.com):

    1. V nabídce vlevo vyberte **všechny služby**.

    1. V části **ANALYTICS**vyberte **clustery HDInsight**.

    1. Vyberte ze seznamu, které se otevře zobrazení výchozí svůj cluster.

    1. Výchozí zobrazení v části **nastavení**vyberte **aplikací**.

        ![HDInsight aplikace](./media/hdinsight-hadoop-install-presto/hdinsight-presto-launch-airpal.png)

    1. Z **nainstalované aplikace** stránky, vyhledejte záznam tabulky pro **airpal**. Vyberte **portál**.

        ![Nainstalované aplikace](./media/hdinsight-hadoop-install-presto/hdinsight-presto-launch-airpal-1.png)

    1. Po zobrazení výzvy zadejte přihlašovací údaje správce, které jste zadali při vytváření clusteru HDInsight se systémem Hadoop.

## <a name="customize-a-presto-installation-on-hdinsight-cluster"></a>Přizpůsobení Presto instalace v clusteru HDInsight

K přizpůsobení instalace, proveďte následující kroky:

1. Pomocí SSH připojte k hlavnímu uzlu clusteru HDInsight, která má nainstalovanou Presto:
   
    `ssh USERNAME@CLUSTERNAME-ssh.azurehdinsight.net`
   
    Další informace najdete v tématu [připojení k HDInsight (Apache Hadoop) pomocí protokolu SSH](hdinsight-hadoop-linux-use-ssh-unix.md).

2. Proveďte požadované změny konfigurace v souboru `/var/lib/presto/presto-hdinsight-master/appConfig-default.json`. Další informace o konfiguraci Presto, naleznete v tématu [možnosti Presto konfigurace pro clustery založené na YARN](https://prestodb.io/presto-yarn/installation-yarn-configuration-options.html).

3. Zastavit a ukončit aktuální spuštěné instance Presto:

    `sudo slider stop presto1 --force` `sudo slider destroy presto1 --force`

4. Spusťte novou instanci třídy Presto pomocí vlastního nastavení:

    `sudo slider create presto1 --template /var/lib/presto/presto-hdinsight-master/appConfig-default.json --resources /var/lib/presto/presto-hdinsight-master/resources-default.json`

5. Vyčkat, než novou instanci, až bude připravená. Poznamenejte si adresu Presto koordinátor:


    `sudo slider registry --name presto1 --getexp presto`

## <a name="generate-benchmark-data-for-hdinsight-clusters-that-run-presto"></a>Generovat data srovnávacích testů pro clustery HDInsight se spuštěnou technologií Presto

Oborový standard pro měření výkonu mnoho systémů, podpora rozhodování, včetně systémy velkých objemů dat je TPC-DS. Presto můžete použít pro generování dat a vyhodnotit jejím srovnání s vlastními daty srovnávací test HDInsight. Další informace najdete v tématu [tpcds hdinsight](https://github.com/hdinsight/tpcds-datagen-as-hive-query/blob/master/README.md).



## <a name="next-steps"></a>Další postup
* [Nainstalovat a používat rozhraní Hue v clusterech HDInsight Hadoop](hdinsight-hadoop-hue-linux.md). Odstín, který je webové uživatelské rozhraní, které usnadňuje vytvoření, spuštění a uložit Apache Pig a úloh Hive.

* [Nainstalovat Apache Giraph v clusterech HDInsight Hadoop a použití Giraphu ke zpracování rozsáhlých grafů](hdinsight-hadoop-giraph-install-linux.md). Clustery založené na systému Hadoop HDInsight nainstalovat Giraph pomocí přizpůsobení clusteru. Giraph umožňuje zpracování grafů s použitím Hadoopu. Taky jde použít s Azure HDInsight.

* [Nainstalovat a používat Apache Solr na clusterech HDInsight Hadoop](hdinsight-hadoop-solr-install-linux.md). Clustery založené na systému Hadoop HDInsight nainstalovat Solr pomocí přizpůsobení clusteru. S použitím Solr, můžete provádět operace výkonné vyhledávání na uložená data.

[hdinsight-install-r]: hdinsight-hadoop-r-scripts-linux.md
[hdinsight-cluster-customize]: hdinsight-hadoop-customize-cluster-linux.md
