---
title: Připojení k Wunderlistu z Azure Logic Apps | Dokumentace Microsoftu
description: Automatizace úloh a pracovních postupů, které monitorování a správa seznamů, úloh, připomenutí a informace ve vašem účtu Wunderlistu s využitím Azure Logic Apps
services: logic-apps
ms.service: logic-apps
ms.suite: integration
author: ecfan
ms.author: estfan
ms.reviewer: klam, LADocs
ms.assetid: e4773ecf-3ad3-44b4-a1b5-ee5f58baeadd
ms.topic: article
tags: connectors
ms.date: 08/25/2018
ms.openlocfilehash: e3570ab1227ca388ac62bffdc74bb68b1ddc41d1
ms.sourcegitcommit: fbdfcac863385daa0c4377b92995ab547c51dd4f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/30/2018
ms.locfileid: "50230155"
---
# <a name="monitor-and-manage-wunderlist-by-using-azure-logic-apps"></a>Monitorování a správa Wunderlistu s využitím Azure Logic Apps

S Azure Logic Apps a konektor Wunderlist můžete vytvořit automatizovaných úloh a pracovních postupů, které monitorovat a spravovat seznamy úkolů, úkolů, připomenutí a informace ve vašem účtu Wunderlistu, společně s další akce, například:

* Sledování nové úkoly vytvořené, při splnění úkolů nebo dochází připomenutí.
* Vytvoření a správa seznamů, poznámky, úkoly, dílčí úkoly a další.
* Nastavení připomenutí.
* Získáte seznamy, úkoly, dílčí úkoly, připomenutí, souborů, poznámky, komentáře a další.

[Wunderlist](https://www.wunderlist.com/) je služba, která umožňuje plánovat, spravovat a dokončení projekty, seznamů úkolů a úkoly – na libovolném zařízení a kdekoli. Můžete použít aktivační události, které odpovědi z vašeho účtu Wunderlistu a zpřístupnit výstup dalších akcí. Pomocí akcí, které provádějí úlohy s vaším účtem Wunderlistu. Také můžete mít další akce pomocí výstupu z akce Wunderlistu. Například po vypršení platnosti nové úkoly se může publikovat zprávy s konektorem Slack. Pokud se službou logic Apps teprve začínáte, přečtěte si [co je Azure Logic Apps?](../logic-apps/logic-apps-overview.md)

## <a name="prerequisites"></a>Požadavky

* Předplatné Azure. Pokud nemáte předplatné Azure, <a href="https://azure.microsoft.com/free/" target="_blank">zaregistrujte si bezplatný účet Azure</a>. 

* Váš účet a uživatelské přihlašovací údaje k Wunderlistu

   Vaše přihlašovací údaje autorizaci aplikace logiky k vytvoření připojení a přístup k vašemu účtu Wunderlistu.

* Základní znalosti o [postupy vytváření aplikací logiky](../logic-apps/quickstart-create-first-logic-app-workflow.md)

* Aplikace logiky, ve které chcete přístup k vašemu účtu Yammeru. Spustit s triggerem Wunderlistu [vytvoření prázdné aplikace logiky](../logic-apps/quickstart-create-first-logic-app-workflow.md). Pokud chcete používat Wunderlist akce, aplikaci logiky spustit s jinou aktivační událost, například, **opakování** aktivační události.

## <a name="connect-to-wunderlist"></a>Připojení k Wunderlistu

[!INCLUDE [Create connection general intro](../../includes/connectors-create-connection-general-intro.md)]

1. Přihlaste se k [webu Azure portal](https://portal.azure.com)a otevřete svou aplikaci logiky v návrháři aplikace logiky, není již otevřete.

1. Zvolte cestu: 

   * V případě prázdné logic apps do vyhledávacího pole zadejte jako filtr "wunderlistu". 
   V seznamu triggerů vyberte trigger, který chcete. 

     -nebo-

   * Pro existující aplikace logiky: 
   
     * V posledním kroku, ve které chcete přidat akci, zvolte **nový krok**. 

       -nebo-

     * Mezi kroky, ve které chcete přidat akci přesuňte ukazatel nad šipku mezi kroky. 
     Vyberte znaménko plus (**+**), který se zobrazí a pak vyberte **přidat akci**.
     
       Do vyhledávacího pole zadejte jako filtr "wunderlistu". 
       V seznamu akcí vyberte požadovanou akci.

1. Pokud se zobrazí výzva k přihlášení do Wunderlistu, přihlaste se hned, můžete povolit přístup.

1. Zadejte potřebné podrobnosti o vybrané aktivační události nebo akce a pokračujte v rozvíjení pracovní postup aplikace logiky.

## <a name="connector-reference"></a>Referenční informace ke konektorům

Technické podrobnosti o omezení, akce a triggery, které jsou popsány pomocí konektoru OpenAPI (dříve Swagger) popis, přečtěte si tento konektor [referenční stránce](/connectors/wunderlist/).

## <a name="get-support"></a>Získat podporu

* Pokud máte dotazy, navštivte [fórum Azure Logic Apps](https://social.msdn.microsoft.com/Forums/en-US/home?forum=azurelogicapps).
* Pokud chcete zanechat své nápady na funkce nebo hlasovat, navštivte [web zpětné vazby od uživatelů Logic Apps](https://aka.ms/logicapps-wish).

## <a name="next-steps"></a>Další postup

* Další informace o dalších [konektory Logic Apps](../connectors/apis-list.md)