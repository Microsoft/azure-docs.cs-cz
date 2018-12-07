---
title: Průvodce programováním pro Azure Event Hubs | Dokumentace Microsoftu
description: Psaní kódu pro Azure Event Hubs pomocí sady Azure .NET SDK.
services: event-hubs
documentationcenter: na
author: ShubhaVijayasarathy
ms.service: event-hubs
ms.custom: seodec18
ms.topic: article
ms.date: 12/06/2018
ms.author: shvija
ms.openlocfilehash: cd97aed36e9fd82df0d37913d5ea9e57c875a673
ms.sourcegitcommit: 698ba3e88adc357b8bd6178a7b2b1121cb8da797
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/07/2018
ms.locfileid: "53011449"
---
# <a name="programming-guide-for-azure-event-hubs"></a>Průvodce programováním pro službu Azure Event Hubs
Tento článek popisuje některé běžné situace při psaní kódu s využitím služby Azure Event Hubs. Předpokládá se předběžná znalost služby Event Hubs. Koncepční přehled služby Event Hubs naleznete v tématu [Přehled služby Event Hubs](event-hubs-what-is-event-hubs.md).

## <a name="event-publishers"></a>Zdroje událostí

Odesíláte události do centra událostí, buď pomocí HTTP POST nebo prostřednictvím připojení protokolu AMQP 1.0. Možnost, která chcete používat a kdy závisí na konkrétním adresovaném scénáři. Připojení protokolu AMQP 1.0 se měří jako zprostředkovaná připojení ve službě Service Bus. Díky tomu, že poskytují trvalý kanál pro zasílání zpráv, jsou vhodnější ve scénářích, kde se počítá s častými vysokými objemy zpráv a vyžaduje se nižší latence.

Pokud používáte rozhraní API spravované pomocí .NET, budou primárními konstrukcemi pro publikování dat ve službě Event Hubs třídy [EventHubClient][] a [EventData][]. [EventHubClient][] poskytuje komunikační kanál AMQP, přes který se události posílají do centra událostí. [EventData][] třída představuje událost a slouží k publikování zpráv do centra událostí. Tato třída obsahuje tělo, některá metadata a záhlaví s informacemi o události. Další vlastnosti jsou přidány do [EventData][] objektu prostřednictvím centra událostí.

## <a name="get-started"></a>Začínáme

