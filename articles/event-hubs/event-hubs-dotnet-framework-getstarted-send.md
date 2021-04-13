---
title: Azure Event Hubs – události odesílání a příjmu pomocí .NET Framework
description: 'Rychlý Start: Tento článek poskytuje návod pro vytvoření .NET Framework aplikace, která odesílá události do Azure Event Hubs.'
ms.topic: quickstart
ms.date: 06/23/2020
ms.custom: devx-track-csharp
ms.openlocfilehash: 3c2ce58f86fa27544641c0917e96fa3172017232
ms.sourcegitcommit: b4fbb7a6a0aa93656e8dd29979786069eca567dc
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/13/2021
ms.locfileid: "107309352"
---
# <a name="quickstart-send-events-to-or-receive-events-from-azure-event-hubs-using-net-framework"></a>Rychlý Start: odeslání událostí do nebo příjem událostí z Azure Event Hubs pomocí .NET Framework
Azure Event Hubs je platforma pro streamování velkých objemů dat a služba pro ingestování událostí, která je schopná přijmout a zpracovat miliony událostí za sekundu. Služba Event Hubs dokáže zpracovávat a ukládat události, data nebo telemetrické údaje produkované distribuovaným softwarem a zařízeními. Data odeslaná do centra událostí je možné transformovat a uložit pomocí libovolného poskytovatele analýz v reálném čase nebo adaptérů pro dávkové zpracování a ukládání. Podrobnější přehled služby Event Hubs najdete v tématech [Přehled služby Event Hubs](event-hubs-about.md) a [Funkce služby Event Hubs](event-hubs-features.md).

V tomto kurzu se dozvíte, jak vytvořit .NET Framework konzolové aplikace v jazyce C# pro odesílání událostí nebo přijímání událostí z centra eventhub. 

## <a name="prerequisites"></a>Požadavky
Pro absolvování tohoto kurzu musí být splněné následující požadavky:

