---
title: Apache Storm příklad java topologie - Azure HDInsight
description: Naučte se vytvářet topologie Apache Storm v Javě vytvořením ukázkové topologie počtu slov.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.date: 03/14/2019
ms.custom: H1Hack27Feb2017,hdinsightactive,hdiseo17may2017
ms.openlocfilehash: 75100b47ddf8f36ed9a22ff3073c439f8ad9040b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "74083297"
---
# <a name="create-an-apache-storm-topology-in-java"></a>Vytvoření topologie Apache Storm v Javě

Přečtěte si, jak vytvořit topologii založenou na Javě pro [Apache Storm](https://storm.apache.org/). Zde vytvoříte topologii Storm, která implementuje aplikaci počtu slov. Pomocí [Apache Maven](https://maven.apache.org/) můžete vytvořit a balíček projektu. Potom se dozvíte, jak definovat topologii pomocí rámce [Apache Storm Flux.](https://storm.apache.org/releases/2.0.0/flux.html)

Po dokončení kroků v tomto dokumentu můžete nasadit topologii do Apache Storm na HDInsight.

> [!NOTE]  
> Dokončená verze příkladů topologie bouře vytvořená v [https://github.com/Azure-Samples/hdinsight-java-storm-wordcount](https://github.com/Azure-Samples/hdinsight-java-storm-wordcount)tomto dokumentu je k dispozici na adrese .

## <a name="prerequisites"></a>Požadavky

* [Java Developer Kit (JDK) verze 8](https://aka.ms/azure-jdks)

* [Apache Maven](https://maven.apache.org/download.cgi) správně [nainstalován](https://maven.apache.org/install.html) podle Apache.  Maven je systém vytváření projektů pro java projekty.

## <a name="test-environment"></a>Testovací prostředí

Prostředí používané pro tento článek byl počítač se systémem Windows 10.  Příkazy byly provedeny v příkazovém řádku a různé soubory byly upraveny pomocí poznámkového bloku.

Z příkazového řádku zadejte níže uvedené příkazy a vytvořte pracovní prostředí:

```cmd
mkdir C:\HDI
cd C:\HDI
```

## <a name="create-a-maven-project"></a>Vytvoření projektu Maven

Chcete-li vytvořit projekt Maven s názvem **WordCount**, zadejte následující příkaz :

```cmd
mvn archetype:generate -DarchetypeArtifactId=maven-archetype-quickstart -DgroupId=com.microsoft.example -DartifactId=WordCount -DinteractiveMode=false

cd WordCount
mkdir resources
```

Tento příkaz vytvoří `WordCount` adresář pojmenovaný v aktuálním umístění, který obsahuje základní projekt Maven. Druhý příkaz změní současný pracovní `WordCount`adresář na . Třetí příkaz vytvoří nový `resources`adresář , který bude použit později.  Adresář `WordCount` obsahuje následující položky:

* `pom.xml`: Obsahuje nastavení projektu Maven.
* `src\main\java\com\microsoft\example`: Obsahuje kód aplikace.
* `src\test\java\com\microsoft\example`: Obsahuje testy pro vaši aplikaci.  

### <a name="remove-the-generated-example-code"></a>Odebrání generovaného ukázkového kódu

Odstraňte generované testovací a `AppTest.java`aplikační `App.java` soubory a zadáním následujících příkazů:

```cmd
DEL src\main\java\com\microsoft\example\App.java
DEL src\test\java\com\microsoft\example\AppTest.java
```

## <a name="add-maven-repositories"></a>Přidání repozitářů Maven

HDInsight je založen na datové platformě Hortonworks (HDP), takže doporučujeme použít úložiště Hortonworks ke stažení závislostí pro vaše projekty Apache Storm.  

Otevřít `pom.xml` zadáním níže uvedeného příkazu:

```cmd
notepad pom.xml
```

Pak za `<url>https://maven.apache.org</url>` řádek přidejte následující XML:

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

Maven umožňuje definovat hodnoty na úrovni projektu nazývané vlastnosti. V `pom.xml`písmenu a) `</repositories>` přidejte za řádek následující text:

```xml
<properties>
    <project.build.sourceEncoding>UTF-8</project.build.sourceEncoding>
    <!--
    This is a version of Storm from the Hortonworks repository that is compatible with HDInsight 3.6.
    -->
    <storm.version>1.1.0.2.6.1.9-1</storm.version>
</properties>
```

Nyní můžete tuto hodnotu použít `pom.xml`v jiných částech . Například při zadávání verze komponent Storm, můžete `${storm.version}` použít místo pevnékódování hodnotu.

## <a name="add-dependencies"></a>Přidání závislostí

Přidejte závislost pro komponenty Storm. V `pom.xml`oddíle přidejte `<dependencies>` následující text:

```xml
<dependency>
    <groupId>org.apache.storm</groupId>
    <artifactId>storm-core</artifactId>
    <version>${storm.version}</version>
    <!-- keep storm out of the jar-with-dependencies -->
    <scope>provided</scope>
</dependency>
```

V době kompilace Maven používá `storm-core` tyto informace k vyhledat v úložišti Maven. Nejprve se podívá do úložiště v místním počítači. Pokud soubory nejsou k dispozici, Maven je stáhne z veřejného úložiště Maven a uloží je do místního úložiště.

> [!NOTE]  
> Všimněte `<scope>provided</scope>` si řádku v této části. Toto nastavení říká Maven vyloučit **storm-core** ze všech souborů JAR, které jsou vytvořeny, protože je poskytována systémem.

## <a name="build-configuration"></a>Konfigurace sestavení

Moduly plug-in Maven umožňují přizpůsobit fáze sestavení projektu. Například jak je projekt zkompilován nebo jak jej zabalit do souboru JAR. V `pom.xml`aplikaci `</project>` přidejte následující text přímo nad řádek.

```xml
<build>
    <plugins>
    </plugins>
    <resources>
    </resources>
</build>
```

Tato část slouží k přidání modulů plug-in, prostředků a dalších možností konfigurace sestavení. Úplný odkaz na `pom.xml` soubor naleznete [https://maven.apache.org/pom.html](https://maven.apache.org/pom.html)v tématu .

### <a name="add-plug-ins"></a>Přidání modulů plug-in

* **Modul plug-in Exec Maven**

    Pro topologie Apache Storm implementované v Javě je [plugin Exec Maven](https://www.mojohaus.org/exec-maven-plugin/) užitečný, protože umožňuje snadno spustit topologii lokálně ve vývojovém prostředí. Do části souboru `<plugins>` `pom.xml` přidejte následující:

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

* **Plugin kompilátoru Apache Maven**

    Dalším užitečným plug-in je [Apache Maven Compiler Plugin](https://maven.apache.org/plugins/maven-compiler-plugin/), který se používá ke změně možností kompilace. Změňte verzi Jazyka Java, kterou Maven používá pro zdroj a cíl pro vaši aplikaci.

  * U __hdinsightu 3.4 nebo staršího__nastavte zdrojovou a cílovou verzi Javy na __verzi 1.7__.

  * Pro HDInsight __3.5__nastavte zdrojovou a cílovou verzi Javy na __1.8__.

  Přidejte do `<plugins>` části souboru `pom.xml` následující text, který zahrne plugin Kompilátoru Apache Maven. Tento příklad určuje 1.8, takže cílová verze HDInsight je 3.5.

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

Část prostředky umožňuje zahrnout prostředky, které nejsou kódem, jako jsou konfigurační soubory potřebné součástmi v topologii. V tomto příkladu přidejte `<resources>` do části `pom.xml` souboru následující text. Pak tento soubor uložte a zavřete.

```xml
<resource>
    <directory>${basedir}/resources</directory>
    <filtering>false</filtering>
    <includes>
            <include>log4j2.xml</include>
    </includes>
</resource>
```

Tento příklad přidá adresář zdrojů do kořenového adresáře projektu (`${basedir}`) jako `log4j2.xml`umístění, které obsahuje zdroje, a obsahuje soubor s názvem . Tento soubor slouží ke konfiguraci, jaké informace jsou zaznamenány topologie.

## <a name="create-the-topology"></a>Vytvoření topologie

Topologie Apache Storm založená na javě se skládá ze tří součástí, které musíte vytvořit (nebo odkazovat) jako závislost.

* **Výtoky**: Čte data z externích zdrojů a vysílá proudy dat do topologie.

* **Šrouby**: Provádí zpracování datových proudů vyzařovaných výtokovými nebo jinými šrouby a vyzařuje jeden nebo více datových proudů.

* **Topologie**: Definuje, jak jsou uspořádány výtoky a šrouby, a poskytuje vstupní bod pro topologii.

### <a name="create-the-spout"></a>Vytvoření výtoku

Chcete-li snížit požadavky na nastavení externích zdrojů dat, následující výtok jednoduše vydává náhodné věty. Jedná se o upravenou verzi výtoku, která je dodávána s [příklady Storm-Starter](https://github.com/apache/storm/blob/0.10.x-branch/examples/storm-starter/src/jvm/storm/starter).  Ačkoli tato topologie používá pouze jeden výtok, jiní mohou mít několik, které dodávají data z různých zdrojů do topologie.

Chcete-li vytvořit a otevřít nový `RandomSentenceSpout.java`soubor, zadejte následující příkaz :

```cmd
notepad src\main\java\com\microsoft\example\RandomSentenceSpout.java
```

Pak zkopírujte a vložte java kód níže do nového souboru.  Potom soubor zavřete.

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
> Příklad výtoku, který čte z externího zdroje dat, naleznete v jednom z následujících příkladů:
>
> * [TwitterSampleSPout:](https://github.com/apache/storm/blob/0.10.x-branch/examples/storm-starter/src/jvm/storm/starter/spout/TwitterSampleSpout.java)Příklad výtoku, který čte z Twitteru.
> * [Storm-Kafka](https://github.com/apache/storm/tree/0.10.x-branch/external/storm-kafka): Výtok, který čte z Kafka.

### <a name="create-the-bolts"></a>Vytvoření šroubů

Šrouby zpracovávají zpracování dat. Šrouby mohou dělat cokoli, například výpočty, trvalost, nebo mluvit s externími součástmi. Tato topologie používá dva šrouby:

* **SplitSentence**: Rozdělí věty vyzařované **RandomSentenceSpout** na jednotlivá slova.

* **WordCount**: Spočítá, kolikrát došlo ke každému slovu.

#### <a name="splitsentence"></a>Splitsentence

Chcete-li vytvořit a otevřít nový `SplitSentence.java`soubor, zadejte následující příkaz :

```cmd
notepad src\main\java\com\microsoft\example\SplitSentence.java
```

Pak zkopírujte a vložte java kód níže do nového souboru.  Potom soubor zavřete.

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

#### <a name="wordcount"></a>Počet slov WordCount

Chcete-li vytvořit a otevřít nový `WordCount.java`soubor, zadejte následující příkaz :

```cmd
notepad src\main\java\com\microsoft\example\WordCount.java
```

Pak zkopírujte a vložte java kód níže do nového souboru.  Potom soubor zavřete.

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

Topologie spojuje výtoky a šrouby do grafu, který definuje, jak data toky mezi součástmi. Poskytuje také rady při paralelismu, které Storm používá při vytváření instancí součástí v rámci clusteru.

Následující obrázek je základním diagramem grafu komponent pro tuto topologii.

![diagram znázorňující uspořádání hubic a šroubů](./media/apache-storm-develop-java-topology/word-count-topology1.png)

Chcete-li implementovat topologii, zadejte níže uvedený `WordCountTopology.java`příkaz pro vytvoření a otevření nového souboru :

```cmd
notepad src\main\java\com\microsoft\example\WordCountTopology.java
```

Pak zkopírujte a vložte java kód níže do nového souboru.  Potom soubor zavřete.

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

### <a name="configure-logging"></a>Konfigurace protokolování

Storm používá [Apache Log4j 2](https://logging.apache.org/log4j/2.x/) k protokolování informací. Pokud protokolování nenakonfigurujete, topologie vyzařuje diagnostické informace. Chcete-li řídit, co je `log4j2.xml` zaznamenáno, vytvořte soubor pojmenovaný v `resources` adresáři zadáním následujícího příkazu:

```cmd
notepad resources\log4j2.xml
```

Poté zkopírujte a vložte níže uvedený text XML do nového souboru.  Potom soubor zavřete.

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

Tento jazyk XML konfiguruje nový protokolovací nástroj pro třídu, `com.microsoft.example` který zahrnuje součásti v tomto příkladu topologie. Úroveň je nastavena na trasování pro tento protokolovací nástroj, který zachycuje všechny informace protokolování vyzařované součástmi v této topologii.

V `<Root level="error">` části konfiguruje kořenovou `com.microsoft.example`úroveň protokolování (vše, co není v ) pouze protokolovat informace o chybě.

Další informace o konfiguraci protokolování pro [https://logging.apache.org/log4j/2.x/manual/configuration.html](https://logging.apache.org/log4j/2.x/manual/configuration.html)protokol log4j 2 naleznete v tématu .

> [!NOTE]  
> Storm verze 0.10.0 a vyšší použití Log4j 2.x. Starší verze storm ukládal Log4j 1.x, který používal jiný formát pro konfiguraci protokolu. Informace o starší konfiguraci [https://wiki.apache.org/logging-log4j/Log4jXmlFormat](https://wiki.apache.org/logging-log4j/Log4jXmlFormat)naleznete v tématu .

## <a name="test-the-topology-locally"></a>Testování topologie lokálně

Po uložení souborů otestujte topologii místně pomocí následujícího příkazu.

```cmd
mvn compile exec:java -Dstorm.topology=com.microsoft.example.WordCountTopology
```

Při spuštění topologie zobrazuje informace o spuštění. Následující text je příkladem výstupu počtu slov:

    17:33:27 [Thread-12-count] INFO  com.microsoft.example.WordCount - Emitting a count of 56 for word snow
    17:33:27 [Thread-12-count] INFO  com.microsoft.example.WordCount - Emitting a count of 56 for word white
    17:33:27 [Thread-12-count] INFO  com.microsoft.example.WordCount - Emitting a count of 112 for word seven
    17:33:27 [Thread-16-count] INFO  com.microsoft.example.WordCount - Emitting a count of 195 for word the
    17:33:27 [Thread-30-count] INFO  com.microsoft.example.WordCount - Emitting a count of 113 for word and
    17:33:27 [Thread-30-count] INFO  com.microsoft.example.WordCount - Emitting a count of 57 for word dwarfs
    17:33:27 [Thread-12-count] INFO  com.microsoft.example.WordCount - Emitting a count of 57 for word snow

Tento příklad protokolu označuje, že slovo 'a' byla vyzařována 113 krát. Počet pokračuje jít nahoru tak dlouho, dokud topologie běží, protože výtok nepřetržitě vydává stejné věty.

Mezi emisemi slov a počty je 5 sekund. Komponenta **WordCount** je nakonfigurována tak, aby vyzařovala informace pouze při příchodu n-tice značek. Požaduje, aby se kolekce členů značek doručovaly pouze každých pět sekund.

## <a name="convert-the-topology-to-flux"></a>Převést topologii na tavek

[Flux](https://storm.apache.org/releases/2.0.0/flux.html) je nový rámec k dispozici s Storm 0.10.0 a vyšší, který vám umožní oddělit konfiguraci od implementace. Vaše komponenty jsou stále definovány v jazyce Java, ale topologie je definována pomocí souboru YAML. Můžete zabalit definici výchozí topologie s projektem nebo použít samostatný soubor při odesílání topologie. Při odesílání topologie do stormu můžete použít proměnné prostředí nebo konfigurační soubory k naplnění hodnot v definici topologie YAML.

Soubor YAML definuje součásti, které mají být používány pro topologii a tok dat mezi nimi. Můžete zahrnout soubor YAML jako součást souboru jar nebo můžete použít externí soubor YAML.

Další informace o tavek naleznete v [tématu Fluxhttps://storm.apache.org/releases/current/flux.html)framework (](https://storm.apache.org/releases/current/flux.html).

> [!WARNING]  
> Kvůli [chybě (shttps://issues.apache.org/jira/browse/STORM-2055) ](https://issues.apache.org/jira/browse/STORM-2055) Storm 1.0.1, budete muset nainstalovat vývojové prostředí [Storm](https://storm.apache.org/releases/current/Setting-up-development-environment.html) spustit topologií Flux místně.

1. Dříve `WordCountTopology.java` definoval topologii, ale není potřeba s Flux. Odstraňte soubor pomocí následujícího příkazu:

    ```cmd
    DEL src\main\java\com\microsoft\example\WordCountTopology.java
    ```

1. Chcete-li vytvořit a otevřít nový `topology.yaml`soubor, zadejte následující příkaz :

    ```cmd
    notepad resources\topology.yaml
    ```

    Poté zkopírujte a vložte níže uvedený text do nového souboru.  Potom soubor zavřete.

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

1. Chcete-li otevřít `pom.xml` níže popsané revize, zadejte níže uvedený příkaz:

    ```cmd
    notepad pom.xml
    ```

   1. Do `<dependencies>` oddílu přidejte následující novou závislost:

        ```xml
        <!-- Add a dependency on the Flux framework -->
        <dependency>
            <groupId>org.apache.storm</groupId>
            <artifactId>flux-core</artifactId>
            <version>${storm.version}</version>
        </dependency>
        ```

   1. Do `<plugins>` sekce přidejte následující plugin. Tento plugin zpracovává vytvoření balíčku (jar soubor) pro projekt a použije některé transformace specifické pro Flux při vytváření balíčku.

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

   1. V části Modul plug-in Exec `<configuration>`  >  `<mainClass>` Maven přejděte na stránku a přejděte `${storm.topology}` na . `org.apache.storm.flux.Flux` Toto nastavení umožňuje společnosti Flux zpracovat spuštění topologie místně ve vývoji.

   1. V `<resources>` části přidejte do `<includes>`. Tento kód XML zahrnuje soubor YAML, který definuje topologii jako součást projektu.

        ```xml
        <include>topology.yaml</include>
        ```

## <a name="test-the-flux-topology-locally"></a>Test topologie toku lokálně

1. Zadejte následující příkaz pro kompilaci a spuštění topologie Flux pomocí mavenu:

    ```cmd
    mvn compile exec:java -Dexec.args="--local -R /topology.yaml"
    ```

    > [!WARNING]  
    > Pokud topologie používá storm 1.0.1 bitů, tento příkaz se nezdaří. Toto selhání [https://issues.apache.org/jira/browse/STORM-2055](https://issues.apache.org/jira/browse/STORM-2055)je způsobeno . Místo toho [nainstalujte Storm ve vývojovém prostředí](https://storm.apache.org/releases/current/Setting-up-development-environment.html) a použijte následující kroky:
    >
    > Pokud jste [aplikaci Storm nainstalovali ve vývojovém prostředí](https://storm.apache.org/releases/current/Setting-up-development-environment.html), můžete místo toho použít následující příkazy:
    >
    > ```cmd
    > mvn compile package
    > storm jar target/WordCount-1.0-SNAPSHOT.jar org.apache.storm.flux.Flux --local -R /topology.yaml
    > ```

    Parametr `--local` spustí topologii v místním režimu ve vývojovém prostředí. Parametr `-R /topology.yaml` používá `topology.yaml` prostředek souboru ze souboru jar k definování topologie.

    Při spuštění topologie zobrazuje informace o spuštění. Následující text je příkladem výstupu:

    ```
    17:33:27 [Thread-12-count] INFO  com.microsoft.example.WordCount - Emitting a count of 56 for word snow
    17:33:27 [Thread-12-count] INFO  com.microsoft.example.WordCount - Emitting a count of 56 for word white
    17:33:27 [Thread-12-count] INFO  com.microsoft.example.WordCount - Emitting a count of 112 for word seven
    17:33:27 [Thread-16-count] INFO  com.microsoft.example.WordCount - Emitting a count of 195 for word the
    17:33:27 [Thread-30-count] INFO  com.microsoft.example.WordCount - Emitting a count of 113 for word and
    17:33:27 [Thread-30-count] INFO  com.microsoft.example.WordCount - Emitting a count of 57 for word dwarfs
    ```

    Mezi jednotlivými dávkami protokolovaných informací je 10sekundová prodleva.

2. Vytvořte z projektu novou topologii yaml.

    1. Chcete-li otevřít, `topology.xml`zadejte níže uvedený příkaz :

    ```cmd
    notepad resources\topology.yaml
    ```

    1. Najděte následující část a `10` změňte hodnotu na `5`. Tato změna změní interval mezi emitujícími listy počtu slov z 10 sekund na 5.  

    ```yaml
    - id: "counter-bolt"
           className: "com.microsoft.example.WordCount"
           constructorArgs:
             - 5
           parallelism: 1  
    ```

    1. Uložit soubor `newtopology.yaml`jako .

3. Chcete-li spustit topologii, zadejte následující příkaz:

    ```cmd
    mvn exec:java -Dexec.args="--local resources/newtopology.yaml"
    ```

    Nebo pokud máte Storm na vývojovém prostředí:

    ```cmd
    storm jar target/WordCount-1.0-SNAPSHOT.jar org.apache.storm.flux.Flux --local resources/newtopology.yaml
    ```

    Tento příkaz `newtopology.yaml` používá jako definici topologie. Vzhledem k tomu, `compile` že jsme nezahrnuli parametr, Maven používá verzi projektu postavenou v předchozích krocích.

    Po spuštění topologie byste si měli všimnout, že doba mezi emitovanými dávkami se změnila tak, aby odrážela hodnotu v . `newtopology.yaml` Takže můžete vidět, že můžete změnit konfiguraci prostřednictvím souboru YAML bez nutnosti překompilovat topologii.

Další informace o těchto a dalších funkcích rozhraní Flux naleznete [v tématuhttps://storm.apache.org/releases/current/flux.html)Flux (](https://storm.apache.org/releases/current/flux.html).

## <a name="trident"></a>Trident

[Trident](https://storm.apache.org/releases/current/Trident-API-Overview.html) je abstrakce na vysoké úrovni, která je poskytována Storm. Podporuje stavové zpracování. Hlavní výhodou Trident je, že může zaručit, že každá zpráva, která vstoupí do topologie je zpracována pouze jednou. Bez použití Trident, topologie může zaručit pouze, že zprávy jsou zpracovány alespoň jednou. Existují také další rozdíly, například předdefinované součásti, které lze použít namísto vytváření šroubů. Šrouby jsou ve skutečnosti nahrazeny méně obecnými součástmi, jako jsou filtry, projekce a funkce.

Trident aplikace lze vytvořit pomocí projektů Maven. Použijete stejné základní kroky, jak je uvedeno dříve v tomto článku – pouze kód se liší. Trident také nelze (v současné době) použít s frameworkem Flux.

Další informace o Trident, naleznete [v Trident API Přehled](https://storm.apache.org/releases/current/Trident-API-Overview.html).

## <a name="next-steps"></a>Další kroky

Naučili jste se, jak vytvořit topologii Apache Storm pomocí Javy. Nyní se naučte, jak:

* [Nasazení a správa topologie Apache Storm na HDInsightu](apache-storm-deploy-monitor-topology-linux.md)

* [Vývoj topologií Jazyka C# pro Apache Storm na HDInsightu pomocí Visual Studia](apache-storm-develop-csharp-visual-studio-topology.md)

Další příklady topologie Apache Storm najdete na [stránce Příklad topologie apache storm na HDInsight](apache-storm-example-topology.md).
