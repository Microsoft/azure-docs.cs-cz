---
title: Připojení k Bingu vyhledávání - Azure Logic Apps | Microsoft Docs
description: Hledání zpráv pomocí rozhraní API REST vyhledávání v Bingu a Azure Logic Apps
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
ms.openlocfilehash: 8ac67f9df0e5baccc668c2aeb70f65d96e574df5
ms.sourcegitcommit: 0fa8b4622322b3d3003e760f364992f7f7e5d6a9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/27/2018
ms.locfileid: "37021281"
---
# <a name="find-news-with-bing-search-and-azure-logic-apps"></a>Hledání zpráv s Bing vyhledávání a Azure Logic Apps 

Tento článek ukazuje, jak můžete najít příspěvky, videa a jiné položky prostřednictvím Bing vyhledávání z v rámci aplikace logiky s konektorem služby Bing vyhledávání. Tímto způsobem můžete vytvořit aplikace logiky, které automatizují úlohy a pracovní postupy pro zpracování výsledky hledání a ujistěte se, tyto položky k dispozici pro další akce. 

Můžete například najít příspěvků na základě kritérií vyhledávání a mít post ty položky, jako tweetů ve vaší služby Twitter feed služby Twitter.

Pokud nemáte předplatné Azure, <a href="https://azure.microsoft.com/free/" target="_blank">zaregistrujte si bezplatný účet Azure</a>. Pokud jste nové aplikace logiky, přečtěte si [co je Azure Logic Apps](../logic-apps/logic-apps-overview.md) a [rychlý start: vytvoření první aplikace logiky](../logic-apps/quickstart-create-first-logic-app-workflow.md).
Technické informace specifické pro konektor najdete v tématu <a href="https://docs.microsoft.com/connectors/bingsearch/" target="blank">Bing vyhledávání konektor odkaz</a>.

## <a name="prerequisites"></a>Požadavky

* A [účet kognitivní Services](../cognitive-services/cognitive-services-apis-create-account.md)

