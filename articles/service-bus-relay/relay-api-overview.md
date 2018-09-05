---
title: Přehled služby Azure Relay API | Dokumentace Microsoftu
description: Přehled dostupných rozhraní API Azure Relay
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
ms.date: 05/02/2018
ms.author: spelluru
ms.openlocfilehash: 2c7aec700a1077bc1a1b56afb5d8d07f47f4c6e8
ms.sourcegitcommit: cb61439cf0ae2a3f4b07a98da4df258bfb479845
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/05/2018
ms.locfileid: "43700368"
---
# <a name="available-relay-apis"></a>K dispozici předávání přes rozhraní API

## <a name="runtime-apis"></a>Rozhraní API modulu runtime

Následující tabulka uvádí všechny aktuálně dostupné klienty modulu runtime Relay.

[Další informace o](#additional-information) oddíl obsahuje další informace o stavu jednotlivých knihoven modulu runtime.

| Jazyk nebo platformu | K dispozici funkce | Balíček klienta | Úložiště |
| --- | --- | --- | --- |
| .NET Standard | Hybridní připojení | [Microsoft.Azure.Relay](https://www.nuget.org/packages/Microsoft.Azure.Relay/) | [GitHub](https://github.com/azure/azure-relay-dotnet) |
| .NET Framework | WCF Relay | [WindowsAzure.ServiceBus](https://www.nuget.org/packages/WindowsAzure.ServiceBus/) | neuvedeno |
| Node | Hybridní připojení | [Protokoly Websocket: `hyco-ws`](https://www.npmjs.com/package/hyco-ws)<br/>[Protokoly Websocket: `hyco-websocket`](https://www.npmjs.com/package/hyco-websocket)<br/>[Požadavky HTTP: `hyco-https`](https://www.npmjs.com/package/hyco-https) | [GitHub](https://github.com/Azure/azure-relay-node) |

### <a name="additional-information"></a>Další informace

#### <a name="net"></a>.NET

Ekosystému .NET má více modulů runtime, proto jsou více knihovny .NET pro přenos. Knihovny .NET Standard lze spouštět s využitím .NET Core nebo .NET Framework, zatímco knihovně rozhraní .NET Framework lze spustit pouze v prostředí .NET Framework. Další informace o rozhraní .NET Framework najdete v tématu [verze rozhraní framework](/dotnet/articles/standard/frameworks#framework-versions).

Knihovně rozhraní .NET Framework pouze podporuje programovacího modelu WCF a využívá proprietární binární protokol založený na WCF `net.tcp` přenosu. Tento protokol a knihovny je zachován z důvodu zpětné kompatibility se stávajícími aplikacemi.

Knihovny .NET Standard vychází z definice otevřený protokol pro Relay hybridní připojení, která je založena na protokolu HTTP a Websocket. Knihovny podporuje datový proud abstrakce přes Websockets a gesto jednoduchého typu žádost odpověď rozhraní API pro volaný požadavky HTTP. [Webového rozhraní API](https://github.com/Azure/azure-relay-dotnet) příklad ukazuje, jak integrovat Hybrid Connections pro webové služby pomocí ASP.NET Core.

#### <a name="nodejs"></a>Node.js

Hybridní připojení modulů uvedených v předchozí tabulce nahradit nebo změnit stávající modulů Node.js s alternativní implementace, které naslouchat na službě Azure Relay místo místního síťového zásobníku.

`hyco-https` Modulu mění a částečně přepíše modulů Node.js core `http` a `https`, poskytuje implementace naslouchací proces HTTPS, která je kompatibilní s mnoha stávající moduly Node.js a aplikace, které spoléhají na tyto základní moduly.

`hyco-ws` a `hyco-websocket` změnit oblíbené moduly `ws` a `websocket` moduly pro Node.js poskytuje alternativní naslouchací proces implementace, které umožňuje moduly i aplikací spoléhá na buď modulu práce za hybridního Připojení přenosu.

Podrobnosti o těchto modulů najdete v [uzel azure relay](https://github.com/Azure/azure-relay-node) úložiště GitHub.

## <a name="next-steps"></a>Další postup

Další informace o Azure Relay, najdete pomocí těchto odkazů:
* [Co je Azure Relay?](relay-what-is-it.md)
* [Přenos – nejčastější dotazy](relay-faq.md)