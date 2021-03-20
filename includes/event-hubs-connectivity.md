---
title: zahrnout soubor
description: zahrnout soubor
services: event-hubs
author: spelluru
ms.service: event-hubs
ms.topic: include
ms.date: 01/21/2021
ms.author: spelluru
ms.custom: include file
ms.openlocfilehash: 44afd8ea4ef2ab06ec31b7528e9776faebc3b4dc
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2021
ms.locfileid: "98690089"
---
### <a name="what-ports-do-i-need-to-open-on-the-firewall"></a>Jaké porty potřebuji v bráně firewall otevřít? 
Pomocí následujících protokolů se službou Azure Event Hubs můžete posílat a přijímat události:

- Rozšířený protokol řízení front zpráv (AMQP) 1,0 (AMQP)
- Http (Hypertext Transfer Protocol 1,1) s protokolem TLS (HTTPS)
- Apache Kafka

V následující tabulce najdete Odchozí porty, které musíte otevřít, abyste mohli tyto protokoly používat ke komunikaci s Azure Event Hubs. 

| Protokol | Porty | Podrobnosti | 
| -------- | ----- | ------- | 
| AMQP | 5671 a 5672 | Viz [Průvodce protokolem AMQP](../articles/service-bus-messaging/service-bus-amqp-protocol-guide.md) . | 
| HTTPS | 443 | Tento port se používá pro HTTP/REST API a pro sokety AMQP-over-Web. |
| Kafka | 9093 | Viz [použití Event Hubs z aplikací Kafka](../articles/event-hubs/event-hubs-for-kafka-ecosystem-overview.md) .

Port HTTPS se vyžaduje pro odchozí komunikaci, i když se AMQP používá přes port 5671, protože klientské sady SDK prováděly různé operace správy, které provádějí klientské sady SDK, a získání tokenů z Azure Active Directory (Pokud se používá) se spouští přes protokol HTTPS. 

Oficiální sady Azure SDK obecně používají protokol AMQP pro posílání a příjem událostí z Event Hubs. Možnost protokolu AMQP-over-WebSockets se spouští přes port TCP 443 stejně jako rozhraní HTTP API, ale je jinak funkčně identická s prostým AMQP. Tato možnost má vyšší latenci počátečního připojení, protože dodatečné metody handshake jsou rychlejší a mírně větší režijní náklady na sdílení portu HTTPS. Pokud je vybrán tento režim, je pro komunikaci dostačující port TCP 443. Následující možnosti povolují výběr režimu jednoduchých AMQP nebo AMQP WebSockets:

| Jazyk | Možnost   |
| -------- | ----- |
| .NET     | [EventHubConnectionOptions. TransportType](/dotnet/api/azure.messaging.eventhubs.eventhubconnectionoptions.transporttype) – vlastnost s [EventHubsTransportType. AmqpTcp](/dotnet/api/azure.messaging.eventhubs.eventhubstransporttype) nebo [EventHubsTransportType. AmqpWebSockets](/dotnet/api/azure.messaging.eventhubs.eventhubstransporttype) |
| Java     | [com. Microsoft. Azure. eventhubs. EventProcessorClientBuilder. TransportType](/java/api/com.azure.messaging.eventhubs.eventprocessorclientbuilder.transporttype) s [AmqpTransportType. AMQP](/java/api/com.azure.core.amqp.amqptransporttype) nebo [AmqpTransportType.AMQP_WEB_SOCKETS](/java/api/com.azure.core.amqp.amqptransporttype) |
| Uzel  | [EventHubConsumerClientOptions](/javascript/api/@azure/event-hubs/eventhubconsumerclientoptions) má `webSocketOptions` vlastnost. |
| Python | [EventHubConsumerClient.transport_type](/python/api/azure-eventhub/azure.eventhub.eventhubconsumerclient) s [TransportType. AMQP](/python/api/azure-eventhub/azure.eventhub.transporttype) nebo [TransportType. AmqpOverWebSocket](/python/api/azure-eventhub/azure.eventhub.transporttype) |

