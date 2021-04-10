---
title: MapReduce s Apache Hadoop v HDInsight
description: Naučte se spouštět úlohy Apache MapReduce na Apache Hadoop v clusterech HDInsight.
ms.service: hdinsight
ms.topic: how-to
ms.custom: hdinsightactive
ms.date: 12/06/2019
ms.openlocfilehash: 33d96262ca2e2ff3003fbf2b40ce9ceb496337ae
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/29/2021
ms.locfileid: "98944250"
---
# <a name="use-mapreduce-in-apache-hadoop-on-hdinsight"></a>Použití MapReduce v Apache Hadoopu ve službě HDInsight

Naučte se spouštět úlohy MapReduce v clusterech HDInsight.

## <a name="example-data"></a>Příklad dat

HDInsight poskytuje různé příklady datových sad, které jsou uložené v `/example/data` adresáři a `/HdiSamples` . Tyto adresáře jsou ve výchozím úložišti pro váš cluster. V tomto dokumentu používáme `/example/data/gutenberg/davinci.txt` soubor. Tento soubor obsahuje poznámkové bloky Leonardo da Vinci.

## <a name="example-mapreduce"></a>Příklad MapReduce

Cluster HDInsight obsahuje příklad aplikace MapReduce počet slov. Tento příklad se nachází ve `/example/jars/hadoop-mapreduce-examples.jar` výchozím úložišti pro váš cluster.

Následující kód Java je zdrojem aplikace MapReduce obsažené v `hadoop-mapreduce-examples.jar` souboru:

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

Pokyny k psaní vlastních aplikací MapReduce najdete v tématu [vývoj aplikací Java MapReduce pro HDInsight](apache-hadoop-develop-deploy-java-mapreduce-linux.md).

## <a name="run-the-mapreduce"></a>Spusťte MapReduce.

HDInsight může spouštět úlohy HiveQL pomocí různých metod. Pomocí následující tabulky se rozhodněte, která metoda je pro vás nejvhodnější, a pak použijte odkaz na návod.

| **Použít**... | **... k tomu** |  ... z tohoto **klientského operačního systému** |
|:--- |:--- |:--- |:--- |
| [SSH](apache-hadoop-use-mapreduce-ssh.md) |Použití příkazu Hadoop přes **SSH** |Linux, UNIX, Mac OS X nebo Windows |
| [Curl](apache-hadoop-use-mapreduce-curl.md) |Vzdálenou úlohu odešlete pomocí **REST** . |Linux, UNIX, Mac OS X nebo Windows |
| [Windows PowerShell](apache-hadoop-use-mapreduce-powershell.md) |Vzdálená úloha se odešle pomocí **Windows PowerShellu** .  |Windows |

## <a name="next-steps"></a>Další kroky

Další informace o práci s daty v HDInsight najdete v následujících dokumentech:

* [Vývoj programů Java MapReduce pro HDInsight](apache-hadoop-develop-deploy-java-mapreduce-linux.md)

* [Použití Apache Hive se službou HDInsight](./hdinsight-use-hive.md)
