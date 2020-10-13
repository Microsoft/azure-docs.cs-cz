---
title: Připojení k SharePointu z Azure Logic Apps
description: Automatizace úloh a pracovních postupů, které sledují a spravují prostředky na SharePointu Online nebo na SharePointovém serveru pomocí Azure Logic Apps
services: logic-apps
ms.suite: integration
ms.reviewer: klam, logicappspm
ms.topic: article
ms.date: 08/25/2018
tags: connectors
ms.openlocfilehash: c72330792e508361830c1bf391f85eefe78bdd1e
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "87283975"
---
# <a name="monitor-and-manage-sharepoint-resources-with-azure-logic-apps"></a>Monitorování a správa prostředků SharePointu pomocí Azure Logic Apps

Pomocí Azure Logic Apps a SharePointového konektoru můžete vytvářet automatizované úlohy a pracovní postupy, které sledují a spravují prostředky, jako jsou soubory, složky, seznamy, položky, osoby a tak dále, v SharePointu Online nebo na SharePointovém serveru v místním prostředí, například:

* Monitorování při vytvoření, změně nebo odstranění souborů nebo položek.
* Vytvořit, získat, aktualizovat nebo odstranit položky.
* Přidat, získat nebo odstranit přílohy. Získat obsah z příloh
* Vytváření, kopírování, aktualizace nebo odstraňování souborů. 
* Aktualizovat vlastnosti souboru. Získání obsahu, metadat nebo vlastností souboru.
* Vypíše nebo extrahuje složky.
* Získá seznamy nebo zobrazení seznamu.
* Nastaví stav schválení obsahu.
* Vyřešte osoby.
* Odesílat požadavky HTTP na službu SharePoint.
* Získá hodnoty entit.

Můžete použít triggery, které získávají odpovědi ze SharePointu a zpřístupnit výstup ostatním akcím. Akce v aplikaci Logic Apps můžete použít k provádění úloh v SharePointu. Můžete také použít jiné akce výstup z akcí SharePointu. Například pokud pravidelně načítáte soubory ze SharePointu, můžete odesílat zprávy do svého týmu pomocí konektoru časové rezervy.
Pokud s Logic Apps začínáte, přečtěte si téma [co je Azure Logic Apps?](../logic-apps/logic-apps-overview.md)

## <a name="prerequisites"></a>Požadavky

* Předplatné Azure. Pokud nemáte předplatné Azure, [zaregistrujte si bezplatný účet Azure](https://azure.microsoft.com/free/). 

* Vaše adresa SharePointového webu a přihlašovací údaje uživatele

  Vaše přihlašovací údaje autorizují vaši aplikaci logiky, aby vytvořila připojení a měl přístup k vašemu účtu služby SharePoint. 

* Předtím, než budete moci připojit Logic Apps k místním systémům, jako je například server SharePoint, je třeba [nainstalovat a nastavit místní bránu dat](../logic-apps/logic-apps-gateway-install.md). Tímto způsobem můžete nastavit, aby se instalace brány používala při vytváření připojení k serveru SharePoint pro vaši aplikaci logiky.

* Základní znalosti o [tom, jak vytvářet aplikace logiky](../logic-apps/quickstart-create-first-logic-app-workflow.md)

* Aplikace logiky, ke které chcete získat přístup k účtu služby SharePoint. Pokud chcete začít s triggerem služby SharePoint, [vytvořte prázdnou aplikaci logiky](../logic-apps/quickstart-create-first-logic-app-workflow.md). Pokud chcete použít akci SharePointu, spusťte aplikaci logiky s triggerem, jako je například Trigger Salesforce, pokud máte účet Salesforce.

  Aplikaci logiky můžete například spustit pomocí triggeru **při vytvoření záznamu** Salesforce. 
  Tato aktivační událost se aktivuje pokaždé, když se v Salesforce vytvoří nový záznam, třeba zájemce. 
  Tuto aktivační událost pak můžete sledovat pomocí akce **vytvořit soubor** v SharePointu. Tímto způsobem vaše aplikace logiky vytvoří soubor na SharePointu s informacemi o tomto novém záznamu, když se vytvoří nový záznam.

## <a name="connect-to-sharepoint"></a>Připojení k SharePointu

[!INCLUDE [Create connection general intro](../../includes/connectors-create-connection-general-intro.md)]

1. Přihlaste se k [Azure Portal](https://portal.azure.com)a otevřete aplikaci logiky v návrháři aplikace logiky, pokud už není otevřený.

1. Pro prázdné aplikace logiky zadejte do vyhledávacího pole "SharePoint" jako filtr. V seznamu triggery vyberte aktivační událost, kterou chcete. 

   -nebo-

   U existujících aplikací logiky klikněte v posledním kroku na místo, kam chcete přidat akci služby SharePoint, na možnost **Nový krok**. 
   Do vyhledávacího pole zadejte "SharePoint" jako filtr. 
   V seznamu akce vyberte akci, kterou chcete.

   Chcete-li přidat akci mezi kroky, přesuňte ukazatel myši na šipku mezi jednotlivými kroky. 
   Vyberte symbol plus ( **+** ), který se zobrazí, a pak vyberte **přidat akci**.

1. Jakmile budete vyzváni k přihlášení, zadejte potřebné informace o připojení. Pokud používáte SharePoint Server, ujistěte se, že jste vybrali **připojit přes místní bránu dat**. Jakmile budete hotoví, vyberte **Vytvořit**.

1. Zadejte potřebné podrobnosti pro vybraný Trigger nebo akci a pokračujte v vytváření pracovního postupu aplikace logiky.

## <a name="connector-reference"></a>Referenční informace ke konektorům

Technické podrobnosti o aktivačních událostech, akcích a omezeních, které jsou popsány v popisu OpenAPI konektoru (dříve Swagger), najdete na [referenční stránce](/connectors/sharepoint/)konektoru.

## <a name="get-support"></a>Získat podporu

* Otázky najdete na stránce s [dotazem na&Microsoft Q pro Azure Logic Apps](/answers/topics/azure-logic-apps.html).
* Pokud chcete zanechat své nápady na funkce nebo hlasovat, navštivte [web zpětné vazby od uživatelů Logic Apps](https://aka.ms/logicapps-wish).

## <a name="next-steps"></a>Další kroky

* Další informace o dalších [konektorech Logic Apps](../connectors/apis-list.md)

