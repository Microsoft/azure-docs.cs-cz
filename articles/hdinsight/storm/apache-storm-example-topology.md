---
title: Příklad topologií Apache Storm v Azure HDInsight
description: Seznam příkladů topologie Storm vytvořit a testovat pomocí Apache Storm v HDInsight, včetně základní topologie C# nebo Java a práce s Event Hubs.
services: hdinsight
ms.service: hdinsight
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 05/30/2018
ms.openlocfilehash: 73a7c2ef80291920a6d19f0c3ab01b45c1d6b22a
ms.sourcegitcommit: c94cf3840db42f099b4dc858cd0c77c4e3e4c436
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/19/2018
ms.locfileid: "53627720"
---
# <a name="example-apache-storm-topologies-and-components-for-apache-storm-on-hdinsight"></a>Příklad topologií Apache Storm a komponenty pro Apache Storm v HDInsight

Tady je seznam příkladů vytvořené a udržované microsoftem pro použití s [Apache Storm](https://storm.apache.org/) v HDInsight. Tyto příklady pokrývají celou řadu témat, od vytvoření základní C# a topologií Java pracovat se službami Azure, jako jsou Event Hubs, služby Cosmos DB, databáze SQL, [Apache HBase](https://hbase.apache.org/) na HDInsight a Azure Storage. Některé příklady také ukazují, jak pracovat s technologiemi mimo Azure, nebo dokonce i jiných společností než Microsoft, jako je například SignalR a Socket.IO.

| Popis | Demonstruje | Jazyk a rozhraní |
|:--- |:--- |:--- |
| [Zápis do Azure Data Lake Storage z Apache Storm](apache-storm-write-data-lake-store.md) |Zápis do úložiště Azure Data Lake |Java |
| [Event Hub Spout a Bolt zdroje](https://github.com/apache/storm/tree/master/external/storm-eventhubs) |Zdroj pro Event Hub Spout a Bolt |Java |
| [Vývoj topologií založených na jazyce Java pro Apache Storm v HDInsight][5797064f] |Maven |Java |
| [Vývoj topologií C# pro Apache Storm v HDInsight pomocí sady Visual Studio][16fce2d1] |Nástroje HDInsight pro Visual Studio |C#, Java |
| [Zpracování událostí z Azure Event Hubs pomocí Apache Storm v HDInsight (C#)][844d1d81] |Event Hubs |C# nebo Java |
| [Zpracování událostí z Azure Event Hubs se Stormem v HDInsight (Java)](https://azure.microsoft.com/resources/samples/hdinsight-java-storm-eventhub/) |Event Hubs |Java |
| [Zpracování dat snímače vozidla ze služby Event Hubs pomocí Apache Storm v HDInsight][246ee964] |Event Hubs, služby Cosmos DB, Azure úložiště objektů Blob (WASB) |C#, Java |
| [Extrakce, transformace a načítání (ETL) ze služby Azure Event Hubs pro Apache HBase, využívající Apache Storm v HDInsight][b4b68194] |Event Hubs, HBase |C# |
| [Šablona C# projektu topologie Storm pro práci se službami Azure od Apache Storm v HDInsight][ce0c02a2] |Event Hubs, služby Cosmos DB, SQL Database, HBase, SignalR |C#, Java |
| [Srovnávací testy škálovatelnost pro čtení ze služby Azure Event Hubs pomocí Apache Storm v HDInsight][d6c540e3] |Zpráva propustnosti služby Event Hubs, SQL Database |C#, Java |
| [Použití Pythonu s Apache Storm v HDInsight](apache-storm-develop-python-topology.md) |Komponent v Pythonu s topologií tok |Python |
| [Použití Apache Kafka s Apache Storm v HDInsight](../hdinsight-apache-storm-with-kafka.md) | Apache Storm pro čtení a zápis k Apache Kafka | Java |

> [!WARNING]  
> Příklady jazyka C# v tomto seznamu byly původně vytvořený a otestovaný s HDInsight se systémem Windows a může není fungují správně s clustery HDInsight založené na Linuxu. Linuxových clusterech Mono používat ke spouštění kódu .NET a mohou mít problémy s kompatibilitou s architektury a balíčky používané v příkladu.
>
> Linux je pouze operační systém používaný v HDInsight verze 3.4 a vyšší.

### <a name="next-steps"></a>Další kroky

* [Začínáme s Apache Storm v HDInsight][2b8c3488]
* [Další informace o nasazení a správa topologií Apache Storm pomocí Apache Storm v HDInsight][6eb0d3b8]

[2b8c3488]:apache-storm-tutorial-get-started-linux.md "Naučte se vytvořit Apache Storm v clusteru HDInsight a používat řídicí panel Storm k nasazení příklad topologií."
[6eb0d3b8]:apache-storm-deploy-monitor-topology.md "Zjistěte, jak nasadit a spravovat topologie pomocí panelu webové Apache Storm a uživatelské rozhraní Storm nebo nástrojů HDInsight pro Visual Studio."
[16fce2d1]:apache-storm-develop-csharp-visual-studio-topology.md "Zjistěte, jak vytvořit topologie C# Storm pomocí nástrojů HDInsight pro Visual Studio."
[5797064f]:apache-storm-develop-java-topology.md "Zjistěte, jak vytváření topologií Storm v Javě pomocí Maven, tak, že vytvoříte topologie základní wordcount."
[844d1d81]:apache-storm-develop-csharp-event-hub-topology.md "Zjistěte, jak číst a zapisovat data z Azure Event Hubs se Stormem v HDInsight."
[246ee964]: https://github.com/hdinsight/hdinsight-storm-examples/blob/master/IotExample/README.md "Naučte se používat topologie Storm ke čtení zpráv ze služby Azure Event Hubs, přečtěte si dokumenty ze služby Azure Cosmos DB pro odkazování na data a uložit data do služby Azure Storage."
[d6c540e3]: https://github.com/hdinsight/hdinsight-storm-examples/blob/master/EventCountExample "Několik topologií k předvedení propustnost při čtení ze služby Azure Event Hubs a ukládání do služby SQL Database pomocí Apache Storm v HDInsight."
[b4b68194]: https://github.com/hdinsight/hdinsight-storm-examples/blob/master/RealTimeETLExample "Zjistěte, jak přečíst data z Azure Event Hubs, což je agregační & transformaci dat a pak jej uložte do HBase v HDInsight."
[ce0c02a2]: https://github.com/hdinsight/hdinsight-storm-examples/tree/master/templates/HDInsightStormExamples "Tento projekt obsahuje šablony pro spoutů a boltů topologie pro interakci s různými službami Azure, jako jsou Event Hubs, služby Cosmos DB a SQL Database."

