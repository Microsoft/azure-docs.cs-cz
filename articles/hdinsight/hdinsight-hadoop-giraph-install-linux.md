---
title: Nainstalovat a používat Giraph v HDInsight (Hadoop) - Azure | Microsoft Docs
description: Informace o instalaci Giraph v clusterech HDInsight se systémem Linux pomocí akcí skriptů. Akce skriptu umožňují přizpůsobit clusteru během vytváření, změnou konfigurace clusteru nebo instalace služby a nástroje.
services: hdinsight
documentationcenter: ''
author: Blackmist
manager: jhubbard
editor: cgronlun
tags: azure-portal
ms.assetid: 9fcac906-8f06-4002-9fe8-473e42f8fd0f
ms.service: hdinsight
ms.custom: hdinsightactive
ms.devlang: na
ms.topic: conceptual
ms.date: 01/22/2018
ms.author: larryfr
ms.openlocfilehash: dcfd37a40ce16a1574c21e3a6e9520cb2e773166
ms.sourcegitcommit: 9cdd83256b82e664bd36991d78f87ea1e56827cd
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/16/2018
---
# <a name="install-giraph-on-hdinsight-hadoop-clusters-and-use-giraph-to-process-large-scale-graphs"></a>Nainstalujte Giraph clusterů systému HDInsight Hadoop a používat Giraph ke zpracování velkých grafy

Informace o instalaci Apache Giraph v clusteru HDInsight. Funkci akce skriptu HDInsight umožňuje přizpůsobit clusteru spuštěním skriptu prostředí bash. Skripty slouží k přizpůsobení clusterů během a po vytvoření clusteru.

