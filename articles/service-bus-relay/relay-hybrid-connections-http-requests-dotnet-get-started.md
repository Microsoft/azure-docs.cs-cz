---
title: Začínáme s požadavky HTTP hybridních připojení Azure Relay v .NET | Microsoft Docs
description: Napište konzolovou aplikaci v jazyce C# pro požadavky HTTP Azure Relay Hybrid Connections v .NET.
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
ms.date: 08/16/2018
ms.author: spelluru
ms.openlocfilehash: e66a1651a46cfaeb7fb8b232eeb7cf6a2fb8044d
ms.sourcegitcommit: f31bfb398430ed7d66a85c7ca1f1cc9943656678
ms.translationtype: HT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/28/2018
ms.locfileid: "47451218"
---
# <a name="get-started-with-relay-hybrid-connections-http-requests-in-net"></a>Začínáme s požadavky HTTP Relay Hybrid Connections v .NET
[!INCLUDE [relay-selector-hybrid-connections](../../includes/relay-selector-hybrid-connections.md)]

Tento kurz poskytuje úvod do [hybridních připojení Azure Relay](relay-what-is-it.md#hybrid-connections). Zjistěte, jak s využitím Microsoft .NET vytvořit klientskou aplikaci, která odesílá požadavky do příslušné aplikace naslouchacího procesu. 

## <a name="what-will-be-accomplished"></a>Co všechno zvládneme
Hybridní připojení vyžadují komponentu klienta i komponentu serveru. V tomto kurzu dokončíte tyto kroky a vytvoříte dvě konzolové aplikace:

1. Pomocí webu Azure Portal vytvoříte obor názvů služby Relay.
2. Pomocí webu Azure Portal vytvoříte v tomto oboru názvů hybridní připojení.
3. Napíšete konzolovou aplikaci serveru (naslouchacího procesu) pro příjem požadavků.
4. Napíšete konzolovou aplikaci klienta (odesílatele) pro odesílání požadavků.

## <a name="prerequisites"></a>Požadavky

Pro absolvování tohoto kurzu musí být splněné následující požadavky:

* [Sada Visual Studio 2015 nebo novější](http://www.visualstudio.com). V příkladech v tomto kurzu se používá sada Visual Studio 2017.
* Předplatné Azure.

[!INCLUDE [create-account-note](../../includes/create-account-note.md)]

## <a name="1-create-a-namespace-by-using-the-azure-portal"></a>1. Vytvoření oboru názvů pomocí webu Azure Portal
Pokud už máte vytvořený obor názvů služby Relay, přejděte k části [Vytvoření hybridního připojení pomocí webu Azure Portal](#2-create-a-hybrid-connection-using-the-azure-portal).

[!INCLUDE [relay-create-namespace-portal](../../includes/relay-create-namespace-portal.md)]

## <a name="2-create-a-hybrid-connection-by-using-the-azure-portal"></a>2. Vytvoření hybridního připojení pomocí webu Azure Portal
Pokud už máte vytvořené hybridní připojení, přejděte k části [Vytvoření serverové aplikace](#3-create-a-server-application-listener).

[!INCLUDE [relay-create-hybrid-connection-portal](../../includes/relay-create-hybrid-connection-portal.md)]

## <a name="3-create-a-server-application-listener"></a>3. Vytvoření serverové aplikace (naslouchací proces)
Napište v sadě Visual Studio konzolovou aplikaci v jazyce C#, která bude naslouchat a přijímat zprávy z předávací služby.

[!INCLUDE [relay-hybrid-connections-http-requests-dotnet-get-started-server](../../includes/relay-hybrid-connections-http-requests-dotnet-get-started-server.md)]

## <a name="4-create-a-client-application-sender"></a>4. Vytvoření klientské aplikace (odesílatel)
Napište v sadě Visual Studio konzolovou aplikaci v jazyce C#, která bude odesílat zprávy do předávací služby.

[!INCLUDE [relay-hybrid-connections-http-requests-dotnet-get-started-client](../../includes/relay-hybrid-connections-http-requests-dotnet-get-started-client.md)]

## <a name="5-run-the-applications"></a>5. Spuštění aplikací
1. Spusťte serverovou aplikaci. V okně konzoly uvidíte následující text:

    ```
    Online
    Server listening
    ```
1. Spusťte klientskou aplikaci. Zobrazí `hello!` v okně klienta. Klient odešle požadavek HTTP na server a server odpověděl zprávou `hello!`. 
3. K zavření oken konzoly, stiskněte **ENTER** v obou oknech konzoly. 

Blahopřejeme, vytvořili jste kompletní aplikaci Hybrid Connections.

## <a name="next-steps"></a>Další kroky

* [Přenos – nejčastější dotazy](relay-faq.md)
* [Vytvoření oboru názvů](relay-create-namespace-portal.md)
* [Začínáme s aplikací Node](relay-hybrid-connections-node-get-started.md)

