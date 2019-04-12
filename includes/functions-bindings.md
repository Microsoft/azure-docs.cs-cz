---
author: ggailey777
ms.service: azure-functions
ms.topic: include
ms.date: 09/04/2018
ms.author: glenga
ms.openlocfilehash: 957009ec9833df2dd0f89c3745c7f3df5a3705b4
ms.sourcegitcommit: a60a55278f645f5d6cda95bcf9895441ade04629
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/03/2019
ms.locfileid: "59520533"
---
V následující tabulce jsou uvedeny vazby, které jsou podporovány ve dvou hlavních verzí modulu runtime Azure Functions.

| Type | 1.x | 2.x<sup>1</sup> | Trigger | Vstup | Výstup |
| ---- | :-: | :-: | :------: | :---: | :----: |
| [Blob Storage](../articles/azure-functions/functions-bindings-storage-blob.md)          |✔|✔|✔|✔|✔|
| [Cosmos DB](../articles/azure-functions/functions-bindings-documentdb.md)               |✔|✔|✔|✔|✔|
| [Event Grid](../articles/azure-functions/functions-bindings-event-grid.md)              |✔|✔|✔| | |
| [Event Hubs](../articles/azure-functions/functions-bindings-event-hubs.md)              |✔|✔|✔| |✔|
| [HTTP a Webhooky](../articles/azure-functions/functions-bindings-http-webhook.md)             |✔|✔|✔| |✔|
| [Microsoft Graph<br/>Excelové tabulky](../articles/azure-functions/functions-bindings-microsoft-graph.md)   ||✔| |✔|✔|
| [Microsoft Graph<br/>Soubory na OneDrive](../articles/azure-functions/functions-bindings-microsoft-graph.md) ||✔| |✔|✔|
| [Microsoft Graph<br/>Outlookový e-mail](../articles/azure-functions/functions-bindings-microsoft-graph.md)  ||✔| | |✔|
| [Microsoft Graph<br/>Události](../articles/azure-functions/functions-bindings-microsoft-graph.md)         ||✔|✔|✔|✔|
| [Microsoft Graph<br/>Ověřovacích tokenů](../articles/azure-functions/functions-bindings-microsoft-graph.md)    ||✔| |✔| |
| [Mobile Apps](../articles/azure-functions/functions-bindings-mobile-apps.md)             |✔| | |✔|✔|
| [Notification Hubs](../articles/azure-functions/functions-bindings-notification-hubs.md) |✔|| | |✔|
| [Queue Storage](../articles/azure-functions/functions-bindings-storage-queue.md)         |✔|✔|✔| |✔|
| [SendGrid](../articles/azure-functions/functions-bindings-sendgrid.md)                   |✔|✔| | |✔|
| [Service Bus](../articles/azure-functions/functions-bindings-service-bus.md)             |✔|✔|✔| |✔|
| [SignalR](../articles/azure-functions/functions-bindings-signalr-service.md)             | |✔| |✔|✔|
| [Úložiště Table](../articles/azure-functions/functions-bindings-storage-table.md)         |✔|✔| |✔|✔|
| [Časovač](../articles/azure-functions/functions-bindings-timer.md)                         |✔|✔|✔| | |
| [Twilio](../articles/azure-functions/functions-bindings-twilio.md)                       |✔|✔| | |✔|

<sup>1</sup> v 2.x, musí být zaregistrovaný všechny vazby s výjimkou HTTP a časovač. Zobrazit [registraci rozšíření vazby](../articles/azure-functions/functions-bindings-register.md).
