---
title: Azure Relay Hybrid Connections – požadavky HTTP v uzlu
description: Napište konzolovou aplikaci Node.js pro požadavky HTTP Azure Relay Hybrid Connections v Node.
ms.topic: conceptual
ms.date: 06/23/2020
ms.custom: devx-track-js
ms.openlocfilehash: 249b4fa231cd54a1a8054b32985ed0e48fcc16f1
ms.sourcegitcommit: 32c521a2ef396d121e71ba682e098092ac673b30
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/25/2020
ms.locfileid: "91263755"
---
# <a name="get-started-with-relay-hybrid-connections-http-requests-in-node"></a>Začínáme s požadavky HTTP Relay Hybrid Connections v Node

[!INCLUDE [relay-selector-hybrid-connections](../../includes/relay-selector-hybrid-connections.md)]

V tomto rychlém startu vytvoříte Node.js aplikace odesílatele a přijímače, které odesílají a přijímají zprávy pomocí protokolu HTTP. Aplikace používají Hybrid Connections funkce Azure Relay. Další informace o Azure Relay obecně najdete v tématu [Azure Relay](relay-what-is-it.md). 

V tomto rychlém startu proveďte následující kroky:

1. Pomocí webu Azure Portal vytvoříte obor názvů služby Relay.
2. Pomocí webu Azure Portal vytvoříte v tomto oboru názvů hybridní připojení.
3. Napíšeme konzolovou aplikaci serveru (naslouchacího procesu) pro příjem zpráv.
4. Napíšeme konzolovou aplikaci klienta (odesílatele) pro odesílání zpráv.
5. Spusťte aplikace.

## <a name="prerequisites"></a>Požadavky
- [Node.js](https://nodejs.org/en/).
- Předplatné Azure. Pokud ho ještě nemáte, [Vytvořte si bezplatný účet](https://azure.microsoft.com/free/) před tím, než začnete.

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
V tomto rychlém startu jste vytvořili Node.js klientských a serverových aplikací, které používaly protokol HTTP k odesílání a přijímání zpráv. Funkce Hybrid Connections Azure Relay také podporuje použití WebSockets k posílání a přijímání zpráv. Další informace o použití WebSockets s Azure Relay Hybrid Connections najdete v tématu [rychlý Start pro WebSockets](relay-hybrid-connections-node-get-started.md).

V tomto rychlém startu jste použili Node.js k vytváření klientských a serverových aplikací. Informace o tom, jak psát klientské a serverové aplikace pomocí .NET Framework, najdete v tématu [rychlý Start k rozhraní .NET WebSockets](relay-hybrid-connections-dotnet-get-started.md) nebo [rychlé zprovoznění protokolu HTTP .NET](relay-hybrid-connections-http-requests-dotnet-get-started.md).
