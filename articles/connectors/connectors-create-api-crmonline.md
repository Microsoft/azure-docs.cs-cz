---
title: Připojení k Dynamics 365 – Azure Logic Apps | Dokumentace Microsoftu
description: Vytvořit a spravovat záznamy s Dynamics 365 (online) rozhraní REST API a Azure Logic Apps
author: Mattp123
ms.author: matp
ms.service: logic-apps
services: logic-apps
ms.reviewer: estfan, LADocs
ms.suite: integration
ms.topic: article
ms.date: 08/18/2018
tags: connectors
ms.openlocfilehash: 1247f603b759364edcee2bff12f75bb0a217ecd6
ms.sourcegitcommit: fbdfcac863385daa0c4377b92995ab547c51dd4f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/30/2018
ms.locfileid: "50231250"
---
# <a name="manage-dynamics-365-records-with-azure-logic-apps"></a>Správa záznamů Dynamics 365 s Azure Logic Apps

S Azure Logic Apps a konektoru Dynamics 365 můžete vytvořit automatizovaných úloh a pracovní postupy založené na svoje záznamy v Dynamics 365. Vaše pracovní postupy můžete vytvořit záznamy, aktualizovat položky, návratový záznamů a další ve vašem účtu Dynamics 365. Můžete zahrnout akce ve svých aplikacích logiky, které odpovědi z Dynamics 365 a zpřístupnit výstup pro další akce. Například při aktualizaci položky v Dynamics 365, můžete poslat e-mail pomocí Office 365.

Tento článek popisuje, jak se dají vytvářet aplikace logiky, která se vytvoří úkol v Dynamics 365 vždy, když se vytvoří nový záznam zájemce v Dynamics 365.
Pokud se službou logic Apps teprve začínáte, přečtěte si [co je Azure Logic Apps?](../logic-apps/logic-apps-overview.md).

## <a name="prerequisites"></a>Požadavky

* Předplatné Azure. Pokud nemáte předplatné Azure, <a href="https://azure.microsoft.com/free/" target="_blank">zaregistrujte si bezplatný účet Azure</a>. 

