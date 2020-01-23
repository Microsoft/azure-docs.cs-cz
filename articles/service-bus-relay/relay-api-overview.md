---
title: Přehled rozhraní API pro Azure Relay | Microsoft Docs
description: Tento článek poskytuje přehled dostupných Azure Relay rozhraní API (.NET Standard, .NET Framework, Node. js atd.).
services: event-hubs
documentationcenter: na
author: spelluru
manager: timlt
editor: ''
ms.assetid: fdaa1d2b-bd80-4e75-abb9-0c3d0773af2d
ms.service: service-bus-relay
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 01/21/2020
ms.author: spelluru
ms.openlocfilehash: 41d9e2026c19c959dc6fe2546b0ef699571ec7cd
ms.sourcegitcommit: 38b11501526a7997cfe1c7980d57e772b1f3169b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/22/2020
ms.locfileid: "76513962"
---
# <a name="available-relay-apis"></a>Dostupná předávací rozhraní API

## <a name="runtime-apis"></a>Běhová rozhraní API

V následující tabulce jsou uvedeny všechny aktuálně dostupné klienty modulu runtime přenosu.

V části [Další informace](#additional-information) najdete další informace o stavu každé běhové knihovny.

| Jazyk/platforma | Dostupná funkce | Klientský balíček | Úložiště |
| --- | --- | --- | --- |
| .NET Standard | Hybridní připojení | [Microsoft.Azure.Relay](https://www.nuget.org/packages/Microsoft.Azure.Relay/) | [GitHub](https://github.com/azure/azure-relay-dotnet) |
| .NET Framework | WCF Relay | [WindowsAzure.ServiceBus](https://www.nuget.org/packages/WindowsAzure.ServiceBus/) | Nevztahuje se |
| Uzel | Hybridní připojení | [WebSockets: `hyco-ws`](https://www.npmjs.com/package/hyco-ws)<br/>[WebSockets: `hyco-websocket`](https://www.npmjs.com/package/hyco-websocket)<br/>[Požadavky HTTP: `hyco-https`](https://www.npmjs.com/package/hyco-https) | [GitHub](https://github.com/Azure/azure-relay-node) |

### <a name="additional-information"></a>Další informace

#### <a name="net"></a>.NET

Ekosystém .NET má několik modulů runtime, proto existuje více knihoven .NET pro přenos. Knihovnu .NET Standard lze spustit buď pomocí .NET Core, nebo .NET Framework, zatímco knihovnu .NET Framework lze spustit pouze v .NET Framework prostředí. Další informace o rozhraní .NET Framework naleznete v tématu [verze rozhraní](/dotnet/articles/standard/frameworks).

Knihovna .NET Framework podporuje pouze programovací model WCF a spoléhá na proprietární binární protokol založený na přenosu `net.tcp` WCF. Tento protokol a knihovna se uchovávají kvůli zpětné kompatibilitě se stávajícími aplikacemi.

Knihovna .NET Standard je založena na definici otevřeného protokolu pro Hybrid Connections Relay, která je založena na protokolech HTTP a WebSockets. Knihovna podporuje abstrakci streamování přes objekty WebSockets a jednoduché gesto rozhraní API požadavku a odpovědi pro zodpovězení požadavků HTTP. Ukázka [webového rozhraní API](https://github.com/Azure/azure-relay-dotnet) ukazuje, jak integrovat Hybrid Connections s ASP.NET Core pro webové služby.

#### <a name="nodejs"></a>Node.js

Moduly Hybrid Connections uvedené v tabulce výše nahrazují nebo mění existující moduly node. js s alternativními implementacemi, které naslouchají na službě Azure Relay místo do zásobníku místní sítě.

Modul `hyco-https` mění a částečně Přepisuje základní moduly node. js `http` a `https`a poskytuje implementaci naslouchacího procesu HTTPS, která je kompatibilní s mnoha existujícími moduly a aplikacemi Node. js, které jsou závislé na těchto základních modulech.

Moduly `hyco-ws` a `hyco-websocket` mění oblíbené `ws` a `websocket` moduly pro Node. js, což poskytuje alternativní implementace naslouchacího procesu, které umožňují modulům a aplikacím, aby fungovaly na základě obou modulů pro práci za Hybrid Connections Relay.

Podrobnosti o těchto modulech najdete v úložišti GitHub [Azure-Relay-Node](https://github.com/Azure/azure-relay-node) .

## <a name="next-steps"></a>Další kroky

Další informace o Azure Relay najdete na těchto odkazech:
* [Co je Azure Relay?](relay-what-is-it.md)
* [Přenos – nejčastější dotazy](relay-faq.md)
