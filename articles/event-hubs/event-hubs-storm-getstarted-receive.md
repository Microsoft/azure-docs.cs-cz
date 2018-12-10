---
title: Příjem událostí pomocí Apache Storm – Azure Event Hubs | Dokumentace Microsoftu
description: Tento článek obsahuje informace o tom, jak příjem událostí ze služby Azure Event Hubs pomocí Apache Storm.
services: event-hubs
documentationcenter: ''
author: ShubhaVijayasarathy
manager: timlt
editor: ''
ms.assetid: ''
ms.service: event-hubs
ms.workload: na
ms.tgt_pltfrm: java
ms.devlang: multiple
ms.topic: article
ms.custom: seodec18
ms.date: 12/06/2018
ms.author: shvija
ms.openlocfilehash: 3c50916f648a2bce634f7aeb109147a873de1de6
ms.sourcegitcommit: 9fb6f44dbdaf9002ac4f411781bf1bd25c191e26
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/08/2018
ms.locfileid: "53094606"
---
# <a name="receive-events-from-event-hubs-using-apache-storm"></a>Příjem událostí ze služby Event Hubs pomocí Apache Storm

[Apache Storm](https://storm.incubator.apache.org) je distribuovaný výpočet v reálném čase, která zjednodušuje spolehlivé zpracování streamů dat bez vazby. Tato část ukazuje, jak pomocí Azure Event Hubs Storm spout příjem událostí ze služby Event Hubs. Použití Apache Storm, můžete události rozdělit mezi více procesů, které jsou hostované v různých uzlech. Integrace služby Event Hubs se Stormem zjednodušuje spotřebu událostí tím, že transparentně vytváření kontrolních bodů průběhu pomocí instalace Zookeeper pro Storm, spravuje trvalé kontrolní body a paralelní příjmy ze služby Event Hubs.

Další informace o službě Event Hubs přijímat vzory, najdete v článku [Přehled služby Event Hubs][Event Hubs overview].

## <a name="create-project-and-add-code"></a>Vytvořte projekt a přidejte kód

Tento kurz používá [HDInsight Storm] [ HDInsight Storm] instalace, která je součástí spout Event Hubs, která je již k dispozici.

1. Postupujte podle [Storm v HDInsight – Začínáme](../hdinsight/storm/apache-storm-overview.md) postup pro vytvoření nového clusteru HDInsight a připojte se k němu přes vzdálenou plochu.
2. Kopírovat `%STORM_HOME%\examples\eventhubspout\eventhubs-storm-spout-0.9-jar-with-dependencies.jar` soubor do svého místního vývojového prostředí. Tato položka obsahuje spout storm události.
3. Použijte následující příkaz k instalaci balíčku do místního úložiště Maven. To umožňuje přidat jako odkaz v projektu Storm v pozdějším kroku.

    ```shell
    mvn install:install-file -Dfile=target\eventhubs-storm-spout-0.9-jar-with-dependencies.jar -DgroupId=com.microsoft.eventhubs -DartifactId=eventhubs-storm-spout -Dversion=0.9 -Dpackaging=jar
    ```
4. V nástroji Eclipse vytvořte nový projekt Maven (klikněte na tlačítko **souboru**, pak **nový**, pak **projektu**).
   
    ![Soubor -> Nový -> Projekt][12]
5. Vyberte **použijte výchozí umístění pracovního prostoru**, pak klikněte na tlačítko **další**
6. Vyberte **maven-archetype-quickstart** archetype, pak klikněte na tlačítko **další**
7. Vložit **GroupId** a **ArtifactId**, pak klikněte na tlačítko **dokončit**
8. V **pom.xml**, přidejte následující závislosti v `<dependency>` uzlu.

    ```xml  
    <dependency>
        <groupId>org.apache.storm</groupId>
        <artifactId>storm-core</artifactId>
        <version>0.9.2-incubating</version>
        <scope>provided</scope>
    </dependency>
    <dependency>
        <groupId>com.microsoft.eventhubs</groupId>
        <artifactId>eventhubs-storm-spout</artifactId>
        <version>0.9</version>
    </dependency>
    <dependency>
        <groupId>com.netflix.curator</groupId>
        <artifactId>curator-framework</artifactId>
        <version>1.3.3</version>
        <exclusions>
            <exclusion>
                <groupId>log4j</groupId>
                <artifactId>log4j</artifactId>
            </exclusion>
            <exclusion>
                <groupId>org.slf4j</groupId>
                <artifactId>slf4j-log4j12</artifactId>
            </exclusion>
        </exclusions>
        <scope>provided</scope>
    </dependency>
    ```

9. V **src** složce vytvořte soubor s názvem **Config.properties** a zkopírujte do něj následující obsah, nahraďte `receive rule key` a `event hub name` hodnoty:

    ```java
    eventhubspout.username = ReceiveRule
    eventhubspout.password = {receive rule key}
    eventhubspout.namespace = ioteventhub-ns
    eventhubspout.entitypath = {event hub name}
    eventhubspout.partitions.count = 16
       
    # if not provided, will use storm's zookeeper settings
    # zookeeper.connectionstring=localhost:2181
       
    eventhubspout.checkpoint.interval = 10
    eventhub.receiver.credits = 10
    ```
    Hodnota pro **eventhub.receiver.credits** Určuje, kolik události jsou dávce před uvolněním do kanálu Storm. Z důvodu zjednodušení v tomto příkladu nastaví tuto hodnotu 10. V produkčním prostředí to je obvykle třeba nastavit na vyšší hodnoty; například 1024.
10. Vytvořit novou třídu s názvem **LoggerBolt** následujícím kódem:
    
    ```java
    import java.util.Map;
    import org.slf4j.Logger;
    import org.slf4j.LoggerFactory;
    import backtype.storm.task.OutputCollector;
    import backtype.storm.task.TopologyContext;
    import backtype.storm.topology.OutputFieldsDeclarer;
    import backtype.storm.topology.base.BaseRichBolt;
    import backtype.storm.tuple.Tuple;
    
    public class LoggerBolt extends BaseRichBolt {
        private OutputCollector collector;
        private static final Logger logger = LoggerFactory
                  .getLogger(LoggerBolt.class);
    
        @Override
        public void execute(Tuple tuple) {
            String value = tuple.getString(0);
            logger.info("Tuple value: " + value);
   
            collector.ack(tuple);
        }
   
        @Override
        public void prepare(Map map, TopologyContext context, OutputCollector collector) {
            this.collector = collector;
            this.count = 0;
        }
        
        @Override
        public void declareOutputFields(OutputFieldsDeclarer declarer) {
            // no output fields
        }
    
    }
    ```
    
    Touto funkcí bolt Storm zaznamená obsah přijatých událostí. To lze snadno rozšířit pro uložení řazených kolekcí členů ve službě úložiště. [Storm v HDInsight pomocí příkladu centra událostí] používá tento stejný přístup k ukládání dat do služby Azure Storage a Power BI.
11. Vytvořte třídu s názvem **LogTopology** následujícím kódem:
    
    ```java
    import java.io.FileReader;
    import java.util.Properties;
    import backtype.storm.Config;
    import backtype.storm.LocalCluster;
    import backtype.storm.StormSubmitter;
    import backtype.storm.generated.StormTopology;
    import backtype.storm.topology.TopologyBuilder;
    import com.microsoft.eventhubs.samples.EventCount;
    import com.microsoft.eventhubs.spout.EventHubSpout;
    import com.microsoft.eventhubs.spout.EventHubSpoutConfig;
        
    public class LogTopology {
        protected EventHubSpoutConfig spoutConfig;
        protected int numWorkers;
        
        protected void readEHConfig(String[] args) throws Exception {
            Properties properties = new Properties();
            if (args.length > 1) {
                properties.load(new FileReader(args[1]));
            } else {
                properties.load(EventCount.class.getClassLoader()
                        .getResourceAsStream("Config.properties"));
            }
        
            String username = properties.getProperty("eventhubspout.username");
            String password = properties.getProperty("eventhubspout.password");
            String namespaceName = properties
                    .getProperty("eventhubspout.namespace");
            String entityPath = properties.getProperty("eventhubspout.entitypath");
            String zkEndpointAddress = properties
                    .getProperty("zookeeper.connectionstring"); // opt
            int partitionCount = Integer.parseInt(properties
                    .getProperty("eventhubspout.partitions.count"));
            int checkpointIntervalInSeconds = Integer.parseInt(properties
                    .getProperty("eventhubspout.checkpoint.interval"));
            int receiverCredits = Integer.parseInt(properties
                    .getProperty("eventhub.receiver.credits")); // prefetch count
                                                                // (opt)
            System.out.println("Eventhub spout config: ");
            System.out.println("  partition count: " + partitionCount);
            System.out.println("  checkpoint interval: "
                    + checkpointIntervalInSeconds);
            System.out.println("  receiver credits: " + receiverCredits);
     
            spoutConfig = new EventHubSpoutConfig(username, password,
                    namespaceName, entityPath, partitionCount, zkEndpointAddress,
                    checkpointIntervalInSeconds, receiverCredits);
        
            // set the number of workers to be the same as partition number.
            // the idea is to have a spout and a logger bolt co-exist in one
            // worker to avoid shuffling messages across workers in storm cluster.
            numWorkers = spoutConfig.getPartitionCount();
        
            if (args.length > 0) {
                // set topology name so that sample Trident topology can use it as
                // stream name.
                spoutConfig.setTopologyName(args[0]);
            }
        }
        
        protected StormTopology buildTopology() {
            TopologyBuilder topologyBuilder = new TopologyBuilder();
       
            EventHubSpout eventHubSpout = new EventHubSpout(spoutConfig);
            topologyBuilder.setSpout("EventHubsSpout", eventHubSpout,
                    spoutConfig.getPartitionCount()).setNumTasks(
                    spoutConfig.getPartitionCount());
            topologyBuilder
                    .setBolt("LoggerBolt", new LoggerBolt(),
                            spoutConfig.getPartitionCount())
                    .localOrShuffleGrouping("EventHubsSpout")
                    .setNumTasks(spoutConfig.getPartitionCount());
            return topologyBuilder.createTopology();
        }
        
        protected void runScenario(String[] args) throws Exception {
            boolean runLocal = true;
            readEHConfig(args);
            StormTopology topology = buildTopology();
            Config config = new Config();
            config.setDebug(false);
        
            if (runLocal) {
                config.setMaxTaskParallelism(2);
                LocalCluster localCluster = new LocalCluster();
                localCluster.submitTopology("test", config, topology);
                Thread.sleep(5000000);
                localCluster.shutdown();
            } else {
                config.setNumWorkers(numWorkers);
                StormSubmitter.submitTopology(args[0], config, topology);
            }
        }
        
        public static void main(String[] args) throws Exception {
            LogTopology topology = new LogTopology();
            topology.runScenario(args);
        }
    }
    ```

    Tato třída vytvoří nové spout Event Hubs pomocí vlastností v konfiguračním souboru k vytvoření instance této komponenty. Je důležité si uvědomit, že tento příklad vytvoří libovolný počet spoutů úlohy jako počet oddílů v Centru událostí, aby bylo možné používat maximální paralelismu tohoto centra událostí dovolují.

## <a name="next-steps"></a>Další postup
Další informace o službě Event Hubs najdete na následujících odkazech:

* [Přehled služby Event Hubs][Event Hubs overview]
* [Vytvoření centra událostí](event-hubs-create.md)
* [Nejčastější dotazy k Event Hubs](event-hubs-faq.md)

<!-- Links -->
[Event Hubs overview]: event-hubs-what-is-event-hubs.md
[HDInsight Storm]: ../hdinsight/storm/apache-storm-overview.md
[Storm v HDInsight pomocí příkladu centra událostí]: https://azure.microsoft.com/resources/samples/hdinsight-java-storm-eventhub/

<!-- Images -->

[12]: ./media/event-hubs-get-started-receive-storm/create-storm1.png
