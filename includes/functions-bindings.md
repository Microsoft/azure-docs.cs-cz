---
author: ggailey777
ms.service: azure-functions
ms.topic: include
ms.date: 09/04/2018
ms.author: glenga
ms.openlocfilehash: 0134d20f4b5d2816fa38bfb7377154463acd4781
ms.sourcegitcommit: 3877b77e7daae26a5b367a5097b19934eb136350
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/30/2019
ms.locfileid: "68669585"
---
Tato tabulka ukazuje vazby, které jsou podporovány ve dvou hlavních verzích modulu Azure Functions Runtime:

| type | verze | 2.x<sup>1</sup> | Trigger | Vstup | Výstup |
| ---- | :-: | :-: | :------: | :---: | :----: |
| [Blob Storage](../articles/azure-functions/functions-bindings-storage-blob.md)          |✔|✔|✔|✔|✔|
| [Databáze Cosmos](../articles/azure-functions/functions-bindings-documentdb.md)               |✔|✔|✔|✔|✔|
| [Event Grid](../articles/azure-functions/functions-bindings-event-grid.md)              |✔|✔|✔| | |
| [Event Hubs](../articles/azure-functions/functions-bindings-event-hubs.md)              |✔|✔|✔| |✔|
| [HTTP & Webhooky](../articles/azure-functions/functions-bindings-http-webhook.md)             |✔|✔|✔| |✔|
| [Microsoft Graph<br/>excelové tabulky](../articles/azure-functions/functions-bindings-microsoft-graph.md)   ||✔| |✔|✔|
| [Soubory<br/>Microsoft Graph OneDrive](../articles/azure-functions/functions-bindings-microsoft-graph.md) ||✔| |✔|✔|
| [Microsoft Graph<br/>outlookový e-mail](../articles/azure-functions/functions-bindings-microsoft-graph.md)  ||✔| | |✔|
| [Události<br/>Microsoft Graph](../articles/azure-functions/functions-bindings-microsoft-graph.md)         ||✔|✔|✔|✔|
| [Microsoft Graph<br/>ověřovacích tokenů](../articles/azure-functions/functions-bindings-microsoft-graph.md)    ||✔| |✔| |
| [Mobile Apps](../articles/azure-functions/functions-bindings-mobile-apps.md)             |✔| | |✔|✔|
| [Notification Hubs](../articles/azure-functions/functions-bindings-notification-hubs.md) |✔|| | |✔|
| [Queue Storage](../articles/azure-functions/functions-bindings-storage-queue.md)         |✔|✔|✔| |✔|
| [SendGrid](../articles/azure-functions/functions-bindings-sendgrid.md)                   |✔|✔| | |✔|
| [Service Bus](../articles/azure-functions/functions-bindings-service-bus.md)             |✔|✔|✔| |✔|
| [SignalR](../articles/azure-functions/functions-bindings-signalr-service.md)             | |✔| |✔|✔|
| [Table Storage](../articles/azure-functions/functions-bindings-storage-table.md)         |✔|✔| |✔|✔|
| [Timer](../articles/azure-functions/functions-bindings-timer.md)                         |✔|✔|✔| | |
| [Twilio](../articles/azure-functions/functions-bindings-twilio.md)                       |✔|✔| | |✔|

<sup>1</sup> v 2. x musí být registrovány všechny vazby kromě protokolu HTTP a časovače. Viz [Registrace rozšíření vazby](../articles/azure-functions/functions-bindings-register.md).
