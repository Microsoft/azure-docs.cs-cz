---
title: Azure Relay Hybrid Connections – WebSockets v uzlu
description: Napište konzolovou aplikaci v Node.js pro WebSockety Azure Relay Hybrid Connections.
ms.topic: conceptual
ms.date: 06/23/2020
ms.custom: devx-track-js
ms.openlocfilehash: b362caa6570d4a8e212ff7adf4310a0c63e8b755
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2021
ms.locfileid: "91263704"
---
# <a name="get-started-with-relay-hybrid-connections-websockets-in-nodejs"></a>Začínáme se službou Relay Hybrid Connections WebSockets v Node.js

[!INCLUDE [relay-selector-hybrid-connections](../../includes/relay-selector-hybrid-connections.md)]

V tomto rychlém startu vytvoříte Node.js aplikace odesílatele a přijímače, které odesílají a přijímají zprávy pomocí Hybrid Connections WebSockets v Azure Relay. Další informace o Azure Relay obecně najdete v tématu [Azure Relay](relay-what-is-it.md). 

V tomto rychlém startu proveďte následující kroky: 

1. Pomocí webu Azure Portal vytvoříte obor názvů služby Relay.
2. Pomocí webu Azure Portal vytvoříte v tomto oboru názvů hybridní připojení.
3. Napíšeme konzolovou aplikaci serveru (naslouchacího procesu) pro příjem zpráv.
4. Napíšeme konzolovou aplikaci klienta (odesílatele) pro odesílání zpráv.
5. Spusťte aplikace. 

## <a name="prerequisites"></a>Předpoklady

- [Node.js](https://nodejs.org/en/).
- Předplatné Azure. Pokud ho ještě nemáte, [Vytvořte si bezplatný účet](https://azure.microsoft.com/free/) před tím, než začnete.

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

    ![Okna konzoly testují aplikace serveru i klienta.](./media/relay-hybrid-connections-node-get-started/running-applications.png)

Blahopřejeme, vytvořili jste kompletní aplikaci Hybrid Connections pomocí Node.js!

## <a name="next-steps"></a>Další kroky
V tomto rychlém startu jste vytvořili Node.js klientských a serverových aplikací, které používaly WebSockets k posílání a přijímání zpráv. Funkce Hybrid Connections Azure Relay také podporuje odesílání a příjem zpráv pomocí protokolu HTTP. Informace o tom, jak používat protokol HTTP s Azure Relay Hybrid Connections, najdete v tématu [ rychlý Start proNode.js http](relay-hybrid-connections-http-requests-node-get-started.md).

V tomto rychlém startu jste použili Node.js k vytváření klientských a serverových aplikací. Informace o tom, jak psát klientské a serverové aplikace pomocí .NET Framework, najdete v tématu [rychlý Start k rozhraní .NET WebSockets](relay-hybrid-connections-dotnet-get-started.md) nebo [rychlé zprovoznění protokolu HTTP .NET](relay-hybrid-connections-http-requests-dotnet-get-started.md).


