---
title: Připojení k dynamics 365
description: Vytváření a správa záznamů pomocí řešení REST API Dynamics 365 (online) a aplikací Azure Logic Apps
services: logic-apps
ms.suite: integration
author: Mattp123
ms.author: matp
ms.reviewer: estfan, logicappspm
ms.topic: conceptual
ms.date: 08/18/2018
tags: connectors
ms.openlocfilehash: 9837b68fbfba783a468712d8ba1883b198af4954
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "74789887"
---
# <a name="manage-dynamics-365-records-with-azure-logic-apps"></a>Správa záznamů v Dynamics 365 pomocí Azure Logic Apps

Pomocí Azure Logic Apps a konektoru Dynamics 365 můžete vytvářet automatizované úlohy a pracovní postupy založené na záznamech v Dynamics 365. Pracovní postupy můžou vytvářet záznamy, aktualizovat položky, vracet záznamy a provádět další akce ve vašem účtu Dynamics 365. Můžete zahrnout akce ve vašich aplikacích logiky, které získat odpovědi z Dynamics 365 a zpřístupnit výstup pro jiné akce. Když se například v Dynamics 365 aktualizuje položka, můžete poslat e-mail pomocí Office 365.

Tento článek ukazuje, jak můžete vytvořit aplikaci logiky, která vytvoří úkol v Dynamics 365 při každém vytvoření nového záznamu zájemce v aplikaci Dynamics 365.
Pokud s aplikacemi logiky tečujete, přečtěte [si, co je Azure Logic Apps?](../logic-apps/logic-apps-overview.md).

## <a name="prerequisites"></a>Požadavky

