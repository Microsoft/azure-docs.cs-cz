---
title: Co je Apache Storm – Azure HDInsight
description: Apache Storm umožňuje zpracovávat streamy dat v reálném čase. Azure HDInsight umožňuje snadno vytvořit clustery Storm v cloudu Azure. Se sadou Visual Studio můžete vytvořit řešení Storm pomocí C# a potom ho nasadit do clusterů HDInsight Storm.
ms.service: hdinsight
ms.topic: overview
ms.custom: hdinsightactive,hdiseo17may2017,seoapr2020
ms.date: 04/20/2020
ms.openlocfilehash: 867f042332457ebc5fdd6b1f10ce7fb636309ba8
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/30/2021
ms.locfileid: "104865328"
---
# <a name="what-is-apache-storm-on-azure-hdinsight"></a>Co je Apache Storm ve službě Azure HDInsight?

[Apache Storm](https://storm.apache.org/) je distribuovaný výpočetní systém typu open source a je odolný vůči poruchám. Pro zpracování datových proudů dat v reálném čase pomocí [Apache Hadoop](../hadoop/apache-hadoop-introduction.md)můžete použít proces. Řešení na platformě Storm můžou také poskytnout zaručené zpracování dat s možností opětovného přehrání dat, která nebyla poprvé úspěšně zpracována.

## <a name="why-use-apache-storm-on-hdinsight"></a>Proč používat Apache Storm ve službě HDInsight?

Storm v HDInsight poskytuje následující funkce:

* __99% smlouva SLA (SLA) v době provozuschopnosti__: v HDInsight se dodává plná podpora. Storm v HDInsight má také SLA 99,9 %. To znamená, že Microsoft zaručuje možnost externího připojení ke clusteru Storm alespoň 99,9 % času. Další informace najdete v článku [Podpora Azure](https://azure.microsoft.com/support/options/). Viz také [informace o smlouvě SLA pro dokument HDInsight](https://azure.microsoft.com/support/legal/sla/hdinsight/v1_0/) .

* Podporuje snadné přizpůsobení prostřednictvím spouštění skriptů v clusteru Storm během nebo po jeho vytvoření. Další informace obsahuje článek [Přizpůsobení clusterů HDInsight pomocí skriptových akcí](../hdinsight-hadoop-customize-cluster-linux.md).

* **Vytváření řešení v různých jazycích:** Komponenty pro Storm můžete psát v jazyce, který vám vyhovuje, například Java, C# nebo Python.

    * Integruje Visual Studio a HDInsight pro vývoj, správu a monitorování topologií C#. Další informace naleznete v tématu [Vývoj topologií C# Storm pomocí sady HDInsight Tools pro Visual Studio](apache-storm-develop-csharp-visual-studio-topology.md).

    * Podporuje rozhraní Java Trident. Můžete vytvářet topologie Storm, které podporují právě jedno zpracování zpráv, transakční trvalé uchovávání datového skladu a sadu běžných operací pro analýzy datového proudu.

* **Dynamické škálování:** Můžete přidávat a odebírat pracovní uzly bez jakéhokoli dopadu na činnost topologií Storm. Deaktivujte a znovu aktivujte spuštěné topologie, abyste mohli využívat nové uzly přidané prostřednictvím operací škálování.

* **Vytváření kanálů streamování s využitím několika služeb Azure**: v HDInsight se integruje s dalšími službami Azure. Například Event Hubs, SQL Database, Azure Storage a Azure Data Lake Storage. Ukázkové řešení, které se integruje se službami Azure, najdete v tématu [zpracování událostí z Event Hubs pomocí Apache Storm v HDInsight](https://github.com/Azure-Samples/hdinsight-java-storm-eventhub).

Seznam společností, které používají pro svá řešení pro analýzu v reálném čase Apache Storm, najdete v tématu [Společnosti využívající Apache Storm](https://storm.apache.org/Powered-By.html).

Pokud chcete začít používat, přečtěte si téma [Vytvoření a monitorování Apache Storm topologie ve službě Azure HDInsight](apache-storm-quickstart.md).

## <a name="how-does-apache-storm-work"></a>Jak funguje Apache Storm

K dispozici jsou topologie spuštění, místo [Apache Hadoop úloh MapReduce](https://hadoop.apache.org/docs/r1.2.1/mapred_tutorial.html)  , se kterými jste se mohli seznámit. Topologie Storm se skládají z několika součástí, které jsou uspořádány do orientovaného acyklického grafu (DAG). Data proudí mezi komponentami v grafu. Každá komponenta spotřebovává jeden či více datových streamů a případně může i jeden či více streamů vysílat. Následující diagram znázorňuje tok dat mezi součástmi v topologii pro základní počet slov:

:::image type="content" source="./media/apache-storm-overview/example-apache-storm-topology-diagram.png" alt-text="Příklad uspořádání součástí v topologii Storm" border="false":::

* Součásti spout přenášejí data do topologie. Vysílají do topologie jeden nebo více datových proudů.

* Součásti bolt zpracovávají datové proudy vyslané ze spoutů nebo z jiných boltů. Bolty mohou volitelně vysílat do topologie datové streamy. Bolty také odpovídají za zápis dat do externích služeb nebo úložiště, například HDFS, Kafka nebo HBase.

## <a name="reliability"></a>Spolehlivost

Apache Storm zaručuje, že příchozí zprávy budou vždy plně zpracovány, i když je analýza dat rozdělena do stovek uzlů.

Uzel Nimbus poskytuje podobné funkce jako Apache Hadoop JobTracker. Nimbus přiřazuje úlohy jiným uzlům v clusteru prostřednictvím Apache ZooKeeper. Uzly Zookeeper poskytují koordinaci pro cluster a pomáhají komunikovat mezi Nimbus a procesem vedoucího procesu na pracovních uzlech. Pokud dojde k selhání jednoho uzlu zpracování, uzel Nimbus je informován a přiřadí úlohu a přidružená data do jiného uzlu.

Výchozí konfigurace pro clustery Apache Storm může obsahovat pouze jeden uzel Nimbus. Storm v HDInsight poskytuje dva uzly Nimbus. V případě selhání primárního uzlu se cluster Storm přepne na sekundární uzel a primární uzel se obnoví. Následující diagram znázorňuje tok konfigurace úlohy pro Storm v HDInsight:

:::image type="content" source="./media/apache-storm-overview/storm-diagram-nimbus.png" alt-text="Graf nimbusu, zookeeper a supervisor" border="false":::

## <a name="ease-of-use"></a>Jednoduché používání

|Použití |Description |
|---|---|
|Připojení k Secure Shell (SSH)|K hlavním uzlům clusteru s převzetím služeb při selhání můžete přistupovat přes Internet pomocí SSH. Příkazy můžete spouštět přímo v clusteru prostřednictvím SSH. Další informace najdete v tématu [Použití SSH se službou HDInsight](../hdinsight-hadoop-linux-use-ssh-unix.md).|
|Připojení k webu|Všechny clustery HDInsight poskytují webové uživatelské rozhraní Ambari. Webové uživatelské rozhraní Ambari umožňuje snadno monitorovat, konfigurovat a spravovat služby v clusteru. Clustery Storm také poskytují uživatelské rozhraní Storm. Prostřednictvím uživatelského rozhraní Storm můžete monitorovat a spravovat spuštěné topologie Storm z prohlížeče. Další informace najdete v tématu [Správa HDInsight pomocí webového uživatelského rozhraní Apache Ambari](../hdinsight-hadoop-manage-ambari.md) a [monitorování a správa pomocí Apache STORMCH dokumentů uživatelského rozhraní](apache-storm-deploy-monitor-topology-linux.md#monitor-and-manage-a-topology-using-the-storm-ui) .|
|Azure PowerShell a Azure CLI|PowerShell a Azure CLI poskytují nástroje příkazového řádku, které můžete použít v klientském systému pro práci s prostředím HDInsight a dalšími službami Azure.|
|integrace sady Visual Studio|Nástroje Azure Data Lake pro Visual Studio zahrnout šablony projektů pro vytváření topologií v jazyce C# pomocí architektury SCP.NET. Nástroje Data Lake poskytují také nástroje pro nasazení, monitorování a správu řešení se Stormem v HDInsight. Další informace naleznete v tématu [Vývoj topologií C# Storm pomocí sady HDInsight Tools pro Visual Studio](apache-storm-develop-csharp-visual-studio-topology.md).|

## <a name="integration-with-other-azure-services"></a>Integrace s dalšími službami Azure

* __Azure Data Lake Storage__: Přečtěte si téma [použití Azure Data Lake Storage s Apache Storm v HDInsight](apache-storm-write-data-lake-store.md).

* __Event Hubs:__ Příklad použití služby Event Hubs s clusterem Storm najdete v následujících příkladech:

    * [Zpracování událostí z Azure Event Hubs s využitím Apache Storm v HDInsight (Java)](https://github.com/Azure-Samples/hdinsight-java-storm-eventhub)

    * [Zpracování událostí z Azure Event Hubs s využitím Apache Storm v HDInsight (C#)](apache-storm-develop-csharp-event-hub-topology.md)

* __SQL Database__, __Azure Cosmos DB__, __Event Hubs__ a __HBase__: Příklady šablon jsou součástí nástrojů Data Lake pro Visual Studio. Další informace najdete v tématu [vývoj topologie jazyka C# pro Apache Storm v HDInsight](apache-storm-develop-csharp-visual-studio-topology.md).

## <a name="apache-storm-use-cases"></a>Případy použití Apache Storm

Následují některé obvyklé scénáře, pro které můžete použít Storm v HDInsight:

* Internet věcí (IoT)
* Odhalování podvodů
* Sociální analýzy
* Extrakce, transformace a načítání (ETL)
* Monitorování sítě
* Hledat
* Mobile engagement

Informace o reálných scénářích najdete v tématu [jak společnosti používají Apache Storm](https://storm.apache.org/Powered-By.html) dokumentu.

## <a name="development"></a>Vývoj

Nástroje Data Lake Tools pro Visual Studio umožňují vývojářům .NET navrhovat a implementovat topologie v jazyce C#. Můžete také vytvářet hybridní topologie, které využívají součásti Java a C#. Další informace naleznete v tématu [Vývoj topologií C# pro Apache Storm v HDInsight pomocí sady Visual Studio](apache-storm-develop-csharp-visual-studio-topology.md).

Můžete také vyvíjet řešení v jazyce Java s použitím rozhraní IDE podle svého výběru. Další informace najdete v tématu [Vývoj topologií Java pro Apache Storm v HDInsight](apache-storm-develop-java-topology.md).

K vývoji součástí Storm je možné použít i jazyk Python. Další informace najdete v tématu [vývoj Apache Stormch topologií pomocí Pythonu v HDInsight](apache-storm-develop-python-topology.md).

## <a name="common-development-patterns"></a>Běžné vývojové vzory

### <a name="guaranteed-message-processing"></a>Zaručené zpracování zprávy

Apache Storm můžete poskytovat různé úrovně zaručeného zpracování zprávy. Například základní aplikace pro zaplavování zaručuje alespoň jedno zpracování a Trident může zaručit přesně jedno zpracování. Viz [záruky při zpracování dat](https://storm.apache.org/about/guarantees-data-processing.html) na Apache.org.

### <a name="ibasicbolt"></a>IBasicBolt

Vzor čtení vstupní řazené kolekce členů, generování nulových nebo více řazených kolekcí členů a následné potvrzení vstupní řazené kolekce členů okamžitě na konci metody Execute je běžné. Storm pro automatizaci tohoto vzoru poskytuje rozhraní [IBasicBolt](https://storm.apache.org/releases/current/javadocs/org/apache/storm/topology/IBasicBolt.html).

### <a name="joins"></a>Spojení

Způsob spojení datových proudů se v jednotlivých aplikacích liší. Například můžete spojit jednotlivé řazené kolekce členů z více datových proudů do jednoho nového datového proudu nebo spojit pouze dávky řazené kolekce členů pro konkrétní okno. V obou případech můžete spojení provést pomocí příkazu [fieldsGrouping](https://storm.apache.org/releases/current/javadocs/org/apache/storm/topology/InputDeclarer.html#fieldsGrouping-java.lang.String-org.apache.storm.tuple.Fields-). Seskupení polí je způsob, který definuje, jak se řazené kolekce členů směrují do boltů.

V následujícím příkladu Java se příkaz fieldsGrouping využívá ke směrování řazených kolekcí členů, které pocházejí z komponent „1“, „2“ a „3“, do boltu MyJoiner:

```java
builder.setBolt("join", new MyJoiner(), parallelism) .fieldsGrouping("1", new Fields("joinfield1", "joinfield2")) .fieldsGrouping("2", new Fields("joinfield1", "joinfield2")) .fieldsGrouping("3", new Fields("joinfield1", "joinfield2"));
```

### <a name="batches"></a>Dávky

Apache Storm poskytuje interní mechanismus časování, který je znám jako odškrtávání řazených kolekcí členů. Můžete nastavit, jak často se odškrtávání řazených kolekcí členů ve vaší topologii vysílá.

Příklad použití odškrtávaní řazených kolekcí členů v součásti v C# najdete v souboru [PartialBoltCount.cs](https://github.com/hdinsight/hdinsight-storm-examples/blob/3b2c960549cac122e8874931df4801f0934fffa7/EventCountExample/EventCountTopology/src/main/java/com/microsoft/hdinsight/storm/examples/PartialCountBolt.java).

### <a name="caches"></a>Mezipaměti

Ukládání do mezipaměti se často používá jako mechanismus pro urychlení zpracování, protože udržuje často používané prostředky v paměti. Protože se topologie distribuuje mezi více uzlů a v rámci každého uzlu se distribuuje více procesů, měli byste zvážit použití příkazu [fieldsGrouping](https://storm.apache.org/releases/current/javadocs/org/apache/storm/topology/InputDeclarer.html#fieldsGrouping-java.lang.String-org.apache.storm.tuple.Fields-). Pomocí příkazu `fieldsGrouping` můžete zajistit, aby se řazené kolekce členů obsahující pole, která se používají pro vyhledávání v mezipaměti, vždy směrovaly do stejného procesu. Díky této funkci seskupování předejdete duplikaci položek mezipaměti napříč procesy.

### <a name="stream-top-n"></a>Datový proud „horních N“

Pokud vaše topologie závisí na výpočtu hodnoty „horních N“, počítejte hodnotu „horních N“ paralelně. Poté je třeba výstup z těchto výpočtů sloučit do globální hodnoty. Tuto operaci můžete provést pomocí příkazu [fieldsGrouping](https://storm.apache.org/releases/current/javadocs/org/apache/storm/topology/InputDeclarer.html#fieldsGrouping-java.lang.String-org.apache.storm.tuple.Fields-) a provést směrování podle pole pro paralelní zpracování. Potom můžete provést směrování na bolt, který globálně určuje hodnotu horních N.

Příklad výpočtu hodnoty TOP N najdete v příkladu [části rollingtopwords](https://github.com/apache/storm/blob/master/examples/storm-starter/src/jvm/org/apache/storm/starter/RollingTopWords.java) .

## <a name="logging"></a>protokolování

K protokolování informací používá protokol Apache log4j 2. Ve výchozím nastavení se zaznamenává velké množství dat a může být obtížné informace roztřídit. Konfigurační soubor protokolování můžete zahrnout jako součást topologie Storm k řízení chování protokolování.

Topologii příkladu, která ukazuje, jak nakonfigurovat protokolování, najdete v části [Počet slov na základě jazyka Java](apache-storm-develop-java-topology.md) pro Storm v HDInsight.

## <a name="next-steps"></a>Další kroky

Další informace o řešení pro analýzu v reálném čase pomocí Apache Storm ve službě HDInsight:

* [Vytvoření a monitorování topologie Apache Storm ve službě Azure HDInsight](apache-storm-quickstart.md)
* [Příklad topologií pro Apache Storm v HDInsight](apache-storm-example-topology.md)
