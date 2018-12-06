---
title: Příjem událostí ze služby Azure Event Hubs pomocí knihovny .NET Core | Dokumentace Microsoftu
description: Začínáme s příjmem zpráv pomocí třídy EventProcessorHost v .NET Core
services: event-hubs
documentationcenter: na
author: ShubhaVijayasarathy
manager: timlt
editor: ''
ms.assetid: ''
ms.service: event-hubs
ms.devlang: na
ms.topic: get-started-article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 08/16/2018
ms.author: shvija
ms.openlocfilehash: fb99307182ba3ce8ba49a9ae09c12af173799c87
ms.sourcegitcommit: 5d837a7557363424e0183d5f04dcb23a8ff966bb
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/06/2018
ms.locfileid: "52963347"
---
# <a name="get-started-receiving-messages-with-the-event-processor-host-in-net-core"></a>Začínáme s příjmem zpráv pomocí třídy Event Processor Host v .NET Core
Event Hubs je služba, která zpracovává velké objemy dat událostí (telemetrie) z připojených zařízení a aplikací. Data, která shromáždíte pomocí služby Event Hubs, můžete uložit pomocí úložného clusteru nebo transformovat pomocí zprostředkovatele datové analýzy v reálném čase. Schopnost shromažďovat a zpracovávat velké množství událostí je klíčovou komponentou moderních aplikačních architektur, například internetu věcí (Internet of Things – IoT). Podrobnější přehled služby Event Hubs najdete v tématech [Přehled služby Event Hubs](event-hubs-about.md) a [Funkce služby Event Hubs](event-hubs-features.md).

Tento kurz ukazuje, jak napsat konzolovou aplikaci .NET Core, která přijímá zprávy z centra událostí pomocí třídy [Event Processor Host](event-hubs-event-processor-host.md). [Event Processor Host](event-hubs-event-processor-host.md) je třída rozhraní .NET, která zjednodušuje přijímání událostí z center událostí tím, že spravuje trvalé kontrolní body a paralelní příjmy z těchto center událostí. Pomocí třídy Event Processor Host můžete události rozdělit mezi několik příjemců, i když jsou hostovaní v různých uzlech. Tento příklad ukazuje způsob použití třídy Event Processor Host pro jednoho příjemce. Ukázka [horizontálního navýšení kapacity zpracování událostí][Horizontální navýšení kapacity zpracování událostí pomocí služby Event Hubs] znázorňuje způsob použití třídy Event Processor Host v případě několika příjemců.

