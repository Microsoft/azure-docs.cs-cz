---
title: Vytvoření java mapreduce pro Apache Hadoop - Azure HDInsight
description: Naučte se používat Apache Maven k vytvoření aplikace MapReduce založené na Javě a pak ji spusťte s Hadoopem v Azure HDInsight.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.custom: hdinsightactive,hdiseo17may2017
ms.date: 01/16/2020
ms.openlocfilehash: a37a8bb45c11d5b74f3059a153806e3d083cf452
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "76311950"
---
# <a name="develop-java-mapreduce-programs-for-apache-hadoop-on-hdinsight"></a>Vývoj Java MapReduce programy pro Apache Hadoop na HDInsight

Naučte se používat Apache Maven k vytvoření java-založené aplikace MapReduce a pak ji spustit s Apache Hadoop na Azure HDInsight.

## <a name="prerequisites"></a>Požadavky

* [Java Developer Kit (JDK) verze 8](https://aka.ms/azure-jdks).

* [Apache Maven](https://maven.apache.org/download.cgi) správně [nainstalován](https://maven.apache.org/install.html) podle Apache.  Maven je systém vytváření projektů pro java projekty.

## <a name="configure-development-environment"></a>Konfigurace vývojového prostředí

Prostředí používané pro tento článek byl počítač se systémem Windows 10. Příkazy byly provedeny v příkazovém řádku a různé soubory byly upraveny pomocí poznámkového bloku. Upravte odpovídajícím způsobem pro vaše prostředí.

Z příkazového řádku zadejte níže uvedené příkazy a vytvořte pracovní prostředí:

```cmd
IF NOT EXIST C:\HDI MKDIR C:\HDI
cd C:\HDI
```

## <a name="create-a-maven-project"></a>Vytvoření projektu Maven

1. Zadejte následující příkaz pro vytvoření projektu Maven s názvem **wordcountjava**:

   ```bash
   mvn archetype:generate -DgroupId=org.apache.hadoop.examples -DartifactId=wordcountjava -DarchetypeArtifactId=maven-archetype-quickstart -DinteractiveMode=false
   ```

    Tento příkaz vytvoří adresář s názvem `artifactID` určeným parametrem **(wordcountjava** v tomto příkladu.) Tento adresář obsahuje následující položky:

    * `pom.xml`- [Objektový model projektu (POM),](https://maven.apache.org/guides/introduction/introduction-to-the-pom.html) který obsahuje informace a podrobnosti konfigurace použité k sestavení projektu.
    * src\main\java\org\apache\hadoop\examples: Obsahuje kód aplikace.
    * src\test\java\org\apache\hadoop\examples: Obsahuje testy pro vaši aplikaci.

1. Odeberte generovaný ukázkový kód. Odstraňte generované testovací a `AppTest.java`aplikační `App.java` soubory a zadáním následujících příkazů:

    ```cmd
    cd wordcountjava
    DEL src\main\java\org\apache\hadoop\examples\App.java
    DEL src\test\java\org\apache\hadoop\examples\AppTest.java
    ```

## <a name="update-the-project-object-model"></a>Aktualizace objektového modelu projektu

Úplný odkaz na soubor pom.xml https://maven.apache.org/pom.htmlnaleznete v tématu . Otevřít `pom.xml` zadáním níže uvedeného příkazu:

```cmd
notepad pom.xml
```

### <a name="add-dependencies"></a>Přidání závislostí

V `pom.xml`oddíle přidejte `<dependencies>` následující text:

```xml
<dependency>
    <groupId>org.apache.hadoop</groupId>
    <artifactId>hadoop-mapreduce-examples</artifactId>
    <version>2.7.3</version>
    <scope>provided</scope>
</dependency>
<dependency>
    <groupId>org.apache.hadoop</groupId>
    <artifactId>hadoop-mapreduce-client-common</artifactId>
    <version>2.7.3</version>
    <scope>provided</scope>
</dependency>
<dependency>
    <groupId>org.apache.hadoop</groupId>
    <artifactId>hadoop-common</artifactId>
    <version>2.7.3</version>
    <scope>provided</scope>
</dependency>
```

To definuje požadované knihovny &lt;(uvedené\>v rámci artifactId &lt;)\>s konkrétní verzí (uvedenou ve verzi ). V době kompilace jsou tyto závislosti staženy z výchozího úložiště Maven. Můžete použít [vyhledávání úložiště Maven](https://search.maven.org/#artifactdetails%7Corg.apache.hadoop%7Chadoop-mapreduce-examples%7C2.5.1%7Cjar) pro zobrazení více.

Říká `<scope>provided</scope>` Maven, že tyto závislosti by neměly být zabaleny s aplikací, protože jsou poskytovány clusteru HDInsight za běhu.

> [!IMPORTANT]
> Použitá verze by měla odpovídat verzi Hadoop u vašeho clusteru. Další informace o verzích najdete v dokumentu [pro správu verzí komponent HDInsight.](../hdinsight-component-versioning.md)

### <a name="build-configuration"></a>Konfigurace sestavení

Moduly plug-in Maven umožňují přizpůsobit fáze sestavení projektu. Tato část slouží k přidání modulů plug-in, prostředků a dalších možností konfigurace sestavení.

Přidejte do souboru `pom.xml` následující kód a potom soubor uložte a zavřete. Tento text musí `<project>...</project>` být uvnitř tagů v `</dependencies>` souboru, například mezi a `</project>`.

```xml
<build>
    <plugins>
    <plugin>
        <groupId>org.apache.maven.plugins</groupId>
        <artifactId>maven-shade-plugin</artifactId>
        <version>2.3</version>
        <configuration>
        <transformers>
            <transformer implementation="org.apache.maven.plugins.shade.resource.ApacheLicenseResourceTransformer">
            </transformer>
        </transformers>
        </configuration>
        <executions>
        <execution>
            <phase>package</phase>
                <goals>
                <goal>shade</goal>
                </goals>
        </execution>
        </executions>
        </plugin>
    <plugin>
        <groupId>org.apache.maven.plugins</groupId>
        <artifactId>maven-compiler-plugin</artifactId>
        <version>3.6.1</version>
        <configuration>
        <source>1.8</source>
        <target>1.8</target>
        </configuration>
    </plugin>
    </plugins>
</build>
```

Tato část konfiguruje Apache Maven Compiler Plugin a Apache Maven Shade Plugin. Modul plug-in kompilátoru se používá ke kompilaci topologie. Modul plug-in shade se používá k zabránění duplikaci licencí v balíčku JAR, který je vytvořen společností Maven. Tento plugin se používá k zabránění chyby "duplicitních licenčních souborů" za běhu v clusteru HDInsight. Použití maven-shade-plugin `ApacheLicenseResourceTransformer` s implementací zabraňuje chybě.

Maven-shade-plugin také vytváří uber jar, který obsahuje všechny závislosti požadované aplikací.

Uložte soubor `pom.xml`.

## <a name="create-the-mapreduce-application"></a>Vytvoření aplikace MapReduce

1. Chcete-li vytvořit a otevřít nový `WordCount.java`soubor , zadejte následující příkaz . Vyberte **Ano** na výzvu k vytvoření nového souboru.

    ```cmd
    notepad src\main\java\org\apache\hadoop\examples\WordCount.java
    ```

2. Pak zkopírujte a vložte java kód níže do nového souboru. Potom soubor zavřete.

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

    Všimněte si, `org.apache.hadoop.examples` že název `WordCount`balíčku je a název třídy je . Tyto názvy se používají při odesílání úlohy MapReduce.

## <a name="build-and-package-the-application"></a>Sestavení a balíček aplikace

Z `wordcountjava` adresáře vytvořte soubor JAR obsahující aplikaci pomocí následujícího příkazu:

```cmd
mvn clean package
```

Tento příkaz vyčistí všechny předchozí artefakty sestavení, stáhne všechny závislosti, které ještě nebyly nainstalovány, a potom vytvoří a zabalí aplikaci.

Po dokončení příkazu `wordcountjava/target` obsahuje adresář soubor `wordcountjava-1.0-SNAPSHOT.jar`s názvem .

> [!NOTE]
> Soubor `wordcountjava-1.0-SNAPSHOT.jar` je uberjar, který obsahuje nejen úlohu WordCount, ale také závislosti, které úloha vyžaduje za běhu.

## <a name="upload-the-jar-and-run-jobs-ssh"></a>Nahrát JAR a spustit úlohy (SSH)

Následující kroky `scp` slouží ke zkopírování JAR do primárního hlavního uzlu vašeho clusteru Apache HBase v clusteru HDInsight. Příkaz `ssh` se pak používá k připojení ke clusteru a spustit příklad přímo na hlavním uzlu.

1. Nahrajte nádobu do clusteru. Nahraďte `CLUSTERNAME` název clusteru HDInsight a zadejte následující příkaz:

    ```cmd
    scp target/wordcountjava-1.0-SNAPSHOT.jar sshuser@CLUSTERNAME-ssh.azurehdinsight.net:
    ```

1. Připojte se ke clusteru. Nahraďte `CLUSTERNAME` název clusteru HDInsight a zadejte následující příkaz:

    ```cmd
    ssh sshuser@CLUSTERNAME-ssh.azurehdinsight.net
    ```

1. Z relace SSH spusťte aplikaci MapReduce pomocí následujícího příkazu:

   ```bash
   yarn jar wordcountjava-1.0-SNAPSHOT.jar org.apache.hadoop.examples.WordCount /example/data/gutenberg/davinci.txt /example/data/wordcountout
   ```

    Tento příkaz spustí aplikaci WordCount MapReduce. Vstupní soubor `/example/data/gutenberg/davinci.txt`je a výstupní `/example/data/wordcountout`adresář je . Vstupní soubor i výstup jsou uloženy do výchozího úložiště pro cluster.

1. Po dokončení úlohy zobrazte výsledky pomocí následujícího příkazu:

   ```bash
   hdfs dfs -cat /example/data/wordcountout/*
   ```

    Měli byste obdržet seznam slov a počtů s hodnotami podobnými následujícímu textu:

    ```output
    zeal    1
    zelus   1
    zenith  2
    ```

## <a name="next-steps"></a>Další kroky

V tomto dokumentu jste se naučili, jak vytvořit úlohu Java MapReduce. Další způsoby práce s HDInsight najdete v následujících dokumentech.

* [Použití Apache Hive s HDInsight](hdinsight-use-hive.md)
* [Použití mapreduce s HDInsight](hdinsight-use-mapreduce.md)
* [Středisko pro vývojáře Java](https://azure.microsoft.com/develop/java/)
