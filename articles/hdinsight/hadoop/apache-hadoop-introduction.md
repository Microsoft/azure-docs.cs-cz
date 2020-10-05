---
title: Co jsou Apache Hadoop a MapReduce – Azure HDInsight
description: Úvod do služby HDInsight a Apache Hadoop a součásti technologie.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: overview
ms.custom: hdinsightactive,hdiseo17may2017,mvc,seodec18
ms.date: 02/27/2020
ms.openlocfilehash: 5e5f02b1684e56496778ab677aa9dc46e7dcd9aa
ms.sourcegitcommit: eb6bef1274b9e6390c7a77ff69bf6a3b94e827fc
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/05/2020
ms.locfileid: "87086519"
---
# <a name="what-is-apache-hadoop-in-azure-hdinsight"></a>Co je Apache Hadoop ve službě Azure HDInsight?

[Apache Hadoop](https://hadoop.apache.org/) byl původní open source architekturou pro distribuované zpracování a analýzy velkých datových sad v clusterech. Ekosystém Hadoop zahrnuje související software a nástroje, včetně Apache Hive, Apache HBA, Spark, Kafka a mnoha dalších.

Azure HDInsight je plně spravovaná, plně spektrum Open Source služby pro analýzu v cloudu pro podniky. Typ clusteru Apache Hadoop v Azure HDInsight umožňuje používat HDFS, správu prostředků PŘÍZe a jednoduchý programovací model MapReduce pro souběžné zpracování a analýzu dat Batch.

Komponenty technologie Hadoop dostupné ve službě HDInsight najdete v tématu [Dostupné komponenty a verze ve službě HDInsight](../hdinsight-component-versioning.md). Další informace o platformě Hadoop v prostředí HDInsight najdete v tématu [Funkce Azure pro HDInsight](https://azure.microsoft.com/services/hdinsight/).

## <a name="what-is-mapreduce"></a>Co je MapReduce

Apache Hadoop MapReduce je softwarové rozhraní pro psaní úloh, které zpracovávají velké objemy dat. Vstupní data jsou rozdělená do nezávislých bloků dat. Jednotlivé bloky dat se zpracovávají paralelně napříč uzly ve vašem clusteru. Úloha MapReduce se skládá ze dvou funkcí:

* **Mapper**: používá vstupní data, analyzuje je (obvykle pomocí operací filtrování a řazení) a generuje řazené kolekce členů (páry klíč-hodnota).

* **Redukce**: spotřebovává řazené kolekce členů generovaných mapovačem a provede souhrnnou operaci, která vytvoří menší, kombinovaný výsledek z dat mapovače.

Příkladem MapReduce úlohy Basic Count (počet slov) je znázorněný v následujícím diagramu:

 ![HDI. WordCountDiagram](./media/apache-hadoop-introduction/hdi-word-count-diagram.gif)

Výstupem této úlohy je počet, kolikrát se každé slovo v textu objevilo.

* Mapovač přebírá každý řádek ze vstupního textu jako vstup a přerušuje na slova. Vygeneruje dvojici klíč/hodnota pokaždé, když se slovo objeví v slově a následuje 1. Výstup je seřazen před odesláním do zmenšení.
* Tento oddělovač sečte jednotlivé počty jednotlivých slov a vygeneruje jeden pár klíč/hodnota, který obsahuje slovo následovaný součtem jeho výskytů.

MapReduce je možné implementovat v různých jazycích. Java je nejběžnější implementace, která se používá pro demonstrační účely v tomto dokumentu.

## <a name="development-languages"></a>Vývojové jazyky

Jazyky nebo architektury založené na jazyce Java a prostředí Java Virtual Machine mohou být spuštěny přímo jako úloha MapReduce. Příkladem použitým v tomto dokumentu je aplikace Java MapReduce. Jazyky mimo jazyk Java, jako je C#, Python nebo samostatné spustitelné soubory, musí využívat **streamování Hadoop**.

Streamování Hadoop komunikuje s mapovačem a redukcí přes STDIN a STDOUT. Mapovač a zmenšování čtou data na řádek ze standardního vstupu a zápis výstupu do STDOUT. Každý řádek načtený nebo vygenerovaný mapovačem a redukcí musí být ve formátu dvojice klíč/hodnota, oddělený znakem tabulátoru:

`[key]\t[value]`

Další informace najdete v tématu [streamování Hadoop](https://hadoop.apache.org/docs/current/hadoop-streaming/HadoopStreaming.html).

Příklady použití streamování Hadoop v HDInsight najdete v tomto dokumentu:

* [Vývoj úloh C# MapReduce](apache-hadoop-dotnet-csharp-mapreduce-streaming.md)

## <a name="next-steps"></a>Další kroky

* [Vytvoření clusteru Apache Hadoop v HDInsight](apache-hadoop-linux-create-cluster-get-started-portal.md)
