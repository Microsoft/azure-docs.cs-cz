---
title: Spustit ukázky Hadoopu do HDInsight – Azure
description: Začněte používat službu Azure HDInsight s ukázkami, které jsou k dispozici. Pomocí skriptů PowerShell, které poběží na clusterech s data programů MapReduce.
services: hdinsight
author: jasonwhowell
editor: jasonwhowell
ms.service: hdinsight
ms.topic: conceptual
ms.date: 05/25/2017
ms.author: jasonh
ROBOTS: NOINDEX
ms.openlocfilehash: ef88e1d3e165e3ae21b235a33b295b51b574ff67
ms.sourcegitcommit: 1f0587f29dc1e5aef1502f4f15d5a2079d7683e9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/07/2018
ms.locfileid: "39593280"
---
# <a name="run-hadoop-mapreduce-samples-in-windows-based-hdinsight"></a>Ukázky spouštění Hadoop MapReduce v HDInsight se systémem Windows
[!INCLUDE [samples-selector](../../includes/hdinsight-run-samples-selector.md)]

Které vám pomohou začít spuštěných úloh MapReduce v clusterech Hadoop pomocí Azure HDInsight jsou k dispozici řadu ukázek. Tyto ukázky jsou k dispozici na všech HDInsight spravované clustery, které vytvoříte. Spuštění ukázek vám seznámit se s použitím rutin prostředí Azure PowerShell můžete spouštět úlohy na clusterech Hadoop.

