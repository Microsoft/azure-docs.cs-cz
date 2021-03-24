---
title: Dostupnost a konzistence – Azure Event Hubs | Microsoft Docs
description: Jak zajistit maximální možnou dostupnost a konzistenci s Azure Event Hubs pomocí oddílů.
ms.topic: article
ms.date: 03/15/2021
ms.custom: devx-track-csharp
ms.openlocfilehash: e014a33e94fe7f90569dd2ef1e9b620eef274842
ms.sourcegitcommit: ac035293291c3d2962cee270b33fca3628432fac
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/24/2021
ms.locfileid: "104952860"
---
# <a name="availability-and-consistency-in-event-hubs"></a>Dostupnost a konzistence ve službě Event Hubs
Tento článek poskytuje informace o dostupnosti a konzistenci, které podporuje Azure Event Hubs. 

## <a name="availability"></a>Dostupnost
Azure Event Hubs šíří riziko závažných selhání jednotlivých počítačů nebo dokonce kompletních stojanů napříč clustery, které v rámci datového centra přesahují více domén selhání. Implementuje transparentní detekci selhání a mechanismy převzetí služeb při selhání tak, aby služba pokračovala v provozu v rámci zajištěných úrovní služeb a obvykle bez znatelného přerušení, když dojde k takovým selháním. 

Pokud byl vytvořen obor názvů Event Hubs s povolenými [zónami dostupnosti](../availability-zones/az-overview.md) , je riziko výpadku dále rozdělené mezi tři fyzicky oddělená zařízení a služba má dostatečné rezervy na kapacitu, aby se okamžitě zavedla k úplné a závažné ztrátě celého zařízení. Další informace najdete v tématu [Azure Event Hubs – geografická zotavení po havárii](event-hubs-geo-dr.md).

Když klientská aplikace pošle události do centra událostí bez určení oddílu, události se automaticky rozdělují mezi oddíly v centru událostí. Pokud oddíl z nějakého důvodu není dostupný, události se rozdělují mezi zbývající oddíly. Toto chování umožňuje pro největší dobu provozu. Pro případy použití, které vyžadují maximální dobu provozu, je tento model upřednostňován místo odesílání událostí do konkrétního oddílu. 

## <a name="consistency"></a>Konzistence
V některých scénářích může být řazení událostí důležité. Například může být vhodné, aby váš back-end systém zpracovával příkaz Update před příkazem DELETE. V tomto scénáři klientská aplikace odesílá události do konkrétního oddílu tak, aby bylo řazení zachováno. Když aplikace příjemce spotřebovává tyto události z oddílu, čtou se v daném pořadí. 

V případě této konfigurace Pamatujte na to, že pokud konkrétní oddíl, na který odesíláte, není k dispozici, zobrazí se chybová odpověď. Pokud v bodě porovnání nemáte spřažení k jednomu oddílu, Služba Event Hubs odešle událost do dalšího dostupného oddílu.

