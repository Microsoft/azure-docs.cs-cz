---
title: MapReduce se systémem Hadoop v HDInsight
description: Zjistěte, jak spouštět úlohy mapreduce je možné na systému Hadoop v prostředí clusterů HDInsight.
services: hdinsight
author: jasonwhowell
ms.author: jasonh
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 05/16/2018
ms.openlocfilehash: 3b9cc70a1adc55850923f2313f17be435257117d
ms.sourcegitcommit: 161d268ae63c7ace3082fc4fad732af61c55c949
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/27/2018
ms.locfileid: "43044865"
---
# <a name="use-mapreduce-in-hadoop-on-hdinsight"></a>Použití MapReduce se v clusteru Hadoop v HDInsight

Zjistěte, jak spouštět úlohy MapReduce na clusterech HDInsight. Následující tabulku použijte k prozkoumejte různé způsoby, jak je možné MapReduce s HDInsight:

| **Použít**... | **...a tomu** | ...při to **clusteru operačního systému** | ...from to **klientský operační systém** |
|:--- |:--- |:--- |:--- |
| [SSH](apache-hadoop-use-mapreduce-ssh.md) |Použití příkazu Hadoop prostřednictvím **SSH** |Linux |Linux, Unix, Mac OS X a Windows |
| [REST](apache-hadoop-use-mapreduce-curl.md) |Odeslání úlohy vzdáleně pomocí **REST** (příkladech používáme nástroj cURL) |Linux nebo Windows |Linux, Unix, Mac OS X a Windows |
| [Windows PowerShell](apache-hadoop-use-mapreduce-powershell.md) |Odeslání úlohy vzdáleně pomocí **prostředí Windows PowerShell** |Linux nebo Windows |Windows |

