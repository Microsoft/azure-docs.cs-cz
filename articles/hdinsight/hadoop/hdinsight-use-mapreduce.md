---
title: MapReduce s Apache Hadoop na HDInsight
description: Přečtěte si, jak spustit úlohy Apache MapReduce na Apache Hadoop v clusterech HDInsight.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.custom: hdinsightactive
ms.date: 12/06/2019
ms.openlocfilehash: 238f37cc1e8cbc8d3260693181d6e54ac5f592f7
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "75435709"
---
# <a name="use-mapreduce-in-apache-hadoop-on-hdinsight"></a>Použití MapReduce v Apache Hadoopu ve službě HDInsight

Přečtěte si, jak spustit úlohy MapReduce v clusterech HDInsight.

## <a name="example-data"></a>Příklad dat

HDInsight poskytuje různé ukázkové datové sady, které jsou uloženy v adresáři `/example/data` a. `/HdiSamples` Tyto adresáře jsou ve výchozím úložišti pro váš cluster. V tomto dokumentu použijeme `/example/data/gutenberg/davinci.txt` soubor. Tento soubor obsahuje zápisníky Leonarda da Vinciho.

## <a name="example-mapreduce"></a>Příklad MapReduce

Příklad aplikace MapReduce počet slov je součástí clusteru HDInsight. Tento příklad je `/example/jars/hadoop-mapreduce-examples.jar` umístěn na výchozí úložiště pro váš cluster.

Následující kód Jazyka Java je zdrojem aplikace MapReduce obsažené v souboru: `hadoop-mapreduce-examples.jar`

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

Pokyny k psaní vlastních aplikací MapReduce naleznete v [tématu Vývoj aplikací Java MapReduce pro HDInsight](apache-hadoop-develop-deploy-java-mapreduce-linux.md).

## <a name="run-the-mapreduce"></a>Spuštění mapového omezení

HDInsight můžete spustit hiveql úlohy pomocí různých metod. V následující tabulce se můžete rozhodnout, která metoda je pro vás vhodná, a potom postupujte podle odkazu na návod.

| **Použijte tento**... | **... k tomu** |  ... z tohoto **klientského operačního systému** |
|:--- |:--- |:--- |:--- |
| [Ssh](apache-hadoop-use-mapreduce-ssh.md) |Použití příkazu Hadoop přes **SSH** |Linux, Unix, Mac OS X nebo Windows |
| [Curl](apache-hadoop-use-mapreduce-curl.md) |Odeslání úlohy na dálku pomocí **rest** |Linux, Unix, Mac OS X nebo Windows |
| [Windows PowerShell](apache-hadoop-use-mapreduce-powershell.md) |Vzdáleně odeslat úlohu pomocí **prostředí Windows PowerShell**  |Windows |

## <a name="next-steps"></a>Další kroky

Další informace o práci s daty ve službě HDInsight najdete v následujících dokumentech:

* [Vývoj programů Java MapReduce pro HDInsight](apache-hadoop-develop-deploy-java-mapreduce-linux.md)

* [Použití Apache Hive s HDInsight](./hdinsight-use-hive.md)
