---
title: Přehled rozhraní API pro Azure Event Hubs | Microsoft Docs
description: Tento článek poskytuje přehled dostupných rozhraní API (za běhu a správu) pro používání služby Azure Event Hubs.
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
ms.sourcegitcommit: 76bc196464334a99510e33d836669d95d7f57643
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/12/2020
ms.locfileid: "77162221"
---
# <a name="available-event-hubs-apis"></a>Dostupná Event Hubs rozhraní API

Tento článek popisuje sadu dostupných klientů rozhraní API, které můžete použít ke správě Event Hubsch prostředků.

## <a name="runtime-apis"></a>Běhová rozhraní API

V následující části jsou popsány všechny aktuálně dostupné klienty Azure Event Hubs runtime. I když některé z těchto knihoven obsahují také omezené funkce správy, jsou k dispozici také [konkrétní knihovny](#management-apis) vyhrazené pro operace správy. Hlavním cílem těchto knihoven je odesílat a přijímat zprávy z centra událostí.

Další informace o aktuálním stavu každé běhové knihovny najdete v části [Další informace](#additional-information).

| Jazyk/platforma | Balíček klienta | Balíček EventProcessorHost | Úložiště |
| --- | --- | --- | --- |
| .NET Standard | [NuGet](https://www.nuget.org/packages/Microsoft.Azure.EventHubs/) | [NuGet](https://www.nuget.org/packages/Microsoft.Azure.EventHubs.Processor/) | [GitHub](https://github.com/azure/azure-event-hubs-dotnet) |
| .NET Framework | [NuGet](https://www.nuget.org/packages/WindowsAzure.ServiceBus/) | [NuGet](https://www.nuget.org/packages/Microsoft.Azure.ServiceBus.EventProcessorHost/) | NEUŽÍVÁ SE. |
| Java | [Maven](https://search.maven.org/#search%7Cga%7C1%7Ca%3A%22azure-eventhubs%22) | [Maven](https://search.maven.org/#search%7Cga%7C1%7Ca%3A%22azure-eventhubs-eph%22) | [GitHub](https://github.com/Azure/azure-event-hubs-java) |
| Node | [NPM](https://www.npmjs.com/package/azure-event-hubs) | NEUŽÍVÁ SE. | [GitHub](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/eventhub/event-hubs) |
| C | NEUŽÍVÁ SE. | NEUŽÍVÁ SE. | [GitHub](https://github.com/Azure/azure-event-hubs-c) |

### <a name="additional-information"></a>Další informace

#### <a name="net"></a>.NET

Ekosystém .NET má několik modulů runtime, takže existuje více knihoven .NET pro Event Hubs. Knihovnu .NET Standard lze spustit buď pomocí .NET Core, nebo .NET Framework, zatímco knihovnu .NET Framework lze spustit pouze v .NET Framework prostředí. Další informace o .NET Framework verzích najdete v tématu [verze rozhraní](https://docs.microsoft.com/dotnet/articles/standard/frameworks).

#### <a name="node"></a>Node

[Knihovna JavaScriptu](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/eventhub/event-hubs) je aktuálně ve verzi Preview a je udržována jako vedlejší projekt od zaměstnanců společnosti Microsoft a externích přispěvatelů. Všechny příspěvky včetně zdrojového kódu jsou Welcome a budou přezkoumány.

## <a name="management-apis"></a>Rozhraní API pro správu

V následující tabulce jsou uvedeny všechny aktuálně dostupné knihovny specifické pro správu. Žádná z těchto knihoven neobsahuje běhové operace a slouží výhradně pro správu Event Hubsch entit.

| Jazyk/platforma | Balíček pro správu | Úložiště |
| --- | --- | --- |
| .NET Standard | [NuGet](https://www.nuget.org/packages/Microsoft.Azure.Management.EventHub) | [GitHub](https://github.com/Azure/azure-sdk-for-net/tree/AutoRest/src/ResourceManagement/EventHub) |

## <a name="next-steps"></a>Další kroky
Další informace o službě Event Hubs najdete na následujících odkazech:

* [Přehled služby Event Hubs](event-hubs-what-is-event-hubs.md)
* [Vytvoření centra událostí](event-hubs-create.md)
* [Nejčastější dotazy k Event Hubs](event-hubs-faq.md)
