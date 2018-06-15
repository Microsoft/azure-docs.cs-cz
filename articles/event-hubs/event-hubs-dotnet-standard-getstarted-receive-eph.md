---
title: Příjem událostí ze služby Azure Event Hubs pomocí knihovny .NET Standard | Microsoft Docs
description: Začínáme s příjmem zpráv pomocí třídy EventProcessorHost v .NET Standard
services: event-hubs
documentationcenter: na
author: sethmanheim
manager: timlt
editor: ''
ms.assetid: ''
ms.service: event-hubs
ms.devlang: na
ms.topic: get-started-article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/28/2017
ms.author: sethm
ms.openlocfilehash: 5eb5c2d1f0b85c907f788fb6ac752488601f613a
ms.sourcegitcommit: d87b039e13a5f8df1ee9d82a727e6bc04715c341
ms.translationtype: HT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/21/2018
ms.locfileid: "29389831"
---
# <a name="get-started-receiving-messages-with-the-event-processor-host-in-net-standard"></a>Začínáme s příjmem zpráv pomocí třídy Event Processor Host v .NET Standard

> [!NOTE]
> Tato ukázka je k dispozici na [GitHubu](https://github.com/Azure/azure-event-hubs/tree/master/samples/DotNet/Microsoft.Azure.EventHubs/SampleEphReceiver).

Tento kurz ukazuje, jak napsat konzolovou aplikaci .NET Core, která přijímá zprávy z centra událostí pomocí knihovny **Event Processor Host**. Řešení z [GitHubu](https://github.com/Azure/azure-event-hubs/tree/master/samples/DotNet/Microsoft.Azure.EventHubs/SampleEphReceiver) můžete spustit tak, jak je, stačí nahradit řetězce hodnotami vašeho centra událostí a účtu úložiště. Nebo můžete vytvořit vlastní řešení podle kroků v tomto kurzu.

## <a name="prerequisites"></a>Požadavky

* [Sada Microsoft Visual Studio 2015 nebo 2017](http://www.visualstudio.com). V příkladech v tomto kurzu se používá sada Visual Studio 2017, ale podporuje se i sada Visual Studio 2015.
* [Nástroje .NET Core pro sadu Visual Studio 2015 nebo 2017](http://www.microsoft.com/net/core).
* Předplatné Azure.
* Obor názvů služby Azure Event Hubs.
* Účet služby Azure Storage.

## <a name="create-an-event-hubs-namespace-and-an-event-hub"></a>Vytvoření oboru názvů Event Hubs a centra událostí  

Prvním krokem je použití webu [Azure Portal](https://portal.azure.com) k vytvoření oboru názvů pro příslušný typ služby Event Hubs a získání přihlašovacích údajů pro správu, které vaše aplikace potřebuje ke komunikaci s centrem událostí. Pokud chcete vytvořit obor názvů a centrum událostí, postupujte podle pokynů v [tomto článku](event-hubs-create.md) a pak pokračujte v tomto kurzu.  

## <a name="create-an-azure-storage-account"></a>Vytvoření účtu služby Azure Storage  

1. Přihlaste se k webu [Azure Portal](https://portal.azure.com).  
2. V levém navigačním podokně portálu klikněte na **Vytvořit prostředek**, pak klikněte na **Úložiště** a pak na **Účet úložiště**.  
3. Vyplňte pole v okně účtu úložiště a pak klikněte na **Vytvořit**.

    ![Vytvoření účtu úložiště][1]

4. Po zobrazení zprávy **Nasazení proběhla úspěšně** klikněte na název nového účtu úložiště. V okně **Základy** klikněte na **Objekty blob**. Po otevření dialogového okna **Služba Blob** klikněte v horní části na **+ Kontejner**. Zadejte název kontejneru a pak okno **Služba Blob** zavřete.  
5. V levém okně klikněte na **Přístupové klíče** a zkopírujte název kontejneru úložiště, název účtu úložiště a hodnotu položky **klíč1**. Uložte tyto hodnoty do Poznámkového bloku nebo jiného dočasného umístění.  

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

## <a name="write-a-main-console-method-that-uses-the-simpleeventprocessor-class-to-receive-messages"></a>Napsání hlavní metody konzoly, která pomocí třídy SimpleEventProcessor přijímá zprávy

1. Do horní části souboru Program.cs přidejte následující příkazy `using`.

    ```csharp
    using Microsoft.Azure.EventHubs;
    using Microsoft.Azure.EventHubs.Processor;
    using System.Threading.Tasks;
    ```

2. Do třídy `Program` přidejte konstanty pro připojovací řetězec centra událostí, název centra událostí, název kontejneru účtu úložiště, název účtu úložiště a klíč účtu úložiště. Přidejte následující kód a zástupné texty nahraďte odpovídajícími hodnotami.

    ```csharp
    private const string EhConnectionString = "{Event Hubs connection string}";
    private const string EhEntityPath = "{Event Hub path/name}";
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
            EhEntityPath,
            PartitionReceiver.DefaultConsumerGroupName,
            EhConnectionString,
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
            private const string EhConnectionString = "{Event Hubs connection string}";
            private const string EhEntityPath = "{Event Hub path/name}";
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
                    EhEntityPath,
                    PartitionReceiver.DefaultConsumerGroupName,
                    EhConnectionString,
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

## <a name="next-steps"></a>Další kroky
Další informace o službě Event Hubs najdete na následujících odkazech:

* [Přehled služby Event Hubs](event-hubs-what-is-event-hubs.md)
* [Vytvoření centra událostí](event-hubs-create.md)
* [Nejčastější dotazy k Event Hubs](event-hubs-faq.md)

[1]: ./media/event-hubs-dotnet-standard-getstarted-receive-eph/event-hubs-python1.png
[2]: ./media/event-hubs-dotnet-standard-getstarted-receive-eph/netcore.png
