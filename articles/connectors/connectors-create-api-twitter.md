---
title: Připojení ke Twitteru z Azure Logic Apps | Dokumentace Microsoftu
description: Automatizace úloh a pracovních postupů, které monitorování a správa tweety a získání dat o sledujících sledované uživatele, jiným uživatelům, časové osy a informace z vašeho účtu na Twitteru s využitím Azure Logic Apps
services: logic-apps
ms.service: logic-apps
ms.suite: integration
author: ecfan
ms.author: estfan
ms.reviewer: klam, LADocs
ms.assetid: 8bce2183-544d-4668-a2dc-9a62c152d9fa
ms.topic: article
tags: connectors
ms.date: 08/25/2018
ms.openlocfilehash: 0fbd89202796cb4543dbecbeee605c9b87cc9d05
ms.sourcegitcommit: fbdfcac863385daa0c4377b92995ab547c51dd4f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/30/2018
ms.locfileid: "50230654"
---
# <a name="monitor-and-manage-twitter-by-using-azure-logic-apps"></a>Monitorování a správa Twitteru s využitím Azure Logic Apps

S Azure Logic Apps a konektor Twitter můžete vytvořit automatizovaných úloh a pracovní postupy, které monitorovat a spravovat data, na kterých vám záleží na Twitteru jako tweetuje sledujících uživatele a použili jste uživatelům, časové osy a další, společně s další akce, například:

* Monitorovat příspěvku a vyhledávání tweetů.
* Získání dat jako sledující, sledovaných uživatelů, časové osy a další.

Můžete použít aktivační události, které odpovědi z vašeho účtu na Twitteru a zpřístupnit výstup dalších akcí. Pomocí akcí, které provádějí úlohy pomocí svého účtu Twitter. Také můžete mít další akce pomocí výstupu z akce Twitter. Například když se objeví nový tweet s určitým hashtagem, mohou odesílat zprávy s konektoru Slack. Pokud se službou logic Apps teprve začínáte, přečtěte si [co je Azure Logic Apps?](../logic-apps/logic-apps-overview.md)

## <a name="prerequisites"></a>Požadavky

* Předplatné Azure. Pokud nemáte předplatné Azure, <a href="https://azure.microsoft.com/free/" target="_blank">zaregistrujte si bezplatný účet Azure</a>. 

* Váš účet a uživatelské přihlašovací údaje k Twitteru

   Vaše přihlašovací údaje autorizaci aplikace logiky k vytvoření připojení a přístup ke svému účtu na Twitteru.

* Základní znalosti o [postupy vytváření aplikací logiky](../logic-apps/quickstart-create-first-logic-app-workflow.md)

* Aplikace logiky, ve které chcete přístup ke svému účtu na Twitteru. Spustit s triggerem Twitter [vytvoření prázdné aplikace logiky](../logic-apps/quickstart-create-first-logic-app-workflow.md). Pokud chcete použít akce Twitter, spusťte svou aplikaci logiky s další aktivační události, například, **opakování** aktivační události.

## <a name="connect-to-twitter"></a>Připojení k Twitteru

[!INCLUDE [Create connection general intro](../../includes/connectors-create-connection-general-intro.md)]

1. Přihlaste se k [webu Azure portal](https://portal.azure.com)a otevřete svou aplikaci logiky v návrháři aplikace logiky, není již otevřete.

1. Zvolte cestu: 

   * V případě prázdné logic apps do vyhledávacího pole zadejte jako filtr "twitter". 
   V seznamu triggerů vyberte trigger, který chcete. 

     -nebo-

   * Pro existující aplikace logiky: 
   
     * V posledním kroku, ve které chcete přidat akci, zvolte **nový krok**. 

       -nebo-

     * Mezi kroky, ve které chcete přidat akci přesuňte ukazatel nad šipku mezi kroky. 
     Vyberte znaménko plus (**+**), který se zobrazí a pak vyberte **přidat akci**.
     
       Do vyhledávacího pole zadejte jako filtr "twitter". 
       V seznamu akcí vyberte požadovanou akci.

1. Pokud se zobrazí výzva k přihlášení k Twitteru, přihlaste se hned, můžete povolit přístup pro aplikaci logiky.

1. Zadejte potřebné podrobnosti o vybrané aktivační události nebo akce a pokračujte v rozvíjení pracovní postup aplikace logiky.

## <a name="examples"></a>Příklady

### <a name="twitter-trigger-when-a-new-tweet-is-posted"></a>Trigger twitteru: když se nový tweet

Tato aktivační událost se spustí pracovní postup aplikace logiky při trigger například rozpozná nový tweet s hashtagem, #Seattle. Takže například nalezený tyto tweety můžete přidat soubor s obsahem tweety do úložiště, například účtu Dropboxu pomocí konektor Dropbox. 

Volitelně může obsahovat podmínku, která oprávněné tweety musí pocházet od uživatelů s alespoň zadaný počet sledujících.

**Příklad organizace**: Tato aktivační událost můžete použít ke sledování tweetů o vaší společnosti a nahrát obsah tweetů do služby SQL database.

### <a name="twitter-action-post-a-tweet"></a>Akce na twitteru: publikování tweetu

Tato akce publikuje tweet, ale akce můžete nastavit tak, aby tento tweet obsahuje obsah tweetů objevila dříve popsaná aktivační události. 

## <a name="connector-reference"></a>Referenční informace ke konektorům

Technické podrobnosti o omezení, akce a triggery, které jsou popsány pomocí konektoru OpenAPI (dříve Swagger) popis, přečtěte si tento konektor [referenční stránce](/connectors/twitterconnector/).

## <a name="get-support"></a>Získat podporu

* Pokud máte dotazy, navštivte [fórum Azure Logic Apps](https://social.msdn.microsoft.com/Forums/en-US/home?forum=azurelogicapps).
* Pokud chcete zanechat své nápady na funkce nebo hlasovat, navštivte [web zpětné vazby od uživatelů Logic Apps](https://aka.ms/logicapps-wish).

## <a name="next-steps"></a>Další postup

* Další informace o dalších [konektory Logic Apps](../connectors/apis-list.md)