* [**Word count**][hdinsight-sample-wordcount]: vypočítá frekvenci výskytu slov v textovém souboru.
* [**Počet slov streamováním v C#**][hdinsight-sample-csharp-streaming]: vypočítá frekvenci výskytu slov v textovém souboru pomocí rozhraní streamování Hadoop.
* [**Odhadu hodnoty pí**][hdinsight-sample-pi-estimator]: používá statistického (dál Monte Carlo) způsob, jak odhadnout hodnotu čísla pí.
* [**10 GB Graysort**][hdinsight-sample-10gb-graysort]: spusťte pro obecné účely GraySort na 10 GB souboru pomocí HDInsight. Existují tři úlohy pro spuštění: Teragen generují data, Terasort řadit data a Teravalidate potvrďte správně seřazená data.

> [!NOTE]
> Zdrojový kód můžete najít v dodatku.

Existuje dodatečných dokumentaci na webu pro technologie související s Hadoop, jako je například programování založené na jazyce Java MapReduce a datových proudů a dokumentaci o rutinách, které se používají v prostředí Windows PowerShell skriptování. Další informace o těchto prostředků najdete v tématu:

* [Vývoj programů Java MapReduce pro Hadoop v HDInsight](hadoop/apache-hadoop-develop-deploy-java-mapreduce-linux.md)
* [Odesílání úloh Hadoop do služby HDInsight](hadoop/submit-apache-hadoop-jobs-programmatically.md)
* [Úvod do služby Azure HDInsight][hdinsight-introduction]

V současné době mnoho lidí zvolte Hive a Pig přes MapReduce.  Další informace naleznete v tématu:

* [Použití Hivu ve službě HDInsight](hadoop/hdinsight-use-hive.md)
* [Použití Pigu se v HDInsight](hadoop/hdinsight-use-pig.md)

**Požadavky**:

* **Předplatné Azure**. Viz [Získání bezplatné zkušební verze Azure](https://azure.microsoft.com/documentation/videos/get-azure-free-trial-for-testing-hadoop-in-hdinsight/).
* **HDInsight cluster**. Různé způsoby, ve kterých je možné vytvořit clustery tohoto typu, v tématu [vytváření clusterů Hadoop v HDInsight](hdinsight-hadoop-provision-linux-clusters.md).
* **Pracovní stanice s prostředím Azure PowerShell**.

    > [!IMPORTANT]
    > Podpora prostředí Azure PowerShell pro správu prostředků služby HDInsight pomocí Azure Service Manageru je **zastaralá** a 1. ledna 2017 dojde k jejímu odebrání. Kroky v tomto dokumentu používají nové rutiny služby HDInsight, které pracují s Azure Resource Managerem.
    >
    > Postupujte podle kroků v [instalace a konfigurace Azure Powershellu](/powershell/azureps-cmdlets-docs) nainstalovat nejnovější verzi Azure Powershellu. Pokud máte skripty, které je potřeba upravit tak, aby používají nové rutiny, které pracují s Azure Resource Manageru, najdete v [migrace do nástroje pro vývoj založených na Azure Resource Manageru pro clustery HDInsight](hdinsight-hadoop-development-using-azure-resource-manager.md).

## <a name="hdinsight-sample-wordcount"></a>Word počet – Java
K odeslání projektu MapReduce, nejprve vytvoříte definici úlohy MapReduce. V definici úlohy určete soubor jar program MapReduce a umístění souboru jar, který je **wasb:///example/jars/hadoop-mapreduce-examples.jar**, název třídy a argumentů.  Wordcount MapReduce program přebírá dva argumenty: zdrojový soubor, který slouží ke zjištění počtu slov a umístění pro výstup.

Zdrojový kód najdete v [příloha A](#apendix-a---the-word-count-MapReduce-program-in-java).

Postup vývoje MapReduce v Javě programu, najdete v článku – [programů vývoj Java MapReduce pro Hadoop v HDInsight](hadoop/apache-hadoop-develop-deploy-java-mapreduce-linux.md)

**Odeslat úlohu MapReduce počet slov**

1. Otevřít **Windows PowerShell ISE**. Pokyny najdete v tématu [nainstalovat a nakonfigurovat Azure PowerShell][powershell-install-configure].
2. Vložte následující skript prostředí PowerShell:

    ```powershell
    $subscriptionName = "<Azure Subscription Name>"
    $resourceGroupName = "<Resource Group Name>"
    $clusterName = "<HDInsight cluster name>"             # HDInsight cluster name

    Select-AzureRmSubscription -SubscriptionName $subscriptionName

    # Define the MapReduce job
    $mrJobDefinition = New-AzureRmHDInsightMapReduceJobDefinition `
                                -JarFile "wasb:///example/jars/hadoop-mapreduce-examples.jar" `
                                -ClassName "wordcount" `
                                -Arguments "wasb:///example/data/gutenberg/davinci.txt", "wasb:///example/data/WordCountOutput"

    # Submit the job and wait for job completion
    $cred = Get-Credential -Message "Enter the HDInsight cluster HTTP user credential:"
    $mrJob = Start-AzureRmHDInsightJob `
                        -ResourceGroupName $resourceGroupName `
                        -ClusterName $clusterName `
                        -HttpCredential $cred `
                        -JobDefinition $mrJobDefinition

    Wait-AzureRmHDInsightJob `
        -ResourceGroupName $resourceGroupName `
        -ClusterName $clusterName `
        -HttpCredential $cred `
        -JobId $mrJob.JobId

    # Get the job output
    $cluster = Get-AzureRmHDInsightCluster -ResourceGroupName $resourceGroupName -ClusterName $clusterName
    $defaultStorageAccount = $cluster.DefaultStorageAccount -replace '.blob.core.windows.net'
    $defaultStorageAccountKey = (Get-AzureRmStorageAccountKey -ResourceGroupName $resourceGroupName -Name $defaultStorageAccount)[0].Value
    $defaultStorageContainer = $cluster.DefaultStorageContainer

    Get-AzureRmHDInsightJobOutput `
        -ResourceGroupName $resourceGroupName `
        -ClusterName $clusterName `
        -HttpCredential $cred `
        -DefaultStorageAccountName $defaultStorageAccount `
        -DefaultStorageAccountKey $defaultStorageAccountKey `
        -DefaultContainer $defaultStorageContainer  `
        -JobId $mrJob.JobId `
        -DisplayOutputType StandardError

    # Download the job output to the workstation
    $storageContext = New-AzureStorageContext -StorageAccountName $defaultStorageAccount -StorageAccountKey $defaultStorageAccountKey
    Get-AzureStorageBlobContent -Container $defaultStorageContainer -Blob example/data/WordCountOutput/part-r-00000 -Context $storageContext -Force

    # Display the output file
    cat ./example/data/WordCountOutput/part-r-00000 | findstr "there"
    ```

    Úlohy MapReduce vytvoří soubor s názvem *část r-00000*, který obsahuje slova a počty. Tento skript využívá **findstr** příkazu zobrazte výpis všech slov, která obsahuje *"obsahuje"*.
3. Nastavení prvních tří proměnných a spusťte skript.

## <a name="hdinsight-sample-csharp-streaming"></a>Word count - streamováním v C#
Hadoop MapReduce, která umožňuje zápis mapy a omezit funkce v jiných jazycích než Java poskytuje rozhraní API pro streamování.

> [!NOTE]
> Postup v tomto kurzu se vztahuje pouze na clustery HDInsight se systémem Windows. Příklad streamování pro clustery HDInsight založené na Linuxu najdete v tématu [vývoj streamovacích programů v Pythonu pro HDInsight](hadoop/apache-hadoop-streaming-python.md).

V příkladu mapper a reduktorem jsou spustitelné soubory, které číst vstupu z [stdin –][stdin-stdout-stderr] (řádek po řádku) a výstup do [stdout][stdin-stdout-stderr]. Program Spočítá slova v textu.

Pokud je zadaný spustitelný soubor pro **mapovače**, každý úkol Mapovač při inicializaci Mapovač spustí spustitelný soubor jako samostatný proces. Mapovač úloha běží, převede vstup na řádky a řádky, které se předají [stdin] [ stdin-stdout-stderr] procesu.

Do té doby Mapovač shromažďuje výstup orientovaný na řádku z výstupu stdout procesu. Každý řádek převede do dvojici klíč/hodnota, která se shromažďuje jako výstup Mapovač. Ve výchozím nastavení Předpona řádku až po první znak tabulátoru je klíč a zbytek řádku (s výjimkou znak tabulátoru) je hodnota. Pokud neexistuje žádný znak tabulátoru na řádku, celý řádek se považují za klíč a hodnotu null.

Pokud je zadaný spustitelný soubor pro **reduktorů**, každý úkol redukční funkci spouští spustitelný soubor jako samostatný proces, když je inicializován redukční funkci. Při spuštění úlohy redukční funkci, převede jeho dvojice klíč/hodnota vstupu na řádky a kanály řádky, které se [stdin] [ stdin-stdout-stderr] procesu.

Do té doby redukční funkci shromažďuje výstup řádkový [stdout] [ stdin-stdout-stderr] procesu. Převede každý řádek dvojice klíč/hodnota, která se shromažďuje jako výstup redukční funkci. Ve výchozím nastavení Předpona řádku až po první znak tabulátoru je klíč a zbytek řádku (s výjimkou znak tabulátoru) je hodnota.

**Odeslání aplikace word count úloha streamování jazyka C#**

* Postupujte podle pokynů v [Word počet – Java](#word-count-java)a nahraďte definici úlohy následující řádek:

    ```powershell
    $mrJobDefinition = New-AzureRmHDInsightStreamingMapReduceJobDefinition `
                            -Files "/example/apps/cat.exe","/example/apps/wc.exe" `
                            -Mapper "cat.exe" `
                            -Reducer "wc.exe" `
                            -InputPath "/example/data/gutenberg/davinci.txt" `
                            -OutputPath "/example/data/StreamingOutput/wc.txt"
    ```

    Výstupní soubor musí být:

        example/data/StreamingOutput/wc.txt/part-00000

## <a name="hdinsight-sample-pi-estimator"></a>Odhadu hodnoty PÍ
Používá statistického odhadu hodnoty pí (dál Monte Carlo) způsob, jak odhadnout hodnotu čísla pí. Body náhodně umístit uvnitř jednotka čtverec také spadal do kruh zapsány v rámci této čtverec se pravděpodobnost rovná obsah kruhu, pi nebo 4. Hodnotu pí můžete odhadnout z hodnoty 4R, kde R je poměr počtu bodů, které jsou uvnitř kroužku celkového počtu bodů, které jsou v rámci buňky. Čím větší vzorek bodů použít, je tím lepší odhadu.

Skript uvedený pro tuto ukázku odešle soubor jar úlohy Hadoopu a nastavit až do spuštění s hodnotou 16 mapy, z nichž každý je požadované pro 10 milionů vzorových bodů podle hodnoty parametrů. Tyto hodnoty parametrů můžete změnit na zlepšení odhadovanou hodnotu čísla pí. Pro srovnání je prvních 10 desetinná čísla pí 3.1415926535.

**Odeslání úlohy estimator pi**

* Postupujte podle pokynů v [Word počet – Java](#word-count-java)a nahraďte definici úlohy následující řádek:

    ```powershell
    $mrJobJobDefinition = New-AzureRmHDInsightMapReduceJobDefinition `
                                -JarFile "wasb:///example/jars/hadoop-mapreduce-examples.jar" `
                                -ClassName "pi" `
                                -Arguments "16", "10000000"
    ```

## <a name="hdinsight-sample-10gb-graysort"></a>Graysort 10 GB
Tato ukázka používá mírné 10GB dat, aby mohla být poměrně rychle spustit. Používá MapReduce aplikace vyvinuté tak, že Owen O'Malley a Arun Murthy získané roční kritérium řazení terabajt pro obecné účely ("daytona") v roce 2009 s mírou 0.578 TB za minutu (100 TB za 173 minut). Další informace o tomto a dalších řazení srovnávací testy, najdete v článku [Sortbenchmark](http://sortbenchmark.org/) lokality.

Tato ukázka používá tři páry programů MapReduce:

1. **TeraGen** je program MapReduce, který můžete použít k vygenerování řádků dat. Chcete-li seřadit.
2. **TeraSort** ukázkové vstupní data a řadit data do celkového pořadí pomocí MapReduce. TeraSort je standardní druh funkce MapReduce, s výjimkou vlastního rozdělovače, který používá seřazený seznam N-1 vzorkovány klíče, které definovat rozsah klíče pro každý zmenšit. Zejména všechny klíče takové, které ukázkový [i-1] < = klíč < ukázka [i] odešlou ke snížení i. To zaručuje, že výstupy snížit i všechny jsou menší, než se výstup snížit i + 1.
3. **TeraValidate** je program MapReduce, která ověřuje, že výstup globálně seřazené. Vytvoří jednu mapu každý soubor ve výstupním adresáři a každé mapování zajistí, že každý klíč je menší nebo rovna předchozí. Mapování funkce také vygeneruje záznamy první a poslední klíče každého souboru a funkci reduce zajišťuje, že je větší než poslední klíč souboru i-1 první klíč souboru. Problémy označené jako výstup snížit pomocí klíčů, které jsou mimo pořadí.

Vstupní a výstupní formát, používá všechny tři aplikace čte a zapisuje textových souborů ve správném formátu. Výstup snížit má nastavena na hodnotu 1, místo výchozího 3, replikace, protože soutěže srovnávací test nevyžaduje, že výstupní data replikovat do více uzlů.

Ukázka každý odpovídá jedné z programů MapReduce popsané v úvodu vyžaduje tři úkoly:

1. Generování dat pro řazení spuštěním **TeraGen** úlohu MapReduce.
2. Řazení dat spuštěním **TeraSort** úlohu MapReduce.
3. Potvrďte, že data správně seřazené spuštěním **TeraValidate** úlohu MapReduce.

**Odeslání úlohy**

* Postupujte podle pokynů v [Word počet – Java](#word-count-java)a použijte následující definice úloh:

    ```powershell
    $teragen = New-AzureRmHDInsightMapReduceJobDefinition `
                                -JarFile "/example/jars/hadoop-mapreduce-examples.jar" `
                                -ClassName "teragen" `
                                -Arguments "-Dmapred.map.tasks=50", "100000000", "/example/data/10GB-sort-input"

    $terasort = New-AzureRmHDInsightMapReduceJobDefinition `
                                -JarFile "/example/jars/hadoop-mapreduce-examples.jar" `
                                -ClassName "terasort" `
                                -Arguments "-Dmapred.map.tasks=50", "-Dmapred.reduce.tasks=25", "/example/data/10GB-sort-input", "/example/data/10GB-sort-output"

    $teravalidate = New-AzureRmHDInsightMapReduceJobDefinition `
                                -JarFile "/example/jars/hadoop-mapreduce-examples.jar" `
                                -ClassName "teravalidate" `
                                -Arguments "-Dmapred.map.tasks=50", "-Dmapred.reduce.tasks=25", "/example/data/10GB-sort-output", "/example/data/10GB-sort-validate"
    ```

## <a name="next-steps"></a>Další postup
Z tohoto článku a články v každém z ukázek jste zjistili, jak ke spuštění ukázky, které jsou součástí clusterů HDInsight pomocí Azure Powershellu. Kurzy o používání Pig, Hive a MapReduce s HDInsight najdete v následujících tématech:

* [Začínáme používat Hadoop pomocí Hive v HDInsight k analýze pomocí mobilního telefonu][hdinsight-get-started]
* [Použití Pigu se systémem Hadoop v HDInsight][hdinsight-use-pig]
* [Použití Hivu s Hadoopem v HDInsight][hdinsight-use-hive]
* [Odesílání úloh Hadoop v HDInsight][hdinsight-submit-jobs]
* [Dokumentace ke službě Azure HDInsight SDK][hdinsight-sdk-documentation]

## <a name="appendix-a---the-word-count-source-code"></a>Příloha A – zdrojový kód počet slov

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

## <a name="appendix-b---the-word-count-streaming-source-code"></a>Příloha B – počet slov streamování zdrojového kódu
MapReduce program cat.exe aplikace používá jako mapování rozhraní ke streamování text do konzoly a wc.exe aplikace jako rozhraní snižte počet slov, která se streamují z dokumentu. V mapovací a redukční funkci čtení znaků, řádek po řádku, ze standardního vstupního proudu (stdin) a zápis do datového proudu standardního výstupu (stdout).

```csharp
// The source code for the cat.exe (Mapper).

using System;
using System.IO;

namespace cat
{
    class cat
    {
        static void Main(string[] args)
        {
            if (args.Length > 0)
            {
                Console.SetIn(new StreamReader(args[0]));
            }

            string line;
            char[] separators = { ' ', '\n'};
            while ((line = Console.ReadLine()) != null)
            {
                string[] words = line.Split(separators);
                foreach (var word in words)
                {
                    Console.WriteLine("{0}\t1", word);
                }
            }
        }
    }
}
```

Mapovač kód v souboru cat.cs používá [StreamReader] [ streamreader] objektu určeného ke čtení příchozího datového proudu do konzoly, který poté provede zápis datového proudu do standardního výstupního datového proudu se statickými znaky[ Console.Writeline] [ console-writeline] metody.

```csharp
// The source code for wc.exe (Reducer) is:

using System;
using System.IO;
using System.Linq;
using System.Collections;

namespace wc
{
    class wc
    {
        static void Main(string[] args)
        {
            string line;

            if (args.Length > 0)
            {
                Console.SetIn(new StreamReader(args[0]));
            }

            Hashtable wordCount = new Hashtable();
            while ((line = Console.ReadLine()) != null)
            {
                string[] words = line.Split('\t');

                string key = words[0];

                if (wordCount.ContainsKey(key) == true)
                {
                    int n = Convert.ToInt32(wordCount[key]);
                    wordCount[key] = Convert.ToString(n + 1);
                }
                else
                {
                    wordCount[key] = words[1];
                }
            }
            foreach (var key in wordCount.Keys)
            {
                Console.WriteLine("{0} {1}", key, wordCount[key]);
            }
        }
    }
}
```

Redukční funkci kód v souboru wc.cs používá [StreamReader] [ streamreader] objektu určeného ke čtení znaků ze standardního vstupního proudu, který byl výstupem Mapovač cat.exe. Čte znaky s [Console.Writeline] [ console-writeline] metody Spočítá slova určeno spočítáním mezer a znaků na konci řádku na konci každého slova. Pak zapíše do standardního výstupního datového proudu s celkovou [Console.Writeline] [ console-writeline] metody.

## <a name="appendix-c---the-pi-estimator-source-code"></a>Příloha C – Pi estimator zdrojového kódu
Odhadu hodnoty pí kódu Java, který obsahuje mapovací a redukční funkce je k dispozici pro kontrolu níže. Mapovač program vygeneruje určený počet bodů náhodně umístit uvnitř hranatých jednotky a pak vypočítá počet těchto bodů, které jsou uvnitř kroužku. Program redukční funkci nahromadí body spočítaných podle mapovačů a pak odhadne hodnotu čísla pí ze vzorců 4R, kde R je poměr počtu bodů počítá uvnitř kroužku celkového počtu bodů, které jsou v rámci buňky.

```java
/**
* Licensed to the Apache Software Foundation (ASF) under one
* or more contributor license agreements. See the NOTICE file
* distributed with this work for additional information
* regarding copyright ownership. The ASF licenses this file
* to you under the Apache License, Version 2.0 (the
* "License"); you may not use this file except in compliance
* with the License. You may obtain a copy of the License at
*
* http://www.apache.org/licenses/LICENSE-2.0
*
* Unless required by applicable law or agreed to in writing, software
* distributed under the License is distributed on an "AS IS" BASIS,
* WITHOUT WARRANTIES OR CONDITIONS OF ANY KIND, either express or     implied.
* See the License for the specific language governing permissions and
* limitations under the License.
*/

package org.apache.hadoop.examples;

import java.io.IOException;
import java.math.BigDecimal;
import java.util.Iterator;

import org.apache.hadoop.conf.Configured;
import org.apache.hadoop.fs.FileSystem;
import org.apache.hadoop.fs.Path;
import org.apache.hadoop.io.BooleanWritable;
import org.apache.hadoop.io.LongWritable;
import org.apache.hadoop.io.SequenceFile;
import org.apache.hadoop.io.Writable;
import org.apache.hadoop.io.WritableComparable;
import org.apache.hadoop.io.SequenceFile.CompressionType;
import org.apache.hadoop.mapred.FileInputFormat;
import org.apache.hadoop.mapred.FileOutputFormat;
import org.apache.hadoop.mapred.JobClient;
import org.apache.hadoop.mapred.JobConf;
import org.apache.hadoop.mapred.MapReduceBase;
import org.apache.hadoop.mapred.Mapper;
import org.apache.hadoop.mapred.OutputCollector;
import org.apache.hadoop.mapred.Reducer;
import org.apache.hadoop.mapred.Reporter;
import org.apache.hadoop.mapred.SequenceFileInputFormat;
import org.apache.hadoop.mapred.SequenceFileOutputFormat;
import org.apache.hadoop.util.Tool;
import org.apache.hadoop.util.ToolRunner;

//A Map-reduce program to estimate the value of Pi
//using quasi-Monte Carlo method.
//
//Mapper:
//Generate points in a unit square
//and then count points inside/outside of the inscribed circle of the square.
//
//Reducer:
//Accumulate points inside/outside results from the mappers.
//Let numTotal = numInside + numOutside.
//The fraction numInside/numTotal is a rational approximation of
//the value (Area of the circle)/(Area of the square),
//where the area of the inscribed circle is Pi/4
//and the area of unit square is 1.
//Then, Pi is estimated value to be 4(numInside/numTotal).
//

public class PiEstimator extends Configured implements Tool {
//tmp directory for input/output
static private final Path TMP_DIR = new Path(
PiEstimator.class.getSimpleName() + "_TMP_3_141592654");

//2-dimensional Halton sequence {H(i)},
//where H(i) is a 2-dimensional point and i >= 1 is the index.
//Halton sequence is used to generate sample points for Pi estimation.
private static class HaltonSequence {
// Bases
static final int[] P = {2, 3};
//Maximum number of digits allowed
static final int[] K = {63, 40};

private long index;
private double[] x;
private double[][] q;
private int[][] d;

//Initialize to H(startindex),
//so the sequence begins with H(startindex+1).
HaltonSequence(long startindex) {
index = startindex;
x = new double[K.length];
q = new double[K.length][];
d = new int[K.length][];
for(int i = 0; i < K.length; i++) {
q[i] = new double[K[i]];
d[i] = new int[K[i]];
}

for(int i = 0; i < K.length; i++) {
long k = index;
x[i] = 0;

for(int j = 0; j < K[i]; j++) {
q[i][j] = (j == 0? 1.0: q[i][j-1])/P[i];
d[i][j] = (int)(k % P[i]);
k = (k - d[i][j])/P[i];
x[i] += d[i][j] * q[i][j];
}
}
}

//Compute next point.
//Assume the current point is H(index).
//Compute H(index+1).
//@return a 2-dimensional point with coordinates in [0,1)^2
double[] nextPoint() {
index++;
for(int i = 0; i < K.length; i++) {
for(int j = 0; j < K[i]; j++) {
d[i][j]++;
x[i] += q[i][j];
if (d[i][j] < P[i]) {
break;
}
d[i][j] = 0;
x[i] -= (j == 0? 1.0: q[i][j-1]);
}
}
return x;
}
}

//Mapper class for Pi estimation.
//Generate points in a unit square and then
//count points inside/outside of the inscribed circle of the square.
public static class PiMapper extends MapReduceBase
implements Mapper<LongWritable, LongWritable, BooleanWritable, LongWritable> {

//Map method.
//@param offset samples starting from the (offset+1)th sample.
//@param size the number of samples for this map
//@param out output {true->numInside, false->numOutside}
//@param reporter
public void map(LongWritable offset,
LongWritable size,
OutputCollector<BooleanWritable, LongWritable> out,
Reporter reporter) throws IOException {

final HaltonSequence haltonsequence = new HaltonSequence(offset.get());
long numInside = 0L;
long numOutside = 0L;

for(long i = 0; i < size.get(); ) {
//generate points in a unit square
final double[] point = haltonsequence.nextPoint();

//count points inside/outside of the inscribed circle of the square
final double x = point[0] - 0.5;
final double y = point[1] - 0.5;
if (x*x + y*y > 0.25) {
numOutside++;
} else {
numInside++;
}

//report status
i++;
if (i % 1000 == 0) {
reporter.setStatus("Generated " + i + " samples.");
}
}

//output map results
out.collect(new BooleanWritable(true), new LongWritable(numInside));
out.collect(new BooleanWritable(false), new LongWritable(numOutside));
}
}

//Reducer class for Pi estimation.
//Accumulate points inside/outside results from the mappers.
public static class PiReducer extends MapReduceBase
implements Reducer<BooleanWritable, LongWritable, WritableComparable<?>, Writable> {

private long numInside = 0;
private long numOutside = 0;
private JobConf conf; //configuration for accessing the file system

//Store job configuration.
@Override
public void configure(JobConf job) {
conf = job;
}

// Accumulate number of points inside/outside results from the mappers.
// @param isInside Is the points inside?
// @param values An iterator to a list of point counts
// @param output dummy, not used here.
// @param reporter

public void reduce(BooleanWritable isInside,
Iterator<LongWritable> values,
OutputCollector<WritableComparable<?>, Writable> output,
Reporter reporter) throws IOException {
if (isInside.get()) {
for(; values.hasNext(); numInside += values.next().get());
} else {
for(; values.hasNext(); numOutside += values.next().get());
}
}

//Reduce task done, write output to a file.
@Override
public void close() throws IOException {
//write output to a file
Path outDir = new Path(TMP_DIR, "out");
Path outFile = new Path(outDir, "reduce-out");
FileSystem fileSys = FileSystem.get(conf);
SequenceFile.Writer writer = SequenceFile.createWriter(fileSys, conf,
outFile, LongWritable.class, LongWritable.class,
CompressionType.NONE);
writer.append(new LongWritable(numInside), new LongWritable(numOutside));
writer.close();
}
}

//Run a map/reduce job for estimating Pi.
//@return the estimated value of Pi.
public static BigDecimal estimate(int numMaps, long numPoints, JobConf jobConf
)
throws IOException {
//setup job conf
jobConf.setJobName(PiEstimator.class.getSimpleName());

jobConf.setInputFormat(SequenceFileInputFormat.class);

jobConf.setOutputKeyClass(BooleanWritable.class);
jobConf.setOutputValueClass(LongWritable.class);
jobConf.setOutputFormat(SequenceFileOutputFormat.class);

jobConf.setMapperClass(PiMapper.class);
jobConf.setNumMapTasks(numMaps);

jobConf.setReducerClass(PiReducer.class);
jobConf.setNumReduceTasks(1);

// turn off speculative execution, because DFS doesn't handle
// multiple writers to the same file.
jobConf.setSpeculativeExecution(false);

//setup input/output directories
final Path inDir = new Path(TMP_DIR, "in");
final Path outDir = new Path(TMP_DIR, "out");
FileInputFormat.setInputPaths(jobConf, inDir);
FileOutputFormat.setOutputPath(jobConf, outDir);

final FileSystem fs = FileSystem.get(jobConf);
if (fs.exists(TMP_DIR)) {
throw new IOException("Tmp directory " + fs.makeQualified(TMP_DIR)
+ " already exists. Remove it first.");
}
if (!fs.mkdirs(inDir)) {
throw new IOException("Cannot create input directory " + inDir);
}

//generate an input file for each map task
try {
for(int i=0; i < numMaps; ++i) {
final Path file = new Path(inDir, "part"+i);
final LongWritable offset = new LongWritable(i * numPoints);
final LongWritable size = new LongWritable(numPoints);
final SequenceFile.Writer writer = SequenceFile.createWriter(
fs, jobConf, file,
LongWritable.class, LongWritable.class, CompressionType.NONE);
try {
writer.append(offset, size);
} finally {
writer.close();
}
System.out.println("Wrote input for Map #"+i);
}

//start a map/reduce job
System.out.println("Starting Job");
final long startTime = System.currentTimeMillis();
JobClient.runJob(jobConf);
final double duration = (System.currentTimeMillis() - startTime)/1000.0;
System.out.println("Job Finished in " + duration + " seconds");

//read outputs
Path inFile = new Path(outDir, "reduce-out");
LongWritable numInside = new LongWritable();
LongWritable numOutside = new LongWritable();
SequenceFile.Reader reader = new SequenceFile.Reader(fs, inFile, jobConf);
try {
reader.next(numInside, numOutside);
} finally {
reader.close();
}

//compute estimated value
return BigDecimal.valueOf(4).setScale(20)
.multiply(BigDecimal.valueOf(numInside.get()))
.divide(BigDecimal.valueOf(numMaps))
.divide(BigDecimal.valueOf(numPoints));
} finally {
fs.delete(TMP_DIR, true);
}
}

//Parse arguments and then runs a map/reduce job.
//Print output in standard out.
//@return a non-zero if there is an error. Otherwise, return 0.
public int run(String[] args) throws Exception {
if (args.length != 2) {
System.err.println("Usage: "+getClass().getName()+" <nMaps> <nSamples>");
ToolRunner.printGenericCommandUsage(System.err);
return -1;
}

final int nMaps = Integer.parseInt(args[0]);
final long nSamples = Long.parseLong(args[1]);

System.out.println("Number of Maps = " + nMaps);
System.out.println("Samples per Map = " + nSamples);

final JobConf jobConf = new JobConf(getConf(), getClass());
System.out.println("Estimated value of Pi is "
+ estimate(nMaps, nSamples, jobConf));
return 0;
}

//main method for running it as a stand alone command.
public static void main(String[] argv) throws Exception {
System.exit(ToolRunner.run(null, new PiEstimator(), argv));
}
}
```

## <a name="appendix-d---the-10gb-graysort-source-code"></a>Příloha D – 10gb graysort zdrojového kódu
Pro kontrolu v této části se zobrazí kód pro program TeraSort MapReduce.

```java
/**
    * Licensed to the Apache Software Foundation (ASF) under one
    * or more contributor license agreements.  See the NOTICE file
    * distributed with this work for additional information
    * regarding copyright ownership.  The ASF licenses this file
    * to you under the Apache License, Version 2.0 (the
    * "License"); you may not use this file except in compliance
    * with the License.  You may obtain a copy of the License at
    *
    *     http://www.apache.org/licenses/LICENSE-2.0
    *
    * Unless required by applicable law or agreed to in writing, software
    * distributed under the License is distributed on an "AS IS" BASIS,
    * WITHOUT WARRANTIES OR CONDITIONS OF ANY KIND, either express or implied.
    * See the License for the specific language governing permissions and
    * limitations under the License.
    */

package org.apache.hadoop.examples.terasort;

import java.io.IOException;
import java.io.PrintStream;
import java.net.URI;
import java.util.ArrayList;
import java.util.List;

import org.apache.commons.logging.Log;
import org.apache.commons.logging.LogFactory;
import org.apache.hadoop.conf.Configured;
import org.apache.hadoop.filecache.DistributedCache;
import org.apache.hadoop.fs.FileSystem;
import org.apache.hadoop.fs.Path;
import org.apache.hadoop.io.NullWritable;
import org.apache.hadoop.io.SequenceFile;
import org.apache.hadoop.io.Text;
import org.apache.hadoop.mapred.FileOutputFormat;
import org.apache.hadoop.mapred.JobClient;
import org.apache.hadoop.mapred.JobConf;
import org.apache.hadoop.mapred.Partitioner;
import org.apache.hadoop.util.Tool;
import org.apache.hadoop.util.ToolRunner;

/**
    * Generates the sampled split points, launches the job,
    * and waits for it to finish.
    * <p>
    * To run the program:
    * <b>bin/hadoop jar hadoop-examples-*.jar terasort in-dir out-dir</b>
    */

public class TeraSort extends Configured implements Tool {
    private static final Log LOG = LogFactory.getLog(TeraSort.class);

    /**
    * A partitioner that splits text keys into roughly equal
    * partitions in a global sorted order.
    */

    static class TotalOrderPartitioner implements Partitioner<Text,Text>{
    private TrieNode trie;
    private Text[] splitPoints;

    /**
        * A generic trie node
        */
    static abstract class TrieNode {
        private int level;
        TrieNode(int level) {
        this.level = level;
        }
        abstract int findPartition(Text key);
        abstract void print(PrintStream strm) throws IOException;
        int getLevel() {
        return level;
        }
    }

    /**
        * An inner trie node that contains 256 children based on the next
        * character.
        */
    static class InnerTrieNode extends TrieNode {
        private TrieNode[] child = new TrieNode[256];

        InnerTrieNode(int level) {
        super(level);
        }
        int findPartition(Text key) {
        int level = getLevel();
        if (key.getLength() <= level) {
            return child[0].findPartition(key);
        }
        return child[key.getBytes()[level]].findPartition(key);
        }
        void setChild(int idx, TrieNode child) {
        this.child[idx] = child;
        }
        void print(PrintStream strm) throws IOException {
        for(int ch=0; ch < 255; ++ch) {
            for(int i = 0; i < 2*getLevel(); ++i) {
            strm.print(' ');
            }
            strm.print(ch);
            strm.println(" ->");
            if (child[ch] != null) {
            child[ch].print(strm);
            }
        }
        }
    }

    /**
        * A leaf trie node that does string compares to figure out where the given
        * key belongs between lower..upper.
        */
    static class LeafTrieNode extends TrieNode {
        int lower;
        int upper;
        Text[] splitPoints;
        LeafTrieNode(int level, Text[] splitPoints, int lower, int upper) {
        super(level);
        this.splitPoints = splitPoints;
        this.lower = lower;
        this.upper = upper;
        }
        int findPartition(Text key) {
        for(int i=lower; i<upper; ++i) {
            if (splitPoints[i].compareTo(key) >= 0) {
            return i;
            }
        }
        return upper;
        }
        void print(PrintStream strm) throws IOException {
        for(int i = 0; i < 2*getLevel(); ++i) {
            strm.print(' ');
        }
        strm.print(lower);
        strm.print(", ");
        strm.println(upper);
        }
    }

    /**
        * Read the cut points from the given sequence file.
        * @param fs the file system
        * @param p the path to read
        * @param job the job config
        * @return the strings to split the partitions on
        * @throws IOException
        */
    private static Text[] readPartitions(FileSystem fs, Path p,
                                            JobConf job) throws IOException {
        SequenceFile.Reader reader = new SequenceFile.Reader(fs, p, job);
        List<Text> parts = new ArrayList<Text>();
        Text key = new Text();
        NullWritable value = NullWritable.get();
        while (reader.next(key, value)) {
        parts.add(key);
        key = new Text();
        }
        reader.close();
        return parts.toArray(new Text[parts.size()]);
    }

    /**
        * Given a sorted set of cut points, build a trie that will find the correct
        * partition quickly.
        * @param splits the list of cut points
        * @param lower the lower bound of partitions 0..numPartitions-1
        * @param upper the upper bound of partitions 0..numPartitions-1
        * @param prefix the prefix that we have already checked against
        * @param maxDepth the maximum depth we will build a trie for
        * @return the trie node that will divide the splits correctly
        */
    private static TrieNode buildTrie(Text[] splits, int lower, int upper,
                                        Text prefix, int maxDepth) {
        int depth = prefix.getLength();
        if (depth >= maxDepth || lower == upper) {
        return new LeafTrieNode(depth, splits, lower, upper);
        }
        InnerTrieNode result = new InnerTrieNode(depth);
        Text trial = new Text(prefix);
        // append an extra byte on to the prefix
        trial.append(new byte[1], 0, 1);
        int currentBound = lower;
        for(int ch = 0; ch < 255; ++ch) {
        trial.getBytes()[depth] = (byte) (ch + 1);
        lower = currentBound;
        while (currentBound < upper) {
            if (splits[currentBound].compareTo(trial) >= 0) {
            break;
            }
            currentBound += 1;
        }
        trial.getBytes()[depth] = (byte) ch;
        result.child[ch] = buildTrie(splits, lower, currentBound, trial,
                                        maxDepth);
        }
        // pick up the rest
        trial.getBytes()[depth] = 127;
        result.child[255] = buildTrie(splits, currentBound, upper, trial,
                                    maxDepth);
        return result;
    }

    public void configure(JobConf job) {
        try {
        FileSystem fs = FileSystem.getLocal(job);
        Path partFile = new Path(TeraInputFormat.PARTITION_FILENAME);
        splitPoints = readPartitions(fs, partFile, job);
        trie = buildTrie(splitPoints, 0, splitPoints.length, new Text(), 2);
        } catch (IOException ie) {
        throw new IllegalArgumentException("can't read paritions file", ie);
        }
    }

    public TotalOrderPartitioner() {
    }

    public int getPartition(Text key, Text value, int numPartitions) {
        return trie.findPartition(key);
    }

    }

    public int run(String[] args) throws Exception {
    LOG.info("starting");
    JobConf job = (JobConf) getConf();
    Path inputDir = new Path(args[0]);
    inputDir = inputDir.makeQualified(inputDir.getFileSystem(job));
    Path partitionFile = new Path(inputDir, TeraInputFormat.PARTITION_FILENAME);
    URI partitionUri = new URI(partitionFile.toString() +
                                "#" + TeraInputFormat.PARTITION_FILENAME);
    TeraInputFormat.setInputPaths(job, new Path(args[0]));
    FileOutputFormat.setOutputPath(job, new Path(args[1]));
    job.setJobName("TeraSort");
    job.setJarByClass(TeraSort.class);
    job.setOutputKeyClass(Text.class);
    job.setOutputValueClass(Text.class);
    job.setInputFormat(TeraInputFormat.class);
    job.setOutputFormat(TeraOutputFormat.class);
    job.setPartitionerClass(TotalOrderPartitioner.class);
    TeraInputFormat.writePartitionFile(job, partitionFile);
    DistributedCache.addCacheFile(partitionUri, job);
    DistributedCache.createSymlink(job);
    job.setInt("dfs.replication", 1);
    TeraOutputFormat.setFinalSync(job, true);
    JobClient.runJob(job);
    LOG.info("done");
    return 0;
    }

    /**
    * @param args
    */

    public static void main(String[] args) throws Exception {
    int res = ToolRunner.run(new JobConf(), new TeraSort(), args);
    System.exit(res);
    }
}
```

[hdinsight-sdk-documentation]: https://msdn.microsoft.com/library/azure/dn479185.aspx

[hdinsight-submit-jobs]: hadoop/submit-apache-hadoop-jobs-programmatically.md
[hdinsight-introduction]:hadoop/apache-hadoop-introduction.md

[powershell-install-configure]: /powershell/azureps-cmdlets-docs

[hdinsight-get-started]:hadoop/apache-hadoop-linux-tutorial-get-started.md

[hdinsight-samples]: hdinsight-run-samples.md
[hdinsight-sample-10gb-graysort]: #hdinsight-sample-10gb-graysort
[hdinsight-sample-csharp-streaming]: #hdinsight-sample-csharp-streaming
[hdinsight-sample-pi-estimator]: #hdinsight-sample-pi-estimator
[hdinsight-sample-wordcount]: #hdinsight-sample-wordcount

[hdinsight-use-hive]: hadoop/hdinsight-use-hive.md
[hdinsight-use-pig]: hadoop/hdinsight-use-pig.md

[streamreader]: http://msdn.microsoft.com/library/system.io.streamreader.aspx
[console-writeline]: http://msdn.microsoft.com/library/system.console.writeline
[stdin-stdout-stderr]: https://msdn.microsoft.com/library/3x292kth.aspx
