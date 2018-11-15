---
title: Vytvoření MapReduce v Javě pro Apache Hadoop – Azure HDInsight
description: Zjistěte, jak vytvářet aplikace založené na jazyce Java MapReduce a pak spustit ji s Hadoop v Azure HDInsight pomocí nástroje Apache Maven.
services: hdinsight
ms.reviewer: jasonh
author: hrasheed-msft
ms.service: hdinsight
ms.custom: hdinsightactive,hdiseo17may2017
ms.topic: conceptual
ms.date: 04/23/2018
ms.author: hrasheed
ms.openlocfilehash: 3c16d85479399d7988be18f9b852563617bb824b
ms.sourcegitcommit: 0b7fc82f23f0aa105afb1c5fadb74aecf9a7015b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/14/2018
ms.locfileid: "51632643"
---
# <a name="develop-java-mapreduce-programs-for-apache-hadoop-on-hdinsight"></a>Vývoj programů Java MapReduce pro Apache Hadoop v HDInsight

Zjistěte, jak vytvářet aplikace založené na jazyce Java MapReduce a pak ho spustit Apache Hadoop v Azure HDInsight pomocí nástroje Apache Maven.

> [!NOTE]
> V tomto příkladu byl testován jako poslední v HDInsight 3.6.

## <a name="prerequisites"></a>Požadavky

