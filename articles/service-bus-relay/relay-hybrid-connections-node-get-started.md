---
title: Začínáme s WebSockety Azure Relay Hybrid Connections v Node | Microsoft Docs
description: Napište konzolovou aplikaci v Node.js pro WebSockety Azure Relay Hybrid Connections.
services: service-bus-relay
documentationcenter: node
author: spelluru
manager: timlt
editor: ''
ms.assetid: e44e4867-3cf3-46be-8f8a-7671e2013bc4
ms.service: service-bus-relay
ms.devlang: tbd
ms.topic: get-started-article
ms.tgt_pltfrm: node
ms.workload: na
ms.date: 11/01/2018
ms.author: spelluru
ms.openlocfilehash: 9bdf3d319735d8d4ca85235dfb949d440bba9a02
ms.sourcegitcommit: b62f138cc477d2bd7e658488aff8e9a5dd24d577
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/13/2018
ms.locfileid: "51615071"
---
# <a name="get-started-with-relay-hybrid-connections-websockets-in-nodejs"></a>Začínáme s předávání hybridních připojení WebSockets v Node.js

[!INCLUDE [relay-selector-hybrid-connections](../../includes/relay-selector-hybrid-connections.md)]

V tomto rychlém startu vytváříte aplikace odesílatele a příjemce Node.js, které odesílání a příjem zpráv pomocí WebSockets hybridní připojení Azure Relay. Obecné informace o Azure Relay, najdete v článku [Azure Relay](relay-what-is-it.md). 

V tomto rychlém startu proveďte následující kroky: 

1. Pomocí webu Azure Portal vytvoříte obor názvů služby Relay.
2. Pomocí webu Azure Portal vytvoříte v tomto oboru názvů hybridní připojení.
3. Napíšeme konzolovou aplikaci serveru (naslouchacího procesu) pro příjem zpráv.
4. Napíšeme konzolovou aplikaci klienta (odesílatele) pro odesílání zpráv.
5. Spouštění aplikací. 

## <a name="prerequisites"></a>Požadavky

- [Node.js](https://nodejs.org/en/).
- Předplatné Azure. Pokud ho nemáte, [vytvořte si bezplatný účet](https://azure.microsoft.com/free/) před tím, než začnete.

## <a name="create-a-namespace"></a>Vytvoření oboru názvů
[!INCLUDE [relay-create-namespace-portal](../../includes/relay-create-namespace-portal.md)]

## <a name="create-a-hybrid-connection"></a>Přidání hybridního připojení
[!INCLUDE [relay-create-hybrid-connection-portal](../../includes/relay-create-hybrid-connection-portal.md)]

## <a name="create-a-server-application-listener"></a>Vytvoření serverové aplikace (naslouchací proces)
Aby bylo možné naslouchat zprávám ze služby Relay a přijímat je, napíšeme konzolovou aplikaci Node.js.

[!INCLUDE [relay-hybrid-connections-node-get-started-server](../../includes/relay-hybrid-connections-node-get-started-server.md)]

## <a name="create-a-client-application-sender"></a>Vytvoření klientské aplikace (odesílatel)
Aby bylo možné odesílat zprávy do služby Relay, napíšeme konzolovou aplikaci Node.js.

[!INCLUDE [relay-hybrid-connections-node-get-started-client](../../includes/relay-hybrid-connections-node-get-started-client.md)]

## <a name="run-the-applications"></a>Spuštění aplikací

1. Spuštění serverové aplikace: v příkazovém řádku Node.js zadejte `node listener.js`.
2. Spuštění klientské aplikace: v příkazovém řádku Node.js zadejte `node sender.js` a nějaký text.
3. Ujistěte se, že výstupem konzoly serverové aplikace je text, který jste zadali v klientské aplikaci.

    ![running-applications](./media/relay-hybrid-connections-node-get-started/running-applications.png)

Blahopřejeme, vytvořili jste kompletní aplikaci Hybrid Connections pomocí Node.js!

## <a name="next-steps"></a>Další postup
V tomto rychlém startu jste vytvořili Node.js klientské a serverové aplikace, které používá objekty Websocket k odesílání a příjem zpráv. Funkce hybridní připojení Azure Relay podporuje také odesílat a přijímat zprávy pomocí protokolu HTTP. Zjistěte, jak používat protokol HTTP s hybridními připojeními Azure Relay, najdete v článku [rychlý start Node.js HTTP](relay-hybrid-connections-http-requests-node-get-started.md).

V tomto rychlém startu jste použili Node.js k vytvoření klientských a serverových aplikací. Další informace o zápisu klientské a serverové aplikace pomocí rozhraní .NET Framework, najdete v článku [.NET WebSockets rychlý Start](relay-hybrid-connections-dotnet-get-started.md) nebo [.NET HTTP rychlý Start](relay-hybrid-connections-http-requests-dotnet-get-started.md).


