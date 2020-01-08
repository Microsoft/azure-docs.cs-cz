---
title: Instalace a použití Giraph ve službě Azure HDInsight
description: Naučte se instalovat Giraph na clustery HDInsight pomocí akcí skriptů. Pomocí Giraph můžete provádět zpracování grafů v Apache Hadoop v cloudu Azure.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.date: 12/26/2019
ms.openlocfilehash: 1f6fd88ec492f26f6819dff099ec8fe53364ba0b
ms.sourcegitcommit: ec2eacbe5d3ac7878515092290722c41143f151d
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/31/2019
ms.locfileid: "75552249"
---
# <a name="install-apache-giraph-on-hdinsight-hadoop-clusters-and-use-giraph-to-process-large-scale-graphs"></a>Nainstalujte Apache Giraph do clusterů HDInsight Hadoop a používejte Giraph ke zpracování rozsáhlých grafů.

Přečtěte si, jak nainstalovat Apache Giraph v clusteru HDInsight. Funkce skriptu HDInsight umožňuje přizpůsobit cluster spuštěním skriptu bash. Skripty lze použít k přizpůsobení clusterů během a po vytvoření clusteru.

## <a name="what-is-giraph"></a>Co je Giraph

[Apache Giraph](https://giraph.apache.org/) vám umožňuje provádět zpracování grafů pomocí Hadoop a dá se použít s Azure HDInsight. Grafy modelů vztahů mezi objekty. Například připojení mezi směrovači ve velké síti, jako je například Internet, nebo vztahy mezi lidmi v sociálních sítích. Zpracování grafů vám umožňuje důvodné vztahy mezi objekty v grafu, například:

* Identifikace potenciálních přátel na základě aktuálních relací.

* Identifikace nejkratší trasy mezi dvěma počítači v síti.

* Výpočet pořadí stránek webových stránek.

> [!WARNING]  
> Součásti dodávané s clusterem HDInsight jsou plně podporované – podpora Microsoftu pomáhají izolovat a řešit problémy související s těmito součástmi.
>
> Vlastní komponenty, jako je Giraph, přijímají komerčně přiměřenou podporu, která vám může pomoct s dalším řešením tohoto problému. Podpora Microsoftu možná bude možné problém vyřešit. Pokud ne, musíte se obrátit na open source komunitu, kde najdete hloubkové znalosti pro tuto technologii. Například existuje mnoho webů komunity, které lze použít, například [Fórum MSDN pro HDInsight](https://social.msdn.microsoft.com/Forums/azure/en-US/home?forum=hdinsight), [https://stackoverflow.com](https://stackoverflow.com). Projekty Apache také obsahují projektové weby na [https://apache.org](https://apache.org), například: [Hadoop](https://hadoop.apache.org/).

## <a name="what-the-script-does"></a>Co skript dělá

Tento skript provede následující akce:

* Nainstaluje Giraph do `/usr/hdp/current/giraph`.

* Zkopíruje soubor `giraph-examples.jar` do výchozího úložiště (WASB) pro váš cluster: `/example/jars/giraph-examples.jar`.

## <a name="install-giraph-using-script-actions"></a>Instalace Giraph pomocí akcí skriptů

Vzorový skript pro instalaci Giraph v clusteru HDInsight je k dispozici na adrese `https://hdiconfigactions.blob.core.windows.net/linuxgiraphconfigactionv01/giraph-installer-v01.sh`

V této části najdete pokyny, jak použít vzorový skript při vytváření clusteru pomocí Azure Portal.

> [!NOTE]  
> Akci skriptu lze použít pomocí kterékoli z následujících metod:
> * Azure PowerShell
> * Rozhraní příkazového řádku Azure
> * Sada HDInsight .NET SDK
> * Šablony Azure Resource Manageru
> 
> Můžete také použít akce skriptů pro již spuštěné clustery. Další informace najdete v tématu [Přizpůsobení clusterů HDInsight pomocí akcí skriptů](hdinsight-hadoop-customize-cluster-linux.md).

1. Začněte vytvářet cluster pomocí kroků v části [Vytvoření clusterů HDInsight se systémem Linux](hdinsight-hadoop-create-linux-clusters-portal.md), ale nevytvářejte kompletní vytváření. Budete muset použít **klasické prostředí pro vytváření** a **vlastní (velikost, nastavení, aplikace)** .

1. V části **Velikost clusteru** zajistěte, aby byl **počet pracovních uzlů** alespoň 2, v tomto příkladu.

1. V části **akce skriptu** zadejte následující informace:

    |Vlastnost |Hodnota |
    |---|---|
    |Typ skriptu|– Vlastní|
    |Name (Název)|Nainstalovat Giraph|
    |Identifikátor URI skriptu bash|`https://hdiconfigactions.blob.core.windows.net/linuxgiraphconfigactionv01/giraph-installer-v01.sh`|
    |Typ (typy) uzlů|Hlavní uzel|
    |Parametry|Ponechte prázdné|

    Další informace najdete v tématu [použití akce skriptu při vytváření clusteru](./hdinsight-hadoop-customize-cluster-linux.md#use-a-script-action-during-cluster-creation).

1. Pokračujte v vytváření clusteru, jak je popsáno v tématu [vytváření clusterů HDInsight se systémem Linux](hdinsight-hadoop-create-linux-clusters-portal.md).

## <a name="how-do-i-use-giraph-in-hdinsight"></a>Návody používat Giraph v HDInsight?

Po vytvoření clusteru pomocí následujících kroků spusťte příklad SimpleShortestPathsComputation, který je součástí Giraph. Tento příklad používá základní implementaci [Pregel](https://people.apache.org/~edwardyoon/documents/pregel.pdf) k nalezení nejkratší cesty mezi objekty v grafu.

1. Připojte se ke clusteru pomocí [příkazu SSH](./hdinsight-hadoop-linux-use-ssh-unix.md) . Níže uvedený příkaz upravte tak, že ho nahradíte názvem clusteru a pak zadáte tento příkaz:

    ```cmd
    ssh sshuser@CLUSTERNAME-ssh.azurehdinsight.net
    ```

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

    Tato data popisují vztah mezi objekty v řízeném grafu pomocí `[source_id, source_value,[[dest_id], [edge_value],...]]`formátu. Každý řádek představuje vztah mezi objektem `source_id` a jedním nebo více objekty `dest_id`. `edge_value` lze představit jako sílu nebo vzdálenost připojení mezi `source_id` a `dest\_id`.

    Tato data mohou vypadat podobně jako v následujícím diagramu, přičemž se použije hodnota (nebo váhy) jako vzdálenost mezi objekty.

    ![tiny_graph. txt vykreslený jako kružnice s řádky s různou vzdáleností mezi](./media/hdinsight-hadoop-giraph-install-linux/hdinsight-giraph-graph.png)

3. Pokud chcete soubor uložit, použijte **kombinaci kláves CTRL + X**, pak **Y**a nakonec **Zadejte** , abyste potvrdili název souboru.

4. K uložení dat do primárního úložiště pro cluster HDInsight použijte následující:

    ```bash
    hdfs dfs -put tiny_graph.txt /example/data/tiny_graph.txt
    ```

5. Spusťte příklad SimpleShortestPathsComputation pomocí následujícího příkazu:

    ```bash
    yarn jar /usr/hdp/current/giraph/giraph-examples.jar org.apache.giraph.GiraphRunner org.apache.giraph.examples.SimpleShortestPathsComputation -ca mapred.job.tracker=headnodehost:9010 -vif org.apache.giraph.io.formats.JsonLongDoubleFloatDoubleVertexInputFormat -vip /example/data/tiny_graph.txt -vof org.apache.giraph.io.formats.IdWithValueTextOutputFormat -op /example/output/shortestpaths -w 2
    ```

    > [!IMPORTANT]
    > Hodnota předaná `-w` musí být menší než nebo rovna skutečnému počtu pracovních uzlů.

    Parametry používané s tímto příkazem jsou popsány v následující tabulce:

   | Parametr | Výsledek |
   | --- | --- |
   | JAR |Soubor JAR obsahující příklady |
   | org. Apache. Giraph. GiraphRunner |Třída použitá ke spuštění příkladů. |
   | org. Apache. Giraph. Examples. SimpleShortestPathsComputation |Příklad, který je použit. V tomto příkladu vypočítá nejkratší cestu mezi ID 1 a všemi ostatními identifikátory v grafu. |
   | – CA mapred. job. sledovací |Hlavnímu uzlu pro cluster. |
   | -vif |Vstupní formát, který se má použít pro vstupní data |
   | – VIP |Vstupní datový soubor. |
   | -vof |Výstupní formát. V tomto příkladu ID a hodnota jako prostý text. |
   | – op |Umístění výstupu. |
   | – w 2 |Počet pracovních procesů, které se mají použít. V tomto příkladu 2. |

    Další informace o těchto a dalších parametrech, které se používají s ukázkami Giraph, najdete v tématu [rychlý Start Giraph](https://giraph.apache.org/quick_start.html).

6. Po dokončení úlohy se výsledky uloží do adresáře **/example/Output/shortestpaths** . Názvy výstupních souborů začínají **částí až m** a končí číslem, který označuje první, druhý a tak, soubor. Výstup zobrazíte pomocí následujícího příkazu:

    ```bash
    hdfs dfs -text /example/output/shortestpaths/*
    ```

    Výstup se zobrazí podobně jako následující text:

    ```output
    0    1.0
    4    5.0
    2    2.0
    1    0.0
    3    1.0
    ```

    SimpleShortestPathComputation příklad je pevně zakódovaný pro začátek s ID objektu 1 a vyhledá nejkratší cestu k ostatním objektům. Výstup má formát `destination_id` a `distance`. `distance` je hodnota (nebo váha) hran mezi objekty s ID 1 a ID cíle.

    Vizualizace těchto dat můžete ověřit výsledky na cestách nejkratší cesty mezi ID 1 a všemi ostatními objekty. Nejkratší cesta mezi ID 1 a ID 4 je 5. Tato hodnota je celková vzdálenost mezi ID 1 a 3 a pak s ID 3 a 4.

    ![Vykreslování objektů jako kruhů s nejkratšími cestami nakreslenými mezi](./media/hdinsight-hadoop-giraph-install-linux/hdinsight-giraph-graph-out.png)

## <a name="next-steps"></a>Další kroky

[Instalace a použití odstínu v clusterech HDInsight](hdinsight-hadoop-hue-linux.md).
