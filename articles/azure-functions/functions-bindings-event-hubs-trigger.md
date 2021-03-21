---
title: Aktivační událost služby Azure Event Hubs pro Azure Functions
description: Naučte se používat aktivační událost Azure Event Hubs v Azure Functions.
author: craigshoemaker
ms.assetid: daf81798-7acc-419a-bc32-b5a41c6db56b
ms.topic: reference
ms.date: 02/21/2020
ms.author: cshoe
ms.openlocfilehash: c2b8302e64f7dcc657fd20ed5d918ed6816d750d
ms.sourcegitcommit: e6de1702d3958a3bea275645eb46e4f2e0f011af
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/20/2021
ms.locfileid: "102608903"
---
# <a name="azure-event-hubs-trigger-for-azure-functions"></a>Aktivační událost služby Azure Event Hubs pro Azure Functions

Tento článek vysvětluje, jak pracovat s triggerem [Event Hubs služby Azure](../event-hubs/event-hubs-about.md) pro Azure Functions. Azure Functions podporuje triggery a [výstupní vazby](functions-bindings-event-hubs-output.md) pro Event Hubs.

Informace o nastavení a podrobnostech o konfiguraci najdete v tématu [Přehled](functions-bindings-event-hubs.md).

[!INCLUDE [functions-bindings-event-hubs-trigger](../../includes/functions-bindings-event-hubs-trigger.md)]

## <a name="hostjson-settings"></a>host.jsnastavení

[host.jsv](functions-host-json.md#eventhub) souboru obsahuje nastavení, která řídí chování triggeru centra událostí. Podrobnosti o dostupných nastaveních najdete v části [host.jsv nastavení](functions-bindings-event-hubs.md#hostjson-settings) .

## <a name="next-steps"></a>Další kroky

- [Zápis událostí do datového proudu událostí (výstupní vazba)](./functions-bindings-event-hubs-output.md)
