---
title: MapReduce s Hadoop v HDInsight | Microsoft Docs
description: Informace o spouštění úloh MapReduce systému Hadoop v clusterech HDInsight.
services: hdinsight
documentationcenter: ''
author: Blackmist
manager: jhubbard
editor: cgronlun
tags: azure-portal
ms.assetid: 7f321501-d62c-4ffc-b5d6-102ecba6dd76
ms.service: hdinsight
ms.custom: hdinsightactive
ms.devlang: na
ms.topic: conceptual
ms.date: 05/16/2018
ms.author: larryfr
ms.openlocfilehash: 131e3065da4ccfb20d63856844a302a94806fd19
ms.sourcegitcommit: eb75f177fc59d90b1b667afcfe64ac51936e2638
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/16/2018
ms.locfileid: "34202624"
---
# <a name="use-mapreduce-in-hadoop-on-hdinsight"></a>Používání nástroje MapReduce v Hadoop v HDInsight

Informace o spouštění úloh MapReduce clustery prostředí HDInsight. Následující tabulku použijte ke zjištění různých způsobů, jak lze MapReduce s HDInsight:

| **Použít**... | **...a tomu** | ...při to **clusteru operačního systému** | ...from to **klientský operační systém** |
|:--- |:--- |:--- |:--- |
| [SSH](apache-hadoop-use-mapreduce-ssh.md) |Použijte příkaz Hadoop prostřednictvím **SSH** |Linux |Linux, Unix, Mac OS X nebo systému Windows |
| [REST](apache-hadoop-use-mapreduce-curl.md) |Odeslání úlohy vzdáleně pomocí **REST** (příklady použití cURL) |Linux nebo Windows |Linux, Unix, Mac OS X nebo systému Windows |
| [Windows PowerShell](apache-hadoop-use-mapreduce-powershell.md) |Odeslání úlohy vzdáleně pomocí **prostředí Windows PowerShell** |Linux nebo Windows |Windows |

