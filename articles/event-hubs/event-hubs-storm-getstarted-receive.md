---
title: 'Úvodní příručka: Příjem událostí pomocí Apache Storm – Azure Event Hubs'
description: 'Úvodní příručka: Tento článek obsahuje informace o tom, jak přijímat události z Azure Event Hubs pomocí Apache Storm.'
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
ms.topic: quickstart
ms.custom: seodec18
ms.date: 11/05/2019
ms.author: shvija
ms.openlocfilehash: 90293da07d3a7ef1c32e5f82d35198d4ffa536b1
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/26/2020
ms.locfileid: "73717603"
---
# <a name="quickstart-receive-events-from-event-hubs-using-apache-storm"></a>Úvodní příručka: Příjem událostí z centra událostí pomocí Apache Storm

[Apache Storm](https://storm.incubator.apache.org) je distribuovaný výpočetní systém v reálném čase, který zjednodušuje spolehlivé zpracování nevázaných datových proudů dat. Tato část ukazuje, jak používat huby Azure Event Hubs Storm k příjmu událostí z centra událostí. Pomocí Apache Storm můžete rozdělit události mezi více procesů hostovaných v různých uzlech. Integrace Event Hubs s Storm zjednodušuje spotřebu událostí tím, že transparentně osvořuje svůj průběh pomocí instalace Storm Zookeeper, spravuje trvalé kontrolní body a paralelní příjem z event hubů.

Další informace o vzorech pro příjem centra událostí najdete v tématu [Přehled centra událostí][Event Hubs overview].

## <a name="prerequisites"></a>Požadavky
Než začnete s rychlým startem, **vytvořte obor názvů Event Hubs a centrum událostí**. Na [portálu Azure](https://portal.azure.com) můžete vytvořit obor názvů typu Event Hubs a získat přihlašovací údaje pro správu, které vaše aplikace potřebuje ke komunikaci s centrem událostí. Chcete-li vytvořit obor názvů a centrum událostí, postupujte podle postupu v [tomto článku](event-hubs-create.md). 

## <a name="create-project-and-add-code"></a>Vytvoření projektu a přidání kódu

Tento kurz používá instalaci [HDInsight Storm,][HDInsight Storm] která je dodávána s chrličem centra událostí, které jsou již k dispozici.

1. Podle postupu [HDInsight Storm – Začínáme](../hdinsight/storm/apache-storm-overview.md) vytvořte nový cluster HDInsight a připojte se k němu prostřednictvím vzdálené plochy.
2. Zkopírujte `%STORM_HOME%\examples\eventhubspout\eventhubs-storm-spout-0.9-jar-with-dependencies.jar` soubor do místního vývojového prostředí. Obsahuje události-bouře-výtok.
3. Pomocí následujícího příkazu nainstalujte balíček do místního úložiště Maven. To umožňuje přidat jako odkaz v projektu Storm v pozdějším kroku.

    ```shell
    mvn install:install-file -Dfile=target\eventhubs-storm-spout-0.9-jar-with-dependencies.jar -DgroupId=com.microsoft.eventhubs -DartifactId=eventhubs-storm-spout -Dversion=0.9 -Dpackaging=jar
    ```
4. V eclipse vytvořte nový projekt Maven (klikněte na **Soubor**, potom **na Nový**a potom **na Project).**
   
    ![Soubor -> New -> Project][12]
5. Vyberte **Použít výchozí umístění pracovního prostoru**a klikněte na **Další.**
6. Vyberte archetyp **maven-archetype-quickstart** a klikněte na **Další**
7. Vložení **groupId** a **ArtifactId**, pak klepněte na **Dokončit**
8. V **souboru pom.xml**přidejte `<dependency>` do uzlu následující závislosti.

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

9. Ve složce **src** vytvořte soubor s názvem **Config.properties** a zkopírujte `receive rule key` následující `event hub name` obsah a napodobte hodnoty a:

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
    Hodnota pro **eventhub.receiver.credits** určuje, kolik událostí jsou dávkovány před jejich uvolněním do kanálu Storm. Z důvodu jednoduchosti tento příklad nastaví tuto hodnotu na 10. Ve výrobě by měla být obvykle nastavena na vyšší hodnoty; například 1024.
10. Vytvořte novou třídu s názvem **LoggerBolt** s následujícím kódem:
    
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
    
    Tento storm šroub zaznamenává obsah přijatých událostí. To lze snadno rozšířit na uložení řazené kolekce členů ve službě úložiště. Příklad [HDInsight Storm with Event Hub] používá stejný přístup k ukládání dat do Azure Storage a Power BI.
11. Vytvořte třídu s názvem **Logtopology** s následujícím kódem:
    
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

    Tato třída vytvoří novou huby událostí huby huby, pomocí vlastností v konfiguračním souboru k vytvoření instance. Je důležité si uvědomit, že tento příklad vytvoří tolik úloh výtoku jako počet oddílů v centru událostí, aby bylo možné použít maximální paralelismus povolený tímto centrem událostí.

## <a name="next-steps"></a>Další kroky
Další informace o službě Event Hubs najdete na následujících odkazech:

* [Přehled centra událostí][Event Hubs overview]
* [Vytvoření centra událostí](event-hubs-create.md)
* [Nejčastější dotazy k Event Hubs](event-hubs-faq.md)

<!-- Links -->
[Event Hubs overview]: event-hubs-what-is-event-hubs.md
[HDInsight Storm]: ../hdinsight/storm/apache-storm-overview.md
[Příklad hdinsightové bouře s centrem událostí]: https://github.com/Azure-Samples/hdinsight-java-storm-eventhub

<!-- Images -->

[12]: ./media/event-hubs-get-started-receive-storm/create-storm1.png
