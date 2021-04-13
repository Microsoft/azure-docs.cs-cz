---
title: 'Rychlý Start: příjem událostí pomocí Apache Storm – Azure Event Hubs'
description: 'Rychlý Start: Tento článek poskytuje informace o tom, jak přijímat události z Azure Event Hubs pomocí Apache Storm.'
ms.topic: quickstart
ms.date: 06/23/2020
ms.openlocfilehash: b79c2958a10d0193b706c92a28b1792d63c24b82
ms.sourcegitcommit: b4fbb7a6a0aa93656e8dd29979786069eca567dc
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/13/2021
ms.locfileid: "107302960"
---
# <a name="quickstart-receive-events-from-event-hubs-using-apache-storm"></a>Rychlý Start: příjem událostí z Event Hubs pomocí Apache Storm

[Apache Storm](https://storm.incubator.apache.org) je distribuovaný výpočetní systém v reálném čase, který zjednodušuje spolehlivé zpracování nevázaných datových proudů dat. V této části se dozvíte, jak pro příjem událostí z Event Hubs použít Spout s využitím Azure Event Hubs. Pomocí Apache Storm můžete rozdělit události mezi více procesů hostovaných v různých uzlech. Event Hubs integrace se zaplavou zjednodušuje spotřebu událostí transparentním prostupným vytvářením kontrolních bodů pomocí instalace Zookeeper, správu trvalých kontrolních bodů a paralelních přijímání z Event Hubs.

Další informace o Event Hubs vzorech přijímání najdete v [přehledu Event Hubs][Event Hubs overview].

## <a name="prerequisites"></a>Požadavky
Než začnete s rychlým startem, **vytvořte obor názvů Event Hubs a centrum událostí**. Použijte [Azure Portal](https://portal.azure.com) k vytvoření oboru názvů typu Event Hubs a získání přihlašovacích údajů pro správu, které vaše aplikace potřebuje ke komunikaci s centrem událostí. Pokud chcete vytvořit obor názvů a centrum událostí, postupujte podle pokynů v [tomto článku](event-hubs-create.md). 

## <a name="create-project-and-add-code"></a>Vytvořit projekt a přidat kód

Tento kurz používá instalaci [HDInsight][HDInsight Storm] , která je součástí Event Hubs Spout již k dispozici.

1. Pokud chcete vytvořit nový cluster HDInsight a připojit se k němu přes vzdálenou plochu, postupujte podle pokynů v části Začínáme s využitím [HDInsight](../hdinsight/storm/apache-storm-overview.md) .
2. Zkopírujte `%STORM_HOME%\examples\eventhubspout\eventhubs-storm-spout-0.9-jar-with-dependencies.jar` soubor do místního vývojového prostředí. Obsahuje události-Spout.
3. K instalaci balíčku do místního úložiště Maven použijte následující příkaz. To umožňuje, abyste ho v pozdějším kroku přidali jako odkaz v projektu zaplavení.

    ```shell
    mvn install:install-file -Dfile=target\eventhubs-storm-spout-0.9-jar-with-dependencies.jar -DgroupId=com.microsoft.eventhubs -DartifactId=eventhubs-storm-spout -Dversion=0.9 -Dpackaging=jar
    ```
4. V zatmění vytvořte nový projekt Maven (klikněte na **soubor**, pak na **Nový** a pak na **projekt**).
   
    ![Soubor-> nový projekt >][12]
5. Vyberte **použít výchozí umístění pracovního prostoru** a pak klikněte na **Další** .
6. Vyberte **Maven-Archetype – rychlý Start** Archetype a potom klikněte na **Další** .
7. Vložte ID **skupiny** a **ArtifactId** a potom klikněte na **Dokončit** .
8. V **pom.xml** přidejte do uzlu následující závislosti `<dependency>` .

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

9. Ve složce **Src** vytvořte soubor s názvem **config. Properties** a zkopírujte následující obsah a nahraďte `receive rule key` `event hub name` hodnoty a:

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
    Hodnota pro **eventhub. Receiver. kredity** určuje, kolik událostí je dávkování dávek před jejich uvolněním do kanálu pro zaplavení. V zájmu jednoduchosti tento příklad nastaví tuto hodnotu na 10. V produkčním prostředí by mělo být obvykle nastaveno na vyšší hodnoty; například 1024.
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
    
    Tento šroub zaznamená obsah přijatých událostí. Tuto možnost lze snadno rozšířit tak, aby ukládala řazené kolekce členů do služby úložiště. V [příkladu HDInsight se v centru událostí] používá stejný přístup k ukládání dat do Azure Storage a Power BI.
11. Vytvořte třídu s názvem **LogTopology** s následujícím kódem:
    
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

    Tato třída vytvoří novou Event Hubs Spout pomocí vlastností v konfiguračním souboru pro vytvoření instance. Je důležité si uvědomit, že tento příklad vytvoří tolik úloh spoutů jako počet oddílů v centru událostí, aby bylo možné použít maximální paralelismus povolenou pro toto centrum událostí.

## <a name="next-steps"></a>Další kroky
Další informace o službě Event Hubs najdete na následujících odkazech:

* [Přehled Event Hubs][Event Hubs overview]
* [Vytvoření centra událostí](event-hubs-create.md)
* [Nejčastější dotazy k Event Hubs](event-hubs-faq.yml)

<!-- Links -->
[Event Hubs overview]: ./event-hubs-about.md
[HDInsight Storm]: ../hdinsight/storm/apache-storm-overview.md
[Příklad HDInsight s využitím centra událostí]: https://github.com/Azure-Samples/hdinsight-java-storm-eventhub

<!-- Images -->

[12]: ./media/event-hubs-get-started-receive-storm/create-storm1.png
