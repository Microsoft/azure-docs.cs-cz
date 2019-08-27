---
title: Připojení k Dynamics 365 – Azure Logic Apps
description: Vytváření a Správa záznamů pomocí rozhraní REST API Dynamics 365 (online) a Azure Logic Apps
services: logic-apps
ms.service: logic-apps
ms.suite: integration
author: Mattp123
ms.author: matp
manager: carmonm
ms.reviewer: estfan, LADocs
ms.topic: conceptual
ms.date: 08/18/2018
tags: connectors
ms.openlocfilehash: ce83e6b1847a8f08467cb7877e517bdaace27953
ms.sourcegitcommit: bba811bd615077dc0610c7435e4513b184fbed19
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/27/2019
ms.locfileid: "70051024"
---
# <a name="manage-dynamics-365-records-with-azure-logic-apps"></a>Správa záznamů Dynamics 365 pomocí Azure Logic Apps

Pomocí Azure Logic Apps a konektoru Dynamics 365 můžete vytvářet automatizované úlohy a pracovní postupy založené na záznamech v Dynamics 365. Pracovní postupy můžou vytvářet záznamy, aktualizovat položky, vracet záznamy a provádět další akce ve vašem účtu Dynamics 365. Do svých aplikací logiky můžete zahrnout akce, které získají odpovědi z Dynamics 365, a zpřístupnit výstup pro jiné akce. Například pokud je položka aktualizována v Dynamics 365, můžete odeslat e-mail pomocí Office 365.

Tento článek ukazuje, jak můžete vytvořit aplikaci logiky, která vytvoří úkol v Dynamics 365 při každém vytvoření nového záznamu zájemce v Dynamics 365.
Pokud s Logic Apps začínáte, přečtěte si téma [co je Azure Logic Apps?](../logic-apps/logic-apps-overview.md).

## <a name="prerequisites"></a>Požadavky