* [Java JDK](http://www.oracle.com/technetwork/java/javase/downloads/) 8 nebo novější (nebo ekvivalentní, například OpenJDK).
    
    > [!NOTE]
    > HDInsight verze 3.4 a starší pomocí Java 7. HDInsight 3.5 a vyšší používá Java 8.

* [Apache Maven](http://maven.apache.org/)

## <a name="configure-development-environment"></a>Konfigurace vývojového prostředí

Následující proměnné prostředí může být nastaven při instalaci Javy a sadu JDK. Nicméně byste měli zkontrolovat, že existují a že obsahují hodnoty správné pro váš systém.

* `JAVA_HOME` -by měly odkazovat na adresář, kde je nainstalován prostředí Java runtime (JRE). Například v systému OS X, Unix nebo Linux, měl by mít hodnotu podobnou `/usr/lib/jvm/java-7-oracle`. Windows neměl by mít hodnotu podobnou `c:\Program Files (x86)\Java\jre1.7`

* `PATH` – měla by obsahovat následující cesty:
  
  * `JAVA_HOME` (nebo ekvivalentní cesty)

  * `JAVA_HOME\bin` (nebo ekvivalentní cesty)

  * Adresář, kde je nainstalován Maven

## <a name="create-a-maven-project"></a>Vytvořte projekt Maven

1. Z relace Terminálové služby nebo příkazového řádku ve vašem vývojovém prostředí změňte adresáře na umístění, které chcete uložit tento projekt.

2. Použití `mvn` příkazu, který je nainstalován pomocí Mavenu, vygenerujte generování uživatelského rozhraní pro projekt.

   ```bash
   mvn archetype:generate -DgroupId=org.apache.hadoop.examples -DartifactId=wordcountjava -DarchetypeArtifactId=maven-archetype-quickstart -DinteractiveMode=false
   ```

    > [!NOTE]
    > Pokud používáte PowerShell, je nutné uzavřít `-D` parametry do dvojitých uvozovek.
    >
    > `mvn archetype:generate "-DgroupId=org.apache.hadoop.examples" "-DartifactId=wordcountjava" "-DarchetypeArtifactId=maven-archetype-quickstart" "-DinteractiveMode=false"`

    Tento příkaz vytvoří adresář s názvem zadaným `artifactID` parametr (**wordcountjava** v tomto příkladu.) Tento adresář obsahuje následující položky:

   * `pom.xml` – [Model objektu projektu (POM)](http://maven.apache.org/guides/introduction/introduction-to-the-pom.html) , který obsahuje podrobnosti o informace a konfigurace použít k sestavení projektu.

   * `src` – Adresář obsahující aplikaci.

3. Odstranit `src/test/java/org/apache/hadoop/examples/apptest.java` souboru. Není použit v tomto příkladu.

## <a name="add-dependencies"></a>Přidat závislosti

1. Upravit `pom.xml` a přidejte následující text mezi `<dependencies>` části:
   
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

    Definuje požadované knihovny (uvedené v rámci &lt;artifactId\>) s určitou verzí (uvedené v rámci &lt;verze\>). V době kompilace jsou tyto závislosti stáhnout z úložiště Maven výchozí. Můžete použít [Maven úložiště search](http://search.maven.org/#artifactdetails%7Corg.apache.hadoop%7Chadoop-mapreduce-examples%7C2.5.1%7Cjar) zobrazíte informace.
   
    `<scope>provided</scope>` Říká Maven, že tyto závislosti by neměl se dá zabalit s aplikací, uvedené v clusteru HDInsight v době běhu.

    > [!IMPORTANT]
    > Verze používá by měla odpovídat verzi k dispozici ve vašem clusteru Hadoop. Další informace o verzích, najdete v článku [Správa verzí komponenty HDInsight](../hdinsight-component-versioning.md) dokumentu.

2. Do souboru `pom.xml` přidejte následující kód. Tento text musí být uvnitř `<project>...</project>` značky v souboru; například mezi `</dependencies>` a `</project>`.

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

    První modul plug-in nakonfiguruje [modulu plug-in Maven odstín](http://maven.apache.org/plugins/maven-shade-plugin/), sloužící k sestavení uberjar (říká se jim fatjar), která obsahuje závislosti vyžadované aplikací. Zabrání také duplikace licencí v rámci balíček jar, což může způsobit problémy v některých systémech.

    Druhý modul plug-in konfiguruje cílovou verzi jazyka Java.

    > [!NOTE]
    > HDInsight 3.4 a použijte starší Java 7. HDInsight 3.5 a vyšší používá Java 8.

3. Uložte soubor `pom.xml`.

## <a name="create-the-mapreduce-application"></a>Vytvoření aplikace nástroje MapReduce

1. Přejděte `wordcountjava/src/main/java/org/apache/hadoop/examples` adresáře a přejmenovat `App.java` do souboru `WordCount.java`.

2. Otevřít `WordCount.java` souboru v textovém editoru a nahraďte jeho obsah následujícím textem:
   
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
   
    Všimněte si, že je název balíčku `org.apache.hadoop.examples` a název třídy je `WordCount`. Tyto názvy používat, když odešlete úlohu MapReduce.

3. Uložte soubor.

## <a name="build-the-application"></a>Sestavení aplikace

1. Přejděte `wordcountjava` adresář, pokud si nejste již existuje.

2. Sestavit soubor JAR obsahující aplikace, použijte následující příkaz:

   ```
   mvn clean package
   ```

    Tento příkaz odstraní všechny předchozí artefakty sestavení, soubory ke stažení všechny závislosti, které dosud nebyly nainstalovány a pak sestavení a balíček aplikace.

3. Po dokončení příkazu `wordcountjava/target` adresář obsahuje soubor s názvem `wordcountjava-1.0-SNAPSHOT.jar`.
   
   > [!NOTE]
   > `wordcountjava-1.0-SNAPSHOT.jar` Soubor je uberjar, který obsahuje nejen WordCount úlohy, ale také závislosti, které úloha vyžaduje za běhu.

## <a id="upload"></a>Nahrát soubor jar

Nahrát soubor jar pro hlavní uzel HDInsight použijte následující příkaz:

   ```bash
   scp target/wordcountjava-1.0-SNAPSHOT.jar USERNAME@CLUSTERNAME-ssh.azurehdinsight.net:
   ```

Nahraďte __uživatelské jméno__ se svým uživatelským jménem SSH pro cluster. Nahraďte __CLUSTERNAME__ s názvem clusteru HDInsight.

Tento příkaz zkopíruje soubory z místního systému k hlavnímu uzlu. Další informace najdete v tématu [Použití SSH se službou HDInsight](../hdinsight-hadoop-linux-use-ssh-unix.md).

## <a name="run"></a>Spustit úlohu MapReduce pro Hadoop

1. Připojení k HDInsight pomocí SSH. Další informace najdete v tématu [Použití SSH se službou HDInsight](../hdinsight-hadoop-linux-use-ssh-unix.md).

2. Z relace SSH použijte následující příkaz pro spuštění aplikace MapReduce:
   
   ```bash
   yarn jar wordcountjava-1.0-SNAPSHOT.jar org.apache.hadoop.examples.WordCount /example/data/gutenberg/davinci.txt /example/data/wordcountout
   ```
   
    Tento příkaz spustí aplikaci WordCount MapReduce. Vstupní soubor je `/example/data/gutenberg/davinci.txt`, a výstupní adresář je `/example/data/wordcountout`. Vstupní soubor i výstupu jsou uloženy do výchozího úložiště pro cluster.

3. Po dokončení úlohy, použijte následující příkaz k zobrazení výsledků:
   
   ```bash
   hdfs dfs -cat /example/data/wordcountout/*
   ```

    Mělo by se zobrazit seznam slov a počty s hodnotami, které jsou podobné následujícímu textu:
   
        zeal    1
        zelus   1
        zenith  2

## <a id="nextsteps"></a>Další kroky

V tomto dokumentu jste zjistili, jak vývoj úloh MapReduce v Javě. Najdete v následujících dokumentech další způsoby práce s HDInsight.

* [Použití Hivu se službou HDInsight](hdinsight-use-hive.md)
* [Použití Pigu se službou HDInsight](hdinsight-use-pig.md)
* [Použití MapReduce se službou HDInsight](hdinsight-use-mapreduce.md)

Další informace najdete v tématu taky [středisko pro vývojáře Java](https://azure.microsoft.com/develop/java/).

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

