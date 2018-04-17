---
title: Příklad topologií Apache Storm v HDInsight | Microsoft Docs
description: Seznam příkladů topologie Storm vytvořit a otestovat s Apache Storm v HDInsight, včetně základní topologie C# a Java a práci s Event Hubs.
services: hdinsight
documentationcenter: ''
author: Blackmist
manager: jhubbard
editor: cgronlun
tags: azure-portal
ms.assetid: f9b1bdff-5928-4705-a76d-52fd200917cb
ms.service: hdinsight
ms.custom: hdinsightactive
ms.devlang: na
ms.topic: conceptual
ms.date: 02/21/2018
ms.author: larryfr
ms.openlocfilehash: f633797147b2090680ebb5ea11f60271addc7f52
ms.sourcegitcommit: 9cdd83256b82e664bd36991d78f87ea1e56827cd
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/16/2018
---
# <a name="example-storm-topologies-and-components-for-apache-storm-on-hdinsight"></a>Příklad topologií Storm a součásti pro Apache Storm v HDInsight

Následuje seznam příkladů vytvořen a spravován společností Microsoft pro použití s Apache Storm v HDInsight. Tyto příklady zahrnují různá témata z vytváření základní C# a Java topologie spolupracuje se službami Azure, jako je například Event Hubs, Cosmos databázi, databázi SQL, databáze HBase v HDInsight a Azure Storage. Některé příklady ukazují, jak pracovat s technologií mimo Azure, nebo i jiných společností než Microsoft, například SignalR a Socket.IO.

| Popis | Demonstruje | Jazyk nebo Framework |
|:--- |:--- |:--- |
| [Zápis do Azure Data Lake Store z Apache Storm](apache-storm-write-data-lake-store.md) |Zápis do Azure Data Lake Store |Java |
| [Event Hub Spout a Bolt zdroj](https://github.com/apache/storm/tree/master/external/storm-eventhubs) |Zdroj pro Event Hub Spout a Bolt |Java |
| [Vyvíjet topologie založené na jazyce Java pro Apache Storm v HDInsight][5797064f] |Maven |Java |
| [Vývoj topologie C# pro Apache Storm v HDInsight pomocí sady Visual Studio][16fce2d1] |Nástroje HDInsight pro Visual Studio |C#, Java |
| [Zpracování událostí z Azure Event Hubs se Storm v HDInsight (C#)][844d1d81] |Event Hubs |C# a Java |
| [Zpracování událostí z Azure Event Hubs se Stormem v HDInsight (Java)](https://azure.microsoft.com/resources/samples/hdinsight-java-storm-eventhub/) |Event Hubs |Java |
| [Zpracování dat snímačů vehicle ze služby Event Hubs pomocí Storm v HDInsight][246ee964] |Události rozbočovače, Cosmos DB úložiště Azure Blob (WASB) |C#, Java |
| [Extrakce, transformace a načítání (ETL) z Azure Event Hubs k HBase, používat Storm v HDInsight][b4b68194] |Služba Event Hubs, HBase |C# |
| [Šablona projektu topologie C# Storm pro práci se službami Azure z Storm v HDInsight][ce0c02a2] |Event Hubs, Cosmos databáze, SQL databáze, HBase, SignalR |C#, Java |
| [Škálovatelnost srovnávacích testů pro čtení z Azure Event Hubs pomocí Storm v HDInsight][d6c540e3] |Propustnost zpráv, Event Hubs, databáze SQL |C#, Java |
| [Používat jazyk Python se Storm v HDInsight](apache-storm-develop-python-topology.md) |Součásti Python s topologií tok |Python |
| [Kafka pomocí Storm v HDInsight](../hdinsight-apache-storm-with-kafka.md) | Apache Storm čtení a zápis do Apache Kafka | Java |

### <a name="next-steps"></a>Další kroky

* [Začínáme s Apache Storm v HDInsight][2b8c3488]
* [Zjistěte, jak nasadit a spravovat topologie Storm se Storm v HDInsight][6eb0d3b8]

[2b8c3488]:apache-storm-tutorial-get-started-linux.md "Naučte se vytvářet Storm na clusteru HDInsight a používat ovládací panel Storm k nasazení příklad topologií."
[6eb0d3b8]:apache-storm-deploy-monitor-topology.md "Informace o nasazení a Správa topologie pomocí řídicího panelu webové Storm a uživatelské rozhraní Storm nebo nástroje HDInsight pro Visual Studio."
[16fce2d1]:apache-storm-develop-csharp-visual-studio-topology.md "Naučte se vytvářet topologie C# Storm pomocí nástrojů HDInsight pro Visual Studio."
[5797064f]:apache-storm-develop-java-topology.md "Zjistěte, jak k vytváření topologie Storm v jazyce Java, pomocí nástroje Maven, vytvořením topologie základní wordcount."
[844d1d81]:apache-storm-develop-csharp-event-hub-topology.md "Zjistěte, jak číst a zapisovat data z Azure Event Hubs se Storm v HDInsight."
[246ee964]: https://github.com/hdinsight/hdinsight-storm-examples/blob/master/IotExample/README.md "Naučte se používat topologie Storm ke čtení zpráv z Azure Event Hubs, přečtěte si dokumenty z Azure Cosmos databáze pro odkazování na data a uložení dat do služby Azure Storage."
[d6c540e3]: https://github.com/hdinsight/hdinsight-storm-examples/blob/master/EventCountExample "Několik topologií k předvedení propustnost při čtení ze služby Azure Event Hubs a ukládání do databáze SQL pomocí Apache Storm v HDInsight."
[b4b68194]: https://github.com/hdinsight/hdinsight-storm-examples/blob/master/RealTimeETLExample "Zjistěte, jak číst data z Azure Event Hubs, což je agregační & transformaci dat a pak jej uložte do HBase v HDInsight."
[ce0c02a2]: https://github.com/hdinsight/hdinsight-storm-examples/tree/master/templates/HDInsightStormExamples "Tento projekt obsahuje šablony pro funkcích spouts, funkce bolts a topologie pro interakci s různými službami Azure jako Event Hubs, Cosmos databáze a databáze SQL."

