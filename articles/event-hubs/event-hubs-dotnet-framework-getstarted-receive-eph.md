---
title: Příjem událostí z Azure Event Hubs pomocí rozhraní .NET Framework | Dokumentace Microsoftu
description: V tomto kurzu se naučíte přijímat události z Azure Event Hubs pomocí rozhraní .NET Framework.
services: event-hubs
documentationcenter: ''
author: ShubhaVijayasarathy
manager: timlt
editor: ''
ms.assetid: c4974bd3-2a79-48a1-aa3b-8ee2d6655b28
ms.service: event-hubs
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.custom: seodec18
ms.date: 12/06/2018
ms.author: shvija
ms.openlocfilehash: c7b211202013050c826ef8a530a831b8f02673f6
ms.sourcegitcommit: 698ba3e88adc357b8bd6178a7b2b1121cb8da797
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/07/2018
ms.locfileid: "53015088"
---
# <a name="receive-events-from-azure-event-hubs-using-the-net-framework"></a>Příjem událostí z Azure Event Hubs pomocí rozhraní .NET Framework

## <a name="introduction"></a>Úvod

Event Hubs je služba, která zpracovává velké objemy dat událostí (telemetrie) z připojených zařízení a aplikací. Data, která shromáždíte pomocí služby Event Hubs, můžete uložit pomocí úložného clusteru nebo transformovat pomocí zprostředkovatele datové analýzy v reálném čase. Schopnost shromažďovat a zpracovávat velké množství událostí je klíčovou komponentou moderních aplikačních architektur, například internetu věcí (Internet of Things – IoT). Podrobnější přehled služby Event Hubs najdete v tématech [Přehled služby Event Hubs](event-hubs-about.md) a [Funkce služby Event Hubs](event-hubs-features.md).

Tento kurz ukazuje, jak psát aplikace konzoly rozhraní .NET Framework, která přijímá zprávy z centra událostí pomocí [Event Processor Host](event-hubs-event-processor-host.md). [Event Processor Host](event-hubs-event-processor-host.md) je třída rozhraní .NET, která zjednodušuje přijímání událostí z center událostí tím, že spravuje trvalé kontrolní body a paralelní příjmy z těchto center událostí. Pomocí třídy Event Processor Host můžete události rozdělit mezi několik příjemců, i když jsou hostovaní v různých uzlech. Tento příklad ukazuje způsob použití třídy Event Processor Host pro jednoho příjemce. [Horizontální navýšení kapacity zpracování událostí] [ Scale out Event Processing with Event Hubs] příklad ukazuje, jak pomocí třídy Event Processor Host v případě několika příjemců.

## <a name="prerequisites"></a>Požadavky

Pro absolvování tohoto kurzu musí být splněné následující požadavky:

