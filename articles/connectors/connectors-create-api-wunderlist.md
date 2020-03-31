---
title: Připojení k Wunderlist u aplikací Azure Logic Apps
description: Automatizace úloh a pracovních postupů, které monitorují a spravují seznamy, úkoly, připomenutí a další funkce v účtu Wunderlist pomocí Azure Logic Apps
services: logic-apps
ms.suite: integration
ms.reviewer: klam, logicappspm
ms.topic: article
ms.date: 08/25/2018
tags: connectors
ms.openlocfilehash: 5ac13595bd77238aaede5fa3bdc3a35ef69e8504
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "74789116"
---
# <a name="monitor-and-manage-wunderlist-by-using-azure-logic-apps"></a>Sledování a správa Wunderlistu pomocí Azure Logic Apps

Pomocí aplikací Azure Logic Apps a konektoru Wunderlist můžete vytvářet automatizované úkoly a pracovní postupy, které monitorují a spravují seznamy úkolů, úkoly, připomenutí a další v účtu Wunderlist, spolu s dalšími akcemi, například:

* Sledujte, kdy se vytvoří nové úkoly, kdy jsou úkoly splatné nebo kdy dojde k připomenutí.
* Vytvářejte a spravujte seznamy, poznámky, úkoly, dílčí úkoly a další.
* Nastavte připomenutí.
* Získejte seznamy, úkoly, dílčí úkoly, připomenutí, soubory, poznámky, komentáře a další.

[Wunderlist](https://www.wunderlist.com/) je služba, která vám pomůže plánovat, spravovat a dokončit vaše projekty, seznamy úkolů a úkoly – na libovolném zařízení a kdekoli. Můžete použít aktivační události, které získají odpovědi z vašeho účtu Wunderlist a zpřístupní výstup dalším akcím. Můžete použít akce, které provádějí úkoly s vaším účtem Wunderlist. Můžete také mít jiné akce použít výstup z Wunderlist akce. Například když jsou splatné nové úkoly, můžete posílat zprávy pomocí konektoru Slack. Pokud s aplikacemi logiky tečujete, přečtěte [si, co je Azure Logic Apps?](../logic-apps/logic-apps-overview.md)

## <a name="prerequisites"></a>Požadavky

* Předplatné Azure. Pokud nemáte předplatné Azure, [zaregistrujte si bezplatný účet Azure](https://azure.microsoft.com/free/). 

* Váš účet Wunderlist a pověření uživatele

   Vaše přihlašovací údaje autorizují aplikaci logiky k vytvoření připojení a přístupu k vašemu účtu Wunderlist.

* Základní znalosti o [vytváření aplikací logiky](../logic-apps/quickstart-create-first-logic-app-workflow.md)

* Aplikace logiky, kde chcete získat přístup k účtu Yammer. Chcete-li začít s aktivační událostí Wunderlist, [vytvořte prázdnou aplikaci logiky](../logic-apps/quickstart-create-first-logic-app-workflow.md). Chcete-li použít akci Wunderlist, spusťte aplikaci logiky s jinou aktivační událostí, například aktivační událost **opakování.**

## <a name="connect-to-wunderlist"></a>Připojení k Wunderlistu

[!INCLUDE [Create connection general intro](../../includes/connectors-create-connection-general-intro.md)]

1. Přihlaste se k [portálu Azure](https://portal.azure.com)a otevřete aplikaci logiky v Návrháři aplikací logiky, pokud už není otevřená.

1. Zvolte cestu: 

   * U prázdných aplikací logiky zadejte do vyhledávacího pole jako filtr "wunderlist". 
   V seznamu aktivačních událostí vyberte požadovanou aktivační událost. 

     -nebo-

   * Pro existující aplikace logiky: 
   
     * V posledním kroku, kam chcete přidat akci, zvolte **Nový krok**. 

       -nebo-

     * Mezi kroky, kam chcete přidat akci, přesuňte ukazatel myši na šipku mezi kroky. 
     Zvolte znaménko plus (**+**), které se zobrazí, a pak vyberte Přidat **akci**.
     
       Do vyhledávacího pole zadejte jako filtr "wunderlist". 
       V seznamu akcí vyberte požadovanou akci.

1. Pokud se zobrazí výzva k přihlášení k Wunderlistu, přihlaste se hned, abyste mohli povolit přístup.

1. Poskytněte potřebné podrobnosti pro vybranou aktivační událost nebo akci a pokračujte v vytváření pracovního postupu aplikace logiky.

## <a name="connector-reference"></a>Referenční informace ke konektorům

Technické podrobnosti o aktivačních událostech, akcích a limitech, které jsou popsány v popisu openapi (dříve Swagger) konektoru, projděte referenční [stránku konektoru](/connectors/wunderlist/).

## <a name="get-support"></a>Získat podporu

* Pokud máte dotazy, navštivte [fórum Azure Logic Apps](https://social.msdn.microsoft.com/Forums/en-US/home?forum=azurelogicapps).
* Pokud chcete zanechat své nápady na funkce nebo hlasovat, navštivte [web zpětné vazby od uživatelů Logic Apps](https://aka.ms/logicapps-wish).

## <a name="next-steps"></a>Další kroky

* Další informace o dalších [konektorech logic apps](../connectors/apis-list.md)