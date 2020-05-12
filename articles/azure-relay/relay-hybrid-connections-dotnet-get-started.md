---
title: Azure Relay Hybrid Connections – WebSockets v .NET
description: Napíšeme konzolovou aplikaci v jazyce C# pro Azure Relay Hybrid Connections objekty WebSockets.
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
ms.openlocfilehash: 13612f8ffa343e483165a8dbdd54d1b2b1f5e2cf
ms.sourcegitcommit: a8ee9717531050115916dfe427f84bd531a92341
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/12/2020
ms.locfileid: "83211928"
---
# <a name="get-started-with-relay-hybrid-connections-websockets-in-net"></a>Začínáme s přenosovým Hybrid Connections WebSockets v .NET
[!INCLUDE [relay-selector-hybrid-connections](../../includes/relay-selector-hybrid-connections.md)]

V tomto rychlém startu vytvoříte aplikace pro odesílatele a přijímače v rozhraní .NET, které odesílají a přijímají zprávy pomocí Hybrid Connections WebSockets v Azure Relay. Další informace o Azure Relay obecně najdete v tématu [Azure Relay](relay-what-is-it.md). 

V tomto rychlém startu proveďte následující kroky:

1. Pomocí webu Azure Portal vytvoříte obor názvů služby Relay.
2. Pomocí webu Azure Portal vytvoříte v tomto oboru názvů hybridní připojení.
3. Napíšeme konzolovou aplikaci serveru (naslouchacího procesu) pro příjem zpráv.
4. Napíšeme konzolovou aplikaci klienta (odesílatele) pro odesílání zpráv.
5. Spusťte aplikace. 

## <a name="prerequisites"></a>Požadavky

Pro absolvování tohoto kurzu musí být splněné následující požadavky:

* [Sada Visual Studio 2015 nebo novější](https://www.visualstudio.com). V příkladech v tomto kurzu se používá sada Visual Studio 2017.
* Předplatné Azure. Pokud ho ještě nemáte, [Vytvořte si bezplatný účet](https://azure.microsoft.com/free/) před tím, než začnete.

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

Blahopřejeme, vytvořili jste kompletní aplikaci Hybrid Connections!

## <a name="next-steps"></a>Další kroky
V tomto rychlém startu jste vytvořili klientské a serverové aplikace .NET, které používají objekty WebSockets k posílání a přijímání zpráv. Funkce Hybrid Connections Azure Relay také podporuje odesílání a příjem zpráv pomocí protokolu HTTP. Informace o tom, jak používat protokol HTTP s Azure Relay Hybrid Connections, najdete v [rychlém startu protokolu HTTP](relay-hybrid-connections-http-requests-dotnet-get-started.md).

V tomto rychlém startu jste použili .NET Framework k vytváření klientských a serverových aplikací. Informace o tom, jak psát klientské a serverové aplikace pomocí Node. js, najdete v tématu rychlý Start k objektům [WebSockets Node. js](relay-hybrid-connections-node-get-started.md) nebo [rychlý Start pro Node. js http](relay-hybrid-connections-http-requests-dotnet-get-started.md).

