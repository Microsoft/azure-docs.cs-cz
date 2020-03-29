---
title: Hybridní připojení azure relay – websockety v uzlu
description: Napište konzolovou aplikaci v Node.js pro WebSockety Azure Relay Hybrid Connections.
services: service-bus-relay
documentationcenter: node
author: spelluru
manager: timlt
editor: ''
ms.assetid: e44e4867-3cf3-46be-8f8a-7671e2013bc4
ms.service: service-bus-relay
ms.devlang: tbd
ms.topic: conceptual
ms.tgt_pltfrm: node
ms.workload: na
ms.date: 11/01/2018
ms.author: spelluru
ms.openlocfilehash: 818db4db082a441877b573fd52361e63becce374
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "75352677"
---
# <a name="get-started-with-relay-hybrid-connections-websockets-in-nodejs"></a>Začínáme s webovými sokety hybridních připojení přenosu v souboru Node.js

[!INCLUDE [relay-selector-hybrid-connections](../../includes/relay-selector-hybrid-connections.md)]

V tomto rychlém startu vytvoříte aplikace odesílatele a příjemce node.js, které odesílají a přijímají zprávy pomocí hybridních připojení WebSockets v Azure Relay. Další informace o Azure Relay obecně najdete v [tématu Azure Relay](relay-what-is-it.md). 

V tomto rychlém startu provedete následující kroky: 

1. Pomocí webu Azure Portal vytvoříte obor názvů služby Relay.
2. Pomocí webu Azure Portal vytvoříte v tomto oboru názvů hybridní připojení.
3. Napíšeme konzolovou aplikaci serveru (naslouchacího procesu) pro příjem zpráv.
4. Napíšeme konzolovou aplikaci klienta (odesílatele) pro odesílání zpráv.
5. Spouštět aplikace. 

## <a name="prerequisites"></a>Požadavky

- [Soubor Node.js](https://nodejs.org/en/).
- Předplatné Azure. Pokud ho nemáte, [vytvořte si účet zdarma,](https://azure.microsoft.com/free/) než začnete.

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

## <a name="next-steps"></a>Další kroky
V tomto rychlém startu jste vytvořili klientské a serverové aplikace Node.js, které používaly websockety k odesílání a přijímání zpráv. Funkce Hybridní připojení azure relay také podporuje použití protokolu HTTP pro odesílání a přijímání zpráv. Informace o použití protokolu HTTP s hybridními připojeními azure relay naleznete v [rychlém spuštění protokolu Node.js HTTP](relay-hybrid-connections-http-requests-node-get-started.md).

V tomto rychlém startu jste použili soubor Node.js k vytvoření klientských a serverových aplikací. Informace o zápisu klientských a serverových aplikací pomocí rozhraní .NET Framework naleznete [v rychlém startu .NET WebSockets](relay-hybrid-connections-dotnet-get-started.md) nebo [v rychlém startu protokolu .NET HTTP](relay-hybrid-connections-http-requests-dotnet-get-started.md).


