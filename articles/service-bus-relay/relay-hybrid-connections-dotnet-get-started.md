---
title: Začínáme s WebSockety Azure Relay Hybrid Connections v .NET | Microsoft Docs
description: Napište konzolovou aplikaci v jazyce C# pro WebSockety Azure Relay Hybrid Connections.
services: service-bus-relay
documentationcenter: .net
author: spelluru
manager: timlt
editor: ''
ms.assetid: d1386900-b942-4abf-acfc-38d2ef826253
ms.service: service-bus-relay
ms.devlang: tbd
ms.topic: get-started-article
ms.tgt_pltfrm: dotnet
ms.workload: na
ms.date: 11/01/2018
ms.author: spelluru
ms.openlocfilehash: 63961e978056a20e697872c0c1d892efaadcc627
ms.sourcegitcommit: b62f138cc477d2bd7e658488aff8e9a5dd24d577
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/13/2018
ms.locfileid: "51612065"
---
# <a name="get-started-with-relay-hybrid-connections-websockets-in-net"></a>Začínáme s předávání hybridních připojení WebSockets v rozhraní .NET
[!INCLUDE [relay-selector-hybrid-connections](../../includes/relay-selector-hybrid-connections.md)]

V tomto rychlém startu vytvoříte odesílatele a příjemce aplikací .NET, které odesílání a příjem zpráv pomocí WebSockets hybridní připojení Azure Relay. Obecné informace o Azure Relay, najdete v článku [Azure Relay](relay-what-is-it.md). 

V tomto rychlém startu proveďte následující kroky:

1. Pomocí webu Azure Portal vytvoříte obor názvů služby Relay.
2. Pomocí webu Azure Portal vytvoříte v tomto oboru názvů hybridní připojení.
3. Napíšeme konzolovou aplikaci serveru (naslouchacího procesu) pro příjem zpráv.
4. Napíšeme konzolovou aplikaci klienta (odesílatele) pro odesílání zpráv.
5. Spouštění aplikací. 

## <a name="prerequisites"></a>Požadavky

Pro absolvování tohoto kurzu musí být splněné následující požadavky:

* [Sada Visual Studio 2015 nebo novější](http://www.visualstudio.com). V příkladech v tomto kurzu se používá sada Visual Studio 2017.
* Předplatné Azure. Pokud ho nemáte, [vytvořte si bezplatný účet](https://azure.microsoft.com/free/) před tím, než začnete.

## <a name="create-a-namespace"></a>Vytvoření oboru názvů
[!INCLUDE [relay-create-namespace-portal](../../includes/relay-create-namespace-portal.md)]

## <a name="create-a-hybrid-connection"></a>Přidání hybridního připojení
[!INCLUDE [relay-create-hybrid-connection-portal](../../includes/relay-create-hybrid-connection-portal.md)]

## <a name="create-a-server-application-listener"></a>Vytvoření serverové aplikace (naslouchací proces)
Napište v sadě Visual Studio konzolovou aplikaci v jazyce C#, která bude naslouchat a přijímat zprávy z předávací služby.

[!INCLUDE [relay-hybrid-connections-dotnet-get-started-server](../../includes/relay-hybrid-connections-dotnet-get-started-server.md)]

## <a name="create-a-client-application-sender"></a>Vytvoření klientské aplikace (odesílatel)
Napište v sadě Visual Studio konzolovou aplikaci v jazyce C#, která bude odesílat zprávy do předávací služby.

[!INCLUDE [relay-hybrid-connections-dotnet-get-started-client](../../includes/relay-hybrid-connections-dotnet-get-started-client.md)]

## <a name="run-the-applications"></a>Spuštění aplikací
1. Spusťte serverovou aplikaci.
2. Spusťte klientskou aplikaci a napište nějaký text.
3. Ujistěte se, že konzola serverové aplikace zobrazí text, který jste zadali v klientské aplikaci.

    ![running-applications](./media/relay-hybrid-connections-dotnet-get-started/running-applications.png)

Blahopřejeme, vytvořili jste kompletní aplikaci Hybrid Connections.

## <a name="next-steps"></a>Další postup
V tomto rychlém startu jste vytvořili .NET klientské a serverové aplikace, které používá objekty Websocket k odesílání a příjem zpráv. Funkce hybridní připojení Azure Relay podporuje také odesílat a přijímat zprávy pomocí protokolu HTTP. Zjistěte, jak používat protokol HTTP s hybridními připojeními Azure Relay, najdete v článku [rychlý start HTTP](relay-hybrid-connections-http-requests-dotnet-get-started.md).

V tomto rychlém startu použijete k vytvoření klientských a serverových aplikací rozhraní .NET Framework. Další informace o zápisu klientské a serverové aplikace využívající Node.js, najdete v článku [rychlý start pro Node.js WebSockets](relay-hybrid-connections-node-get-started.md) nebo [Node.js HTTP rychlý Start](relay-hybrid-connections-http-requests-dotnet-get-started.md).

