---
title: Vytvoření Java MapReduce pro Apache Hadoop – Azure HDInsight
description: Naučte se, jak pomocí Apache Maven vytvořit aplikaci MapReduce založenou na jazyce Java a pak ji spustit se systémem Hadoop ve službě Azure HDInsight.
ms.service: hdinsight
ms.topic: how-to
ms.custom: hdinsightactive,hdiseo17may2017, devx-track-java
ms.date: 01/16/2020
ms.openlocfilehash: 7c1a6623883cbee46ba98982808f3c392dc50ffa
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2021
ms.locfileid: "98946653"
---
# <a name="develop-java-mapreduce-programs-for-apache-hadoop-on-hdinsight"></a>Vývoj programů Java MapReduce pro Apache Hadoop v HDInsight

Naučte se, jak pomocí Apache Maven vytvořit aplikaci MapReduce založenou na jazyce Java a pak ji spustit s Apache Hadoop v Azure HDInsight.

## <a name="prerequisites"></a>Předpoklady

* [Java Developer Kit (JDK) verze 8](/azure/developer/java/fundamentals/java-jdk-long-term-support).

* [Apache Maven](https://maven.apache.org/download.cgi) správně [nainstalované](https://maven.apache.org/install.html) v souladu s Apache.  Maven je systém sestavení projektu pro projekty v jazyce Java.

## <a name="configure-development-environment"></a>Konfigurace vývojového prostředí

Prostředí použité pro tento článek bylo počítač se systémem Windows 10. Příkazy byly provedeny v příkazovém řádku a různé soubory byly upraveny pomocí poznámkového bloku. Upravte odpovídajícím způsobem pro vaše prostředí.

Z příkazového řádku zadejte níže uvedené příkazy pro vytvoření funkčního prostředí:

```cmd
IF NOT EXIST C:\HDI MKDIR C:\HDI
cd C:\HDI
```

## <a name="create-a-maven-project"></a>Vytvoření projektu Maven

1. Zadejte následující příkaz a vytvořte tak projekt Maven s názvem **wordcountjava**:

   ```bash
   mvn archetype:generate -DgroupId=org.apache.hadoop.examples -DartifactId=wordcountjava -DarchetypeArtifactId=maven-archetype-quickstart -DinteractiveMode=false
   ```

    Tento příkaz vytvoří adresář s názvem zadaným `artifactID` parametrem (v tomto příkladu **wordcountjava** ). Tento adresář obsahuje následující položky:

    * `pom.xml` – [Model objektu projektu (pom)](https://maven.apache.org/guides/introduction/introduction-to-the-pom.html) , který obsahuje informace a podrobnosti o konfiguraci použité k sestavení projektu.
    * src\main\java\org\apache\hadoop\examples: obsahuje kód vaší aplikace.
    * src\test\java\org\apache\hadoop\examples: obsahuje testy pro vaši aplikaci.

1. Odeberte generovaný ukázkový kód. Odstraňte vygenerované soubory testů a aplikace `AppTest.java` a `App.java` zadáním následujících příkazů:

    ```cmd
    cd wordcountjava
    DEL src\main\java\org\apache\hadoop\examples\App.java
    DEL src\test\java\org\apache\hadoop\examples\AppTest.java
    ```

## <a name="update-the-project-object-model"></a>Aktualizace modelu objektu projektu

Úplný odkaz na soubor pom.xml naleznete v tématu https://maven.apache.org/pom.html . Otevřete zadáním `pom.xml` následujícího příkazu:

```cmd
notepad pom.xml
```

### <a name="add-dependencies"></a>Přidat závislosti

V `pom.xml` přidejte do oddílu následující text `<dependencies>` :

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

Tato definice definuje požadované knihovny (uvedené v &lt; artifactId \> ) s konkrétní verzí (uvedenou v rámci &lt; verze \> ). V době kompilace se tyto závislosti stáhnou z výchozího úložiště Maven. Pomocí [hledání úložiště Maven](https://search.maven.org/#artifactdetails%7Corg.apache.hadoop%7Chadoop-mapreduce-examples%7C2.5.1%7Cjar) můžete zobrazit další informace.

`<scope>provided</scope>`Říká Maven, že tyto závislosti by neměly být zabaleny do aplikace, protože jsou poskytovány clusterem HDInsight za běhu.

> [!IMPORTANT]
> Použitá verze by měla odpovídat verzi Hadoop přítomné ve vašem clusteru. Další informace o verzích najdete v dokumentu [Správa verzí komponent HDInsight](../hdinsight-component-versioning.md) .

### <a name="build-configuration"></a>Konfigurace sestavení

Moduly plug-in Maven umožňují přizpůsobit fáze sestavení projektu. Tato část slouží k přidání modulů plug-in, prostředků a dalších možností konfigurace sestavení.

Do souboru přidejte následující kód `pom.xml` a soubor uložte a zavřete. Tento text musí být uvnitř `<project>...</project>` značek v souboru, například mezi `</dependencies>` a `</project>` .

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

V této části nakonfigurujeme modul plug-in kompilátoru Apache Maven a modul plug-in Apache Maven. Modul plug-in kompilátoru se používá ke kompilaci topologie. Modul plug-in pro barevný nádech se používá k tomu, aby se zabránilo duplicitě licencí v balíčku JAR, který je sestavený pomocí Maven. Tento modul plug-in se používá k tomu, aby v clusteru HDInsight nedocházelo k chybě duplicitních licenčních souborů v době běhu. Použití Maven-destínového plug-in s `ApacheLicenseResourceTransformer` implementací zabraňuje chybu.

Maven-barevný modul plug-in také vytvoří Uber jar, který obsahuje všechny závislosti, které aplikace požaduje.

Uložte soubor `pom.xml`.

## <a name="create-the-mapreduce-application"></a>Vytvoření aplikace MapReduce

1. Zadáním následujícího příkazu vytvořte nový soubor a otevřete ho `WordCount.java` . V příkazovém řádku vyberte **Ano** , pokud chcete vytvořit nový soubor.

    ```cmd
    notepad src\main\java\org\apache\hadoop\examples\WordCount.java
    ```

2. Pak zkopírujte a vložte kód Java níže do nového souboru. Pak soubor zavřete.

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

    Všimněte si, že název balíčku je `org.apache.hadoop.examples` a název třídy je `WordCount` . Tyto názvy použijete při odeslání úlohy MapReduce.

## <a name="build-and-package-the-application"></a>Sestavení a zabalení aplikace

Z `wordcountjava` adresáře použijte následující příkaz k vytvoření souboru jar, který obsahuje aplikaci:

```cmd
mvn clean package
```

Tento příkaz vyčistí všechny předchozí artefakty sestavení, stáhne všechny závislosti, které ještě nejsou nainstalované, a pak sestaví a zabalí aplikaci.

Po dokončení příkazu `wordcountjava/target` adresář obsahuje soubor s názvem `wordcountjava-1.0-SNAPSHOT.jar` .

> [!NOTE]
> `wordcountjava-1.0-SNAPSHOT.jar`Soubor je uberjar, který obsahuje nejen úlohu WORDCOUNT, ale také závislosti, které úloha vyžaduje za běhu.

## <a name="upload-the-jar-and-run-jobs-ssh"></a>Nahrát úlohy JAR a spustit (SSH)

Následující postup slouží `scp` ke zkopírování jar do primárního hlavního uzlu HBA HBA v clusteru HDInsight. `ssh`Příkaz se pak použije pro připojení ke clusteru a spuštění příkladu přímo na hlavním uzlu.

1. Nahrajte jar do clusteru. Nahraďte `CLUSTERNAME` názvem clusteru HDInsight a potom zadejte následující příkaz:

    ```cmd
    scp target/wordcountjava-1.0-SNAPSHOT.jar sshuser@CLUSTERNAME-ssh.azurehdinsight.net:
    ```

1. Připojte se ke clusteru. Nahraďte `CLUSTERNAME` názvem clusteru HDInsight a potom zadejte následující příkaz:

    ```cmd
    ssh sshuser@CLUSTERNAME-ssh.azurehdinsight.net
    ```

1. Z relace SSH pomocí následujícího příkazu spusťte aplikaci MapReduce:

   ```bash
   yarn jar wordcountjava-1.0-SNAPSHOT.jar org.apache.hadoop.examples.WordCount /example/data/gutenberg/davinci.txt /example/data/wordcountout
   ```

    Tento příkaz spustí aplikaci WordCount MapReduce. Vstupní soubor je `/example/data/gutenberg/davinci.txt` a výstupní adresář je `/example/data/wordcountout` . Vstupní soubor i výstup budou uloženy do výchozího úložiště pro cluster.

1. Po dokončení úlohy použijte následující příkaz k zobrazení výsledků:

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

V tomto dokumentu jste se seznámili s postupem vývoje úlohy Java MapReduce. Další způsoby, jak pracovat se službou HDInsight, najdete v následujících dokumentech.

* [Použití Apache Hive se službou HDInsight](hdinsight-use-hive.md)
* [Použití MapReduce se službou HDInsight](hdinsight-use-mapreduce.md)
* [Středisko pro vývojáře Java](https://azure.microsoft.com/develop/java/)