* A [účtu Dynamics 365](https://dynamics.microsoft.com)

* Základní znalosti o [postupy vytváření aplikací logiky](../logic-apps/quickstart-create-first-logic-app-workflow.md)

* Aplikace logiky, ve které chcete přístup k vašemu účtu Dynamics 365. Chcete-li spustit aplikaci logiky s triggerem Dynamics 365, musíte [prázdné aplikace logiky](../logic-apps/quickstart-create-first-logic-app-workflow.md). 

## <a name="add-dynamics-365-trigger"></a>Přidání triggeru Dynamics 365

[!INCLUDE [Create connection general intro](../../includes/connectors-create-connection-general-intro.md)]

Nejprve přidejte triggerem Dynamics 365, který se aktivuje, když se objeví nový záznam zájemce v Dynamics 365.

1. V [webu Azure portal](https://portal.azure.com), otevřete v návrháři aplikace logiky vaší prázdné aplikace logiky, není-li již otevřete.

1. Do vyhledávacího pole zadejte jako filtr "Dynamics 365". V tomto příkladu v seznamu triggerů vyberte tento trigger: **při vytvoření záznamu**

   ![Vybrat trigger](./media/connectors-create-api-crmonline/select-dynamics-365-trigger.png)

1. Pokud se zobrazí výzva k přihlášení k Dynamics 365, přihlaste se hned.

1. Uveďte následující údaje aktivační události:

   | Vlastnost | Požaduje se | Popis | 
   |----------|----------|-------------| 
   | **Název organizace** | Ano | Název instance Dynamics 365 vaší organizace k monitorování, například "Contoso" |
   | **Název entity** | Ano | Název entity k monitorování, například "vede" | 
   | **Frekvence** | Ano | Jednotka času pro použití s intervaly při vyhledávání aktualizací související se aktivační událost |
   | **Interval** | Ano | Počet sekund, minuty, hodiny, dny, týdny nebo měsíce, které předávají před další kontroly |
   ||| 

   ![Podrobnosti o triggeru](./media/connectors-create-api-crmonline/trigger-details.png)

## <a name="add-dynamics-365-action"></a>Přidání akce Dynamics 365

Teď přidejte akci Dynamics 365, který se vytvoří úkol záznam pro nový záznam zájemce.

1. Pod triggerem zvolte **nový krok**.

1. Do vyhledávacího pole zadejte jako filtr "Dynamics 365". Ze seznamu akcí vyberte tuto akci: **vytvoří nový záznam**

   ![Vyberte akci](./media/connectors-create-api-crmonline/select-action.png)

1. Zadejte podrobnosti tyto akce:

   | Vlastnost | Požaduje se | Popis | 
   |----------|----------|-------------| 
   | **Název organizace** | Ano | Instance Dynamics 365, kde chcete vytvořit záznam, který nemusí být stejné instance v aktivační událost, ale je v tomto příkladu "Contoso" |
   | **Název entity** | Ano | Entita, ve kterém chcete vytvořit záznam, třeba "Úloh" | 
   | | |

   ![Detaily akce](./media/connectors-create-api-crmonline/action-details.png)

1. Když **subjektu** pole se zobrazí v akci, klikněte do **subjektu** pole, zobrazí se seznam dynamického obsahu. Z tohoto seznamu vyberte pole hodnoty pro zahrnutí v záznamu úlohy spojené s nový záznam zájemce:

   | Pole | Popis | 
   |-------|-------------| 
   | **Příjmení** | Příjmení z zájemce jako primární kontakt v záznamu |
   | **Téma** | Popisný název pro zájemce v záznamu | 
   | | | 

   ![Podrobnosti záznamu úlohy](./media/connectors-create-api-crmonline/create-record-details.png)

1. Na panelu nástrojů návrháře zvolte **Uložit** pro vaši aplikaci logiky. 

1. Chcete-li ručně spustit aplikaci logiky na panelu nástrojů návrháře, zvolte **spustit**.

   ![Spuštění aplikace logiky](./media/connectors-create-api-crmonline/designer-toolbar-run.png)

1. Teď vytvořte záznam zájemce v Dynamics 365, můžete aktivovat pracovní postup aplikace logiky.

## <a name="add-filter-or-query"></a>Přidat filtr nebo dotazu

Chcete-li určit způsob filtrování dat v Dynamics 365 akci, zvolte **zobrazit pokročilé možnosti** v této akci. Potom můžete přidat filtr nebo uspořádání dotazem.
Například dotaz filtru můžete získat jenom aktivní účty a řadit záznamy podle názvu účtu. Pro tuto úlohu postupujte takto:

1. V části **dotaz filtru**, zadejte tento dotaz filtru OData: `statuscode eq 1`

2. V části **klauzule Order By**, jakmile se zobrazí v seznamu dynamického obsahu, vyberte **název účtu**. 

   ![Zadejte filtr a pořadí](./media/connectors-create-api-crmonline/advanced-options.png)

Další informace najdete v tématu tyto možnosti dotazu Dynamics 365 zákazníka Engagement webového rozhraní API systému: 

* [$filter](https://docs.microsoft.com/dynamics365/customer-engagement/developer/webapi/query-data-web-api#filter-results)
* [$orderby](https://docs.microsoft.com/dynamics365/customer-engagement/developer/webapi/query-data-web-api#order-results)

### <a name="best-practices-for-advanced-options"></a>Osvědčené postupy pro pokročilé možnosti

Pokud zadáte hodnotu pro pole v triggeru nebo akce, typ dat hodnoty musí odpovídat typu pole, ať už ručně zadejte hodnotu nebo vybrat hodnotu ze seznamu dynamického obsahu.

Tato tabulka popisuje některé typy polí a typů požadovaná data pro jejich hodnoty.

| Typ pole | Požadovaný typ dat | Popis | 
|------------|--------------------|-------------|
| Textová pole | Jeden řádek textu | Tato pole vyžadují jeden řádek textu nebo dynamický obsah, který má typ text. <p><p>*Příklad polí*: **popis** a **kategorie** | 
| Pole typu Integer | Celé číslo | Některá pole vyžadují integer nebo dynamický obsah, který má typ celé číslo. <p><p>*Příklad polí*: **dokončeno** a **doba trvání** | 
| Datová pole | Datum a čas | Některá pole vyžadují datum pomocí formátu mm/dd/rrrr nebo dynamický obsah, který má typ date. <p><p>*Příklad polí*: **vytvořeno**, **datum zahájení**, **skutečné zahájení**, **skutečný konec**, a **termín splnění** | 
| Zadejte pole, které vyžadují ID záznamu i vyhledávání | Primární klíč | Některá pole, které odkazují na jiný záznam entity vyžadují ID záznamu i typ vyhledávání. | 
||||

Pokud rozvineme tyto typy polí, tady je příklad polí v Dynamics 365 triggery a akce, které vyžadují ID záznamu i typ vyhledávání. Tento požadavek znamená, že hodnoty, které můžete vybrat ze seznamu dynamického nebude fungovat. 

| Pole | Popis | 
|-------|-------------|
| **Vlastník** | Musí být buď platné ID uživatele nebo týmu ID záznamu. | 
| **Typ vlastníka** | Musí být buď **systemusers** nebo **týmy**. | 
| **Pokud jde o** | Musí být platné ID záznamu, jako je například ID účtu nebo se obraťte na ID záznamu. | 
| **Související typ** | Musí být typ vyhledávání, například **účty** nebo **kontakty**. | 
| **Zákazníka** | Musí být platné ID záznamu, jako je například ID účtu nebo se obraťte na ID záznamu. | 
| **Typ odběratele** | Musí být typ vyhledávání, například **účty** nebo **kontakty**. | 
|||

V tomto příkladu s názvem akce **vytvoří nový záznam** vytvoří nový záznam úloh: 

![Vytváření záznamu úkolu s ID záznamu a vyhledávací typů](./media/connectors-create-api-crmonline/create-record-advanced.png)

Tato akce záznam úkolu přiřadí ID konkrétního uživatele nebo ID záznamu týmu, na základě ID záznamu v **vlastníka** pole a vyhledávání zadejte **typ vlastníka** pole:

![Zadejte ID záznamu vlastníka a vyhledávání](./media/connectors-create-api-crmonline/owner-record-id-and-lookup-type.png)

Tato akce také přidá záznam účtu, který je spojen s ID přidá záznam **ohledně** pole a vyhledávání zadejte **související typ** pole: 

![Zadejte odpovídající ID záznamu a vyhledávání](./media/connectors-create-api-crmonline/regarding-record-id-lookup-type-account.png)

## <a name="find-record-id"></a>Vyhledání ID záznamu

K vyhledání ID záznamu, postupujte podle těchto kroků: 

1. V Dynamics 365 otevřete záznam, třeba záznam účtu.

2. Na panelu akcí vyberte jednu z těchto kroků:

   * Zvolte **otevřít v novém okně**. ![zobrazovat záznamu](./media/connectors-create-api-crmonline/popout-record.png) 
   * Zvolte **odkaz e-MAILEM A** tak úplnou adresu URL můžete zkopírovat do vaší výchozí e-mailový program.

   Záznam se zobrazí ID v adrese URL mezi `%7b` a `%7d` kódování znaků:

   ![Vyhledání ID záznamu](./media/connectors-create-api-crmonline/find-record-ID.png)

## <a name="troubleshoot-failed-runs"></a>Řešení potíží s neúspěšná spuštění

Najít a projděte si kroky ve vaší aplikaci logiky, které selhaly, můžete zobrazit historii spouštění aplikace logiky, stav, vstupy, výstupy a tak dále.

1. Na webu Azure Portal, v hlavní nabídce aplikace logiky, vyberte **přehled**. V **historie běhů** oddílu, který zobrazuje všechna spuštění stavy pro vaši aplikaci logiky, vyberte se nezdařilo spustit další informace.

   ![Stav spuštění aplikace logiky](./media/connectors-create-api-crmonline/run-history.png)

1. Abyste mohli zobrazit další podrobnosti, rozbalte položku neúspěšných kroků. 

   ![Rozbalte kroku](./media/connectors-create-api-crmonline/expand-failed-step.png)

1. Zkontrolujte podrobnosti v kroku, jako je například vstupy a výstupy, které vám pomohou najít příčinu selhání.

   ![Selhání kroku - vstupy a výstupy](./media/connectors-create-api-crmonline/expand-failed-step-inputs-outputs.png)

Další informace o řešení potíží s logic apps najdete v tématu [diagnostikování selhání aplikací logiky](../logic-apps/logic-apps-diagnosing-failures.md).

## <a name="connector-reference"></a>Referenční informace ke konektorům

Technické podrobnosti, jako jsou triggery, akce a omezení, jak je popsáno v souboru Swagger konektoru, najdete v článku [konektoru referenční stránce](/connectors/crm/). 

## <a name="get-support"></a>Získat podporu

* Pokud máte dotazy, navštivte [fórum Azure Logic Apps](https://social.msdn.microsoft.com/Forums/en-US/home?forum=azurelogicapps).
* Pokud chcete zanechat své nápady na funkce nebo hlasovat, navštivte [web zpětné vazby od uživatelů Logic Apps](https://aka.ms/logicapps-wish).

## <a name="next-steps"></a>Další postup

* Další informace o dalších [konektory Logic Apps](../connectors/apis-list.md)
