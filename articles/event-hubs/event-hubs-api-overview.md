---
title: Přehled služby Azure Event Hubs API | Microsoft Docs
description: Přehled rozhraní API centra událostí Azure k dispozici
services: event-hubs
documentationcenter: na
author: sethmanheim
manager: timlt
editor: ''
ms.assetid: 3f221a0c-182d-4e39-9f3d-3a3c16c5c6ed
ms.service: event-hubs
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/28/2017
ms.author: sethm
ms.openlocfilehash: abd44fd0c9cbfab2365b1552e3cd90e84a5348d7
ms.sourcegitcommit: 651a6fa44431814a42407ef0df49ca0159db5b02
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/28/2017
ms.locfileid: "25957523"
---
# <a name="available-event-hubs-apis"></a>Rozhraní API k dispozici události rozbočovače

Tento článek popisuje sadu k dispozici rozhraní API klientů, které můžete použít pro správu prostředků služby Event Hubs.

## <a name="runtime-apis"></a>Modul runtime rozhraní API

Následuje popis všech klientů aktuálně k dispozici modul runtime Azure Event Hubs. I když některé z těchto knihoven, také mají omezenou správu funkci, existují také [specifické knihovny](#management-apis) vyhrazený pro operace správy. Základní fokus tyto knihovny je odesílat a přijímat zprávy z centra událostí.

V tématu [Další informace o](#additional-information) další podrobnosti o aktuální stav jednotlivých modulu runtime knihoven.

| Jazyk nebo platformu | Balíček klienta | Balíček EventProcessorHost | Úložiště |
| --- | --- | --- | --- |
| Standardní rozhraní .NET | [NuGet](https://www.nuget.org/packages/Microsoft.Azure.EventHubs/) | [NuGet](https://www.nuget.org/packages/Microsoft.Azure.EventHubs.Processor/) | [GitHub](https://github.com/azure/azure-event-hubs-dotnet) |
| Rozhraní .NET framework | [NuGet](https://www.nuget.org/packages/WindowsAzure.ServiceBus/) | [NuGet](https://www.nuget.org/packages/Microsoft.Azure.ServiceBus.EventProcessorHost/) | Není k dispozici |
| Java | [Maven](https://search.maven.org/#search%7Cga%7C1%7Ca%3A%22azure-eventhubs%22) | [Maven](https://search.maven.org/#search%7Cga%7C1%7Ca%3A%22azure-eventhubs-eph%22) | [GitHub](https://github.com/Azure/azure-event-hubs-java) |
| Node | [NPM](https://www.npmjs.com/package/azure-event-hubs) | Není k dispozici | [GitHub](https://github.com/Azure/azure-event-hubs-node) |
| C | Není k dispozici | Není k dispozici | [GitHub](https://github.com/Azure/azure-event-hubs-c) |

### <a name="additional-information"></a>Další informace

#### <a name="net"></a>.NET
Ekosystému .NET má více moduly runtime a proto nejsou více knihovny .NET pro centra událostí. Knihovně .NET Standard můžete spustit pomocí .NET Core nebo rozhraní .NET Framework, když rozhraní .NET Framework – knihovna může spustit pouze v prostředí .NET Framework. Další informace o rozhraní .NET Framework naleznete v tématu [framework verze](https://docs.microsoft.com/dotnet/articles/standard/frameworks#framework-versions).

#### <a name="node"></a>Node

Knihovna Node.js je aktuálně ve verzi preview a je udržován jako straně projekt tak, že zaměstnanci společnosti Microsoft a externí přispěvatele. Všechny příspěvky včetně zdrojového kódu se úvodní a bude znovu.

## <a name="management-apis"></a>Rozhraní API pro správu

Následuje seznam všech aktuálně k dispozici správa specifické knihovny. Žádná z těchto knihoven obsahovat operace runtime a jsou pouze za účelem správy služby Event Hubs entity.

| Jazyk nebo platformu | Balíček pro správu | Úložiště |
| --- | --- | --- | --- |
| Standardní rozhraní .NET | [NuGet](https://www.nuget.org/packages/Microsoft.Azure.Management.EventHub) | [GitHub](https://github.com/Azure/azure-sdk-for-net/tree/AutoRest/src/ResourceManagement/EventHub) |

## <a name="next-steps"></a>Další kroky
Další informace o službě Event Hubs najdete na následujících odkazech:

* [Přehled služby Event Hubs](event-hubs-what-is-event-hubs.md)
* [Vytvoření centra událostí](event-hubs-create.md)
* [Nejčastější dotazy k Event Hubs](event-hubs-faq.md)