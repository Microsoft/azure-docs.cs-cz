---
title: Instalace a použití Giraph ve službě Azure HDInsight
description: Naučte se instalovat Giraph na clustery HDInsight pomocí akcí skriptů. Pomocí Giraph můžete provádět zpracování grafů v Apache Hadoop v cloudu Azure.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.date: 04/22/2019
ms.openlocfilehash: f1ca536ffa2166df4ef6cf51654b7b410e72ea66
ms.sourcegitcommit: dd69b3cda2d722b7aecce5b9bd3eb9b7fbf9dc0a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/12/2019
ms.locfileid: "70962076"
---
# <a name="install-apache-giraph-on-hdinsight-hadoop-clusters-and-use-giraph-to-process-large-scale-graphs"></a>Nainstalujte Apache Giraph do clusterů HDInsight Hadoop a používejte Giraph ke zpracování rozsáhlých grafů.

Přečtěte si, jak nainstalovat Apache Giraph v clusteru HDInsight. Funkce skriptu HDInsight umožňuje přizpůsobit cluster spuštěním skriptu bash. Skripty lze použít k přizpůsobení clusterů během a po vytvoření clusteru.

## <a name="whatis"></a>Co je Giraph

[Apache Giraph](https://giraph.apache.org/) vám umožňuje provádět zpracování grafů pomocí Hadoop a dá se použít s Azure HDInsight. Grafy modelů vztahů mezi objekty. Například připojení mezi směrovači ve velké síti, jako je například Internet, nebo vztahy mezi lidmi v sociálních sítích. Zpracování grafů vám umožňuje důvodné vztahy mezi objekty v grafu, například:

* Identifikace potenciálních přátel na základě aktuálních relací.

* Identifikace nejkratší trasy mezi dvěma počítači v síti.

* Výpočet pořadí stránek webových stránek.

> [!WARNING]  
> Součásti dodávané s clusterem HDInsight jsou plně podporované – podpora Microsoftu pomáhají izolovat a řešit problémy související s těmito součástmi.
>
> Vlastní komponenty, jako je Giraph, přijímají komerčně přiměřenou podporu, která vám může pomoct s dalším řešením tohoto problému. Podpora Microsoftu možná bude možné problém vyřešit. Pokud ne, musíte se obrátit na open source komunitu, kde najdete hloubkové znalosti pro tuto technologii. Například existuje mnoho webů komunity, které lze použít, například: [Fórum MSDN pro HDInsight](https://social.msdn.microsoft.com/Forums/azure/en-US/home?forum=hdinsight), [https://stackoverflow.com](https://stackoverflow.com). Projekty Apache také obsahují projektové weby [https://apache.org](https://apache.org), například: [Hadoop](https://hadoop.apache.org/).


## <a name="what-the-script-does"></a>Co skript dělá

Tento skript provede následující akce:

* Nainstaluje Giraph do`/usr/hdp/current/giraph`

* `giraph-examples.jar` Zkopíruje soubor do výchozího úložiště (WASB) pro váš cluster:`/example/jars/giraph-examples.jar`

## <a name="install"></a>Instalace Giraph pomocí akcí skriptů

Vzorový skript pro instalaci Giraph v clusteru HDInsight je k dispozici v tomto umístění:

    https://hdiconfigactions.blob.core.windows.net/linuxgiraphconfigactionv01/giraph-installer-v01.sh

V této části najdete pokyny, jak použít vzorový skript při vytváření clusteru pomocí Azure Portal.

> [!NOTE]  
> Akci skriptu lze použít pomocí kterékoli z následujících metod:
> * Azure PowerShell
> * Rozhraní příkazového řádku Azure
> * Sada HDInsight .NET SDK
> * Šablony Azure Resource Manageru
> 
> Můžete také použít akce skriptů pro již spuštěné clustery. Další informace najdete v tématu [Přizpůsobení clusterů HDInsight pomocí akcí skriptů](hdinsight-hadoop-customize-cluster-linux.md).

1. Začněte vytvářet cluster pomocí kroků v části [Vytvoření clusterů HDInsight se systémem Linux](hdinsight-hadoop-create-linux-clusters-portal.md), ale neprovádějte vytváření.

2. V části **volitelný konfigurační** oddíl vyberte **akce skriptu**a zadejte následující informace:

   * **NÁZEV**: Zadejte popisný název akce skriptu.

   * **IDENTIFIKÁTOR URI SKRIPTU**: https://hdiconfigactions.blob.core.windows.net/linuxgiraphconfigactionv01/giraph-installer-v01.sh

   * **POZICE**: Podívejte se na tuto položku.

   * **PRACOVNÍ PROCES**: Tuto položku nechte nezaškrtnutou.

   * **ZOOKEEPER**: Tuto položku nechte nezaškrtnutou.

   * **PARAMETRY**: Ponechte toto pole prázdné.

3. V dolní části **akcí skriptu**uložte konfiguraci pomocí tlačítka pro **Výběr** . Nakonec použijte tlačítko **Vybrat** v dolní části **volitelné konfiguračního** oddílu a uložte volitelné informace o konfiguraci.

4. Pokračujte v vytváření clusteru, jak je popsáno v tématu [vytváření clusterů HDInsight se systémem Linux](hdinsight-hadoop-create-linux-clusters-portal.md).

## <a name="usegiraph"></a>Návody používat Giraph v HDInsight?

Po vytvoření clusteru pomocí následujících kroků spusťte příklad SimpleShortestPathsComputation, který je součástí Giraph. Tento příklad používá základní implementaci [Pregel](https://people.apache.org/~edwardyoon/documents/pregel.pdf) k nalezení nejkratší cesty mezi objekty v grafu.

1. Připojte se ke clusteru HDInsight pomocí protokolu SSH:

    ```bash
    ssh USERNAME@CLUSTERNAME-ssh.azurehdinsight.net
    ```

    Další informace najdete v tématu [Použití SSH se službou HDInsight](hdinsight-hadoop-linux-use-ssh-unix.md).

2. Pomocí následujícího příkazu vytvořte soubor s názvem **tiny_graph. txt**:

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

    Tato data popisují vztah mezi objekty v řízeném grafu pomocí formátu `[source_id, source_value,[[dest_id], [edge_value],...]]`. Každý řádek představuje vztah mezi `source_id` objektem a jedním nebo více `dest_id` objekty. Lze `edge_value` si představit jako sílu nebo vzdálenost připojení mezi `source_id` a `dest\_id`.

    Tato data mohou vypadat podobně jako v následujícím diagramu, přičemž se použije hodnota (nebo váhy) jako vzdálenost mezi objekty.

    ![tiny_graph. txt vykreslený jako kruhy s čarami s různou vzdáleností mezi](./media/hdinsight-hadoop-giraph-install-linux/hdinsight-giraph-graph.png)

3. Pokud chcete soubor uložit, použijte **kombinaci kláves CTRL + X**, pak **Y**a nakonec **Zadejte** , abyste potvrdili název souboru.

4. K uložení dat do primárního úložiště pro cluster HDInsight použijte následující:

    ```bash
    hdfs dfs -put tiny_graph.txt /example/data/tiny_graph.txt
    ```

5. Spusťte příklad SimpleShortestPathsComputation pomocí následujícího příkazu:

    ```bash
    yarn jar /usr/hdp/current/giraph/giraph-examples.jar org.apache.giraph.GiraphRunner org.apache.giraph.examples.SimpleShortestPathsComputation -ca mapred.job.tracker=headnodehost:9010 -vif org.apache.giraph.io.formats.JsonLongDoubleFloatDoubleVertexInputFormat -vip /example/data/tiny_graph.txt -vof org.apache.giraph.io.formats.IdWithValueTextOutputFormat -op /example/output/shortestpaths -w 2
    ```

    Parametry používané s tímto příkazem jsou popsány v následující tabulce:

   | Parametr | Co dělá |
   | --- | --- |
   | `jar` |Soubor JAR obsahující příklady |
   | `org.apache.giraph.GiraphRunner` |Třída použitá ke spuštění příkladů. |
   | `org.apache.giraph.examples.SimpleShortestPathsCoputation` |Příklad, který je použit. V tomto příkladu vypočítá nejkratší cestu mezi ID 1 a všemi ostatními identifikátory v grafu. |
   | `-ca mapred.job.tracker` |Hlavnímu uzlu pro cluster. |
   | `-vif` |Vstupní formát, který se má použít pro vstupní data |
   | `-vip` |Vstupní datový soubor. |
   | `-vof` |Výstupní formát. V tomto příkladu ID a hodnota jako prostý text. |
   | `-op` |Umístění výstupu. |
   | `-w 2` |Počet pracovních procesů, které se mají použít. V tomto příkladu 2. |

    Další informace o těchto a dalších parametrech, které se používají s ukázkami Giraph, najdete v tématu [rychlý Start Giraph](https://giraph.apache.org/quick_start.html).

6. Po dokončení úlohy se výsledky uloží do adresáře **/example/out/shortestpaths** . Názvy výstupních souborů začínají **částí-m** a končí číslem, který označuje první, druhý atd. soubor. Výstup zobrazíte pomocí následujícího příkazu:

    ```bash
    hdfs dfs -text /example/output/shortestpaths/*
    ```

    Výstup se zobrazí podobně jako následující text:

        0    1.0
        4    5.0
        2    2.0
        1    0.0
        3    1.0

    SimpleShortestPathComputation příklad je pevně zakódovaný pro začátek s ID objektu 1 a vyhledá nejkratší cestu k ostatním objektům. Výstup je ve formátu `destination_id` a. `distance` `distance` Je hodnota (nebo váha) hran, které se cestují mezi objekty s ID 1 a cílovým ID.

    Vizualizace těchto dat můžete ověřit výsledky na cestách nejkratší cesty mezi ID 1 a všemi ostatními objekty. Nejkratší cesta mezi ID 1 a ID 4 je 5. Tato hodnota je celková vzdálenost mezi <span style="color:orange">ID 1 a 3</span>a pak s <span style="color:red">ID 3 a 4</span>.

    ![Vykreslování objektů jako kruhů s nejkratšími cestami nakreslenými mezi](./media/hdinsight-hadoop-giraph-install-linux/hdinsight-giraph-graph-out.png)

## <a name="next-steps"></a>Další postup

* [Instalace a použití odstínu v clusterech HDInsight](hdinsight-hadoop-hue-linux.md).
