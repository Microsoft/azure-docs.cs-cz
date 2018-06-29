---
title: Kurz volání aplikace LUIS (Language Understanding Intelligent Service) pomocí jazyka C# | Microsoft Docs
description: V tomto kurzu zjistíte, jak volat aplikaci LUIS pomocí jazyka C#.
services: cognitive-services
author: v-geberr
manager: kaiqb
ms.service: cognitive-services
ms.component: language-understanding
ms.topic: tutorial
ms.date: 12/13/2017
ms.author: v-geberr
ms.openlocfilehash: 0416d19d27810a2ab8eeb20e16b2f921fc7826ee
ms.sourcegitcommit: 301855e018cfa1984198e045872539f04ce0e707
ms.translationtype: HT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/19/2018
ms.locfileid: "36263485"
---
# <a name="tutorial-call-a-luis-endpoint-using-c"></a>Kurz: Volání koncového bodu služby LUIS pomocí jazyka C#

Do koncového bodu služby LUIS můžete předávat promluvy a získávat zpět záměr a entity.

<!-- green checkmark -->
> [!div class="checklist"]
> * Vytvoření předplatného LUIS a zkopírování hodnoty klíče pro pozdější použití
> * Zobrazení výsledků z koncového bodu služby LUIS ve veřejné ukázkové aplikaci IoT v prohlížeči
> * Vytvoření konzolové aplikace jazyka C# v sadě Visual Studio pro volání koncového bodu služby LUIS přes protokol HTTPS

<!-- link to free account --> Pro účely tohoto článku potřebujete bezplatný účet [LUIS][LUIS], abyste mohli vytvořit svou aplikaci LUIS.

## <a name="create-luis-subscription-key"></a>Vytvoření klíče předplatného LUIS
1. Nejprve musíte na webu Azure Portal vytvořit [účet rozhraní API služeb Cognitive Services](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account). Pokud ještě nemáte předplatné Azure, vytvořte si [bezplatný účet](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) před tím, než začnete.

2. Přihlaste se k webu Azure Portal na adrese https://portal.azure.com. 

3. Získejte klíč podle postupu v tématu věnovaném [vytváření klíčů předplatného pomocí Azure](./luis-how-to-azure-subscription.md).

4. Vraťte se na web [LUIS](luis-reference-regions.md). Přihlaste se pomocí svého účtu Azure. 

    [![](media/luis-get-started-cs-get-intent/app-list.png "Snímek obrazovky se seznamem aplikací")](media/luis-get-started-cs-get-intent/app-list.png)

## <a name="understand-what-luis-returns"></a>Vysvětlení toho, co služba LUIS vrací

Pokud chcete zjistit, co aplikace LUIS vrací, můžete vložit adresu URL ukázkové aplikace LUIS do okna prohlížeče. Ukázková aplikace je aplikace IoT, která rozpozná, jestli chce uživatel zapnout nebo vypnout světlo.

1. Koncový bod ukázkové aplikace má tento formát: `https://westus.api.cognitive.microsoft.com/luis/v2.0/apps/df67dcdb-c37d-46af-88e1-8b97951ca1c2?subscription-key=<YOUR_API_KEY>&verbose=false&q=turn%20on%20the%20bedroom%20light` Zkopírujte adresu URL a nahraďte hodnotu v poli `subscription-key` klíčem vašeho předplatného.
2. Vložte adresu URL do okna prohlížeče a stiskněte Enter. V prohlížeči se zobrazí výsledek JSON, který značí, že služba LUIS rozpoznala záměr `HomeAutomation.TurnOn` a entitu `HomeAutomation.Room` s hodnotou `bedroom`.

    ![Výsledek JSON s rozpoznaným záměrem TurnOn (Zapnout)](./media/luis-get-started-cs-get-intent/turn-on-bedroom.png)
3. Změňte hodnotu parametru `q=` v adrese URL na `turn off the living room light` a stiskněte Enter. Výsledek teď značí, že služba LUIS rozpoznala záměr `HomeAutomation.TurnOff` a entitu `HomeAutomation.Room` s hodnotou `living room`. 

    ![Výsledek JSON s rozpoznaným záměrem TurnOff (Vypnout)](./media/luis-get-started-cs-get-intent/turn-off-living-room.png)


## <a name="consume-a-luis-result-using-the-endpoint-api-with-c"></a>Využití výsledku ze služby LUIS pomocí rozhraní API pro koncové body a jazyka C# 

Pomocí jazyka C# můžete získat přístup ke stejným výsledkům, jako jste viděli v okně prohlížeče v předchozím kroku. 

1. V sadě Visual Studio vytvořte novou konzolovou aplikaci. Zkopírujte následující kód a uložte ho do souboru *.cs:
    
   [!code-csharp[Console app code that calls a LUIS endpoint](~/samples-luis/documentation-samples/endpoint-api-samples/csharp/Program.cs)]
1. Nahraďte hodnotu proměnné `subscriptionKey` klíčem vašeho předplatného LUIS.

3. V projektu sady Visual Studio přidejte odkaz na **System.Web**.

4. Spusťte konzolovou aplikaci. Zobrazí se stejný JSON, jako jste viděli dříve v okně prohlížeče.

![Okno konzoly se zobrazeným výsledkem JSON ze služby LUIS](./media/luis-get-started-cs-get-intent/console-turn-on.png)

## <a name="clean-up-resources"></a>Vyčištění prostředků
V tomto kurzu se vytvořily dva prostředky: klíč předplatného LUIS a projekt jazyka C#. Na webu Azure Portal odstraňte klíč předplatného LUIS. Zavřete projekt sady Visual Studio a odeberte příslušný adresář ze systému souborů. 

## <a name="next-steps"></a>Další kroky
> [!div class="nextstepaction"]
> [Přidání projevů](luis-get-started-cs-add-utterance.md)

[LUIS]: https://docs.microsoft.com/azure/cognitive-services/luis/luis-reference-regions#luis-website