### <a name="what-ip-addresses-do-i-need-to-allow"></a>Jaké IP adresy potřebuji povolit?
Když pracujete s Azure, někdy je potřeba, abyste v podnikové bráně firewall nebo proxy povolili konkrétní rozsahy IP adres nebo adresy URL pro přístup ke všem službám Azure, které používáte nebo se pokoušíte použít. Ověřte, jestli je povolený provoz na IP adresách, které používá Event Hubs. Pro IP adresy, které používá Azure Event Hubs: Přečtěte si téma [rozsahy IP adres Azure a značky služeb – veřejný cloud](https://www.microsoft.com/download/details.aspx?id=56519).

Ověřte také, zda je povolena IP adresa pro váš obor názvů. Chcete-li najít správné IP adresy, které mají být pro vaše připojení povoleny, postupujte takto:

1. Z příkazového řádku spusťte následující příkaz: 

    ```
    nslookup <YourNamespaceName>.servicebus.windows.net
    ```
2. Poznamenejte si IP adresu vrácenou v `Non-authoritative answer` . 

Pokud používáte **redundanci zóny** pro svůj obor názvů, musíte provést několik kroků navíc: 

1. Nejprve spustíte nástroj nslookup v oboru názvů.

    ```
    nslookup <yournamespace>.servicebus.windows.net
    ```
2. Poznamenejte si název v části **nesměrodatná odpověď** , která je v jednom z následujících formátů: 

    ```
    <name>-s1.cloudapp.net
    <name>-s2.cloudapp.net
    <name>-s3.cloudapp.net
    ```
3. Spusťte nástroj nslookup pro každý z nich s příponami S1, S2 a S3 k získání IP adres všech tří instancí spuštěných ve třech zónách dostupnosti. 

    > [!NOTE]
    > IP adresa vrácená `nslookup` příkazem není statická IP adresa. Zůstává ale konstantní, dokud se základní nasazení neodstraní nebo nepřesune do jiného clusteru.

### <a name="what-client-ips-are-sending-events-to-or-receiving-events-from-my-namespace"></a>Jaká IP adresa klienta odesílá události do nebo přijímá události z oboru názvů?
Nejdřív v oboru názvů povolte [filtrování IP adres](../articles/event-hubs/event-hubs-ip-filtering.md) . 

Potom povolte protokoly diagnostiky pro [Event Hubs události připojení k virtuální síti](../articles/event-hubs/event-hubs-diagnostic-logs.md#event-hubs-virtual-network-connection-event-schema) podle pokynů v tématu [Povolení diagnostických protokolů](../articles/event-hubs/event-hubs-diagnostic-logs.md#enable-diagnostic-logs). Zobrazí se IP adresa, pro kterou bylo připojení odepřeno.

```json
{
    "SubscriptionId": "0000000-0000-0000-0000-000000000000",
    "NamespaceName": "namespace-name",
    "IPAddress": "1.2.3.4",
    "Action": "Deny Connection",
    "Reason": "IPAddress doesn't belong to a subnet with Service Endpoint enabled.",
    "Count": "65",
    "ResourceId": "/subscriptions/0000000-0000-0000-0000-000000000000/resourcegroups/testrg/providers/microsoft.eventhub/namespaces/namespace-name",
    "Category": "EventHubVNetConnectionEvent"
}
```

> [!IMPORTANT]
> Protokoly virtuální sítě se generují jenom v případě, že obor názvů umožňuje přístup z **konkrétních IP adres** (pravidla filtru IP). Pokud nechcete omezit přístup k vašemu oboru názvů pomocí těchto funkcí a přesto chcete získat protokoly virtuální sítě ke sledování IP adres klientů připojujících se k Event Hubsému oboru názvů, můžete použít následující řešení: Povolit filtrování IP adres a přidat celkový adresovatelný rozsah IPv4 (1.0.0.0/1-255.0.0.0/1). Event Hubs nepodporuje rozsahy IPv6 adres. 

> [!NOTE]
> V současné době není možné určit zdrojovou IP adresu jednotlivé zprávy nebo události. 