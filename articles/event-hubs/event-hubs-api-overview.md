---
title: Přehled rozhraní API centra událostí Azure | Dokumenty společnosti Microsoft
description: Tento článek obsahuje přehled dostupných rozhraní API (runtime a správa) pro použití služby Azure Event Hubs.
services: event-hubs
documentationcenter: na
author: ShubhaVijayasarathy
manager: timlt
editor: ''
ms.assetid: 3f221a0c-182d-4e39-9f3d-3a3c16c5c6ed
ms.service: event-hubs
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 07/02/2018
ms.author: shvija
ms.openlocfilehash: 08e10996f633d35ffbf946b61937a0e9a76f7227
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "77162221"
---
# <a name="available-event-hubs-apis"></a>Rozhraní API centra událostí k dispozici

Tento článek popisuje sadu dostupných klientů rozhraní API, které můžete použít pro správu prostředků centra událostí.

## <a name="runtime-apis"></a>Runtime API

Následující část popisuje všechny aktuálně dostupné runtime klienty centra Azure Event Hubs. Zatímco některé z těchto knihoven také obsahují omezené funkce správy, existují také [specifické knihovny](#management-apis) vyhrazené pro operace správy. Hlavním cílem těchto knihoven je odesílání a přijímání zpráv z centra událostí.

Další informace o aktuálním stavu jednotlivých runtime knihovny naleznete [v dalších informacích](#additional-information).

| Jazyk/platforma | Klientský balíček | Balíček EventProcessorHost | Repository |
| --- | --- | --- | --- |
| .NET Standard | [NuGet](https://www.nuget.org/packages/Microsoft.Azure.EventHubs/) | [NuGet](https://www.nuget.org/packages/Microsoft.Azure.EventHubs.Processor/) | [GitHub](https://github.com/azure/azure-event-hubs-dotnet) |
| .NET Framework | [NuGet](https://www.nuget.org/packages/WindowsAzure.ServiceBus/) | [NuGet](https://www.nuget.org/packages/Microsoft.Azure.ServiceBus.EventProcessorHost/) | Není dostupné. |
| Java | [Maven](https://search.maven.org/#search%7Cga%7C1%7Ca%3A%22azure-eventhubs%22) | [Maven](https://search.maven.org/#search%7Cga%7C1%7Ca%3A%22azure-eventhubs-eph%22) | [GitHub](https://github.com/Azure/azure-event-hubs-java) |
| Node | [NPM](https://www.npmjs.com/package/azure-event-hubs) | Není dostupné. | [GitHub](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/eventhub/event-hubs) |
| C | Není dostupné. | Není dostupné. | [GitHub](https://github.com/Azure/azure-event-hubs-c) |

### <a name="additional-information"></a>Další informace

#### <a name="net"></a>.NET

Ekosystém .NET má více runtimes, takže existuje více knihoven .NET pro event huby. Knihovnu .NET Standard lze spustit pomocí rozhraní .NET Core nebo rozhraní .NET Framework, zatímco knihovnu rozhraní .NET Framework lze spustit pouze v prostředí rozhraní .NET Framework. Další informace o verzích rozhraní .NET Framework naleznete v [tématu Framework versions](https://docs.microsoft.com/dotnet/articles/standard/frameworks).

#### <a name="node"></a>Node

[Knihovna JavaScriptu](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/eventhub/event-hubs) je v současné době ve verzi preview a je udržována jako vedlejší projekt zaměstnanci společnosti Microsoft a externími přispěvateli. Všechny příspěvky včetně zdrojového kódu jsou vítány a budou přezkoumány.

## <a name="management-apis"></a>Rozhraní API pro správu

V následující tabulce jsou uvedeny všechny aktuálně dostupné knihovny specifické pro správu. Žádná z těchto knihoven neobsahuje operace runtime a je výhradně pro účely správy entit Event Hubs.

| Jazyk/platforma | Balíček pro správu | Repository |
| --- | --- | --- |
| .NET Standard | [NuGet](https://www.nuget.org/packages/Microsoft.Azure.Management.EventHub) | [GitHub](https://github.com/Azure/azure-sdk-for-net/tree/AutoRest/src/ResourceManagement/EventHub) |

## <a name="next-steps"></a>Další kroky
Další informace o službě Event Hubs najdete na následujících odkazech:

* [Přehled centra událostí](event-hubs-what-is-event-hubs.md)
* [Vytvoření centra událostí](event-hubs-create.md)
* [Nejčastější dotazy k Event Hubs](event-hubs-faq.md)
