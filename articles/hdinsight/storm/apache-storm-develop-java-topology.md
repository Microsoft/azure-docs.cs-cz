---
title: Apache Storm ukázka topologie Java – Azure HDInsight
description: Naučte se vytvářet topologie Apache Storm v jazyce Java vytvořením ukázkové topologie počtu slov.
ms.service: hdinsight
ms.topic: how-to
ms.custom: H1Hack27Feb2017,hdinsightactive,hdiseo17may2017,seoapr2020,devx-track-java
ms.date: 04/27/2020
ms.openlocfilehash: 620a4e1627b25af22db68173f35924376e26f5f8
ms.sourcegitcommit: 2f9f306fa5224595fa5f8ec6af498a0df4de08a8
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/28/2021
ms.locfileid: "98929116"
---
# <a name="create-an-apache-storm-topology-in-java"></a>Vytvoření topologie Apache Storm v jazyce Java

Naučte se vytvořit topologii založenou na jazyce Java pro Apache Storm. Vytvoříte topologii, která implementuje aplikaci počtu slov. Použijte Apache Maven k sestavení a zabalení projektu. Pak se naučíte, jak definovat topologii pomocí rozhraní Apache Stormch toků.

Po dokončení kroků v tomto dokumentu můžete tuto topologii nasadit do Apache Storm ve službě HDInsight.

