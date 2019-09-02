---
title: Co je Apache Hadoop Technology Stack? – Azure HDInsight
description: Úvod do služby HDInsight a Apache Hadoop a součásti technologie.
keywords: azure hadoop, hadoop azure, hadoop úvod, úvod hadoop, technologie hadoop, úvod do technologie hadoop, úvod k technologii hadoop, co je cluster hadoop, co je hadoop cluster, k čemu slouží hadoop
author: hrasheed-msft
ms.author: hrasheed
ms.service: hdinsight
ms.custom: hdinsightactive,hdiseo17may2017,mvc,seodec18
ms.topic: overview
ms.date: 08/15/2019
ms.openlocfilehash: d6127d4fba3d2255dee28cd41179f16394545328
ms.sourcegitcommit: 5f67772dac6a402bbaa8eb261f653a34b8672c3a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/01/2019
ms.locfileid: "70207230"
---
# <a name="what-is-apache-hadoop-in-azure-hdinsight"></a>Co je Apache Hadoop ve službě Azure HDInsight?

[Apache Hadoop](https://hadoop.apache.org/) byl původní open source architekturou pro distribuované zpracování a analýzy velkých datových sad v clusterech. Ekosystém Hadoop zahrnuje související software a nástroje, včetně Apache Hive, Apache HBA, Spark, Kafka a mnoha dalších.

Azure HDInsight je plně spravovaná, plně spektrum Open Source služby pro analýzu v cloudu pro podniky. Typ clusteru Apache Hadoop v Azure HDInsight umožňuje používat HDFS, správu prostředků PŘÍZe a jednoduchý programovací model MapReduce pro souběžné zpracování a analýzu dat Batch.

Pokud chcete zobrazit dostupné komponenty zásobníku technologie Hadoop ve službě HDInsight, přečtěte si téma [součásti a verze dostupné v HDInsight](../hdinsight-component-versioning.md). Další informace o platformě Hadoop v prostředí HDInsight najdete v tématu [Funkce Azure pro HDInsight](https://azure.microsoft.com/services/hdinsight/).

## <a id="whatis"></a>Co je MapReduce

Apache Hadoop MapReduce je softwarové rozhraní pro psaní úloh, které zpracovávají velké objemy dat. Vstupní data jsou rozdělená do nezávislých bloků dat. Jednotlivé bloky dat se zpracovávají paralelně napříč uzly ve vašem clusteru. Úloha MapReduce se skládá ze dvou funkcí:

* **Mapovač**: Spotřebovává vstupní data, analyzuje je (obvykle pomocí operací filtrování a řazení) a generuje řazené kolekce členů (páry klíč-hodnota).

* **Redukce**: Spotřebovává řazené kolekce členů generované mapovačem a provádí souhrnnou operaci, která vytváří menší, kombinovaný výsledek z dat mapovače.

Příkladem MapReduce úlohy Basic Count (počet slov) je znázorněný v následujícím diagramu:

![HDI.WordCountDiagram][image-hdi-wordcountdiagram]

Výstupem této úlohy je počet, kolikrát se každé slovo v textu objevilo.

* Mapovač přebírá každý řádek ze vstupního textu jako vstup a přerušuje na slova. Vygeneruje dvojici klíč/hodnota pokaždé, když se slovo objeví v slově a následuje 1. Výstup je seřazen před odesláním do zmenšení.
* Tento oddělovač sečte jednotlivé počty jednotlivých slov a vygeneruje jeden pár klíč/hodnota, který obsahuje slovo následovaný součtem jeho výskytů.

MapReduce je možné implementovat v různých jazycích. Java je nejběžnější implementace, která se používá pro demonstrační účely v tomto dokumentu.

## <a name="development-languages"></a>Vývojové jazyky

Jazyky nebo architektury založené na jazyce Java a prostředí Java Virtual Machine mohou být spuštěny přímo jako úloha MapReduce. Příkladem použitým v tomto dokumentu je aplikace Java MapReduce. Jazyky mimo jazyk Java, například C#Python nebo samostatné spustitelné soubory, musí využívat streamování **Hadoop**.

Streamování Hadoop komunikuje s mapovačem a redukcí přes STDIN a STDOUT. Mapovač a zmenšování čtou data na řádek ze standardního vstupu a zápis výstupu do STDOUT. Každý řádek načtený nebo vygenerovaný mapovačem a redukcí musí být ve formátu dvojice klíč/hodnota, oddělený znakem tabulátoru:

    [key]/t[value]

Další informace najdete v tématu [streamování Hadoop](https://hadoop.apache.org/docs/r1.2.1/streaming.html).

Příklady použití streamování Hadoop v HDInsight najdete v tomto dokumentu:

* [Vývoj C# úloh MapReduce](apache-hadoop-dotnet-csharp-mapreduce-streaming.md)

## <a name="next-steps"></a>Další postup

* [Vytvoření clusteru Apache Hadoop v HDInsight](apache-hadoop-linux-create-cluster-get-started-portal.md)

[image-hdi-wordcountdiagram]: ./media/hdinsight-use-mapreduce/HDI.WordCountDiagram.gif