* Předplatné Azure. Pokud nemáte předplatné Azure, [zaregistrujte si bezplatný účet Azure](https://azure.microsoft.com/free/).

* [Účet Dynamics 365](https://dynamics.microsoft.com)

* Základní znalosti o [vytváření aplikací logiky](../logic-apps/quickstart-create-first-logic-app-workflow.md)

* Aplikace logiky, kde chcete získat přístup k účtu Dynamics 365. Chcete-li spustit aplikaci logiky s aktivační událostí Dynamics 365, potřebujete [prázdnou aplikaci logiky](../logic-apps/quickstart-create-first-logic-app-workflow.md).

## <a name="add-dynamics-365-trigger"></a>Přidat aktivační událost Dynamics 365

[!INCLUDE [Create connection general intro](../../includes/connectors-create-connection-general-intro.md)]

Nejprve přidejte aktivační událost Dynamics 365, která se aktivuje, když se v aplikaci Dynamics 365 zobrazí nový záznam zájemce.

1. Na [webu Azure Portal](https://portal.azure.com)otevřete prázdnou aplikaci logiky v Návrháři aplikací logiky, pokud už není otevřená.

1. Do vyhledávacího pole zadejte jako filtr "Dynamics 365". V tomto příkladu vyberte v seznamu aktivačních událostí tuto aktivační událost: **Při vytvoření záznamu**

   ![Vybrat aktivační událost](./media/connectors-create-api-crmonline/select-dynamics-365-trigger.png)

1. Pokud se zobrazí výzva k přihlášení k Dynamics 365, přihlaste se hned.

1. Zadejte tyto podrobnosti o aktivační události:

   | Vlastnost | Požaduje se | Popis |
   |----------|----------|-------------|
   | **Název organizace** | Ano | Název instance Dynamics 365 vaší organizace ke sledování, například "Contoso" |
   | **Název entity** | Ano | Název entity, která má sledovat, například "Zájemci" | 
   | **Frequency** | Ano | Jednotka času, která se má použít v intervalech při kontrole aktualizací souvisejících s aktivační událostí |
   | **Interval** | Ano | Počet sekund, minut, hodin, dnů, týdnů nebo měsíců, které projdou před další kontrolou |
   ||| 

   ![Podrobnosti o aktivační události](./media/connectors-create-api-crmonline/trigger-details.png)

## <a name="add-dynamics-365-action"></a>Přidat akci Dynamics 365

Nyní přidejte akci Dynamics 365, která vytvoří záznam úkolu pro nový záznam zájemce.

1. Pod aktivační událostí zvolte **Nový krok**.

1. Do vyhledávacího pole zadejte jako filtr "Dynamics 365". Ze seznamu akcí vyberte tuto akci: **Vytvoření nového záznamu**

   ![Vybrat akci](./media/connectors-create-api-crmonline/select-action.png)

1. Uveďte tyto podrobnosti akce:

   | Vlastnost | Požaduje se | Popis |
   |----------|----------|-------------|
   | **Název organizace** | Ano | Instance Dynamics 365, kde chcete vytvořit záznam, který nemusí být ve vaší aktivační události stejnou instancí, ale v tomto příkladu je "Contoso". |
   | **Název entity** | Ano | Entita, ve které chcete záznam vytvořit, například "Úkoly" |
   | | |

   ![Podrobnosti akce](./media/connectors-create-api-crmonline/action-details.png)

1. Když se v akci zobrazí pole **Předmět,** klikněte do pole **Předmět,** aby se zobrazil seznam dynamického obsahu. V tomto seznamu vyberte hodnoty polí, které chcete zahrnout do záznamu úkolu přidruženého k novému záznamu zájemce:

   | Pole | Popis |
   |-------|-------------|
   | **Příjmení** | Příjmení zájemce jako primární ho kontaktu v záznamu |
   | **Téma** | Popisný název zájemce v záznamu |
   | | |

   ![Podrobnosti záznamu úkolu](./media/connectors-create-api-crmonline/create-record-details.png)

1. Na panelu nástrojů návrháře zvolte **Uložit** pro aplikaci logiky. 

1. Chcete-li aplikaci logiky spustit ručně, zvolte na panelu nástrojů návrháře **možnost Spustit**.

   ![Spuštění aplikace logiky](./media/connectors-create-api-crmonline/designer-toolbar-run.png)

1. Teď vytvořte záznam zájemce v Dynamics 365, abyste mohli aktivovat pracovní postup aplikace logiky.

## <a name="add-filter-or-query"></a>Přidání filtru nebo dotazu

Chcete-li určit, jak filtrovat data v akci Dynamics 365, zvolte **Zobrazit upřesňující volby** v této akci. Potom můžete přidat filtr nebo pořadí podle dotazu.
Pomocí dotazu filtru můžete například získat pouze aktivní účty a seřadit tyto záznamy podle názvu účtu. Pro tento úkol postupujte takto:

1. V části **Filtrdotaz**zadejte tento dotaz filtru OData:`statuscode eq 1`

2. V části **Order By**vyberte po zobrazení seznamu dynamického obsahu **položku Název účtu**. 

   ![Určení filtru a pořadí](./media/connectors-create-api-crmonline/advanced-options.png)

Další informace naleznete v těchto možnostech systémových dotazů řešení Dynamics 365 Customer Engagement Web API:

* [$filter](https://docs.microsoft.com/dynamics365/customer-engagement/developer/webapi/query-data-web-api#filter-results)
* [$orderby](https://docs.microsoft.com/dynamics365/customer-engagement/developer/webapi/query-data-web-api#order-results)

### <a name="best-practices-for-advanced-options"></a>Osvědčené postupy u pokročilých možností

Pokud zadáte hodnotu pole v akci nebo aktivační události, datový typ hodnoty se musí shodovat s typem pole, ať už hodnotu zadáte ručně nebo vyberete hodnotu ze seznamu dynamického obsahu.

Tato tabulka popisuje některé typy polí a požadované datové typy pro jejich hodnoty.

| Typ pole | Požadovaný datový typ | Popis | 
|------------|--------------------|-------------|
| Textová pole | Jeden řádek textu | Tato pole vyžadují jeden řádek textu nebo dynamický obsah, který má typ textu. <p><p>*Příklad polí*: **Popis** a **kategorie** | 
| Pole typu Integer | Celé číslo | Některá pole vyžadují celé číslo nebo dynamický obsah, který má typ celé číslo. <p><p>*Příklad polí*: **Procento dokončení** a **Doba trvání** | 
| Datová pole | Datum a čas | Některá pole vyžadují datum s formátem mm/dd/yyyy nebo dynamickým obsahem, který má typ data. <p><p>*Příkladpolí*: **Datum vytvoření,** **Počáteční datum**, **Skutečné zahájení**, Skutečné **ukončení**a **Termín splnění** | 
| Pole vyžadující ID záznamu i typ vyhledávání | Primární klíč | Některá pole, která odkazují na jiný záznam entity, vyžadují ID záznamu i typ vyhledávání. | 
||||

Rozbalování těchto typů polí, zde jsou ukázková pole v aktivačních událostech dynamics 365 a akce, které vyžadují ID záznamu i typ vyhledávání. Tento požadavek znamená, že hodnoty, které vyberete z dynamického seznamu, nebudou fungovat.

| Pole | Popis |
|-------|-------------|
| **Vlastník** | Musí být platné ID uživatele nebo ID záznam týmu. |
| **Typ vlastníka** | Musí být `systemusers` `teams`buď nebo . |
| **Týká se** | Musí se jednat o platné ID záznamu, například ID obchodního vztahu nebo ID záznamu kontaktu. |
| **Pokud jde o typ** | Musí se jednat o vyhledávací `accounts` `contacts`typ, například nebo . |
| **Zákazník** | Musí se jednat o platné ID záznamu, například ID obchodního vztahu nebo ID záznamu kontaktu. |
| **Typ zákazníka** | Musí se jednat o typ `accounts` `contacts`vyhledávání, například nebo . |
|||

V tomto příkladu akce s názvem **Vytvořit nový záznam** vytvoří nový záznam úkolu:

![Vytvoření záznamu úkolu s ID záznamů a typy vyhledávání](./media/connectors-create-api-crmonline/create-record-advanced.png)

Tato akce přiřadí záznam úkolu k určitému ID uživatele nebo ID záznamu týmu na základě ID záznamu v poli **Vlastník** a typu vyhledávání v poli **Typ vlastníka:**

![ID záznamu vlastníka a typ vyhledávání](./media/connectors-create-api-crmonline/owner-record-id-and-lookup-type.png)

Tato akce také přidá záznam obchodního vztahu, který je přidružen k ID záznamu přidanému do pole **Týká se,** a typ vyhledávání v poli **Týká se typu:**

![Pokud jde o ID záznamu a typ vyhledávání](./media/connectors-create-api-crmonline/regarding-record-id-lookup-type-account.png)

## <a name="find-record-id"></a>Najít ID záznamu

Chcete-li najít ID záznamu, postupujte takto:

1. V dynamics 365 otevřete záznam, například záznam obchodního vztahu.

2. Na panelu nástrojů Akce zvolte jeden z těchto kroků:

   * Zvolte **Pop Out**. ![vyskakovací záznam](./media/connectors-create-api-crmonline/popout-record.png) 
   * Zvolte **E-MAIL A LINK,** abyste mohli zkopírovat úplnou adresu URL do výchozího e-mailového programu.

   ID záznamu se zobrazí v `%7b` `%7d` adrese URL mezi a kódovacími znaky:

   ![Najít ID záznamu](./media/connectors-create-api-crmonline/find-record-ID.png)

## <a name="troubleshoot-failed-runs"></a>Řešení potíží s neúspěšnými spuštěními

Chcete-li najít a zkontrolovat neúspěšné kroky v aplikaci logiky, můžete zobrazit historii spuštění aplikace logiky, stav, vstupy, výstupy a tak dále.

1. Na portálu Azure v hlavní nabídce aplikace logiky vyberte **Přehled**. V části **Spustí historii,** která zobrazuje všechny stavy spuštění pro aplikaci logiky, vyberte neúspěšné spuštění pro další informace.

   ![Stav spuštění aplikace logiky](./media/connectors-create-api-crmonline/run-history.png)

1. Rozbalte neúspěšný krok, abyste mohli zobrazit další podrobnosti.

   ![Rozbalit neúspěšný krok](./media/connectors-create-api-crmonline/expand-failed-step.png)

1. Zkontrolujte podrobnosti kroku, jako jsou vstupy a výstupy, které vám pomohou najít příčinu selhání.

   ![Neúspěšný krok - vstupy a výstupy](./media/connectors-create-api-crmonline/expand-failed-step-inputs-outputs.png)

Další informace o řešení potíží s aplikacemi logiky najdete [v tématu Diagnostika selhání aplikace logiky](../logic-apps/logic-apps-diagnosing-failures.md).

## <a name="connector-reference"></a>Referenční informace ke konektorům

Technické podrobnosti, jako jsou aktivační události, akce a omezení, jak je popsáno v souboru OpenAPI (dříve Swagger) konektoru, naleznete [na referenční stránce konektoru](/connectors/dynamicscrmonline/).

## <a name="next-steps"></a>Další kroky

* Další informace o dalších [konektorech logic apps](../connectors/apis-list.md)
