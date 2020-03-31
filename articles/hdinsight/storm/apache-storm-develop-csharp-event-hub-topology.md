---
title: Zpracovávat události z centra událostí pomocí Stormu – Azure HDInsight
description: Zjistěte, jak zpracovávat data z Center událostí Azure pomocí topologie C# Storm vytvořené v Sadě Visual Studio pomocí nástrojů HDInsight pro Visual Studio.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.date: 01/14/2020
ms.openlocfilehash: 85b7093df99127b690c51e8f2f28d18e3f5f3c95
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "75981642"
---
# <a name="process-events-from-azure-event-hubs-with-apache-storm-on-hdinsight-c"></a>Zpracování událostí z Centra událostí Azure pomocí Apache Storm na HDInsightu (C#)

Přečtěte si, jak pracovat s Azure Event Hubs z [Apache Storm](https://storm.apache.org/) na HDInsightu. Tento dokument používá topologii C# Storm ke čtení a zápisu dat z event hubů

> [!NOTE]  
> Java verzi tohoto projektu najdete v tématu [zpracování událostí z Azure Event Hubs s Apache Storm na HDInsight (Java)](https://github.com/Azure-Samples/hdinsight-java-storm-eventhub).

## <a name="scpnet"></a>SCP.NET

Kroky v tomto dokumentu používají SCP.NET, balíček NuGet, který usnadňuje vytváření topologií c# a komponent pro použití s Storm na HDInsight.

HDInsight 3.4 a více použití Mono ke spuštění C# topologie. Příklad použitý v tomto dokumentu pracuje s HDInsight 3.6. Pokud máte v plánu na vytvoření vlastních řešení .NET pro HDInsight, zkontrolujte mono [kompatibility](https://www.mono-project.com/docs/about-mono/compatibility/) dokumentu pro potenciální nekompatibility.

### <a name="cluster-versioning"></a>Správa verzí clusteru

Balíček Microsoft.SCP.Net.SDK NuGet, který používáte pro svůj projekt, se musí shodovat s hlavní verzí stormu nainstalovanou na webu HDInsight. HdInsight verze 3.5 a 3.6 používat Storm 1.x, takže je nutné použít SCP.NET verze 1.0.x.x s těmito clustery.

Topologií jazyka C# musí být také cílováno na rozhraní .NET 4.5.

## <a name="how-to-work-with-event-hubs"></a>Jak pracovat s event huby

Společnost Microsoft poskytuje sadu součástí jazyka Java, které lze použít ke komunikaci s centry událostí z topologie bouře. Soubor archivu Java (JAR), který obsahuje verzi těchto komponent kompatibilní s [https://github.com/hdinsight/mvn-repo/raw/master/org/apache/storm/storm-eventhubs/1.1.0.1/storm-eventhubs-1.1.0.1.jar](https://github.com/hdinsight/mvn-repo/raw/master/org/apache/storm/storm-eventhubs/1.1.0.1/storm-eventhubs-1.1.0.1.jar)HDInsight 3.6, naleznete na adrese .

> [!IMPORTANT]  
> Zatímco komponenty jsou napsány v jazyce Java, můžete je snadno použít z topologie Jazyka C#.

V tomto příkladu se používají následující součásti:

* __EventHubSpout__: Čte data z centra událostí.
* __EventHubBolt__: Zapisuje data do centra událostí.
* __EventHubSpoutConfig__: Používá se ke konfiguraci EventHubSpout.
* __EventHubBoltConfig__: Používá se ke konfiguraci EventHubBolt.

### <a name="example-spout-usage"></a>Příklad použití výtoku

SCP.NET poskytuje metody pro přidání EventHubSpout do topologie. Tyto metody usnadňují přidání výtoku než použití obecných metod pro přidání komponenty Java. Následující příklad ukazuje, jak vytvořit hubici pomocí metod __SetEventHubSpout__ a **EventHubSpoutConfig** poskytovaných SCP.NET:

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

Předchozí příklad vytvoří novou komponentu výtoku s názvem __EventHubSpout__a nakonfiguruje ji pro komunikaci s centrem událostí. Nápověda paralelismu pro komponentu je nastavena na počet oddílů v centru událostí. Toto nastavení umožňuje storm vytvořit instanci komponenty pro každý oddíl.

### <a name="example-bolt-usage"></a>Příklad použití šroubu

Pomocí metody **JavaComponmentConstructor** vytvořte instanci šroubu. Následující příklad ukazuje, jak vytvořit a nakonfigurovat novou instanci **EventHubBolt**:

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
> Tento příklad používá Clojure výraz předaný jako řetězec, namísto použití **JavaComponentConstructor** k vytvoření **EventHubBoltConfig**, jako příklad výtoku. Obě metody funguje. Použijte metodu, která vám připadá nejlepší.

## <a name="download-the-completed-project"></a>Stažení dokončeného projektu

Můžete si stáhnout kompletní verzi projektu vytvořeného v tomto článku z [GitHubu](https://github.com/Azure-Samples/hdinsight-dotnet-java-storm-eventhub). Stále však potřebujete poskytnout nastavení konfigurace podle kroků v tomto článku.

### <a name="prerequisites"></a>Požadavky

* Cluster Apache Storm na HDInsight. Viz [Vytvoření clusterů Apache Hadoop pomocí portálu Azure](../hdinsight-hadoop-create-linux-clusters-portal.md) a vyberte **Storm** pro **typ clusteru**.

* Centrum [událostí Azure](../../event-hubs/event-hubs-create.md).

* Sada [Azure .NET SDK](https://azure.microsoft.com/downloads/).

* [Nástroje HDInsight pro Visual Studio](../hadoop/apache-hadoop-visual-studio-tools-get-started.md).

* Java JDK 1.8 nebo novější ve vývojovém prostředí. Soubory ke stažení z jdk jsou k dispozici u společnosti [Oracle](https://aka.ms/azure-jdks).

  * Proměnná **prostředí JAVA_HOME** musí ukazovat na adresář, který obsahuje jazyk Java.
  * Adresář **%JAVA_HOME%/bin** musí být v cestě.

## <a name="download-the-event-hubs-components"></a>Stažení součástí Centra událostí

Stáhněte si huby událostí a [https://github.com/hdinsight/mvn-repo/raw/master/org/apache/storm/storm-eventhubs/1.1.0.1/storm-eventhubs-1.1.0.1.jar](https://github.com/hdinsight/mvn-repo/raw/master/org/apache/storm/storm-eventhubs/1.1.0.1/storm-eventhubs-1.1.0.1.jar)součást šroubu z .

Vytvořte adresář `eventhubspout`s názvem a uložte soubor do adresáře.

## <a name="configure-event-hubs"></a>Konfigurace centra událostí

Centrum událostí je zdrojem dat pro tento příklad. Použijte informace v části "Vytvořit centrum událostí" [začínáme s event huby](../../event-hubs/event-hubs-create.md).

1. Po vytvoření centra událostí zobrazte nastavení **EventHubu** na webu Azure Portal a vyberte **zásady sdíleného přístupu**. Výběrem **možnosti + přidat** vytvořte následující zásady:

   | Name (Název) | Oprávnění |
   | --- | --- |
   | Spisovatel |Odeslat |
   | Čtenář |Naslouchat |

    ![Snímek obrazovky s oknem Zásady přístupu ke sdílení](./media/apache-storm-develop-csharp-event-hub-topology/share-access-policies.png)

2. Vyberte zásady **čtečky** a **zapisovače.** Zkopírujte a uložte hodnotu primárního klíče pro obě zásady, protože tyto hodnoty se použijí později.

## <a name="configure-the-eventhubwriter"></a>Konfigurace eventhubwriteru

1. Pokud jste ještě nenainstalovali nejnovější verzi nástrojů HDInsight pro Visual Studio, přečtěte si část [Začínáme používat nástroje HDInsight pro Visual Studio](../hadoop/apache-hadoop-visual-studio-tools-get-started.md).

2. Stáhněte si řešení z [eventhub-storm-hybrid](https://github.com/Azure-Samples/hdinsight-dotnet-java-storm-eventhub).

3. Otevřete **soubor EventHubExample.sln**. V projektu **EventHubWriter** otevřete soubor **App.config.** Použijte informace z centra událostí, které jste nakonfigurovali dříve k vyplnění hodnoty pro následující klíče:

   | Klíč | Hodnota |
   | --- | --- |
   | Název EventHubPolicyName |Zapisovač (Pokud jste pro zásadu použili jiný název se oprávněním *Odeslat,* použijte ho místo něj.) |
   | EventHubPolicyKey |Klíč pro zásady zapisovače. |
   | Obor EventHubNamespace |Obor názvů, který obsahuje centrum událostí. |
   | Název EventHubName |Název centra událostí. |
   | EventHubPartitionCount |Počet oddílů v centru událostí. |

4. Uložte a zavřete soubor **App.config.**

## <a name="configure-the-eventhubreader"></a>Konfigurace aplikace EventHubReader

1. Otevřete projekt **EventHubReader.**

2. Otevřete soubor **App.config** pro **aplikaci EventHubReader**. Použijte informace z centra událostí, které jste nakonfigurovali dříve k vyplnění hodnoty pro následující klíče:

   | Klíč | Hodnota |
   | --- | --- |
   | Název EventHubPolicyName |čtečka (Pokud jste pro zásadu použili jiný název s oprávněním *k naslouchání,* použijte ji místo něj.) |
   | EventHubPolicyKey |Klíč pro zásady čtečky. |
   | Obor EventHubNamespace |Obor názvů, který obsahuje centrum událostí. |
   | Název EventHubName |Název centra událostí. |
   | EventHubPartitionCount |Počet oddílů v centru událostí. |

3. Uložte a zavřete soubor **App.config.**

## <a name="deploy-the-topologies"></a>Nasazení topologie

1. V **Průzkumníku řešení**klikněte pravým tlačítkem myši na projekt **EventHubReader** a vyberte **odeslat storm na HDInsight**.

    ![Snímek obrazovky Průzkumníka řešení se zvýrazněnou možností Odeslat bouři na hdinsightu](./media/apache-storm-develop-csharp-event-hub-topology/submit-to-apache-storm.png)

2. V dialogovém okně **Odeslat topologii** vyberte **storm cluster**. Rozbalte **další konfigurace**, vyberte **položku Cesty k souborům v jazyce Java**, vyberte **...** a vyberte adresář obsahující dříve stažený soubor JAR. Nakonec klepněte na tlačítko **Odeslat**.

    ![Snímek obrazovky s dialogovým oknem Odeslat topologii](./media/apache-storm-develop-csharp-event-hub-topology/submit-storm-topology.png)

3. Po odeslání topologie se zobrazí **prohlížeč topologií bouře.** Chcete-li zobrazit informace o topologii, vyberte topologii **EventHubReader** v levém podokně.

    ![Snímek obrazovky prohlížeče Storm Topologies Viewer](./media/apache-storm-develop-csharp-event-hub-topology/storm-topology-viewer.png)

4. V **Průzkumníku řešení**klikněte pravým tlačítkem myši na projekt **EventHubWriter** a vyberte **odeslat storm na HDInsight**.

5. V dialogovém okně **Odeslat topologii** vyberte **storm cluster**. Rozbalte **další konfigurace**, vyberte **položku Cesty k souborům v jazyce Java**, vyberte **...** a vyberte adresář obsahující dříve stažený soubor JAR. Nakonec klepněte na tlačítko **Odeslat**.

6. Po odeslání topologie aktualizujte seznam topologie v **prohlížeči topologií bouře a** ověřte, zda jsou v clusteru spuštěny obě topologie.

7. V **prohlížeči Storm Topologies vyberte**topologii **EventHubReader.**

8. Chcete-li otevřít souhrn součásti šroubu, poklepejte na komponentu **LogBolt** v diagramu.

9. V části **Executors** vyberte jeden z odkazů ve sloupci **Port.** Zobrazí se informace zaznamenané komponentou. Protokolované informace jsou podobné následujícímu textu:

    ```output
    2017-03-02 14:51:29.255 m.s.p.TaskHost [INFO] Received C# STDOUT: 2017-03-02 14:51:29,255 [1] INFO  EventHubReader_LogBolt [(null)] - Received data: {"deviceValue":1830978598,"deviceId":"8566ccbc-034d-45db-883d-d8a31f34068e"}
    2017-03-02 14:51:29.283 m.s.p.TaskHost [INFO] Received C# STDOUT: 2017-03-02 14:51:29,283 [1] INFO  EventHubReader_LogBolt [(null)] - Received data: {"deviceValue":1756413275,"deviceId":"647a5eff-823d-482f-a8b4-b95b35ae570b"}
    2017-03-02 14:51:29.313 m.s.p.TaskHost [INFO] Received C# STDOUT: 2017-03-02 14:51:29,312 [1] INFO  EventHubReader_LogBolt [(null)] - Received data: {"deviceValue":1108478910,"deviceId":"206a68fa-8264-4d61-9100-bfdb68ee8f0a"}
    ```

## <a name="stop-the-topologies"></a>Zastavení topologií

Chcete-li topologii zastavit, vyberte jednotlivé topologie v **prohlížeči topologie bouře**a klepněte na tlačítko **Zabít**.

![Snímek obrazovky prohlížeče topologie bouře se zvýrazněným tlačítkem Zabít](./media/apache-storm-develop-csharp-event-hub-topology/kill-storm-topology1.png)

## <a name="delete-your-cluster"></a>Odstranění clusteru

[!INCLUDE [delete-cluster-warning](../../../includes/hdinsight-delete-cluster-warning.md)]

## <a name="next-steps"></a>Další kroky

V tomto dokumentu jste se naučili, jak používat huby událostí Java a šroub z topologie C# pro práci s daty v Azure Event Hubs. Další informace o vytváření topologií jazyka C# najdete v následujících tématech:

* [Vývoj topologií Jazyka C# pro Apache Storm na HDInsightu pomocí Visual Studia](apache-storm-develop-csharp-visual-studio-topology.md)
* [Průvodce programováním SCP](apache-storm-scp-programming-guide.md)
* [Příklad topologií pro Apache Storm v HDInsight](apache-storm-example-topology.md)
