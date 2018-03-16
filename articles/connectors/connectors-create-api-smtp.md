---
title: Konektor SMTP v Azure Logic Apps | Microsoft Docs
description: "Vytvoření aplikace logiky službou Azure App service. Připojení k SMTP pro odeslání e-mailu."
services: logic-apps
documentationcenter: .net,nodejs,java
author: ecfan
manager: anneta
editor: 
tags: connectors
ms.assetid: d4141c08-88d7-4e59-a757-c06d0dc74300
ms.service: logic-apps
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: integration
ms.date: 07/15/2016
ms.author: estfan; ladocs
ms.openlocfilehash: 9bf7c9b7c3e775ab03b071d13d792f4b2d8fb3e3
ms.sourcegitcommit: 8aab1aab0135fad24987a311b42a1c25a839e9f3
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/16/2018
---
# <a name="get-started-with-the-smtp-connector"></a>Začínáme s konektor SMTP
Připojení k SMTP pro odeslání e-mailu.

Chcete-li použít [všechny konektory](apis-list.md), musíte nejprve vytvořit aplikaci logiky. Abyste mohli začít podle [vytvoření aplikace logiky teď](../logic-apps/quickstart-create-first-logic-app-workflow.md).

## <a name="connect-to-smtp"></a>Připojení k SMTP
Než se aplikace logiky k jakékoli služby, musíte nejprve vytvořit *připojení* ke službě. A [připojení](connectors-overview.md) poskytuje připojení mezi aplikace logiky a jiné služby. Například pro připojení k SMTP, musíte nejprve serveru SMTP *připojení*. Vytvoření připojení, zadejte přihlašovací údaje, které standardně používáte k přístupu ke službě, ke kterým se připojujete. Ano v příkladu SMTP zadejte přihlašovací údaje k název připojení, adresu serveru SMTP a přihlašovací informace uživatele vytvořit připojení k SMTP.  

### <a name="create-a-connection-to-smtp"></a>Umožňuje vytvořit připojení k SMTP
> [!INCLUDE [Steps to create a connection to SMTP](../../includes/connectors-create-api-smtp.md)]
> 
> 

## <a name="use-an-smtp-trigger"></a>Aktivační událost pomocí serveru SMTP
Aktivační událost je událost, která můžete použít ke spuštění pracovního postupu definované v aplikaci logiky. [Další informace o aktivační události](../logic-apps/logic-apps-overview.md#logic-app-concepts).

V tomto příkladu SMTP nemá aktivační událost své vlastní. Ano, použít **Salesforce – když je vytvořen objekt** aktivační události. Tento aktivační událost se aktivuje, když je vytvořen nový objekt v Salesforce. V tomto příkladu se má nastavit tak, aby nové zájemce pokaždé, když je vytvořen v Salesforce, *odeslání e-mailu* akci dojde konektor SMTP pomocí upozornění při vytváření nové zájemce.

1. Zadejte *salesforce* do vyhledávacího pole v designeru aplikace logiky zvolte **Salesforce – když je vytvořen objekt** aktivační události.  
   ![](../../includes/media/connectors-create-api-salesforce/trigger-1.png)  
2. **Když je vytvořen objekt** ovládací prvek je zobrazen.
   ![](../../includes/media/connectors-create-api-salesforce/trigger-2.png)  
3. Vyberte **typ objektu** vyberte *vést* ze seznamu objektů. V tomto kroku vytvoříte aktivační událost, která upozorní svou aplikaci logiky vždy, když se vytvoří nové zájemce v Salesforce.  
   ![](../../includes/media/connectors-create-api-salesforce/trigger3.png)  
4. Aktivační událost byla vytvořena.  
   ![](../../includes/media/connectors-create-api-salesforce/trigger-4.png)  

## <a name="use-an-smtp-action"></a>Použít akci SMTP
Akce je operace prováděné definované v aplikaci logiky pracovního postupu. [Další informace o akcích](../logic-apps/logic-apps-overview.md#logic-app-concepts).

Teď, když byla přidána aktivační událost, pomocí následujících kroků přidáte SMTP akci, která nastane, když je v Salesforce vytvoří nové zájemce.

1. Vyberte **+ nový krok** přidat akci chcete provést, když se vytvoří nové zájemce.  
   ![](../../includes/media/connectors-create-api-salesforce/trigger4.png)  
2. Vyberte **přidat akci**. Otevře se tato, které chcete do vyhledávacího pole, kde můžete vyhledat všechny akce můžete provést.  
   ![](../../includes/media/connectors-create-api-smtp/using-smtp-action-2.png)  
3. Zadejte *smtp* k vyhledání akcí souvisejících s SMTP.  
4. Vyberte **SMTP - odeslat E-mail** jako pro případ, kdy se vytvoří nové zájemce. Otevře se řídicí blok akce. Budete mít k navázání připojení k smtp v návrháře bloku, pokud jste tak dosud neučinili dříve.  
   ![](../../includes/media/connectors-create-api-smtp/smtp-2.png)    
5. Zadejte příslušné informace požadované e-mailu na **SMTP - odeslat E-mail** bloku.  
   ![](../../includes/media/connectors-create-api-smtp/using-smtp-action-4.PNG)  
6. Uložte práci aktivovat pracovní postup.  

## <a name="connector-specific-details"></a>Podrobnosti o konkrétní konektor

Zobrazit všechny aktivační události a akce definované v swagger a také zobrazit žádné limity v [connector – podrobnosti](/connectors/smtpconnector/).

## <a name="more-connectors"></a>Více konektorů
Přejděte zpět [rozhraní API seznamu](apis-list.md).