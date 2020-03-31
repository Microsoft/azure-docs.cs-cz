---
title: Přehled rozhraní AZURE Relay API | Dokumenty společnosti Microsoft
description: Tento článek obsahuje přehled dostupných rozhraní API pro přenos Azure (.NET Standard, .NET Framework, Node.js atd.)
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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "76513962"
---
# <a name="available-relay-apis"></a>Dostupná rozhraní API pro přenos

## <a name="runtime-apis"></a>Runtime API

V následující tabulce jsou uvedeni všichni aktuálně dostupní klienti runtime relé.

Další [informace](#additional-information) část obsahuje další informace o stavu každé knihovny runtime.

| Jazyk/platforma | Dostupná funkce | Klientský balíček | Repository |
| --- | --- | --- | --- |
| .NET Standard | Hybridní připojení | [Microsoft.Azure.Relay](https://www.nuget.org/packages/Microsoft.Azure.Relay/) | [GitHub](https://github.com/azure/azure-relay-dotnet) |
| .NET Framework | WCF Relay | [WindowsAzure.ServiceBus](https://www.nuget.org/packages/WindowsAzure.ServiceBus/) | Není dostupné. |
| Node | Hybridní připojení | [Webové zásuvky:`hyco-ws`](https://www.npmjs.com/package/hyco-ws)<br/>[Webové zásuvky:`hyco-websocket`](https://www.npmjs.com/package/hyco-websocket)<br/>[Požadavky HTTP:`hyco-https`](https://www.npmjs.com/package/hyco-https) | [GitHub](https://github.com/Azure/azure-relay-node) |

### <a name="additional-information"></a>Další informace

#### <a name="net"></a>.NET

Ekosystém .NET má více runtimes, proto existuje více knihoven .NET pro relé. Knihovnu .NET Standard lze spustit pomocí rozhraní .NET Core nebo rozhraní .NET Framework, zatímco knihovnu rozhraní .NET Framework lze spustit pouze v prostředí rozhraní .NET Framework. Další informace o rozhraní .NET Frameworks naleznete v [tématu Framework versions](/dotnet/articles/standard/frameworks).

Knihovna rozhraní .NET Framework podporuje pouze programovací model WCF a spoléhá `net.tcp` na proprietární binární protokol založený na přenosu WCF. Tento protokol a knihovna je udržována pro zpětnou kompatibilitu s existujícími aplikacemi.

Knihovna .NET Standard je založena na definici otevřeného protokolu pro přenos hybridních připojení, která je založena na protokolech HTTP a WebSockets. Knihovna podporuje odběr datového proudu přes websockety a jednoduché gesto rozhraní API pro odpověď na požadavky HTTP. Ukázka [webového rozhraní API](https://github.com/Azure/azure-relay-dotnet) ukazuje, jak integrovat hybridní připojení s ASP.NET Core pro webové služby.

#### <a name="nodejs"></a>Node.js

Moduly hybridnípřipojení uvedené v tabulce výše nahradí nebo změní existující moduly Node.js alternativními implementacemi, které naslouchají službě Azure Relay namísto zásobníku místní sítě.

Modul `hyco-https` změní a částečně přepíše základní moduly `http` Node.js a `https`poskytuje implementaci naslouchacího procesu HTTPS, která je kompatibilní s mnoha existujícími moduly a aplikacemi Node.js, které jsou závislé na těchto základních modulech.

`hyco-ws` Moduly `hyco-websocket` a změnit `ws` populární `websocket` a moduly pro Node.js, poskytuje alternativní naslouchací proces implementace, které umožňují moduly a aplikace spoléhat na obou modulů pracovat za hybridní připojení relé.

Podrobnosti o těchto modulech najdete v úložišti [GitHub azure-relay-node.](https://github.com/Azure/azure-relay-node)

## <a name="next-steps"></a>Další kroky

Další informace o Azure Relay najdete na těchto odkazech:
* [Co je Azure Relay?](relay-what-is-it.md)
* [Přenos – nejčastější dotazy](relay-faq.md)
