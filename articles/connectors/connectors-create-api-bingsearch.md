---
title: Připojení k Vyhledávání Bingu
description: Automatizace úloh a pracovních postupů, které vyhledají výsledky v Vyhledávání Bingu pomocí Azure Logic Apps
services: logic-apps
ms.suite: integration
ms.reviewer: klam, logicappspm
ms.topic: conceptual
ms.date: 05/21/2018
tags: connectors
ms.openlocfilehash: 306298e4338665ef52add7f46d6da8675c97c3e2
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/03/2021
ms.locfileid: "101716546"
---
# <a name="find-results-in-bing-search-by-using-azure-logic-apps"></a>Výsledky hledání Vyhledávání Bingu pomocí Azure Logic Apps

V tomto článku se dozvíte, jak můžete prostřednictvím Vyhledávání Bingu v rámci aplikace logiky pomocí konektoru Vyhledávání Bingu najít novinky, videa a další položky. Tímto způsobem můžete vytvářet aplikace logiky, které automatizují úlohy a pracovní postupy pro zpracování výsledků hledání a zpřístupňují tyto položky pro jiné akce. 

Například můžete vyhledat položky zpráv na základě kritérií hledání a vystavit na Twitteru tyto položky jako tweety v informačním kanálu na Twitteru.

Pokud nemáte předplatné Azure, [zaregistrujte si bezplatný účet Azure](https://azure.microsoft.com/free/). Pokud s Logic Apps začínáte, přečtěte si téma [co je Azure Logic Apps](../logic-apps/logic-apps-overview.md) a [rychlý Start: Vytvoření první aplikace logiky](../logic-apps/quickstart-create-first-logic-app-workflow.md).
Technické informace specifické pro konektor najdete v referenčních informacích ke [konektoru vyhledávání Bingu](/connectors/bingsearch/).

## <a name="prerequisites"></a>Požadavky

* [Účet Cognitive Services](../cognitive-services/cognitive-services-apis-create-account.md)

* [Vyhledávání Bingu klíč rozhraní API](https://azure.microsoft.com/try/cognitive-services/?api=bing-news-search-api), který poskytuje přístup z vaší aplikace logiky do rozhraní API pro vyhledávání Bingu

* Aplikace logiky, ve které chcete získat přístup k centru událostí. Pokud chcete spustit aplikaci logiky pomocí Vyhledávání Bingu triggeru, budete potřebovat [prázdnou aplikaci logiky](../logic-apps/quickstart-create-first-logic-app-workflow.md).

<a name="add-trigger"></a>

## <a name="add-a-bing-search-trigger"></a>Přidat aktivační událost Vyhledávání Bingu

V Azure Logic Apps musí každá aplikace logiky začínat [triggerem](../logic-apps/logic-apps-overview.md#logic-app-concepts), který se aktivuje, když dojde ke konkrétní události nebo když dojde ke splnění určité podmínky. Pokaždé, když se Trigger aktivuje, modul Logic Apps vytvoří instanci aplikace logiky a začne spouštět pracovní postup vaší aplikace.

1. V Azure Portal nebo Visual Studiu vytvořte prázdnou aplikaci logiky, která otevře návrháře aplikace logiky. V tomto příkladu se používá Azure Portal.

2. Do vyhledávacího pole zadejte jako filtr "vyhledávání Bing". V seznamu triggery vyberte aktivační událost, kterou chcete.

   Tento příklad používá tuto aktivační událost: **vyhledávání Bingu-on New News** .

   ![Najít aktivační událost Vyhledávání Bingu](./media/connectors-create-api-bing-search/add-trigger.png)

3. Pokud se zobrazí výzva k zadání podrobností o připojení, [Vytvořte si připojení vyhledávání Bingu nyní](#create-connection).
Nebo, pokud připojení již existuje, zadejte potřebné informace pro aktivační událost.

   V tomto příkladu zadejte kritéria pro vrácení odpovídajících článků s příspěvky z Vyhledávání Bingu.

   | Vlastnost | Požaduje se | Hodnota | Popis |
   |----------|----------|-------|-------------|
   | Vyhledávací dotaz | Ano | <*Hledat slova*> | Zadejte klíčová slova pro hledání, která chcete použít. |
   | Uvádět | Ano | <*jazyka*> | Národní prostředí hledání Výchozí hodnota je "en-US", ale můžete vybrat jinou hodnotu. |
   | Bezpečné vyhledávání | Ano | <*úroveň vyhledávání*> | Úroveň filtru pro vyloučení obsahu pro dospělé. Výchozí hodnota je "střední", ale vyberete jinou úroveň. |
   | Počet | Ne | <*výsledky – počet*> | Vrátí zadaný počet výsledků. Výchozí hodnota je 20, ale můžete zadat jinou hodnotu. Skutečný počet vrácených výsledků může být menší než zadané číslo. |
   | Posun | Ne | <*Přeskočit hodnotu*> | Počet výsledků, které se mají přeskočit před vrácením výsledků |
   |||||

   Například:

   ![Nastavení triggeru](./media/connectors-create-api-bing-search/bing-search-trigger.png)

4. Vyberte interval a frekvenci, jak často chcete, aby aktivační událost kontrolovala výsledky.

5. Až budete hotovi, na panelu nástrojů návrháře vyberte **Uložit**.

6. Nyní pokračujte v přidávání jedné nebo více akcí do aplikace logiky pro úlohy, které chcete provést s výsledky triggeru.

<a name="add-action"></a>

## <a name="add-a-bing-search-action"></a>Přidat Vyhledávání Bingu akci

V Azure Logic Apps [Akce](../logic-apps/logic-apps-overview.md#logic-app-concepts) je krok v pracovním postupu, který následuje po triggeru nebo jiné akci. V tomto příkladu se aplikace logiky spustí s triggerem Vyhledávání Bingu, který vrátí články s příspěvky odpovídající zadaným kritériím.

1. V Azure Portal nebo Visual Studiu otevřete aplikaci logiky v návrháři aplikace logiky. V tomto příkladu se používá Azure Portal.

2. V aktivační události nebo akci vyberte **Nový krok**  >  **přidat akci**.

   Tento příklad používá tuto aktivační událost:

   **Vyhledávání Bingu – nový článek novinek**

   ![Přidání akce](./media/connectors-create-api-bing-search/add-action.png)

   Pokud chcete přidat akci mezi stávajícími kroky, přesuňte ukazatel myši na šipku připojení. 
   Vyberte symbol plus ( **+** ), který se zobrazí, a pak vyberte **přidat akci**.

3. Do vyhledávacího pole zadejte jako filtr "vyhledávání Bing".
V seznamu akce vyberte akci, kterou chcete.

   Tento příklad používá tuto akci:

   **Vyhledávání Bingu – vypíše zprávy podle dotazu.**

   ![Najít Vyhledávání Bingu akci](./media/connectors-create-api-bing-search/bing-search-select-action.png)

4. Pokud se zobrazí výzva k zadání podrobností o připojení, [Vytvořte si připojení vyhledávání Bingu nyní](#create-connection). Nebo, pokud připojení již existuje, zadejte potřebné informace pro akci.

   V tomto příkladu zadejte kritéria pro vrácení podmnožiny výsledků triggeru.

   | Vlastnost | Požaduje se | Hodnota | Popis |
   |----------|----------|-------|-------------|
   | Vyhledávací dotaz | Ano | <*výraz hledání*> | Zadejte výraz pro dotazování na výsledky triggeru. Můžete vybrat z polí v seznamu dynamického obsahu nebo vytvořit výraz pomocí Tvůrce výrazů. |
   | Uvádět | Ano | <*jazyka*> | Národní prostředí hledání Výchozí hodnota je "en-US", ale můžete vybrat jinou hodnotu. |
   | Bezpečné vyhledávání | Ano | <*úroveň vyhledávání*> | Úroveň filtru pro vyloučení obsahu pro dospělé. Výchozí hodnota je "střední", ale vyberete jinou úroveň. |
   | Počet | Ne | <*výsledky – počet*> | Vrátí zadaný počet výsledků. Výchozí hodnota je 20, ale můžete zadat jinou hodnotu. Skutečný počet vrácených výsledků může být menší než zadané číslo. |
   | Posun | Ne | <*Přeskočit hodnotu*> | Počet výsledků, které se mají přeskočit před vrácením výsledků |
   |||||

   Předpokládejme například, že chcete, aby výsledky, jejichž název kategorie obsahuje slovo "tech".

   1. Klikněte do pole **vyhledávací dotaz** , aby se zobrazil seznam dynamického obsahu. 
   V tomto seznamu vyberte **výraz** , aby se zobrazil Tvůrce výrazů. 

      ![Aktivační událost Vyhledávání Bingu](./media/connectors-create-api-bing-search/bing-search-action.png)

      Teď můžete začít vytvářet svůj výraz.

   2. V seznamu funkce vyberte funkci **Contains ()** , která se pak zobrazí v poli výraz. Klikněte na **dynamický obsah** , aby se seznam polí znovu zobrazil, ale ujistěte se, že kurzor zůstane uvnitř závorek.

      ![Výběr funkce](./media/connectors-create-api-bing-search/expression-select-function.png)

   3. V seznamu polí vyberte **kategorie**, která se převede na parametr. 
   Přidejte čárku za první parametr a za čárku přidejte toto slovo: `'tech'` 

      ![Vybrat pole](./media/connectors-create-api-bing-search/expression-select-field.png)

   4. Až to bude hotové, vyberte **OK**.

      Výraz se nyní zobrazí v poli **vyhledávacího dotazu** v tomto formátu:

      ![Dokončený výraz](./media/connectors-create-api-bing-search/resolved-expression.png)

      V zobrazení kódu se tento výraz zobrazuje v tomto formátu:

      `"@{contains(triggerBody()?['category'],'tech')}"`

5. Až budete hotovi, na panelu nástrojů návrháře vyberte **Uložit**.

<a name="create-connection"></a>

## <a name="connect-to-bing-search"></a>Připojení k Vyhledávání Bingu

[!INCLUDE [Create connection general intro](../../includes/connectors-create-connection-general-intro.md)]

1. Po zobrazení výzvy k zadání informací o připojení zadejte tyto podrobnosti:

   | Vlastnost | Požaduje se | Hodnota | Popis |
   |----------|----------|-------|-------------|
   | Název připojení | Ano | <*název připojení*> | Název, který se má pro připojení vytvořit |
   | Verze rozhraní API | Ano | <*Verze API-Version*> | Ve výchozím nastavení je verze rozhraní Vyhledávání Bingu API nastavená na aktuální verzi. V případě potřeby můžete vybrat starší verzi. |
   | Klíč rozhraní API | Ano | <*Klíč rozhraní API*> | Vyhledávání Bingu klíč rozhraní API, který jste získali dříve. Pokud klíč nemáte, Získejte [hned svůj klíč rozhraní API](https://azure.microsoft.com/try/cognitive-services/?api=bing-news-search-api). |  
   |||||  

   Například:

   ![Vytvoření připojení](./media/connectors-create-api-bing-search/bing-search-create-connection.png)

2. Po dokončení vyberte **Vytvořit**.

## <a name="connector-reference"></a>Referenční informace ke konektorům

Podrobnosti o technických podrobnostech, jako jsou triggery, akce a omezení, jak je popsáno v souboru Swagger konektoru, najdete na [referenční stránce konektoru](/connectors/bingsearch/).

## <a name="next-steps"></a>Další kroky

* Další informace o dalších [konektorech Logic Apps](../connectors/apis-list.md)

