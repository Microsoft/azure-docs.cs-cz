---
title: Připojení k Slacku z aplikací Azure Logic Apps
description: Automatizace úloh a pracovních postupů, které monitorují soubory a spravují kanály, skupiny a zprávy v účtu Slack pomocí Azure Logic Apps
services: logic-apps
ms.suite: integration
ms.reviewer: klam, logicappspm
ms.topic: article
ms.date: 08/25/2018
tags: connectors
ms.openlocfilehash: 5f61009ee7b43be618e37acb4a783a54dbf11e55
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "74789167"
---
# <a name="monitor-and-manage-slack-with-azure-logic-apps"></a>Monitorování a správa Slacku pomocí aplikací Azure Logic Apps

Pomocí aplikací Azure Logic Apps a konektoru Slack můžete vytvářet automatizované úlohy a pracovní postupy, které monitorují vaše soubory Slack a spravují kanály Slack, zprávy, skupiny a tak dále, například:

* Sledujte, kdy jsou vytvořeny nové soubory.
* Vytváření, seznam a spojení kanálů 
* Zveřejňujte zprávy.
* Vytvořte skupiny a nastavte nerušit.

Můžete použít aktivační události, které získají odpovědi z vašeho účtu Slack a zpřístupní výstup dalším akcím. Můžete použít akce, které provádějí úkoly s účtem Slack. Můžete také mít jiné akce použít výstup z akce Slack. Když je například vytvořen nový soubor, můžete poslat e-mail pomocí konektoru Office 365 Outlook. Pokud s aplikacemi logiky tečujete, přečtěte [si, co je Azure Logic Apps?](../logic-apps/logic-apps-overview.md)

## <a name="prerequisites"></a>Požadavky

* Předplatné Azure. Pokud nemáte předplatné Azure, [zaregistrujte si bezplatný účet Azure](https://azure.microsoft.com/free/). 

* Váš účet [Slack](https://slack.com/) a přihlašovací údaje uživatele

  Vaše přihlašovací údaje autorizují aplikaci logiky k vytvoření připojení a přístupu k účtu Slack.

* Základní znalosti o [vytváření aplikací logiky](../logic-apps/quickstart-create-first-logic-app-workflow.md)

* Aplikace logiky, kde chcete získat přístup k účtu Slack. Chcete-li začít s aktivační událostí Slack, [vytvořte prázdnou aplikaci logiky](../logic-apps/quickstart-create-first-logic-app-workflow.md). Chcete-li použít akci Slack, spusťte aplikaci logiky s aktivační událostí, jako je například aktivační událost Slack nebo jiná aktivační událost, jako je například aktivační událost **opakování.**

## <a name="connect-to-slack"></a>Připojení k časovce

[!INCLUDE [Create connection general intro](../../includes/connectors-create-connection-general-intro.md)]

1. Přihlaste se k [portálu Azure](https://portal.azure.com)a otevřete aplikaci logiky v Návrháři aplikací logiky, pokud už není otevřená.

1. U prázdných aplikací logiky zadejte do vyhledávacího pole jako filtr "slack". V seznamu aktivačních událostí vyberte požadovanou aktivační událost. 

   -nebo-

   U existujících aplikací logiky v posledním kroku, kam chcete přidat akci, zvolte **Nový krok**. 
   Do vyhledávacího pole zadejte jako filtr "slack". 
   V seznamu akcí vyberte požadovanou akci.

   Chcete-li přidat akci mezi kroky, přesuňte ukazatel myši na šipku mezi kroky. 
   Zvolte znaménko plus (**+**), které se zobrazí, a pak vyberte Přidat **akci**.

1. Pokud se zobrazí výzva k přihlášení k Slacku, přihlaste se do pracovního prostoru Slack. 

   ![Přihlášení k pracovnímu prostoru Slack](./media/connectors-create-api-slack/slack-sign-in-workspace.png)

1. Autorizujte přístup pro aplikaci logiky.

   ![Autorizovat přístup k Slacku](./media/connectors-create-api-slack/slack-authorize-access.png)

1. Zadejte potřebné podrobnosti pro vybranou aktivační událost nebo akci. Chcete-li pokračovat v vytváření pracovního postupu aplikace logiky, přidejte další akce.

## <a name="connector-reference"></a>Referenční informace ke konektorům

Technické podrobnosti o aktivačních událostech, akcích a limitech, které jsou popsány v popisu openapi (dříve Swagger) konektoru, projděte referenční [stránku konektoru](/connectors/slack/).

## <a name="get-support"></a>Získat podporu

* Pokud máte dotazy, navštivte [fórum Azure Logic Apps](https://social.msdn.microsoft.com/Forums/en-US/home?forum=azurelogicapps).
* Pokud chcete zanechat své nápady na funkce nebo hlasovat, navštivte [web zpětné vazby od uživatelů Logic Apps](https://aka.ms/logicapps-wish).

## <a name="next-steps"></a>Další kroky

* Další informace o dalších [konektorech logic apps](../connectors/apis-list.md)
