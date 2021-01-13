---
title: Průvodce programováním v .NET – Azure Event Hubs (starší verze) | Microsoft Docs
description: Tento článek poskytuje informace o tom, jak napsat kód pro Azure Event Hubs pomocí sady Azure .NET SDK.
ms.topic: article
ms.date: 06/23/2020
ms.custom: devx-track-csharp
ms.openlocfilehash: 46bd0c3c1488d6dd7afbae5e88e0b83f56654bb8
ms.sourcegitcommit: 431bf5709b433bb12ab1f2e591f1f61f6d87f66c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/12/2021
ms.locfileid: "98131232"
---
# <a name="net-programming-guide-for-azure-event-hubs-legacy-microsoftazureeventhubs-package"></a>Průvodce programováním v rozhraní .NET pro Azure Event Hubs (starší balíček Microsoft. Azure. EventHubs)
Tento článek popisuje některé běžné scénáře psaní kódu pomocí Azure Event Hubs. Předpokládá se předběžná znalost služby Event Hubs. Koncepční přehled služby Event Hubs naleznete v tématu [Přehled služby Event Hubs](./event-hubs-about.md).

> [!WARNING]
> Tato příručka je určena pro starý balíček **Microsoft. Azure. EventHubs** . Doporučujeme, abyste při [migraci](https://github.com/Azure/azure-sdk-for-net/blob/master/sdk/eventhub/Azure.Messaging.EventHubs/MigrationGuide.md) kódu používali nejnovější balíček [Azure. Messaging. EventHubs](event-hubs-dotnet-standard-getstarted-send.md) .  


## <a name="event-publishers"></a>Zdroje událostí

Události se odesílají do centra událostí buď pomocí HTTP POST, nebo prostřednictvím připojení AMQP 1,0. Volba, která se má použít a která závisí na konkrétním scénáři, který se řeší. Připojení protokolu AMQP 1.0 se měří jako zprostředkovaná připojení ve službě Service Bus. Díky tomu, že poskytují trvalý kanál pro zasílání zpráv, jsou vhodnější ve scénářích, kde se počítá s častými vysokými objemy zpráv a vyžaduje se nižší latence.

Pokud používáte rozhraní API spravované pomocí .NET, budou primárními konstrukcemi pro publikování dat ve službě Event Hubs třídy [EventHubClient][] a [EventData][]. [EventHubClient][] poskytuje komunikační kanál AMQP, přes který se události odesílají do centra událostí. Třída [EventData][] představuje událost a slouží k publikování zpráv do centra událostí. Tato třída zahrnuje tělo, některá metadata (vlastnosti) a informace hlavičky (SystemProperties) o události. Do objektu [EventData][] se přidají další vlastnosti, které procházejí centrem událostí.

## <a name="get-started"></a>Začínáme
Třídy .NET, které podporují Event Hubs, jsou k dispozici v balíčku NuGet [Microsoft. Azure. EventHubs](https://www.nuget.org/packages/Microsoft.Azure.EventHubs/) . Můžete nainstalovat pomocí Průzkumníka řešení sady Visual Studio nebo [konzoly Správce balíčků](https://docs.nuget.org/docs/start-here/using-the-package-manager-console) v aplikaci Visual Studio. V tom případě je potřeba zadat v okně [konzoly Správce balíčků](https://docs.nuget.org/docs/start-here/using-the-package-manager-console) následující příkaz:

```shell
Install-Package Microsoft.Azure.EventHubs
```

## <a name="create-an-event-hub"></a>Vytvoření centra událostí

K vytvoření Event Hubs můžete použít Azure Portal, Azure PowerShell nebo rozhraní příkazového řádku Azure. Podrobnosti najdete v tématu [Vytvoření oboru názvů Event Hubs a centra událostí pomocí Azure Portal](event-hubs-create.md).

## <a name="create-an-event-hubs-client"></a>Vytvoření klienta pro centra událostí (Event Hubs)

Primární třídou pro interakci s Event Hubs je [Microsoft. Azure. EventHubs. EventHubClient][EventHubClient]. Můžete vytvořit instanci této třídy pomocí metody [CreateFromConnectionString](/dotnet/api/microsoft.azure.eventhubs.eventhubclient.createfromconnectionstring) , jak je znázorněno v následujícím příkladu:

```csharp
private const string EventHubConnectionString = "Event Hubs namespace connection string";
private const string EventHubName = "event hub name";

var connectionStringBuilder = new EventHubsConnectionStringBuilder(EventHubConnectionString)
{
    EntityPath = EventHubName

};
eventHubClient = EventHubClient.CreateFromConnectionString(connectionStringBuilder.ToString());
```

## <a name="send-events-to-an-event-hub"></a>Odesílání událostí do centra událostí

Události odesíláte do centra událostí vytvořením instance [EventHubClient][] a jejím asynchronním odesláním prostřednictvím metody [SendAsync](/dotnet/api/microsoft.azure.eventhubs.eventhubclient.sendasync) . Tato metoda přijímá jeden parametr instance [EventData][] a asynchronně ho odesílá do centra událostí.

## <a name="event-serialization"></a>Serializace událostí

Třída [EventData][] má [dva přetížené konstruktory](/dotnet/api/microsoft.azure.eventhubs.eventdata.-ctor) , které přijímají různé parametry, bajty nebo bajtové pole, které reprezentují datovou část dat události. Pokud při práci s třídou [EventData][] používáte JSON, můžete načíst pole bajtů řetězce kódovaného ve formátu JSON pomocí metody **Encoding.UTF8.GetBytes()**. Příklad:

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
> Pokud nejste obeznámeni s oddíly, přečtěte si [Tento článek](event-hubs-features.md#partitions). 

Při odesílání dat události můžete zadat hodnotu, která má hash k vytvoření přiřazení oddílu. Oddíl určíte pomocí vlastnosti [PartitionSender. PartitionID](/dotnet/api/microsoft.azure.eventhubs.partitionsender.partitionid) . Rozhodnutí o použití oddílů ale implikuje možnost volby mezi dostupností a konzistencí. 

### <a name="availability-considerations"></a>Aspekty dostupnosti

Použití klíče oddílu je volitelné a měli byste pečlivě zvážit, zda jej použít. Pokud při publikování události nezadáte klíč oddílu, Event Hubs vyrovnává zatížení mezi oddíly. V mnoha případech je použití klíče oddílu dobrou volbou, pokud je řazení událostí důležité. Když použijete klíč oddílu, budou tyto oddíly vyžadovat dostupnost na jednom uzlu a výpadky můžou probíhat v průběhu času. například při restartu a opravě výpočetních uzlů. V takovém případě, pokud nastavíte ID oddílu a daný oddíl z nějakého důvodu nebude k dispozici, pokus o přístup k datům v tomto oddílu selže. Je-li vysoká dostupnost nejdůležitější, nezadávejte klíč oddílu. V takovém případě se události odesílají do oddílů pomocí interního algoritmu vyrovnávání zatížení. V tomto scénáři provedete explicitní volbu mezi dostupností (bez ID oddílu) a konzistencí (připnutí událostí k IDENTIFIKÁTORu oddílu).

Dalším aspektem je zpracování zpoždění při zpracování událostí. V některých případech může být vhodnější vyřadit data a opakovat se, než se pokusíte zachovat zpracování, což může potenciálně způsobit zpoždění při zpracování dat. Například u burzovních značek je lepší počkat na kompletní aktuální data, ale ve scénáři živého chatu nebo VOIP byste měli data rychle, a to i v případě, že není dokončená.

Vzhledem k těmto hlediskům dostupnosti můžete v těchto scénářích zvolit jednu z následujících strategií zpracování chyb:

- Zastavit (přerušit čtení z Event Hubs, dokud nebudou opraveny věci)
- Drop (zprávy nejsou důležité, přetáhněte je)
- Zkuste to znovu (podle potřeby zprávy zkuste zobrazit).

Další informace a diskuzi o kompromisech mezi dostupností a konzistencí najdete v tématu [dostupnost a konzistence v Event Hubs](event-hubs-availability-and-consistency.md). 

## <a name="batch-event-send-operations"></a>Dávkové operace odesílání událostí

Odesílání událostí v dávkách může přispět ke zvýšení propustnosti. Pomocí rozhraní [CreateBatch](/dotnet/api/microsoft.azure.eventhubs.eventhubclient.createbatch) API můžete vytvořit dávku, do které lze později přidat datové objekty pro volání [SendAsync](/dotnet/api/microsoft.azure.eventhubs.eventhubclient.sendasync) .

Jedna dávka nesmí překročit omezení 1 MB události. Kromě toho každá zpráva v batchi používá stejnou identitu zdroje. Dodržení maximálního limitu velikosti události u batche musí zajistit odesílatel. V případě překročení se u klienta vygeneruje chyba odeslání (**Send**). K zajištění, aby dávka nepřekročila 1 MB, můžete použít pomocnou metodu [EventHubClient. CreateBatch](/dotnet/api/microsoft.azure.eventhubs.eventhubclient.createbatch) . Z rozhraní [CreateBatch](/dotnet/api/microsoft.azure.eventhubs.eventhubclient.createbatch) API získáte prázdné [EventDataBatch](/dotnet/api/microsoft.azure.eventhubs.eventdatabatch) a pak pomocí [TryAdd](/dotnet/api/microsoft.azure.eventhubs.eventdatabatch.tryadd) přidáte události pro vytvoření dávky. 

## <a name="send-asynchronously-and-send-at-scale"></a>Asynchronní odesílání a škálované odesílání

Události se odesílají do centra událostí asynchronně. Odesílání asynchronně zvyšuje rychlost, s jakou klient může odesílat události. [SendAsync](/dotnet/api/microsoft.azure.eventhubs.eventhubclient.sendasync) vrátí objekt [Task](/dotnet/api/system.threading.tasks.task?view=netcore-3.1) . K řízení možností opakování klienta můžete použít třídu [RetryPolicy](/dotnet/api/microsoft.servicebus.retrypolicy) na klientovi.

## <a name="event-consumers"></a>Příjemci událostí
Třída [EventProcessorHost][] zpracovává data z center událostí (Event Hubs). Tuto implementaci byste měli používat při vytváření čtenářů událostí na platformě .NET. Třída [EventProcessorHost][] poskytuje pro implementace zpracovatelů událostí bezpečné prostředí runtime, které umožňuje bezpečné použití vláken a více procesů. Taky poskytuje možnost vytváření kontrolních bodů a správy „půjčování“ oddílu.

K použití třídy [EventProcessorHost][] může být potřeba implementovat rozhraní [IEventProcessor](/dotnet/api/microsoft.azure.eventhubs.processor.ieventprocessor). Toto rozhraní obsahuje čtyři metody:

* [OpenAsync](/dotnet/api/microsoft.azure.eventhubs.processor.ieventprocessor.openasync)
* [CloseAsync](/dotnet/api/microsoft.azure.eventhubs.processor.ieventprocessor.closeasync)
* [ProcessEventsAsync](/dotnet/api/microsoft.azure.eventhubs.processor.ieventprocessor.processeventsasync)
* [ProcessErrorAsync](/dotnet/api/microsoft.azure.eventhubs.processor.ieventprocessor.processerrorasync)

Pokud chcete spustit zpracování událostí, vytvořte instanci [EventProcessorHost][]a poskytněte příslušné parametry centra událostí. Příklad:

> [!NOTE]
> EventProcessorHost a související třídy jsou k dispozici v balíčku **Microsoft. Azure. EventHubs. Processor** . Přidejte balíček do projektu sady Visual Studio podle pokynů v [tomto článku](event-hubs-dotnet-framework-getstarted-send.md#add-the-event-hubs-nuget-package) nebo vyvoláním následujícího příkazu v okně [konzoly Správce balíčků](https://docs.nuget.org/docs/start-here/using-the-package-manager-console) : `Install-Package Microsoft.Azure.EventHubs.Processor` .

```csharp
var eventProcessorHost = new EventProcessorHost(
        EventHubName,
        PartitionReceiver.DefaultConsumerGroupName,
        EventHubConnectionString,
        StorageConnectionString,
        StorageContainerName);
```

Pak zavolejte [RegisterEventProcessorAsync](/dotnet/api/microsoft.azure.eventhubs.processor.eventprocessorhost.registereventprocessorasync) k registraci vaší implementace [IEventProcessor](/dotnet/api/microsoft.azure.eventhubs.processor.ieventprocessor) s modulem runtime:

```csharp
await eventProcessorHost.RegisterEventProcessorAsync<SimpleEventProcessor>();
```

V tomto okamžiku se hostitel pokusí získat zapůjčení na všech oddílech v centru událostí pomocí algoritmu "hladce". Tato zapůjčení pro daný rámec uplynula a musí být obnovena. V tomto případě se instance pracovních procesů připojí jako nové uzly a umístí své rezervace zapůjčení. Každý uzel se snaží získat další zapůjčení, a tak se v průběhu času zatížení posune.

![Event Processor Host](./media/event-hubs-programming-guide/IC759863.png)

Postupem času se dosáhne rovnováhy. Tato dynamická funkce umožňuje použití automatického škálování na bázi procesoru u příjemců jak pro vertikální navýšení, tak pro vertikální snížení kapacity. Vzhledem k tomu, že Event Hubs nemá přímý koncept počtů zpráv, je průměrné využití procesoru často nejlepším mechanismem měření back-endu nebo měřítka uživatele. Když začnou zdroje publikovat více událostí, než mohou příjemci zpracovat, může být zvýšená aktivita procesoru využita k automatickému škálování počtu instancí pracovních procesů.

Třída [EventProcessorHost][] taky implementuje mechanismus vytváření kontrolních bodů Azure na bázi úložiště. Tento mechanismus ukládá posun na bázi oddílu, každý příjemce tedy může určit poslední kontrolní bod předchozího příjemce. Vzhledem k tomu, že oddíly prostřednictvím zapůjčení přechází mezi uzly, usnadňuje tento synchronizační mechanismus přesun zátěže.

## <a name="publisher-revocation"></a>Odvolání zdroje

Kromě pokročilých běhových funkcí pro hostitele procesoru událostí umožňuje služba Event Hubs [odvolání vydavatele](/rest/api/eventhub/revoke-publisher) , aby bylo možné zablokovat konkrétním vydavatelům odeslání události do centra událostí. Tyto funkce jsou užitečné, pokud dojde k ohrožení bezpečnosti tokenu vydavatele nebo aktualizace softwaru způsobuje nepatřičné chování. V těchto situacích může být pro identitu zdroje, která je součástí jeho tokenu SAS, zablokováno publikování událostí.

> [!NOTE]
> V současné době tato funkce podporuje jenom REST API ([odvolání vydavatele](/rest/api/eventhub/revoke-publisher)).


## <a name="next-steps"></a>Další kroky

Další informace o scénářích služby Event Hubs naleznete pod těmito odkazy:

* [Přehled rozhraní API služby Event Hubs](./event-hubs-samples.md)
* [Co je Event Hubs](./event-hubs-about.md)
* [Dostupnost a konzistence ve službě Event Hubs](event-hubs-availability-and-consistency.md)
* [Referenční dokumentace rozhraní API třídy EventProcessorHost](/dotnet/api/microsoft.servicebus.messaging.eventprocessorhost)

[NamespaceManager]: /dotnet/api/microsoft.servicebus.namespacemanager
[EventHubClient]: /dotnet/api/microsoft.azure.eventhubs.eventhubclient
[EventData]: /dotnet/api/microsoft.azure.eventhubs.eventdata
[CreateEventHubIfNotExists]: /dotnet/api/microsoft.servicebus.namespacemanager.createeventhubifnotexists
[PartitionKey]: /dotnet/api/microsoft.servicebus.messaging.eventdata#Microsoft_ServiceBus_Messaging_EventData_PartitionKey
[EventProcessorHost]: /dotnet/api/microsoft.azure.eventhubs.processor
