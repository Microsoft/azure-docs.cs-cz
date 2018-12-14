---
title: Instalace a použití Giraphu na HDInsight (Hadoop) – Azure
description: Zjistěte, jak nainstalovat Giraph na clusterech HDInsight založených na Linuxu pomocí skriptových akcí. Akce se skripty umožňují přizpůsobit clusteru během vytváření, změny konfigurace clusteru nebo instalace služby a nástroje.
services: hdinsight
author: hrasheed-msft
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 04/23/2018
ms.author: hrasheed
ms.openlocfilehash: 6cedc269e279a9154e63bae241f02e766e06ec96
ms.sourcegitcommit: 85d94b423518ee7ec7f071f4f256f84c64039a9d
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/14/2018
ms.locfileid: "53383897"
---
# <a name="install-apache-giraph-on-hdinsight-hadoop-clusters-and-use-giraph-to-process-large-scale-graphs"></a>Nainstalovat Apache Giraph v clusterech HDInsight Hadoop a použití Giraphu ke zpracování rozsáhlých grafů

Zjistěte, jak clusteru služby HDInsight nainstalovat Apache Giraph. Funkci akce skriptu HDInsight umožňuje přizpůsobit cluster spuštěním skriptu bash. Skripty slouží k přizpůsobení clusterů, během a po vytvoření clusteru.

