---
title: Připojte se k libovolný koncový bod protokolu HTTP s Azure Logic Apps | Dokumentace Microsoftu
description: Automatizace úloh a pracovních postupů, které komunikují s jakýmkoli koncovým bodem HTTP pomocí Azure Logic Apps
services: logic-apps
ms.service: logic-apps
ms.suite: integration
author: ecfan
ms.author: estfan
ms.reviewer: klam, LADocs
ms.assetid: e11c6b4d-65a5-4d2d-8e13-38150db09c0b
ms.topic: article
tags: connectors
ms.date: 08/25/2018
ms.openlocfilehash: 1c30f77c061ec25c88186caee3f60e65f3afb3de
ms.sourcegitcommit: fbdfcac863385daa0c4377b92995ab547c51dd4f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/30/2018
ms.locfileid: "50232864"
---
# <a name="call-http-or-https-endpoints-with-azure-logic-apps"></a>Volání koncových bodů HTTP nebo HTTPS s Azure Logic Apps

S Azure Logic Apps a konektoru protokol HTTP (Hypertext Transfer) můžete automatizovat pracovní postupy, které komunikují s libovolný koncový bod HTTP nebo HTTPS díky vytváření aplikací logiky. Můžete například monitorovat koncový bod služby pro váš web. Pokud k události dojde na tomto koncovém, jako je například webu směrem dolů, událost aktivuje pracovní postup aplikace logiky a spustí zadané akce. 

HTTP trigger můžete použít jako první krok v váš pracovní postup pro kontrolu nebo *dotazování* koncový bod v pravidelných intervalech. Na každé kontrole, odešle aktivační událost volání nebo *požadavek* ke koncovému bodu. Odpověď koncový bod určuje, jestli se spouští pracovní postup aplikace logiky. Aktivační událost předá jakýkoli obsah z odpovědi na akce ve vaší aplikaci logiky. 

Akce HTTP můžete použít jako další krok v pracovním postupu pro volání koncového bodu, pokud chcete. Odpověď koncový bod určuje, jak spustit zbývající akce pracovního postupu.

Pokud se službou logic Apps teprve začínáte, přečtěte si [co je Azure Logic Apps?](../logic-apps/logic-apps-overview.md)

## <a name="prerequisites"></a>Požadavky

* Předplatné Azure. Pokud nemáte předplatné Azure, <a href="https://azure.microsoft.com/free/" target="_blank">zaregistrujte si bezplatný účet Azure</a>. 

* Adresa URL pro koncový bod cílové chcete volat 

* Základní znalosti o [postupy vytváření aplikací logiky](../logic-apps/quickstart-create-first-logic-app-workflow.md)

* Pokud chcete volat cílový koncový bod spustit pomocí triggeru protokolu HTTP, aplikace logiky [vytvoření prázdné aplikace logiky](../logic-apps/quickstart-create-first-logic-app-workflow.md). Pokud chcete použít akce HTTP, spusťte aplikaci logiky s triggerem.

## <a name="add-http-trigger"></a>Přidání triggeru HTTP

1. Přihlaste se k [webu Azure portal](https://portal.azure.com)a otevřete váš prázdné aplikace logiky v návrháři aplikace logiky, není již otevřete.

1. Do vyhledávacího pole zadejte jako filtr "http". V seznamu triggerů vyberte **HTTP** aktivační události. 

   ![Výběr triggeru HTTP](./media/connectors-native-http/select-http-trigger.png)

1. Zadejte [parametry a hodnoty triggeru HTTP](../logic-apps/logic-apps-workflow-actions-triggers.md##http-trigger) chcete zahrnout při volání do cílového koncového bodu. Nastavení opakování pro jak často chcete, aby se aktivační událost zkontrolujte cílový koncový bod.

   ![Zadejte parametry triggeru HTTP](./media/connectors-native-http/http-trigger-parameters.png)

   Další informace o aktivační události HTTP, parametry a hodnoty, najdete v části [aktivační události a akce referenční typy](../logic-apps/logic-apps-workflow-actions-triggers.md##http-trigger).

1. Pokračujte v sestavování pracovního postupu aplikace logiky s akcemi, které běží na aktivaci triggeru.

## <a name="add-http-action"></a>Přidání akce HTTP

[!INCLUDE [Create connection general intro](../../includes/connectors-create-connection-general-intro.md)]

1. Přihlaste se k [webu Azure portal](https://portal.azure.com)a otevřete svou aplikaci logiky v návrháři aplikace logiky, není již otevřete.

1. V posledním kroku, ve které chcete přidat akci HTTP, zvolte **nový krok**. 

   V tomto příkladu se spustí aplikace logiky s triggerem HTTP jako první krok.

1. Do vyhledávacího pole zadejte jako filtr "http". V seznamu akcí vyberte **HTTP** akce.

   ![Výběr akce HTTP](./media/connectors-native-http/select-http-action.png)

   Přidání akce mezi kroky, přesuňte ukazatel nad šipku mezi kroky. 
   Vyberte znaménko plus (**+**), který se zobrazí a pak vyberte **přidat akci**.

1. Zadejte [akce HTTP parametry a hodnoty](../logic-apps/logic-apps-workflow-actions-triggers.md##http-action) chcete zahrnout při volání do cílového koncového bodu. 

   ![Zadejte parametry akce HTTP](./media/connectors-native-http/http-action-parameters.png)

1. Jakmile budete hotovi, nezapomeňte že uložení aplikace logiky. Na panelu nástrojů návrháře zvolte **Uložit**. 

## <a name="authentication"></a>Authentication

Pokud chcete nastavit ověřování, zvolte **zobrazit pokročilé možnosti** uvnitř triggeru nebo akce. Další informace o typech ověřování k dispozici pro protokol HTTP triggerů a akcí najdete v tématu [aktivační události a akce referenční typy](../logic-apps/logic-apps-workflow-actions-triggers.md#connector-authentication).

## <a name="get-support"></a>Získat podporu

* Pokud máte dotazy, navštivte [fórum Azure Logic Apps](https://social.msdn.microsoft.com/Forums/en-US/home?forum=azurelogicapps).
* Pokud chcete zanechat své nápady na funkce nebo hlasovat, navštivte [web zpětné vazby od uživatelů Logic Apps](https://aka.ms/logicapps-wish).

## <a name="next-steps"></a>Další postup

* Další informace o dalších [konektory Logic Apps](../connectors/apis-list.md)