> [!IMPORTANT]
> HDInsight od verze 3.4 výše používá výhradně operační systém Linux. Další informace najdete v tématu [Vyřazení prostředí HDInsight ve Windows](../hdinsight-component-versioning.md#hdinsight-windows-retirement).
>

## <a id="whatis"></a>Co je MapReduce

Hadoop MapReduce je software platformě pro psaní úlohy, které zpracovávají velké objemy dat. Vstupní data je rozdělený do bloků nezávislé. Každý blok se zpracovávají paralelně napříč uzly v clusteru. Úlohu MapReduce se skládá ze dvou funkcí:

* **Mapovač**: zpracovává vstupní data, analyzuje je (obvykle pomocí filtrování a řazení) a generuje řazených kolekcí členů (páry klíč hodnota)

* **Redukční funkci**: využívá řazených kolekcí členů, protože ho vygeneroval Mapovač a provádí operaci summary, která vytvoří menší, kombinované výsledek z Mapovač dat

Jako příklad úlohy MapReduce počet slova je znázorněn v následujícím diagramu:

![HDI. WordCountDiagram][image-hdi-wordcountdiagram]

Výstupem této úlohy je počet, kolikrát se každé slovo došlo k chybě v textu.

* Mapovač má každý řádek ze vstupního textu jako vstup a rozdělí na slova. Generuje klíč/hodnota pár pokaždé, když dojde k slovo slova následuje 1. Výstup je řazen před odesláním redukční funkci.
* Redukční funkci Součet těchto počtů jednotlivé pro každé slovo a vysílá dvojici klíč/hodnota, která obsahuje slovo následované součet jeho výskyty.

MapReduce je možné implementovat v různých jazycích. Java je většina běžných implementací a slouží pro účely ukázky v tomto dokumentu.

## <a name="development-languages"></a>Vývojářské jazyky

Jazyků nebo architektur, které jsou založené na jazyce Java a virtuální počítač Java můžete spustit přímo jako úlohu MapReduce. V příkladu v tomto dokumentu se používá aplikace MapReduce v Javě. Java jiné jazyky, jako je C#, Python nebo samostatné spustitelné soubory, musíte použít **streamování Hadoop**.

Streamování Hadoop komunikuje s mapovací a redukční funkci pomocí standardního vstupu a výstupu STDOUT. Mapovací a redukční funkci Číst data po jednom ze STDIN řádku a zapisovat výstup do STDOUT. Každý řádek čtení nebo, protože ho vygeneroval mapovací a redukční funkci musí být ve formátu páru klíč hodnota oddělené tabulátorem:

    [key]/t[value]

Další informace najdete v tématu [streamování Hadoop](http://hadoop.apache.org/docs/r1.2.1/streaming.html).

Příklady použití streamování s HDInsight Hadoop naleznete v následujících dokumentech:

* [Vývoj úloh MapReduce C#](apache-hadoop-dotnet-csharp-mapreduce-streaming.md)

* [Vývoj úloh MapReduce Pythonu](apache-hadoop-streaming-python.md)

## <a id="data"></a>Ukázková data

HDInsight poskytuje různé příklad datové sady, které jsou uloženy v `/example/data` a `/HdiSamples` adresáře. Tyto adresáře jsou ve výchozím nastavení úložiště pro váš cluster. V tomto dokumentu budeme používat `/example/data/gutenberg/davinci.txt` souboru. Tento soubor obsahuje poznámkových bloků Leonardo Da Vinci.

## <a id="job"></a>Příklad MapReduce

Příklad MapReduce dokumentů aplikace word count je součástí vašeho clusteru HDInsight. V tomto příkladu se nachází na `/example/jars/hadoop-mapreduce-examples.jar` na výchozí úložiště pro váš cluster.

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

Pokyny pro psaní vlastních aplikací MapReduce naleznete v následujících dokumentech:

* [Vývoj aplikací Java MapReduce pro HDInsight](apache-hadoop-develop-deploy-java-mapreduce-linux.md)

* [Vývoj aplikace Python MapReduce pro HDInsight](apache-hadoop-streaming-python.md)

## <a id="run"></a>Spustit MapReduce

HDInsight můžete spouštět úlohy HiveQL pomocí různých metod. Použijte následující tabulku k rozhodnutí, která metoda je pro vás nejvhodnější a potom na odkaz pro návod.

| **Použít**... | **...a tomu** | ...při to **clusteru operačního systému** | ...from to **klientský operační systém** |
|:--- |:--- |:--- |:--- |
| [SSH](apache-hadoop-use-mapreduce-ssh.md) |Použití příkazu Hadoop prostřednictvím **SSH** |Linux |Linux, Unix, Mac OS X a Windows |
| [Curl](apache-hadoop-use-mapreduce-curl.md) |Odeslání úlohy vzdáleně pomocí **REST** |Linux nebo Windows |Linux, Unix, Mac OS X a Windows |
| [Windows PowerShell](apache-hadoop-use-mapreduce-powershell.md) |Odeslání úlohy vzdáleně pomocí **prostředí Windows PowerShell** |Linux nebo Windows |Windows |

> [!IMPORTANT]
> HDInsight od verze 3.4 výše používá výhradně operační systém Linux. Další informace najdete v tématu [Vyřazení prostředí HDInsight ve Windows](../hdinsight-component-versioning.md#hdinsight-windows-retirement).

## <a id="nextsteps"></a>Další kroky

Další informace o práci s daty v HDInsight, najdete v následujících dokumentech:

* [Vývoj programů Java MapReduce pro HDInsight](apache-hadoop-develop-deploy-java-mapreduce-linux.md)

* [Vývoj programů MapReduce se streamováním pro HDInsight v Pythonu](apache-hadoop-streaming-python.md)

* [Použití Hivu se službou HDInsight][hdinsight-use-hive]

* [Použití Pigu se službou HDInsight][hdinsight-use-pig]


[hdinsight-upload-data]: hdinsight-upload-data.md
[hdinsight-get-started]:apache-hadoop-linux-tutorial-get-started.md
[hdinsight-develop-mapreduce-jobs]: apache-hadoop-develop-deploy-java-mapreduce-linux.md
[hdinsight-use-hive]:../hdinsight-use-hive.md
[hdinsight-use-pig]:hdinsight-use-pig.md


[powershell-install-configure]: /powershell/azureps-cmdlets-docs

[image-hdi-wordcountdiagram]: ./media/hdinsight-use-mapreduce/HDI.WordCountDiagram.gif
