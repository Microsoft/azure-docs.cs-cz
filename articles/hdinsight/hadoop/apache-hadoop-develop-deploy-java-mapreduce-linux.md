---
title: Vytvoření Java MapReduce pro Hadoop - Azure HDInsight | Microsoft Docs
description: Další informace o použití Apache Maven k vytvoření aplikace založené na jazyce Java MapReduce a potom ji spustit s Hadoop na Azure HDInsight.
services: hdinsight
editor: cgronlun
manager: jhubbard
author: Blackmist
documentationcenter: ''
tags: azure-portal
ms.assetid: 9ee6384c-cb61-4087-8273-fb53fa27c1c3
ms.service: hdinsight
ms.custom: hdinsightactive,hdiseo17may2017
ms.devlang: Java
ms.topic: conceptual
ms.date: 01/25/2018
ms.author: larryfr
ms.openlocfilehash: 05b41f90b9508088c32923469b6c437b74ee5333
ms.sourcegitcommit: 9cdd83256b82e664bd36991d78f87ea1e56827cd
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/16/2018
---
# <a name="develop-java-mapreduce-programs-for-hadoop-on-hdinsight"></a>Vývoj aplikací Java MapReduce pro Hadoop v HDInsight

Další informace o použití Apache Maven k vytvoření aplikace založené na jazyce Java MapReduce a potom ji spustit s Hadoop na Azure HDInsight.

> [!NOTE]
> Tento příklad byl naposledy otestovali na HDInsight 3.6.

## <a name="prerequisites"></a>Požadavky