* [Microsoft Visual Studio 2017 nebo vyšší](https://visualstudio.com).

## <a name="create-an-event-hubs-namespace-and-an-event-hub"></a>Vytvoření oboru názvů Event Hubs a centra událostí
Prvním krokem je použití webu [Azure Portal](https://portal.azure.com) k vytvoření oboru názvů typu Event Hubs a získání přihlašovacích údajů pro správu, které vaše aplikace potřebuje ke komunikaci s centrem událostí. Pokud chcete vytvořit obor názvů a centrum událostí, postupujte podle pokynů v [tomto článku](event-hubs-create.md) a pak pokračujte podle následujících pokynů v tomto kurzu.

[!INCLUDE [event-hubs-create-storage](../../includes/event-hubs-create-storage.md)]

## <a name="create-a-console-application"></a>Vytvoření konzolové aplikace

Pomocí šablony projektu **Konzolová aplikace** vytvořte v sadě Visual Studio nový projekt desktopové aplikace Visual C#. Projekt nazvěte **Receiver** (Příjemce).
   
![Vytvoření konzolové aplikace](./media/event-hubs-dotnet-framework-getstarted-receive-eph/create-receiver-csharp1.png)

## <a name="add-the-event-hubs-nuget-package"></a>Přidání balíčku NuGet služby Event Hubs

1. V Průzkumníku řešení klikněte pravým tlačítkem na projekt **Receiver** a potom klikněte na **Spravovat balíčky NuGet pro řešení**.
2. Klikněte na kartu **Procházet** a potom najděte `Microsoft Azure Service Bus Event Hub - EventProcessorHost`. Klikněte na **Instalovat** a přijměte podmínky použití.
   
    ![Vyhledejte balíček NuGet hostitel procesoru událostí](./media/event-hubs-dotnet-framework-getstarted-receive-eph/create-eph-csharp1.png)
   
    Visual Studio stáhne, nainstaluje a přidá odkaz na [balíček NuGet třídy EventProcessorHost služby Event Hub ve službě Azure Service Bus](https://www.nuget.org/packages/Microsoft.Azure.ServiceBus.EventProcessorHost) se všemi jeho závislostmi.

## <a name="implement-the-ieventprocessor-interface"></a>Implementace rozhraní IEventProcessor

1. Klikněte pravým tlačítkem na projekt **Receiver**, **Přidat** a potom na **Třída**. Pojmenujte novou třídu **SimpleEventProcessor** a potom kliknutím na **Přidat** třídu vytvořte.
   
    ![Přidání třídy simpleeventprocessor přijímá](./media/event-hubs-dotnet-framework-getstarted-receive-eph/create-receiver-csharp2.png)
2. Na začátek souboru SimpleEventProcessor.cs přidejte následující příkazy:
    
      ```csharp
      using Microsoft.ServiceBus.Messaging;
      using System.Diagnostics;
      ```
    
3. Nahraďte následující kód pro tělo třídy:
    
      ```csharp
      class SimpleEventProcessor : IEventProcessor
      {
        Stopwatch checkpointStopWatch;
        
        async Task IEventProcessor.CloseAsync(PartitionContext context, CloseReason reason)
        {
            Console.WriteLine("Processor Shutting Down. Partition '{0}', Reason: '{1}'.", context.Lease.PartitionId, reason);
            if (reason == CloseReason.Shutdown)
            {
                await context.CheckpointAsync();
            }
        }
        
        Task IEventProcessor.OpenAsync(PartitionContext context)
        {
            Console.WriteLine("SimpleEventProcessor initialized.  Partition: '{0}', Offset: '{1}'", context.Lease.PartitionId, context.Lease.Offset);
            this.checkpointStopWatch = new Stopwatch();
            this.checkpointStopWatch.Start();
            return Task.FromResult<object>(null);
        }
        
        async Task IEventProcessor.ProcessEventsAsync(PartitionContext context, IEnumerable<EventData> messages)
        {
            foreach (EventData eventData in messages)
            {
                string data = Encoding.UTF8.GetString(eventData.GetBytes());
        
                Console.WriteLine(string.Format("Message received.  Partition: '{0}', Data: '{1}'",
                    context.Lease.PartitionId, data));
            }
        
            //Call checkpoint every 5 minutes, so that worker can resume processing from 5 minutes back if it restarts.
            if (this.checkpointStopWatch.Elapsed > TimeSpan.FromMinutes(5))
            {
                await context.CheckpointAsync();
                this.checkpointStopWatch.Restart();
            }
        }
      }
      ```
    
      Tuto třídu volá třída **EventProcessorHost** kvůli zpracování událostí přijatých z centra událostí. Třída `SimpleEventProcessor` používá stopky, aby pravidelně volala metodu kontrolního bodu v kontextu třídy **EventProcessorHost**. Tímto způsobem je zajištěno, že příjemce v případě restartování neztratí víc než pět minut práce potřebné ke zpracování.

## <a name="update-the-main-method-to-use-simpleeventprocessor"></a>Aktualizace metody Main pro použití třídy SimpleEventProcessor

1. Ve třídě **Program** přidejte na začátek souboru následující příkaz `using`:
    
      ```csharp
      using Microsoft.ServiceBus.Messaging;
      ```
    
2. Nahradit `Main` metodu `Program` třídy s následujícím kódem, kde nahradíte název centra událostí a úrovni oboru názvů připojovací řetězec, který jste si dříve uložili a účet úložiště a klíč, který jste zkopírovali v předchozí části. 
    
      ```csharp
      static void Main(string[] args)
      {
        string eventHubConnectionString = "{Event Hubs namespace connection string}";
        string eventHubName = "{Event Hub name}";
        string storageAccountName = "{storage account name}";
        string storageAccountKey = "{storage account key}";
        string storageConnectionString = string.Format("DefaultEndpointsProtocol=https;AccountName={0};AccountKey={1}", storageAccountName, storageAccountKey);
        
        string eventProcessorHostName = Guid.NewGuid().ToString();
        EventProcessorHost eventProcessorHost = new EventProcessorHost(eventProcessorHostName, eventHubName, EventHubConsumerGroup.DefaultGroupName, eventHubConnectionString, storageConnectionString);
        Console.WriteLine("Registering EventProcessor...");
        var options = new EventProcessorOptions();
        options.ExceptionReceived += (sender, e) => { Console.WriteLine(e.Exception); };
        eventProcessorHost.RegisterEventProcessorAsync<SimpleEventProcessor>(options).Wait();
        
        Console.WriteLine("Receiving. Press enter key to stop worker.");
        Console.ReadLine();
        eventProcessorHost.UnregisterEventProcessorAsync().Wait();
      }
      ```
    
3. Spusťte program a zkontrolujte, že nejsou žádné chyby.
  
Blahopřejeme! Obdrželi jste nyní zprávy z centra událostí pomocí třídy Event Processor Host.


> [!NOTE]
> Tento kurz používá jednu instanci třídy [EventProcessorHost](event-hubs-event-processor-host.md). Pokud chcete zvýšit propustnost, doporučujeme spustit několik instancí třídy [EventProcessorHost](event-hubs-event-processor-host.md), jak je znázorněno v ukázce [škálovaného zpracování událostí](https://code.msdn.microsoft.com/Service-Bus-Event-Hub-45f43fc3). V těchto případech se spolu navzájem automaticky koordinuje několik instancí, aby dokázaly vyrovnávat zatížení přijatých událostí. 

## <a name="next-steps"></a>Další postup
V tomto rychlém startu jste vytvořili aplikaci rozhraní .NET Framework, která se zobrazila zprávy z centra událostí. Zjistěte, jak odesílat události do centra událostí pomocí rozhraní .NET Framework, naleznete v tématu [odesílání událostí z centra událostí - rozhraní .NET Framework](event-hubs-dotnet-framework-getstarted-send.md).

<!-- Images. -->
[19]: ./media/event-hubs-csharp-ephcs-getstarted/create-eh-proj1.png
[20]: ./media/event-hubs-csharp-ephcs-getstarted/create-eh-proj2.png
[21]: ./media/event-hubs-csharp-ephcs-getstarted/run-csharp-ephcs1.png
[22]: ./media/event-hubs-csharp-ephcs-getstarted/run-csharp-ephcs2.png

<!-- Links -->
[EventProcessorHost]: /dotnet/api/microsoft.servicebus.messaging.eventprocessorhost
[Event Hubs overview]: event-hubs-about.md
[Scale out Event Processing with Event Hubs]: https://code.msdn.microsoft.com/Service-Bus-Event-Hub-45f43fc3
[Event Hubs Programming Guide]: event-hubs-programming-guide.md
[Azure Storage account]:../storage/common/storage-create-storage-account.md
[Event Processor Host]: event-hubs-event-processor-host.md
[Azure portal]: https://portal.azure.com
