---
title: Co jsou Apache Hadoop a MapReduce - Azure HDInsight
description: Úvod do HDInsight a apache hadoop technologie zásobníku a komponent.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: overview
ms.custom: hdinsightactive,hdiseo17may2017,mvc,seodec18
ms.date: 02/27/2020
ms.openlocfilehash: 7e8dd69b7c58e090c30ea1aa59feddab610dd3c5
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/26/2020
ms.locfileid: "78244872"
---
# <a name="what-is-apache-hadoop-in-azure-hdinsight"></a>Co je Apache Hadoop v Azure HDInsight?

[Apache Hadoop](https://hadoop.apache.org/) byl původní open source architekturou pro distribuované zpracování a analýzy velkých datových sad v clusterech. Ekosystém Hadoop zahrnuje související software a nástroje, včetně Apache Hive, Apache HBase, Spark, Kafka a mnoho dalších.

Azure HDInsight je plně spravovaná, celospektrální analytická služba s otevřeným zdrojovým kódem v cloudu pro podniky. Typ clusteru Apache Hadoop v Azure HDInsight umožňuje používat HDFS, správu prostředků YARN a jednoduchý programovací model MapReduce ke paralelnímu zpracování a analýze dávkových dat.

Komponenty technologie Hadoop dostupné ve službě HDInsight najdete v tématu [Dostupné komponenty a verze ve službě HDInsight](../hdinsight-component-versioning.md). Další informace o platformě Hadoop v prostředí HDInsight najdete v tématu [Funkce Azure pro HDInsight](https://azure.microsoft.com/services/hdinsight/).

## <a name="what-is-mapreduce"></a>Co je MapReduce

Apache Hadoop MapReduce je softwarový rámec pro psaní úloh, které zpracovávají obrovské množství dat. Vstupní data jsou rozdělena do nezávislých bloků dat. Každý blok je zpracován paralelně napříč uzly v clusteru. Úloha MapReduce se skládá ze dvou funkcí:

* **Mapovač**: Spotřebovává vstupní data, analyzuje je (obvykle pomocí operací filtrování a třídění) a vydává řazené kolekce členů (dvojice klíč-hodnota)

* **Reduktor**: Spotřebovává řazené kolekce členů vysílané mapovačem a provede souhrnnou operaci, která vytvoří menší kombinovaný výsledek z dat mapovače.

Základní počet slov MapReduce příklad úlohy je znázorněno v následujícím diagramu:

 ![Hdi. WordCountDiagram](./media/apache-hadoop-introduction/hdi-word-count-diagram.gif)

Výstupem této úlohy je počet, kolikrát se každé slovo v textu objevilo.

* Mapovač vezme každý řádek ze vstupního textu jako vstup a rozdělí ho na slova. Vydává dvojici klíč/hodnota pokaždé, když dojde ke slovu, následuje 1. Výstup je seřazen před odesláním do reduktoru.
* Reduktor shrnuje tyto jednotlivé počty pro každé slovo a vydává jeden klíč/hodnota pár, který obsahuje slovo následuje součet jeho výskytů.

MapReduce lze implementovat v různých jazycích. Java je nejběžnější implementace a používá se pro demonstrační účely v tomto dokumentu.

## <a name="development-languages"></a>Vývojové jazyky

Jazyky nebo architektury, které jsou založeny na Java a Java Virtual Machine lze schovat přímo jako úlohu MapReduce. Příkladem použitým v tomto dokumentu je aplikace Java MapReduce. Jazyky mimo jazyky než Java, například C#, Python nebo samostatné spustitelné soubory, musí používat **streamování Hadoop**.

Hadoop streaming komunikuje s mapovačem a reduktorem přes STDIN a STDOUT. Mapovač a reduktor číst data řádek najednou z STDIN a zapsat výstup STDOUT. Každý řádek přečtený nebo vyzařovaný mapovačem a reduktorem musí být ve formátu páru klíč/hodnota, vymezený znakem tabulátoru:

    [key]/t[value]

Další informace naleznete v [tématu Hadoop Streaming](https://hadoop.apache.org/docs/current/hadoop-streaming/HadoopStreaming.html).

Příklady použití streamování Hadoopu pomocí hdinsightu najdete v následujícím dokumentu:

* [Vývoj úloh C# MapReduce](apache-hadoop-dotnet-csharp-mapreduce-streaming.md)

## <a name="next-steps"></a>Další kroky

* [Vytvoření clusteru Apache Hadoop v HDInsightu](apache-hadoop-linux-create-cluster-get-started-portal.md)
