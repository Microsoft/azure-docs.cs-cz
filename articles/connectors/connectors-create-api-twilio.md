---
title: Připojení k Twiliu v Azure Logic Apps | Dokumentace Microsoftu
description: Automatizace úloh a pracovních postupů, které spravují globální zprávy SMS, MMS a IP prostřednictvím svého účtu Twilio pomocí Azure Logic Apps
services: logic-apps
ms.service: logic-apps
ms.suite: integration
author: ecfan
ms.author: estfan
ms.reviewer: klam, LADocs
ms.assetid: 43116187-4a2f-42e5-9852-a0d62f08c5fc
ms.topic: article
tags: connectors
ms.date: 08/25/2018
ms.openlocfilehash: fab52236c701f10c8e8e23ac398362ca4583ea06
ms.sourcegitcommit: fbdfcac863385daa0c4377b92995ab547c51dd4f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/30/2018
ms.locfileid: "50228835"
---
# <a name="manage-messages-in-twilio-with-azure-logic-apps"></a>Správa zpráv v Twiliu v Azure Logic Apps

S Azure Logic Apps a konektor Twilio můžete vytvořit automatizovaných úloh a pracovních postupů, které získáte, odesílání a seznam zpráv v Twiliu, mezi které patří globální zprávy SMS, MMS a IP. Tyto akce můžete použít k provádění úloh pomocí svého účtu Twilio. Také můžete mít další akce pomocí výstupu z Twilia akce. Například při přijetí nového e-mailu, můžete odeslání zprávy obsahu s konektorem Slack. Pokud se službou logic Apps teprve začínáte, přečtěte si [co je Azure Logic Apps?](../logic-apps/logic-apps-overview.md)

## <a name="prerequisites"></a>Požadavky

* Předplatné Azure. Pokud nemáte předplatné Azure, <a href="https://azure.microsoft.com/free/" target="_blank">zaregistrujte si bezplatný účet Azure</a>. 

* Z [Twilio](https://www.twilio.com/): 

  * ID vašeho účtu Twilio a [ověřovací token](https://support.twilio.com/hc/en-us/articles/223136027-Auth-Tokens-and-How-to-Change-Them), které můžete vyhledat na svůj řídicí panel Twilio

    Vaše přihlašovací údaje autorizaci aplikace logiky k vytvoření připojení a přístup k vašemu účtu Twilio vaší aplikace logiky. 
    Pokud používáte zkušební verzi účtu Twilio, můžete poslat SMS pouze *ověřit* telefonních čísel.

  * Ověřené telefonní číslo Twilio, který můžete poslat SMS

  * Ověřené telefonní číslo Twilio, které můžou přijímat zprávy SMS

* Základní znalosti o [postupy vytváření aplikací logiky](../logic-apps/quickstart-create-first-logic-app-workflow.md)

* Aplikace logiky, ve které chcete přístup k vašemu účtu Twilio. Použít akci Twilio, spuštění aplikace logiky s jinou aktivační událost, například, **opakování** aktivační události.

## <a name="connect-to-twilio"></a>Připojení k Twiliu

[!INCLUDE [Create connection general intro](../../includes/connectors-create-connection-general-intro.md)]

1. Přihlaste se k [webu Azure portal](https://portal.azure.com)a otevřete svou aplikaci logiky v návrháři aplikace logiky, není již otevřete.

1. Zvolte cestu: 

     * V posledním kroku, ve které chcete přidat akci, zvolte **nový krok**. 

       -nebo-

     * Mezi kroky, ve které chcete přidat akci přesuňte ukazatel nad šipku mezi kroky. 
     Vyberte znaménko plus (**+**), který se zobrazí a pak vyberte **přidat akci**.
     
       Do vyhledávacího pole zadejte jako filtr "twilio". 
       V seznamu akcí vyberte požadovanou akci.

1. Zadejte nezbytné podrobnosti připojení a pak zvolte **vytvořit**:

   * Název, který má používat pro připojení
   * ID vašeho účtu Twilio 
   * Twilio (ověřování) přístupového tokenu

1. Zadejte potřebné podrobnosti pro vybranou akci a pokračujte v rozvíjení pracovní postup aplikace logiky.

## <a name="connector-reference"></a>Referenční informace ke konektorům

Technické podrobnosti o omezení, akce a triggery, které jsou popsány pomocí konektoru OpenAPI (dříve Swagger) popis, přečtěte si tento konektor [referenční stránce](/connectors/twilio/).

## <a name="get-support"></a>Získat podporu

* Pokud máte dotazy, navštivte [fórum Azure Logic Apps](https://social.msdn.microsoft.com/Forums/en-US/home?forum=azurelogicapps).
* Pokud chcete zanechat své nápady na funkce nebo hlasovat, navštivte [web zpětné vazby od uživatelů Logic Apps](https://aka.ms/logicapps-wish).

## <a name="next-steps"></a>Další postup

* Další informace o dalších [konektory Logic Apps](../connectors/apis-list.md)