> [!NOTE]
> Tento rychlý start si můžete stáhnout jako ukázku z [GitHubu](https://github.com/Azure/azure-event-hubs/tree/master/samples/DotNet/Microsoft.Azure.EventHubs/SampleEphReceiver), nahradit řetězce `EventHubConnectionString`, `EventHubName`, `StorageAccountName`, `StorageAccountKey` a `StorageContainerName` hodnotami pro vaše centrum událostí a spustit. Alternativně můžete vytvořit vlastní řešení podle kroků v tomto kurzu.

## <a name="prerequisites"></a>Požadavky
* [Sada Microsoft Visual Studio 2015 nebo 2017](https://www.visualstudio.com). V příkladech v tomto kurzu se používá sada Visual Studio 2017, ale podporuje se i sada Visual Studio 2015.
* [Nástroje .NET Core pro sadu Visual Studio 2015 nebo 2017](https://www.microsoft.com/net/core).

## <a name="create-an-event-hubs-namespace-and-an-event-hub"></a>Vytvoření oboru názvů Event Hubs a centra událostí
Prvním krokem je použití webu [Azure Portal](https://portal.azure.com) k vytvoření oboru názvů typu Event Hubs a získání přihlašovacích údajů pro správu, které vaše aplikace potřebuje ke komunikaci s centrem událostí. Pokud chcete vytvořit obor názvů a centrum událostí, postupujte podle pokynů v [tomto článku](event-hubs-create.md) a pak pokračujte podle následujících pokynů v tomto kurzu.

[!INCLUDE [event-hubs-create-storage](../../includes/event-hubs-create-storage.md)]

## <a name="create-a-console-application"></a>Vytvoření konzolové aplikace

Spusťte Visual Studio. V nabídce **Soubor** klikněte na položku **Nový** a potom klikněte na položku **Projekt**. Vytvořte konzolovou aplikaci .NET Core.

![Nový projekt][2]

## <a name="add-the-event-hubs-nuget-package"></a>Přidání balíčku NuGet služby Event Hubs

Pomocí následujícího postupu do svého projektu přidejte balíčky NuGet knihoven .NET Standard [**Microsoft.Azure.EventHubs**](https://www.nuget.org/packages/Microsoft.Azure.EventHubs/) a [**Microsoft.Azure.EventHubs.Processor**](https://www.nuget.org/packages/Microsoft.Azure.EventHubs.Processor/): 

1. Klikněte pravým tlačítkem na nově vytvořený projekt a vyberte možnost **Spravovat balíčky NuGet**.
2. Klikněte na kartu **Procházet**, vyhledejte **Microsoft.Azure.EventHubs** a pak vyberte balíček **Microsoft.Azure.EventHubs**. Klikněte na **Instalovat** a dokončete instalaci, pak zavřete dialogové okno.
3. Zopakujte kroky 1 a 2 a nainstalujte balíček **Microsoft.Azure.EventHubs.Processor**.

## <a name="implement-the-ieventprocessor-interface"></a>Implementace rozhraní IEventProcessor

1. V Průzkumníku řešení klikněte pravým tlačítkem na projekt, klikněte na **Přidat** a pak klikněte na **Třída**. Pojmenujte novou třídu **SimpleEventProcessor**.

2. Otevřete soubor SimpleEventProcessor.cs a přidejte na jeho začátek následující příkazy `using`.

    ```csharp
    using Microsoft.Azure.EventHubs;
    using Microsoft.Azure.EventHubs.Processor;
    using System.Threading.Tasks;
    ```

3. Implementujte rozhraní `IEventProcessor`. Celý obsah třídy `SimpleEventProcessor` nahraďte následujícím kódem:

    ```csharp
    public class SimpleEventProcessor : IEventProcessor
    {
        public Task CloseAsync(PartitionContext context, CloseReason reason)
        {
            Console.WriteLine($"Processor Shutting Down. Partition '{context.PartitionId}', Reason: '{reason}'.");
            return Task.CompletedTask;
        }

        public Task OpenAsync(PartitionContext context)
        {
            Console.WriteLine($"SimpleEventProcessor initialized. Partition: '{context.PartitionId}'");
            return Task.CompletedTask;
        }

        public Task ProcessErrorAsync(PartitionContext context, Exception error)
        {
            Console.WriteLine($"Error on Partition: {context.PartitionId}, Error: {error.Message}");
            return Task.CompletedTask;
        }

        public Task ProcessEventsAsync(PartitionContext context, IEnumerable<EventData> messages)
        {
            foreach (var eventData in messages)
            {
                var data = Encoding.UTF8.GetString(eventData.Body.Array, eventData.Body.Offset, eventData.Body.Count);
                Console.WriteLine($"Message received. Partition: '{context.PartitionId}', Data: '{data}'");
            }

            return context.CheckpointAsync();
        }
    }
    ```

## <a name="update-the-main-method-to-use-simpleeventprocessor"></a>Aktualizace metody Main pro použití třídy SimpleEventProcessor

1. Do horní části souboru Program.cs přidejte následující příkazy `using`.

    ```csharp
    using Microsoft.Azure.EventHubs;
    using Microsoft.Azure.EventHubs.Processor;
    using System.Threading.Tasks;
    ```

2. Do třídy `Program` přidejte konstanty pro připojovací řetězec centra událostí, název centra událostí, název kontejneru účtu úložiště, název účtu úložiště a klíč účtu úložiště. Přidejte následující kód a zástupné texty nahraďte odpovídajícími hodnotami:

    ```csharp
    private const string EventHubConnectionString = "{Event Hubs connection string}";
    private const string EventHubName = "{Event Hub path/name}";
    private const string StorageContainerName = "{Storage account container name}";
    private const string StorageAccountName = "{Storage account name}";
    private const string StorageAccountKey = "{Storage account key}";

    private static readonly string StorageConnectionString = string.Format("DefaultEndpointsProtocol=https;AccountName={0};AccountKey={1}", StorageAccountName, StorageAccountKey);
    ```   

3. Do třídy `Program` přidejte následujícím způsobem novou metodu `MainAsync`:

    ```csharp
    private static async Task MainAsync(string[] args)
    {
        Console.WriteLine("Registering EventProcessor...");

        var eventProcessorHost = new EventProcessorHost(
            EventHubName,
            PartitionReceiver.DefaultConsumerGroupName,
            EventHubConnectionString,
            StorageConnectionString,
            StorageContainerName);

        // Registers the Event Processor Host and starts receiving messages
        await eventProcessorHost.RegisterEventProcessorAsync<SimpleEventProcessor>();

        Console.WriteLine("Receiving. Press ENTER to stop worker.");
        Console.ReadLine();

        // Disposes of the Event Processor Host
        await eventProcessorHost.UnregisterEventProcessorAsync();
    }
    ```

3. Do metody `Main` přidejte následující řádek kódu:

    ```csharp
    MainAsync(args).GetAwaiter().GetResult();
    ```

    Soubor Program.cs by měl vypadat takhle:

    ```csharp
    namespace SampleEphReceiver
    {

        public class Program
        {
            private const string EventHubConnectionString = "{Event Hubs connection string}";
            private const string EventHubName = "{Event Hub path/name}";
            private const string StorageContainerName = "{Storage account container name}";
            private const string StorageAccountName = "{Storage account name}";
            private const string StorageAccountKey = "{Storage account key}";

            private static readonly string StorageConnectionString = string.Format("DefaultEndpointsProtocol=https;AccountName={0};AccountKey={1}", StorageAccountName, StorageAccountKey);

            public static void Main(string[] args)
            {
                MainAsync(args).GetAwaiter().GetResult();
            }

            private static async Task MainAsync(string[] args)
            {
                Console.WriteLine("Registering EventProcessor...");

                var eventProcessorHost = new EventProcessorHost(
                    EventHubName,
                    PartitionReceiver.DefaultConsumerGroupName,
                    EventHubConnectionString,
                    StorageConnectionString,
                    StorageContainerName);

                // Registers the Event Processor Host and starts receiving messages
                await eventProcessorHost.RegisterEventProcessorAsync<SimpleEventProcessor>();

                Console.WriteLine("Receiving. Press ENTER to stop worker.");
                Console.ReadLine();

                // Disposes of the Event Processor Host
                await eventProcessorHost.UnregisterEventProcessorAsync();
            }
        }
    }
    ```

4. Spusťte program a zkontrolujte, že nejsou žádné chyby.

Blahopřejeme! Obdrželi jste nyní zprávy z centra událostí pomocí třídy Event Processor Host.

> [!NOTE]
> Tento kurz používá jednu instanci třídy [EventProcessorHost](event-hubs-event-processor-host.md). Pokud chcete zvýšit propustnost, doporučujeme spustit několik instancí třídy [EventProcessorHost](event-hubs-event-processor-host.md), jak je znázorněno v ukázce [škálovaného zpracování událostí](https://code.msdn.microsoft.com/Service-Bus-Event-Hub-45f43fc3). V těchto případech se spolu navzájem automaticky koordinuje několik instancí, aby dokázaly vyrovnávat zatížení přijatých událostí. 

## <a name="next-steps"></a>Další postup
V tomto rychlém startu jste vytvořili aplikaci .NET Core, která se zobrazila zprávy z centra událostí. Zjistěte, jak odesílat události do centra událostí pomocí .NET Core, najdete v článku [odesílání událostí z centra událostí – .NET Core](event-hubs-dotnet-standard-getstarted-send.md).

[1]: ./media/event-hubs-dotnet-standard-getstarted-receive-eph/event-hubs-python1.png
[2]: ./media/event-hubs-dotnet-standard-getstarted-receive-eph/netcorercv.png
