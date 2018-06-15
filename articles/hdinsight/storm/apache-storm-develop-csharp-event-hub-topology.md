---
title: Zpracování událostí ze služby Event Hubs se Storm - Azure HDInsight | Microsoft Docs
description: Informace o zpracování dat z Azure Event Hubs s topologií C# Storm pomocí nástrojů HDInsight pro Visual Studio vytvořit v sadě Visual Studio.
services: hdinsight,notification hubs
documentationcenter: ''
author: Blackmist
manager: jhubbard
editor: cgronlun
ms.assetid: 67f9d08c-eea0-401b-952b-db765655dad0
ms.service: hdinsight
ms.custom: ''
ms.devlang: dotnet
ms.topic: conceptual
ms.date: 11/27/2017
ms.author: larryfr
ROBOTS: NOINDEX
ms.openlocfilehash: 475a63937ed09b4ce875465c0eb3488ce92c38be
ms.sourcegitcommit: 9cdd83256b82e664bd36991d78f87ea1e56827cd
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/16/2018
ms.locfileid: "31419536"
---
# <a name="process-events-from-azure-event-hubs-with-storm-on-hdinsight-c"></a>Zpracování událostí z Azure Event Hubs se Storm v HDInsight (C#)

Naučte se pracovat s Azure Event Hubs z Apache Storm v HDInsight. Tento dokument používá topologie C# Storm ke čtení a zápisu dat ze služby Event Hubs

