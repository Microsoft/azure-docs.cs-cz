---
title: zahrnout soubor
description: zahrnout soubor
services: service-bus-messaging
author: clemensv
ms.service: service-bus-messaging
ms.topic: include
ms.date: 11/24/2020
ms.author: clemensv
ms.custom: include file
ms.openlocfilehash: ca483d0b71bde945a7e46da785dd6a76b3a8f177
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/29/2021
ms.locfileid: "98693391"
---
Možnost protokolu AMQP-over-WebSockets se spouští přes port TCP 443 stejně jako HTTP/REST API, ale je jinak funkčně identická s prostým AMQP. Tato možnost má trochu větší latenci při počátečním připojení, protože u zvýšeného handshake dojde k dalším zpětným signalizaci a mírně větší režii jako u sdílení portu HTTPS. Pokud je vybrán tento režim, je pro komunikaci dostačující port TCP 443. Následující možnosti povolují výběr režimu jednoduchých AMQP nebo AMQP WebSockets:

| Jazyk | Možnost   |
| -------- | ----- |
| .NET     | [ServiceBusConnection. TransportType](/dotnet/api/microsoft.azure.servicebus.servicebusconnection.transporttype) – vlastnost s [TransportType. AMQP](/dotnet/api/microsoft.azure.servicebus.transporttype) nebo [TransportType. AmqpWebSockets](/dotnet/api/microsoft.azure.servicebus.transporttype) |
| Java     | [com. Microsoft. Azure. ServiceBus. ClientSettings](/java/api/com.microsoft.azure.servicebus.clientsettings.clientsettings) s [com. Microsoft. Azure. ServiceBus. primitivs. TransportType. AMQP](/java/api/com.microsoft.azure.servicebus.primitives.transporttype) nebo [com.Microsoft.Azure.ServiceBus.Primitives.TransportType.AMQP_WEB_SOCKETS](/java/api/com.microsoft.azure.servicebus.primitives.transporttype) |
| Uzel  | [ServiceBusClientOptions](/javascript/api/@azure/service-bus/servicebusclientoptions) má `webSocket` argument konstruktoru. |
| Python | [ServiceBusClient.transport_type](https://azuresdkdocs.blob.core.windows.net/$web/python/azure-servicebus/latest/azure.servicebus.html#azure.servicebus.ServiceBusClient) s [TransportType. AMQP](https://azuresdkdocs.blob.core.windows.net/$web/python/azure-servicebus/latest/azure.servicebus.html#azure.servicebus.TransportType) nebo [TransportType. AmqpOverWebSocket](https://azuresdkdocs.blob.core.windows.net/$web/python/azure-servicebus/latest/azure.servicebus.html#azure.servicebus.TransportType) |