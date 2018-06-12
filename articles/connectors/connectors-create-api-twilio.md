---
title: Přidejte konektor Twilio v Azure Logic apps | Microsoft Docs
description: Přehled konektoru Twilio s parametry rozhraní REST API
services: logic-apps
documentationcenter: ''
author: ecfan
manager: jeconnoc
editor: ''
tags: connectors
ms.assetid: 43116187-4a2f-42e5-9852-a0d62f08c5fc
ms.service: logic-apps
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: integration
ms.date: 09/19/2016
ms.author: estfan; ladocs
ms.openlocfilehash: 8bcf69a7c8e04cb45d795fd0d6f20d477c15865d
ms.sourcegitcommit: 6f6d073930203ec977f5c283358a19a2f39872af
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/11/2018
ms.locfileid: "35296131"
---
# <a name="get-started-with-the-twilio-connector"></a>Začínáme s konektorem Twilio
Připojení k Twiliu odesílat a přijímat globální IP, služby SMS a MMS zpráv. Twilio můžete:

* Sestavení vaší firmy toku na základě dat, které můžete získat z Twilio. 
* Pomocí akcí, které získají zprávu, seznam zpráv a další. Tyto akce se odpověď a pak proveďte výstup k dispozici pro další akce. Například když získáte novou zprávu Twilio, můžete provést tuto zprávu a jeho použití pracovního postupu služby Service Bus. 

Začněte vytvořením aplikace logiky; v tématu [vytvoření aplikace logiky](../logic-apps/quickstart-create-first-logic-app-workflow.md).

## <a name="create-a-connection-to-twilio"></a>Umožňuje vytvořit připojení k Twiliu
Když přidáte tento konektor aplikace logiky, zadejte následující hodnoty Twilio:

| Vlastnost | Požaduje se | Popis |
| --- | --- | --- |
| ID účtu |Ano |Zadejte své ID účtu Twilio |
| Přístupový token |Ano |Zadejte přístupový token Twilio |

> [!INCLUDE [Steps to create a connection to Twilio](../../includes/connectors-create-api-twilio.md)]
> 
> 

Pokud nemáte přístupový token Twilio, přečtěte si téma [identitu uživatele & přístupové tokeny](https://www.twilio.com/docs/api/chat/guides/identity).

## <a name="connector-specific-details"></a>Podrobnosti o konkrétní konektor

Zobrazit všechny aktivační události a akce definované v swagger a také zobrazit žádné limity v [connector – podrobnosti](/connectors/twilio/).

## <a name="more-connectors"></a>Více konektorů
Přejděte zpět [rozhraní API seznamu](apis-list.md).