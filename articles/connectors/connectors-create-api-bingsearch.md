---
title: Připojení k hledání Bingu
description: Automatizace úloh a pracovních postupů, které najdou výsledky ve vyhledávání Bingpomocí aplikací Azure Logic Apps
services: logic-apps
ms.suite: integration
ms.reviewer: klam, logicappspm
ms.topic: conceptual
ms.date: 05/21/2018
tags: connectors
ms.openlocfilehash: e547ae59f7b3260f46756825bca2bef1c10bcc97
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "75665883"
---
# <a name="find-results-in-bing-search-by-using-azure-logic-apps"></a>Hledání výsledků ve vyhledávání Bingpomocí aplikací Azure Logic Apps

Tento článek ukazuje, jak můžete najít novinky, videa a další položky prostřednictvím vyhledávání Bing z aplikace logiky s konektorem vyhledávání Bing. Tímto způsobem můžete vytvořit aplikace logiky, které automatizují úkoly a pracovní postupy pro zpracování výsledků hledání a zpřístupnit tyto položky pro jiné akce. 

Můžete například najít zpravodajské položky na základě kritérií vyhledávání a nechat Twitter tyto položky zveřejnit jako tweety ve svém kanálu Twitteru.

Pokud nemáte předplatné Azure, [zaregistrujte si bezplatný účet Azure](https://azure.microsoft.com/free/). Pokud s aplikacemi logiky teprve začínáte, přečtěte [si, co je Azure Logic Apps](../logic-apps/logic-apps-overview.md) a úvodní [příručka: Vytvořte si první aplikaci logiky](../logic-apps/quickstart-create-first-logic-app-workflow.md).
Technické informace specifické pro konektor naleznete v [odkazu na vyhledávací konektor Bing .](https://docs.microsoft.com/connectors/bingsearch/)

## <a name="prerequisites"></a>Požadavky

* [Účet služeb Cognitive Services](../cognitive-services/cognitive-services-apis-create-account.md)

* [Klíč rozhraní API pro vyhledávání Bingu](https://azure.microsoft.com/try/cognitive-services/?api=bing-news-search-api), který poskytuje přístup z aplikace logiky k rozhraním API pro vyhledávání Bingu

* Aplikace logiky, kde chcete získat přístup k centru událostí. Chcete-li spustit aplikaci logiky pomocí aktivační události Hledání Bingu, potřebujete [prázdnou aplikaci logiky](../logic-apps/quickstart-create-first-logic-app-workflow.md).

<a name="add-trigger"></a>

## <a name="add-a-bing-search-trigger"></a>Přidání aktivační události hledání Bingu

V Azure Logic Apps musí každá aplikace logiky začínat [aktivační událostí](../logic-apps/logic-apps-overview.md#logic-app-concepts), která se aktivuje, když dojde k určité události nebo když je splněna určitá podmínka. Pokaždé, když se spustí aktivační událost, modul Logic Apps vytvoří instanci aplikace logiky a spustí pracovní postup aplikace.

1. Na webu Azure Portal nebo Visual Studiu vytvořte prázdnou aplikaci logiky, která otevře Návrhář aplikací logiky. Tento příklad používá portál Azure.

2. Do vyhledávacího pole zadejte jako filtr "Vyhledávání Bingu". Ze seznamu aktivačních událostí vyberte požadovanou aktivační událost.

   Tento příklad používá tuto aktivační událost: **Hledání Bingu – na novém zpravodajském článku**

   ![Najít aktivační událost hledání Bingu](./media/connectors-create-api-bing-search/add-trigger.png)

3. Pokud se zobrazí výzva k zadání podrobností o připojení, [vytvořte nyní připojení služby Bing .](#create-connection)
Nebo pokud připojení již existuje, zadejte potřebné informace pro aktivační událost.

   V tomto příkladu zadejte kritéria pro vrácení odpovídajících zpravodajských článků z vyhledávání Bing.

   | Vlastnost | Požaduje se | Hodnota | Popis |
   |----------|----------|-------|-------------|
   | Vyhledávací dotaz | Ano | <*hledaných slov*> | Zadejte klíčová slova pro vyhledávání, která chcete použít. |
   | Trhu | Ano | <*Národní prostředí*> | Vyhledávací národní prostředí. Výchozí hodnota je "en US", ale můžete vybrat jinou hodnotu. |
   | Bezpečné vyhledávání | Ano | <*úroveň vyhledávání*> | Úroveň filtru pro vyloučení obsahu pro dospělé. Výchozí hodnota je "Střední", ale vyberete jinou úroveň. |
   | Počet | Ne | <*počet výsledků*> | Vrátí zadaný počet výsledků. Výchozí hodnota je 20, ale můžete zadat jinou hodnotu. Skutečný počet vrácených výsledků může být menší než zadaný počet. |
   | Posun | Ne | <*přeskočení hodnoty*> | Počet výsledků, které mají být před vrácením výsledků přesahovy |
   |||||

   Například:

   ![Nastavit aktivační událost](./media/connectors-create-api-bing-search/bing-search-trigger.png)

4. Vyberte interval a frekvenci, jak často má aktivační událost kontrolovat výsledky.

5. Až budete hotovi, na panelu nástrojů návrháře vyberte **Uložit**.

6. Teď pokračujte v přidávání jedné nebo více akcí do aplikace logiky pro úkoly, které chcete provést s výsledky aktivační události.

<a name="add-action"></a>

## <a name="add-a-bing-search-action"></a>Přidání akce Hledání Bingu

V Azure Logic Apps [je akce](../logic-apps/logic-apps-overview.md#logic-app-concepts) krokem ve vašem pracovním postupu, který následuje po aktivační události nebo jiné akci. V tomto příkladu aplikace logiky začíná aktivační událost Hledání Bing, která vrací diskusní články odpovídající zadaným kritériím.

1. Na webu Azure Portal nebo Visual Studiu otevřete aplikaci logiky v Návrháři aplikací logiky. Tento příklad používá portál Azure.

2. Pod aktivační událostí nebo akcí vyberte **Nový krok** > **Přidat akci**.

   Tento příklad používá tuto aktivační událost:

   **Bing Search - Na nové novinky článek**

   ![Přidání akce](./media/connectors-create-api-bing-search/add-action.png)

   Chcete-li přidat akci mezi existující kroky, přesuňte ukazatel myši na spojovací šipku. 
   Vyberte znaménko plus (**+**), které se zobrazí, a pak vyberte Přidat **akci**.

3. Do vyhledávacího pole zadejte jako filtr "Vyhledávání Bingu".
V seznamu akcí vyberte požadovanou akci.

   Tento příklad používá tuto akci:

   **Hledání bingem – seznam novinek podle dotazů**

   ![Akce Hledání bingu](./media/connectors-create-api-bing-search/bing-search-select-action.png)

4. Pokud se zobrazí výzva k zadání podrobností o připojení, [vytvořte nyní připojení služby Bing .](#create-connection) Nebo pokud připojení již existuje, zadejte potřebné informace pro akci.

   V tomto příkladu zadejte kritéria pro vrácení podmnožiny výsledků aktivační události.

   | Vlastnost | Požaduje se | Hodnota | Popis |
   |----------|----------|-------|-------------|
   | Vyhledávací dotaz | Ano | <*vyhledávací výraz*> | Zadejte výraz pro dotazování na výsledky aktivační události. Můžete vybrat z polí v seznamu dynamického obsahu nebo vytvořit výraz pomocí tvůrce výrazů. |
   | Trhu | Ano | <*Národní prostředí*> | Vyhledávací národní prostředí. Výchozí hodnota je "en US", ale můžete vybrat jinou hodnotu. |
   | Bezpečné vyhledávání | Ano | <*úroveň vyhledávání*> | Úroveň filtru pro vyloučení obsahu pro dospělé. Výchozí hodnota je "Střední", ale vyberete jinou úroveň. |
   | Počet | Ne | <*počet výsledků*> | Vrátí zadaný počet výsledků. Výchozí hodnota je 20, ale můžete zadat jinou hodnotu. Skutečný počet vrácených výsledků může být menší než zadaný počet. |
   | Posun | Ne | <*přeskočení hodnoty*> | Počet výsledků, které mají být před vrácením výsledků přesahovy |
   |||||

   Předpokládejme například, že chcete výsledky, jejichž název kategorie obsahuje slovo "tech".

   1. Klikněte do pole **Vyhledávací dotaz,** aby se zobrazil seznam dynamického obsahu. 
   V tomto seznamu vyberte **Výraz,** aby se zobrazil tvůrce výrazů. 

      ![Aktivační událost hledání Bingu](./media/connectors-create-api-bing-search/bing-search-action.png)

      Nyní můžete začít vytvářet výraz.

   2. Ze seznamu funkcí vyberte funkci **contains(),** která se pak zobrazí v poli výrazu. Klikněte na **Dynamický obsah,** aby se seznam polí znovu zobrazil, ale ujistěte se, že kurzor zůstane uvnitř závorek.

      ![Výběr funkce](./media/connectors-create-api-bing-search/expression-select-function.png)

   3. Ze seznamu polí vyberte **Kategorie**, která se převede na parametr. 
   Za první parametr přidejte čárku a za čárku přidejte toto slovo:`'tech'` 

      ![Výběr pole](./media/connectors-create-api-bing-search/expression-select-field.png)

   4. Až to bude hotové, vyberte **OK**.

      Výraz se nyní zobrazí v poli **Vyhledávací dotaz** v tomto formátu:

      ![Dokončený výraz](./media/connectors-create-api-bing-search/resolved-expression.png)

      V zobrazení kódu se tento výraz zobrazí v tomto formátu:

      `"@{contains(triggerBody()?['category'],'tech')}"`

5. Až budete hotovi, na panelu nástrojů návrháře vyberte **Uložit**.

<a name="create-connection"></a>

## <a name="connect-to-bing-search"></a>Připojení k hledání Bingu

[!INCLUDE [Create connection general intro](../../includes/connectors-create-connection-general-intro.md)]

1. Až budete vyzváni k zadání informací o připojení, zadejte tyto podrobnosti:

   | Vlastnost | Požaduje se | Hodnota | Popis |
   |----------|----------|-------|-------------|
   | Název připojení | Ano | <*název připojení*> | Název, který chcete vytvořit pro připojení |
   | Verze rozhraní API | Ano | <*API verze*> | Ve výchozím nastavení je verze rozhraní API pro vyhledávání Bing nastavena na aktuální verzi. Podle potřeby můžete vybrat starší verzi. |
   | Klíč rozhraní API | Ano | <*KLÍČ ROZHRANÍ API*> | Klíč rozhraní API pro vyhledávání Bingu, který jste získali dříve. Pokud klíč nemáte, získejte [nyní klíč rozhraní API](https://azure.microsoft.com/try/cognitive-services/?api=bing-news-search-api). |  
   |||||  

   Například:

   ![Vytvoření připojení](./media/connectors-create-api-bing-search/bing-search-create-connection.png)

2. Až to budete mít, vyberte **Vytvořit**.

## <a name="connector-reference"></a>Referenční informace ke konektorům

Technické podrobnosti, jako jsou aktivační události, akce a omezení, jak je popsáno v souboru Swagger konektoru, naleznete [na referenční stránce konektoru](/connectors/bingsearch/).

## <a name="next-steps"></a>Další kroky

* Další informace o dalších [konektorech logic apps](../connectors/apis-list.md)
