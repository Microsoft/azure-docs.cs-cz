---
title: Připojení k Bingu pro vyhledávání – Azure Logic Apps | Dokumentace Microsoftu
description: Hledání zpráv pomocí REST API pro vyhledávání Bingu a Azure Logic Apps
author: ecfan
manager: jeconnoc
ms.author: estfan
ms.date: 05/21/2018
ms.topic: article
ms.service: logic-apps
services: logic-apps
ms.reviewer: klam, LADocs
ms.suite: integration
tags: connectors
ms.openlocfilehash: 9997f27f360f84ff3cd185d7c12c45519513d82b
ms.sourcegitcommit: fbdfcac863385daa0c4377b92995ab547c51dd4f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/30/2018
ms.locfileid: "50233085"
---
# <a name="find-news-with-bing-search-and-azure-logic-apps"></a>Hledání zpráv pomocí Bingu pro vyhledávání a Azure Logic Apps 

Tento článek popisuje, jak můžete najít zprávy, videa a dalších položek prostřednictvím Bingu pro vyhledávání z uvnitř aplikace logiky s konektorem Bingu pro vyhledávání. Tímto způsobem můžete vytvořit aplikace logiky pro automatizaci úloh a pracovních postupů pro zpracování výsledky hledání a nastavte tyto položky k dispozici pro další akce. 

Můžete například najít příspěvků na základě kritérií vyhledávání a mít Twitter publikovat tyto položky jako feed tweetů na Twitteru.

Pokud nemáte předplatné Azure, <a href="https://azure.microsoft.com/free/" target="_blank">zaregistrujte si bezplatný účet Azure</a>. Pokud se službou logic Apps teprve začínáte, přečtěte si [co je Azure Logic Apps](../logic-apps/logic-apps-overview.md) a [rychlý start: vytvoření první aplikace logiky](../logic-apps/quickstart-create-first-logic-app-workflow.md).
Technické informace specifické pro konektor najdete v tématu <a href="https://docs.microsoft.com/connectors/bingsearch/" target="blank">referenční informace ke konektorům vyhledávání Bingu</a>.

## <a name="prerequisites"></a>Požadavky

* A [účtu služeb Cognitive Services](../cognitive-services/cognitive-services-apis-create-account.md)

