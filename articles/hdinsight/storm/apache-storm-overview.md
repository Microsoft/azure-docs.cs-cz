---
title: Co je Apache Storm – Azure HDInsight
description: Apache Storm umožňuje zpracovávat streamy dat v reálném čase. Azure HDInsight umožňuje snadno vytvořit clustery Storm v cloudu Azure. Se sadou Visual Studio můžete vytvořit řešení Storm pomocí C# a potom ho nasadit do clusterů HDInsight Storm.
author: hrasheed-msft
ms.reviewer: jasonh
keywords: případy použití apache storm,cluster storm,co je apache storm
ms.service: hdinsight
ms.custom: hdinsightactive,hdiseo17may2017
ms.topic: overview
ms.date: 06/12/2019
ms.author: hrasheed
ms.openlocfilehash: 9c7e49fe522859f97f00f760822d5eef60db5f69
ms.sourcegitcommit: d6b68b907e5158b451239e4c09bb55eccb5fef89
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/20/2019
ms.locfileid: "74228861"
---
# <a name="what-is-apache-storm-on-azure-hdinsight"></a>Co je Apache Storm ve službě Azure HDInsight?

[Apache Storm](https://storm.apache.org/) je distribuovaný výpočetní systém typu open source a je odolný vůči poruchám. You can use Storm to process streams of data in real time with [Apache Hadoop](https://hadoop.apache.org/). Řešení Storm také zajišťují garantované zpracování data se schopností opakování dat, která nebyla úspěšně zpracována na první pokus.

## <a name="why-use-apache-storm-on-hdinsight"></a>Why use Apache Storm on HDInsight?

Storm v HDInsight poskytuje následující funkce:

* __Smlouva o úrovni služeb (SLA) zajišťující 99,9% dostupnost Stormu:__ Další informace najdete v tématu [Informace o smlouvě SLA pro HDInsight](https://azure.microsoft.com/support/legal/sla/hdinsight/v1_0/).

* Podporuje snadné přizpůsobení prostřednictvím spouštění skriptů v clusteru Storm během nebo po jeho vytvoření. Další informace obsahuje článek [Přizpůsobení clusterů HDInsight pomocí skriptových akcí](../hdinsight-hadoop-customize-cluster-linux.md).

* **Vytváření řešení v různých jazycích:** Komponenty pro Storm můžete psát v jazyce, který vám vyhovuje, například Java, C# nebo Python.

    * Integruje Visual Studio a HDInsight pro vývoj, správu a monitorování topologií C#. Další informace naleznete v tématu [Vývoj topologií C# Storm pomocí sady HDInsight Tools pro Visual Studio](apache-storm-develop-csharp-visual-studio-topology.md).

    * Podporuje rozhraní Java Trident. Můžete vytvářet topologie Storm, které podporují právě jedno zpracování zpráv, transakční trvalé uchovávání datového skladu a sadu běžných operací pro analýzy datového proudu.

* **Dynamické škálování:** Můžete přidávat a odebírat pracovní uzly bez jakéhokoli dopadu na činnost topologií Storm.

    * Abyste mohli využít nové uzly přidané prostřednictvím operací škálování, musíte deaktivovat a znovu aktivovat spuštěné topologie.

* **Create streaming pipelines using multiple Azure services**: Storm on HDInsight integrates with other Azure services such as Event Hubs, SQL Database, Azure Storage, and Azure Data Lake Storage.

    For an example solution that integrates with Azure services, see [Process events from Event Hubs with Apache Storm on HDInsight](https://github.com/Azure-Samples/hdinsight-java-storm-eventhub).

Seznam společností, které používají pro svá řešení pro analýzu v reálném čase Apache Storm, najdete v tématu [Společnosti využívající Apache Storm](https://storm.apache.org/documentation/Powered-By.html).

To get started using Storm, see [Create and monitor an Apache Storm topology in Azure HDInsight](apache-storm-quickstart.md).

## <a name="how-does-apache-storm-work"></a>How does Apache Storm work

Storm runs topologies instead of the [Apache Hadoop MapReduce](https://hadoop.apache.org/docs/r1.2.1/mapred_tutorial.html)  jobs that you might be familiar with. Topologie Storm se skládají z několika součástí, které jsou uspořádány do orientovaného acyklického grafu (DAG). Data proudí mezi komponentami v grafu. Každá komponenta spotřebovává jeden či více datových streamů a případně může i jeden či více streamů vysílat. Následující diagram znázorňuje tok dat mezi součástmi v topologii pro základní počet slov:

![Příklad uspořádání součástí v topologii Storm](./media/apache-storm-overview/example-apache-storm-topology-diagram.png)

* Součásti spout přenášejí data do topologie. Vysílají do topologie jeden nebo více datových proudů.

* Součásti bolt zpracovávají datové proudy vyslané ze spoutů nebo z jiných boltů. Bolty mohou volitelně vysílat do topologie datové streamy. Bolty také odpovídají za zápis dat do externích služeb nebo úložiště, například HDFS, Kafka nebo HBase.

## <a name="reliability"></a>Spolehlivost

Apache Storm zaručuje, že příchozí zprávy budou vždy plně zpracovány, i když je analýza dat rozdělena do stovek uzlů.

The Nimbus node provides functionality similar to the Apache Hadoop JobTracker, and it assigns tasks to other nodes in a cluster through [Apache ZooKeeper](https://zookeeper.apache.org/). Uzly Zookeeper poskytují koordinaci pro cluster a usnadňují komunikaci mezi procesy Nimbus a Supervisor v pracovních uzlech. Pokud dojde k selhání jednoho uzlu zpracování, uzel Nimbus je informován a přiřadí úlohu a přidružená data do jiného uzlu.

Výchozí konfigurace pro clustery Apache Storm může obsahovat pouze jeden uzel Nimbus. Storm v HDInsight poskytuje dva uzly Nimbus. V případě selhání primárního uzlu se cluster Storm přepne na sekundární uzel a primární uzel se obnoví. Následující diagram znázorňuje tok konfigurace úlohy pro Storm v HDInsight:

![Graf nimbusu, zookeeper a supervisor](./media/apache-storm-overview/storm-diagram-nimbus.png)

## <a name="ease-of-creation"></a>Snadné vytvoření

V HDInsight můžete vytvořit nový cluster Storm během několika minut. For more information on creating a Storm cluster, see [Create Apache Hadoop clusters using the Azure portal](../hdinsight-hadoop-create-linux-clusters-portal.md).

## <a name="ease-of-use"></a>Snadné použití

* __Připojení Secure Shell (SSH)__ : Hlavní uzly clusteru Storm jsou přístupné z internetu prostřednictvím SSH. Příkazy můžete spouštět přímo v clusteru prostřednictvím SSH.

  Další informace najdete v tématu [Použití SSH se službou HDInsight](../hdinsight-hadoop-linux-use-ssh-unix.md).

* __Webové připojení__: Všechny clustery HDInsight poskytují webové uživatelské rozhraní Ambari. Webové uživatelské rozhraní Ambari umožňuje snadno monitorovat, konfigurovat a spravovat služby v clusteru. Clustery Storm také poskytují uživatelské rozhraní Storm. Prostřednictvím uživatelského rozhraní Storm můžete monitorovat a spravovat spuštěné topologie Storm z prohlížeče.

  For more information, see the [Manage HDInsight using the Apache Ambari Web UI](../hdinsight-hadoop-manage-ambari.md) and [Monitor and manage using the Apache Storm UI](apache-storm-deploy-monitor-topology-linux.md#monitor-and-manage-a-topology-using-the-storm-ui) documents.

* __Azure PowerShell and Azure Classic CLI__: PowerShell and classic CLI both provide command-line utilities that you can use from your client system to work with HDInsight and other Azure services.

* __Visual Studio integration__: Azure Data Lake Tools for Visual Studio include project templates for creating C# Storm topologies by using the SCP.NET framework. Nástroje Data Lake poskytují také nástroje pro nasazení, monitorování a správu řešení se Stormem v HDInsight.

  Další informace naleznete v tématu [Vývoj topologií C# Storm pomocí sady HDInsight Tools pro Visual Studio](apache-storm-develop-csharp-visual-studio-topology.md).

## <a name="integration-with-other-azure-services"></a>Integrace s dalšími službami Azure

* __Azure Data Lake Storage__: For an example of using Data Lake Storage with a Storm cluster, see [Use Azure Data Lake Storage with Apache Storm on HDInsight](apache-storm-write-data-lake-store.md).

* __Event Hubs:__ Příklad použití služby Event Hubs s clusterem Storm najdete v následujících příkladech:

    * [Process events from Azure Event Hubs with Apache Storm on HDInsight (Java)](https://github.com/Azure-Samples/hdinsight-java-storm-eventhub)

    * [Process events from Azure Event Hubs with Apache Storm on HDInsight (C#)](apache-storm-develop-csharp-event-hub-topology.md)

* __SQL Database__, __Azure Cosmos DB__, __Event Hubs__ a __HBase__: Příklady šablon jsou součástí nástrojů Data Lake pro Visual Studio. For more information, see [Develop a C# topology for Apache Storm on HDInsight](apache-storm-develop-csharp-visual-studio-topology.md).

## <a name="support"></a>Podpora

Storm v HDInsight obsahuje nepřetržitou plnou podporu na úrovni rozlehlé sítě. Storm v HDInsight má také SLA 99,9 %. To znamená, že Microsoft zaručuje možnost externího připojení ke clusteru Storm alespoň 99,9 % času.

Další informace najdete v článku [Podpora Azure](https://azure.microsoft.com/support/options/).

## <a name="apache-storm-use-cases"></a>Případy použití Apache Storm

Následují některé obvyklé scénáře, pro které můžete použít Storm v HDInsight:

* Internet věcí (IoT)
* Odhalování podvodů
* Sociální analýzy
* Extrakce, transformace a načítání (ETL)
* Monitorování sítě
* Hledat
* Mobile engagement

For information about real-world scenarios, see the [How companies are using Apache Storm](https://storm.apache.org/Powered-By.html) document.

## <a name="development"></a>Vývoj

Nástroje Data Lake Tools pro Visual Studio umožňují vývojářům .NET navrhovat a implementovat topologie v jazyce C#. Můžete také vytvářet hybridní topologie, které využívají součásti Java a C#.

Další informace naleznete v tématu [Vývoj topologií C# pro Apache Storm v HDInsight pomocí sady Visual Studio](apache-storm-develop-csharp-visual-studio-topology.md).

Můžete také vyvíjet řešení v jazyce Java s použitím rozhraní IDE podle svého výběru. For more information, see [Develop Java topologies for Apache Storm on HDInsight](apache-storm-develop-java-topology.md).

K vývoji součástí Storm je možné použít i jazyk Python. For more information, see [Develop Apache Storm topologies using Python on HDInsight](apache-storm-develop-python-topology.md).

## <a name="common-development-patterns"></a>Běžné vývojové vzory

### <a name="guaranteed-message-processing"></a>Zaručené zpracování zprávy

Apache Storm můžete poskytovat různé úrovně zaručeného zpracování zprávy. For example, a basic Storm application can guarantee at-least-once processing, and [Trident](https://storm.apache.org/releases/current/Trident-API-Overview.html) can guarantee exactly once processing.

Další informace naleznete v tématu [Záruky na zpracování dat](https://storm.apache.org/about/guarantees-data-processing.html) na webu apache.org.

### <a name="ibasicbolt"></a>IBasicBolt

The pattern of reading an input tuple, emitting zero or more tuples, and then acknowledging the input tuple immediately at the end of the execute method is common. Storm pro automatizaci tohoto vzoru poskytuje rozhraní [IBasicBolt](https://storm.apache.org/releases/current/javadocs/org/apache/storm/topology/IBasicBolt.html).

### <a name="joins"></a>Spojení

Způsob spojení datových proudů se v jednotlivých aplikacích liší. Můžete například spojovat jednotlivé řazené kolekce členů z různých datových proudů do jednoho nového datového proudu nebo můžete spojovat pouze dávky řazených kolekcí členů pro konkrétní okno. V obou případech můžete spojení provést pomocí příkazu [fieldsGrouping](https://storm.apache.org/releases/current/javadocs/org/apache/storm/topology/InputDeclarer.html#fieldsGrouping-java.lang.String-org.apache.storm.tuple.Fields-). Seskupení polí je způsob, který definuje, jak se řazené kolekce členů směrují do boltů.

V následujícím příkladu Java se příkaz fieldsGrouping využívá ke směrování řazených kolekcí členů, které pocházejí z komponent „1“, „2“ a „3“, do boltu MyJoiner:

```java
builder.setBolt("join", new MyJoiner(), parallelism) .fieldsGrouping("1", new Fields("joinfield1", "joinfield2")) .fieldsGrouping("2", new Fields("joinfield1", "joinfield2")) .fieldsGrouping("3", new Fields("joinfield1", "joinfield2"));
```

### <a name="batches"></a>Dávky

Apache Storm poskytuje interní mechanismus časování, který je znám jako odškrtávání řazených kolekcí členů. Můžete nastavit, jak často se odškrtávání řazených kolekcí členů ve vaší topologii vysílá.

Příklad použití odškrtávaní řazených kolekcí členů v součásti v C# najdete v souboru [PartialBoltCount.cs](https://github.com/hdinsight/hdinsight-storm-examples/blob/3b2c960549cac122e8874931df4801f0934fffa7/EventCountExample/EventCountTopology/src/main/java/com/microsoft/hdinsight/storm/examples/PartialCountBolt.java).

### <a name="caches"></a>Caches

Ukládání do mezipaměti se často používá jako mechanismus pro urychlení zpracování, protože udržuje často používané prostředky v paměti. Protože se topologie distribuuje mezi více uzlů a v rámci každého uzlu se distribuuje více procesů, měli byste zvážit použití příkazu [fieldsGrouping](https://storm.apache.org/releases/current/javadocs/org/apache/storm/topology/InputDeclarer.html#fieldsGrouping-java.lang.String-org.apache.storm.tuple.Fields-). Pomocí příkazu `fieldsGrouping` můžete zajistit, aby se řazené kolekce členů obsahující pole, která se používají pro vyhledávání v mezipaměti, vždy směrovaly do stejného procesu. Díky této funkci seskupování předejdete duplikaci položek mezipaměti napříč procesy.

### <a name="stream-top-n"></a>Datový proud „horních N“

Pokud vaše topologie závisí na výpočtu hodnoty „horních N“, počítejte hodnotu „horních N“ paralelně. Poté je třeba výstup z těchto výpočtů sloučit do globální hodnoty. Tuto operaci můžete provést pomocí příkazu [fieldsGrouping](https://storm.apache.org/releases/current/javadocs/org/apache/storm/topology/InputDeclarer.html#fieldsGrouping-java.lang.String-org.apache.storm.tuple.Fields-) a provést směrování podle pole pro paralelní zpracování. Potom můžete provést směrování na bolt, který globálně určuje hodnotu horních N.

Příklad výpočtu hodnoty „horních N“ najdete v příkladu [RollingTopWords](https://github.com/apache/storm/blob/master/examples/storm-starter/src/jvm/org/apache/storm/starter/RollingTopWords.java).

## <a name="logging"></a>Protokolování

Storm uses [Apache Log4j 2](https://logging.apache.org/log4j/2.x/) to log information. Ve výchozím nastavení se zaznamenává velké množství dat a může být obtížné informace roztřídit. Konfigurační soubor protokolování můžete zahrnout jako součást topologie Storm k řízení chování protokolování.

Topologii příkladu, která ukazuje, jak nakonfigurovat protokolování, najdete v části [Počet slov na základě jazyka Java](apache-storm-develop-java-topology.md) pro Storm v HDInsight.

## <a name="next-steps"></a>Další kroky

Learn more about real-time analytics solutions with Apache Storm on HDInsight:

* [Create and monitor an Apache Storm topology in Azure HDInsight](apache-storm-quickstart.md)
* [Příklad topologií pro Apache Storm v HDInsight](apache-storm-example-topology.md)