Třídy, které podporují službu Event Hubs jsou k dispozici v rozhraní .NET [Microsoft.Azure.EventHubs](https://www.nuget.org/packages/Microsoft.Azure.EventHubs/) balíček NuGet. Můžete nainstalovat pomocí Průzkumníka řešení v sadě Visual Studio nebo [Konzola správce balíčků](http://docs.nuget.org/docs/start-here/using-the-package-manager-console) v sadě Visual Studio. V tom případě je potřeba zadat v okně [konzoly Správce balíčků](http://docs.nuget.org/docs/start-here/using-the-package-manager-console) následující příkaz:

```shell
Install-Package Microsoft.Azure.EventHubs
```

## <a name="create-an-event-hub"></a>Vytvoření centra událostí

Webu Azure portal, Azure Powershellu nebo Azure CLI slouží k vytvoření služby Event Hubs. Podrobnosti najdete v tématu [vytvořit obor názvů služby Event Hubs a centra událostí pomocí webu Azure portal](event-hubs-create.md).

## <a name="create-an-event-hubs-client"></a>Vytvoření klienta pro centra událostí (Event Hubs)

Primární třídou pro interakci se službou Event Hubs je [Microsoft.Azure.EventHubs.EventHubClient][EventHubClient]. Můžete vytvořit instanci této třídy pomocí [CreateFromConnectionString](/dotnet/api/microsoft.azure.eventhubs.eventhubclient.createfromconnectionstring) způsob, jak je znázorněno v následujícím příkladu:

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

Odesílání událostí do centra událostí tak, že vytvoříte [EventHubClient][] instance a odesláním asynchronně prostřednictvím [SendAsync](/dotnet/api/microsoft.azure.eventhubs.eventhubclient.sendasync) metody. Tato metoda přebírá jediný [EventData][] parametru instance a asynchronně odešle do centra událostí.

## <a name="event-serialization"></a>Serializace událostí

[EventData][] třída má [dvě přetížené konstruktory](/dotnet/api/microsoft.azure.eventhubs.eventdata.-ctor) , které přebírají různé parametry, bajtů nebo s polem bajtů, které představují data datové části události. Pokud při práci s třídou [EventData][] používáte JSON, můžete načíst pole bajtů řetězce kódovaného ve formátu JSON pomocí metody **Encoding.UTF8.GetBytes()**. Příklad:

```csharp
for (var i = 0; i < numMessagesToSend; i++)
{
    var message = $"Message {i}";
    Console.WriteLine($"Sending message: {message}");
    await eventHubClient.SendAsync(new EventData(Encoding.UTF8.GetBytes(message)));
}
```

## <a name="partition-key"></a>Klíč oddílu

Při odesílání dat události, můžete zadat hodnotu, která se po zahašování použije k vytvoření přiřazení k oddílu. Můžete zadat pomocí oddílu [PartitionSender.PartitionID](/dotnet/api/microsoft.azure.eventhubs.partitionsender.partitionid) vlastnost. Však rozhodnout a použít oddíly znamená možnost volby mezi dostupností a konzistencí. 

### <a name="availability-considerations"></a>Aspekty dostupnosti

Použití klíče oddílu je volitelný a měli byste pečlivě zvážit, jestli chcete použít jeden. V mnoha případech použití klíče oddílu je dobrou volbou, pokud řazení událostí je důležité. Pokud použijete klíč oddílu, tyto oddíly vyžadují dostupnost v jednom uzlu a můžete v čase, dojde k výpadku například se při výpočetní uzly restartování a opravy. V důsledku toho pokud nastavujete ID oddílu a daného oddílu z nějakého důvodu nedostupný, se nezdaří pokus o přístup k datům v tomto oddílu. Pokud je vysoká dostupnost vašich nejdůležitějších, nezadávejte klíč oddílu; v takovém případě se události posílají do oddílů pomocí modelu kruhového je popsáno výše. V tomto scénáři vytvoříte explicitní volbu mezi dostupností (žádné ID oddílu) a konzistenci (Připnutí události pro ID oddílu).

Zpoždění při zpracování události je zpracování potřeba vzít v úvahu. V některých případech může být lepší vyřazení dat a zkuste to znovu, než se pokoušet držet krok s zpracování, což může způsobit další zpracování příjmu dat zpoždění. Například s běžícími je vhodnější počkat na dokončení aktuální data, ale v živý chat nebo scénář VOIP byste raději chtěli data rychle, i v případě, že není kompletní.

Tyto požadavky na dostupnost podle scénářů můžete zvolit jednu z následujících chyb strategie zpracování:

- Stop (Zastavit čtení ze služby Event Hubs, dokud nebudou opraveny things)
- Vyřadit (zprávy nejsou důležité. můžete vyřadit je)
- Opakovat (zprávy podle svých potřeb opakování)

Další informace a diskuze o kompromisech mezi dostupností a konzistencí najdete v tématu [dostupnost a konzistence ve službě Event Hubs](event-hubs-availability-and-consistency.md). 

## <a name="batch-event-send-operations"></a>Dávkové operace odesílání událostí

Odesílání událostí v dávkách může pomoci zvýšit propustnost. Můžete použít [CreateBatch](/dotnet/api/microsoft.azure.eventhubs.eventhubclient.createbatch) rozhraní API k vytvoření dávky dat je možné později přidat objekty pro [SendAsync](/dotnet/api/microsoft.azure.eventhubs.eventhubclient.sendasync) volání.

Jeden batch nesmí překročit velikost 256 KB omezení pro událost. Kromě toho každá zpráva v batchi používá stejnou identitu zdroje. Dodržení maximálního limitu velikosti události u batche musí zajistit odesílatel. V případě překročení se u klienta vygeneruje chyba odeslání (**Send**). Můžete použít metodu helper [EventHubClient.CreateBatch](/dotnet/api/microsoft.azure.eventhubs.eventhubclient.createbatch) zajistit, že služby batch není delší než 256 KB. Získejte prázdnou [EventDataBatch](/dotnet/api/microsoft.azure.eventhubs.eventdatabatch) z [CreateBatch](/dotnet/api/microsoft.azure.eventhubs.eventhubclient.createbatch) rozhraní API a pak použijte [TryAdd](/dotnet/api/microsoft.azure.eventhubs.eventdatabatch.tryadd) přidání události k vytvoření služby batch. 

## <a name="send-asynchronously-and-send-at-scale"></a>Asynchronní odesílání a škálované odesílání

Asynchronně odesíláte události do centra událostí. Asynchronní odesílání zvýší rychlost, s jakou je klient schopný odesílat události. [SendAsync](/dotnet/api/microsoft.azure.eventhubs.eventhubclient.sendasync) vrátí [úloh](https://msdn.microsoft.com/library/system.threading.tasks.task.aspx) objektu. Můžete použít [RetryPolicy](/dotnet/api/microsoft.servicebus.retrypolicy) možnosti opakování třídy na straně klienta pro klienta ovládacího prvku.

## <a name="event-consumers"></a>Příjemci událostí

Třída [EventProcessorHost][] zpracovává data z center událostí (Event Hubs). Tuto implementaci byste měli používat při vytváření čtenářů událostí na platformě .NET. Třída [EventProcessorHost][] poskytuje pro implementace zpracovatelů událostí bezpečné prostředí runtime, které umožňuje bezpečné použití vláken a více procesů. Taky poskytuje možnost vytváření kontrolních bodů a správy „půjčování“ oddílu.

K použití třídy [EventProcessorHost][] může být potřeba implementovat rozhraní [IEventProcessor](/dotnet/api/microsoft.azure.eventhubs.processor.ieventprocessor). Toto rozhraní obsahuje čtyři metody:

* [OpenAsync](/dotnet/api/microsoft.azure.eventhubs.processor.ieventprocessor.openasync)
* [CloseAsync](/dotnet/api/microsoft.azure.eventhubs.processor.ieventprocessor.closeasync)
* [ProcessEventsAsync](/dotnet/api/microsoft.azure.eventhubs.processor.ieventprocessor.processeventsasync)
* [ProcessErrorAsync](/dotnet/api/microsoft.azure.eventhubs.processor.ieventprocessor.processerrorasync)

Abyste mohli zahájit zpracování událostí, vytvořit instanci [EventProcessorHost][], poskytnutím příslušných parametrů pro vaše Centrum událostí. Příklad:

```csharp
var eventProcessorHost = new EventProcessorHost(
        EventHubName,
        PartitionReceiver.DefaultConsumerGroupName,
        EventHubConnectionString,
        StorageConnectionString,
        StorageContainerName);
```

Potom zavolejte [RegisterEventProcessorAsync](/dotnet/api/microsoft.azure.eventhubs.processor.eventprocessorhost.registereventprocessorasync) zaregistrovat vaše [IEventProcessor](/dotnet/api/microsoft.azure.eventhubs.processor.ieventprocessor) implementace s modulem runtime:

```csharp
await eventProcessorHost.RegisterEventProcessorAsync<SimpleEventProcessor>();
```

V tomto okamžiku hostitel bude snažit získat zapůjčení na každý oddíl v Centru událostí použitím "chamtivého" algoritmu. Tato zapůjčení vydrží po stanovenou dobu trvat a následně musí být obnovena. V tomto případě se instance pracovních procesů připojí jako nové uzly a umístí své rezervace zapůjčení. Každý uzel se snaží získat další zapůjčení, a tak se v průběhu času zatížení posune.

![Event Processor Host](./media/event-hubs-programming-guide/IC759863.png)

Postupem času se dosáhne rovnováhy. Tato dynamická funkce umožňuje použití automatického škálování na bázi procesoru u příjemců jak pro vertikální navýšení, tak pro vertikální snížení kapacity. Protože Služba Event Hubs nemá přímým konceptem počtu zpráv, je průměrné využití procesoru často nejlepším mechanismem, jak měřit back end nebo škálování příjemců. Když začnou zdroje publikovat více událostí, než mohou příjemci zpracovat, může být zvýšená aktivita procesoru využita k automatickému škálování počtu instancí pracovních procesů.

Třída [EventProcessorHost][] taky implementuje mechanismus vytváření kontrolních bodů Azure na bázi úložiště. Tento mechanismus ukládá posun na bázi oddílu, každý příjemce tedy může určit poslední kontrolní bod předchozího příjemce. Vzhledem k tomu, že oddíly prostřednictvím zapůjčení přechází mezi uzly, usnadňuje tento synchronizační mechanismus přesun zátěže.

## <a name="publisher-revocation"></a>Odvolání zdroje

Kromě pokročilých funkcí za běhu [EventProcessorHost][], umožňuje Služba Event Hubs odvolání zdroje, aby bylo možné blokovat konkrétním zdrojům možnost odesílat události do centra událostí. Tyto funkce jsou užitečné, pokud došlo k napadení tokenu zdroje nebo aktualizace softwaru je příčinou chovat nevhodně. V těchto situacích může být pro identitu zdroje, která je součástí jeho tokenu SAS, zablokováno publikování událostí.

Další informace o odvolání zdroje a o tom, jak jako zdroj odesílat do centra událostí, najdete v ukázce [zabezpečeného publikování ve velkém rozsahu u služby Event Hubs](https://code.msdn.microsoft.com/Service-Bus-Event-Hub-99ce67ab).

## <a name="next-steps"></a>Další postup

Další informace o scénářích služby Event Hubs naleznete pod těmito odkazy:

* [Přehled rozhraní API služby Event Hubs](event-hubs-api-overview.md)
* [Co je Event Hubs](event-hubs-what-is-event-hubs.md)
* [Dostupnost a konzistence ve službě Event Hubs](event-hubs-availability-and-consistency.md)
* [Referenční dokumentace rozhraní API třídy EventProcessorHost](/dotnet/api/microsoft.servicebus.messaging.eventprocessorhost)

[NamespaceManager]: /dotnet/api/microsoft.servicebus.namespacemanager
[EventHubClient]: /dotnet/api/microsoft.azure.eventhubs.eventhubclient
[EventData]: /dotnet/api/microsoft.azure.eventhubs.eventdata
[CreateEventHubIfNotExists]: /dotnet/api/microsoft.servicebus.namespacemanager.createeventhubifnotexists
[PartitionKey]: /dotnet/api/microsoft.servicebus.messaging.eventdata#Microsoft_ServiceBus_Messaging_EventData_PartitionKey
[EventProcessorHost]: /dotnet/api/microsoft.azure.eventhubs.processor