> [!IMPORTANT]
> Kroky v tomto dokumentu vyžadují cluster HDInsight s Linuxem. HDInsight od verze 3.4 výše používá výhradně operační systém Linux. Další informace najdete v tématu [Vyřazení prostředí HDInsight ve Windows](hdinsight-component-versioning.md#hdinsight-windows-retirement).

## <a name="whatis"></a>Co je Giraph

[Apache Giraph](http://giraph.apache.org/) je možné provádět zpracování s použitím Hadoopu grafů a jde použít s Azure HDInsight. Grafy modelovat vztahy mezi objekty. Například připojení mezi směrovači ve velké síti, jako je Internet nebo vztahy mezi uživateli v sociálních sítích. Zpracování grafů umožňuje argumentovat o vztahy mezi objekty v grafu, jako například:

* Určení potenciální přátel podle aktuální relace.

* Identifikace nejkratší mezi dvěma počítači v síti.

* Výpočet hodnocení stránky webových stránek.

> [!WARNING]  
> Součásti, které jsou součástí clusteru HDInsight jsou plně podporované – Microsoft Support pomáhá izolovat a vyřešit problémy týkající se těchto součástí.
>
> Vlastní komponenty, jako je například Giraph, přijímat obchodně přiměřenou podporu můžete-li dále řešit tento problém. Microsoft Support pravděpodobně možné do vyřešení problému. V opačném případě musí konzultovat opensourcové komunity, ve kterých se nachází rozsáhlé znalosti pro tuto technologii. Existuje například mnoho komunitním webům, které lze použít jako: [Fórum na webu MSDN pro HDInsight](https://social.msdn.microsoft.com/Forums/azure/en-US/home?forum=hdinsight), [ http://stackoverflow.com ](http://stackoverflow.com). Také projektů Apache mít projektovým webům na [ http://apache.org ](http://apache.org), například: [Hadoop](http://hadoop.apache.org/).


## <a name="what-the-script-does"></a>Co dělá skriptu

Tento skript provede následující akce:

* Giraph k instalaci `/usr/hdp/current/giraph`

* Kopie `giraph-examples.jar` souboru do výchozí storage (WASB) pro váš cluster: `/example/jars/giraph-examples.jar`

## <a name="install"></a>Nainstalovat Giraph pomocí akcí skriptů

Ukázkový skript do clusteru služby HDInsight nainstalovat Giraph je k dispozici v následujícím umístění:

    https://hdiconfigactions.blob.core.windows.net/linuxgiraphconfigactionv01/giraph-installer-v01.sh

Tato část obsahuje informace o tom, jak pomocí vzorového skriptu při vytváření clusteru pomocí webu Azure portal.

> [!NOTE]
> Akce skriptu můžete použít některou z následujících metod:
> * Azure PowerShell
> * Azure Classic CLI
> * Sady HDInsight .NET SDK
> * Šablony Azure Resource Manageru
> 
> Akce se skripty můžete také použít k už běží clustery. Další informace najdete v tématu [HDInsight přizpůsobit clustery pomocí skriptových akcí](hdinsight-hadoop-customize-cluster-linux.md).

1. Začněte s vytvářením clusteru pomocí kroků v [clusterech HDInsight založených na Linuxu vytvořit](hdinsight-hadoop-create-linux-clusters-portal.md), ale dokončí vytváření.

2. V **volitelná konfigurace** vyberte **akcí skriptů**a zadejte následující informace:

   * **NÁZEV**: Zadejte popisný název akce skriptu.

   * **IDENTIFIKÁTOR URI SKRIPTU**: https://hdiconfigactions.blob.core.windows.net/linuxgiraphconfigactionv01/giraph-installer-v01.sh

   * **HLAVNÍ**: Zkontrolujte tuto položku.

   * **PRACOVNÍK**: Tato položka nechte nezaškrtnuté.

   * **ZOOKEEPER**: Tato položka nechte nezaškrtnuté.

   * **PARAMETRY**: Toto pole nechte prázdné.

3. V dolní části **akcí skriptů**, použijte **vyberte** tlačítko, čímž konfiguraci uložíte. Nakonec použijte **vyberte** tlačítko v dolní části **volitelná konfigurace** části a uložte informace o konfiguraci volitelné.

4. Pokračovat ve vytváření clusteru, jak je popsáno v [clusterech HDInsight založených na Linuxu vytvořit](hdinsight-hadoop-create-linux-clusters-portal.md).

## <a name="usegiraph"></a>Použití Giraph v HDInsight

Po vytvoření clusteru použijte následující kroky ke spuštění příkladu SimpleShortestPathsComputation součástí Giraph. Tento příklad používá na úrovni basic [Pregel](http://people.apache.org/~edwardyoon/documents/pregel.pdf) implementace pro nalezení nejkratší cesty mezi objekty v grafu.

1. Připojte se ke clusteru HDInsight pomocí protokolu SSH:

    ```bash
    ssh USERNAME@CLUSTERNAME-ssh.azurehdinsight.net
    ```

    Další informace najdete v tématu [Použití SSH se službou HDInsight](hdinsight-hadoop-linux-use-ssh-unix.md).

2. Pomocí následujícího příkazu vytvořte soubor s názvem **tiny_graph.txt**:

    ```bash
    nano tiny_graph.txt
    ```

    Jako obsah souboru použijte následující text:

    ```text
    [0,0,[[1,1],[3,3]]]
    [1,0,[[0,1],[2,2],[3,1]]]
    [2,0,[[1,2],[4,4]]]
    [3,0,[[0,3],[1,1],[4,4]]]
    [4,0,[[3,4],[2,4]]]
    ```

    Tato data popisuje vztah mezi objekty v řízeném grafu ve formátu `[source_id, source_value,[[dest_id], [edge_value],...]]`. Každý řádek představuje vztah mezi `source_id` objekt a jeden nebo více `dest_id` objekty. `edge_value` Si lze představit jako sílu nebo vzdálenost připojení mezi `source_id` a `dest\_id`.

    Vleklým, a pomocí hodnoty (nebo váha) jako vzdálenosti mezi objekty, data může vypadat jako na následujícím diagramu:

    ![tiny_graph.txt vykreslen jako kroužky s různou vzdálenost mezi řádky](./media/hdinsight-hadoop-giraph-install-linux/giraph-graph.png)

3. Chcete-li uložit soubor, použijte **Ctrl + X**, pak **Y**a nakonec **Enter** tak, aby přijímal název souboru.

4. Použijte následující postupy k uložení dat do primární úložiště pro váš cluster HDInsight:

    ```bash
    hdfs dfs -put tiny_graph.txt /example/data/tiny_graph.txt
    ```

5. Spusťte příklad SimpleShortestPathsComputation pomocí následujícího příkazu:

    ```bash
    yarn jar /usr/hdp/current/giraph/giraph-examples.jar org.apache.giraph.GiraphRunner org.apache.giraph.examples.SimpleShortestPathsComputation -ca mapred.job.tracker=headnodehost:9010 -vif org.apache.giraph.io.formats.JsonLongDoubleFloatDoubleVertexInputFormat -vip /example/data/tiny_graph.txt -vof org.apache.giraph.io.formats.IdWithValueTextOutputFormat -op /example/output/shortestpaths -w 2
    ```

    Parametry tohoto příkazu jsou popsány v následující tabulce:

   | Parametr | Co dělá |
   | --- | --- |
   | `jar` |Soubor jar obsahující příklady. |
   | `org.apache.giraph.GiraphRunner` |Třída sloužící ke spuštění příkladů. |
   | `org.apache.giraph.examples.SimpleShortestPathsCoputation` |V příkladu, který se používá. V tomto příkladu vrací nejkratší cesty mezi ID 1 a všechny ostatní identifikátory v grafu. |
   | `-ca mapred.job.tracker` |Hlavní uzel clusteru. |
   | `-vif` |Vstupní formát pro vstupní data. |
   | `-vip` |Vstupní datový soubor. |
   | `-vof` |Výstupní formát. V tomto příkladu, ID a hodnotu jako prostý text. |
   | `-op` |Umístění výstupu. |
   | `-w 2` |Počet pracovních procesů, které chcete použít. V tomto příkladu 2. |

    Další informace o těchto a dalších parametrů použitých s ukázkami Giraph, najdete v článku [Giraph quickstart](http://giraph.apache.org/quick_start.html).

6. Po dokončení úlohy se mají výsledky ukládat do **/example/out/shotestpaths** adresáře. Názvů výstupních souborů začínat **část-m -** a končit číslo označující první, druhý, soubor atd. Následujícím příkazem zobrazíte výstup:

    ```bash
    hdfs dfs -text /example/output/shortestpaths/*
    ```

    Zobrazí výstup podobný následujícímu textu:

        0    1.0
        4    5.0
        2    2.0
        1    0.0
        3    1.0

    SimpleShortestPathComputation příklad je obtížné programového začít s objektu ID 1 a nalezení nejkratší cesty na jiné objekty. Výstup je ve formátu `destination_id` a `distance`. `distance` Je hodnota (nebo váha) okraje mezi objektem ID 1 a ID cílové cesty

    Vizualizace dat, můžete ověřit výsledky tak cestování nejkratší cesty mezi ID 1 a všechny ostatní objekty. Nejkratší cesty mezi ID 1 a ID 4 je 5. Tato hodnota je celkový počet vzdálenost mezi <span style="color:orange">ID 1 a 3</span>a potom <span style="color:red">ID 3 a 4</span>.

    ![Vykreslení objektů jako kroužky s nejkratší cesty mezi](./media/hdinsight-hadoop-giraph-install-linux/giraph-graph-out.png)

## <a name="next-steps"></a>Další postup

* [Nainstalovat a používat rozhraní Hue v clusterech HDInsight](hdinsight-hadoop-hue-linux.md).

* [Instalace Apache Solr na clusterech HDInsight](hdinsight-hadoop-solr-install-linux.md).
