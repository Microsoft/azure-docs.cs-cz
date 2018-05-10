---
title: Přehled služby Azure API předávání | Microsoft Docs
description: Přehled dostupných rozhraní API předávání přes Azure
services: event-hubs
documentationcenter: na
author: sethmanheim
manager: timlt
editor: ''
ms.assetid: fdaa1d2b-bd80-4e75-abb9-0c3d0773af2d
ms.service: service-bus-relay
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 05/02/2018
ms.author: sethm
ms.openlocfilehash: 00496ca6c0138a840322c053d7d20944df228e9f
ms.sourcegitcommit: 870d372785ffa8ca46346f4dfe215f245931dae1
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/08/2018
---
# <a name="available-relay-apis"></a>K dispozici předávání přes rozhraní API

## <a name="runtime-apis"></a>Modul runtime rozhraní API

Následující tabulka uvádí všechny klienty aktuálně k dispozici modul runtime předávání.

[Další informace o](#additional-information) část obsahuje informace o stavu jednotlivých modulu runtime knihoven.

| Jazyk nebo platformu | Dostupné funkce | Balíček klienta | Úložiště |
| --- | --- | --- | --- |
| Standardní rozhraní .NET | Hybridní připojení | [Microsoft.Azure.Relay](https://www.nuget.org/packages/Microsoft.Azure.Relay/) | [GitHub](https://github.com/azure/azure-relay-dotnet) |
| .NET Framework | WCF Relay | [WindowsAzure.ServiceBus](https://www.nuget.org/packages/WindowsAzure.ServiceBus/) | neuvedeno |
| Node | Hybridní připojení | [Technologie Websockets: `hyco-ws`](https://www.npmjs.com/package/hyco-ws)<br/>[Technologie Websockets: `hyco-websocket`](https://www.npmjs.com/package/hyco-websocket)<br/>[Požadavky HTTP: `hyco-https`](https://www.npmjs.com/package/hyco-https) | [GitHub](https://github.com/Azure/azure-relay-node) |

### <a name="additional-information"></a>Další informace

#### <a name="net"></a>.NET

Ekosystému .NET má více moduly runtime a proto nejsou více knihovny .NET pro předávací službu. Knihovně .NET Standard můžete spustit pomocí .NET Core nebo rozhraní .NET Framework, když rozhraní .NET Framework – knihovna může spustit pouze v prostředí .NET Framework. Další informace o rozhraní .NET Framework naleznete v tématu [framework verze](/dotnet/articles/standard/frameworks#framework-versions).

Knihovně rozhraní .NET Framework pouze podporuje programovací model WCF a spoléhá na vlastní binární protokol založený na WCF `net.tcp` přenosu. Tento protokol a knihovny bude zachována pro účely zpětné kompatibilitě se stávajícími aplikacemi.

Standardní .NET knihovna je založený na definici otevřený protokol pro předávání hybridní připojení, který je založený na protokolu HTTP a WebSockets. Knihovny podporuje datový proud abstrakci přes objekty Websockets a gesto jednoduchých požadavků a odpovědí rozhraní API pro volaného požadavky HTTP. [Webového rozhraní API](https://github.com/Azure/azure-relay-dotnet) příklad ukazuje postup pro integraci hybridní připojení pro webové služby ASP.NET Core.

#### <a name="nodejs"></a>Node.js

Hybridní připojení moduly uvedené v předchozí tabulce nahradit nebo změnit stávající modulů Node.js s alternativní implementací, které naslouchat na službu předávání přes Azure místo místního síťového zásobníku.

`hyco-https` Modulu mění a částečně přepsání modulů Node.js základní `http` a `https`, poskytuje implementace naslouchací proces protokolu HTTPS, který je kompatibilní s mnoha existující modulů Node.js a aplikace, které závisí na těchto jádra moduly.

`hyco-ws` a `hyco-websocket` moduly změnit oblíbených `ws` a `websocket` moduly pro Node.js, poskytuje alternativní naslouchací proces implementace, které umožňují moduly a aplikace, které spoléhají na buď modul fungovat za hybridním Předávání přes připojení.

Podrobnosti o těchto modulů najdete v [azure předávání uzlu](https://github.com/Azure/azure-relay-node) úložiště GitHub.

## <a name="next-steps"></a>Další postup

Další informace o předávání přes Azure, najdete pomocí těchto odkazů:
* [Co je Azure Relay?](relay-what-is-it.md)
* [Přenos – nejčastější dotazy](relay-faq.md)