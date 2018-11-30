---
title: Zpracování událostí ze služby Event Hubs se Stormem – Azure HDInsight
description: Zjistěte, jak můžete zpracovávat data z Azure Event Hubs s topologií Storm v jazyce C# vytvořené v sadě Visual Studio pomocí nástrojů HDInsight pro Visual Studio.
services: hdinsight,notification hubs
author: hrasheed-msft
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.date: 11/27/2017
ms.author: hrasheed
ROBOTS: NOINDEX
ms.openlocfilehash: 027c8155c84959ca429eb9b093a155ac22aaf324
ms.sourcegitcommit: 56d20d444e814800407a955d318a58917e87fe94
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/29/2018
ms.locfileid: "52582204"
---
# <a name="process-events-from-azure-event-hubs-with-apache-storm-on-hdinsight-c"></a>Zpracování událostí z Azure Event Hubs pomocí Apache Storm v HDInsight (C#)

Zjistěte, jak pracovat s Azure Event Hubs z [Apache Storm](http://storm.apache.org/) v HDInsight. Tento dokument topologie Storm v jazyce C# používá ke čtení a zápisu dat ze služby Event Hubs

> [!NOTE]
> Verze Javy pro tento projekt, naleznete v tématu [zpracovávat události z Azure Event Hubs pomocí Apache Storm v HDInsight (Java)](https://azure.microsoft.com/resources/samples/hdinsight-java-storm-eventhub/).

## <a name="scpnet"></a>SCP.NET

Kroky v tomto dokumentu používají SCP.NET, balíček NuGet, který usnadňuje vytváření topologií C# a komponenty pro použití se Stormem v HDInsight.

> [!IMPORTANT]
> Přestože postup v tomto dokumentu využívají prostředí pro vývoj Windows pomocí sady Visual Studio, zkompilovaný projekt můžete odeslat do Storm v clusteru HDInsight, který využívá systém Linux. Clustery se systémem Linux vytvořené po 28. říjnem 2016, podporují pouze topologie SCP.NET.

HDInsight 3.4 a vyšší použití Mono pro spouštění topologií C#. V příkladu v tomto dokumentu pracuje s HDInsight 3.6. Pokud plánujete vytvořit svoje vlastní řešení .NET pro HDInsight, zkontrolujte, [Mono compatibility](http://www.mono-project.com/docs/about-mono/compatibility/) dokumentů, kde najdete potenciální nekompatibility.

### <a name="cluster-versioning"></a>Správa verzí clusteru

Balíček NuGet Microsoft.scp.NET.SDK, který, který používáte pro váš projekt musí shodovat s hlavní verzí Stormu nainstalovanou v HDInsight. HDInsight verze 3.5 a 3.6 používat Storm 1.x, takže je nutné použít verzi 1.0.x.x SCP.NET se tyto clustery.

> [!IMPORTANT]
> V příkladu v tomto dokumentu očekává, že HDInsight 3.5 a 3.6 clusteru.
>
> HDInsight od verze 3.4 výše používá výhradně operační systém Linux. Další informace najdete v tématu [Vyřazení prostředí HDInsight ve Windows](../hdinsight-component-versioning.md#hdinsight-windows-retirement).

Topologií C# musí také cílit na .NET 4.5.

## <a name="how-to-work-with-event-hubs"></a>Jak pracovat s Event Hubs

Společnost Microsoft poskytuje sadu komponent v jazyce Java, které můžete použít ke komunikaci se službou Event Hubs z topologie Storm. Můžete najít soubor archivu (JAR) Java, která obsahuje HDInsight 3.6 kompatibilní verze těchto součástí v [ https://github.com/hdinsight/mvn-repo/raw/master/org/apache/storm/storm-eventhubs/1.1.0.1/storm-eventhubs-1.1.0.1.jar ](https://github.com/hdinsight/mvn-repo/raw/master/org/apache/storm/storm-eventhubs/1.1.0.1/storm-eventhubs-1.1.0.1.jar).

> [!IMPORTANT]
> Zatímco součásti jsou napsané v jazyce Java, je můžete snadno použít z topologií v jazyce C#.

V tomto příkladu se používají následující komponenty:

* __EventHubSpout__: čte data ze služby Event Hubs.
* __EventHubBolt__: zapisuje data do služby Event Hubs.
* __EventHubSpoutConfig__: slouží ke konfiguraci EventHubSpout.
* __EventHubBoltConfig__: slouží ke konfiguraci EventHubBolt.

### <a name="example-spout-usage"></a>Příklad použití spout

SCP.NET poskytuje metody pro přidání EventHubSpout do topologie. Tyto metody usnadňují přidání spout než použití obecných metod pro přidání komponenty Java. Následující příklad ukazuje, jak vytvořit spout pomocí __SetEventHubSpout__ a **EventHubSpoutConfig** metody poskytované objektem SCP.NET:

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

Předchozí příklad vytvoří nové součásti spout s názvem __EventHubSpout__a nakonfiguruje ho, aby komunikoval s centrem událostí. Pomocný parametr paralelismus pro komponentu nastavená na počet oddílů události rozbočovače. Toto nastavení umožňuje Storm k vytvoření instance komponenty pro každý oddíl.

### <a name="example-bolt-usage"></a>Příklad použití boltu

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
> Tento příklad používá Clojure výrazu předaného jako řetězec namísto použití **JavaComponentConstructor** k vytvoření **EventHubBoltConfig**, jako v příkladu spout. Některé z metod funguje. Použijte metodu, která je pro vás nejlepší.

## <a name="download-the-completed-project"></a>Stáhnout dokončený projekt

Můžete stáhnout úplnou verzi do projektu vytvořeného v tomto kurzu z [Githubu](https://github.com/Azure-Samples/hdinsight-dotnet-java-storm-eventhub). Nicméně stále musíte zadat nastavení konfigurace pomocí následujících kroků v tomto kurzu.

### <a name="prerequisites"></a>Požadavky

* [Apache Storm v clusteru HDInsight verze 3.5 a 3.6](apache-storm-tutorial-get-started-linux.md).

    > [!WARNING]
    > V příkladu v tomto dokumentu vyžaduje Storm v HDInsight verze 3.5 a 3.6. To nebude fungovat se staršími verzemi HDInsight, z důvodu rozbíjející změny názvu třídy. Verzi tohoto příkladu, který pracuje s starší clustery, naleznete v části [Githubu](https://github.com/Azure-Samples/hdinsight-dotnet-java-storm-eventhub/releases).

* [Centrum událostí Azure](../../event-hubs/event-hubs-create.md).

* [Sady Azure .NET SDK](https://azure.microsoft.com/downloads/).

* [Nástroje HDInsight pro Visual Studio](../hadoop/apache-hadoop-visual-studio-tools-get-started.md).

* Java JDK 1.8 nebo novějším na vašem vývojovém prostředí. JDK soubory ke stažení jsou k dispozici z [Oracle](https://aka.ms/azure-jdks).

  * **JAVA_HOME** proměnné prostředí musí odkazovat na adresář, který obsahuje Java.
  * **%JAVA_HOME%/bin** adresář musí být v cestě.

## <a name="download-the-event-hubs-components"></a>Stáhnout součásti služby Event Hubs

Stáhněte si spoutu centra událostí a funkce bolt komponenty z [ https://github.com/hdinsight/mvn-repo/raw/master/org/apache/storm/storm-eventhubs/1.1.0.1/storm-eventhubs-1.1.0.1.jar ](https://github.com/hdinsight/mvn-repo/raw/master/org/apache/storm/storm-eventhubs/1.1.0.1/storm-eventhubs-1.1.0.1.jar).

Vytvořte adresář `eventhubspout`a uložte soubor do adresáře.

## <a name="configure-event-hubs"></a>Konfigurovat službu Event Hubs

Event Hubs je zdroj dat pro účely tohoto příkladu. Použijte informace v části "Vytvoření centra událostí" [Začínáme se službou Event Hubs](../../event-hubs/event-hubs-create.md).

1. Po vytvoření centra událostí, zobrazení **EventHub** nastavení v Azure portal a vyberte **zásady sdíleného přístupu**. Vyberte **+ přidat** přidáte následující zásady:

   | Název | Oprávnění |
   | --- | --- |
   | zapisovatel |Odeslat |
   | čtenář |Naslouchat |

    ![Okno zásad přístupu snímek sdílené složky](./media/apache-storm-develop-csharp-event-hub-topology/sas.png)

2. Vyberte **čtečky** a **zapisovače** zásady. Zkopírujte a uložte hodnotu primárního klíče pro obě zásady, protože tyto hodnoty se později použijí.

## <a name="configure-the-eventhubwriter"></a>Konfigurace EventHubWriter

1. Pokud jste ještě nenainstalovali nejnovější verzi nástrojů HDInsight pro Visual Studio, přečtěte si téma [začněte používat nástroje HDInsight pro Visual Studio](../hadoop/apache-hadoop-visual-studio-tools-get-started.md).

2. Stáhněte si řešení od [centra událostí stormu – hybridní](https://github.com/Azure-Samples/hdinsight-dotnet-java-storm-eventhub).

3. V **EventHubWriter** projekt, otevřete **App.config** souboru. Pomocí informací z centra událostí, který jste nakonfigurovali v předchozích krocích zadejte hodnotu pro následující klíče:

   | Klíč | Hodnota |
   | --- | --- |
   | EventHubPolicyName |Zapisovač (Pokud jste použili jiný název pro zásadu s *odeslat* oprávnění, použijte ji.) |
   | EventHubPolicyKey |Klíč pro zásady zapisovače. |
   | EventHubNamespace |Obor názvů, který obsahuje vaše Centrum událostí. |
   | eventHubName |Název vašeho centra událostí. |
   | EventHubPartitionCount |Počet oddílů v Centru událostí. |

4. Uložte a zavřete **App.config** souboru.

## <a name="configure-the-eventhubreader"></a>Konfigurace EventHubReader

1. Otevřít **EventHubReader** projektu.

2. Otevřít **App.config** souboru **EventHubReader**. Pomocí informací z centra událostí, který jste nakonfigurovali v předchozích krocích zadejte hodnotu pro následující klíče:

   | Klíč | Hodnota |
   | --- | --- |
   | EventHubPolicyName |čtečky (Pokud jste použili jiný název pro zásadu s *naslouchání* oprávnění, použijte ji.) |
   | EventHubPolicyKey |Klíč pro zásady čtecí zařízení. |
   | EventHubNamespace |Obor názvů, který obsahuje vaše Centrum událostí. |
   | eventHubName |Název vašeho centra událostí. |
   | EventHubPartitionCount |Počet oddílů v Centru událostí. |

3. Uložte a zavřete **App.config** souboru.

## <a name="deploy-the-topologies"></a>Nasazení topologie

1. Z **Průzkumníka řešení**, klikněte pravým tlačítkem myši **EventHubReader** projektu a vyberte **odeslat do Storm v HDInsight**.

    ![Snímek obrazovky Průzkumníka řešení, odeslat do Storm v HDInsight zvýrazněnou](./media/apache-storm-develop-csharp-event-hub-topology/submittostorm.png)

2. Na **odeslat topologii** dialogovém okně vyberte váš **Storm Cluster**. Rozbalte **další konfigurace, které**vyberte **cesty k souborům Java**vyberte **...** a vyberte adresář, který obsahuje soubor JAR, který jste předtím stáhli. Nakonec klikněte na tlačítko **odeslat**.

    ![Dialogové okno snímek obrazovky odeslat topologii](./media/apache-storm-develop-csharp-event-hub-topology/submit.png)

3. Při odeslání topologie **prohlížeč topologií Storm** se zobrazí. Chcete-li zobrazit informace o topologii, vyberte **EventHubReader** topologie v levém podokně.

    ![Snímek obrazovky prohlížeče topologií Storm](./media/apache-storm-develop-csharp-event-hub-topology/topologyviewer.png)

4. Z **Průzkumníka řešení**, klikněte pravým tlačítkem myši **EventHubWriter** projektu a vyberte **odeslat do Storm v HDInsight**.

5. Na **odeslat topologii** dialogovém okně vyberte váš **Storm Cluster**. Rozbalte **další konfigurace, které**vyberte **cesty k souborům Java**vyberte **...** a vyberte adresář, který obsahuje soubor JAR, který jste předtím stáhli. Nakonec klikněte na tlačítko **odeslat**.

6. Pokud byla odeslána na topologii, aktualizujte seznam topologie v **prohlížeč topologií Storm** k ověření, že cluster běží obou topologií.

7. V **prohlížeč topologií Storm**, vyberte **EventHubReader** topologie.

8. Spustit souhrn bolt součásti, dvakrát klikněte **LogBolt** komponentu v diagramu.

9. V **prováděcí moduly** vyberte jeden z odkazů v **Port** sloupce. Zobrazí se informacím protokolovaným v komponentě. Zaznamenané informace se podobá následujícímu textu:

        2017-03-02 14:51:29.255 m.s.p.TaskHost [INFO] Received C# STDOUT: 2017-03-02 14:51:29,255 [1] INFO  EventHubReader_LogBolt [(null)] - Received data: {"deviceValue":1830978598,"deviceId":"8566ccbc-034d-45db-883d-d8a31f34068e"}
        2017-03-02 14:51:29.283 m.s.p.TaskHost [INFO] Received C# STDOUT: 2017-03-02 14:51:29,283 [1] INFO  EventHubReader_LogBolt [(null)] - Received data: {"deviceValue":1756413275,"deviceId":"647a5eff-823d-482f-a8b4-b95b35ae570b"}
        2017-03-02 14:51:29.313 m.s.p.TaskHost [INFO] Received C# STDOUT: 2017-03-02 14:51:29,312 [1] INFO  EventHubReader_LogBolt [(null)] - Received data: {"deviceValue":1108478910,"deviceId":"206a68fa-8264-4d61-9100-bfdb68ee8f0a"}

## <a name="stop-the-topologies"></a>Zastavení topologií

Pokud chcete zastavit topologie, vyberte každou topologii v **prohlížeč topologie Storm**, pak klikněte na tlačítko **Kill**.

![Snímek obrazovky z Storm topologie Vieweru. to se zvýrazněným tlačítkem Kill](./media/apache-storm-develop-csharp-event-hub-topology/killtopology.png)

## <a name="delete-your-cluster"></a>Odstranit cluster

[!INCLUDE [delete-cluster-warning](../../../includes/hdinsight-delete-cluster-warning.md)]

## <a name="next-steps"></a>Další postup

V tomto dokumentu jste se dozvěděli, jak používat Java Event Hubs spout a funkce bolt od topologie C# pro práci s daty ve službě Azure Event Hubs. Další informace o vytváření topologií C#, naleznete v následujících tématech:

* [Vývoj topologií C# pro Apache Storm v HDInsight pomocí sady Visual Studio](apache-storm-develop-csharp-visual-studio-topology.md)
* [Průvodce programováním pro spojovací bod služby](apache-storm-scp-programming-guide.md)
* [Příklad topologií pro Apache Storm v HDInsight](apache-storm-example-topology.md)
