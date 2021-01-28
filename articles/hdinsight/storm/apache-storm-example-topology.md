---
title: Příklady Apache Storm topologie v Azure HDInsight
description: Seznam ukázkových topologií s více podmnožinami vytvořených a testovaných pomocí Apache Storm v HDInsight, včetně základních topologií C# a Java a práce s Event Hubs
ms.service: hdinsight
ms.topic: how-to
ms.custom: hdinsightactive
ms.date: 12/27/2019
ms.openlocfilehash: 8dabfec18cb904fa72518428220991b817b53529
ms.sourcegitcommit: 2f9f306fa5224595fa5f8ec6af498a0df4de08a8
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/28/2021
ms.locfileid: "98928922"
---
# <a name="example-apache-storm-topologies-and-components-for-apache-storm-on-hdinsight"></a>Příklad Apache Storm topologií a komponent pro Apache Storm ve službě HDInsight

Následuje seznam příkladů vytvořených a spravovaných společností Microsoft pro použití s [Apache Storm](https://storm.apache.org/) v HDInsight. Tyto příklady se týkají nejrůznějších témat, od vytvoření základních topologií C# a Java pro práci se službami Azure, jako jsou Event Hubs, Cosmos DB, SQL Database, [Apache HBA](https://hbase.apache.org/) v HDInsight a Azure Storage. Některé příklady také ukazují, jak pracovat s jinými technologiemi než Azure nebo i jinými technologiemi, jako je například Signal a Socket.IO.

| Popis | Demonstruje | Jazyk/rozhraní |
|:--- |:--- |:--- |
| [Zápis do Azure Data Lake Storage z Apache Storm](apache-storm-write-data-lake-store.md) |Zápis do Azure Data Lake Storage |Java |
| [Spout a zdroj šroubů centra událostí](https://github.com/apache/storm/tree/master/external/storm-eventhubs) |Zdroj pro Spout a šroub centra událostí |Java |
| [Vývoj topologií založených na jazyce Java pro Apache Storm v HDInsight][5797064f] |Maven |Java |
| [Vývoj topologií v jazyce C# pro Apache Storm v HDInsight pomocí sady Visual Studio][16fce2d1] |Nástroje HDInsight pro Visual Studio |C#, Java |
| [Zpracování událostí z Azure Event Hubs s využitím Apache Storm v HDInsight (C#)][844d1d81] |Event Hubs |C# a Java |
| [Zpracování událostí z Azure Event Hubs se Stormem v HDInsight (Java)](https://github.com/Azure-Samples/hdinsight-java-storm-eventhub) |Event Hubs |Java |
| [Zpracování dat snímače vozidla z Event Hubs pomocí Apache Storm v HDInsight][246ee964] |Event Hubs, Cosmos DB Azure Storage Blob (WASB) |C#, Java |
| [Extrakce, transformace a načítání (ETL) z Azure Event Hubs do Apache HBA pomocí Apache Storm ve službě HDInsight][b4b68194] |Event Hubs, HBA |C# |
| [Projekt šablony topologie naplavení v C# pro práci se službami Azure z Apache Storm ve službě HDInsight][ce0c02a2] |Event Hubs, Cosmos DB, SQL Database, HBA, Signal |C#, Java |
| [Srovnávací testy škálovatelnosti pro čtení z Azure Event Hubs pomocí Apache Storm ve službě HDInsight][d6c540e3] |Propustnost zpráv, Event Hubs SQL Database |C#, Java |
| [Použití Apache Kafka s Apache Storm v HDInsight](../hdinsight-apache-storm-with-kafka.md) | Apache Storm čtení a zápisu do Apache Kafka | Java |

> [!WARNING]  
> Příklady C# v tomto seznamu byly původně vytvořeny a testovány pomocí HDInsight se systémem Windows a nemusí správně fungovat s clustery HDInsight se systémem Linux. Clustery se systémem Linux používají mono ke spouštění kódu .NET a mohou mít problémy s kompatibilitou s rozhraními a balíčky použitými v tomto příkladu.
>
> Linux je jediným operačním systémem používaným ve službě HDInsight verze 3,4 nebo novější.

## <a name="python-only"></a>Pouze Python

Příklady komponent Pythonu s topologií toků můžete najít v tématu [použití Pythonu s Apache Storm v HDInsight](apache-storm-develop-python-topology.md) .

## <a name="next-steps"></a>Další kroky

* [Vytvoření a monitorování topologie Apache Storm ve službě Azure HDInsight](./apache-storm-quickstart.md)
* [Naučte se nasazovat a spravovat topologie Apache Storm s využitím Apache Storm v HDInsight.][6eb0d3b8]

[6eb0d3b8]:apache-storm-deploy-monitor-topology-linux.md "Naučte se nasazovat a spravovat topologie pomocí webového Apache Stormho řídicího panelu a uživatelského rozhraní pro více procesorů nebo nástrojů HDInsight pro Visual Studio."
[16fce2d1]:apache-storm-develop-csharp-visual-studio-topology.md "Naučte se vytvářet topologie v jazyce C# pomocí nástrojů HDInsight pro Visual Studio."
[5797064f]:apache-storm-develop-java-topology.md "Naučte se vytvářet topologie s více podmnožinami v jazyce Java pomocí Maven vytvořením základní topologie WORDCOUNT."
[844d1d81]:apache-storm-develop-csharp-event-hub-topology.md "Přečtěte si, jak číst a zapisovat data z Azure Event Hubs se zaplavou v HDInsight."
[246ee964]: https://github.com/hdinsight/hdinsight-storm-examples/blob/master/IotExample/README.md "Naučte se, jak pomocí topologie rozkladu číst zprávy z Azure Event Hubs, číst dokumenty z Azure Cosmos DB a odkazovat na ně data a ukládat je do Azure Storage."
[d6c540e3]: https://github.com/hdinsight/hdinsight-storm-examples/blob/master/EventCountExample "Několik topologií k předvedení propustnosti při čtení z Azure Event Hubs a ukládání do SQL Database pomocí Apache Storm v HDInsight."
[b4b68194]: https://github.com/hdinsight/hdinsight-storm-examples/blob/master/RealTimeETLExample "Naučte se číst data z Azure Event Hubs, agregovat & transformovat data a pak je Uložit do HBA v HDInsight."
[ce0c02a2]: https://github.com/hdinsight/hdinsight-storm-examples/tree/master/templates/HDInsightStormExamples "Tento projekt obsahuje šablony pro spoutů, šrouby a topologie pro interakci s různými službami Azure, jako jsou Event Hubs, Cosmos DB a SQL Database."