* [Java JDK](http://www.oracle.com/technetwork/java/javase/downloads/) 8 nebo novější (nebo ekvivalentní, jako je například OpenJDK).
    
    > [!NOTE]
    > HDInsight verze 3.4 a starší pomocí Java 7. HDInsight 3.5 a vyšší používá Java 8.

* [Apache Maven](http://maven.apache.org/)

## <a name="configure-development-environment"></a>Konfigurace vývojového prostředí

Následující proměnné prostředí může být nastaven při instalaci Java a sadu JDK. Nicméně byste měli zkontrolovat, že existují a že obsahují správné hodnoty pro váš systém.

* `JAVA_HOME` -by měla odkazovat na adresář, kam nainstalovat prostředí Java runtime (JRE). Například v systému OS X, Unix nebo Linux, musí mít hodnotu podobnou `/usr/lib/jvm/java-7-oracle`. V systému Windows má hodnotu podobnou `c:\Program Files (x86)\Java\jre1.7`

* `PATH` -musí obsahovat následující cesty:
  
  * `JAVA_HOME` (nebo ekvivalentní cesta)

  * `JAVA_HOME\bin` (nebo ekvivalentní cesta)

  * Adresář, kde je nainstalován Maven

## <a name="create-a-maven-project"></a>Vytvořte projekt Maven

1. Z relace Terminálové služby nebo příkazového řádku ve vašem vývojovém prostředí změňte adresáře na umístění, které chcete uložit tento projekt.

2. Použití `mvn` příkazu, který se instaluje s Maven, vygenerujte generování uživatelského rozhraní pro projekt.

   ```bash
   mvn archetype:generate -DgroupId=org.apache.hadoop.examples -DartifactId=wordcountjava -DarchetypeArtifactId=maven-archetype-quickstart -DinteractiveMode=false
   ```

    > [!NOTE]
    > Pokud používáte prostředí PowerShell, je nutné uzavřít `-D` parametry v uvozovkách.
    >
    > `mvn archetype:generate "-DgroupId=org.apache.hadoop.examples" "-DartifactId=wordcountjava" "-DarchetypeArtifactId=maven-archetype-quickstart" "-DinteractiveMode=false"`

    Tento příkaz vytvoří adresář s názvem zadaným `artifactID` parametr (**wordcountjava** v tomto příkladu.) Tento adresář obsahuje následující položky:

   * `pom.xml` -Na [projektu objektu modelu (POM)](http://maven.apache.org/guides/introduction/introduction-to-the-pom.html) obsahující podrobnosti o informace a konfigurace použít k sestavení projektu.

   * `src` -Adresář, který obsahuje aplikace.

3. Odstranit `src/test/java/org/apache/hadoop/examples/apptest.java` souboru. V tomto příkladu se nepoužívá.

## <a name="add-dependencies"></a>Přidat závislosti

1. Upravit `pom.xml` souboru a přidejte následující text v rámci `<dependencies>` části:
   
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

    Definuje požadované knihovny (uvedené v &lt;artifactId\>) s určitou verzí (uvedené v &lt;verze\>). Při kompilaci se stáhnou tyto závislosti z úložiště Maven výchozí. Můžete použít [hledání úložiště Maven](http://search.maven.org/#artifactdetails%7Corg.apache.hadoop%7Chadoop-mapreduce-examples%7C2.5.1%7Cjar) zobrazení.
   
    `<scope>provided</scope>` Informuje Maven, tyto závislosti by neměl být zabalené s aplikací, jako jsou k dispozici v clusteru HDInsight za běhu.

    > [!IMPORTANT]
    > Je verze použitá by měl odpovídat verzi nachází v clusteru Hadoop. Další informace o verzích, najdete v článku [Správa verzí komponenty HDInsight](../hdinsight-component-versioning.md) dokumentu.

2. Přidejte následující `pom.xml` souboru. Tento text musí být uvnitř `<project>...</project>` značky v souboru, například mezi `</dependencies>` a `</project>`.

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

    První modul plug-in nakonfiguruje [modulu plug-in stín Maven](http://maven.apache.org/plugins/maven-shade-plugin/), sloužící k sestavení uberjar (někdy nazývané fatjar), která obsahuje závislosti, které jsou požadované aplikací. Rovněž zamezí duplikace licencí v rámci balíčku jar, což může způsobit problémy na některé systémy.

    Druhý modul plug-in nakonfiguruje cílová verze Java.

    > [!NOTE]
    > HDInsight 3.4 a starší používání Java 7. HDInsight 3.5 a vyšší používá Java 8.

3. Uložte soubor `pom.xml`.

## <a name="create-the-mapreduce-application"></a>Vytvoření aplikace nástroje MapReduce

1. Přejděte na `wordcountjava/src/main/java/org/apache/hadoop/examples` adresáře a přejmenujte `App.java` do souboru `WordCount.java`.

2. Otevřete `WordCount.java` soubor v textovém editoru a nahraďte jeho obsah následujícím textem:
   
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
   
    Všimněte si, název balíčku je `org.apache.hadoop.examples` a název třídy je `WordCount`. Můžete použít tyto názvy při odeslání úlohy MapReduce.

3. Uložte soubor.

## <a name="build-the-application"></a>Sestavení aplikace

1. Změnit na `wordcountjava` adresáře, pokud si nejste již existuje.

2. Vytvořit soubor JAR obsahující aplikace, použijte následující příkaz:

   ```
   mvn clean package
   ```

    Tento příkaz odstraní všechny předchozí artefakty sestavení, stáhne všechny závislosti, které dosud nebyly nainstalovány a pak sestavení a balíček aplikace.

3. Po dokončení příkazu `wordcountjava/target` adresář obsahuje soubor s názvem `wordcountjava-1.0-SNAPSHOT.jar`.
   
   > [!NOTE]
   > `wordcountjava-1.0-SNAPSHOT.jar` Soubor je uberjar, který obsahuje pouze úlohu WordCount, ale také závislosti, které úloha vyžaduje za běhu.

## <a id="upload"></a>Nahrát jar

Použijte následující příkaz k nahrání na soubor jar do HDInsight headnode:

   ```bash
   scp target/wordcountjava-1.0-SNAPSHOT.jar USERNAME@CLUSTERNAME-ssh.azurehdinsight.net:
   ```

    Replace __USERNAME__ with your SSH user name for the cluster. Replace __CLUSTERNAME__ with the HDInsight cluster name.

Tento příkaz zkopíruje soubory z místního systému k hlavnímu uzlu. Další informace najdete v tématu [Použití SSH se službou HDInsight](../hdinsight-hadoop-linux-use-ssh-unix.md).

## <a name="run"></a>Spustit úlohu MapReduce na Hadoop

1. Připojení k HDInsight pomocí SSH. Další informace najdete v tématu [Použití SSH se službou HDInsight](../hdinsight-hadoop-linux-use-ssh-unix.md).

2. Z relace SSH použijte následující příkaz ke spuštění aplikace MapReduce:
   
   ```bash
   yarn jar wordcountjava-1.0-SNAPSHOT.jar org.apache.hadoop.examples.WordCount /example/data/gutenberg/davinci.txt /example/data/wordcountout
   ```
   
    Tento příkaz spustí aplikaci WordCount MapReduce. Vstupní soubor je `/example/data/gutenberg/davinci.txt`, a je k zadanému výstupnímu adresáři `/example/data/wordcountout`. Soubor vstupní i výstupní jsou uloženy na výchozí úložiště pro cluster.

3. Po dokončení úlohy pro zobrazení výsledků použijte následující příkaz:
   
   ```bash
   hdfs dfs -cat /example/data/wordcountout/*
   ```

    Mělo by se zobrazit seznam slova a počty s hodnotami podobná následující text:
   
        zeal    1
        zelus   1
        zenith  2

## <a id="nextsteps"></a>Další kroky

V tomto dokumentu jste se naučili, jak vyvíjet úlohu Java MapReduce. Najdete v následujících dokumentech pro další způsoby, jak pracovat s HDInsight.

* [Použití Hivu se službou HDInsight](hdinsight-use-hive.md)
* [Použití Pigu se službou HDInsight](hdinsight-use-pig.md)
* [Používání nástroje MapReduce s HDInsight](hdinsight-use-mapreduce.md)

Další informace naleznete také [středisko pro vývojáře Java](https://azure.microsoft.com/develop/java/).

[azure-purchase-options]: http://azure.microsoft.com/pricing/purchase-options/
[azure-member-offers]: http://azure.microsoft.com/pricing/member-offers/
[azure-free-trial]: http://azure.microsoft.com/pricing/free-trial/

[hdinsight-use-sqoop]:hdinsight-use-sqoop.md
[hdinsight-ODBC]: hdinsight-connect-excel-hive-ODBC-driver.md
[hdinsight-power-query]:apache-hadoop-connect-excel-power-query.md

[hdinsight-upload-data]: hdinsight-upload-data.md
[hdinsight-admin-powershell]: hdinsight-administer-use-powershell.md
[hdinsight-power-query]:apache-hadoop-connect-excel-power-query.md

[powershell-PSCredential]: http://social.technet.microsoft.com/wiki/contents/articles/4546.working-with-passwords-secure-strings-and-credentials-in-windows-powershell.aspx

