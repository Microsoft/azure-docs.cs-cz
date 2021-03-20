---
title: Azure Relay požadavky HTTP Hybrid Connections v .NET
description: Napište konzolovou aplikaci v jazyce C# pro požadavky HTTP Azure Relay Hybrid Connections v .NET.
ms.topic: conceptual
ms.custom: devx-track-csharp
ms.date: 06/23/2020
ms.openlocfilehash: 7a11abb984da3601a4d6aa921224e01f94d0871c
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2021
ms.locfileid: "88922578"
---
# <a name="get-started-with-relay-hybrid-connections-http-requests-in-net"></a>Začínáme s požadavky HTTP Relay Hybrid Connections v .NET
[!INCLUDE [relay-selector-hybrid-connections](../../includes/relay-selector-hybrid-connections.md)]

V tomto rychlém startu vytvoříte aplikace pro odesílatele a přijímače v rozhraní .NET, které odesílají a přijímají zprávy pomocí protokolu HTTP. Aplikace používají Hybrid Connections funkce Azure Relay. Další informace o Azure Relay obecně najdete v tématu [Azure Relay](relay-what-is-it.md). 

V tomto rychlém startu proveďte následující kroky:

1. Pomocí webu Azure Portal vytvoříte obor názvů služby Relay.
2. Pomocí webu Azure Portal vytvoříte v tomto oboru názvů hybridní připojení.
3. Napíšeme konzolovou aplikaci serveru (naslouchacího procesu) pro příjem zpráv.
4. Napíšeme konzolovou aplikaci klienta (odesílatele) pro odesílání zpráv.
5. Spusťte aplikace. 

## <a name="prerequisites"></a>Předpoklady

Pro absolvování tohoto kurzu musí být splněné následující požadavky:

* [Visual Studio 2015 nebo novější](https://www.visualstudio.com). V příkladech v tomto kurzu se používá sada Visual Studio 2017.
* Předplatné Azure. Pokud ho ještě nemáte, [Vytvořte si bezplatný účet](https://azure.microsoft.com/free/) před tím, než začnete.

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
1. Spusťte klientskou aplikaci. Zobrazí `hello!` v okně klienta. Klient odeslal serveru požadavek HTTP a server odpověděl na `hello!` . 
3. K zavření oken konzoly, stiskněte **ENTER** v obou oknech konzoly. 

Blahopřejeme, vytvořili jste kompletní aplikaci Hybrid Connections!

## <a name="next-steps"></a>Další kroky

V tomto rychlém startu jste vytvořili klienta rozhraní .NET a serverové aplikace, které používaly protokol HTTP k odesílání a přijímání zpráv. Funkce Hybrid Connections Azure Relay také podporuje použití WebSockets k posílání a přijímání zpráv. Další informace o použití WebSockets s Azure Relay Hybrid Connections najdete v tématu [rychlý Start pro WebSockets](relay-hybrid-connections-dotnet-get-started.md).

V tomto rychlém startu jste použili .NET Framework k vytváření klientských a serverových aplikací. Informace o tom, jak psát klientské a serverové aplikace pomocí Node.js, najdete v tématu [ rychlý Start kNode.js WebSockets](relay-hybrid-connections-node-get-started.md) nebo v [ rychlém startu proNode.js http](relay-hybrid-connections-http-requests-dotnet-get-started.md).
