---
title: Hybridní připojení azure relay – požadavky HTTP v uzlu
description: Napište konzolovou aplikaci Node.js pro požadavky HTTP Azure Relay Hybrid Connections v Node.
services: service-bus-relay
documentationcenter: node
author: clemensv
manager: timlt
editor: ''
ms.assetid: e44e4867-3cf3-46be-8f8a-7671e2013bc4
ms.service: service-bus-relay
ms.devlang: tbd
ms.topic: conceptual
ms.tgt_pltfrm: node
ms.workload: na
ms.date: 11/01/2018
ms.author: clemensv
ms.openlocfilehash: d71386b86bf7133bb73ddce2e65c3b88743009ad
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "75462024"
---
# <a name="get-started-with-relay-hybrid-connections-http-requests-in-node"></a>Začínáme s požadavky HTTP Relay Hybrid Connections v Node

[!INCLUDE [relay-selector-hybrid-connections](../../includes/relay-selector-hybrid-connections.md)]

V tomto rychlém startu vytvoříte aplikace odesílatele a příjemce node.js, které odesílají a přijímají zprávy pomocí protokolu HTTP. Aplikace používají funkci Hybridní připojení služby Azure Relay. Další informace o Azure Relay obecně najdete v [tématu Azure Relay](relay-what-is-it.md). 

V tomto rychlém startu provedete následující kroky:

1. Pomocí webu Azure Portal vytvoříte obor názvů služby Relay.
2. Pomocí webu Azure Portal vytvoříte v tomto oboru názvů hybridní připojení.
3. Napíšeme konzolovou aplikaci serveru (naslouchacího procesu) pro příjem zpráv.
4. Napíšeme konzolovou aplikaci klienta (odesílatele) pro odesílání zpráv.
5. Spouštět aplikace.

## <a name="prerequisites"></a>Požadavky
- [Soubor Node.js](https://nodejs.org/en/).
- Předplatné Azure. Pokud ho nemáte, [vytvořte si účet zdarma,](https://azure.microsoft.com/free/) než začnete.

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

## <a name="next-steps"></a>Další kroky
V tomto rychlém startu jste vytvořili klientské a serverové aplikace Node.js, které používaly protokol HTTP k odesílání a přijímání zpráv. Funkce Hybridní připojení Azure Relay také podporuje použití WebSockets pro odesílání a přijímání zpráv. Informace o tom, jak používat websockety s hybridními připojeními Azure Relay, najdete v [tématu WebSockets quickstart](relay-hybrid-connections-node-get-started.md).

V tomto rychlém startu jste použili soubor Node.js k vytvoření klientských a serverových aplikací. Informace o zápisu klientských a serverových aplikací pomocí rozhraní .NET Framework naleznete [v rychlém startu .NET WebSockets](relay-hybrid-connections-dotnet-get-started.md) nebo [v rychlém startu protokolu .NET HTTP](relay-hybrid-connections-http-requests-dotnet-get-started.md).