* Předplatné Azure. Pokud nemáte předplatné Azure, [zaregistrujte si bezplatný účet Azure](https://azure.microsoft.com/free/).

* [Účet Dynamics 365](https://dynamics.microsoft.com)

* Základní znalosti o [tom, jak vytvářet aplikace logiky](../logic-apps/quickstart-create-first-logic-app-workflow.md)

* Aplikace logiky, ke které chcete získat přístup k účtu Dynamics 365. Chcete-li spustit aplikaci logiky pomocí triggeru Dynamics 365, budete potřebovat [prázdnou aplikaci logiky](../logic-apps/quickstart-create-first-logic-app-workflow.md).

## <a name="add-dynamics-365-trigger"></a>Přidání triggeru Dynamics 365

[!INCLUDE [Create connection general intro](../../includes/connectors-create-connection-general-intro.md)]

Nejdřív přidejte Trigger Dynamics 365, který se aktivuje při zobrazení nového záznamu zájemce v Dynamics 365.

1. V [Azure Portal](https://portal.azure.com)otevřete prázdnou aplikaci logiky v návrháři aplikace logiky, pokud už není otevřený.

1. Do vyhledávacího pole zadejte "Dynamics 365" jako filtr. V tomto příkladu vyberte v seznamu triggery tuto aktivační událost: **Při vytvoření záznamu**

   ![Vybrat trigger](./media/connectors-create-api-crmonline/select-dynamics-365-trigger.png)

1. Pokud se zobrazí výzva k přihlášení k Dynamics 365, přihlaste se hned.

1. Zadejte tyto podrobnosti triggeru:

   | Vlastnost | Požadováno | Popis |
   |----------|----------|-------------|
   | **Název organizace** | Ano | Název instance Dynamics 365 vaší organizace, kterou chcete monitorovat, například contoso |
   | **Název entity** | Ano | Název entity, která se má monitorovat, například "Zájemci" | 
   | **Frekvence** | Ano | Jednotka času, která se má použít v intervalech pro kontrolu aktualizací, které se vztahují k triggeru |
   | **Interval** | Ano | Počet sekund, minut, hodin, dnů, týdnů nebo měsíců, které před další kontrolou procházejí |
   ||| 

   ![Podrobnosti triggeru](./media/connectors-create-api-crmonline/trigger-details.png)

## <a name="add-dynamics-365-action"></a>Přidat akci Dynamics 365

Nyní přidejte akci Dynamics 365, která vytvoří záznam úkolu pro nový záznam zájemce.

1. V části Trigger vyberte **Nový krok**.

1. Do vyhledávacího pole zadejte "Dynamics 365" jako filtr. V seznamu akce vyberte tuto akci: **Vytvořit nový záznam**

   ![Vybrat akci](./media/connectors-create-api-crmonline/select-action.png)

1. Zadejte následující podrobnosti o akci:

   | Vlastnost | Požadováno | Popis |
   |----------|----------|-------------|
   | **Název organizace** | Ano | Instance Dynamics 365, ve které chcete vytvořit záznam, který se nemusí shodovat s instancí triggeru, ale v tomto příkladu je contoso |
   | **Název entity** | Ano | Entita, ve které chcete záznam vytvořit, například "úkoly" |
   | | |

   ![Detaily akce](./media/connectors-create-api-crmonline/action-details.png)

1. Po zobrazení pole **Předmět** v rámci akce klikněte do pole **Předmět** , aby se zobrazil seznam dynamického obsahu. V tomto seznamu vyberte hodnoty polí, které se mají zahrnout do záznamu úkolu přidruženého k novému záznamu zájemce:

   | Pole | Popis |
   |-------|-------------|
   | **Příjmení** | Příjmení od zájemce jako primární kontakt v záznamu |
   | **Téma** | Popisný název zájemce v záznamu |
   | | |

   ![Podrobnosti o záznamu úlohy](./media/connectors-create-api-crmonline/create-record-details.png)

1. Na panelu nástrojů návrháře klikněte na možnost **Uložit** pro aplikaci logiky. 

1. Pokud chcete aplikaci logiky spustit ručně, na panelu nástrojů návrháře klikněte na **Spustit**.

   ![Spustit aplikaci logiky](./media/connectors-create-api-crmonline/designer-toolbar-run.png)

1. Teď vytvořte záznam zájemce v Dynamics 365, abyste mohli aktivovat pracovní postup vaší aplikace logiky.

## <a name="add-filter-or-query"></a>Přidat filtr nebo dotaz

Chcete-li určit, jak filtrovat data v akci Dynamics 365, zvolte možnost **Zobrazit rozšířené možnosti** v této akci. Pak můžete přidat dotaz Filter nebo ORDER by.
Dotaz filtru můžete například použít k získání jenom aktivních účtů a seřazení těchto záznamů podle názvu účtu. Pro tuto úlohu použijte následující postup:

1. V části **dotaz filtru**zadejte tento dotaz filtru OData:`statuscode eq 1`

2. V části **ORDER by**(Pokud se zobrazí seznam dynamického obsahu) vyberte **název účtu**. 

   ![Zadat filtr a pořadí](./media/connectors-create-api-crmonline/advanced-options.png)

Další informace najdete v tématu Možnosti dotazů systému webového rozhraní API pro zákaznickou rezervaci v Dynamics 365:

* [$filter](https://docs.microsoft.com/dynamics365/customer-engagement/developer/webapi/query-data-web-api#filter-results)
* [$orderby](https://docs.microsoft.com/dynamics365/customer-engagement/developer/webapi/query-data-web-api#order-results)

### <a name="best-practices-for-advanced-options"></a>Osvědčené postupy pro pokročilé možnosti

Když zadáte hodnotu pro pole v akci nebo triggeru, datový typ hodnoty se musí shodovat s typem pole, ať už ručně zadáte hodnotu nebo vyberte hodnotu ze seznamu dynamického obsahu.

Tato tabulka popisuje některé typy polí a požadované datové typy pro jejich hodnoty.

| Typ pole | Požadovaný datový typ | Popis | 
|------------|--------------------|-------------|
| Textová pole | Jeden řádek textu | Tato pole vyžadují jeden řádek textu nebo dynamický obsah, který má typ text. <p><p>*Ukázková pole*: **Popis** a **kategorie** | 
| Pole typu Integer | Celé číslo | Některá pole vyžadují celé číslo nebo dynamický obsah, který má typ Integer. <p><p>*Ukázková pole*: **Procento dokončení** a **Doba trvání** | 
| Pole data | Datum a čas | Některá pole vyžadují datum ve formátu mm/dd/rrrr nebo dynamický obsah, který má typ data. <p><p>*Ukázková pole*: Datum **Vytvoření**, **Datum zahájení**, **skutečný začátek**, **skutečný konec**a **termín splnění** | 
| Pole, která vyžadují ID záznamu i typ vyhledávání | Primární klíč | Některá pole, která odkazují na jiný záznam entity, vyžadují ID záznamu i typ vyhledávání. | 
||||

Rozšiřování těchto typů polí je příklad polí v aktivačních událostech Dynamics 365 a akcí, které vyžadují ID záznamu i typ vyhledávání. Tento požadavek znamená, že hodnoty, které vyberete z dynamického seznamu, nebudou fungovat.

| Pole | Popis |
|-------|-------------|
| **Vlastník** | Musí to být buď platné ID uživatele, nebo ID záznamu týmu. |
| **Typ vlastníka** | Musí být buď `systemusers` nebo `teams`. |
| **Týkající** | Musí se jednat o platné ID záznamu, jako je ID účtu nebo ID záznamu kontaktu. |
| **Související typ** | Musí se jednat o typ vyhledávání, například `accounts` nebo `contacts`. |
| **Zákazníka** | Musí se jednat o platné ID záznamu, jako je ID účtu nebo ID záznamu kontaktu. |
| **Typ zákazníka** | Musí se jednat o typ vyhledávání, například `accounts` nebo `contacts`. |
|||

V tomto příkladu akce s názvem **vytvořit nový záznam** vytvoří nový záznam úkolu:

![Vytvoření záznamu úlohy s ID záznamů a vyhledávacími typy](./media/connectors-create-api-crmonline/create-record-advanced.png)

Tato akce přiřadí záznam úlohy k určitému ID uživatele nebo ID záznamu týmu na základě ID záznamu v poli **vlastník** a typu vyhledávání v poli **typ vlastníka** :

![ID záznamu vlastníka a typ vyhledávání](./media/connectors-create-api-crmonline/owner-record-id-and-lookup-type.png)

Tato akce také přidá záznam účtu, který je spojen s ID záznamu přidaným do pole **týká** se a typem vyhledávání v poli **související typ** :

![Související s ID záznamu a typem vyhledávání](./media/connectors-create-api-crmonline/regarding-record-id-lookup-type-account.png)

## <a name="find-record-id"></a>Najít ID záznamu

Pokud chcete najít ID záznamu, postupujte podle těchto kroků:

1. V Dynamics 365 otevřete záznam, například záznam účtu.

2. Na panelu nástrojů Akce vyberte jeden z následujících kroků:

   * Vyberte možnost **automaticky otevíraná okna**. ![záznam popout](./media/connectors-create-api-crmonline/popout-record.png) 
   * Vyberte **odkaz Odeslat e-mailem** , abyste mohli zkopírovat úplnou adresu URL do výchozího e-mailového programu.

   ID záznamu se zobrazí v adrese URL mezi `%7b` znaky kódování a: `%7d`

   ![Najít ID záznamu](./media/connectors-create-api-crmonline/find-record-ID.png)

## <a name="troubleshoot-failed-runs"></a>Řešení neúspěšných spuštění

Pokud chcete najít a zkontrolovat neúspěšné kroky ve vaší aplikaci logiky, můžete zobrazit historii spuštění vaší aplikace logiky, stav, vstupy, výstupy atd.

1. V Azure Portal v hlavní nabídce aplikace logiky zvolte **Přehled**. V části **historie spuštění** , která zobrazuje všechny stavy spuštění vaší aplikace logiky, vyberte neúspěšné spuštění, kde najdete další informace.

   ![Stav spuštění aplikace logiky](./media/connectors-create-api-crmonline/run-history.png)

1. Rozbalí neúspěšný krok, abyste si mohli zobrazit další podrobnosti.

   ![Rozbalit neúspěšný krok](./media/connectors-create-api-crmonline/expand-failed-step.png)

1. Přečtěte si podrobnosti o kroku, jako jsou vstupy a výstupy, které vám pomůžou najít příčinu selhání.

   ![Neúspěšný krok – vstupy a výstupy](./media/connectors-create-api-crmonline/expand-failed-step-inputs-outputs.png)

Další informace o řešení potíží s Logic Apps najdete v tématu [Diagnostika selhání aplikace logiky](../logic-apps/logic-apps-diagnosing-failures.md).

## <a name="connector-reference"></a>Referenční informace ke konektorům

Podrobnosti o technických podrobnostech, jako jsou triggery, akce a omezení, jak je popsáno v souboru OpenAPI konektoru (dříve Swagger), najdete na [referenční stránce konektoru](/connectors/dynamicscrmonline/).

## <a name="next-steps"></a>Další postup

* Další informace o dalších [konektorech Logic Apps](../connectors/apis-list.md)
