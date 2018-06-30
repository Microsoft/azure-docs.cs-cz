---
title: Apache Storm příkladu Java topologie – Azure HDInsight | Microsoft Docs
description: Naučte se vytvářet topologií Apache Storm v jazyce Java tak, že vytvoříte ukázkové topologie počet aplikace word.
services: hdinsight
documentationcenter: ''
author: Blackmist
manager: jhubbard
editor: cgronlun
tags: azure-portal
keywords: Apache storm, například apache storm storm java, příklad topologie storm
ms.assetid: a8838f29-9c08-4fd9-99ef-26655d1bf6d7
ms.service: hdinsight
ms.devlang: java
ms.topic: conceptual
ms.date: 02/20/2018
ms.author: larryfr
ms.custom: H1Hack27Feb2017,hdinsightactive,hdiseo17may2017
ms.openlocfilehash: 105c10423a9688300ba74874769b162e29572185
ms.sourcegitcommit: 5892c4e1fe65282929230abadf617c0be8953fd9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/29/2018
ms.locfileid: "37128601"
---
# <a name="create-an-apache-storm-topology-in-java"></a>Vytvoření topologie Apache Storm v jazyce Java

Naučte se vytvářet topologii založené na jazyce Java pro Apache Storm. Můžete vytvořit topologie Storm, který implementuje počtu slov aplikace. Používáte Maven k sestavení a balíček projektu. Pak zjistíte, jak definovat topologie pomocí rozhraní tok.

Po dokončení kroků v tomto dokumentu, můžete nasadit topologie do Apache Storm v HDInsight.

