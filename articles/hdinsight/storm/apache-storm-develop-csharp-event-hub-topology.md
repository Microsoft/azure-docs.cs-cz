---
title: Zpracování událostí z Event Hubs s využitím více než Azure HDInsight
description: Naučte se zpracovávat data z Azure Event Hubs pomocí C# topologie pro zaplavení vytvořenou v aplikaci Visual Studio pomocí nástrojů HDInsight pro Visual Studio.
author: hrasheed-msft
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.date: 11/27/2017
ms.author: hrasheed
ROBOTS: NOINDEX
ms.openlocfilehash: 53399fbdeba44b184ef4e76c89affefd29dbc413
ms.sourcegitcommit: 083aa7cc8fc958fc75365462aed542f1b5409623
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/11/2019
ms.locfileid: "70915249"
---
# <a name="process-events-from-azure-event-hubs-with-apache-storm-on-hdinsight-c"></a>Zpracování událostí z Azure Event Hubs s využitím Apache Storm veC#službě HDInsight ()

Naučte se pracovat s Azure Event Hubs z [Apache Storm](https://storm.apache.org/) v HDInsight. Tento dokument používá C# topologii pro čtení a zápis dat z Event Hubs

> [!NOTE]  
> Verzi tohoto projektu v jazyce Java najdete v tématu [zpracování událostí z Azure Event Hubs s Apache Storm v HDInsight (Java)](https://azure.microsoft.com/resources/samples/hdinsight-java-storm-eventhub/).

## <a name="scpnet"></a>SCP.NET

Kroky v tomto dokumentu používají SCP.NET, balíček NuGet, který usnadňuje vytváření C# topologií a komponent pro použití s využitím více než v HDInsight.

> [!IMPORTANT]  
> I když postup v tomto dokumentu spoléhá na vývojové prostředí systému Windows se sadou Visual Studio, zkompilovaný projekt lze odeslat do systému v clusteru HDInsight, který používá systém Linux. Pouze clustery se systémem Linux vytvořené po 28. říjnu 2016 podporují topologie SCP.NET.

HDInsight 3,4 a vyšší použití mono ke spuštění C# topologií. Příklad použitý v tomto dokumentu funguje se službou HDInsight 3,6. Pokud máte v úmyslu vytvořit vlastní řešení .NET pro HDInsight, podívejte se na dokument [kompatibility mono](https://www.mono-project.com/docs/about-mono/compatibility/) , abyste mohli nekompatibilní problémy.

### <a name="cluster-versioning"></a>Správa verzí clusteru

Balíček NuGet Microsoft. SCP. NET. SDK, který použijete pro váš projekt, musí odpovídat hlavní verzi systému, která je nainstalovaná v HDInsight. Verze HDInsight 3,5 a 3,6 používají přeplavi 1. x, takže musíte použít SCP.NET verze 1.0. x. x s těmito clustery.

> [!IMPORTANT]  
> Příklad v tomto dokumentu očekává cluster HDInsight 3,5 nebo 3,6.
>
> HDInsight od verze 3.4 výše používá výhradně operační systém Linux. 

C#topologie musí také cílit na rozhraní .NET 4,5.

## <a name="how-to-work-with-event-hubs"></a>Jak pracovat s Event Hubs

Společnost Microsoft poskytuje sadu komponent Java, které lze použít ke komunikaci s Event Hubs z topologie s více podmnožinami. Soubor archivu Java (JAR), který obsahuje verzi kompatibilní s HDInsight 3,6, najdete v [https://github.com/hdinsight/mvn-repo/raw/master/org/apache/storm/storm-eventhubs/1.1.0.1/storm-eventhubs-1.1.0.1.jar](https://github.com/hdinsight/mvn-repo/raw/master/org/apache/storm/storm-eventhubs/1.1.0.1/storm-eventhubs-1.1.0.1.jar)části.

> [!IMPORTANT]  
> I když jsou komponenty napsané v jazyce Java, můžete je snadno použít z C# topologie.

V tomto příkladu se používají následující komponenty:

* __EventHubSpout__: Načte data z Event Hubs.
* __EventHubBolt__: Zapisuje data do Event Hubs.
* __EventHubSpoutConfig__: Používá se ke konfiguraci EventHubSpout.
* __EventHubBoltConfig__: Používá se ke konfiguraci EventHubBolt.

### <a name="example-spout-usage"></a>Příklad použití Spout

SCP.NET poskytuje metody pro přidání EventHubSpout do topologie. Tyto metody usnadňují přidání Spout než použití obecných metod pro přidání komponenty Java. Následující příklad ukazuje, jak vytvořit Spout pomocí metod __SetEventHubSpout__ a **EventHubSpoutConfig** poskytovaných SCP.NET:

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

Předchozí příklad vytvoří novou komponentu Spout s názvem __EventHubSpout__a nakonfiguruje ji ke komunikaci s centrem událostí. Pomocný parametr paralelismus pro komponentu je nastaven na počet oddílů v centru událostí. Toto nastavení umožňuje vytvořit instanci součásti pro každý oddíl.

### <a name="example-bolt-usage"></a>Příklad použití šroubu

K vytvoření instance šroubu použijte metodu **JavaComponmentConstructor** . Následující příklad ukazuje, jak vytvořit a nakonfigurovat novou instanci rozhraní **EventHubBolt**:

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
> Tento příklad používá výraz Clojure předaný jako řetězec namísto použití **JavaComponentConstructor** k vytvoření **EventHubBoltConfig**, jako by byl použit jako příklad Spout. Kterákoli z metod funguje. Použijte metodu, která je pro vás nejvhodnější.

## <a name="download-the-completed-project"></a>Stáhnout dokončený projekt

Kompletní verzi projektu vytvořeného v tomto článku si můžete stáhnout z [GitHubu](https://github.com/Azure-Samples/hdinsight-dotnet-java-storm-eventhub). Přesto ale budete muset zadat nastavení konfigurace podle kroků v tomto článku.

### <a name="prerequisites"></a>Požadavky

* Cluster Apache Storm v HDInsight. Přečtěte si téma [vytvoření Apache Hadoop clusterů pomocí Azure Portal](../hdinsight-hadoop-create-linux-clusters-portal.md) **a výběr funkce** pro **typ clusteru**.

    > [!WARNING]  
    > Příklad použitý v tomto dokumentu vyžaduje více než ve službě HDInsight verze 3,5 nebo 3,6. Nefunguje se staršími verzemi HDInsight, protože se změnily změny názvu třídy. Verzi tohoto příkladu, která funguje se staršími clustery, najdete v tématu [GitHub](https://github.com/Azure-Samples/hdinsight-dotnet-java-storm-eventhub/releases).

* [Centrum událostí Azure](../../event-hubs/event-hubs-create.md).

* [Sada Azure .NET SDK](https://azure.microsoft.com/downloads/).

* [Nástroje HDInsight pro Visual Studio](../hadoop/apache-hadoop-visual-studio-tools-get-started.md).

* Java JDK 1,8 nebo novější ve vašem vývojovém prostředí. Soubory ke stažení JDK jsou k dispozici od [Oracle](https://aka.ms/azure-jdks).

  * Proměnná prostředí **JAVA_HOME** musí odkazovat na adresář, který obsahuje Java.
  * Adresář **% JAVA_HOME%/bin** musí být v cestě.

## <a name="download-the-event-hubs-components"></a>Stáhnout součásti Event Hubs

Stáhněte komponentu Event Hubs Spout a šroub z [https://github.com/hdinsight/mvn-repo/raw/master/org/apache/storm/storm-eventhubs/1.1.0.1/storm-eventhubs-1.1.0.1.jar](https://github.com/hdinsight/mvn-repo/raw/master/org/apache/storm/storm-eventhubs/1.1.0.1/storm-eventhubs-1.1.0.1.jar).

Vytvořte adresář s názvem `eventhubspout`a uložte ho do adresáře.

## <a name="configure-event-hubs"></a>Konfigurace Event Hubs

Event Hubs je zdroj dat pro tento příklad. Použijte informace v části Vytvoření centra událostí tématu Začínáme [s Event Hubs](../../event-hubs/event-hubs-create.md).

1. Po vytvoření centra událostí si prohlédněte nastavení **EventHub** v Azure Portal a vyberte **zásady sdíleného přístupu**. Vyberte **+ Přidat** a přidejte následující zásady:

   | Name | Oprávnění |
   | --- | --- |
   | zapisovatel |Poslat |
   | čtenář |Naslouchat |

    ![Snímek obrazovky okna zásady přístupu ke sdílení](./media/apache-storm-develop-csharp-event-hub-topology/share-access-policies.png)

2. Vyberte zásady **čtecího zařízení** a **zapisovače** . Zkopírujte a uložte hodnotu primárního klíče pro obě zásady, protože se tyto hodnoty použijí později.

## <a name="configure-the-eventhubwriter"></a>Konfigurace EventHubWriter

1. Pokud jste ještě nenainstalovali nejnovější verzi nástrojů HDInsight pro Visual Studio, přečtěte si téma Začínáme [používat nástroje HDInsight pro Visual Studio](../hadoop/apache-hadoop-visual-studio-tools-get-started.md).

2. Stáhněte řešení z centra pro zaplavení [– hybridní](https://github.com/Azure-Samples/hdinsight-dotnet-java-storm-eventhub).

3. V projektu **EventHubWriter** otevřete soubor **App. config** . Pomocí informací z centra událostí, které jste nakonfigurovali dříve, vyplníte hodnotu pro následující klíče:

   | Klíč | Value |
   | --- | --- |
   | EventHubPolicyName |zapisovač (Pokud jste pro zásadu s oprávněním *Odeslat* použili jiný název, použijte ji místo toho.) |
   | EventHubPolicyKey |Klíč pro zásady zapisovače. |
   | EventHubNamespace |Obor názvů, který obsahuje centrum událostí. |
   | eventHubName |Název vašeho centra událostí. |
   | EventHubPartitionCount |Počet oddílů v centru událostí. |

4. Uložte a zavřete soubor **App. config** .

## <a name="configure-the-eventhubreader"></a>Konfigurace EventHubReader

1. Otevřete projekt **EventHubReader** .

2. Otevřete soubor **App. config** pro **EventHubReader**. Pomocí informací z centra událostí, které jste nakonfigurovali dříve, vyplníte hodnotu pro následující klíče:

   | Klíč | Value |
   | --- | --- |
   | EventHubPolicyName |Čtenář (Pokud jste pro zásadu použili jiný název, použijte místo toho oprávnění k *naslouchání* .) |
   | EventHubPolicyKey |Klíč pro zásady čtenáře |
   | EventHubNamespace |Obor názvů, který obsahuje centrum událostí. |
   | eventHubName |Název vašeho centra událostí. |
   | EventHubPartitionCount |Počet oddílů v centru událostí. |

3. Uložte a zavřete soubor **App. config** .

## <a name="deploy-the-topologies"></a>Nasazení topologií

1. Z **Průzkumník řešení**klikněte pravým tlačítkem myši na projekt **EventHubReader** a vyberte **Odeslat pro**zaplavení v HDInsight.

    ![Snímek obrazovky Průzkumník řešení se zvýrazněnou možností odeslat na HDInsight ve službě HDInsight](./media/apache-storm-develop-csharp-event-hub-topology/submit-to-apache-storm.png)

2. V dialogovém okně **Odeslat topologii** vyberte svůj **cluster**pro zaplavení. Rozbalte **Další konfigurace**, vyberte **cesty k souborům Java**, vyberte **...** a vyberte adresář, který obsahuje soubor JAR, který jste předtím stáhli. Nakonec klikněte na **Odeslat**.

    ![Snímek obrazovky dialogového okna pro odeslání topologie](./media/apache-storm-develop-csharp-event-hub-topology/submit-storm-topology.png)

3. Po odeslání topologie se zobrazí **prohlížeč topologie** pro vyplavení. Pokud chcete zobrazit informace o topologii, vyberte topologii **EventHubReader** v levém podokně.

    ![Snímek obrazovky s prohlížečem topologií s více podsystému](./media/apache-storm-develop-csharp-event-hub-topology/storm-topology-viewer.png)

4. Z **Průzkumník řešení**klikněte pravým tlačítkem myši na projekt **EventHubWriter** a vyberte **Odeslat pro**zaplavení v HDInsight.

5. V dialogovém okně **Odeslat topologii** vyberte svůj **cluster**pro zaplavení. Rozbalte **Další konfigurace**, vyberte **cesty k souborům Java**, vyberte **...** a vyberte adresář, který obsahuje soubor JAR, který jste stáhli dříve. Nakonec klikněte na **Odeslat**.

6. Po odeslání topologie aktualizujte seznam topologie v **prohlížeči topologií** se všemi topologiemi, abyste ověřili, že oba topologie jsou v clusteru spuštěné.

7. V **prohlížeči topologií se všemi topologiemi**vyberte topologii **EventHubReader** .

8. Chcete-li otevřít souhrn komponenty pro šroub, dvakrát klikněte na součást **LogBolt** v diagramu.

9. V části **vykonavatelé** vyberte jeden z odkazů ve sloupci **port** . Zobrazí se informace zaprotokolované součástí. Protokolované informace jsou podobné následujícímu textu:

        2017-03-02 14:51:29.255 m.s.p.TaskHost [INFO] Received C# STDOUT: 2017-03-02 14:51:29,255 [1] INFO  EventHubReader_LogBolt [(null)] - Received data: {"deviceValue":1830978598,"deviceId":"8566ccbc-034d-45db-883d-d8a31f34068e"}
        2017-03-02 14:51:29.283 m.s.p.TaskHost [INFO] Received C# STDOUT: 2017-03-02 14:51:29,283 [1] INFO  EventHubReader_LogBolt [(null)] - Received data: {"deviceValue":1756413275,"deviceId":"647a5eff-823d-482f-a8b4-b95b35ae570b"}
        2017-03-02 14:51:29.313 m.s.p.TaskHost [INFO] Received C# STDOUT: 2017-03-02 14:51:29,312 [1] INFO  EventHubReader_LogBolt [(null)] - Received data: {"deviceValue":1108478910,"deviceId":"206a68fa-8264-4d61-9100-bfdb68ee8f0a"}

## <a name="stop-the-topologies"></a>Zastavení topologií

Chcete-li zastavit topologie, vyberte každou topologii v **prohlížeči topologie**neplní a klikněte na tlačítko **ukončit**.

![Snímek obrazovky s prohlížečem topologie s více podsystému, se zvýrazněným tlačítkem Kill](./media/apache-storm-develop-csharp-event-hub-topology/kill-storm-topology1.png)

## <a name="delete-your-cluster"></a>Odstranění clusteru

[!INCLUDE [delete-cluster-warning](../../../includes/hdinsight-delete-cluster-warning.md)]

## <a name="next-steps"></a>Další kroky

V tomto dokumentu jste se naučili, jak používat Java Event Hubs Spout a šroub z C# topologie pro práci s daty v Azure Event Hubs. Další informace o vytváření C# topologií najdete v následujících tématech:

* [Vývoj C# topologií pro Apache Storm v HDInsight pomocí sady Visual Studio](apache-storm-develop-csharp-visual-studio-topology.md)
* [Průvodce programováním SCP](apache-storm-scp-programming-guide.md)
* [Příklad topologií pro Apache Storm v HDInsight](apache-storm-example-topology.md)