> [!NOTE]  
> V tomto dokumentu jsou k dispozici kompletní verze ukázek topologie, které jsou vytvořeny v tomto dokumentu [https://github.com/Azure-Samples/hdinsight-java-storm-wordcount](https://github.com/Azure-Samples/hdinsight-java-storm-wordcount) .

## <a name="prerequisites"></a>Požadavky

* [Java Developer Kit (JDK) verze 8](/azure/developer/java/fundamentals/java-jdk-long-term-support)

* [Apache Maven](https://maven.apache.org/download.cgi) správně [nainstalované](https://maven.apache.org/install.html) v souladu s Apache.  Maven je systém sestavení projektu pro projekty v jazyce Java.

## <a name="test-environment"></a>Testovací prostředí

Prostředí použité pro tento článek bylo počítač se systémem Windows 10.  Příkazy byly provedeny v příkazovém řádku a různé soubory byly upraveny pomocí poznámkového bloku.

Z příkazového řádku zadejte níže uvedené příkazy pro vytvoření funkčního prostředí:

```cmd
mkdir C:\HDI
cd C:\HDI
```

## <a name="create-a-maven-project"></a>Vytvoření projektu Maven

Zadejte následující příkaz a vytvořte tak projekt Maven s názvem **WORDCOUNT**:

```cmd
mvn archetype:generate -DarchetypeArtifactId=maven-archetype-quickstart -DgroupId=com.microsoft.example -DartifactId=WordCount -DinteractiveMode=false

cd WordCount
mkdir resources
```

Tento příkaz vytvoří adresář s názvem `WordCount` v aktuálním umístění, který obsahuje základní projekt Maven. Druhý příkaz změní stávající pracovní adresář na `WordCount` . Třetí příkaz vytvoří nový adresář, `resources` který bude později použit.  `WordCount`Adresář obsahuje následující položky:

* `pom.xml`: Obsahuje nastavení pro projekt Maven.
* `src\main\java\com\microsoft\example`: Obsahuje kód vaší aplikace.
* `src\test\java\com\microsoft\example`: Obsahuje testy pro vaši aplikaci.  

### <a name="remove-the-generated-example-code"></a>Odebrat generovaný ukázkový kód

Odstraňte vygenerované soubory testů a aplikace `AppTest.java` a `App.java` zadáním následujících příkazů:

```cmd
DEL src\main\java\com\microsoft\example\App.java
DEL src\test\java\com\microsoft\example\AppTest.java
```

## <a name="add-maven-repositories"></a>Přidat úložiště Maven

HDInsight je založený na HDP (Hortonworks data Platform), proto doporučujeme použít úložiště Hortonworks ke stažení závislostí pro vaše projekty Apache Storm.  

Otevřete zadáním `pom.xml` následujícího příkazu:

```cmd
notepad pom.xml
```

Pak přidejte následující XML za `<url>https://maven.apache.org</url>` řádek:

```xml
<repositories>
    <repository>
        <releases>
            <enabled>true</enabled>
            <updatePolicy>always</updatePolicy>
            <checksumPolicy>warn</checksumPolicy>
        </releases>
        <snapshots>
            <enabled>false</enabled>
            <updatePolicy>never</updatePolicy>
            <checksumPolicy>fail</checksumPolicy>
        </snapshots>
        <id>HDPReleases</id>
        <name>HDP Releases</name>
        <url>https://repo.hortonworks.com/content/repositories/releases/</url>
        <layout>default</layout>
    </repository>
    <repository>
        <releases>
            <enabled>true</enabled>
            <updatePolicy>always</updatePolicy>
            <checksumPolicy>warn</checksumPolicy>
        </releases>
        <snapshots>
            <enabled>false</enabled>
            <updatePolicy>never</updatePolicy>
            <checksumPolicy>fail</checksumPolicy>
        </snapshots>
        <id>HDPJetty</id>
        <name>Hadoop Jetty</name>
        <url>https://repo.hortonworks.com/content/repositories/jetty-hadoop/</url>
        <layout>default</layout>
    </repository>
</repositories>
```

## <a name="add-properties"></a>Přidání vlastností

Maven umožňuje definovat hodnoty na úrovni projektu s názvem Properties (vlastnosti). Do `pom.xml` přidejte následující text za `</repositories>` řádek:

```xml
<properties>
    <project.build.sourceEncoding>UTF-8</project.build.sourceEncoding>
    <!--
    This is a version of Storm from the Hortonworks repository that is compatible with HDInsight 3.6.
    -->
    <storm.version>1.1.0.2.6.1.9-1</storm.version>
</properties>
```

Tuto hodnotu teď můžete použít v dalších oddílech `pom.xml` . Například při určení verze nenáročné komponenty můžete použít `${storm.version}` místo pevného kódování hodnoty.

## <a name="add-dependencies"></a>Přidat závislosti

Přidejte závislost pro součásti pro zaplavení. V `pom.xml` přidejte do oddílu následující text `<dependencies>` :

```xml
<dependency>
    <groupId>org.apache.storm</groupId>
    <artifactId>storm-core</artifactId>
    <version>${storm.version}</version>
    <!-- keep storm out of the jar-with-dependencies -->
    <scope>provided</scope>
</dependency>
```

V době kompilace Maven používá tyto informace k vyhledání `storm-core` v úložišti Maven. Nejprve se podíváme na úložiště v místním počítači. Pokud tam soubory nejsou, Maven je stáhne z veřejného úložiště Maven a uloží je do místního úložiště.

> [!NOTE]  
> Všimněte si, že `<scope>provided</scope>` řádek v této části. Toto nastavení určuje, že Maven **vyloučí ze** všech vytvořených souborů JAR, aby nedošlo k tomu, že je k dispozici v systému.

## <a name="build-configuration"></a>Konfigurace sestavení

Moduly plug-in Maven umožňují přizpůsobit fáze sestavení projektu. Například způsob kompilace projektu nebo jeho zabalení do souboru JAR. Do `pom.xml` přidejte následující text přímo nad `</project>` řádek.

```xml
<build>
    <plugins>
    </plugins>
    <resources>
    </resources>
</build>
```

Tato část slouží k přidání modulů plug-in, prostředků a dalších možností konfigurace sestavení. Úplný odkaz na `pom.xml` soubor naleznete v tématu [https://maven.apache.org/pom.html](https://maven.apache.org/pom.html) .

### <a name="add-plug-ins"></a>Přidat moduly plug-in

* **Modul plug-in Maven exec**

    U Apache Storm topologií implementovaných v jazyce Java je [modul plug-in exec Maven](https://www.mojohaus.org/exec-maven-plugin/) užitečný, protože umožňuje snadno spustit topologii místně ve vašem vývojovém prostředí. Do části souboru přidejte následující `<plugins>` `pom.xml` obsah, aby zahrnoval modul plug-in exec Maven:

    ```xml
    <plugin>
        <groupId>org.codehaus.mojo</groupId>
        <artifactId>exec-maven-plugin</artifactId>
        <version>1.6.0</version>
        <executions>
            <execution>
                <goals>
                    <goal>exec</goal>
                </goals>
            </execution>
        </executions>
        <configuration>
            <executable>java</executable>
            <includeProjectDependencies>true</includeProjectDependencies>
            <includePluginDependencies>false</includePluginDependencies>
            <classpathScope>compile</classpathScope>
            <mainClass>${storm.topology}</mainClass>
            <cleanupDaemonThreads>false</cleanupDaemonThreads>
        </configuration>
    </plugin>
    ```

* **Modul plug-in kompilátoru Apache Maven**

    Dalším užitečným modulem plug-in je [`Apache Maven Compiler Plugin`](https://maven.apache.org/plugins/maven-compiler-plugin/) , který se používá ke změně možností kompilace. Změňte verzi Java, kterou Maven používá pro zdroj a cíl vaší aplikace.

  * Pro HDInsight __3,4 nebo starší__ nastavte zdrojovou a cílovou verzi Java na __1,7__.

  * V případě HDInsight __3,5__ nastavte zdrojovou a cílovou verzi Java na __1,8__.

  Do části souboru přidejte následující text `<plugins>` `pom.xml` , který bude zahrnovat modul plug-in Maven pro Apache. Tento příklad určuje 1,8, takže cílová verze HDInsight je 3,5.

  ```xml
  <plugin>
    <groupId>org.apache.maven.plugins</groupId>
    <artifactId>maven-compiler-plugin</artifactId>
    <version>3.8.1</version>
    <configuration>
            <source>1.8</source>
            <target>1.8</target>
    </configuration>
  </plugin>
  ```

### <a name="configure-resources"></a>Konfigurace prostředků

V části Resources (prostředky) můžete zahrnout nekódové zdroje, jako jsou třeba konfigurační soubory, které jsou potřeba součástmi v topologii. V tomto příkladu přidejte následující text do `<resources>` části `pom.xml` souboru. Pak soubor uložte a zavřete ho.

```xml
<resource>
    <directory>${basedir}/resources</directory>
    <filtering>false</filtering>
    <includes>
            <include>log4j2.xml</include>
    </includes>
</resource>
```

Tento příklad přidá adresář prostředků do kořenového adresáře projektu ( `${basedir}` ) jako umístění, které obsahuje prostředky a obsahuje soubor s názvem `log4j2.xml` . Tento soubor slouží ke konfiguraci informací, které jsou protokolovány pomocí topologie.

## <a name="create-the-topology"></a>Vytvoření topologie

Apache Storm topologie založené na jazyce Java se skládá ze tří komponent, které musíte vytvořit (nebo referenční) jako závislost.

* **Spoutů**: čte data z externích zdrojů a vysílá proudy dat do topologie.

* **Šrouby**: zpracovává zpracování streamů emitovaných spoutů nebo jiným šrouby a vysílá jeden nebo více datových proudů.

* **Topologie**: definuje způsob uspořádání spoutů a šrouby a poskytuje vstupní bod pro topologii.

### <a name="create-the-spout"></a>Vytvoření Spout

Aby se snížily požadavky na nastavení externích zdrojů dat, následující Spout jednoduše vygeneruje náhodné věty. Jedná se o upravenou verzi Spout, která je k dispozici v příkladech pro zaplavování [– Starter](https://github.com/apache/storm/blob/0.10.x-branch/examples/storm-starter/src/jvm/storm/starter).  I když tato topologie používá jeden Spout, ostatní můžou mít několik datových kanálů z různých zdrojů do topologie.`.`

Zadáním následujícího příkazu vytvořte a otevřete nový soubor `RandomSentenceSpout.java` :

```cmd
notepad src\main\java\com\microsoft\example\RandomSentenceSpout.java
```

Pak zkopírujte a vložte kód Java níže do nového souboru.  Pak soubor zavřete.

```java
package com.microsoft.example;

import org.apache.storm.spout.SpoutOutputCollector;
import org.apache.storm.task.TopologyContext;
import org.apache.storm.topology.OutputFieldsDeclarer;
import org.apache.storm.topology.base.BaseRichSpout;
import org.apache.storm.tuple.Fields;
import org.apache.storm.tuple.Values;
import org.apache.storm.utils.Utils;

import java.util.Map;
import java.util.Random;

//This spout randomly emits sentences
public class RandomSentenceSpout extends BaseRichSpout {
  //Collector used to emit output
  SpoutOutputCollector _collector;
  //Used to generate a random number
  Random _rand;

  //Open is called when an instance of the class is created
  @Override
  public void open(Map conf, TopologyContext context, SpoutOutputCollector collector) {
  //Set the instance collector to the one passed in
    _collector = collector;
    //For randomness
    _rand = new Random();
  }

  //Emit data to the stream
  @Override
  public void nextTuple() {
  //Sleep for a bit
    Utils.sleep(100);
    //The sentences that are randomly emitted
    String[] sentences = new String[]{ "the cow jumped over the moon", "an apple a day keeps the doctor away",
        "four score and seven years ago", "snow white and the seven dwarfs", "i am at two with nature" };
    //Randomly pick a sentence
    String sentence = sentences[_rand.nextInt(sentences.length)];
    //Emit the sentence
    _collector.emit(new Values(sentence));
  }

  //Ack is not implemented since this is a basic example
  @Override
  public void ack(Object id) {
  }

  //Fail is not implemented since this is a basic example
  @Override
  public void fail(Object id) {
  }

  //Declare the output fields. In this case, an sentence
  @Override
  public void declareOutputFields(OutputFieldsDeclarer declarer) {
    declarer.declare(new Fields("sentence"));
  }
}
```

> [!NOTE]  
> Příklad Spout, který čte z externího zdroje dat, naleznete v jednom z následujících příkladů:
>
> * [TwitterSampleSPout](https://github.com/apache/storm/blob/0.10.x-branch/examples/storm-starter/src/jvm/storm/starter/spout/TwitterSampleSpout.java): příklad Spout, který čte z Twitteru.
> * [Kafka](https://github.com/apache/storm/tree/0.10.x-branch/external/storm-kafka): Spout, který čte z Kafka.

### <a name="create-the-bolts"></a>Vytvořit šrouby

Šrouby zpracuje zpracování dat. Šrouby může dělat cokoli, například výpočet, trvalost nebo vzmluvit s externími součástmi. Tato topologie používá dvě šrouby:

* **SplitSentence**: rozdělí věty vyslané **RandomSentenceSpout** na jednotlivá slova.

* **WORDCOUNT**: spočítá počet výskytů jednotlivých slov.

#### <a name="splitsentence"></a>SplitSentence

Zadáním následujícího příkazu vytvořte a otevřete nový soubor `SplitSentence.java` :

```cmd
notepad src\main\java\com\microsoft\example\SplitSentence.java
```

Pak zkopírujte a vložte kód Java níže do nového souboru.  Pak soubor zavřete.

```java
package com.microsoft.example;

import java.text.BreakIterator;

import org.apache.storm.topology.BasicOutputCollector;
import org.apache.storm.topology.OutputFieldsDeclarer;
import org.apache.storm.topology.base.BaseBasicBolt;
import org.apache.storm.tuple.Fields;
import org.apache.storm.tuple.Tuple;
import org.apache.storm.tuple.Values;

//There are a variety of bolt types. In this case, use BaseBasicBolt
public class SplitSentence extends BaseBasicBolt {

  //Execute is called to process tuples
  @Override
  public void execute(Tuple tuple, BasicOutputCollector collector) {
    //Get the sentence content from the tuple
    String sentence = tuple.getString(0);
    //An iterator to get each word
    BreakIterator boundary=BreakIterator.getWordInstance();
    //Give the iterator the sentence
    boundary.setText(sentence);
    //Find the beginning first word
    int start=boundary.first();
    //Iterate over each word and emit it to the output stream
    for (int end=boundary.next(); end != BreakIterator.DONE; start=end, end=boundary.next()) {
      //get the word
      String word=sentence.substring(start,end);
      //If a word is whitespace characters, replace it with empty
      word=word.replaceAll("\\s+","");
      //if it's an actual word, emit it
      if (!word.equals("")) {
        collector.emit(new Values(word));
      }
    }
  }

  //Declare that emitted tuples contain a word field
  @Override
  public void declareOutputFields(OutputFieldsDeclarer declarer) {
    declarer.declare(new Fields("word"));
  }
}
```

#### <a name="wordcount"></a>WordCount

Zadáním následujícího příkazu vytvořte a otevřete nový soubor `WordCount.java` :

```cmd
notepad src\main\java\com\microsoft\example\WordCount.java
```

Pak zkopírujte a vložte kód Java níže do nového souboru.  Pak soubor zavřete.

```java
package com.microsoft.example;

import java.util.HashMap;
import java.util.Map;
import java.util.Iterator;

import org.apache.storm.Constants;
import org.apache.storm.topology.BasicOutputCollector;
import org.apache.storm.topology.OutputFieldsDeclarer;
import org.apache.storm.topology.base.BaseBasicBolt;
import org.apache.storm.tuple.Fields;
import org.apache.storm.tuple.Tuple;
import org.apache.storm.tuple.Values;
import org.apache.storm.Config;

// For logging
import org.apache.logging.log4j.Logger;
import org.apache.logging.log4j.LogManager;

//There are a variety of bolt types. In this case, use BaseBasicBolt
public class WordCount extends BaseBasicBolt {
  //Create logger for this class
  private static final Logger logger = LogManager.getLogger(WordCount.class);
  //For holding words and counts
  Map<String, Integer> counts = new HashMap<String, Integer>();
  //How often to emit a count of words
  private Integer emitFrequency;

  // Default constructor
  public WordCount() {
      emitFrequency=5; // Default to 60 seconds
  }

  // Constructor that sets emit frequency
  public WordCount(Integer frequency) {
      emitFrequency=frequency;
  }

  //Configure frequency of tick tuples for this bolt
  //This delivers a 'tick' tuple on a specific interval,
  //which is used to trigger certain actions
  @Override
  public Map<String, Object> getComponentConfiguration() {
      Config conf = new Config();
      conf.put(Config.TOPOLOGY_TICK_TUPLE_FREQ_SECS, emitFrequency);
      return conf;
  }

  //execute is called to process tuples
  @Override
  public void execute(Tuple tuple, BasicOutputCollector collector) {
    //If it's a tick tuple, emit all words and counts
    if(tuple.getSourceComponent().equals(Constants.SYSTEM_COMPONENT_ID)
            && tuple.getSourceStreamId().equals(Constants.SYSTEM_TICK_STREAM_ID)) {
      for(String word : counts.keySet()) {
        Integer count = counts.get(word);
        collector.emit(new Values(word, count));
        logger.info("Emitting a count of " + count + " for word " + word);
      }
    } else {
      //Get the word contents from the tuple
      String word = tuple.getString(0);
      //Have we counted any already?
      Integer count = counts.get(word);
      if (count == null)
        count = 0;
      //Increment the count and store it
      count++;
      counts.put(word, count);
    }
  }

  //Declare that this emits a tuple containing two fields; word and count
  @Override
  public void declareOutputFields(OutputFieldsDeclarer declarer) {
    declarer.declare(new Fields("word", "count"));
  }
}
```

### <a name="define-the-topology"></a>Definování topologie

Topologie spojuje spoutů a šrouby do grafu. Graf definuje tok dat mezi součástmi. Poskytuje také pomocný parametr paralelismus, který využívá při vytváření instancí součástí v clusteru.

Následující obrázek je základní diagram grafu komponent pro tuto topologii.

![Diagram znázorňující uspořádání spoutů a šrouby](./media/apache-storm-develop-java-topology/word-count-topology1.png)

Chcete-li implementovat topologii, zadejte následující příkaz pro vytvoření a otevření nového souboru `WordCountTopology.java` :

```cmd
notepad src\main\java\com\microsoft\example\WordCountTopology.java
```

Pak zkopírujte a vložte kód Java níže do nového souboru.  Pak soubor zavřete.

```java
package com.microsoft.example;

import org.apache.storm.Config;
import org.apache.storm.LocalCluster;
import org.apache.storm.StormSubmitter;
import org.apache.storm.topology.TopologyBuilder;
import org.apache.storm.tuple.Fields;

import com.microsoft.example.RandomSentenceSpout;

public class WordCountTopology {

  //Entry point for the topology
  public static void main(String[] args) throws Exception {
  //Used to build the topology
    TopologyBuilder builder = new TopologyBuilder();
    //Add the spout, with a name of 'spout'
    //and parallelism hint of 5 executors
    builder.setSpout("spout", new RandomSentenceSpout(), 5);
    //Add the SplitSentence bolt, with a name of 'split'
    //and parallelism hint of 8 executors
    //shufflegrouping subscribes to the spout, and equally distributes
    //tuples (sentences) across instances of the SplitSentence bolt
    builder.setBolt("split", new SplitSentence(), 8).shuffleGrouping("spout");
    //Add the counter, with a name of 'count'
    //and parallelism hint of 12 executors
    //fieldsgrouping subscribes to the split bolt, and
    //ensures that the same word is sent to the same instance (group by field 'word')
    builder.setBolt("count", new WordCount(), 12).fieldsGrouping("split", new Fields("word"));

    //new configuration
    Config conf = new Config();
    //Set to false to disable debug information when
    // running in production on a cluster
    conf.setDebug(false);

    //If there are arguments, we are running on a cluster
    if (args != null && args.length > 0) {
      //parallelism hint to set the number of workers
      conf.setNumWorkers(3);
      //submit the topology
      StormSubmitter.submitTopology(args[0], conf, builder.createTopology());
    }
    //Otherwise, we are running locally
    else {
      //Cap the maximum number of executors that can be spawned
      //for a component to 3
      conf.setMaxTaskParallelism(3);
      //LocalCluster is used to run locally
      LocalCluster cluster = new LocalCluster();
      //submit the topology
      cluster.submitTopology("word-count", conf, builder.createTopology());
      //sleep
      Thread.sleep(10000);
      //shut down the cluster
      cluster.shutdown();
    }
  }
}
```

### <a name="configure-logging"></a>Konfigurovat protokolování

K protokolování informací používá protokol [Apache log4j 2](https://logging.apache.org/log4j/2.x/) . Pokud protokolování nenakonfigurujete, vygeneruje tato topologie diagnostické informace. Chcete-li řídit, co je zaznamenáno, vytvořte soubor s názvem `log4j2.xml` v `resources` adresáři zadáním následujícího příkazu:

```cmd
notepad resources\log4j2.xml
```

Potom zkopírujte a vložte text XML níže do nového souboru.  Pak soubor zavřete.

```xml
<?xml version="1.0" encoding="UTF-8"?>
<Configuration>
    <Appenders>
        <Console name="STDOUT" target="SYSTEM_OUT">
            <PatternLayout pattern="%d{HH:mm:ss} [%t] %-5level %logger{36} - %msg%n"/>
        </Console>
    </Appenders>
    <Loggers>
        <Logger name="com.microsoft.example" level="trace" additivity="false">
            <AppenderRef ref="STDOUT"/>
        </Logger>
        <Root level="error">
            <Appender-Ref ref="STDOUT"/>
        </Root>
    </Loggers>
</Configuration>
```

Tento kód XML nakonfiguruje nový protokolovací nástroj pro `com.microsoft.example` třídu, která obsahuje komponenty v této ukázkové topologii. Úroveň je nastavená na trasování pro tento protokolovací nástroj, který zachycuje všechny informace o protokolování vydávané součástmi v této topologii.

V `<Root level="error">` části se konfiguruje kořenová úroveň protokolování (vše není v `com.microsoft.example` ), aby se zaprotokoloval jenom informace o chybě.

Další informace o konfiguraci protokolování pro log4j 2 najdete v tématu [https://logging.apache.org/log4j/2.x/manual/configuration.html](https://logging.apache.org/log4j/2.x/manual/configuration.html) .

> [!NOTE]  
> Ne0.10.0 verze a vyšší použití log4j 2. x. Starší verze systému log4j používaly 1. x, které používaly jiný formát pro konfiguraci protokolu. Informace o starší konfiguraci najdete v tématu [https://cwiki.apache.org/confluence/display/LOGGINGLOG4J/Log4jXmlFormat](https://cwiki.apache.org/confluence/display/LOGGINGLOG4J/Log4jXmlFormat) .

## <a name="test-the-topology-locally"></a>Místní otestování topologie

Po uložení souborů použijte následující příkaz k otestování topologie místně.

```cmd
mvn compile exec:java -Dstorm.topology=com.microsoft.example.WordCountTopology
```

Při spuštění topologie zobrazí informace o spuštění. Následující text je příkladem výstupu počtu slov:

```output
17:33:27 [Thread-12-count] INFO  com.microsoft.example.WordCount - Emitting a count of 56 for word snow
17:33:27 [Thread-12-count] INFO  com.microsoft.example.WordCount - Emitting a count of 56 for word white
17:33:27 [Thread-12-count] INFO  com.microsoft.example.WordCount - Emitting a count of 112 for word seven
17:33:27 [Thread-16-count] INFO  com.microsoft.example.WordCount - Emitting a count of 195 for word the
17:33:27 [Thread-30-count] INFO  com.microsoft.example.WordCount - Emitting a count of 113 for word and
17:33:27 [Thread-30-count] INFO  com.microsoft.example.WordCount - Emitting a count of 57 for word dwarfs
17:33:27 [Thread-12-count] INFO  com.microsoft.example.WordCount - Emitting a count of 57 for word snow
```

Tento ukázkový protokol indikuje, že slovo a bylo vygenerováno 113 krát. Počet se stále zvětšuje, dokud se topologie spustí. Toto zvýšení je, protože Spout průběžně generuje stejné věty.

Mezi emisemi slov a počtů je interval 5 sekund. Komponenta **WORDCOUNT** je nakonfigurována tak, aby vygenerovala pouze informace, když dorazí do řazené kolekce členů. Vyžádá, aby se řazené kolekce členů prodávaly jenom každých pět sekund.

## <a name="convert-the-topology-to-flux"></a>Převod topologie na tok

[Tok](https://storm.apache.org/releases/2.0.0/flux.html) je nová architektura dostupná s využitím 0.10.0 a vyšší. Tok umožňuje oddělit konfiguraci od implementace. Vaše komponenty jsou pořád definované v jazyce Java, ale topologie je definovaná pomocí souboru YAML. Můžete zabalit výchozí definici topologie s vaším projektem nebo při odesílání topologie použít samostatný soubor. Při odesílání topologie k vyplavování použijte proměnné prostředí nebo konfigurační soubory k naplnění hodnot definice topologie YAML.

Soubor YAML definuje komponenty, které se mají použít pro topologii a tok dat mezi nimi. Soubor YAML můžete zahrnout jako součást souboru jar. Můžete také použít externí soubor YAML.

Další informace o toku najdete v tématu [tok rozhraní ( https://storm.apache.org/releases/current/flux.html) ](https://storm.apache.org/releases/current/flux.html).

> [!WARNING]  
> Kvůli [chybě ( https://issues.apache.org/jira/browse/STORM-2055) ](https://issues.apache.org/jira/browse/STORM-2055) s využitím systému 1.0.1 se může stát, že budete muset nainstalovat [vývojové prostředí](https://storm.apache.org/releases/current/Setting-up-development-environment.html) s více systémy, aby bylo možné místně spouštět topologie toků.

1. Dřív `WordCountTopology.java` definovala topologii, ale u toku není potřeba. Odstraňte soubor pomocí následujícího příkazu:

    ```cmd
    DEL src\main\java\com\microsoft\example\WordCountTopology.java
    ```

1. Zadáním následujícího příkazu vytvořte a otevřete nový soubor `topology.yaml` :

    ```cmd
    notepad resources\topology.yaml
    ```

    Pak zkopírujte a vložte text uvedený níže do nového souboru.  Pak soubor zavřete.

    ```yaml
    name: "wordcount"       # friendly name for the topology

    config:                 # Topology configuration
           topology.workers: 1     # Hint for the number of workers to create
  
    spouts:                 # Spout definitions
    - id: "sentence-spout"
           className: "com.microsoft.example.RandomSentenceSpout"
           parallelism: 1      # parallelism hint

    bolts:                  # Bolt definitions
    - id: "splitter-bolt"
           className: "com.microsoft.example.SplitSentence"
           parallelism: 1

    - id: "counter-bolt"
           className: "com.microsoft.example.WordCount"
           constructorArgs:
             - 10
           parallelism: 1

    streams:                # Stream definitions
    - name: "Spout --> Splitter" # name isn't used (placeholder for logging, UI, etc.)
           from: "sentence-spout"       # The stream emitter
           to: "splitter-bolt"          # The stream consumer
           grouping:                    # Grouping type
             type: SHUFFLE

    - name: "Splitter -> Counter"
           from: "splitter-bolt"
           to: "counter-bolt"
           grouping:
             type: FIELDS
             args: ["word"]           # field(s) to group on
    ```

1. Zadejte následující příkaz, který se otevře, `pom.xml` aby byly popsané revize uvedené níže:

    ```cmd
    notepad pom.xml
    ```

   1. Do oddílu přidejte následující novou závislost `<dependencies>` :

        ```xml
        <!-- Add a dependency on the Flux framework -->
        <dependency>
            <groupId>org.apache.storm</groupId>
            <artifactId>flux-core</artifactId>
            <version>${storm.version}</version>
        </dependency>
        ```

   1. Do oddílu přidejte následující modul plug-in `<plugins>` . Tento modul plug-in zpracovává vytvoření balíčku (souboru jar) pro projekt a při vytváření balíčku aplikuje některé transformace specifické pro tok.

        ```xml
        <!-- build an uber jar -->
        <plugin>
            <groupId>org.apache.maven.plugins</groupId>
            <artifactId>maven-shade-plugin</artifactId>
            <version>3.2.1</version>
            <configuration>
                <transformers>
                    <!-- Keep us from getting a "can't overwrite file error" -->
                    <transformer implementation="org.apache.maven.plugins.shade.resource.ApacheLicenseResourceTransformer" />
                    <transformer implementation="org.apache.maven.plugins.shade.resource.ServicesResourceTransformer" />
                    <!-- We're using Flux, so refer to it as main -->
                    <transformer implementation="org.apache.maven.plugins.shade.resource.ManifestResourceTransformer">
                        <mainClass>org.apache.storm.flux.Flux</mainClass>
                    </transformer>
                </transformers>
                <!-- Keep us from getting a bad signature error -->
                <filters>
                    <filter>
                        <artifact>*:*</artifact>
                        <excludes>
                            <exclude>META-INF/*.SF</exclude>
                            <exclude>META-INF/*.DSA</exclude>
                            <exclude>META-INF/*.RSA</exclude>
                        </excludes>
                    </filter>
                </filters>
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
        ```

   1. V části modul plug-in exec Maven přejděte na adresu `<configuration>`  >  `<mainClass>` a změňte `${storm.topology}` na `org.apache.storm.flux.Flux` . Toto nastavení umožňuje, aby tok zpracovával místně probíhající topologii.

   1. V `<resources>` části přidejte následující do `<includes>` . Tento kód XML obsahuje soubor YAML, který definuje topologii v rámci projektu.

        ```xml
        <include>topology.yaml</include>
        ```

## <a name="test-the-flux-topology-locally"></a>Místní testování topologie toků

1. Zadejte následující příkaz pro zkompilování a spuštění topologie toků pomocí Maven:

    ```cmd
    mvn compile exec:java -Dexec.args="--local -R /topology.yaml"
    ```

    > [!WARNING]  
    > Pokud vaše topologie používá 1.0.1 bitů, tento příkaz se nezdařil. Tato chyba je způsobena nástrojem [https://issues.apache.org/jira/browse/STORM-2055](https://issues.apache.org/jira/browse/STORM-2055) . Místo toho [nainstalujte do vývojového prostředí](https://storm.apache.org/releases/current/Setting-up-development-environment.html) vše a použijte následující postup:
    >
    > Pokud máte [ve svém vývojovém prostředí nainstalovanou](https://storm.apache.org/releases/current/Setting-up-development-environment.html)práci, můžete místo toho použít následující příkazy:
    >
    > ```cmd
    > mvn compile package
    > storm jar target/WordCount-1.0-SNAPSHOT.jar org.apache.storm.flux.Flux --local -R /topology.yaml
    > ```

    `--local`Parametr spustí topologii v místním režimu ve vývojovém prostředí. `-R /topology.yaml`Parametr používá `topology.yaml` soubor prostředků ze souboru jar k definování topologie.

    Při spuštění topologie zobrazí informace o spuštění. Následující text je příklad výstupu:

    ```
    17:33:27 [Thread-12-count] INFO  com.microsoft.example.WordCount - Emitting a count of 56 for word snow
    17:33:27 [Thread-12-count] INFO  com.microsoft.example.WordCount - Emitting a count of 56 for word white
    17:33:27 [Thread-12-count] INFO  com.microsoft.example.WordCount - Emitting a count of 112 for word seven
    17:33:27 [Thread-16-count] INFO  com.microsoft.example.WordCount - Emitting a count of 195 for word the
    17:33:27 [Thread-30-count] INFO  com.microsoft.example.WordCount - Emitting a count of 113 for word and
    17:33:27 [Thread-30-count] INFO  com.microsoft.example.WordCount - Emitting a count of 57 for word dwarfs
    ```

    Mezi dávkami protokolovaných informací je prodleva o délce 10 sekund.

2. Vytvoří novou topologii YAML z projektu.

    1. Zadejte následující příkaz, který chcete otevřít `topology.xml` :

    ```cmd
    notepad resources\topology.yaml
    ```

    1. Vyhledejte následující část a změňte hodnotu `10` na `5` . Tato změna změní interval mezi vygenerováním dávek slov od 10 sekund do 5.  

    ```yaml
    - id: "counter-bolt"
           className: "com.microsoft.example.WordCount"
           constructorArgs:
             - 5
           parallelism: 1  
    ```

    1. Uložte soubor jako `newtopology.yaml` .

3. Pokud chcete spustit topologii, zadejte následující příkaz:

    ```cmd
    mvn exec:java -Dexec.args="--local resources/newtopology.yaml"
    ```

    Nebo, pokud máte ve svém vývojovém prostředí vše:

    ```cmd
    storm jar target/WordCount-1.0-SNAPSHOT.jar org.apache.storm.flux.Flux --local resources/newtopology.yaml
    ```

    Tento příkaz používá `newtopology.yaml` jako definici topologie. Vzhledem k tomu, že jsme parametr nezahrnuli `compile` , používá Maven verzi projektu sestavené v předchozích krocích.

    Po spuštění topologie byste si měli všimnout, že se změnil čas mezi vygenerovanými dávkami tak, aby odrážel hodnotu v `newtopology.yaml` . Takže vidíte, že můžete změnit konfiguraci prostřednictvím souboru YAML, aniž byste museli znovu kompilovat topologii.

Další informace o těchto a dalších funkcích rozhraní toků najdete v tématu [tok ( https://storm.apache.org/releases/current/flux.html) ](https://storm.apache.org/releases/current/flux.html).

## <a name="trident"></a>Trident

[Trident](https://storm.apache.org/releases/current/Trident-API-Overview.html) je abstrakce na vysoké úrovni, která je poskytována pomocí průchozího. Podporuje stavové zpracování. Primární výhodou Trident je, že zaručuje, že všechny zprávy, které vstupují do topologie, se zpracovávají jenom jednou. Bez použití Trident může vaše topologie zaručit, že se zprávy zpracovávají aspoň jednou. K dispozici jsou také jiné rozdíly, například integrované komponenty, které lze použít místo vytvoření šrouby. Šrouby jsou nahrazeny méně obecnými součástmi, jako jsou filtry, projekce a funkce.

Aplikace Trident lze vytvořit pomocí projektů Maven. Použijete stejný základní postup, jak je uvedeno výše v tomto článku – pouze kód je jiný. Trident také nemůže (aktuálně) používat s rozhraním toků.

Další informace o Trident naleznete v tématu [Přehled rozhraní Trident API](https://storm.apache.org/releases/current/Trident-API-Overview.html).

## <a name="next-steps"></a>Další kroky

Zjistili jste, jak vytvořit topologii Apache Storm pomocí Java. Teď se dozvíte, jak:

* [Nasazení a Správa topologií Apache Storm v HDInsight](apache-storm-deploy-monitor-topology-linux.md)

* [Vývoj topologií pomocí Pythonu](apache-storm-develop-python-topology.md)

Další příklady Apache Storm topologií najdete [v tématu Příklady topologií pro Apache Storm v HDInsight](apache-storm-example-topology.md).