> [!NOTE]
> Dokončené verze příkladů topologie Storm vytvořené v tomto dokumentu je k dispozici na [ https://github.com/Azure-Samples/hdinsight-java-storm-wordcount ](https://github.com/Azure-Samples/hdinsight-java-storm-wordcount).

## <a name="prerequisites"></a>Požadavky

* [Java Developer Kit (JDK) verze 8](http://www.oracle.com/technetwork/java/javase/downloads/jdk8-downloads-2133151.html)

* [Maven (https://maven.apache.org/download.cgi)](https://maven.apache.org/download.cgi): Maven je systém sestavení projektu pro projekty Java.

* Textového editoru nebo IDE.

## <a name="configure-environment-variables"></a>Konfigurace proměnných prostředí

Následující proměnné prostředí může být nastaven při instalaci Java a sadu JDK. Nicméně byste měli zkontrolovat, že existují a že obsahují hodnoty správné pro váš systém.

* **JAVA_HOME** -by měla odkazovat na adresář, kam nainstalovat prostředí Java runtime (JRE). Například v distribuci systému Unix nebo Linux, musí mít hodnotu podobnou `/usr/lib/jvm/java-8-oracle`. V systému Windows má hodnotu podobnou `c:\Program Files (x86)\Java\jre1.8`

* **CESTA** -musí obsahovat následující cesty:

  * **JAVA_HOME** (nebo ekvivalentní cesta)

  * **JAVA_HOME\bin** (nebo ekvivalentní cesta)

  * Adresář, kde je nainstalován Maven

## <a name="create-a-maven-project"></a>Vytvořte projekt Maven

Z příkazového řádku, použijte následující příkaz k vytvoření Maven projektu s názvem **WordCount**:

```bash
mvn archetype:generate -DarchetypeArtifactId=maven-archetype-quickstart -DgroupId=com.microsoft.example -DartifactId=WordCount -DinteractiveMode=false
```

> [!NOTE]
> Pokud používáte prostředí PowerShell, je třeba vložit`-D` parametry s dvojitými uvozovkami.
>
> `mvn archetype:generate "-DarchetypeArtifactId=maven-archetype-quickstart" "-DgroupId=com.microsoft.example" "-DartifactId=WordCount" "-DinteractiveMode=false"`

Tento příkaz vytvoří adresář s názvem `WordCount` do aktuálního umístění, která obsahuje základní projekt Maven. `WordCount` Adresář obsahuje následující položky:

* `pom.xml`: Obsahuje nastavení pro projekt Maven.
* `src\main\java\com\microsoft\example`: Obsahuje kód aplikace.
* `src\test\java\com\microsoft\example`: Obsahuje testy pro vaši aplikaci. 

### <a name="remove-the-generated-example-code"></a>Odebrat generovaný ukázkový kód

Odstraňte generovaný test a soubory aplikace:

* **src\test\java\com\microsoft\example\AppTest.java**
* **src\main\java\com\microsoft\example\App.java**

## <a name="add-maven-repositories"></a>Přidání úložiště Maven

HDInsight je založena na Hortonworks Data Platform (HDP), takže vám doporučujeme používat úložiště Hortonworks ke stažení závislostí pro projekty Apache Storm. V __pom.xml__ soubor, přidejte následující kód XML po `<url>http://maven.apache.org</url>` řádku:

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
        <url>http://repo.hortonworks.com/content/repositories/releases/</url>
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
        <url>http://repo.hortonworks.com/content/repositories/jetty-hadoop/</url>
        <layout>default</layout>
    </repository>
</repositories>
```

## <a name="add-properties"></a>Přidání vlastnosti

Maven umožňuje definovat hodnoty úrovni projektu názvem vlastnosti. V __pom.xml__, přidejte následující text po `</repositories>` řádku:

```xml
<properties>
    <project.build.sourceEncoding>UTF-8</project.build.sourceEncoding>
    <!--
    This is a version of Storm from the Hortonworks repository that is compatible with HDInsight 3.6.
    -->
    <storm.version>1.1.0.2.6.1.9-1</storm.version>
</properties>
```

Teď můžete použít tuto hodnotu v dalších částech `pom.xml`. Například při určení verze součástí Storm, můžete použít `${storm.version}` místo pevné kódování hodnotu.

## <a name="add-dependencies"></a>Přidat závislosti

Přidáte závislost pro komponenty Storm. Otevřete `pom.xml` souboru a přidejte následující kód `<dependencies>` části:

```xml
<dependency>
    <groupId>org.apache.storm</groupId>
    <artifactId>storm-core</artifactId>
    <version>${storm.version}</version>
    <!-- keep storm out of the jar-with-dependencies -->
    <scope>provided</scope>
</dependency>
```

Při kompilaci, Maven používá tuto informaci k vyhledání `storm-core` v úložišti Maven. Nejprve hledá v úložišti místního počítače. Nejsou-li soubory existuje, Maven je stáhne z veřejného úložiště Maven a ukládá je v místním úložišti.

> [!NOTE]
> Upozornění `<scope>provided</scope>` řádek v této části. Toto nastavení určuje Maven vyloučit **storm základní** z všechny JAR soubory, které jsou vytvořeny, protože je k dispozici v systému.

## <a name="build-configuration"></a>Konfigurace sestavení

Moduly plug-in maven umožňují přizpůsobit fáze sestavení projektu. Například jak kompilace projektu nebo jak zabalit do soubor JAR. Otevřete `pom.xml` souboru a přidejte následující kód přímo výše `</project>` řádku.

```xml
<build>
    <plugins>
    </plugins>
    <resources>
    </resources>
</build>
```

V této části se používá k přidání modulů plug-in, prostředky a další možnosti konfigurace sestavení. Úplný přehled o **pom.xml** souborů najdete v tématu [ http://maven.apache.org/pom.html ](http://maven.apache.org/pom.html).

### <a name="add-plug-ins"></a>Přidat moduly plug-in

Pro topologií Apache Storm implementována v jazyce Java [modulu plug-in Maven Exec](http://www.mojohaus.org/exec-maven-plugin/) je užitečné, protože umožňuje snadno topologii místní spuštění ve vašem vývojovém prostředí. Přidejte následující `<plugins>` části `pom.xml` souboru modulu plug-in Exec Maven:

```xml
<plugin>
    <groupId>org.codehaus.mojo</groupId>
    <artifactId>exec-maven-plugin</artifactId>
    <version>1.5.0</version>
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

Další užitečné modul plug-in je [Apache Maven kompilátoru modul plug-in](http://maven.apache.org/plugins/maven-compiler-plugin/), který se používá k změnit možnosti kompilace. Změny jazyce Java verze, která používá Maven pro zdroje a cíle pro vaši aplikaci.

* Pro HDInsight __3.4 nebo starším__, nastavte zdroj a cíl verzi Javy k __1.7__.

* Pro HDInsight __3.5__, nastavte zdroj a cíl verzi Javy k __1.8__.

Přidejte následující text do `<plugins>` části `pom.xml` souboru modulu plug-in Apache Maven kompilátoru. Tento příklad určuje 1.8, tak, aby cílová HDInsight verze 3.5.

```xml
<plugin>
    <groupId>org.apache.maven.plugins</groupId>
    <artifactId>maven-compiler-plugin</artifactId>
    <version>3.3</version>
    <configuration>
    <source>1.8</source>
    <target>1.8</target>
    </configuration>
</plugin>
```

### <a name="configure-resources"></a>Konfigurace prostředků

V části prostředky umožňuje zahrnout jiný kód prostředkům, například konfigurační soubory, které jsou potřebné součásti v topologii. V tomto příkladu přidejte následující text do `<resources>` oddílu se soubor pom.xml.

```xml
<resource>
    <directory>${basedir}/resources</directory>
    <filtering>false</filtering>
    <includes>
        <include>log4j2.xml</include>
    </includes>
</resource>
```

Tento příklad přidá adresáře prostředků v kořenovém adresáři projektu (`${basedir}`) jako umístění, která obsahuje prostředky a obsahuje soubor s názvem `log4j2.xml`. Tento soubor se používá ke konfiguraci, které informace se v topologii protokolu.

## <a name="create-the-topology"></a>Vytvoření topologie

Topologie založené na jazyce Java Apache Storm se skládá z tři součásti, které musíte napsat (nebo reference) jako závislost.

* **Spouts**: načte data z externího zdroje a vysílá datových proudů do topologie.

* **Bolts**: provádí zpracování datových proudů vysílaných funkcích spouts nebo jiné funkce bolts a vysílá jeden nebo více datových proudů.

* **Topologie**: definuje, jak funkcích spouts a funkce bolts jsou uspořádány a představuje vstupní bod pro topologii.

### <a name="create-the-spout"></a>Vytvořte spout

Ke snížení požadavků pro nastavení externích zdrojů dat, následující spout jednoduše vyšle náhodných věty. Je upravenou verzi spout, který je zadán v rámci [počáteční příklady Storm](https://github.com/apache/storm/blob/0.10.x-branch/examples/storm-starter/src/jvm/storm/starter).

> [!NOTE]
> Příklad funkcí spout, který čte z externího zdroje dat najdete v následujících příkladech:
>
> * [TwitterSampleSPout](https://github.com/apache/storm/blob/0.10.x-branch/examples/storm-starter/src/jvm/storm/starter/spout/TwitterSampleSpout.java): Příklad funkcí spout, který čte ze služby Twitter.
> * [Storm Kafka](https://github.com/apache/storm/tree/0.10.x-branch/external/storm-kafka): spout, který čte z Kafka

Pro spout, vytvořte soubor s názvem `RandomSentenceSpout.java` v `src\main\java\com\microsoft\example` adresáře a použijte následující Java kód jako obsah:

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
> I když tato topologie používá jenom jeden spout, ostatní může mít několik, které data datového kanálu z různých zdrojů do topologie.

### <a name="create-the-bolts"></a>Vytvořte funkce bolts

Funkce Bolts zpracovávat data zpracování. Tato topologie používá dvě funkce bolts:

* **SplitSentence**: rozdělí věty vysílaných **RandomSentenceSpout** do jednotlivých slov.

* **WordCount**: Spočítá počet opakování jednotlivých slov došlo k chybě.

> [!NOTE]
> Funkce Bolts dělat cokoliv, například výpočet, trvalost nebo rozhovoru externích součástí.

Vytvořte dva nové soubory, `SplitSentence.java` a `WordCount.java` v `src\main\java\com\microsoft\example` adresáře. Použijte následující text jako obsah pro soubory:

#### <a name="splitsentence"></a>SplitSentence

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

### <a name="define-the-topology"></a>Definovat topologii

Topologie sváže funkcích spouts a bolts společně na graf, který definuje, jak se data proudí mezi součástmi. Nabízí taky paralelismus pomocné parametry, které Storm používá při vytváření instancí komponent v rámci clusteru.

Na následujícím obrázku je základní diagram grafu součástí této topologii.

![Diagram zobrazující uspořádání funkcích spouts a funkce bolts](./media/apache-storm-develop-java-topology/wordcount-topology.png)

Pokud chcete implementovat topologii, vytvořte soubor s názvem `WordCountTopology.java` v `src\main\java\com\microsoft\example` adresáře. Použijte následující kód Java jako obsah souboru:

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

Storm používá Apache Log4j k ukládání informací. Pokud neprovedete konfiguraci protokolování, topologii vysílá diagnostické informace. Pokud chcete řídit, co je protokolováno, vytvořte soubor s názvem `log4j2.xml` v `resources` adresáře. Použijte následující kód XML jako obsah souboru.

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

Tato konfigurace XML nakonfiguruje nové protokoly pro `com.microsoft.example` třída, která obsahuje součásti v této topologii příklad. Pro tohoto protokolovacího nástroje, které jsou zaznamenány žádné informace o protokolování vygenerované součásti v této topologii je nastavena úroveň pro trasování.

`<Root level="error">` Části nakonfiguruje kořenové úrovni protokolování (vše, co není ve `com.microsoft.example`) se protokolovat jenom informace o chybě.

Další informace o konfiguraci protokolování pro Log4j najdete v tématu [ http://logging.apache.org/log4j/2.x/manual/configuration.html ](http://logging.apache.org/log4j/2.x/manual/configuration.html).

> [!NOTE]
> Storm verze 0.10.0 a vyšší využití Log4j 2.x. Starší verze storm použít Log4j 1.x, který používá jiný formát pro konfiguraci protokolu. Informace o konfiguraci starší, najdete v části [ http://wiki.apache.org/logging-log4j/Log4jXmlFormat ](http://wiki.apache.org/logging-log4j/Log4jXmlFormat).

## <a name="test-the-topology-locally"></a>Testování topologie místně

Po uložení souborů, použijte následující příkaz k testování topologie místně.

```bash
mvn compile exec:java -Dstorm.topology=com.microsoft.example.WordCountTopology
```

Při jeho spuštění, topologii zobrazí informace o spuštění. Tento text je příklad výstupu, počet word:

    17:33:27 [Thread-12-count] INFO  com.microsoft.example.WordCount - Emitting a count of 56 for word snow
    17:33:27 [Thread-12-count] INFO  com.microsoft.example.WordCount - Emitting a count of 56 for word white
    17:33:27 [Thread-12-count] INFO  com.microsoft.example.WordCount - Emitting a count of 112 for word seven
    17:33:27 [Thread-16-count] INFO  com.microsoft.example.WordCount - Emitting a count of 195 for word the
    17:33:27 [Thread-30-count] INFO  com.microsoft.example.WordCount - Emitting a count of 113 for word and
    17:33:27 [Thread-30-count] INFO  com.microsoft.example.WordCount - Emitting a count of 57 for word dwarfs
    17:33:27 [Thread-12-count] INFO  com.microsoft.example.WordCount - Emitting a count of 57 for word snow

Tento příklad protokol znamená, že slovo 'a' má byla vygenerované 113 časy. Počet i nadále přejít, dokud topologie běží, protože funkcí spout nepřetržitě vysílá stejné věty.

Mezi emisí slova a počty je interval 5 sekund. **WordCount** součást nakonfigurovaná pro vydávání pouze informace, pokud dorazí značek řazené kolekce členů. Požaduje této značky řazených kolekcí členů jsou pouze doručovány každých pět sekund.

## <a name="convert-the-topology-to-flux"></a>Převést topologii tok

Tok je nové rozhraní Storm 0.10.0 k dispozici a vyšší, což umožňuje oddělit konfiguraci z implementace. Vaše komponenty jsou stále definována v jazyce Java, ale topologii je definována pomocí souboru YAML. Můžete balíček definice výchozí topologie s projektu, nebo použijte samostatný soubor při odesílání topologie. Při odesílání topologie do Storm, můžete použít k naplnění hodnoty v definici topologie YAML proměnné prostředí nebo konfigurační soubory.

Soubor YAML definuje součásti, které budou používat pro topologii a data tok mezi nimi. Jako součást na soubor jar můžete zahrnout soubor YAML nebo můžete použít externí soubor YAML.

Další informace o toku najdete v tématu [tok framework (https://storm.apache.org/releases/0.10.0/flux.html)](https://storm.apache.org/releases/0.10.0/flux.html).

> [!WARNING]
> Z důvodu [chyb (https://issues.apache.org/jira/browse/STORM-2055) ](https://issues.apache.org/jira/browse/STORM-2055) s Storm 1.0.1, budete muset nainstalovat [Storm vývojového prostředí](http://storm.apache.org/releases/current/Setting-up-development-environment.html) tok topologie spouštět místně.

1. Přesunout `WordCountTopology.java` souboru mimo projekt. Tento soubor dříve, definované topologii, ale není potřeba s tokem.

2. V `resources` adresáře, vytvořte soubor s názvem `topology.yaml`. Použijte následující text jako obsah tohoto souboru.

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

3. Proveďte následující změny na `pom.xml` souboru.
   
   * Přidejte následující závislost nové v `<dependencies>` části:
     
        ```xml
        <!-- Add a dependency on the Flux framework -->
        <dependency>
            <groupId>org.apache.storm</groupId>
            <artifactId>flux-core</artifactId>
            <version>${storm.version}</version>
        </dependency>
        ```
   * Přidejte následující modul plug-in, který `<plugins>` části. Tento modul plug-in zpracovává vytvoření balíčku (soubor jar) pro projekt a platí některé transformace, které jsou specifické pro tok při vytváření balíčku.
     
        ```xml
        <!-- build an uber jar -->
        <plugin>
            <groupId>org.apache.maven.plugins</groupId>
            <artifactId>maven-shade-plugin</artifactId>
            <version>2.3</version>
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

   * V **modulu plug-in exec maven** `<configuration>` část, změňte hodnotu `<mainClass>` k `org.apache.storm.flux.Flux`. Toto nastavení umožňuje tok pro zpracování topologii spuštěn místně v vývoj.

   * V `<resources>` přidejte následující příkaz a `<includes>`. Tato konfigurace XML obsahuje YAML soubor, který definuje topologii v rámci projektu.

        ```xml
        <include>topology.yaml</include>
        ```

## <a name="test-the-flux-topology-locally"></a>Testování topologii tok místně

1. Pro zkompilování a spuštění tok topologie pomocí nástroje Maven, použijte následující:

    ```bash
    mvn compile exec:java -Dexec.args="--local -R /topology.yaml"
    ```

    Pokud používáte prostředí PowerShell, použijte následující příkaz:

    ```bash
    mvn compile exec:java "-Dexec.args=--local -R /topology.yaml"
    ```

    > [!WARNING]
    > Pokud vaše topologie používá Storm 1.0.1 bits, tento příkaz se nezdaří. Tato chyba je způsobená [ https://issues.apache.org/jira/browse/STORM-2055 ](https://issues.apache.org/jira/browse/STORM-2055). Místo toho [nainstalovat Storm ve vašem vývojovém prostředí](http://storm.apache.org/releases/current/Setting-up-development-environment.html) a pomocí následujících kroků:
    >
    > Pokud máte [nainstalovaný ve vašem vývojovém prostředí Storm](http://storm.apache.org/releases/current/Setting-up-development-environment.html), můžete místo toho použít následující příkazy:
    >
    > ```bash
    > mvn compile package
    > storm jar target/WordCount-1.0-SNAPSHOT.jar org.apache.storm.flux.Flux --local -R /topology.yaml
    > ```

    `--local` Parametr spustí topologii v místním režimu ve vývojovém prostředí. `-R /topology.yaml` Parametr používá `topology.yaml` souborů prostředků z na soubor jar definovat topologii.

    Při jeho spuštění, topologii zobrazí informace o spuštění. Tento text je příklad výstupu:

        17:33:27 [Thread-12-count] INFO  com.microsoft.example.WordCount - Emitting a count of 56 for word snow
        17:33:27 [Thread-12-count] INFO  com.microsoft.example.WordCount - Emitting a count of 56 for word white
        17:33:27 [Thread-12-count] INFO  com.microsoft.example.WordCount - Emitting a count of 112 for word seven
        17:33:27 [Thread-16-count] INFO  com.microsoft.example.WordCount - Emitting a count of 195 for word the
        17:33:27 [Thread-30-count] INFO  com.microsoft.example.WordCount - Emitting a count of 113 for word and
        17:33:27 [Thread-30-count] INFO  com.microsoft.example.WordCount - Emitting a count of 57 for word dwarfs

    Mezi listy protokolovaných informací je prodlevu 10 sekund.

2. Vytvořit kopii `topology.yaml` souboru z projektu. Název nového souboru `newtopology.yaml`. V `newtopology.yaml` souboru, vyhledejte následující oddíl a změňte hodnotu `10` k `5`. Tato úprava změní interval mezi generování dávky počty slov z 10 sekund. 5.

    ```yaml
    - id: "counter-bolt"
    className: "com.microsoft.example.WordCount"
    constructorArgs:
    - 5
    parallelism: 1
    ```yaml

3. To run the topology, use the following command:

    ```bash
    mvn exec:java -Dexec.args="--local /path/to/newtopology.yaml"
    ```

    Nebo, pokud máte Storm na vašem vývojovém prostředí:

    ```bash
    storm jar target/WordCount-1.0-SNAPSHOT.jar org.apache.storm.flux.Flux --local /path/to/newtopology.yaml
    ```

    Změna `/path/to/newtopology.yaml` na cestu k souboru newtopology.yaml jste vytvořili v předchozím kroku. Tento příkaz používá newtopology.yaml jako definice topologie. Vzhledem k tomu, že jsme nezahrnuli `compile` parametr, Maven používá verzi projektu vytvořené v předchozích krocích.

    Jakmile se spustí topologii, měli byste zaznamenat, že čas mezi emitovaného dávky se změnila, aby odrážela hodnotu v newtopology.yaml. Abyste viděli, že můžete změnit konfiguraci prostřednictvím soubor YAML bez nutnosti její kompilace topologii.

Další informace o těchto a dalších funkcí rozhraní tok najdete v tématu [tok (https://storm.apache.org/releases/0.10.0/flux.html)](https://storm.apache.org/releases/0.10.0/flux.html).

## <a name="trident"></a>Trident

Trident má vysokou úroveň abstrakce, která je poskytována Storm. Podporuje stavová zpracování. Primární výhodou Trident je, že ho může zaručit, že každou zprávu, která vstupuje do topologie je zpracovány pouze jednou. Bez použití Trident, topologie pouze zaručit alespoň jednou zpracování zpráv. Existují také další rozdíly, jako je například integrované komponenty, které lze použít místo vytvoření funkce bolts. Funkce bolts jsou ve skutečnosti nahrazovány obecného méně součástí, např. filtry, projekce a funkcí.

Trident aplikace lze vytvořit pomocí projekty Maven. Použijte stejný základní postup uvedenou výše v tomto článku – pouze kód se liší. Trident také (aktuálně) nelze zadat s tokem framework.

Další informace o Trident naleznete v tématu [přehled API Trident](http://storm.apache.org/documentation/Trident-API-Overview.html).

## <a name="next-steps"></a>Další kroky

Jste se naučili vytváření topologie Storm pomocí Java. Teď další postup:

* [Nasazení a správa topologií Apache Storm v HDInsight](apache-storm-deploy-monitor-topology.md)

* [Vývoj topologie C# pro Apache Storm v HDInsight pomocí sady Visual Studio](apache-storm-develop-csharp-visual-studio-topology.md)

Můžete najít další příklad topologií Storm navštivte stránky [příklad topologií pro Storm v HDInsight](apache-storm-example-topology.md).

