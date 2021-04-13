---
title: zahrnout soubor
description: zahrnout soubor
services: service-bus-messaging
author: clemensv
ms.service: service-bus-messaging
ms.topic: include
ms.date: 04/08/2021
ms.author: clemensv
ms.custom: include file
ms.openlocfilehash: 08fccf366321b075542f36b86c9bf22d5d877167
ms.sourcegitcommit: b4fbb7a6a0aa93656e8dd29979786069eca567dc
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/13/2021
ms.locfileid: "107304409"
---
Možnost protokolu AMQP-over-WebSockets se spouští přes port TCP 443 stejně jako HTTP/REST API, ale je jinak funkčně identická s prostým AMQP. Tato možnost má vyšší latenci při počátečním připojení, protože dodatečné metody handshake a mírně větší režijní náklady na sdílení portu HTTPS. Pokud je vybrán tento režim, je pro komunikaci dostačující port TCP 443. Následující možnosti umožňují vybrat režim WebSockets AMQP. 

| Jazyk | Možnost   |
| -------- | ----- |
| .NET (Azure.Messaging.ServiceBus)    | Vytvořte [ServiceBusClient](/dotnet/api/azure.messaging.servicebus.servicebusclient.-ctor) pomocí konstruktoru, který jako parametr přijímá [ServiceBusClientOptions](/dotnet/api/azure.messaging.servicebus.servicebusclientoptions) . Nastavte [ServiceBusClientOptions. TransportType](/dotnet/api/azure.messaging.servicebus.servicebusclientoptions.transporttype) na [ServiceBusTransportType. AmqpWebSockets](/dotnet/api/azure.messaging.servicebus.servicebustransporttype) . |
| .NET (Microsoft.Azure.ServiceBus)    | Při vytváření objektů klienta použijte konstruktory, které jako parametry přijímají [TransportType](/dotnet/api/microsoft.azure.servicebus.transporttype), [ServiceBusConnection](/dotnet/api/microsoft.azure.servicebus.servicebusconnection)nebo [ServiceBusConnectionStringBuilder](/dotnet/api/microsoft.azure.servicebus.servicebusconnectionstringbuilder) . <p>Pro konstrukci, která přijímá `transportType` jako parametr, nastavte parametr na [TransportType. AmqpWebSockets](/dotnet/api/microsoft.azure.servicebus.transporttype).</p> <p>Pro konstruktor, který přijímá `ServiceBusConnection` jako parametr, nastavte [ServiceBusConnection. TransportType](/dotnet/api/microsoft.azure.servicebus.servicebusconnection.transporttype) na [TransportType. AmqpWebSockets](/dotnet/api/microsoft.azure.servicebus.transporttype).</p> <p>Použijete `ServiceBusConnectionStringBuilder` -li, použijte konstruktory, které vám umožní určit parametr `transportType` .</p> |
| Java (com. Azure. Messaging. ServiceBus)     | Při vytváření klientů nastavte [ServiceBusClientBuilder. TransportType](/java/api/com.azure.messaging.servicebus.servicebusclientbuilder.transporttype) na [AmqpTransportType.AMQP.AMQP_WEB_SOCKETS](/java/api/com.azure.core.amqp.amqptransporttype) |
| Java (com. Microsoft. Azure. ServiceBus)    | Při vytváření klientů nastavte `transportType` v [com. Microsoft. Azure. ServiceBus. ClientSettings](/java/api/com.microsoft.azure.servicebus.clientsettings.clientsettings#com_microsoft_azure_servicebus_ClientSettings_ClientSettings_com_microsoft_azure_servicebus_security_TokenProvider_com_microsoft_azure_servicebus_primitives_RetryPolicy_java_time_Duration_com_microsoft_azure_servicebus_primitives_TransportType_)  na [com.Microsoft.Azure.ServiceBus.Primitives.TransportType.AMQP_WEB_SOCKETS](/java/api/com.microsoft.azure.servicebus.primitives.transporttype) |
| JavaScript  | Při vytváření Service Bus objektů klienta použijte `webSocketOptions` vlastnost v [ServiceBusClientOptions](/javascript/api/@azure/service-bus/servicebusclientoptions). |
| Python | Při vytváření klientů Service Bus nastavte [ServiceBusClient.transport_type](/python/api/azure-servicebus/azure.servicebus.servicebusclient) na [TransportType. AmqpOverWebSocket.](/python/api/azure-servicebus/azure.servicebus.transporttype) |

