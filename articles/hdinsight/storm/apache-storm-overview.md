---
title: Co je Apache Storm – Azure HDInsight
description: Apache Storm umožňuje zpracovávat streamy dat v reálném čase. Azure HDInsight umožňuje snadno vytvořit clustery Storm v cloudu Azure. Se sadou Visual Studio můžete vytvořit řešení Storm pomocí C# a potom ho nasadit do clusterů HDInsight Storm.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: overview
ms.custom: hdinsightactive,hdiseo17may2017
ms.date: 03/02/2020
ms.openlocfilehash: 24981c10985cd353fcd476f416e89c94ad6b6cc6
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/26/2020
ms.locfileid: "78271903"
---
# <a name="what-is-apache-storm-on-azure-hdinsight"></a>Co je Apache Storm ve službě Azure HDInsight?

[Apache Storm](https://storm.apache.org/) je distribuovaný výpočetní systém typu open source a je odolný vůči poruchám. Storm můžete použít ke zpracování datových proudů dat v reálném čase s [Apache Hadoop](https://hadoop.apache.org/). Řešení Storm mohou také zajistit zaručené zpracování dat s možností přehrání dat, která nebyla úspěšně zpracována poprvé.

## <a name="why-use-apache-storm-on-hdinsight"></a>Proč používat Apache Storm na HDInsight?

Storm v HDInsight poskytuje následující funkce:

* __Smlouva o úrovni služeb (SLA) zajišťující 99,9% dostupnost Stormu:__ Další informace najdete v tématu [Informace o smlouvě SLA pro HDInsight](https://azure.microsoft.com/support/legal/sla/hdinsight/v1_0/).

* Podporuje snadné přizpůsobení prostřednictvím spouštění skriptů v clusteru Storm během nebo po jeho vytvoření. Další informace obsahuje článek [Přizpůsobení clusterů HDInsight pomocí skriptových akcí](../hdinsight-hadoop-customize-cluster-linux.md).

* **Vytváření řešení v různých jazycích:** Komponenty pro Storm můžete psát v jazyce, který vám vyhovuje, například Java, C# nebo Python.

    * Integruje Visual Studio a HDInsight pro vývoj, správu a monitorování topologií C#. Další informace naleznete v tématu [Vývoj topologií C# Storm pomocí sady HDInsight Tools pro Visual Studio](apache-storm-develop-csharp-visual-studio-topology.md).

    * Podporuje rozhraní Java Trident. Můžete vytvářet topologie Storm, které podporují právě jedno zpracování zpráv, transakční trvalé uchovávání datového skladu a sadu běžných operací pro analýzy datového proudu.

* **Dynamické škálování:** Můžete přidávat a odebírat pracovní uzly bez jakéhokoli dopadu na činnost topologií Storm. Abyste mohli využít nové uzly přidané prostřednictvím operací škálování, musíte deaktivovat a znovu aktivovat spuštěné topologie.

* **Vytvářejte kanály streamování pomocí více služeb Azure:** Storm na HDInsight se integruje s dalšími službami Azure, jako jsou centra událostí, SQL Database, Azure Storage a Azure Data Lake Storage. Příklad řešení, které se integruje se službami Azure, najdete v [tématu Zpracování událostí z centra událostí s Apache Storm na HDInsight](https://github.com/Azure-Samples/hdinsight-java-storm-eventhub).

Seznam společností, které používají pro svá řešení pro analýzu v reálném čase Apache Storm, najdete v tématu [Společnosti využívající Apache Storm](https://storm.apache.org/Powered-By.html).

Informace o tom, jak začít používat Storm, najdete [v tématu Vytvoření a monitorování topologie Apache Storm v Azure HDInsight](apache-storm-quickstart.md).

## <a name="how-does-apache-storm-work"></a>Jak apache storm funguje

Storm spustí topologie namísto [Apache Hadoop MapReduce](https://hadoop.apache.org/docs/r1.2.1/mapred_tutorial.html) úlohy, které byste mohli znát. Topologie Storm se skládají z několika součástí, které jsou uspořádány do orientovaného acyklického grafu (DAG). Data proudí mezi komponentami v grafu. Každá komponenta spotřebovává jeden či více datových streamů a případně může i jeden či více streamů vysílat. Následující diagram znázorňuje tok dat mezi součástmi v topologii pro základní počet slov:

![Příklad uspořádání součástí v topologii Storm](./media/apache-storm-overview/example-apache-storm-topology-diagram.png)

* Součásti spout přenášejí data do topologie. Vysílají do topologie jeden nebo více datových proudů.

* Součásti bolt zpracovávají datové proudy vyslané ze spoutů nebo z jiných boltů. Bolty mohou volitelně vysílat do topologie datové streamy. Bolty také odpovídají za zápis dat do externích služeb nebo úložiště, například HDFS, Kafka nebo HBase.

## <a name="reliability"></a>Spolehlivost

Apache Storm zaručuje, že příchozí zprávy budou vždy plně zpracovány, i když je analýza dat rozdělena do stovek uzlů.

Uzel Nimbus poskytuje funkce podobné Apache Hadoop JobTracker a přiřazuje úkoly jiným uzlům v clusteru prostřednictvím [Apache ZooKeeper](https://zookeeper.apache.org/). Uzly Zookeeper poskytují koordinaci pro cluster a usnadňují komunikaci mezi procesy Nimbus a Supervisor v pracovních uzlech. Pokud dojde k selhání jednoho uzlu zpracování, uzel Nimbus je informován a přiřadí úlohu a přidružená data do jiného uzlu.

Výchozí konfigurace pro clustery Apache Storm může obsahovat pouze jeden uzel Nimbus. Storm v HDInsight poskytuje dva uzly Nimbus. V případě selhání primárního uzlu se cluster Storm přepne na sekundární uzel a primární uzel se obnoví. Následující diagram znázorňuje tok konfigurace úlohy pro Storm v HDInsight:

![Graf nimbusu, zookeeper a supervisor](./media/apache-storm-overview/storm-diagram-nimbus.png)

## <a name="ease-of-creation"></a>Snadné vytvoření

V HDInsight můžete vytvořit nový cluster Storm během několika minut. Další informace o vytvoření clusteru Storm najdete v [tématu Vytváření clusterů Apache Hadoop pomocí portálu Azure](../hdinsight-hadoop-create-linux-clusters-portal.md).

## <a name="ease-of-use"></a>Snadné používání

|Použití |Popis |
|---|---|
|Připojení zabezpečeného prostředí (SSH)|Hlavní uzly clusteru Storm můžete přistupovat přes Internet pomocí ssh. Příkazy můžete spouštět přímo v clusteru prostřednictvím SSH. Další informace najdete v tématu [Použití SSH se službou HDInsight](../hdinsight-hadoop-linux-use-ssh-unix.md).|
|Připojení k webu|Všechny clustery HDInsight poskytují webové uživatelské rozhraní Ambari. Webové uživatelské rozhraní Ambari umožňuje snadno monitorovat, konfigurovat a spravovat služby v clusteru. Clustery Storm také poskytují uživatelské rozhraní Storm. Prostřednictvím uživatelského rozhraní Storm můžete monitorovat a spravovat spuštěné topologie Storm z prohlížeče. Další informace najdete v [tématu Správa HDInsight pomocí Apache Ambari web ui](../hdinsight-hadoop-manage-ambari.md) a monitor a spravovat pomocí dokumentů [Apache Storm uživatelského rozhraní.](apache-storm-deploy-monitor-topology-linux.md#monitor-and-manage-a-topology-using-the-storm-ui)|
|Azure PowerShell a Azure CLI|PowerShell i Azure CLI poskytují nástroje příkazového řádku, které můžete použít z vašeho klientského systému pro práci s HDInsight a dalšími službami Azure.|
|Integrace se sadou Visual Studio|Nástroje datového jezera Azure pro Visual Studio zahrnují šablony projektů pro vytváření topologií C# Storm pomocí SCP.NET frameworku. Nástroje Data Lake poskytují také nástroje pro nasazení, monitorování a správu řešení se Stormem v HDInsight. Další informace naleznete v tématu [Vývoj topologií C# Storm pomocí sady HDInsight Tools pro Visual Studio](apache-storm-develop-csharp-visual-studio-topology.md).|

## <a name="integration-with-other-azure-services"></a>Integrace s dalšími službami Azure

* __Azure Data Lake Storage__: Příklad použití úložiště datových jezer s clusterem Storm najdete v tématu [Použití Azure Data Lake Storage s Apache Storm na HDInsight](apache-storm-write-data-lake-store.md).

* __Event Hubs:__ Příklad použití služby Event Hubs s clusterem Storm najdete v následujících příkladech:

    * [Zpracování událostí z Centra událostí Azure pomocí Apache Storm na HDInsightu (Java)](https://github.com/Azure-Samples/hdinsight-java-storm-eventhub)

    * [Zpracování událostí z Centra událostí Azure pomocí Apache Storm na HDInsightu (C#)](apache-storm-develop-csharp-event-hub-topology.md)

* __SQL Database__, __Azure Cosmos DB__, __Event Hubs__ a __HBase__: Příklady šablon jsou součástí nástrojů Data Lake pro Visual Studio. Další informace naleznete [v tématu Vývoj topologie jazyka C# pro Apache Storm na HDInsight](apache-storm-develop-csharp-visual-studio-topology.md).

## <a name="support"></a>Podpora

Storm v HDInsight obsahuje nepřetržitou plnou podporu na úrovni rozlehlé sítě. Storm v HDInsight má také SLA 99,9 %. To znamená, že Microsoft zaručuje možnost externího připojení ke clusteru Storm alespoň 99,9 % času. Další informace najdete v článku [Podpora Azure](https://azure.microsoft.com/support/options/).

## <a name="apache-storm-use-cases"></a>Případy použití Apache Storm

Následují některé obvyklé scénáře, pro které můžete použít Storm v HDInsight:

* Internet věcí (IoT)
* Odhalování podvodů
* Sociální analýzy
* Extrakce, transformace a načítání (ETL)
* Monitorování sítě
* Search
* Mobile engagement

Informace o reálných scénářích najdete v dokumentu [Jak společnosti používají Apache Storm.](https://storm.apache.org/Powered-By.html)

## <a name="development"></a>Vývoj

Nástroje Data Lake Tools pro Visual Studio umožňují vývojářům .NET navrhovat a implementovat topologie v jazyce C#. Můžete také vytvářet hybridní topologie, které využívají součásti Java a C#. Další informace naleznete v tématu [Vývoj topologií C# pro Apache Storm v HDInsight pomocí sady Visual Studio](apache-storm-develop-csharp-visual-studio-topology.md).

Můžete také vyvíjet řešení v jazyce Java s použitím rozhraní IDE podle svého výběru. Další informace najdete [v tématu Vývoj java topologie pro Apache Storm na HDInsight](apache-storm-develop-java-topology.md).

K vývoji součástí Storm je možné použít i jazyk Python. Další informace najdete [v tématu Vývoj topologií Apache Storm pomocí Pythonu na HDInsight](apache-storm-develop-python-topology.md).

## <a name="common-development-patterns"></a>Běžné vývojové vzory

### <a name="guaranteed-message-processing"></a>Zaručené zpracování zprávy

Apache Storm můžete poskytovat různé úrovně zaručeného zpracování zprávy. Například základní aplikace Storm může zaručit alespoň jednou zpracování a [Trident](https://storm.apache.org/releases/current/Trident-API-Overview.html) může zaručit přesně jednou zpracování. Další informace naleznete v tématu [Záruky na zpracování dat](https://storm.apache.org/about/guarantees-data-processing.html) na webu apache.org.

### <a name="ibasicbolt"></a>IBasicBolt

Vzor čtení vstupní řazené kolekce členů, vyzařující nula nebo více řazené kolekce členů a následné potvrzení vstupní řazené kolekce členů okamžitě na konci metody spuštění je běžné. Storm pro automatizaci tohoto vzoru poskytuje rozhraní [IBasicBolt](https://storm.apache.org/releases/current/javadocs/org/apache/storm/topology/IBasicBolt.html).

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

Příklad výpočtu nejvyšší hodnoty N najdete v příkladu [RollingTopWords.](https://github.com/apache/storm/blob/master/examples/storm-starter/src/jvm/org/apache/storm/starter/RollingTopWords.java)

## <a name="logging"></a>protokolování

Storm používá [Apache Log4j 2](https://logging.apache.org/log4j/2.x/) k protokolování informací. Ve výchozím nastavení se zaznamenává velké množství dat a může být obtížné informace roztřídit. Konfigurační soubor protokolování můžete zahrnout jako součást topologie Storm k řízení chování protokolování.

Topologii příkladu, která ukazuje, jak nakonfigurovat protokolování, najdete v části [Počet slov na základě jazyka Java](apache-storm-develop-java-topology.md) pro Storm v HDInsight.

## <a name="next-steps"></a>Další kroky

Přečtěte si další informace o analytických řešeních v reálném čase s Apache Storm na HDInsightu:

* [Vytvoření a monitorování topologie Apache Storm v Azure HDInsight](apache-storm-quickstart.md)
* [Příklad topologií pro Apache Storm v HDInsight](apache-storm-example-topology.md)
