---
title: Připojení k Slack z Azure Logic Apps | Dokumentace Microsoftu
description: Automatizace úloh a pracovních postupů, které soubory monitorování a Správa kanálů, skupin a zprávy v účtu Slack s využitím Azure Logic Apps
services: logic-apps
ms.service: logic-apps
ms.suite: integration
author: ecfan
ms.author: estfan
ms.reviewer: klam, LADocs
ms.assetid: 234cad64-b13d-4494-ae78-18b17119ba24
ms.topic: article
tags: connectors
ms.date: 08/25/2018
ms.openlocfilehash: 675e37120b06af3add58b564495f22875647a0fa
ms.sourcegitcommit: fbdfcac863385daa0c4377b92995ab547c51dd4f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/30/2018
ms.locfileid: "50230348"
---
# <a name="monitor-and-manage-slack-with-azure-logic-apps"></a>Monitorování a správa Slack s Azure Logic Apps

S Azure Logic Apps a Slack konektor můžete vytvořit automatizovaných úloh a pracovních postupů, které soubory Slack monitorování a Správa kanálů Slack, zprávy, skupiny a tak dále, například:

* Sleduje, vytvoří se nové soubory.
* Vytváření, výpisu a připojte se k kanály 
* Odesílání zpráv.
* Vytváření skupin a sada Nerušit.

Můžete použít aktivační události, které odpovědi ze svého účtu Slack a zpřístupnit výstup dalších akcí. Pomocí akcí, které provádějí úlohy pomocí svého účtu Slack. Také můžete mít další akce pomocí výstupu z akce Slack. Například když je vytvořen nový soubor, můžete odesílat e-mailu s konektorem Office 365 Outlook. Pokud se službou logic Apps teprve začínáte, přečtěte si [co je Azure Logic Apps?](../logic-apps/logic-apps-overview.md)

## <a name="prerequisites"></a>Požadavky

* Předplatné Azure. Pokud nemáte předplatné Azure, <a href="https://azure.microsoft.com/free/" target="_blank">zaregistrujte si bezplatný účet Azure</a>. 

* Vaše [Slack](https://slack.com/) přihlašovací údaje pro účet a uživatele

  Vaše přihlašovací údaje autorizaci aplikace logiky k vytvoření připojení a přístup k vašemu účtu Slack.

* Základní znalosti o [postupy vytváření aplikací logiky](../logic-apps/quickstart-create-first-logic-app-workflow.md)

* Aplikace logiky, ve které chcete přístup k vašemu účtu Slack. Spustit s triggerem Slack [vytvoření prázdné aplikace logiky](../logic-apps/quickstart-create-first-logic-app-workflow.md). Pokud chcete použít Slack akci, spusťte svou aplikaci logiky s triggerem, například Slack aktivační události nebo jiné aktivační události, jako **opakování** aktivační události.

## <a name="connect-to-slack"></a>Připojte se k Slack

[!INCLUDE [Create connection general intro](../../includes/connectors-create-connection-general-intro.md)]

1. Přihlaste se k [webu Azure portal](https://portal.azure.com)a otevřete svou aplikaci logiky v návrháři aplikace logiky, není již otevřete.

1. V případě prázdné logic apps do vyhledávacího pole zadejte jako filtr "slack". V seznamu triggerů vyberte trigger, který chcete. 

   -nebo-

   Pro existující aplikace logiky v posledním kroku, ve které chcete přidat akci, zvolte **nový krok**. 
   Do vyhledávacího pole zadejte jako filtr "slack". 
   V seznamu akcí vyberte požadovanou akci.

   Přidání akce mezi kroky, přesuňte ukazatel nad šipku mezi kroky. 
   Vyberte znaménko plus (**+**), který se zobrazí a pak vyberte **přidat akci**.

1. Pokud se zobrazí výzva k přihlášení na Slack, přihlaste se do pracovního prostoru Slack. 

   ![Přihlaste se k Slack pracovního prostoru](./media/connectors-create-api-slack/slack-sign-in-workspace.png)

1. Povolit přístup pro aplikaci logiky.

   ![Autorizace přístupu k Slack](./media/connectors-create-api-slack/slack-authorize-access.png)

1. Zadejte potřebné podrobnosti o vybrané aktivační události nebo akce. Chcete-li pokračovat ve vytváření pracovní postup aplikace logiky, přidáte další akce.

## <a name="connector-reference"></a>Referenční informace ke konektorům

Technické podrobnosti o omezení, akce a triggery, které jsou popsány pomocí konektoru OpenAPI (dříve Swagger) popis, přečtěte si tento konektor [referenční stránce](/connectors/slack/).

## <a name="get-support"></a>Získat podporu

* Pokud máte dotazy, navštivte [fórum Azure Logic Apps](https://social.msdn.microsoft.com/Forums/en-US/home?forum=azurelogicapps).
* Pokud chcete zanechat své nápady na funkce nebo hlasovat, navštivte [web zpětné vazby od uživatelů Logic Apps](https://aka.ms/logicapps-wish).

## <a name="next-steps"></a>Další postup

* Další informace o dalších [konektory Logic Apps](../connectors/apis-list.md)