> [!IMPORTANT]
> HDInsight od verze 3.4 výše používá výhradně operační systém Linux. Další informace najdete v tématu [Vyřazení prostředí HDInsight ve Windows](../hdinsight-component-versioning.md#hdinsight-windows-retirement).
>

## <a id="whatis"></a>Co je MapReduce

Hadoop MapReduce je framework software pro zápis úlohy, které zpracovávají velké množství dat. Vstupní data je rozdělená do bloků, nezávislé. Každého bloku se zpracovávají paralelně mezi uzly v clusteru. Úloha MapReduce se skládá z dvě funkce:

* **Mapovač**: spotřebovává vstupních dat, analyzuje (obvykle s filtrování a řazení operations) a vysílá řazených kolekcí členů (páry klíč hodnota)

* **Reduktorem**: využívá řazených kolekcí členů vygenerované mapovačem a provádí operaci souhrn, která vytvoří výsledek menší, kombinované z Mapovač dat

V příkladu úlohy MapReduce počet základní word je znázorněno v následujícím diagramu:

![HDI. WordCountDiagram][image-hdi-wordcountdiagram]

Výstup této úlohy je počet kolikrát jednotlivých slov došlo k chybě v textu.

* Mapper trvá každý řádek ze vstupního textu jako vstup a dělí na slova. Ho vysílá klíč/hodnota pár pokaždé, když dojde k slovo aplikace word následuje 1. Výstup je řazen před odesláním reduktorem.
* Reduktorem sčítá tyto počty jednotlivých pro každou aplikaci word a vysílá pár jeden klíč/hodnota obsahující slovo a součet jeho výskytů.

MapReduce můžou se implementovat v různých jazycích. Java nejběžnější implementace a slouží pro účely ukázky v tomto dokumentu.

## <a name="development-languages"></a>Programovacích jazyků

Jazyky nebo rozhraní, které jsou založeny na jazyce Java a virtuální počítač Java můžete spustili přímo jako úlohu MapReduce. V příkladu v tomto dokumentu je aplikace Java MapReduce. Jazyky Java jiného typu, jako je C#, Python nebo samostatné spustitelné soubory, musíte použít **streamování Hadoop**.

Streamování Hadoop komunikuje s mapper a reduktorem pomocí standardního vstupu a výstupu STDOUT. Mapovač a reduktorem číst data řádku současně z stdin – a zapisovat výstup STDOUT. Každý řádek čtení nebo vysílaných mapper a reduktorem musí být ve formátu pár klíč hodnota oddělená tabulátorem:

    [key]/t[value]

Další informace najdete v tématu [streamování Hadoop](http://hadoop.apache.org/docs/r1.2.1/streaming.html).

Příklady použití streamování s HDInsight Hadoop najdete v následujících dokumentech:

* [Vývoj úloh MapReduce C#](apache-hadoop-dotnet-csharp-mapreduce-streaming.md)

* [Vývoj úloh Python MapReduce](apache-hadoop-streaming-python.md)

## <a id="data"></a>Příklad dat

HDInsight poskytuje různé příklad datových sad, které jsou uloženy v `/example/data` a `/HdiSamples` adresáře. Tyto adresáře jsou ve výchozím nastavení úložiště pro cluster. V tomto dokumentu budeme používat `/example/data/gutenberg/davinci.txt` souboru. Tento soubor obsahuje poznámkových bloků Leonardo Da Vinci.

## <a id="job"></a>Příklad MapReduce

Příklad počet dokumentů aplikace word MapReduce je součástí clusteru HDInsight. V tomto příkladu je umístěn v `/example/jars/hadoop-mapreduce-examples.jar` na výchozí úložiště pro cluster.

Následující kód Java je zdrojem MapReduce aplikace součástí `hadoop-mapreduce-examples.jar` souboru:

```java
package org.apache.hadoop.examples;

import java.io.IOException;
import java.util.StringTokenizer;

import org.apache.hadoop.conf.Configuration;
import org.apache.hadoop.fs.Path;
import org.apache.hadoop.io.IntWritable;
import org.apache.hadoop.io.Text;
import org.apache.hadoop.mapreduce.Job;
import org.apache.hadoop.mapreduce.Mapper;
import org.apache.hadoop.mapreduce.Reducer;
import org.apache.hadoop.mapreduce.lib.input.FileInputFormat;
import org.apache.hadoop.mapreduce.lib.output.FileOutputFormat;
import org.apache.hadoop.util.GenericOptionsParser;

public class WordCount {

    public static class TokenizerMapper
        extends Mapper<Object, Text, Text, IntWritable>{

    private final static IntWritable one = new IntWritable(1);
    private Text word = new Text();

    public void map(Object key, Text value, Context context
                    ) throws IOException, InterruptedException {
        StringTokenizer itr = new StringTokenizer(value.toString());
        while (itr.hasMoreTokens()) {
        word.set(itr.nextToken());
        context.write(word, one);
        }
    }
    }

    public static class IntSumReducer
        extends Reducer<Text,IntWritable,Text,IntWritable> {
    private IntWritable result = new IntWritable();

    public void reduce(Text key, Iterable<IntWritable> values,
                        Context context
                        ) throws IOException, InterruptedException {
        int sum = 0;
        for (IntWritable val : values) {
        sum += val.get();
        }
        result.set(sum);
        context.write(key, result);
    }
    }

    public static void main(String[] args) throws Exception {
    Configuration conf = new Configuration();
    String[] otherArgs = new GenericOptionsParser(conf, args).getRemainingArgs();
    if (otherArgs.length != 2) {
        System.err.println("Usage: wordcount <in> <out>");
        System.exit(2);
    }
    Job job = new Job(conf, "word count");
    job.setJarByClass(WordCount.class);
    job.setMapperClass(TokenizerMapper.class);
    job.setCombinerClass(IntSumReducer.class);
    job.setReducerClass(IntSumReducer.class);
    job.setOutputKeyClass(Text.class);
    job.setOutputValueClass(IntWritable.class);
    FileInputFormat.addInputPath(job, new Path(otherArgs[0]));
    FileOutputFormat.setOutputPath(job, new Path(otherArgs[1]));
    System.exit(job.waitForCompletion(true) ? 0 : 1);
    }
}
```

Pokyny pro zápis aplikace MapReduce najdete v následujících dokumentech:

* [Vývoj aplikací Java MapReduce pro HDInsight](apache-hadoop-develop-deploy-java-mapreduce-linux.md)

* [Vývoj aplikací Python MapReduce pro HDInsight](apache-hadoop-streaming-python.md)

## <a id="run"></a>Spuštění MapReduce

HDInsight HiveQL úlohy můžete spustit pomocí různých metod. Následující tabulku použijte k rozhodování, jakou metodu je pro vás nejvhodnější a potom klepněte na odkaz návod.

| **Použít**... | **...a tomu** | ...při to **clusteru operačního systému** | ...from to **klientský operační systém** |
|:--- |:--- |:--- |:--- |
| [SSH](apache-hadoop-use-mapreduce-ssh.md) |Použijte příkaz Hadoop prostřednictvím **SSH** |Linux |Linux, Unix, Mac OS X nebo systému Windows |
| [Curl](apache-hadoop-use-mapreduce-curl.md) |Odeslání úlohy vzdáleně pomocí **REST** |Linux nebo Windows |Linux, Unix, Mac OS X nebo systému Windows |
| [Windows PowerShell](apache-hadoop-use-mapreduce-powershell.md) |Odeslání úlohy vzdáleně pomocí **prostředí Windows PowerShell** |Linux nebo Windows |Windows |

> [!IMPORTANT]
> HDInsight od verze 3.4 výše používá výhradně operační systém Linux. Další informace najdete v tématu [Vyřazení prostředí HDInsight ve Windows](../hdinsight-component-versioning.md#hdinsight-windows-retirement).

## <a id="nextsteps"></a>Další kroky

Další informace o práci s daty v HDInsight, najdete v následujících dokumentech:

* [Vývoj aplikací Java MapReduce pro HDInsight](apache-hadoop-develop-deploy-java-mapreduce-linux.md)

* [Vývoj Python streamování MapReduce programy pro HDInsight](apache-hadoop-streaming-python.md)

* [Použití Hivu se službou HDInsight][hdinsight-use-hive]

* [Použití Pigu se službou HDInsight][hdinsight-use-pig]


[hdinsight-upload-data]: hdinsight-upload-data.md
[hdinsight-get-started]:apache-hadoop-linux-tutorial-get-started.md
[hdinsight-develop-mapreduce-jobs]: apache-hadoop-develop-deploy-java-mapreduce-linux.md
[hdinsight-use-hive]:../hdinsight-use-hive.md
[hdinsight-use-pig]:hdinsight-use-pig.md


[powershell-install-configure]: /powershell/azureps-cmdlets-docs

[image-hdi-wordcountdiagram]: ./media/hdinsight-use-mapreduce/HDI.WordCountDiagram.gif
