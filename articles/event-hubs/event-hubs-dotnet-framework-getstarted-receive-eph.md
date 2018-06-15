---
title: Příjem událostí z Azure Event Hubs pomocí rozhraní .NET Framework | Dokumentace Microsoftu
description: V tomto kurzu se naučíte přijímat události z Azure Event Hubs pomocí rozhraní .NET Framework.
services: event-hubs
documentationcenter: ''
author: sethmanheim
manager: timlt
editor: ''
ms.assetid: c4974bd3-2a79-48a1-aa3b-8ee2d6655b28
ms.service: event-hubs
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/01/2018
ms.author: sethm
ms.openlocfilehash: 8fd70380dbb88f379789e1a4730934dcd38cac5a
ms.sourcegitcommit: d87b039e13a5f8df1ee9d82a727e6bc04715c341
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/21/2018
ms.locfileid: "29393214"
---
# <a name="receive-events-from-azure-event-hubs-using-the-net-framework"></a>Příjem událostí z Azure Event Hubs pomocí rozhraní .NET Framework

## <a name="introduction"></a>Úvod

Event Hubs je služba, která zpracovává velké objemy dat událostí (telemetrie) z připojených zařízení a aplikací. Data, která shromáždíte pomocí služby Event Hubs, můžete uložit pomocí úložného clusteru nebo transformovat pomocí zprostředkovatele datové analýzy v reálném čase. Schopnost shromažďovat a zpracovávat velké množství událostí je klíčovou komponentou moderních aplikačních architektur, například internetu věcí (Internet of Things – IoT).

Tento kurz ukazuje, jak psát aplikace konzoly rozhraní .NET Framework, která přijímá zprávy z centra událostí pomocí třídy **[Event Processor Host][EventProcessorHost]**. Pokud chcete odesílat události pomocí rozhraní .NET Framework, přečtěte si článek [Odesílání událostí do Azure Event Hubs pomocí rozhraní .NET Framework](event-hubs-dotnet-framework-getstarted-send.md) nebo klikněte na příslušný odesílající jazyk v obsahu vlevo.

[Event Processor Host][EventProcessorHost] je třída rozhraní .NET, která zjednodušuje přijímání událostí z center událostí tím, že spravuje trvalé kontrolní body a paralelní příjmy z těchto center událostí. Pomocí třídy [Event Processor Host][Event Processor Host] můžete události rozdělit mezi několik příjemců, i když jsou hostované v různých uzlech. Tento příklad ukazuje způsob použití třídy [Event Processor Host][EventProcessorHost] pro jednoho příjemce. Ukázka metody [Horizontální navýšení kapacity zpracování událostí][Scale out Event Processing with Event Hubs] znázorňuje způsob použití třídy [Event Processor Host][EventProcessorHost] v případě několika příjemců.

## <a name="prerequisites"></a>Požadavky

Pro absolvování tohoto kurzu musí být splněné následující požadavky:

