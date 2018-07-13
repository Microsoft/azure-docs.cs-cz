---
title: Přidejte konektor Twilio v Azure Logic apps | Dokumentace Microsoftu
description: Přehled konektor Twilio s parametry rozhraní REST API
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
ms.sourcegitcommit: 0a84b090d4c2fb57af3876c26a1f97aac12015c5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/11/2018
ms.locfileid: "38652001"
---
# <a name="get-started-with-the-twilio-connector"></a>Začínáme s konektor Twilio
Připojení k Twiliu odesílat a přijímat globální zprávy SMS, MMS a IP. S platformou Twilio můžete:

* Vytvoření obchodní toku na základě dat, které získáte ze služby Twilio. 
* Pomocí akcí, které získat zprávu, seznam zpráv a další. Tyto akce získat odpověď a poté zpřístupní výstup pro další akce. Například když dostanete nové zprávy Twilio, můžete využít tuto zprávu a použije pracovního postupu služby Service Bus. 

Začněte vytvořením aplikace logiky; Zobrazit [vytvoření aplikace logiky](../logic-apps/quickstart-create-first-logic-app-workflow.md).

## <a name="create-a-connection-to-twilio"></a>Vytvoření připojení k Twiliu
Když přidáte tento konektor logic Apps, zadejte následující hodnoty Twilio:

| Vlastnost | Požaduje se | Popis |
| --- | --- | --- |
| ID účtu |Ano |Zadejte své ID účtu Twilio |
| Přístupový token |Ano |Zadejte váš přístupový token pro Twilio |

> [!INCLUDE [Steps to create a connection to Twilio](../../includes/connectors-create-api-twilio.md)]
> 
> 

Pokud nemáte k dispozici přístupový token Twilio, přečtěte si téma [identitu uživatele a přístupové tokeny](https://www.twilio.com/docs/api/chat/guides/identity).

## <a name="connector-specific-details"></a>Podrobné informace specifické pro konektor

Zobrazit všechny aktivační události a akce definované ve swaggeru a také zjistit žádné omezení [podrobnosti o konektoru](/connectors/twilio/).

## <a name="more-connectors"></a>Více konektorů
Přejděte zpět [rozhraní API seznamu](apis-list.md).