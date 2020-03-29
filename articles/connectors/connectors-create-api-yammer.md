---
title: Připojení k Yammeru z Aplikací Azure Logic Apps
description: Automatizace úloh a pracovních postupů, které monitorují, zveřejňují a spravují zprávy, informační kanály a další funkce v Yammeru pomocí Aplikací Azure Logic Apps
services: logic-apps
ms.suite: integration
ms.reviewer: klam, logicappspm
ms.topic: article
ms.date: 08/25/2018
tags: connectors
ms.openlocfilehash: 194c08d034d44ba0a4472b3b516fc45d1d262d28
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "74789065"
---
# <a name="monitor-and-manage-your-yammer-account-by-using-azure-logic-apps"></a>Sledování a správa účtu Yammeru pomocí Azure Logic Apps

Pomocí aplikací Azure Logic Apps a konektoru Yammeru můžete vytvářet automatizované úkoly a pracovní postupy, které monitorují a spravují zprávy, informační kanály a další funkce v účtu Yammeru, spolu s dalšími akcemi, například:

* Sledujte, kdy se v sledovaných informačních kanálech a skupinách objeví nové zprávy.
* Získejte zprávy, skupiny, sítě, podrobnosti o uživatelích a další.
* Zveřejňujte a máte rádi zprávy.

Můžete použít aktivační události, které získají odpovědi z vašeho účtu Yammer a zpřístupní výstup dalším akcím. Akce, které provádějí úkoly s účtem Yammeru, můžete použít. Můžete mít také jiné akce použít výstup z akcí Yammer. Pokud se například v informačních kanálech nebo skupinách objeví nové zprávy, můžete je sdílet s konektorem Slack. Pokud s aplikacemi logiky tečujete, přečtěte [si, co je Azure Logic Apps?](../logic-apps/logic-apps-overview.md)

## <a name="prerequisites"></a>Požadavky

* Předplatné Azure. Pokud nemáte předplatné Azure, [zaregistrujte si bezplatný účet Azure](https://azure.microsoft.com/free/). 

* Váš účet Yammeru a přihlašovací údaje uživatele

   Vaše přihlašovací údaje autorizují vaši aplikaci logiky k vytvoření připojení a přístupu k účtu Yammeru.

* Základní znalosti o [vytváření aplikací logiky](../logic-apps/quickstart-create-first-logic-app-workflow.md)

* Aplikace logiky, kde chcete získat přístup k účtu Yammer. Chcete-li začít s aktivační událostí Yammeru, [vytvořte prázdnou aplikaci logiky](../logic-apps/quickstart-create-first-logic-app-workflow.md). Chcete-li použít akci Yammer, spusťte aplikaci logiky s jinou aktivační událostí, například aktivační událost **opakování.**

## <a name="connect-to-yammer"></a>Připojení k Yammeru

[!INCLUDE [Create connection general intro](../../includes/connectors-create-connection-general-intro.md)]

1. Přihlaste se k [portálu Azure](https://portal.azure.com)a otevřete aplikaci logiky v Návrháři aplikací logiky, pokud už není otevřená.

1. Zvolte cestu: 

   * U prázdných aplikací logiky zadejte do vyhledávacího pole jako filtr "yammer". 
   V seznamu aktivačních událostí vyberte požadovanou aktivační událost. 

     -nebo-

   * Pro existující aplikace logiky: 
   
     * V posledním kroku, kam chcete přidat akci, zvolte **Nový krok**. 

       -nebo-

     * Mezi kroky, kam chcete přidat akci, přesuňte ukazatel myši na šipku mezi kroky. 
     Zvolte znaménko plus (**+**), které se zobrazí, a pak vyberte Přidat **akci**.
     
       Do vyhledávacího pole zadejte jako filtr "yammer". 
       V seznamu akcí vyberte požadovanou akci.

1. Pokud se zobrazí výzva k přihlášení k Yammeru, přihlaste se hned, abyste mohli povolit přístup.

1. Poskytněte potřebné podrobnosti pro vybranou aktivační událost nebo akci a pokračujte v vytváření pracovního postupu aplikace logiky.

## <a name="connector-reference"></a>Referenční informace ke konektorům

Technické podrobnosti o aktivačních událostech, akcích a limitech, které jsou popsány v popisu openapi (dříve Swagger) konektoru, projděte referenční [stránku konektoru](/connectors/yammer/).

## <a name="get-support"></a>Získat podporu

* Pokud máte dotazy, navštivte [fórum Azure Logic Apps](https://social.msdn.microsoft.com/Forums/en-US/home?forum=azurelogicapps).
* Pokud chcete zanechat své nápady na funkce nebo hlasovat, navštivte [web zpětné vazby od uživatelů Logic Apps](https://aka.ms/logicapps-wish).

## <a name="next-steps"></a>Další kroky

* Další informace o dalších [konektorech logic apps](../connectors/apis-list.md)