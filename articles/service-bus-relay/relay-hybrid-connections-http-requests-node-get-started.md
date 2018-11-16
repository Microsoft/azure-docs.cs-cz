---
title: Začínáme s požadavky HTTP Azure Relay Hybrid Connections v Node | Microsoft Docs
description: Napište konzolovou aplikaci Node.js pro požadavky HTTP Azure Relay Hybrid Connections v Node.
services: service-bus-relay
documentationcenter: node
author: clemensv
manager: timlt
editor: ''
ms.assetid: e44e4867-3cf3-46be-8f8a-7671e2013bc4
ms.service: service-bus-relay
ms.devlang: tbd
ms.topic: get-started-article
ms.tgt_pltfrm: node
ms.workload: na
ms.date: 11/01/2018
ms.author: clemensv
ms.openlocfilehash: a25ca0c10b66f6881f2423306564d7d37124c33a
ms.sourcegitcommit: b62f138cc477d2bd7e658488aff8e9a5dd24d577
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/13/2018
ms.locfileid: "51616176"
---
# <a name="get-started-with-relay-hybrid-connections-http-requests-in-node"></a>Začínáme s požadavky HTTP Relay Hybrid Connections v Node

[!INCLUDE [relay-selector-hybrid-connections](../../includes/relay-selector-hybrid-connections.md)]

V tomto rychlém startu vytváříte aplikace odesílatele a příjemce Node.js, které odesílání a příjem zpráv pomocí protokolu HTTP. Aplikace používají funkce hybridní připojení Azure Relay. Obecné informace o Azure Relay, najdete v článku [Azure Relay](relay-what-is-it.md). 

V tomto rychlém startu proveďte následující kroky:

1. Pomocí webu Azure Portal vytvoříte obor názvů služby Relay.
2. Pomocí webu Azure Portal vytvoříte v tomto oboru názvů hybridní připojení.
3. Napíšeme konzolovou aplikaci serveru (naslouchacího procesu) pro příjem zpráv.
4. Napíšeme konzolovou aplikaci klienta (odesílatele) pro odesílání zpráv.
5. Spouštění aplikací.

## <a name="prerequisites"></a>Požadavky
- [Node.js](https://nodejs.org/en/).
- Předplatné Azure. Pokud ho nemáte, [vytvořte si bezplatný účet](https://azure.microsoft.com/free/) před tím, než začnete.

## <a name="create-a-namespace-using-the-azure-portal"></a>Vytvoření oboru názvů služby Service Bus pomocí webu Azure Portal
[!INCLUDE [relay-create-namespace-portal](../../includes/relay-create-namespace-portal.md)]

## <a name="create-a-hybrid-connection-using-the-azure-portal"></a>Vytvoření hybridního připojení pomocí webu Azure Portal
[!INCLUDE [relay-create-hybrid-connection-portal](../../includes/relay-create-hybrid-connection-portal.md)]

## <a name="create-a-server-application-listener"></a>Vytvoření serverové aplikace (naslouchací proces)
Aby bylo možné naslouchat zprávám ze služby Relay a přijímat je, napíšeme konzolovou aplikaci Node.js.

[!INCLUDE [relay-hybrid-connections-node-get-started-server](../../includes/relay-hybrid-connections-http-requests-node-get-started-server.md)]

## <a name="create-a-client-application-sender"></a>Vytvoření klientské aplikace (odesílatel)

Aby bylo možné odesílat zprávy do služby Relay, můžete použít libovolného klienta HTTP nebo napsat konzolovou aplikaci Node.js.

[!INCLUDE [relay-hybrid-connections-node-get-started-client](../../includes/relay-hybrid-connections-http-requests-node-get-started-client.md)]

## <a name="run-the-applications"></a>Spuštění aplikací

1. Spuštění serverové aplikace: v příkazovém řádku Node.js zadejte `node listener.js`.
2. Spuštění klientské aplikace: v příkazovém řádku Node.js zadejte `node sender.js` a nějaký text.
3. Ujistěte se, že výstupem konzoly serverové aplikace je text, který jste zadali v klientské aplikaci.

Blahopřejeme, vytvořili jste kompletní aplikaci Hybrid Connections pomocí Node.js!

## <a name="next-steps"></a>Další postup
V tomto rychlém startu jste vytvořili Node.js klientské a serverové aplikace, které umožňuje odesílat a přijímat zprávy HTTP. Funkce hybridní připojení Azure Relay podporuje také používá objekty Websocket pro odesílání a příjem zpráv. Zjistěte, jak používat objekty Websocket s hybridními připojeními Azure Relay, najdete v článku [objekty Websocket quickstart](relay-hybrid-connections-node-get-started.md).

V tomto rychlém startu jste použili Node.js k vytvoření klientských a serverových aplikací. Další informace o zápisu klientské a serverové aplikace pomocí rozhraní .NET Framework, najdete v článku [.NET WebSockets rychlý Start](relay-hybrid-connections-dotnet-get-started.md) nebo [.NET HTTP rychlý Start](relay-hybrid-connections-http-requests-dotnet-get-started.md).
