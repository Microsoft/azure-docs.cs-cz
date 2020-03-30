---
title: Průvodce programováním rozhraní .NET – Azure Event Hubs (starší verze) | Dokumenty společnosti Microsoft
description: Tento článek obsahuje informace o tom, jak psát kód pro Centra událostí Azure pomocí sady Azure .NET SDK.
services: event-hubs
documentationcenter: na
author: ShubhaVijayasarathy
ms.service: event-hubs
ms.custom: seodec18
ms.topic: article
ms.date: 01/15/2020
ms.author: shvija
ms.openlocfilehash: d958c2d32c16874676f46bb216067fe2d7bbe784
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "79280973"
---
# <a name="net-programming-guide-for-azure-event-hubs-legacy-microsoftazureeventhubs-package"></a>Průvodce programováním rozhraní .NET pro centra událostí Azure (starší balíček Microsoft.Azure.EventHubs)
Tento článek popisuje některé běžné scénáře při psaní kódu pomocí Azure Event Hubs. Předpokládá se předběžná znalost služby Event Hubs. Koncepční přehled služby Event Hubs naleznete v tématu [Přehled služby Event Hubs](event-hubs-what-is-event-hubs.md).

> [!WARNING]
> Tato příručka je pro starý balíček **Microsoft.Azure.EventHubs.** Doporučujeme [migrovat](https://github.com/Azure/azure-sdk-for-net/blob/master/sdk/eventhub/Azure.Messaging.EventHubs/MigrationGuide.md) kód, abyste mohli používat nejnovější balíček [Azure.Messaging.EventHubs.](get-started-dotnet-standard-send-v2.md)  


## <a name="event-publishers"></a>Zdroje událostí

Události odesíláte do centra událostí buď pomocí protokolu HTTP POST, nebo prostřednictvím připojení AMQP 1.0. Volba, které použít a kdy závisí na konkrétním scénáři, který je řešen. Připojení protokolu AMQP 1.0 se měří jako zprostředkovaná připojení ve službě Service Bus. Díky tomu, že poskytují trvalý kanál pro zasílání zpráv, jsou vhodnější ve scénářích, kde se počítá s častými vysokými objemy zpráv a vyžaduje se nižší latence.

Pokud používáte rozhraní API spravované pomocí .NET, budou primárními konstrukcemi pro publikování dat ve službě Event Hubs třídy [EventHubClient][] a [EventData][]. [EventHubClient][] poskytuje komunikační kanál AMQP, přes který jsou události odesílány do centra událostí. Třída [EventData][] představuje událost a slouží k publikování zpráv do centra událostí. Tato třída zahrnuje tělo, některá metadata(Vlastnosti) a informace záhlaví (Vlastnosti systému) o události. Další vlastnosti jsou přidány do objektu [EventData][] při průchodu centrem událostí.

## <a name="get-started"></a>Začínáme
Třídy .NET, které podporují centra událostí jsou k dispozici v balíčku [Microsoft.Azure.EventHubs](https://www.nuget.org/packages/Microsoft.Azure.EventHubs/) NuGet. Instalaci můžete nainstalovat pomocí průzkumníka řešení sady Visual Studio nebo [konzoly Správce balíčků](https://docs.nuget.org/docs/start-here/using-the-package-manager-console) v sadě Visual Studio. V tom případě je potřeba zadat v okně [konzoly Správce balíčků](https://docs.nuget.org/docs/start-here/using-the-package-manager-console) následující příkaz:

```shell
Install-Package Microsoft.Azure.EventHubs
```

## <a name="create-an-event-hub"></a>Vytvoření centra událostí

K vytvoření centra událostí můžete použít portál Azure, Azure PowerShell nebo Azure CLI. Podrobnosti najdete [v tématu Vytvoření oboru názvů Centra událostí a centra událostí pomocí portálu Azure](event-hubs-create.md).

## <a name="create-an-event-hubs-client"></a>Vytvoření klienta pro centra událostí (Event Hubs)

Primární třída pro interakci s event huby je [Microsoft.Azure.EventHubs.EventHubClient][EventHubClient]. Tuto třídu můžete vytvořit pomocí metody [CreateFromConnectionString,](/dotnet/api/microsoft.azure.eventhubs.eventhubclient.createfromconnectionstring) jak je znázorněno v následujícím příkladu:

```csharp
private const string EventHubConnectionString = "Event Hubs namespace connection string";
private const string EventHubName = "event hub name";

var connectionStringBuilder = new EventHubsConnectionStringBuilder(EventHubConnectionString)
{
    EntityPath = EventHubName

};
eventHubClient = EventHubClient.CreateFromConnectionString(connectionStringBuilder.ToString());
```

## <a name="send-events-to-an-event-hub"></a>Odeslání událostí do centra událostí

Události odesíláte do centra událostí vytvořením instance [EventHubClient][] a jejím asynchronním odesláním prostřednictvím metody [SendAsync.](/dotnet/api/microsoft.azure.eventhubs.eventhubclient.sendasync) Tato metoda přebírá jeden parametr instance [EventData][] a asynchronně ji odešle do centra událostí.

## <a name="event-serialization"></a>Serializace událostí

Třída [EventData][] má [dva přetížené konstruktory,](/dotnet/api/microsoft.azure.eventhubs.eventdata.-ctor) které berou různé parametry, bajty nebo bajtové pole, které představují datovou část dat události. Pokud při práci s třídou [EventData][] používáte JSON, můžete načíst pole bajtů řetězce kódovaného ve formátu JSON pomocí metody **Encoding.UTF8.GetBytes()**. Například:

```csharp
for (var i = 0; i < numMessagesToSend; i++)
{
    var message = $"Message {i}";
    Console.WriteLine($"Sending message: {message}");
    await eventHubClient.SendAsync(new EventData(Encoding.UTF8.GetBytes(message)));
}
```

## <a name="partition-key"></a>Klíč oddílu

> [!NOTE]
> Pokud nejste obeznámeni s oddíly, naleznete [v tomto článku](event-hubs-features.md#partitions). 

Při odesílání dat události můžete zadat hodnotu, která je zapisována k vytvoření přiřazení oddílu. Oddíl zadáte pomocí vlastnosti [PartitionSender.PartitionID.](/dotnet/api/microsoft.azure.eventhubs.partitionsender.partitionid) Rozhodnutí o použití oddílů však znamená volbu mezi dostupností a konzistencí. 

### <a name="availability-considerations"></a>Aspekty dostupnosti

Použití klíče oddílu je volitelné a měli byste pečlivě zvážit, zda jej použít. Pokud při publikování události nezadáte klíč oddílu, použije se přiřazení metodou kruhového dotazování. V mnoha případech pomocí klíče oddílu je dobrou volbou, pokud je důležité pořadí událostí. Při použití klíče oddílu tyto oddíly vyžadují dostupnost na jednom uzlu a výpadky může dojít v průběhu času; například při restartování a opravě výpočetních uzlů. Pokud tedy nastavíte ID oddílu a tento oddíl nebude z nějakého důvodu k dispozici, pokus o přístup k datům v tomto oddílu se nezdaří. Pokud je nejdůležitější vysoká dostupnost, nezadávejte klíč oddílu; v takovém případě jsou události odesílány do oddílů pomocí modelu kruhového dotazování popsaného výše. V tomto scénáři provádíte explicitní volbu mezi dostupností (bez ID oddílu) a konzistencí (připnutí událostí k ID oddílu).

Dalším aspektem je zpracování zpoždění při zpracování událostí. V některých případech může být lepší přetažení dat a opakování, než se pokusit držet krok se zpracováním, což může potenciálně způsobit další zpoždění následného zpracování. Například s burzovním panelem je lepší počkat na kompletní aktuální data, ale v živém chatu nebo scénáři VOIP byste raději měli data rychle, i když nejsou kompletní.

Vzhledem k těmto aspektům dostupnosti můžete v těchto scénářích zvolit jednu z následujících strategií zpracování chyb:

- Zastavit (zastavit čtení z centra událostí, dokud se věci neopraví)
- Drop (zprávy nejsou důležité, drop je)
- Opakování (opakování zpráv podle uznat za vhodné)

Další informace a diskuse o kompromisech mezi dostupností a konzistencí naleznete [v tématu Dostupnost a konzistence v centru událostí](event-hubs-availability-and-consistency.md). 

## <a name="batch-event-send-operations"></a>Dávkové operace odesílání událostí

Odesílání událostí v dávkách může pomoci zvýšit propustnost. Rozhraní [CreateBatch](/dotnet/api/microsoft.azure.eventhubs.eventhubclient.createbatch) API můžete použít k vytvoření dávky, do které lze později přidat datové objekty pro volání [SendAsync.](/dotnet/api/microsoft.azure.eventhubs.eventhubclient.sendasync)

Jedna dávka nesmí překročit limit 1 MB události. Kromě toho každá zpráva v batchi používá stejnou identitu zdroje. Dodržení maximálního limitu velikosti události u batche musí zajistit odesílatel. V případě překročení se u klienta vygeneruje chyba odeslání (**Send**). Pomocnou metodu [EventHubClient.CreateBatch](/dotnet/api/microsoft.azure.eventhubs.eventhubclient.createbatch) můžete použít k zajištění, že dávka nepřesáhne 1 MB. Získáte prázdné [EventDataBatch](/dotnet/api/microsoft.azure.eventhubs.eventdatabatch) z [CreateBatch](/dotnet/api/microsoft.azure.eventhubs.eventhubclient.createbatch) ROZHRANÍ API a potom použijte [TryAdd](/dotnet/api/microsoft.azure.eventhubs.eventdatabatch.tryadd) přidat události k vytvoření dávky. 

## <a name="send-asynchronously-and-send-at-scale"></a>Asynchronní odesílání a škálované odesílání

Události odesílat do centra událostí asynchronně. Odeslání asynchronně zvyšuje rychlost, jakou je klient schopen odesílat události. [SendAsync](/dotnet/api/microsoft.azure.eventhubs.eventhubclient.sendasync) vrátí [task](https://msdn.microsoft.com/library/system.threading.tasks.task.aspx) objekt. Třídu [RetryPolicy](/dotnet/api/microsoft.servicebus.retrypolicy) na straně klienta můžete použít k řízení možností opakování klienta.

## <a name="event-consumers"></a>Příjemci událostí
Třída [EventProcessorHost][] zpracovává data z center událostí (Event Hubs). Tuto implementaci byste měli používat při vytváření čtenářů událostí na platformě .NET. Třída [EventProcessorHost][] poskytuje pro implementace zpracovatelů událostí bezpečné prostředí runtime, které umožňuje bezpečné použití vláken a více procesů. Taky poskytuje možnost vytváření kontrolních bodů a správy „půjčování“ oddílu.

K použití třídy [EventProcessorHost][] může být potřeba implementovat rozhraní [IEventProcessor](/dotnet/api/microsoft.azure.eventhubs.processor.ieventprocessor). Toto rozhraní obsahuje čtyři metody:

* [OpenAsync](/dotnet/api/microsoft.azure.eventhubs.processor.ieventprocessor.openasync)
* [CloseAsync](/dotnet/api/microsoft.azure.eventhubs.processor.ieventprocessor.closeasync)
* [ProcessEventsAsync](/dotnet/api/microsoft.azure.eventhubs.processor.ieventprocessor.processeventsasync)
* [ProcessErrorAsync](/dotnet/api/microsoft.azure.eventhubs.processor.ieventprocessor.processerrorasync)

Chcete-li spustit zpracování událostí, vytvořte instanci [EventProcessorHost][]a zadejte příslušné parametry pro centrum událostí. Například:

> [!NOTE]
> EventProcessorHost a jeho související třídy jsou k dispozici v balíčku **Microsoft.Azure.EventHubs.Processor.** Přidejte balíček do projektu sady Visual Studio podle pokynů v [tomto článku](event-hubs-dotnet-framework-getstarted-send.md#add-the-event-hubs-nuget-package) nebo vydáním následujícího příkazu v okně [konzoly Správce balíčků:](https://docs.nuget.org/docs/start-here/using-the-package-manager-console) `Install-Package Microsoft.Azure.EventHubs.Processor`.

```csharp
var eventProcessorHost = new EventProcessorHost(
        EventHubName,
        PartitionReceiver.DefaultConsumerGroupName,
        EventHubConnectionString,
        StorageConnectionString,
        StorageContainerName);
```

Potom volání [RegisterEventProcessorAsync](/dotnet/api/microsoft.azure.eventhubs.processor.eventprocessorhost.registereventprocessorasync) zaregistrovat implementaci [IEventProcessor](/dotnet/api/microsoft.azure.eventhubs.processor.ieventprocessor) s runtime:

```csharp
await eventProcessorHost.RegisterEventProcessorAsync<SimpleEventProcessor>();
```

V tomto okamžiku se hostitel pokusí získat zapůjčení na každém oddílu v centru událostí pomocí algoritmu "hladový". Tyto zapůjčení trvat v daném časovém rámci a pak musí být obnovena. V tomto případě se instance pracovních procesů připojí jako nové uzly a umístí své rezervace zapůjčení. Každý uzel se snaží získat další zapůjčení, a tak se v průběhu času zatížení posune.

![Event Processor Host](./media/event-hubs-programming-guide/IC759863.png)

Postupem času se dosáhne rovnováhy. Tato dynamická funkce umožňuje použití automatického škálování na bázi procesoru u příjemců jak pro vertikální navýšení, tak pro vertikální snížení kapacity. Vzhledem k tomu, že centra událostí nemá přímý koncept počtu zpráv, průměrné využití procesoru je často nejlepší mechanismus pro měření back-end nebo spotřebitelské škálování. Když začnou zdroje publikovat více událostí, než mohou příjemci zpracovat, může být zvýšená aktivita procesoru využita k automatickému škálování počtu instancí pracovních procesů.

Třída [EventProcessorHost][] taky implementuje mechanismus vytváření kontrolních bodů Azure na bázi úložiště. Tento mechanismus ukládá posun na bázi oddílu, každý příjemce tedy může určit poslední kontrolní bod předchozího příjemce. Vzhledem k tomu, že oddíly prostřednictvím zapůjčení přechází mezi uzly, usnadňuje tento synchronizační mechanismus přesun zátěže.

## <a name="publisher-revocation"></a>Odvolání zdroje

Kromě pokročilých funkcí procesoru událostí za běhu umožňuje služba Event Hubs [odvolání vydavatele,](/rest/api/eventhub/revoke-publisher) aby zabránila konkrétním vydavatelům odesílat události do centra událostí. Tyto funkce jsou užitečné, pokud byl napaden token vydavatele nebo pokud je aktualizace softwaru způsobována nevhodným uskutečněním. V těchto situacích může být pro identitu zdroje, která je součástí jeho tokenu SAS, zablokováno publikování událostí.

> [!NOTE]
> V současné době tuto funkci podporuje pouze rozhraní REST API ([odvolání vydavatele).](/rest/api/eventhub/revoke-publisher)

Další informace o odvolání zdroje a o tom, jak jako zdroj odesílat do centra událostí, najdete v ukázce [zabezpečeného publikování ve velkém rozsahu u služby Event Hubs](https://code.msdn.microsoft.com/Service-Bus-Event-Hub-99ce67ab).

## <a name="next-steps"></a>Další kroky

Další informace o scénářích služby Event Hubs naleznete pod těmito odkazy:

* [Přehled rozhraní API služby Event Hubs](event-hubs-api-overview.md)
* [Co jsou centra událostí](event-hubs-what-is-event-hubs.md)
* [Dostupnost a konzistence ve službě Event Hubs](event-hubs-availability-and-consistency.md)
* [Referenční dokumentace rozhraní API třídy EventProcessorHost](/dotnet/api/microsoft.servicebus.messaging.eventprocessorhost)

[NamespaceManager]: /dotnet/api/microsoft.servicebus.namespacemanager
[EventHubClient]: /dotnet/api/microsoft.azure.eventhubs.eventhubclient
[Eventdata]: /dotnet/api/microsoft.azure.eventhubs.eventdata
[CreateEventHubIfNotExists]: /dotnet/api/microsoft.servicebus.namespacemanager.createeventhubifnotexists
[PartitionKey]: /dotnet/api/microsoft.servicebus.messaging.eventdata#Microsoft_ServiceBus_Messaging_EventData_PartitionKey
[EventProcessorHost]: /dotnet/api/microsoft.azure.eventhubs.processor