> [!NOTE]
> Java verzi tohoto projektu, naleznete v části [zpracovat události z Azure Event Hubs se Storm v HDInsight (Java)](https://azure.microsoft.com/resources/samples/hdinsight-java-storm-eventhub/).

## <a name="scpnet"></a>SCP.NET

Kroky v tomto dokumentu používají SCP.NET balíčku NuGet, který usnadňuje vytvoření topologie C# a součásti pro použití s Storm v HDInsight.

> [!IMPORTANT]
> Pokud kroky v tomto dokumentu se spoléhají na vývojového prostředí systému Windows pomocí sady Visual Studio, zkompilovaný projekt lze odeslat do Storm v clusteru HDInsight, který používá Linux. Clustery se systémem Linux vytvořené po 28 říjnu 2016 se podporují jenom SCP.NET topologie.

HDInsight 3.4 a větší použití Mono spouštění topologií C#. V příkladu v tomto dokumentu pracuje s HDInsight 3.6. Pokud máte v plánu na vytváření řešení .NET pro HDInsight, podívejte se [Mono kompatibility](http://www.mono-project.com/docs/about-mono/compatibility/) dokumentu potenciální nekompatibility.

### <a name="cluster-versioning"></a>Správa verzí clusteru

Balíček Microsoft.SCP.Net.SDK NuGet, které používáte pro svůj projekt se musí shodovat hlavní verzi Storm v HDInsight nainstalována. HDInsight verze 3.5 a 3.6 používat Storm 1.x, musíte použít verzi 1.0.x.x SCP.NET s těchto clusterů.

> [!IMPORTANT]
> V příkladu v tomto dokumentu očekává HDInsight 3.5 nebo 3,6 clusteru.
>
> HDInsight od verze 3.4 výše používá výhradně operační systém Linux. Další informace najdete v tématu [Vyřazení prostředí HDInsight ve Windows](../hdinsight-component-versioning.md#hdinsight-windows-retirement).

Topologie C# musí být také .NET 4.5.

## <a name="how-to-work-with-event-hubs"></a>Jak pracovat s Event Hubs

Společnost Microsoft poskytuje sadu komponent v jazyce Java, které lze použít ke komunikaci se službou Event Hubs od topologie Storm. Můžete najít soubor Java archivu (JAR), který obsahuje HDInsight 3.6 kompatibilní verze těchto součástí v [ https://github.com/hdinsight/mvn-repo/raw/master/org/apache/storm/storm-eventhubs/1.1.0.1/storm-eventhubs-1.1.0.1.jar ](https://github.com/hdinsight/mvn-repo/raw/master/org/apache/storm/storm-eventhubs/1.1.0.1/storm-eventhubs-1.1.0.1.jar).

> [!IMPORTANT]
> Při součásti jsou napsané v jazyce Java, je můžete snadno použít z topologie C#.

V tomto příkladu se používají následující součásti:

* __EventHubSpout__: čte data ze služby Event Hubs.
* __EventHubBolt__: zapisuje data do centra událostí.
* __EventHubSpoutConfig__: slouží ke konfiguraci EventHubSpout.
* __EventHubBoltConfig__: slouží ke konfiguraci EventHubBolt.

### <a name="example-spout-usage"></a>Příklad použití funkcí spout

SCP.NET poskytuje metody pro přidání EventHubSpout do vaší topologie. Tyto metody usnadňují přidat spout než použití obecné metody pro přidání součásti Java. Následující příklad ukazuje, jak vytvořit spout pomocí __SetEventHubSpout__ a **EventHubSpoutConfig** metody poskytované SCP.NET:

```csharp
 topologyBuilder.SetEventHubSpout(
    "EventHubSpout",
    new EventHubSpoutConfig(
        ConfigurationManager.AppSettings["EventHubSharedAccessKeyName"],
        ConfigurationManager.AppSettings["EventHubSharedAccessKey"],
        ConfigurationManager.AppSettings["EventHubNamespace"],
        ConfigurationManager.AppSettings["EventHubEntityPath"],
        eventHubPartitions),
    eventHubPartitions);
```

Předchozí příklad vytvoří novou funkcí spout součást s názvem __EventHubSpout__a nakonfiguruje ho ke komunikaci s centra událostí. Pomocný parametr paralelismus pro součást je nastavena na počet oddílů události rozbočovače. Toto nastavení umožňuje Storm k vytvoření instance součásti pro každý oddíl.

### <a name="example-bolt-usage"></a>Příklad použití bolt

Použití **JavaComponmentConstructor** metodu pro vytvoření instance bolt. Následující příklad ukazuje, jak vytvořit a nakonfigurovat novou instanci třídy **EventHubBolt**:

```csharp
// Java construcvtor for the Event Hub Bolt
JavaComponentConstructor constructor = JavaComponentConstructor.CreateFromClojureExpr(
    String.Format(@"(org.apache.storm.eventhubs.bolt.EventHubBolt. (org.apache.storm.eventhubs.bolt.EventHubBoltConfig. " +
        @"""{0}"" ""{1}"" ""{2}"" ""{3}"" ""{4}"" {5}))",
        ConfigurationManager.AppSettings["EventHubPolicyName"],
        ConfigurationManager.AppSettings["EventHubPolicyKey"],
        ConfigurationManager.AppSettings["EventHubNamespace"],
        "servicebus.windows.net",
        ConfigurationManager.AppSettings["EventHubName"],
        "true"));

// Set the bolt to subscribe to data from the spout
topologyBuilder.SetJavaBolt(
    "eventhubbolt",
    constructor,
    partitionCount)
        .shuffleGrouping("Spout");
```

> [!NOTE]
> Tento příklad používá Clojure výrazu předaného jako řetězec, místo použití **JavaComponentConstructor** vytvořit **EventHubBoltConfig**, jako v příkladu funkcí spout. Buď metoda funguje. Použijte metodu, která funguje pro vás nejlepší.

## <a name="download-the-completed-project"></a>Stáhněte si dokončený projekt

Můžete stáhnout úplnou verzi vytvořených v tomto kurzu z projektů [Githubu](https://github.com/Azure-Samples/hdinsight-dotnet-java-storm-eventhub). Však stále musíte zadat nastavení konfigurace pomocí kroků v tomto kurzu.

### <a name="prerequisites"></a>Požadavky

* [Apache Storm v HDInsight clusteru verze 3.5 nebo 3.6](apache-storm-tutorial-get-started-linux.md).

    > [!WARNING]
    > V příkladu v tomto dokumentu vyžaduje Storm v HDInsight verze 3.5 nebo 3.6. To nefunguje se staršími verzemi HDInsight, kvůli nejnovější změny název třídy. Verzi tohoto příkladu, který funguje s starší clustery, naleznete v části [Githubu](https://github.com/Azure-Samples/hdinsight-dotnet-java-storm-eventhub/releases).

* [Centra událostí Azure](../../event-hubs/event-hubs-create.md).

* [Sady Azure .NET SDK](http://azure.microsoft.com/downloads/).

* [Nástroje HDInsight pro Visual Studio](../hadoop/apache-hadoop-visual-studio-tools-get-started.md).

* Java JDK 1.8 nebo později na vašem vývojovém prostředí. Stahování JDK jsou k dispozici na [Oracle](http://www.oracle.com/technetwork/java/javase/downloads/index.html).

  * **JAVA_HOME** proměnnou prostředí musí odkazovat na adresář, který obsahuje Java.
  * **%JAVA_HOME%/bin** adresář se musí nacházet v cestě.

## <a name="download-the-event-hubs-components"></a>Stáhněte si komponenty služby Event Hubs

Stáhnout spout Event Hubs a funkce bolt z komponenty [ https://github.com/hdinsight/mvn-repo/raw/master/org/apache/storm/storm-eventhubs/1.1.0.1/storm-eventhubs-1.1.0.1.jar ](https://github.com/hdinsight/mvn-repo/raw/master/org/apache/storm/storm-eventhubs/1.1.0.1/storm-eventhubs-1.1.0.1.jar).

Vytvořte adresář s názvem `eventhubspout`a uložte soubor do adresáře.

## <a name="configure-event-hubs"></a>Konfigurace služby Event Hubs

Event Hubs je zdroj dat pro tento příklad. Použijte informace v části "Vytvoření centra událostí" [Začínáme se službou Event Hubs](../../event-hubs/event-hubs-create.md).

1. Po vytvoření centra událostí, zobrazení **EventHub** nastavení ve službě Azure portálu a vyberte možnost **zásady sdíleného přístupu**. Vyberte **+ přidat** přidat následující zásady:

   | Název | Oprávnění |
   | --- | --- |
   | zapisovatel |Odeslat |
   | čtenář |Naslouchání |

    ![Okno zásady přístupu – snímek obrazovky sdílené složky](./media/apache-storm-develop-csharp-event-hub-topology/sas.png)

2. Vyberte **čtečky** a **zapisovače** zásady. Zkopírujte a uložte hodnotu primárního klíče pro obě zásady, jako jsou tyto hodnoty použít později.

## <a name="configure-the-eventhubwriter"></a>Konfigurace EventHubWriter

1. Pokud jste ještě nenainstalovali nejnovější verzi nástroje HDInsight pro Visual Studio, najdete v části [Začínáme pomocí nástrojů HDInsight pro Visual Studio](../hadoop/apache-hadoop-visual-studio-tools-get-started.md).

2. Stáhnout z [eventhub. storm hybridní](https://github.com/Azure-Samples/hdinsight-dotnet-java-storm-eventhub).

3. V **EventHubWriter** projekt, otevřete **App.config** souboru. Pomocí informací z centra událostí, které jste dříve nakonfigurovali vyplnit hodnotu pro následující klíče:

   | Klíč | Hodnota |
   | --- | --- |
   | EventHubPolicyName |Zapisovač (Pokud jste použili jiný název pro zásady pomocí *odeslat* oprávnění, použijte místo toho.) |
   | EventHubPolicyKey |Klíč pro zásady zapisovače. |
   | EventHubNamespace |Obor názvů, který obsahuje Centrum událostí. |
   | EventHubName |Název centra událostí. |
   | EventHubPartitionCount |Počet oddílů v Centru událostí. |

4. Uložte a zavřete **App.config** souboru.

## <a name="configure-the-eventhubreader"></a>Konfigurace EventHubReader

1. Otevřete **EventHubReader** projektu.

2. Otevřete **App.config** souboru **EventHubReader**. Pomocí informací z centra událostí, které jste dříve nakonfigurovali vyplnit hodnotu pro následující klíče:

   | Klíč | Hodnota |
   | --- | --- |
   | EventHubPolicyName |Čtečka (Pokud jste použili jiný název pro zásady pomocí *naslouchání* oprávnění, použijte místo toho.) |
   | EventHubPolicyKey |Klíč pro zásady čtečky. |
   | EventHubNamespace |Obor názvů, který obsahuje Centrum událostí. |
   | EventHubName |Název centra událostí. |
   | EventHubPartitionCount |Počet oddílů v Centru událostí. |

3. Uložte a zavřete **App.config** souboru.

## <a name="deploy-the-topologies"></a>Nasazení topologie

1. Z **Průzkumníku řešení**, klikněte pravým tlačítkem myši **EventHubReader** projektu a vyberte **odeslání do Storm v HDInsight**.

    ![Snímek obrazovky řešení Explorer, se odeslat do Storm v HDInsight zvýrazněná](./media/apache-storm-develop-csharp-event-hub-topology/submittostorm.png)

2. Na **odeslání topologie** dialogové okno, vyberte vaše **Storm Cluster**. Rozbalte položku **další konfigurace**, vyberte **cesty k souborům Java**, vyberte **...** a vyberte adresář, který obsahuje soubor JAR, který jste předtím stáhli. Nakonec klikněte na **odeslání**.

    ![Dialogové okno snímek obrazovky odeslání topologie](./media/apache-storm-develop-csharp-event-hub-topology/submit.png)

3. Při odeslání topologii **prohlížeč topologie Storm** se zobrazí. Chcete-li zobrazit informace o topologii, vyberte **EventHubReader** topologie v levém podokně.

    ![Snímek obrazovky prohlížeč topologie Storm](./media/apache-storm-develop-csharp-event-hub-topology/topologyviewer.png)

4. Z **Průzkumníku řešení**, klikněte pravým tlačítkem myši **EventHubWriter** projektu a vyberte **odeslání do Storm v HDInsight**.

5. Na **odeslání topologie** dialogové okno, vyberte vaše **Storm Cluster**. Rozbalte položku **další konfigurace**, vyberte **cesty k souborům Java**, vyberte **...** a vyberte adresář, který obsahuje soubor JAR jste předtím stáhli. Nakonec klikněte na **odeslání**.

6. Při odeslání topologii aktualizaci seznamu topologie v **prohlížeč topologie Storm** ověřit, jestli obou topologií běží na clusteru.

7. V **prohlížeč topologie Storm**, vyberte **EventHubReader** topologie.

8. Chcete-li otevřít komponentu shrnutí bolt, dvakrát klikněte na **LogBolt** součásti v diagramu.

9. V **vykonavatelů** vyberte jeden z odkazů v **Port** sloupce. Zobrazí se informace, které jsou zapsané podle součásti. Zaznamenané informace je podobná následující text:

        2017-03-02 14:51:29.255 m.s.p.TaskHost [INFO] Received C# STDOUT: 2017-03-02 14:51:29,255 [1] INFO  EventHubReader_LogBolt [(null)] - Received data: {"deviceValue":1830978598,"deviceId":"8566ccbc-034d-45db-883d-d8a31f34068e"}
        2017-03-02 14:51:29.283 m.s.p.TaskHost [INFO] Received C# STDOUT: 2017-03-02 14:51:29,283 [1] INFO  EventHubReader_LogBolt [(null)] - Received data: {"deviceValue":1756413275,"deviceId":"647a5eff-823d-482f-a8b4-b95b35ae570b"}
        2017-03-02 14:51:29.313 m.s.p.TaskHost [INFO] Received C# STDOUT: 2017-03-02 14:51:29,312 [1] INFO  EventHubReader_LogBolt [(null)] - Received data: {"deviceValue":1108478910,"deviceId":"206a68fa-8264-4d61-9100-bfdb68ee8f0a"}

## <a name="stop-the-topologies"></a>Zastavit uvedené topologie

Pokud chcete zastavit uvedené topologie, vyberte každý topologii **prohlížeč topologie Storm**, pak klikněte na tlačítko **Kill**.

![Snímek obrazovky systému Storm topologie prohlížeč, s tlačítkem Kill](./media/apache-storm-develop-csharp-event-hub-topology/killtopology.png)

## <a name="delete-your-cluster"></a>Odstranění clusteru

[!INCLUDE [delete-cluster-warning](../../../includes/hdinsight-delete-cluster-warning.md)]

## <a name="next-steps"></a>Další postup

V tomto dokumentu jste se naučili, jak používat spout Java Event Hubs a funkce bolt od topologie C# pro práci s daty v Azure Event Hubs. Další informace o vytváření topologie C#, naleznete v následujících tématech:

* [Vývoj topologie C# pro Apache Storm v HDInsight pomocí sady Visual Studio](apache-storm-develop-csharp-visual-studio-topology.md)
* [Průvodce programováním spojovací bod služby](apache-storm-scp-programming-guide.md)
* [Příklad topologií pro Storm v HDInsight](apache-storm-example-topology.md)
