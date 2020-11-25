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
ms.openlocfilehash: fe205524c5aef6a43771cc09c810da217678d108
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/25/2020
ms.locfileid: "96022132"
---
Možnost protokolu AMQP-over-WebSockets se spouští přes port TCP 443 stejně jako HTTP/REST API, ale je jinak funkčně identická s prostým AMQP. Tato možnost má trochu větší latenci při počátečním připojení, protože u zvýšeného handshake dojde k dalším zpětným signalizaci a mírně větší režii jako u sdílení portu HTTPS. Pokud je vybrán tento režim, je pro komunikaci dostačující port TCP 443. Následující možnosti povolují výběr režimu jednoduchých AMQP nebo AMQP WebSockets:

| Jazyk | Možnost   |
| -------- | ----- |
| .NET     | [ServiceBusConnection. TransportType](/dotnet/api/microsoft.azure.servicebus.servicebusconnection.transporttype?view=azure-dotnet) – vlastnost s [TransportType. AMQP](/dotnet/api/microsoft.azure.servicebus.transporttype?view=azure-dotnet) nebo [TransportType. AmqpWebSockets](/dotnet/api/microsoft.azure.servicebus.transporttype?view=azure-dotnet) |
| Java     | [com. Microsoft. Azure. ServiceBus. ClientSettings](/java/api/com.microsoft.azure.servicebus.clientsettings.clientsettings?view=azure-java-stable) s [com. Microsoft. Azure. ServiceBus. primitivs. TransportType. AMQP](/java/api/com.microsoft.azure.servicebus.primitives.transporttype?view=azure-java-stable) nebo [com.Microsoft.Azure.ServiceBus.Primitives.TransportType.AMQP_WEB_SOCKETS](/java/api/com.microsoft.azure.servicebus.primitives.transporttype?view=azure-java-stable) |
| Node  | [ServiceBusClientOptions](/javascript/api/@azure/service-bus/servicebusclientoptions?view=azure-node-latest) má `webSocket` argument konstruktoru. |
| Python | [ServiceBusClient.transport_type](https://azuresdkdocs.blob.core.windows.net/$web/python/azure-servicebus/latest/azure.servicebus.html#azure.servicebus.ServiceBusClient) s [TransportType. AMQP](https://azuresdkdocs.blob.core.windows.net/$web/python/azure-servicebus/latest/azure.servicebus.html#azure.servicebus.TransportType) nebo [TransportType. AmqpOverWebSocket](https://azuresdkdocs.blob.core.windows.net/$web/python/azure-servicebus/latest/azure.servicebus.html#azure.servicebus.TransportType) |