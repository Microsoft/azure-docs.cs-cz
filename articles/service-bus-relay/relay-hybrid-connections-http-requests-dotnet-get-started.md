---
title: Hybridní připojení přenosu Azure – požadavky HTTP v rozhraní .NET
description: Napište konzolovou aplikaci v jazyce C# pro požadavky HTTP Azure Relay Hybrid Connections v .NET.
services: service-bus-relay
documentationcenter: .net
author: spelluru
manager: timlt
editor: ''
ms.assetid: d1386900-b942-4abf-acfc-38d2ef826253
ms.service: service-bus-relay
ms.devlang: tbd
ms.topic: conceptual
ms.tgt_pltfrm: dotnet
ms.workload: na
ms.date: 11/01/2018
ms.author: spelluru
ms.openlocfilehash: 7c984876c4338b4f6802ba55752c8f612c390e94
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "75355166"
---
# <a name="get-started-with-relay-hybrid-connections-http-requests-in-net"></a>Začínáme s požadavky HTTP Relay Hybrid Connections v .NET
[!INCLUDE [relay-selector-hybrid-connections](../../includes/relay-selector-hybrid-connections.md)]

V tomto rychlém startu vytvoříte aplikace odesílatele a příjemce rozhraní .NET, které odesílají a přijímají zprávy pomocí protokolu HTTP. Aplikace používají funkci Hybridní připojení služby Azure Relay. Další informace o Azure Relay obecně najdete v [tématu Azure Relay](relay-what-is-it.md). 

V tomto rychlém startu provedete následující kroky:

1. Pomocí webu Azure Portal vytvoříte obor názvů služby Relay.
2. Pomocí webu Azure Portal vytvoříte v tomto oboru názvů hybridní připojení.
3. Napíšeme konzolovou aplikaci serveru (naslouchacího procesu) pro příjem zpráv.
4. Napíšeme konzolovou aplikaci klienta (odesílatele) pro odesílání zpráv.
5. Spouštět aplikace. 

## <a name="prerequisites"></a>Požadavky

Pro absolvování tohoto kurzu musí být splněné následující požadavky:

* [Sada Visual Studio 2015 nebo novější](https://www.visualstudio.com). V příkladech v tomto kurzu se používá sada Visual Studio 2017.
* Předplatné Azure. Pokud ho nemáte, [vytvořte si účet zdarma,](https://azure.microsoft.com/free/) než začnete.

## <a name="create-a-namespace"></a>Vytvoření oboru názvů
[!INCLUDE [relay-create-namespace-portal](../../includes/relay-create-namespace-portal.md)]

## <a name="create-a-hybrid-connection"></a>Přidání hybridního připojení
[!INCLUDE [relay-create-hybrid-connection-portal](../../includes/relay-create-hybrid-connection-portal.md)]

## <a name="create-a-server-application-listener"></a>Vytvoření serverové aplikace (naslouchací proces)
Napište v sadě Visual Studio konzolovou aplikaci v jazyce C#, která bude naslouchat a přijímat zprávy z předávací služby.

[!INCLUDE [relay-hybrid-connections-http-requests-dotnet-get-started-server](../../includes/relay-hybrid-connections-http-requests-dotnet-get-started-server.md)]

## <a name="create-a-client-application-sender"></a>Vytvoření klientské aplikace (odesílatel)
Napište v sadě Visual Studio konzolovou aplikaci v jazyce C#, která bude odesílat zprávy do předávací služby.

[!INCLUDE [relay-hybrid-connections-http-requests-dotnet-get-started-client](../../includes/relay-hybrid-connections-http-requests-dotnet-get-started-client.md)]

## <a name="run-the-applications"></a>Spuštění aplikací
1. Spusťte serverovou aplikaci. V okně konzoly uvidíte následující text:

    ```
    Online
    Server listening
    ```
1. Spusťte klientskou aplikaci. Zobrazí `hello!` v okně klienta. Klient odeslal na server požadavek HTTP a `hello!`server odpověděl . 
3. K zavření oken konzoly, stiskněte **ENTER** v obou oknech konzoly. 

Gratulujeme, jste vytvořili kompletní hybridní připojení aplikace!

## <a name="next-steps"></a>Další kroky

V tomto rychlém startu jste vytvořili klientské a serverové aplikace rozhraní .NET, které používaly protokol HTTP k odesílání a přijímání zpráv. Funkce Hybridní připojení Azure Relay také podporuje použití WebSockets pro odesílání a přijímání zpráv. Informace o tom, jak používat websockety s hybridními připojeními Azure Relay, najdete v [tématu WebSockets quickstart](relay-hybrid-connections-dotnet-get-started.md).

V tomto rychlém startu jste k vytvoření klientských a serverových aplikací použili rozhraní .NET Framework. Informace o psaní klientských a serverových aplikací pomocí souboru Node.js naleznete [v rychlém startu Node.js WebSockets](relay-hybrid-connections-node-get-started.md) nebo [na úvodním panelu HTTP Node.js](relay-hybrid-connections-http-requests-dotnet-get-started.md).
