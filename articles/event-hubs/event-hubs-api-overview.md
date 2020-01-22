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
ms.openlocfilehash: c852bdeb30efe6acf626ae67028ec1ccb9e0b6db
ms.sourcegitcommit: a9b1f7d5111cb07e3462973eb607ff1e512bc407
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/22/2020
ms.locfileid: "76310964"
---
# <a name="available-event-hubs-apis"></a>Dostupná Event Hubs rozhraní API

Tento článek popisuje sadu dostupných klientů rozhraní API, které můžete použít ke správě Event Hubsch prostředků.

## <a name="runtime-apis"></a>Běhová rozhraní API

V následující části jsou popsány všechny aktuálně dostupné klienty Azure Event Hubs runtime. I když některé z těchto knihoven obsahují také omezené funkce správy, jsou k dispozici také [konkrétní knihovny](#management-apis) vyhrazené pro operace správy. Hlavním cílem těchto knihoven je odesílat a přijímat zprávy z centra událostí.

Další informace o aktuálním stavu každé běhové knihovny najdete v části [Další informace](#additional-information).

| Jazyk/platforma | Klientský balíček | Balíček EventProcessorHost | Úložiště |
| --- | --- | --- | --- |
| .NET Standard | [NuGet](https://www.nuget.org/packages/Microsoft.Azure.EventHubs/) | [NuGet](https://www.nuget.org/packages/Microsoft.Azure.EventHubs.Processor/) | [GitHub](https://github.com/azure/azure-event-hubs-dotnet) |
| .NET Framework | [NuGet](https://www.nuget.org/packages/WindowsAzure.ServiceBus/) | [NuGet](https://www.nuget.org/packages/Microsoft.Azure.ServiceBus.EventProcessorHost/) | Nevztahuje se |
| Java | [Maven](https://search.maven.org/#search%7Cga%7C1%7Ca%3A%22azure-eventhubs%22) | [Maven](https://search.maven.org/#search%7Cga%7C1%7Ca%3A%22azure-eventhubs-eph%22) | [GitHub](https://github.com/Azure/azure-event-hubs-java) |
| Uzel | [NPM](https://www.npmjs.com/package/azure-event-hubs) | Nevztahuje se | [GitHub](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/eventhub/event-hubs) |
| C | Nevztahuje se | Nevztahuje se | [GitHub](https://github.com/Azure/azure-event-hubs-c) |

### <a name="additional-information"></a>Další informace

#### <a name="net"></a>.NET

Ekosystém .NET má několik modulů runtime, takže existuje více knihoven .NET pro Event Hubs. Knihovnu .NET Standard lze spustit buď pomocí .NET Core, nebo .NET Framework, zatímco knihovnu .NET Framework lze spustit pouze v .NET Framework prostředí. Další informace o .NET Framework verzích najdete v tématu [verze rozhraní](https://docs.microsoft.com/dotnet/articles/standard/frameworks).

#### <a name="node"></a>Uzel

[Knihovna Node. js](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/eventhub/event-hubs) je aktuálně ve verzi Preview a je udržována jako vedlejší projekt od zaměstnanců společnosti Microsoft a externích přispěvatelů. Všechny příspěvky včetně zdrojového kódu jsou Welcome a budou přezkoumány.

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
