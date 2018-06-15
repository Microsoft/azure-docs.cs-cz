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
ms.date: 05/02/2018
ms.author: clemensv
ms.openlocfilehash: 2bc923650425c76562161dd6f44f3a5722b5cefe
ms.sourcegitcommit: 870d372785ffa8ca46346f4dfe215f245931dae1
ms.translationtype: HT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/08/2018
ms.locfileid: "33896621"
---
# <a name="get-started-with-relay-hybrid-connections-http-requests-in-node"></a>Začínáme s požadavky HTTP Relay Hybrid Connections v Node

[!INCLUDE [relay-selector-hybrid-connections](../../includes/relay-selector-hybrid-connections.md)]

Tento kurz představuje úvod do požadavků HTTP služby [Azure Relay Hybrid Connections](relay-what-is-it.md#hybrid-connections) a ukazuje, jak pomocí Node.js vytvořit klientskou aplikaci, která odesílá zprávy do příslušné aplikace naslouchacího procesu.

## <a name="what-will-be-accomplished"></a>Co všechno zvládneme

Protože služba Hybrid Connections vyžaduje komponentu klienta i serveru, vytvoříme v tomto kurzu dvě konzolové aplikace. Postup je následující:

1. Pomocí webu Azure Portal vytvoříme obor názvů přenosu.
2. Pomocí webu Azure Portal vytvoříme hybridní připojení.
3. Napíšeme serverovou aplikaci pro příjem zpráv.
4. Napíšeme aplikaci klientské konzoly pro příjem zpráv.

## <a name="prerequisites"></a>Požadavky

1. [Node.js](https://nodejs.org/en/).
2. Předplatné Azure.

[!INCLUDE [create-account-note](../../includes/create-account-note.md)]

## <a name="1-create-a-namespace-using-the-azure-portal"></a>1. Vytvoření oboru názvů služby Service Bus pomocí webu Azure Portal

Pokud už máte vytvořený obor názvů služby Relay, přejděte do části [Vytvoření hybridního připojení pomocí webu Azure Portal](#2-create-a-hybrid-connection-using-the-azure-portal).

[!INCLUDE [relay-create-namespace-portal](../../includes/relay-create-namespace-portal.md)]

## <a name="2-create-a-hybrid-connection-using-the-azure-portal"></a>2. Vytvoření hybridního připojení pomocí webu Azure Portal

Pokud už máte vytvořené hybridní připojení, přejděte do části [Vytvoření serverové aplikace](#3-create-a-server-application-listener).

[!INCLUDE [relay-create-hybrid-connection-portal](../../includes/relay-create-hybrid-connection-portal.md)]

## <a name="3-create-a-server-application-listener"></a>3. Vytvoření serverové aplikace (naslouchací proces)

Aby bylo možné naslouchat zprávám ze služby Relay a přijímat je, napíšeme konzolovou aplikaci Node.js.

[!INCLUDE [relay-hybrid-connections-node-get-started-server](../../includes/relay-hybrid-connections-http-requests-node-get-started-server.md)]

## <a name="4-create-a-client-application-sender"></a>4. Vytvoření klientské aplikace (odesílatel)

Aby bylo možné odesílat zprávy do služby Relay, můžete použít libovolného klienta HTTP nebo napsat konzolovou aplikaci Node.js.

[!INCLUDE [relay-hybrid-connections-node-get-started-client](../../includes/relay-hybrid-connections-http-requests-node-get-started-client.md)]

## <a name="5-run-the-applications"></a>5. Spuštění aplikací

1. Spuštění serverové aplikace: v příkazovém řádku Node.js zadejte `node listener.js`.
2. Spuštění klientské aplikace: v příkazovém řádku Node.js zadejte `node sender.js` a nějaký text.
3. Ujistěte se, že výstupem konzoly serverové aplikace je text, který jste zadali v klientské aplikaci.

Blahopřejeme, vytvořili jste kompletní aplikaci Hybrid Connections pomocí Node.js!

## <a name="next-steps"></a>Další kroky

* [Přenos – nejčastější dotazy](relay-faq.md)
* [Vytvoření oboru názvů](relay-create-namespace-portal.md)
* [Začínáme s .NET](relay-hybrid-connections-dotnet-get-started.md)
* [Začínáme s aplikací Node](relay-hybrid-connections-node-get-started.md)