- [Microsoft Visual Studio 2019](https://visualstudio.com).
- **Vytvoří obor názvů Event Hubs a centrum událostí**. Prvním krokem je použití [Azure Portal](https://portal.azure.com) k vytvoření oboru názvů typu Event Hubs a získání přihlašovacích údajů pro správu, které vaše aplikace potřebuje ke komunikaci s centrem událostí. Pokud chcete vytvořit obor názvů a centrum událostí, postupujte podle pokynů v [tomto článku](event-hubs-create.md). Pak Získejte **připojovací řetězec pro obor názvů centra událostí** podle pokynů uvedených v článku [získání připojovacího řetězce](event-hubs-get-connection-string.md#get-connection-string-from-the-portal). Připojovací řetězec použijete později v tomto kurzu.

## <a name="send-events"></a>Odesílání událostí 
V této části se dozvíte, jak vytvořit konzolovou aplikaci .NET Framework pro odesílání událostí do centra událostí. 

### <a name="create-a-console-application"></a>Vytvoření konzolové aplikace

V aplikaci Visual Studio vytvořte nový projekt desktopové aplikace Visual C# pomocí šablony projektu **Konzolová aplikace** . Projekt pojmenujte **Odesílatel**.
   
![Snímek obrazovky, který zobrazuje okno "nový projekt" s vybranou možností "Konzolová aplikace (.NET Framework)" a je zvýrazněno tlačítko pro název projektu "odesílatel" a "OK".](./media/event-hubs-dotnet-framework-getstarted-send/create-sender-csharp1.png)

### <a name="add-the-event-hubs-nuget-package"></a>Přidání balíčku NuGet služby Event Hubs

1. V Průzkumníku řešení klikněte pravým tlačítkem na projekt **Sender** a potom klikněte na **Spravovat balíčky NuGet pro řešení**. 
2. Klikněte na kartu **Procházet** a potom najděte `WindowsAzure.ServiceBus`. Klikněte na **Instalovat** a přijměte podmínky použití. 
   
    ![Nainstalovat balíček NuGet Service Bus](./media/event-hubs-dotnet-framework-getstarted-send/create-sender-csharp2.png)
   
    Visual Studio stáhne a nainstaluje [balíček NuGet knihovny Azure Service Bus](https://www.nuget.org/packages/WindowsAzure.ServiceBus) a přidá se na něj odkaz.

### <a name="write-code-to-send-messages-to-the-event-hub"></a>Napsání kódu pro odesílání zpráv do centra událostí

1. Do horní části souboru **Program.cs** přidejte následující příkazy `using`:
   
    ```csharp
    using System.Threading;
    using Microsoft.ServiceBus.Messaging;
    ```
2. K třídě **Program** přidejte následující pole a zástupné hodnoty nahraďte názvem centra událostí, které jste vytvořili v předchozí části, a připojovacím řetězcem na úrovni oboru názvů, který jste si předtím uložili. Připojovací řetězec pro centrum událostí můžete zkopírovat z **připojovacího řetězce – primární** klíč v části **RootManageSharedAccessKey** na stránce centra událostí v Azure Portal. Podrobný postup najdete v tématu [získání připojovacího řetězce](event-hubs-get-connection-string.md#get-connection-string-from-the-portal).
   
    ```csharp
    static string eventHubName = "Your Event Hub name";
    static string connectionString = "namespace connection string";
    ```
3. Do třídy **program** přidejte následující metodu:
   
      ```csharp
      static void SendingRandomMessages()
      {
          var eventHubClient = EventHubClient.CreateFromConnectionString(connectionString, eventHubName);
          while (true)
          {
              try
              {
                  var message = Guid.NewGuid().ToString();
                  Console.WriteLine("{0} > Sending message: {1}", DateTime.Now, message);
                  eventHubClient.Send(new EventData(Encoding.UTF8.GetBytes(message)));
              }
              catch (Exception exception)
              {
                  Console.ForegroundColor = ConsoleColor.Red;
                  Console.WriteLine("{0} > Exception: {1}", DateTime.Now, exception.Message);
                  Console.ResetColor();
              }
       
              Thread.Sleep(200);
          }
      }
      ```
   
      Tato metoda neustále odesílá události do vašeho centra událostí se zpožděním 200 ms.
4. Nakonec přidejte do metody **Main** následující řádky:
   
      ```csharp
      Console.WriteLine("Press Ctrl-C to stop the sender process");
      Console.WriteLine("Press Enter to start now");
      Console.ReadLine();
      SendingRandomMessages();
      ```
5. Spusťte program a zkontrolujte, že nejsou žádné chyby.
  
## <a name="receive-events"></a>Příjem událostí
V této části napíšete .NET Framework konzolovou aplikaci, která přijímá zprávy z centra událostí pomocí nástroje [Event Processor Host](event-hubs-event-processor-host.md). [Procesor událostí Host](event-hubs-event-processor-host.md) je třída rozhraní .NET, která zjednodušuje přijímání událostí z Center událostí tím, že spravuje trvalé kontrolní body a paralelní příjem z těchto Center událostí. Pomocí třídy Event Processor Host můžete události rozdělit mezi několik příjemců, i když jsou hostovaní v různých uzlech. 

[!INCLUDE [event-hubs-create-storage](../../includes/event-hubs-create-storage.md)]

### <a name="create-a-console-application"></a>Vytvoření konzolové aplikace

Pomocí šablony projektu **Konzolová aplikace** vytvořte v sadě Visual Studio nový projekt desktopové aplikace Visual C#. Projekt nazvěte **Receiver** (Příjemce).
   
![Vytvoření konzolové aplikace](./media/event-hubs-dotnet-framework-getstarted-receive-eph/create-receiver-csharp1.png)

### <a name="add-the-event-hubs-nuget-package"></a>Přidání balíčku NuGet služby Event Hubs

1. V Průzkumníku řešení klikněte pravým tlačítkem na projekt **Receiver** a potom klikněte na **Spravovat balíčky NuGet pro řešení**.
2. Klikněte na kartu **Procházet** a potom najděte `Microsoft Azure Service Bus Event Hub - EventProcessorHost`. Klikněte na **Instalovat** a přijměte podmínky použití.
   
    ![Vyhledat balíček NuGet pro procesor událostí](./media/event-hubs-dotnet-framework-getstarted-receive-eph/create-eph-csharp1.png)
   
    Visual Studio stáhne, nainstaluje a přidá odkaz na [balíček NuGet třídy EventProcessorHost služby Event Hub ve službě Azure Service Bus](https://www.nuget.org/packages/Microsoft.Azure.ServiceBus.EventProcessorHost) se všemi jeho závislostmi.

### <a name="implement-the-ieventprocessor-interface"></a>Implementace rozhraní IEventProcessor

1. Klikněte pravým tlačítkem na projekt **Receiver**, **Přidat** a potom na **Třída**. Pojmenujte novou třídu **SimpleEventProcessor** a potom kliknutím na **Přidat** třídu vytvořte.
   
    ![Přidat třídu SimpleEventProcessor](./media/event-hubs-dotnet-framework-getstarted-receive-eph/create-receiver-csharp2.png)
2. Na začátek souboru SimpleEventProcessor.cs přidejte následující příkazy:
    
      ```csharp
      using Microsoft.ServiceBus.Messaging;
      using System.Diagnostics;
      ```
    
3. Pro tělo třídy nahraďte následující kód:
    
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

### <a name="update-the-main-method-to-use-simpleeventprocessor"></a>Aktualizace metody Main pro použití třídy SimpleEventProcessor

1. Ve třídě **Program** přidejte na začátek souboru následující příkaz `using`:
    
      ```csharp
      using Microsoft.ServiceBus.Messaging;
      ```
    
2. Nahraďte `Main` metodu ve `Program` třídě následujícím kódem, kde nahradíte název centra událostí a připojovací řetězec na úrovni oboru názvů, který jste předtím uložili, a účet úložiště a klíč, který jste zkopírovali v předchozích částech. 
    
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
  
## <a name="next-steps"></a>Další kroky
Přečtěte si následující články: 

- [EventProcessorHost](event-hubs-event-processor-host.md)
- [Funkce a terminologie v Azure Event Hubs](event-hubs-features.md).
- [Nejčastější dotazy k Event Hubs](event-hubs-faq.yml)


<!-- Links -->
[EventProcessorHost]: /dotnet/api/microsoft.servicebus.messaging.eventprocessorhost
[Event Hubs overview]: event-hubs-about.md
[Event Hubs Programming Guide]: event-hubs-programming-guide.md
[Azure Storage account]:../storage/common/storage-create-storage-account.md
[Event Processor Host]: event-hubs-event-processor-host.md
[Azure portal]: https://portal.azure.com
