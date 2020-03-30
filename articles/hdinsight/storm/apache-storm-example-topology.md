---
title: Příklad topologie Apache Storm v Azure HDInsight
description: Seznam příkladů topologií Storm vytvořených a testovaných pomocí Apache Storm na HDInsight, včetně základních topologií Jazyka C# a Javy a práce s event huby.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.custom: hdinsightactive
ms.date: 12/27/2019
ms.openlocfilehash: 302ba583f11b15be98832316b1ea05c7f9be931f
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "75530659"
---
# <a name="example-apache-storm-topologies-and-components-for-apache-storm-on-hdinsight"></a>Příklad topologie Apache Storm a komponenty pro Apache Storm na HDInsight

Následuje seznam příkladů vytvořených a udržovaných společností Microsoft pro použití s [Apache Storm](https://storm.apache.org/) na HDInsight. Tyto příklady pokrývají celou řadu témat, od vytváření základních topologií Jazyka C# a Javy až po práci se službami Azure, jako jsou centra událostí, Cosmos DB, SQL Database, [Apache HBase](https://hbase.apache.org/) na HDInsight a Azure Storage. Některé příklady také ukazují, jak pracovat s technologií mimo Azure nebo dokonce s technologiemi jiných společností než Microsoft, jako je SignalR a Socket.IO.

| Popis | Demonstruje | Jazyk/rámec |
|:--- |:--- |:--- |
| [Zápis do úložiště datových jezer Azure z Apache Storm](apache-storm-write-data-lake-store.md) |Zápis do úložiště datových jezer Azure |Java |
| [Hub hubu událostí a zdroj šroubů](https://github.com/apache/storm/tree/master/external/storm-eventhubs) |Zdroj hubu událostí hub a šroub |Java |
| [Vyvíjejte topologii založenou na Javě pro Apache Storm na HDInsightu][5797064f] |Maven |Java |
| [Vývoj topologií Jazyka C# pro Apache Storm na HDInsightu pomocí Visual Studia][16fce2d1] |Nástroje HDInsight pro Visual Studio |C#, Java |
| [Zpracování událostí z Centra událostí Azure pomocí Apache Storm na HDInsightu (C#)][844d1d81] |Event Hubs |C# a Java |
| [Zpracování událostí z Azure Event Hubs se Stormem v HDInsight (Java)](https://github.com/Azure-Samples/hdinsight-java-storm-eventhub) |Event Hubs |Java |
| [Zpracování dat ze senzorů vozidel z centra událostí pomocí Apache Storm na HDInsightu][246ee964] |Centra událostí, Cosmos DB, objekt blob úložiště Azure (WASB) |C#, Java |
| [Extrahujte, transformujte a načtěte (ETL) z Center událostí Azure do Apache HBase pomocí Apache Storm na HDInsightu][b4b68194] |Rozbočovače událostí, HBase |C# |
| [Projekt topologie template C# Storm pro práci se službami Azure z Apache Storm na HDInsightu][ce0c02a2] |Rozbočovače událostí, Cosmos DB, DATABÁZE SQL, HBase, SignalR |C#, Java |
| [Srovnávací testy škálovatelnosti pro čtení z Azure Event Hubs pomocí Apache Storm na HDInsightu][d6c540e3] |Propustnost zpráv, centra událostí, databáze SQL |C#, Java |
| [Používejte Apache Kafka s Apache Storm na HDInsight](../hdinsight-apache-storm-with-kafka.md) | Apache Storm čtení a psaní apache kafka | Java |

> [!WARNING]  
> Příklady jazyka C# v tomto seznamu byly původně vytvořeny a testovány pomocí hdinsightu založeného na systému Windows a nemusí správně fungovat s clustery HDInsight založenými na Linuxu. Clustery založené na Linuxu používají mono ke spuštění kódu .NET a mohou mít problémy s kompatibilitou s rozhraními a balíčky použitými v příkladu.
>
> Linux je jediný operační systém používaný na HDInsight verze 3.4 nebo novější.

## <a name="python-only"></a>Pouze Python

Příklad komponent Pythonu s topologií Flux najdete v tématu [Použití Pythonu s Apache Storm em na HDInsightu.](apache-storm-develop-python-topology.md)

## <a name="next-steps"></a>Další kroky

* [Vytvoření a monitorování topologie Apache Storm v Azure HDInsight](./apache-storm-quickstart.md)
* [Přečtěte si, jak nasadit a spravovat topologie Apache Storm pomocí Apache Storm na HDInsightu][6eb0d3b8]

[6eb0d3b8]:apache-storm-deploy-monitor-topology-linux.md "Zjistěte, jak nasadit a spravovat topologie pomocí webového řídicího panelu Apache Storm a uživatelského rozhraní Storm nebo nástrojů HDInsight pro Visual Studio."
[16fce2d1]:apache-storm-develop-csharp-visual-studio-topology.md "Zjistěte, jak vytvořit topologii C# Storm pomocí nástrojů HDInsight pro Visual Studio."
[5797064f]:apache-storm-develop-java-topology.md "Naučte se vytvářet topologii Storm v Javě pomocí Mavenu vytvořením základní topologie počtu slov."
[844d1d81]:apache-storm-develop-csharp-event-hub-topology.md "Přečtěte si, jak číst a zapisovat data z Centra událostí Azure pomocí Stormu na HDInsightu."
[246ee964]: https://github.com/hdinsight/hdinsight-storm-examples/blob/master/IotExample/README.md "Zjistěte, jak pomocí topologie Storm číst zprávy z Azure Event Hubs, číst dokumenty z Azure Cosmos DB pro odkazování na data a ukládat data do Azure Storage."
[d6c540e3]: https://github.com/hdinsight/hdinsight-storm-examples/blob/master/EventCountExample "Několik topologie demonstrovat propustnost při čtení z Centra událostí Azure a ukládání do SQL Database pomocí Apache Storm na HDInsight."
[b4b68194]: https://github.com/hdinsight/hdinsight-storm-examples/blob/master/RealTimeETLExample "Zjistěte, jak číst data z Azure Event Hubs, agregovat & transformovat data a pak je uložit do HBase na HDInsightu."
[ce0c02a2]: https://github.com/hdinsight/hdinsight-storm-examples/tree/master/templates/HDInsightStormExamples "Tento projekt obsahuje šablony pro výtoky, šrouby a topologie pro interakci s různými službami Azure, jako jsou event huby, Cosmos DB a SQL Database."
