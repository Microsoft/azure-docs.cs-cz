---
title: Připojení k Yammeru z Azure Logic Apps | Dokumentace Microsoftu
description: Automatizace úloh a pracovní postupy monitorování, publikovat a spravovat zprávy, informační kanály a další v Yammeru pomocí Azure Logic Apps
services: logic-apps
ms.service: logic-apps
ms.suite: integration
author: ecfan
ms.author: estfan
ms.reviewer: klam, LADocs
ms.assetid: b5ae0827-fbb3-45ec-8f45-ad1cc2e7eccc
ms.topic: article
tags: connectors
ms.date: 08/25/2018
ms.openlocfilehash: 41855d6e562ddbb78df5d1d8794127e1064cc2ca
ms.sourcegitcommit: ebb460ed4f1331feb56052ea84509c2d5e9bd65c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/24/2018
ms.locfileid: "42918291"
---
# <a name="monitor-and-manage-your-yammer-account-by-using-azure-logic-apps"></a>Monitorovat a spravovat váš účet Yammeru pomocí Azure Logic Apps

S Azure Logic Apps a konektor Yammer můžete vytvořit automatizovaných úloh a pracovních postupů, které monitorování a správa zprávy, informační kanály a informace ve vašem účtu Yammeru, společně s další akce, například:

* Sleduje nové zprávy zobrazí ve sledované kanály a skupiny.
* Získání zprávy, skupiny, sítě, uživatelů, podrobnosti a další.
* Publikovat a zpráv.

Můžete použít aktivační události, které odpovědi z vašeho účtu Yammeru a zpřístupnit výstup dalších akcí. Pomocí akcí, které provádějí úlohy s vaším účtem Yammeru. Také můžete mít další akce pomocí výstupu z akce Yammeru. Například při nové zprávy se zobrazují v informačních kanálů nebo skupin, můžete nasdílet zprávy Slack konektoru. Pokud se službou logic Apps teprve začínáte, přečtěte si [co je Azure Logic Apps?](../logic-apps/logic-apps-overview.md)

## <a name="prerequisites"></a>Požadavky

* Předplatné Azure. Pokud nemáte předplatné Azure, <a href="https://azure.microsoft.com/free/" target="_blank">zaregistrujte si bezplatný účet Azure</a>. 

* Váš účet a uživatelské přihlašovací údaje k Yammeru

   Vaše přihlašovací údaje autorizaci aplikace logiky k vytvoření připojení a přístup k vašemu účtu Yammeru.

* Základní znalosti o [postupy vytváření aplikací logiky](../logic-apps/quickstart-create-first-logic-app-workflow.md)

* Aplikace logiky, ve které chcete přístup k vašemu účtu Yammeru. Spustit s triggerem Yammeru [vytvoření prázdné aplikace logiky](../logic-apps/quickstart-create-first-logic-app-workflow.md). Použít akci Yammeru, začněte svou aplikaci logiky s další trigger, například, **opakování** aktivační události.

## <a name="connect-to-yammer"></a>Připojení k Yammeru

[!INCLUDE [Create connection general intro](../../includes/connectors-create-connection-general-intro.md)]

1. Přihlaste se k [webu Azure portal](https://portal.azure.com)a otevřete svou aplikaci logiky v návrháři aplikace logiky, není již otevřete.

1. Zvolte cestu: 

   * V případě prázdné logic apps do vyhledávacího pole zadejte jako filtr "yammer". 
   V seznamu triggerů vyberte trigger, který chcete. 

     -nebo-

   * Pro existující aplikace logiky: 
   
     * V posledním kroku, ve které chcete přidat akci, zvolte **nový krok**. 

       -nebo-

     * Mezi kroky, ve které chcete přidat akci přesuňte ukazatel nad šipku mezi kroky. 
     Vyberte znaménko plus (**+**), který se zobrazí a pak vyberte **přidat akci**.
     
       Do vyhledávacího pole zadejte jako filtr "yammer". 
       V seznamu akcí vyberte požadovanou akci.

1. Pokud se zobrazí výzva k přihlášení na Yammer, přihlaste se teď přihlašování istrovat hned, můžete povolit přístup.

1. Zadejte potřebné podrobnosti o vybrané aktivační události nebo akce a pokračujte v rozvíjení pracovní postup aplikace logiky.

## <a name="connector-reference"></a>Referenční informace ke konektorům

Technické podrobnosti o omezení, akce a triggery, které jsou popsány pomocí konektoru OpenAPI (dříve Swagger) popis, přečtěte si tento konektor [referenční stránce](/connectors/yammer/).

## <a name="get-support"></a>Získat podporu

* Pokud máte dotazy, navštivte [fórum Azure Logic Apps](https://social.msdn.microsoft.com/Forums/en-US/home?forum=azurelogicapps).
* Pokud chcete zanechat své nápady na funkce nebo hlasovat, navštivte [web zpětné vazby od uživatelů Logic Apps](http://aka.ms/logicapps-wish).

## <a name="next-steps"></a>Další postup

* Další informace o dalších [konektory Logic Apps](../connectors/apis-list.md)