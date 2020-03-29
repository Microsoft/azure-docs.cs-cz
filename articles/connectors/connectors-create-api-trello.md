---
title: Připojení k Trello z Azure Logic Apps
description: Automatizujte úkoly a pracovní postupy, které monitorují a spravují seznamy, vývěsky a karty v projektech Trello pomocí Azure Logic Apps
services: logic-apps
ms.suite: integration
ms.reviewer: klam, logicappspm
ms.topic: article
ms.date: 08/25/2018
tags: connectors
ms.openlocfilehash: 5c4fcb9b4fea1a4d982b5cf665564599d371b7cb
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "74789133"
---
# <a name="monitor-and-manage-trello-with-azure-logic-apps"></a>Sledování a správa Trello pomocí Aplikací Azure Logic Apps

Pomocí aplikací Azure Logic Apps a konektoru Trello můžete vytvářet automatizované úlohy a pracovní postupy, které monitorují a spravují vaše seznamy Trello, karty, nástěnky, členy týmu a tak dále, například:

* Sledujte, kdy jsou do vývěsek a seznamů přidány nové karty. 
* Vytvářejte, vytvářejte a spravujte nástěnky, karty a seznamy.
* Přidejte komentáře a členy na karty.
* Seznam nástěnek, vývěsek, karty na nástěnkách, komentáře k kartám, členové karty, členové týmu a týmy, ve kterých jste členem. 
* Sežeňte týmy.

Můžete použít aktivační události, které získají odpovědi z vašeho účtu Trello a zpřístupní výstup dalším akcím. Můžete použít akce, které provádějí úkoly s vaším účtem Trello. Můžete také mít jiné akce použít výstup z akce Trello. Například při přidání nové karty do vývěsky nebo seznamu můžete odesílat zprávy pomocí konektoru Slack. Pokud s aplikacemi logiky tečujete, přečtěte [si, co je Azure Logic Apps?](../logic-apps/logic-apps-overview.md)

## <a name="prerequisites"></a>Požadavky

* Předplatné Azure. Pokud nemáte předplatné Azure, [zaregistrujte si bezplatný účet Azure](https://azure.microsoft.com/free/). 

* Váš účet Trello a přihlašovací údaje uživatele

  Vaše přihlašovací údaje autorizují aplikaci logiky k vytvoření připojení a přístupu k účtu Trello.

* Základní znalosti o [vytváření aplikací logiky](../logic-apps/quickstart-create-first-logic-app-workflow.md)

* Aplikace logiky, kde chcete získat přístup k účtu Trello. Chcete-li začít s aktivační událostí Trello, [vytvořte prázdnou aplikaci logiky](../logic-apps/quickstart-create-first-logic-app-workflow.md). Chcete-li použít akci Trello, spusťte aplikaci logiky s aktivační událostí, například aktivační událost **opakování.**

## <a name="connect-to-trello"></a>Připojte se k Trello

[!INCLUDE [Create connection general intro](../../includes/connectors-create-connection-general-intro.md)]

1. Přihlaste se k [portálu Azure](https://portal.azure.com)a otevřete aplikaci logiky v Návrháři aplikací logiky, pokud už není otevřená.

1. U prázdných aplikací logiky zadejte do vyhledávacího pole jako filtr "trello". V seznamu aktivačních událostí vyberte požadovanou aktivační událost. 

   -nebo-

   U existujících aplikací logiky v posledním kroku, kam chcete přidat akci, zvolte **Nový krok**. 
   Do vyhledávacího pole zadejte jako filtr "trello". 
   V seznamu akcí vyberte požadovanou akci.

   Chcete-li přidat akci mezi kroky, přesuňte ukazatel myši na šipku mezi kroky. 
   Zvolte znaménko plus (**+**), které se zobrazí, a pak vyberte Přidat **akci**.

1. Pokud se zobrazí výzva k přihlášení k Trello, autorizujte přístup pro aplikaci logiky a přihlaste se.

1. Poskytněte potřebné podrobnosti pro vybranou aktivační událost nebo akci a pokračujte v vytváření pracovního postupu aplikace logiky.

## <a name="connector-reference"></a>Referenční informace ke konektorům

Technické podrobnosti o aktivačních událostech, akcích a limitech, které jsou popsány v popisu openapi (dříve Swagger) konektoru, projděte referenční [stránku konektoru](/connectors/trello/).

## <a name="get-support"></a>Získat podporu

* Pokud máte dotazy, navštivte [fórum Azure Logic Apps](https://social.msdn.microsoft.com/Forums/en-US/home?forum=azurelogicapps).
* Pokud chcete zanechat své nápady na funkce nebo hlasovat, navštivte [web zpětné vazby od uživatelů Logic Apps](https://aka.ms/logicapps-wish).

## <a name="next-steps"></a>Další kroky

* Další informace o dalších [konektorech logic apps](../connectors/apis-list.md)