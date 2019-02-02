---
title: Přehled služby Azure Event Hubs API | Dokumentace Microsoftu
description: Přehled dostupných Azure Event Hubs rozhraní API
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
ms.openlocfilehash: 432f2495dd1b4c8aeb761d12904adf26f3c24c0b
ms.sourcegitcommit: de32e8825542b91f02da9e5d899d29bcc2c37f28
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/02/2019
ms.locfileid: "55660693"
---
# <a name="available-event-hubs-apis"></a>Rozhraní API k dispozici události rozbočovače

Tento článek popisuje sadu dostupných klientů rozhraní API, které používáte pro správu prostředků služby Event Hubs.

## <a name="runtime-apis"></a>Rozhraní API modulu runtime

Následující část popisuje všechny aktuálně dostupné klienty modulu runtime služby Azure Event Hubs. Zatímco některé z těchto knihoven také zahrnují funkce omezenou správu, k dispozici jsou také [specifické knihovny](#management-apis) vyhrazený pro operace správy. Základní sada je zaměřena tak tyto knihovny k odesílání a příjem zpráv z centra událostí.

Další informace o aktuálním stavu jednotlivých knihoven runtime naleznete v tématu [Další informace o](#additional-information).

| Jazyk nebo platformu | Balíček klienta | EventProcessorHost balíčku | Úložiště |
| --- | --- | --- | --- |
| .NET Standard | [NuGet](https://www.nuget.org/packages/Microsoft.Azure.EventHubs/) | [NuGet](https://www.nuget.org/packages/Microsoft.Azure.EventHubs.Processor/) | [GitHub](https://github.com/azure/azure-event-hubs-dotnet) |
| .NET Framework | [NuGet](https://www.nuget.org/packages/WindowsAzure.ServiceBus/) | [NuGet](https://www.nuget.org/packages/Microsoft.Azure.ServiceBus.EventProcessorHost/) | neuvedeno |
| Java | [Maven](https://search.maven.org/#search%7Cga%7C1%7Ca%3A%22azure-eventhubs%22) | [Maven](https://search.maven.org/#search%7Cga%7C1%7Ca%3A%22azure-eventhubs-eph%22) | [GitHub](https://github.com/Azure/azure-event-hubs-java) |
| Node | [NPM](https://www.npmjs.com/package/azure-event-hubs) | neuvedeno | [GitHub](https://github.com/Azure/azure-event-hubs-node) |
| C | neuvedeno | neuvedeno | [GitHub](https://github.com/Azure/azure-event-hubs-c) |

### <a name="additional-information"></a>Další informace

#### <a name="net"></a>.NET

Ekosystému .NET má různými moduly runtime, aby vás nečekala více knihovny .NET pro službu Event Hubs. Knihovny .NET Standard lze spouštět s využitím .NET Core nebo .NET Framework, zatímco knihovně rozhraní .NET Framework lze spustit pouze v prostředí .NET Framework. Další informace o verzích rozhraní .NET Framework najdete v tématu [verze rozhraní framework](https://docs.microsoft.com/dotnet/articles/standard/frameworks).

#### <a name="node"></a>Node

[Knihovna Node.js](https://github.com/Azure/azure-event-hubs-node) je aktuálně ve verzi preview a udržuje jako projekt na straně zaměstnanci Microsoftu a externí přispěvatele. Všechny příspěvky, včetně zdrojový kód se úvodní a bude posouzen.

## <a name="management-apis"></a>Rozhraní API pro správu

V následující tabulce jsou uvedeny všechny aktuálně dostupné knihovny specifické pro správu. Žádná z těchto knihoven obsahovat operace modulu runtime a jsou pouze za účelem správy entit služby Event Hubs.

| Jazyk nebo platformu | Sady Management package | Úložiště |
| --- | --- | --- | --- |
| .NET Standard | [NuGet](https://www.nuget.org/packages/Microsoft.Azure.Management.EventHub) | [GitHub](https://github.com/Azure/azure-sdk-for-net/tree/AutoRest/src/ResourceManagement/EventHub) |

## <a name="next-steps"></a>Další postup
Další informace o službě Event Hubs najdete na následujících odkazech:

* [Přehled služby Event Hubs](event-hubs-what-is-event-hubs.md)
* [Vytvoření centra událostí](event-hubs-create.md)
* [Nejčastější dotazy k Event Hubs](event-hubs-faq.md)
