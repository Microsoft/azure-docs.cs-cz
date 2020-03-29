---
title: Připojení ke SharePointu z azure logic apps
description: Automatizace úloh a pracovních postupů, které monitorují a spravují prostředky v SharePointu Online nebo SharePoint Serveru v místním prostředí pomocí Azure Logic Apps
services: logic-apps
ms.suite: integration
ms.reviewer: klam, logicappspm
ms.topic: article
ms.date: 08/25/2018
tags: connectors
ms.openlocfilehash: bb82ef2d6fb83c2e1b0fa81aa9504c9bb7d8234b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "74789252"
---
# <a name="monitor-and-manage-sharepoint-resources-with-azure-logic-apps"></a>Sledování a správa prostředků SharePointu pomocí Aplikací Logika Azure

Pomocí Aplikací Azure Logic Apps a sharepointového konektoru můžete vytvářet automatizované úlohy a pracovní postupy, které monitorují a spravují prostředky, jako jsou soubory, složky, seznamy, položky, osoby a tak dále, například v SharePointu Online nebo na SharePoint Serveru v místním prostředí:

* Sledujte, kdy jsou soubory nebo položky vytvářeny, měněny nebo odstraňovány.
* Vytvářejte, přiřazujte, aktualizujte nebo odstraňujte položky.
* Přidejte, získejte nebo odstraňte přílohy. Získejte obsah z příloh.
* Vytvářejte, kopírujte, aktualizujte nebo odstraňujte soubory. 
* Aktualizujte vlastnosti souboru. Získejte obsah, metadata nebo vlastnosti souboru.
* Seznam nebo extrahovat složky.
* Získejte seznamy nebo zobrazení seznamů.
* Nastavte stav schválení obsahu.
* Vyřešte osoby.
* Odeslání požadavků HTTP na SharePoint
* Získejte hodnoty entit.

Můžete použít aktivační události, které získají odpovědi ze sharepointu a zpřístupní výstup dalším akcím. Akce v aplikacích logiky můžete použít k provádění úkolů v SharePointu. Výstup z akcí SharePointu můžou použít i další akce. Pokud například pravidelně načítáte soubory ze SharePointu, můžete týmu odesílat zprávy pomocí konektoru Slack.
Pokud s aplikacemi logiky tečujete, přečtěte [si, co je Azure Logic Apps?](../logic-apps/logic-apps-overview.md)

## <a name="prerequisites"></a>Požadavky

* Předplatné Azure. Pokud nemáte předplatné Azure, [zaregistrujte si bezplatný účet Azure](https://azure.microsoft.com/free/). 

* Adresa sharepointového webu a pověření uživatele

  Vaše přihlašovací údaje autorizují aplikaci logiky k vytvoření připojení a přístupu k vašemu sharepointovému účtu. 

* Než budete moci připojit aplikace logiky k místním systémům, jako je SharePoint Server, musíte [nainstalovat a nastavit místní bránu dat](../logic-apps/logic-apps-gateway-install.md). Tímto způsobem můžete určit použití instalace brány při vytváření připojení sharepointového serveru pro aplikaci logiky.

* Základní znalosti o [vytváření aplikací logiky](../logic-apps/quickstart-create-first-logic-app-workflow.md)

* Aplikace logiky, kde chcete získat přístup k účtu SharePoint. Chcete-li začít s aktivační událostí [SharePointu, vytvořte prázdnou aplikaci logiky](../logic-apps/quickstart-create-first-logic-app-workflow.md). Pokud chcete použít akci SharePointu, spusťte aplikaci logiky s aktivační událostí, jako je například aktivační událost Salesforce, pokud máte účet Salesforce.

  Můžete například spustit aplikaci logiky s aktivační událostí **When a record is created** Salesforce. 
  Tato aktivační událost se aktivuje pokaždé, když je v salesforce vytvořen nový záznam, například zájemce. 
  Tuto aktivační událost pak můžete sledovat pomocí akce **Vytvořit soubor** služby SharePoint. Tímto způsobem při vytvoření nového záznamu aplikace logiky vytvoří soubor v SharePointu s informacemi o novém záznamu.

## <a name="connect-to-sharepoint"></a>Připojení k SharePointu

[!INCLUDE [Create connection general intro](../../includes/connectors-create-connection-general-intro.md)]

1. Přihlaste se k [portálu Azure](https://portal.azure.com)a otevřete aplikaci logiky v Návrháři aplikací logiky, pokud už není otevřená.

1. U prázdných aplikací logiky zadejte do vyhledávacího pole jako filtr "sharepoint". V seznamu aktivačních událostí vyberte požadovanou aktivační událost. 

   -nebo-

   U existujících aplikací logiky v posledním kroku, kam chcete přidat akci SharePointu, zvolte **Nový krok**. 
   Do vyhledávacího pole zadejte jako filtr "sharepoint". 
   V seznamu akcí vyberte požadovanou akci.

   Chcete-li přidat akci mezi kroky, přesuňte ukazatel myši na šipku mezi kroky. 
   Zvolte znaménko plus (**+**), které se zobrazí, a pak vyberte Přidat **akci**.

1. Po zobrazení výzvy k přihlášení zadejte potřebné informace o připojení. Pokud používáte SharePoint Server, ujistěte se, že jste vybrali **Připojit přes místní bránu dat**. Jakmile budete hotoví, vyberte **Vytvořit**.

1. Poskytněte potřebné podrobnosti pro vybranou aktivační událost nebo akci a pokračujte v vytváření pracovního postupu aplikace logiky.

## <a name="connector-reference"></a>Referenční informace ke konektorům

Technické podrobnosti o aktivačních událostech, akcích a limitech, které jsou popsány v popisu openapi (dříve Swagger) konektoru, projděte referenční [stránku konektoru](/connectors/sharepoint/).

## <a name="get-support"></a>Získat podporu

* Pokud máte dotazy, navštivte [fórum Azure Logic Apps](https://social.msdn.microsoft.com/Forums/en-US/home?forum=azurelogicapps).
* Pokud chcete zanechat své nápady na funkce nebo hlasovat, navštivte [web zpětné vazby od uživatelů Logic Apps](https://aka.ms/logicapps-wish).

## <a name="next-steps"></a>Další kroky

* Další informace o dalších [konektorech logic apps](../connectors/apis-list.md)
