---
title: Připojení k Trello z Azure Logic Apps | Dokumentace Microsoftu
description: Automatizace úloh a pracovních postupů, které monitorovat a spravovat seznamy, tabulí a karty v Trellu projektech s využitím Azure Logic Apps
services: logic-apps
ms.service: logic-apps
ms.suite: integration
author: ecfan
ms.author: estfan
ms.reviewer: klam, LADocs
ms.assetid: fe7a4377-5c24-4f72-ab1a-6d9d23e8d895
ms.topic: article
tags: connectors
ms.date: 08/25/2018
ms.openlocfilehash: 31357fa313cfa31c0f8a90c0f7722f627e4394d1
ms.sourcegitcommit: fbdfcac863385daa0c4377b92995ab547c51dd4f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/30/2018
ms.locfileid: "50229328"
---
# <a name="monitor-and-manage-trello-with-azure-logic-apps"></a>Monitorování a správa Trello s Azure Logic Apps

S Azure Logic Apps a konektor Trello můžete vytvořit automatizovaných úloh a pracovních postupů, které monitorovat a spravovat seznam Trello, karty, panely, členové týmu a tak dále, například:

* Monitorování při přidání nové karty do tabulí a seznamy. 
* Vytvoření, získání a spravovat panely karet a seznamy.
* Přidejte komentáře a členy na karty.
* Seznam panely, panel popisky, karty na panely, komentáře karta, Karta Členové, členové týmu a týmů, kterých jste členem. 
* Získejte týmy.

Můžete použít aktivační události, které odpovědi z vašeho účtu Trello a zpřístupnit výstup dalších akcí. Pomocí akcí, které provádějí úlohy s vaším účtem Trello. Také můžete mít další akce pomocí výstupu z akce Trello. Například při přidání nové karty na panelu nebo seznam, můžete odesílat zprávy s konektoru Slack. Pokud se službou logic Apps teprve začínáte, přečtěte si [co je Azure Logic Apps?](../logic-apps/logic-apps-overview.md)

## <a name="prerequisites"></a>Požadavky

* Předplatné Azure. Pokud nemáte předplatné Azure, <a href="https://azure.microsoft.com/free/" target="_blank">zaregistrujte si bezplatný účet Azure</a>. 

* Váš účet a uživatelské přihlašovací údaje k Trellu

  Vaše přihlašovací údaje autorizaci aplikace logiky k vytvoření připojení a přístup k vašemu účtu Trello.

* Základní znalosti o [postupy vytváření aplikací logiky](../logic-apps/quickstart-create-first-logic-app-workflow.md)

* Aplikace logiky, ve které chcete přístup k vašemu účtu Trello. Spustit s triggerem Trello [vytvoření prázdné aplikace logiky](../logic-apps/quickstart-create-first-logic-app-workflow.md). Použít akci Trello, spuštění aplikace logiky s triggerem, například, **opakování** aktivační události.

## <a name="connect-to-trello"></a>Připojení k Trello

[!INCLUDE [Create connection general intro](../../includes/connectors-create-connection-general-intro.md)]

1. Přihlaste se k [webu Azure portal](https://portal.azure.com)a otevřete svou aplikaci logiky v návrháři aplikace logiky, není již otevřete.

1. V případě prázdné logic apps do vyhledávacího pole zadejte jako filtr "trello". V seznamu triggerů vyberte trigger, který chcete. 

   -nebo-

   Pro existující aplikace logiky v posledním kroku, ve které chcete přidat akci, zvolte **nový krok**. 
   Do vyhledávacího pole zadejte jako filtr "trello". 
   V seznamu akcí vyberte požadovanou akci.

   Přidání akce mezi kroky, přesuňte ukazatel nad šipku mezi kroky. 
   Vyberte znaménko plus (**+**), který se zobrazí a pak vyberte **přidat akci**.

1. Pokud se zobrazí výzva k přihlášení k Trellu, autorizaci přístupu pro aplikaci logiky a přihlaste se.

1. Zadejte potřebné podrobnosti o vybrané aktivační události nebo akce a pokračujte v rozvíjení pracovní postup aplikace logiky.

## <a name="connector-reference"></a>Referenční informace ke konektorům

Technické podrobnosti o omezení, akce a triggery, které jsou popsány pomocí konektoru OpenAPI (dříve Swagger) popis, přečtěte si tento konektor [referenční stránce](/connectors/trello/).

## <a name="get-support"></a>Získat podporu

* Pokud máte dotazy, navštivte [fórum Azure Logic Apps](https://social.msdn.microsoft.com/Forums/en-US/home?forum=azurelogicapps).
* Pokud chcete zanechat své nápady na funkce nebo hlasovat, navštivte [web zpětné vazby od uživatelů Logic Apps](https://aka.ms/logicapps-wish).

## <a name="next-steps"></a>Další postup

* Další informace o dalších [konektory Logic Apps](../connectors/apis-list.md)