* A [klíč rozhraní API Bingu pro vyhledávání](https://azure.microsoft.com/try/cognitive-services/?api=bing-news-search-api), který zajišťuje přístup z aplikace logiky k rozhraní API pro vyhledávání Bingu 

* Aplikace logiky, ve které chcete přístup k Centru událostí. Spuštění aplikace logiky s triggerem Bingu pro vyhledávání, je nutné [prázdné aplikace logiky](../logic-apps/quickstart-create-first-logic-app-workflow.md). 

<a name="add-trigger"></a>

## <a name="add-a-bing-search-trigger"></a>Přidání triggeru vyhledávání Bingu

V Azure Logic Apps, musí začínat každá aplikace logiky [aktivační událost](../logic-apps/logic-apps-overview.md#logic-app-concepts), který se aktivuje při určité události nebo když je splněna konkrétní podmínka. Pokaždé, když se trigger aktivuje, modul Logic Apps vytvoří instanci aplikace logiky a spustí pracovní postup vaší aplikace.

1. Webu Azure portal nebo Visual Studio vytvořte prázdné aplikace logiky, otevře se návrhář aplikace logiky. Tento příklad používá na webu Azure portal.

2. Do vyhledávacího pole zadejte jako filtr "Vyhledávání Bingu". Ze seznamu triggerů vyberte trigger, který chcete. 

   Tento příklad používá tento trigger: **vyhledávání Bingu – na nový příspěvek**

   ![Najděte aktivační událost pro vyhledávání Bingu](./media/connectors-create-api-bing-search/add-trigger.png)

3. Pokud se zobrazí výzva k připojení podrobnosti [vytvořit připojení k Bingu pro vyhledávání nyní](#create-connection). Nebo, pokud už připojení existuje, zadejte informace potřebné pro aktivační událost.

   V tomto příkladu zadejte kritéria pro vrácení odpovídající články o novinkách z Bingu pro vyhledávání.

   | Vlastnost | Požaduje se | Hodnota | Popis | 
   |----------|----------|-------|-------------| 
   | Vyhledávací dotaz | Ano | <*hledaná slova*> | Zadejte hledaná klíčová slova, které chcete použít. |
   | Trh | Ano | <*Národní prostředí*> | Hledání národní prostředí. Výchozí hodnota je "en US", ale můžete vybrat jinou hodnotu. | 
   | Bezpečné vyhledávání | Ano | <*úroveň hledání*> | Úroveň filtru pro vyloučení obsah pro dospělé. Výchozí hodnota je "Střední", ale vyberte jinou úroveň. | 
   | Počet | Ne | <*počet výsledků*> | Vrátí zadaný počet výsledků. Výchozí hodnota je 20, ale můžete zadat jinou hodnotu. Skutečný počet vrácených výsledků může být nižší než zadané číslo. | 
   | Posun | Ne | <*Hodnota vynechání*> | Počet výsledků, chcete-li přeskočit před vrácením výsledky | 
   ||||| 

   Příklad:

   ![Nastavení aktivační události](./media/connectors-create-api-bing-search/bing-search-trigger.png)

4. Vyberte interval a frekvenci jak často chcete, aby aktivační událost si zkontrolujte výsledky.

5. Jakmile budete hotovi, na panelu nástrojů návrháře zvolte **Uložit**. 

6. Teď pokračujte v přidávání jednu nebo více akcí do aplikace logiky pro úlohy, které chcete provést s výsledky aktivační události.

<a name="add-action"></a>

## <a name="add-a-bing-search-action"></a>Přidání akce vyhledávání Bingu

V Azure Logic Apps [akce](../logic-apps/logic-apps-overview.md#logic-app-concepts) je krok v pracovním postupu, který následuje aktivační události nebo jiné akce. V tomto příkladu aplikace logiky začíná triggerem Bingu pro vyhledávání, která vrací články o novinkách odpovídající zadaným kritériím. 

1. Webu Azure portal nebo Visual Studio otevřete aplikaci logiky v návrháři aplikace logiky. Tento příklad používá na webu Azure portal.

2. V části triggeru nebo akce, zvolte **nový krok** > **přidat akci**.

   Tento příklad používá tento trigger: **vyhledávání Bingu – na nový příspěvek**

   ![Přidání akce](./media/connectors-create-api-bing-search/add-action.png)

   Přidání akce mezi stávající kroky, najeďte myší na připojení šipku. 
   Vyberte znaménko plus (**+**), který se zobrazí a klikněte na tlačítko **přidat akci**.

3. Do vyhledávacího pole zadejte jako filtr "Vyhledávání Bingu".
Ze seznamu akcí vyberte požadovanou akci.

   Tento příklad používá tuto akci: **vyhledávání Bingu – seznam zpráv podle dotazu**

   ![Najít akce vyhledávání Bingu](./media/connectors-create-api-bing-search/bing-search-select-action.png)

4. Pokud se zobrazí výzva k připojení podrobnosti [vytvořit připojení k Bingu pro vyhledávání nyní](#create-connection). Nebo pokud už připojení existuje, uveďte potřebné informace pro akci. 

   V tomto příkladu zadejte kritéria pro vrácení podmnožiny výsledků aktivační události.

   | Vlastnost | Požaduje se | Hodnota | Popis | 
   |----------|----------|-------|-------------| 
   | Vyhledávací dotaz | Ano | <*hledaný výraz*> | Zadejte výraz pro dotazování na výsledky aktivační události. Můžete vybrat z polí v seznamu dynamického obsahu nebo vytvořte výraz s Tvůrce výrazů. |
   | Trh | Ano | <*Národní prostředí*> | Hledání národní prostředí. Výchozí hodnota je "en US", ale můžete vybrat jinou hodnotu. | 
   | Bezpečné vyhledávání | Ano | <*úroveň hledání*> | Úroveň filtru pro vyloučení obsah pro dospělé. Výchozí hodnota je "Střední", ale vyberte jinou úroveň. | 
   | Počet | Ne | <*počet výsledků*> | Vrátí zadaný počet výsledků. Výchozí hodnota je 20, ale můžete zadat jinou hodnotu. Skutečný počet vrácených výsledků může být nižší než zadané číslo. | 
   | Posun | Ne | <*Hodnota vynechání*> | Počet výsledků, chcete-li přeskočit před vrácením výsledky | 
   ||||| 

   Předpokládejme například, že chcete tyto výsledky, jehož název kategorie obsahuje slovo "tech". 
   
   1. Klepněte **vyhledávací dotaz** pole, zobrazí se seznam dynamického obsahu. 
   Z tohoto seznamu, zvolte **výraz** tak objeví Tvůrce výrazů. 

      ![Aktivační událost pro vyhledávání Bingu](./media/connectors-create-api-bing-search/bing-search-action.png)

      Teď můžete začít vytvářet výrazu.

   2. V seznamu funkcí, vyberte **Metoda contains()** funkce, která se pak objeví v poli výraz. Klikněte na tlačítko **dynamický obsah** tak, aby se znovu zobrazí v seznamu polí, ale ujistěte se, že ukazatel myši zůstane v závorkách.

      ![Vyberte funkce](./media/connectors-create-api-bing-search/expression-select-function.png)

   3. V seznamu polí vyberte **kategorie**, která převede na parametr. 
   Přidejte čárku po první parametr a za čárkou, přidejte toto slovo: `'tech'` 

      ![Vybrat pole](./media/connectors-create-api-bing-search/expression-select-field.png)
   
   4. Jakmile budete hotovi, zvolte **OK**.

      Výraz se teď zobrazí v **vyhledávací dotaz** pole v tomto formátu:

      ![Dokončení výrazu](./media/connectors-create-api-bing-search/resolved-expression.png)

      V zobrazení kódu zobrazí se tento výraz v následujícím formátu:

      `"@{contains(triggerBody()?['category'],'tech')}"`

5. Jakmile budete hotovi, na panelu nástrojů návrháře zvolte **Uložit**.

<a name="create-connection"></a>

## <a name="connect-to-bing-search"></a>Připojení k Bingu pro vyhledávání

[!INCLUDE [Create connection general intro](../../includes/connectors-create-connection-general-intro.md)] 

1. Po zobrazení výzvy pro informace o připojení, uveďte následující údaje:

   | Vlastnost | Požaduje se | Hodnota | Popis | 
   |----------|----------|-------|-------------| 
   | Název připojení | Ano | <*Název připojení*> | Název pro vytvoření připojení |
   | Verze rozhraní API | Ano | <*Verze API-version*> | Ve výchozím nastavení je nastavit verzi rozhraní API Bingu pro vyhledávání na aktuální verzi. Podle potřeby můžete vybrat ze starší verze. | 
   | Klíč rozhraní API | Ano | <*Klíč rozhraní API*> | Klíč rozhraní API Bingu pro vyhledávání, které jste získali dříve. Pokud nemáte klíč, vaše [klíč rozhraní API teď](https://azure.microsoft.com/try/cognitive-services/?api=bing-news-search-api). |  
   |||||  

   Příklad:

   ![Vytvoření připojení](./media/connectors-create-api-bing-search/bing-search-create-connection.png)

2. Jakmile budete hotoví, vyberte **Vytvořit**.

## <a name="connector-reference"></a>Referenční informace ke konektorům

Technické podrobnosti, jako jsou triggery, akce a omezení, jak je popsáno v souboru Swagger konektoru, najdete v článku [konektoru referenční stránce](/connectors/bingsearch/). 

## <a name="get-support"></a>Získat podporu

* Pokud máte dotazy, navštivte [fórum Azure Logic Apps](https://social.msdn.microsoft.com/Forums/en-US/home?forum=azurelogicapps).
* Pokud chcete zanechat své nápady na funkce nebo hlasovat, navštivte [web zpětné vazby od uživatelů Logic Apps](https://aka.ms/logicapps-wish).

## <a name="next-steps"></a>Další postup

* Další informace o dalších [konektory Logic Apps](../connectors/apis-list.md)
