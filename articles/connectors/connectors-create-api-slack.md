---
title: Připojení k časové rezervě z Azure Logic Apps
description: Automatizujte úlohy a pracovní postupy, které sledují soubory a spravují kanály, skupiny a zprávy v účtu časové rezervy pomocí Azure Logic Apps
services: logic-apps
ms.suite: integration
ms.reviewer: klam, logicappspm
ms.topic: article
ms.date: 08/25/2018
tags: connectors
ms.openlocfilehash: 5f61009ee7b43be618e37acb4a783a54dbf11e55
ms.sourcegitcommit: 76b48a22257a2244024f05eb9fe8aa6182daf7e2
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/03/2019
ms.locfileid: "74789167"
---
# <a name="monitor-and-manage-slack-with-azure-logic-apps"></a>Monitorování a Správa časové rezervy pomocí Azure Logic Apps

Pomocí Azure Logic Apps a konektoru časové rezervy můžete vytvářet automatizované úlohy a pracovní postupy, které sledují soubory časové rezervy a spravují vaše časové rezervy, zprávy, skupiny atd. například:

* Monitorování při vytváření nových souborů.
* Vytváření, výpis a spojování kanálů 
* Odešle zprávy.
* Vytvořte skupiny a nastavte Nerušit.

Můžete použít triggery, které získávají odpovědi z účtu časové rezervy, a zpřístupnit výstup ostatním akcím. Můžete použít akce, které provádějí úlohy s vaším účtem časové rezervy. Můžete také použít jiné akce výstup z akcí časové rezervy. Když se například vytvoří nový soubor, můžete poslat e-mail pomocí konektoru Office 365 Outlooku. Pokud s Logic Apps začínáte, přečtěte si téma [co je Azure Logic Apps?](../logic-apps/logic-apps-overview.md)

## <a name="prerequisites"></a>Předpoklady

* Předplatné Azure. Pokud nemáte předplatné Azure, [zaregistrujte si bezplatný účet Azure](https://azure.microsoft.com/free/). 

* Účet [časové rezervy](https://slack.com/) a přihlašovací údaje uživatele

  Vaše přihlašovací údaje autorizují vaši aplikaci logiky, aby vytvořila připojení a měl přístup k vašemu účtu časové rezervy.

* Základní znalosti o [tom, jak vytvářet aplikace logiky](../logic-apps/quickstart-create-first-logic-app-workflow.md)

* Aplikace logiky, ke které chcete získat přístup k účtu časové rezervy Pokud chcete začít s triggerem časové rezervy, [vytvořte prázdnou aplikaci logiky](../logic-apps/quickstart-create-first-logic-app-workflow.md). Pokud chcete použít akci časové rezervy, spusťte aplikaci logiky s triggerem, například triggerem časové rezervy nebo jinou triggerem, jako je například Trigger **opakování** .

## <a name="connect-to-slack"></a>Připojit k časové rezervě

[!INCLUDE [Create connection general intro](../../includes/connectors-create-connection-general-intro.md)]

1. Přihlaste se k [Azure Portal](https://portal.azure.com)a otevřete aplikaci logiky v návrháři aplikace logiky, pokud už není otevřený.

1. Pro prázdné aplikace logiky zadejte do vyhledávacího pole "časová rezerva" jako filtr. V seznamu triggery vyberte aktivační událost, kterou chcete. 

   -nebo-

   Pro existující aplikace logiky v rámci posledního kroku, kam chcete přidat akci, vyberte možnost **Nový krok**. 
   Do vyhledávacího pole zadejte jako filtr "časová rezerva". 
   V seznamu akce vyberte akci, kterou chcete.

   Chcete-li přidat akci mezi kroky, přesuňte ukazatel myši na šipku mezi jednotlivými kroky. 
   Zvolte znaménko plus ( **+** ), které se zobrazí, a pak vyberte **přidat akci**.

1. Pokud budete vyzváni k přihlášení k časové rezervě, přihlaste se k pracovnímu prostoru časové rezervy. 

   ![Přihlášení k pracovnímu prostoru časové rezervy](./media/connectors-create-api-slack/slack-sign-in-workspace.png)

1. Autorizovat přístup pro vaši aplikaci logiky.

   ![Autorizovat přístup k časové rezervě](./media/connectors-create-api-slack/slack-authorize-access.png)

1. Zadejte potřebné podrobnosti pro zvolenou aktivační událost nebo akci. Pokud chcete pokračovat v sestavování pracovního postupu aplikace logiky, přidejte další akce.

## <a name="connector-reference"></a>Referenční informace ke konektorům

Technické podrobnosti o aktivačních událostech, akcích a omezeních, které jsou popsány v popisu OpenAPI konektoru (dříve Swagger), najdete na [referenční stránce](/connectors/slack/)konektoru.

## <a name="get-support"></a>Získat podporu

* Pokud máte dotazy, navštivte [fórum Azure Logic Apps](https://social.msdn.microsoft.com/Forums/en-US/home?forum=azurelogicapps).
* Pokud chcete zanechat své nápady na funkce nebo hlasovat, navštivte [web zpětné vazby od uživatelů Logic Apps](https://aka.ms/logicapps-wish).

## <a name="next-steps"></a>Další kroky

* Další informace o dalších [konektorech Logic Apps](../connectors/apis-list.md)