* A [klíč rozhraní API služby Bing Search](https://azure.microsoft.com/try/cognitive-services/?api=bing-news-search-api), který zajišťuje přístup z aplikace logiky k rozhraní API pro vyhledávání Bingu 

* Aplikace logiky, kde chcete získat přístup vašeho centra událostí. Pokud chcete spustit aplikaci logiky s aktivační událost Bing vyhledávání, musíte [aplikace logiky prázdné](../logic-apps/quickstart-create-first-logic-app-workflow.md). 

<a name="add-trigger"></a>

## <a name="add-a-bing-search-trigger"></a>Přidat aktivační událost Bing vyhledávání

V Azure Logic Apps, musí začínat každou aplikaci logiky [aktivační událost](../logic-apps/logic-apps-overview.md#logic-app-concepts), která se stane, aktivuje se při určité události, nebo když je splněna určitá podmínka. Pokaždé, když aktivační událost je aktivována, modul Logic Apps vytvoří instanci aplikace logiky a spuštění pracovního postupu vaší aplikace.

1. V portálu Azure nebo v sadě Visual Studio vytvořte prázdnou logiku aplikace, která otevře Návrhář aplikace logiky. Tento příklad používá portál Azure.

2. Do vyhledávacího pole zadejte "Vyhledat Bing" jako filtr. Ze seznamu aktivační události vyberte aktivační události, které chcete. 

   Tento příklad používá této aktivační události: **vyhledávání Bing - na nový článek zprávy**

   ![Najít aktivační událost Bing vyhledávání](./media/connectors-create-api-bing-search/add-trigger.png)

3. Pokud se zobrazí výzva k podrobnosti připojení [vytvořit připojení k vyhledávání Bing nyní](#create-connection). Nebo, pokud připojení již existuje, zadejte informace potřebné pro aktivační událost.

   V tomto příkladu zadejte kritéria pro zobrazení odpovídající články s příspěvky Bing vyhledávání.

   | Vlastnost | Požaduje se | Hodnota | Popis | 
   |----------|----------|-------|-------------| 
   | Vyhledávací dotaz | Ano | <*hledaná slova*> | Zadejte hledaná klíčová slova, kterou chcete použít. |
   | Trh | Ano | <*Národní prostředí*> | Hledání národní prostředí. Výchozí hodnota je "en US", ale můžete vybrat jinou hodnotu. | 
   | Bezpečné vyhledávání | Ano | <*úrovně vyhledávání*> | Úroveň filtru pro vyloučení obsah pro dospělé. Výchozí hodnota je "Střední", ale můžete vybrat jinou úroveň. | 
   | Počet | Ne | <*počet výsledků*> | Vrátí zadaný počet výsledků. Výchozí hodnota je 20, ale můžete zadat jinou hodnotu. Skutečný počet vrácených výsledků může být nižší než zadané číslo. | 
   | Posun | Ne | <*Skip – hodnota*> | Počet výsledků tak, aby přeskočil před vrácením výsledky | 
   ||||| 

   Příklad:

   ![Nastavit aktivační události](./media/connectors-create-api-bing-search/bing-search-trigger.png)

4. Vyberte interval a četnost pro požadovanou četnost aktivační událost zkontrolujte výsledky.

5. Vyberte, když jste hotovi, na panelu nástrojů návrháře **Uložit**. 

6. Teď pokračujte v přidávání jednoho nebo více akcí do aplikace logiky pro úlohy, které chcete provést ve výsledcích aktivační události.

<a name="add-action"></a>

## <a name="add-a-bing-search-action"></a>Přidat akci Bing vyhledávání

V Azure Logic Apps [akce](../logic-apps/logic-apps-overview.md#logic-app-concepts) je krok ve svém pracovním postupu, který následuje aktivační události nebo jiné akce. V tomto příkladu aplikace logiky začíná Bing vyhledávání aktivační událost, která vrátí články s příspěvky odpovídající zadaným kritériím. 

1. V portálu Azure nebo Visual Studio otevřete aplikaci logiky v návrháři aplikace logiky. Tento příklad používá portál Azure.

2. V části o aktivační události nebo akce, zvolte **nový krok** > **přidat akci**.

   Tento příklad používá této aktivační události: **vyhledávání Bing - na nový článek zprávy**

   ![Akce pro přidání](./media/connectors-create-api-bing-search/add-action.png)

   A přidejte akci mezi existující kroky, přesuňte ukazatel myši nad připojování šipkou. 
   Vyberte znaménko plus (**+**), se zobrazí a potom vyberte **přidat akci**.

3. Do vyhledávacího pole zadejte "Vyhledat Bing" jako filtr.
Ze seznamu Akce vyberte akci, kterou chcete.

   Tento příklad používá tuto akci: **Bing vyhledávání - seznamu zprávy dotazu**

   ![Najít akce Bing vyhledávání](./media/connectors-create-api-bing-search/bing-search-select-action.png)

4. Pokud se zobrazí výzva k podrobnosti připojení [vytvořit připojení k vyhledávání Bing nyní](#create-connection). Nebo, pokud připojení již existuje, zadejte informace potřebné pro akci. 

   V tomto příkladu zadejte kritéria pro vrácení podmnožinu výsledků aktivační události.

   | Vlastnost | Požaduje se | Hodnota | Popis | 
   |----------|----------|-------|-------------| 
   | Vyhledávací dotaz | Ano | <*hledaný výraz*> | Zadejte výraz pro dotazování na výsledky aktivační události. Můžete vybrat z pole v seznamu dynamického obsahu nebo vytvořit výraz s Tvůrce. |
   | Trh | Ano | <*Národní prostředí*> | Hledání národní prostředí. Výchozí hodnota je "en US", ale můžete vybrat jinou hodnotu. | 
   | Bezpečné vyhledávání | Ano | <*úrovně vyhledávání*> | Úroveň filtru pro vyloučení obsah pro dospělé. Výchozí hodnota je "Střední", ale můžete vybrat jinou úroveň. | 
   | Počet | Ne | <*počet výsledků*> | Vrátí zadaný počet výsledků. Výchozí hodnota je 20, ale můžete zadat jinou hodnotu. Skutečný počet vrácených výsledků může být nižší než zadané číslo. | 
   | Posun | Ne | <*Skip – hodnota*> | Počet výsledků tak, aby přeskočil před vrácením výsledky | 
   ||||| 

   Předpokládejme například, že chcete těchto výsledků, jejichž název kategorie zahrnuje slovo "technické". 
   
   1. Kliknutím na tlačítko ve **vyhledávací dotaz** pole, se zobrazí v seznamu dynamického obsahu. 
   Z tohoto seznamu vyberte **výraz** , zobrazí se Tvůrce. 

      ![Aktivační událost Bing vyhledávání](./media/connectors-create-api-bing-search/bing-search-action.png)

      Nyní můžete začít vytváření výraz.

   2. Vyberte ze seznamu funkcí **Metoda contains()** funkce, které se potom zobrazí v poli výrazu. Klikněte na tlačítko **dynamický obsah** , aby se znovu zobrazí seznam polí, ale ujistěte se, že kurzor zůstává v závorkách.

      ![Vyberte funkci](./media/connectors-create-api-bing-search/expression-select-function.png)

   3. Ze seznamu polí vyberte **kategorie**, která převede na parametr. 
   Přidat čárkou po první parametr a za čárkou, přidat slovo: `'tech'` 

      ![Vyberte pole](./media/connectors-create-api-bing-search/expression-select-field.png)
   
   4. Až budete hotoví, zvolte **OK**.

      Výraz nyní se zobrazí v **vyhledávací dotaz** pole v tomto formátu:

      ![Dokončení výrazu](./media/connectors-create-api-bing-search/resolved-expression.png)

      Tento výraz zobrazí v zobrazení kódu, se v tomto formátu:

      `"@{contains(triggerBody()?['category'],'tech')}"`

5. Vyberte, když jste hotovi, na panelu nástrojů návrháře **Uložit**.

<a name="create-connection"></a>

## <a name="connect-to-bing-search"></a>Připojení k Bingu vyhledávání

[!INCLUDE [Create connection general intro](../../includes/connectors-create-connection-general-intro.md)] 

1. Když se zobrazí výzva k informace o připojení, uveďte následující informace:

   | Vlastnost | Požaduje se | Hodnota | Popis | 
   |----------|----------|-------|-------------| 
   | Název připojení | Ano | <*Název připojení*> | Název, který chcete vytvořit pro připojení k |
   | Verze rozhraní API | Ano | <*Verze rozhraní API.*> | Verze rozhraní API služby Bing vyhledávání je standardně nastavena na aktuální verzi. V případě potřeby můžete vybrat starší verze. | 
   | Klíč rozhraní API | Ano | <*Klíč rozhraní API*> | Klíč rozhraní API pro Bing vyhledávání, který jste získali dříve. Pokud nemáte klíč, vaše [klíč rozhraní API nyní](https://azure.microsoft.com/try/cognitive-services/?api=bing-news-search-api). |  
   |||||  

   Příklad:

   ![Vytvoření připojení](./media/connectors-create-api-bing-search/bing-search-create-connection.png)

2. Jakmile budete hotoví, vyberte **Vytvořit**.

## <a name="connector-reference"></a>Referenční informace ke konektorům

Technické podrobnosti, jako je například aktivačních událostí, akcí a omezení, jak je popsáno pomocí souboru Swagger konektoru, najdete v článku [stránka s referencemi k konektoru](/connectors/bingsearch/). 

## <a name="get-support"></a>Získat podporu

* Pokud máte dotazy, navštivte [fórum Azure Logic Apps](https://social.msdn.microsoft.com/Forums/en-US/home?forum=azurelogicapps).
* Pokud chcete zanechat své nápady na funkce nebo hlasovat, navštivte [web zpětné vazby od uživatelů Logic Apps](http://aka.ms/logicapps-wish).

## <a name="next-steps"></a>Další postup

* Další informace o dalších [konektory Logic Apps](../connectors/apis-list.md)