* [Microsoft Visual Studio 2015 nebo vyšší](http://visualstudio.com). Pro snímky obrazovky v tomto kurzu se používá Visual Studio 2017.
* Aktivní účet Azure. Pokud účet nemáte, můžete si ho bezplatně vytvořit během několika minut. Podrobnosti najdete v článku [Bezplatná zkušební verze Azure](https://azure.microsoft.com/free/).

## <a name="create-an-event-hubs-namespace-and-an-event-hub"></a>Vytvoření oboru názvů Event Hubs a centra událostí

Prvním krokem je použití webu [Azure Portal](https://portal.azure.com) k vytvoření oboru názvů typu Event Hubs a získání přihlašovacích údajů pro správu, které vaše aplikace potřebuje ke komunikaci s centrem událostí. Pokud chcete vytvořit obor názvů a centrum událostí, postupujte podle pokynů v [tomto článku](event-hubs-create.md) a pak pokračujte podle následujících pokynů v tomto kurzu.

## <a name="create-an-azure-storage-account"></a>Vytvoření účtu služby Azure Storage

Pokud chcete používat třídu [Event Processor Host][EventProcessorHost], musíte mít [Účet služby Azure Storage][Azure Storage account]:

1. Přihlaste se k [portál Azure][Azure portal]a klikněte na tlačítko **vytvořit prostředek** v levém horním rohu obrazovky.
2. Klikněte na **Storage** a poté klikněte na **Účet úložiště**.
   
    ![](./media/event-hubs-dotnet-framework-getstarted-receive-eph/create-storage1.png)
3. V **vytvořit účet úložiště** podokně, zadejte název pro účet úložiště. Zvolte předplatné Azure, skupinu prostředků a umístění, ve kterém se má prostředek vytvořit. Poté klikněte na **Vytvořit**.
   
    ![](./media/event-hubs-dotnet-framework-getstarted-receive-eph/create-storage2.png)
4. V seznamu účtů úložiště klikněte na nově vytvořený účet úložiště.
5. V podokně účet úložiště, klikněte na tlačítko **přístupové klíče**. Zkopírujte hodnotu **key1** pro pozdější použití v tomto kurzu.
   
    ![](./media/event-hubs-dotnet-framework-getstarted-receive-eph/create-storage3.png)

## <a name="create-a-receiver-console-application"></a>Vytvoření konzolové aplikace Příjemce

1. Pomocí šablony projektu **Konzolová aplikace** vytvořte v sadě Visual Studio nový projekt desktopové aplikace Visual C#. Projekt nazvěte **Receiver** (Příjemce).
   
    ![](./media/event-hubs-dotnet-framework-getstarted-receive-eph/create-receiver-csharp1.png)
2. V Průzkumníku řešení klikněte pravým tlačítkem na projekt **Receiver** a potom klikněte na **Spravovat balíčky NuGet pro řešení**.
3. Klikněte na kartu **Procházet** a potom najděte `Microsoft Azure Service Bus Event Hub - EventProcessorHost`. Klikněte na **Instalovat** a přijměte podmínky použití.
   
    ![](./media/event-hubs-dotnet-framework-getstarted-receive-eph/create-eph-csharp1.png)
   
    Visual Studio stáhne, nainstaluje a přidá odkaz na [balíček NuGet třídy EventProcessorHost služby Event Hub ve službě Azure Service Bus](https://www.nuget.org/packages/Microsoft.Azure.ServiceBus.EventProcessorHost) se všemi jeho závislostmi.
4. Klikněte pravým tlačítkem na projekt **Receiver**, **Přidat** a potom na **Třída**. Pojmenujte novou třídu **SimpleEventProcessor** a potom kliknutím na **Přidat** třídu vytvořte.
   
    ![](./media/event-hubs-dotnet-framework-getstarted-receive-eph/create-receiver-csharp2.png)
5. Na začátek souboru SimpleEventProcessor.cs přidejte následující příkazy:
    
  ```csharp
  using Microsoft.ServiceBus.Messaging;
  using System.Diagnostics;
  ```
    
  Potom nahraďte tělo třídy následujícím kódem:
    
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
6. Ve třídě **Program** přidejte na začátek souboru následující příkaz `using`:
    
  ```csharp
  using Microsoft.ServiceBus.Messaging;
  ```
    
  Potom nahraďte metodu `Main` ve třídě `Program` následujícím kódem, kde nahradíte název centra událostí a připojovací řetězec na úrovni oboru názvů, který jste si dříve uložili, a účet úložiště spolu s klíčem, který jste si v předchozích částech zkopírovali. 
    
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

7. Spusťte program a zkontrolujte, že nejsou žádné chyby.
  
Blahopřejeme! Obdrželi jste nyní zprávy z centra událostí pomocí třídy Event Processor Host.


> [!NOTE]
> Tento kurz používá jednu instanci třídy [EventProcessorHost][EventProcessorHost]. Pokud chcete zvýšit propustnost, spusťte několik instancí třídy [EventProcessorHost][EventProcessorHost], jak je znázorněno v ukázce metody [Horizontální navýšení kapacity zpracování událostí][Horizontální navýšení kapacity zpracování událostí]. V těchto případech se spolu různé instance navzájem automaticky koordinují, aby dokázaly vyrovnávat zatížení přijatých událostí. Pokud chcete, aby každý z několika příjemců zpracovával *všechny* události, musíte použít koncept **ConsumerGroup**. Když přijímáte události z různých počítačů, může být užitečné nazvat instance třídy [EventProcessorHost][EventProcessorHost] podle počítačů (nebo rolí), ve kterých jsou nasazené. Další informace o těchto tématech najdete v tématech [Přehled služby Event Hubs][Event Hubs overview] a [Průvodce programováním pro službu Event Hubs][Event Hubs Programming Guide].
> 
> 

## <a name="next-steps"></a>Další postup

Sestavili jste funkční aplikaci, která vytvoří centrum událostí a odesílá i přijímá data. Nyní se můžete dozvědět víc návštěvou následujících odkazů:

* [Event Processor Host][Event Processor Host]
* [Přehled služby Event Hubs][Event Hubs overview]
* [Nejčastější dotazy k Event Hubs](event-hubs-faq.md)

<!-- Images. -->
[19]: ./media/event-hubs-csharp-ephcs-getstarted/create-eh-proj1.png
[20]: ./media/event-hubs-csharp-ephcs-getstarted/create-eh-proj2.png
[21]: ./media/event-hubs-csharp-ephcs-getstarted/run-csharp-ephcs1.png
[22]: ./media/event-hubs-csharp-ephcs-getstarted/run-csharp-ephcs2.png

<!-- Links -->
[EventProcessorHost]: https://www.nuget.org/packages/Microsoft.Azure.ServiceBus.EventProcessorHost
[Event Hubs overview]: event-hubs-what-is-event-hubs.md
[Scale out Event Processing with Event Hubs]: https://code.msdn.microsoft.com/Service-Bus-Event-Hub-45f43fc3
[Event Hubs Programming Guide]: event-hubs-programming-guide.md
[Azure Storage account]:../storage/common/storage-create-storage-account.md
[Event Processor Host]: /dotnet/api/microsoft.servicebus.messaging.eventprocessorhost
[Azure portal]: https://portal.azure.com
