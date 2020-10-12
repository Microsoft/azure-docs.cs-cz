---
title: Azure Event Hubs – klientské sady SDK | Microsoft Docs
description: Tento článek poskytuje informace o klientských sadách SDK pro Azure Event Hubs.
ms.topic: article
ms.date: 06/23/2020
ms.openlocfilehash: 9f4eec603245f1e4ea6fa4d97b843ca6a770f2ad
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "88930872"
---
# <a name="azure-event-hubs---client-sdks"></a>Azure Event Hubs – klientské sady SDK
Tento článek poskytuje následující informace pro sady SDK podporované službou Azure Event Hubs: 

- Umístění balíčku, který můžete použít ve svých aplikacích 
- Umístění GitHubu, kde můžete najít zdrojový kód, ukázky, soubory Readme, protokol změn, nahlášené problémy a také vyvolat nové problémy 
- Odkazy na kurzy rychlý Start 

## <a name="client-sdks"></a>Klientské sady SDK
Následující tabulka popisuje všechny aktuálně dostupné klienty Azure Event Hubs runtime. I když některé z těchto knihoven obsahují také omezené funkce správy, jsou k dispozici také konkrétní knihovny vyhrazené pro operace správy. Hlavním cílem těchto knihoven je **odesílat a přijímat zprávy** z centra událostí.

| Jazyk | Balíček | Referenční informace | 
| -------- | ------- | --------------- | 
| . NET Standard (**nejnovější** a podporuje rozhraní .NET Core a .NET Framework) | [Azure. Messaging. EventHubs](https://www.nuget.org/packages/Azure.Messaging.EventHubs/) |<ul><li>[Umístění GitHubu](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/eventhub/Azure.Messaging.EventHubs)</li><li>[Kurz](event-hubs-dotnet-standard-getstarted-send.md)</li></ul> |
|       | [Azure. Messaging. EventHubs. procesor](https://www.nuget.org/packages/Azure.Messaging.EventHubs.Processor/) | <ul><li>[Umístění GitHubu](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/eventhub/Azure.Messaging.EventHubs.Processor)</li><li>[Kurz](event-hubs-dotnet-standard-getstarted-send.md)</li></ul> |
| . NET Standard (**starší verze** a podporuje .NET Core a .NET Framework) | [Microsoft.Azure.EventHubs](https://www.nuget.org/packages/Microsoft.Azure.EventHubs/) | <ul><li>[Umístění GitHubu](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/eventhub/Microsoft.Azure.EventHubs)</li><li>[Kurz](event-hubs-dotnet-standard-getstarted-send.md)</li></ul> | 
|       | [Microsoft.Azure.EventHubs.Processor](https://www.nuget.org/packages/Microsoft.Azure.EventHubs.Processor) | <ul><li>[Umístění GitHubu](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/eventhub/Microsoft.Azure.EventHubs.Processor)</li><li>[Kurz](event-hubs-dotnet-standard-getstarted-send.md)</li></ul> |
| . .NET Framework (**starý**) | [WindowsAzure.Messaging](https://www.nuget.org/packages/WindowsAzure.ServiceBus/) |<ul><li>[Kurz](event-hubs-dotnet-framework-getstarted-send.md)</li></ul> |
| Java | [Azure-zasílání zpráv – eventhubs](https://search.maven.org/search?q=a:azure-messaging-eventhubs) | <ul><li>[Umístění GitHubu](https://github.com/Azure/azure-sdk-for-java/tree/master/sdk/eventhubs/azure-messaging-eventhubs)</li><li>[Kurz](event-hubs-java-get-started-send.md)</li></ul> |
|      | [Azure – eventhubs](https://search.maven.org/search?q=a:azure-eventhubs) **(starší verze)** | <ul><li>[Umístění GitHubu](https://github.com/Azure/azure-sdk-for-java/tree/master/sdk/eventhubs/microsoft-azure-eventhubs)</li><li>[Kurz](event-hubs-java-get-started-send.md)</li></ul> |
| Python |  [Azure – eventhub](https://pypi.org/project/azure-eventhub/) | <ul><li>[Umístění GitHubu](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/eventhub/azure-eventhub)</li><li>[Kurz](event-hubs-python-get-started-send.md)</li></ul> |
|        | [Azure – eventhub – checkpointstoreblob – AIO](https://pypi.org/project/azure-eventhub-checkpointstoreblob-aio/) | <ul><li>[Umístění GitHubu](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/eventhub/azure-eventhub-checkpointstoreblob-aio)</li><li>[Kurz](event-hubs-python-get-started-send.md)</li></ul> |
| JavaScript | [Azure/Event – centra](https://www.npmjs.com/package/@azure/event-hubs) | <ul><li>[Umístění GitHubu](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/eventhub/event-hubs)</li><li>[Kurz](event-hubs-node-get-started-send.md)</li></ul> |
|            | [Azure/eventhubs-checkpointstore-BLOB](https://www.npmjs.com/package/@azure/eventhubs-checkpointstore-blob) | <ul><li>[Umístění GitHubu](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/eventhub/eventhubs-checkpointstore-blob)</li><li>[Kurz](event-hubs-node-get-started-send.md)</li></ul> |
| Go | [Azure – Event – centra – přejít](https://github.com/Azure/azure-event-hubs-go) | <ul><li>[Umístění GitHubu](https://github.com/Azure/azure-event-hubs-go)</li><li>[Kurz](event-hubs-go-get-started-send.md)</li></ul> |
| C | [Azure-Event-Centers-c](https://github.com/Azure/azure-event-hubs-c) | <ul><li>[Umístění GitHubu](https://github.com/Azure/azure-event-hubs-c)</li><li>[Kurz](event-hubs-c-getstarted-send.md)</li></ul> |

## <a name="management-sdks"></a>Sady Management SDK
V následující tabulce jsou uvedeny všechny aktuálně dostupné knihovny specifické pro správu. Žádná z těchto knihoven neobsahuje běhové operace a slouží výhradně pro **správu Event Hubsch entit**.

| Jazyk | Balíček | Referenční informace | 
| -------- | ------- | --------------- | 
| .NET Standard | [Microsoft.Azure.Management.EventHub](https://www.nuget.org/packages/Microsoft.Azure.Management.EventHub) |<ul><li>[Umístění GitHubu](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/eventhub/Microsoft.Azure.Management.EventHub)</li><li>[Kurz](event-hubs-dotnet-standard-getstarted-send.md)</li></ul> |


## <a name="next-steps"></a>Další kroky

Další informace o službě Event Hubs najdete na následujících odkazech:

* [Přehled Event Hubs](./event-hubs-about.md)
* [Vytvoření centra událostí](event-hubs-create.md)
* [Nejčastější dotazy k Event Hubs](event-hubs-faq.md)