> [!IMPORTANT]
> Kroky v tomto dokumentu vyžadují clusteru služby HDInsight, který používá Linux. HDInsight od verze 3.4 výše používá výhradně operační systém Linux. Další informace najdete v tématu [Vyřazení prostředí HDInsight ve Windows](hdinsight-component-versioning.md#hdinsight-windows-retirement).

## <a name="whatis"></a>Co je Giraph

[Apache Giraph](http://giraph.apache.org/) umožňuje provádět grafu zpracování pomocí Hadoop a lze použít s Azure HDInsight. Grafy model relace mezi objekty. Připojení mezi směrovače na velké sítě, jako například Internet nebo vztahy mezi uživateli v sociálních sítích. Zpracování graf umožňuje důvod o vztahy mezi objekty v grafu, jako například:

* Identifikace potenciální přátel na základě vaší aktuální relací.

* Identifikace nejkratší trasy mezi dvěma počítači v síti.

* Výpočet pořadí stránky webových stránek.

> [!WARNING]
> Součásti, které jsou součástí clusteru HDInsight plně podporované – Microsoft Support pomáhá izolovat a vyřešení problémů týkajících se těchto součástí.
>
> Vlastní komponenty, například Giraph, přijímat vyvineme podporu o pomoci při další řešení problému. Microsoft Support pravděpodobně možné do vyřešení problému. Pokud ne, musíte obrátit komunit s otevřeným zdrojem, kterých se nachází hluboké znalosti pro tuto technologii. Například existuje mnoho komunity webů, které lze použít jako: [fórum MSDN pro HDInsight](https://social.msdn.microsoft.com/Forums/azure/en-US/home?forum=hdinsight), [ http://stackoverflow.com ](http://stackoverflow.com). Také Apache projekty mají na projektu serverů [ http://apache.org ](http://apache.org), například: [Hadoop](http://hadoop.apache.org/).


## <a name="what-the-script-does"></a>Funkci skriptu

Tento skript provede následující akce:

* Giraph nainstaluje do `/usr/hdp/current/giraph`

* Kopie `giraph-examples.jar` souboru do výchozího úložiště (WASB) pro váš cluster: `/example/jars/giraph-examples.jar`

## <a name="install"></a>Nainstalujte Giraph pomocí akcí skriptů

Ukázkový skript pro instalaci Giraph v clusteru HDInsight je k dispozici v následujícím umístění:

    https://hdiconfigactions.blob.core.windows.net/linuxgiraphconfigactionv01/giraph-installer-v01.sh

Tato část obsahuje pokyny o tom, jak pomocí vzorového skriptu při vytváření clusteru pomocí portálu Azure.

> [!NOTE]
> Akce skriptu můžete použít pomocí kteréhokoli z následujících metod:
> * Azure PowerShell
> * Azure CLI
> * .NET SDK služby HDInsight
> * Šablony Azure Resource Manageru
> 
> Můžete taky použít skript akce již clustery spuštěná. Další informace najdete v tématu [HDInsight přizpůsobit clustery pomocí akcí skriptů](hdinsight-hadoop-customize-cluster-linux.md).

1. Zahájení vytváření clusteru pomocí kroků v [clustery HDInsight se systémem Linux vytvořit](hdinsight-hadoop-create-linux-clusters-portal.md), ale nedokončila vytvoření.

2. V **volitelné konfiguraci** vyberte **akcí skriptů**a zadejte následující informace:

   * **NÁZEV**: Zadejte popisný název akce skriptu.

   * **IDENTIFIKÁTOR URI SKRIPTU**: https://hdiconfigactions.blob.core.windows.net/linuxgiraphconfigactionv01/giraph-installer-v01.sh

   * **HEAD**: Zkontrolujte tuto položku

   * **PRACOVNÍ**: nechte zaškrtnuté políčko tuto položku

   * **ZOOKEEPER**: nechte zaškrtnuté políčko tuto položku

   * **Parametry**: to pole ponechat prázdné

3. V dolní části **akcí skriptů**, použijte **vyberte** tlačítko Uložit konfiguraci. Nakonec použijte **vyberte** tlačítko v dolní části **volitelné konfiguraci** části uložit informace o volitelné konfiguraci.

4. Pokračovat ve vytváření clusteru, jak je popsáno v [clustery HDInsight se systémem Linux vytvořit](hdinsight-hadoop-create-linux-clusters-portal.md).

## <a name="usegiraph"></a>Jak používat Giraph v prostředí HDInsight?

Po vytvoření clusteru, použijte následující kroky pro spuštění příkladu SimpleShortestPathsComputation součástí Giraph. Tento příklad používá základní [Pregel](http://people.apache.org/~edwardyoon/documents/pregel.pdf) implementace pro vyhledání nejkratšího cesta mezi objekty v grafu.

1. Připojte se ke clusteru HDInsight pomocí protokolu SSH:

    ```bash
    ssh USERNAME@CLUSTERNAME-ssh.azurehdinsight.net
    ```

    Další informace najdete v tématu [Použití SSH se službou HDInsight](hdinsight-hadoop-linux-use-ssh-unix.md).

2. Pomocí následujícího příkazu vytvořte soubor s názvem **tiny_graph.txt**:

    ```bash
    nano tiny_graph.txt
    ```

    Jako obsah tohoto souboru použijte následující text:

    ```text
    [0,0,[[1,1],[3,3]]]
    [1,0,[[0,1],[2,2],[3,1]]]
    [2,0,[[1,2],[4,4]]]
    [3,0,[[0,3],[1,1],[4,4]]]
    [4,0,[[3,4],[2,4]]]
    ```

    Tato data popisuje vztah mezi objekty v řízené grafu, ve formátu `[source_id, source_value,[[dest_id], [edge_value],...]]`. Každý řádek představuje vztah mezi `source_id` objektů a jeden nebo více `dest_id` objekty. `edge_value` Si lze představit jako sílu nebo distance připojení mezi `source_id` a `dest\_id`.

    Vykreslovat limitu, a pomocí hodnoty (nebo váhy) jako vzdálenost mezi objekty, data může vypadat jako na následujícím diagramu:

    ![tiny_graph.txt vykreslovat jako kroužky řádků různých vzdálenost mezi](./media/hdinsight-hadoop-giraph-install-linux/giraph-graph.png)

3. Chcete-li uložit soubor, použijte **Ctrl + X**, pak **Y**a v neposlední řadě **Enter** tak, aby přijímal název souboru.

4. Použijte následující postupy k ukládání dat do primárního úložiště pro váš cluster HDInsight:

    ```bash
    hdfs dfs -put tiny_graph.txt /example/data/tiny_graph.txt
    ```

5. Spusťte v příkladu SimpleShortestPathsComputation pomocí následujícího příkazu:

    ```bash
    yarn jar /usr/hdp/current/giraph/giraph-examples.jar org.apache.giraph.GiraphRunner org.apache.giraph.examples.SimpleShortestPathsComputation -ca mapred.job.tracker=headnodehost:9010 -vif org.apache.giraph.io.formats.JsonLongDoubleFloatDoubleVertexInputFormat -vip /example/data/tiny_graph.txt -vof org.apache.giraph.io.formats.IdWithValueTextOutputFormat -op /example/output/shortestpaths -w 2
    ```

    V následující tabulce jsou popsány parametry tohoto příkazu:

   | Parametr | Co dělá |
   | --- | --- |
   | `jar` |Soubor jar obsahující příklady. |
   | `org.apache.giraph.GiraphRunner` |Třída sloužící ke spuštění v příkladech. |
   | `org.apache.giraph.examples.SimpleShortestPathsCoputation` |Příklad, který se používá. V tomto příkladu vypočítá nejkratší cestě mezi ID 1 a všechna ID v grafu. |
   | `-ca mapred.job.tracker` |Headnode pro cluster. |
   | `-vif` |Vstupní formát pro vstupní data. |
   | `-vip` |Vstupní datový soubor. |
   | `-vof` |Formát výstupu. V tomto příkladu, ID a hodnotu jako prostý text. |
   | `-op` |Umístění výstupu. |
   | `-w 2` |Počet pracovních procesů používat. V tomto příkladu 2. |

    Další informace o těchto a dalších parametrů použít s Giraph ukázky najdete v tématu [rychlý start Giraph](http://giraph.apache.org/quick_start.html).

6. Po dokončení úlohy se mají výsledky ukládat v **/example/out/shotestpaths** adresáře. Názvy výstupního souboru začínat **část-m -** a konec s číslo určující první, druhý, soubor atd. Chcete-li zobrazit výstup použijte následující příkaz:

    ```bash
    hdfs dfs -text /example/output/shortestpaths/*
    ```

    Výstup by měl vypadat přibližně následující text:

        0    1.0
        4    5.0
        2    2.0
        1    0.0
        3    1.0

    SimpleShortestPathComputation příklad je pevný programového začínat objektu ID 1 a najít nejkratší cestu k jiné objekty. Výstupem je ve formátu `destination_id` a `distance`. `distance` Je hodnota (nebo váhy) okraje mezi objektem ID 1 a identifikátor cílové cesty

    Vizualizace tato data, můžete ověřit výsledky podle cestě nejkratší cest mezi ID 1 a všechny ostatní objekty. Nejkratší cesta ID 1 až ID 4 je 5. Tato hodnota je celkový počet vzdálenost mezi <span style="color:orange">ID 1 a 3</span>a potom <span style="color:red">ID 3 a 4</span>.

    ![Kreslení objektů jako kroužky s nejkratší cest mezi](./media/hdinsight-hadoop-giraph-install-linux/giraph-graph-out.png)

## <a name="next-steps"></a>Další postup

* [Nainstalovat a používat Hue v clusterech HDInsight](hdinsight-hadoop-hue-linux.md).

* [Nainstalujte Solr clustery HDInsight](hdinsight-hadoop-solr-install-linux.md).
