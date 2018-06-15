---
title: Přijímat události z Azure Event Hubs pomocí Apache Storm | Microsoft Docs
description: Začínáme přijetí ze služby Event Hubs pomocí Apache Storm
services: event-hubs
documentationcenter: ''
author: sethmanheim
manager: timlt
editor: ''
ms.assetid: ''
ms.service: event-hubs
ms.workload: na
ms.tgt_pltfrm: java
ms.devlang: multiple
ms.topic: article
ms.date: 04/12/2018
ms.author: sethm
ms.openlocfilehash: 6f558ff0613937d17f2dd7c2c9db6eb2de31ab9e
ms.sourcegitcommit: 9cdd83256b82e664bd36991d78f87ea1e56827cd
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/16/2018
ms.locfileid: "31405666"
---
# <a name="receive-events-from-event-hubs-using-apache-storm"></a>Přijímat události ze služby Event Hubs pomocí Apache Storm

[Apache Storm](https://storm.incubator.apache.org) je distribuované v reálném čase výpočetní systém, který zjednodušuje spolehlivé zpracování bez vazby datových proudů. Tato část ukazuje způsob použití služby Azure Event Hubs Storm spout přijímat události z centra událostí. Pomocí Apache Storm, můžete události rozdělit mezi více procesů, které jsou hostované v různých uzlech. Integrace služby Event Hubs s Storm zjednodušuje příjmu událostí tím, že transparentně vytváření kontrolních bodů jejím průběhu pomocí instalace Zookeeper na Storm, spravuje trvalé kontrolní body a paralelní příjemce událostí ze služby Event Hubs.

Další informace o službě Event Hubs přijímat vzory najdete [Přehled služby Event Hubs][Event Hubs overview].

## <a name="create-project-and-add-code"></a>Vytvořte projekt a přidejte kód

Tento kurz používá [HDInsight Storm] [ HDInsight Storm] instalace, která se dodává s funkcí spout Event Hubs, která je již k dispozici.

1. Postupujte podle [HDInsight Storm - Začínáme](../hdinsight/storm/apache-storm-overview.md) postup pro vytvoření nového clusteru HDInsight a k nim připojit přes vzdálenou plochu.
2. Kopírování `%STORM_HOME%\examples\eventhubspout\eventhubs-storm-spout-0.9-jar-with-dependencies.jar` soubor do místní vývojové prostředí. Tato položka obsahuje spout storm události.
3. Použijte následující příkaz k instalaci balíčku do místního úložiště Maven. Umožňuje přidat jako odkaz v projektu Storm v pozdější fázi.

    ```shell
    mvn install:install-file -Dfile=target\eventhubs-storm-spout-0.9-jar-with-dependencies.jar -DgroupId=com.microsoft.eventhubs -DartifactId=eventhubs-storm-spout -Dversion=0.9 -Dpackaging=jar
    ```
4. V nástroji Eclipse vytvořte nový projekt Maven (klikněte na tlačítko **soubor**, pak **nový**, pak **projektu**).
   
    ![][12]
5. Vyberte **používat výchozí umístění prostoru**, pak klikněte na tlačítko **další**
6. Vyberte **rychlý start maven archetype** archetype, pak klikněte na tlačítko **další**
7. Vložení **GroupId** a **ArtifactId**, pak klikněte na tlačítko **dokončit**
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

9. V **src** složky, vytvořte soubor s názvem **Config.properties** a zkopírujte následující obsah, nahraďte `receive rule key` a `event hub name` hodnoty:

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
    Hodnota **eventhub.receiver.credits** Určuje, kolik události jsou zpracovat v dávce před uvolněním je do kanálu Storm. Tento příklad z důvodu zjednodušení, nastaví tato hodnota 10. V produkčním prostředí je potřeba obvykle ho nastavit na vyšší hodnoty; například 1024.
10. Vytvořte novou třídu s názvem **LoggerBolt** následujícím kódem:
    
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
    
    Touto funkcí bolt Storm zaznamená obsah přijatých událostí. To lze snadno rozšířit k uložení řazených kolekcí členů v úložišti služby. [HDInsight Storm centra událostí příklad] používá tento stejný přístup k ukládání dat do Azure Storage a Power BI.
11. Vytvoření třídy s názvem **LogTopology** následujícím kódem:
    
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

    Tato třída se vytvoří novou funkcí spout Event Hubs pomocí vlastnosti v konfiguračním souboru vytvořit jeho instanci. Je důležité si uvědomit, že tento příklad vytvoří tolik funkcích spouts úlohy jako počet oddílů v Centru událostí, aby bylo možné používat maximální paralelismus povolenou tohoto centra událostí.

## <a name="next-steps"></a>Další postup
Další informace o službě Event Hubs najdete na následujících odkazech:

* [Přehled služby Event Hubs][Event Hubs overview]
* [Vytvoření centra událostí](event-hubs-create.md)
* [Nejčastější dotazy k Event Hubs](event-hubs-faq.md)

<!-- Links -->
[Event Hubs overview]: event-hubs-what-is-event-hubs.md
[HDInsight Storm]: ../hdinsight/storm/apache-storm-overview.md
[HDInsight Storm centra událostí příklad]: https://azure.microsoft.com/resources/samples/hdinsight-java-storm-eventhub/

<!-- Images -->

[12]: ./media/event-hubs-get-started-receive-storm/create-storm1.png