Proto pokud je nejdůležitější vysoká dostupnost, necílíte na konkrétní oddíl (pomocí ID oddílu/klíče). Pomocí ID oddílu/klíče se sníží dostupnost centra událostí na úrovni oddílů. V tomto scénáři provedete explicitní volbu mezi dostupností (bez ID oddílu/klíče) a konzistencí (připnutí událostí k určitému oddílu). Podrobné informace o oddílech v Event Hubs najdete v tématu [oddíly](event-hubs-features.md#partitions).

## <a name="appendix"></a>Přílohy

### <a name="send-events-without-specifying-a-partition"></a>Odesílání událostí bez určení oddílu
Doporučujeme odesílat události do centra událostí bez nastavování informací o oddílu, aby služba Event Hubs mohla vyrovnávat zatížení napříč oddíly. Další informace o tom, jak to udělat v různých programovacích jazycích, najdete v následujícím rychlém startu. 

- [Odesílání událostí pomocí .NET](event-hubs-dotnet-standard-getstarted-send.md)
- [Odesílání událostí pomocí jazyka Java](event-hubs-java-get-started-send.md)
- [Odesílání událostí pomocí JavaScriptu](event-hubs-python-get-started-send.md)
- [Odesílání událostí pomocí Pythonu](event-hubs-python-get-started-send.md)


### <a name="send-events-to-a-specific-partition"></a>Odeslání událostí do konkrétního oddílu
V této části se dozvíte, jak odesílat události do konkrétního oddílu pomocí různých programovacích jazyků. 

### <a name="net"></a>[.NET](#tab/dotnet)
Chcete-li odeslat události do konkrétního oddílu, vytvořte dávku pomocí metody [EventHubProducerClient. CreateBatchAsync](/dotnet/api/azure.messaging.eventhubs.producer.eventhubproducerclient.createbatchasync#Azure_Messaging_EventHubs_Producer_EventHubProducerClient_CreateBatchAsync_Azure_Messaging_EventHubs_Producer_CreateBatchOptions_System_Threading_CancellationToken_) , a to tak, že zadáte buď `PartitionId` nebo `PartitionKey` v [CreateBatchOptions](/dotnet/api/azure.messaging.eventhubs.producer.createbatchoptions). Následující kód pošle dávku událostí do konkrétního oddílu zadáním klíče oddílu. Event Hubs zajišťuje, aby všechny události sdílející hodnotu klíče oddílu byly uloženy společně a doručeny v pořadí doručení.

```csharp
var batchOptions = new CreateBatchOptions { PartitionKey = "cities" };
using var eventBatch = await producer.CreateBatchAsync(batchOptions);
```

Můžete také použít metodu [EventHubProducerClient. SendAsync](/dotnet/api/azure.messaging.eventhubs.producer.eventhubproducerclient.sendasync#Azure_Messaging_EventHubs_Producer_EventHubProducerClient_SendAsync_System_Collections_Generic_IEnumerable_Azure_Messaging_EventHubs_EventData__Azure_Messaging_EventHubs_Producer_SendEventOptions_System_Threading_CancellationToken_) tak, že v [SendEventOptions](/dotnet/api/azure.messaging.eventhubs.producer.sendeventoptions)zadáte buď **PartitionID** , nebo **PartitionKey** .

```csharp
var sendEventOptions  = new SendEventOptions { PartitionKey = "cities" };
// create the events array
producer.SendAsync(events, sendOptions)
```


### <a name="java"></a>[Java](#tab/java)
Chcete-li odeslat události do konkrétního oddílu, vytvořte dávku pomocí metody [createBatch](/java/api/com.azure.messaging.eventhubs.eventhubproducerclient.createbatch) , a to tak, že v [createBatchOptions](/java/api/com.azure.messaging.eventhubs.models.createbatchoptions)zadáte buď **ID oddílu** , nebo **klíč oddílu** . Následující kód pošle dávku událostí do konkrétního oddílu zadáním klíče oddílu. 

```java
CreateBatchOptions batchOptions = new CreateBatchOptions();
batchOptions.setPartitionKey("cities");
```

Metodu [EventHubProducerClient. Send](/java/api/com.azure.messaging.eventhubs.eventhubproducerclient.send#com_azure_messaging_eventhubs_EventHubProducerClient_send_java_lang_Iterable_com_azure_messaging_eventhubs_EventData__com_azure_messaging_eventhubs_models_SendOptions_) můžete použít také tak, že v [SendOptions](/java/api/com.azure.messaging.eventhubs.models.sendoptions)zadáte buď **ID oddílu** , nebo **klíč oddílu** .

```java
List<EventData> events = Arrays.asList(new EventData("Melbourne"), new EventData("London"), new EventData("New York"));
SendOptions sendOptions = new SendOptions();
sendOptions.setPartitionKey("cities");
producer.send(events, sendOptions);
```


### <a name="python"></a>[Python](#tab/python) 
Chcete-li při vytváření dávky pomocí metody Odeslat události do konkrétního oddílu, [`EventHubProducerClient.create_batch`](/python/api/azure-eventhub/azure.eventhub.eventhubproducerclient#create-batch---kwargs-) Zadejte `partition_id` nebo `partition_key` . Pak pomocí [`EventHubProducerClient.send_batch`](/python/api/azure-eventhub/azure.eventhub.aio.eventhubproducerclient#send-batch-event-data-batch--typing-union-azure-eventhub--common-eventdatabatch--typing-list-azure-eventhub-) metody odešlete dávku do oddílu centra událostí. 

```python
event_data_batch = await producer.create_batch(partition_key='cities')
```

Metodu [EventHubProducerClient.send_batch](/python/api/azure-eventhub/azure.eventhub.eventhubproducerclient#send-batch-event-data-batch----kwargs-) lze použít také zadáním hodnot `partition_id` `partition_key` parametrů nebo.

```python
producer.send_batch(event_data_batch, partition_key="cities")
```

### <a name="javascript"></a>[JavaScript](#tab/javascript)
Chcete-li odeslat události do konkrétního oddílu, [vytvořte dávku](/javascript/api/@azure/event-hubs/eventhubproducerclient#createBatch_CreateBatchOptions_) pomocí objektu [EventHubProducerClient. CreateBatchOptions](/javascript/api/@azure/event-hubs/eventhubproducerclient#createBatch_CreateBatchOptions_) zadáním `partitionId` nebo `partitionKey` . Pak odešlete dávku do centra událostí pomocí metody [EventHubProducerClient. SendBatch](/javascript/api/@azure/event-hubs/eventhubproducerclient#sendBatch_EventDataBatch__OperationOptions_) . 

Prohlédněte si následující příklad.

```javascript
const batchOptions = { partitionKey = "cities"; };
const batch = await producer.createBatch(batchOptions);
```

Můžete také použít metodu [EventHubProducerClient. sendBatch](/javascript/api/@azure/event-hubs/eventhubproducerclient#sendBatch_EventData____SendBatchOptions_) tak, že v [SendBatchOptions](/javascript/api/@azure/event-hubs/sendbatchoptions)zadáte buď **ID oddílu** , nebo **klíč oddílu** .

```javascript
const sendBatchOptions = { partitionKey = "cities"; };
// prepare events
producer.sendBatch(events, sendBatchOptions);
```

---



## <a name="next-steps"></a>Další kroky
Další informace o službě Event Hubs najdete na následujících odkazech:

- [Přehled služby Event Hubs](./event-hubs-about.md)
- [Terminologie Event Hubs](event-hubs-features.md)
