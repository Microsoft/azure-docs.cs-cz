---
title: Co je Apache Hadoop a MapReduce – Azure HDInsight
description: Úvod do služby HDInsight a Apache Hadoop a součásti technologie.
ms.service: hdinsight
ms.topic: overview
ms.custom: hdinsightactive,hdiseo17may2017,mvc,seodec18
ms.date: 02/27/2020
ms.openlocfilehash: 1e3fc23dd220a8d9764a64c453e9fb856307ac47
ms.sourcegitcommit: 2f9f306fa5224595fa5f8ec6af498a0df4de08a8
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/28/2021
ms.locfileid: "98946515"
---
# <a name="what-is-apache-hadoop-in-azure-hdinsight"></a>Co je Apache Hadoop ve službě Azure HDInsight?

[Apache Hadoop](https://hadoop.apache.org/) byl původní open source architekturou pro distribuované zpracování a analýzy velkých datových sad v clusterech. Ekosystém Hadoop zahrnuje související software a nástroje, včetně Apache Hive, Apache HBA, Spark, Kafka a mnoha dalších.

Azure HDInsight je plně spravovaná, plně spektrum Open Source služby pro analýzu v cloudu pro podniky. Typ clusteru Apache Hadoop v Azure HDInsight umožňuje používat [Apache Hadoop systém souborů DFS (Distributed File System) (HDFS)](https://hadoop.apache.org/docs/current/hadoop-project-dist/hadoop-hdfs/HdfsUserGuide.html), Apache Hadoop správu prostředků [příze](https://hadoop.apache.org/docs/current/hadoop-yarn/hadoop-yarn-site/YARN.html) a jednoduchý programovací model [MapReduce](https://hadoop.apache.org/docs/current/hadoop-mapreduce-client/hadoop-mapreduce-client-core/MapReduceTutorial.html) pro souběžné zpracování a analýzu dat Batch.  Clustery Hadoop ve službě HDInsight jsou kompatibilní se službou [Azure Blob Storage](../../storage/common/storage-introduction.md), [Azure Data Lake Storage Gen1](../../data-lake-store/data-lake-store-overview.md)nebo [Azure Data Lake Storage Gen2](../../storage/blobs/data-lake-storage-introduction.md).

Komponenty technologie Hadoop dostupné ve službě HDInsight najdete v tématu [Dostupné komponenty a verze ve službě HDInsight](../hdinsight-component-versioning.md). Další informace o platformě Hadoop v prostředí HDInsight najdete v tématu [Funkce Azure pro HDInsight](https://azure.microsoft.com/services/hdinsight/).

## <a name="what-is-mapreduce"></a>Co je MapReduce

[Apache Hadoop MapReduce](https://hadoop.apache.org/docs/current/hadoop-mapreduce-client/hadoop-mapreduce-client-core/MapReduceTutorial.html) je softwarové rozhraní pro psaní úloh, které zpracovávají velké objemy dat. Vstupní data jsou rozdělená do nezávislých bloků dat. Jednotlivé bloky dat se zpracovávají paralelně napříč uzly ve vašem clusteru. Úloha MapReduce se skládá ze dvou funkcí:

* **Mapper**: používá vstupní data, analyzuje je (obvykle pomocí operací filtrování a řazení) a generuje řazené kolekce členů (páry klíč-hodnota).

* **Redukce**: spotřebovává řazené kolekce členů generovaných mapovačem a provede souhrnnou operaci, která vytvoří menší, kombinovaný výsledek z dat mapovače.

Příkladem MapReduce úlohy Basic Count (počet slov) je znázorněný v následujícím diagramu:

 ![HDI. WordCountDiagram](./media/apache-hadoop-introduction/hdi-word-count-diagram.gif)

Výstupem této úlohy je počet, kolikrát se každé slovo v textu objevilo.

* Mapovač přebírá každý řádek ze vstupního textu jako vstup a přerušuje na slova. Vygeneruje dvojici klíč/hodnota pokaždé, když se slovo objeví v slově a následuje 1. Výstup je seřazen před odesláním do zmenšení.
* Tento oddělovač sečte jednotlivé počty jednotlivých slov a vygeneruje jeden pár klíč/hodnota, který obsahuje slovo následovaný součtem jeho výskytů.

MapReduce je možné implementovat v různých jazycích. Java je nejběžnější implementace, která se používá pro demonstrační účely v tomto dokumentu.

## <a name="development-languages"></a>Vývojové jazyky

Jazyky nebo architektury založené na jazyce Java a prostředí Java Virtual Machine mohou být spuštěny přímo jako [úloha MapReduce](..//hadoop/submit-apache-hadoop-jobs-programmatically.md). Příkladem použitým v tomto dokumentu je aplikace Java MapReduce. Jazyky mimo jazyk Java, jako je C#, Python nebo samostatné spustitelné soubory, musí využívat **streamování Hadoop**.

Streamování Hadoop komunikuje s mapovačem a redukcí přes STDIN a STDOUT. Mapovač a zmenšování čtou data na řádek ze standardního vstupu a zápis výstupu do STDOUT. Každý řádek načtený nebo vygenerovaný mapovačem a redukcí musí být ve formátu dvojice klíč/hodnota, oddělený znakem tabulátoru:

`[key]\t[value]`

Další informace najdete v tématu [streamování Hadoop](https://hadoop.apache.org/docs/current/hadoop-streaming/HadoopStreaming.html).

Příklady použití streamování Hadoop v HDInsight najdete v tomto dokumentu:

* [Vývoj úloh C# MapReduce](apache-hadoop-dotnet-csharp-mapreduce-streaming.md)

## <a name="where-do-i-start"></a>Kde mám začít

* [Rychlý Start: Vytvoření clusteru Apache Hadoop ve službě Azure HDInsight pomocí Azure Portal](../hadoop/apache-hadoop-linux-create-cluster-get-started-portal.md)
* [Kurz: odeslání Apache Hadoop úloh v HDInsight](../hadoop/submit-apache-hadoop-jobs-programmatically.md)
* [Vývoj programů Java MapReduce pro Apache Hadoop v HDInsight](../hadoop/apache-hadoop-develop-deploy-java-mapreduce-linux.md)
* [Použití Apache Hive jako nástroje pro extrakci, transformaci a načítání (ETL)](../hadoop/apache-hadoop-using-apache-hive-as-an-etl-tool.md)
* [Extrakce, transformace a načítání (ETL) ve velkém měřítku](../hadoop/apache-hadoop-etl-at-scale.md)
* [Zprovoznění kanálu datových analýz](../hdinsight-operationalize-data-pipeline.md)

## <a name="next-steps"></a>Další kroky

* [Vytvoření clusteru Apache Hadoop v HDInsight pomocí portálu](../hadoop/apache-hadoop-linux-create-cluster-get-started-portal.md)
* [Vytvoření clusteru Apache Hadoop v HDInsight pomocí šablony ARM](../hadoop/apache-hadoop-linux-tutorial-get-started.md)