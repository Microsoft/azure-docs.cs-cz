---
title: Rychlý start k volání aplikace LUIS (Language Understanding Intelligent Service) pomocí Pythonu | Microsoft Docs
description: V tomto rychlém startu zjistíte, jak volat aplikaci LUIS pomocí Pythonu.
services: cognitive-services
author: diberry
manager: cjgronlund
ms.service: cognitive-services
ms.component: language-understanding
ms.topic: quickstart
ms.date: 06/27/2018
ms.author: diberry
ms.openlocfilehash: bc7ae912d762a98c34b9a1b2d6a82d5630c4794b
ms.sourcegitcommit: f1e6e61807634bce56a64c00447bf819438db1b8
ms.translationtype: HT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/24/2018
ms.locfileid: "43769214"
---
# <a name="quickstart-call-a-luis-endpoint-using-python"></a>Rychlý start: Volání koncového bodu služby LUIS pomocí Pythonu
V tomto rychlém startu budete do koncového bodu služby LUIS předávat promluvy a získávat zpět záměr a entity.

<!-- green checkmark -->
<!--
> [!div class="checklist"]
> * Create LUIS subscription and copy key value for later use
> * View LUIS endpoint results from browser to public sample IoT app
> * Create Visual Studio C# console app to make HTTPS call to LUIS endpoint
-->

Pro účely tohoto článku potřebujete bezplatný účet [LUIS](luis-reference-regions.md#luis-website), abyste mohli vytvořit svou aplikaci LUIS.

<a name="create-luis-subscription-key"></a>
## <a name="create-luis-endpoint-key"></a>Vytvoření klíče koncového bodu služby LUIS
Abyste mohli volat ukázkovou aplikaci LUIS použitou v tomto návodu, potřebujete klíč rozhraní API služeb Cognitive Services. 

Klíč rozhraní API získáte následujícím způsobem: 

1. Nejprve musíte na webu Azure Portal vytvořit [účet rozhraní API služeb Cognitive Services](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account). Pokud ještě nemáte předplatné Azure, vytvořte si [bezplatný účet](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) před tím, než začnete.

2. Přihlaste se k webu Azure Portal na adrese https://portal.azure.com. 

3. Získejte klíč podle postupu v tématu věnovaném [vytváření klíčů koncových bodů pomocí Azure](./luis-how-to-azure-subscription.md).

4. Vraťte se na web [LUIS](luis-reference-regions.md) a přihlaste se pomocí svého účtu Azure. 

    [![](media/luis-get-started-node-get-intent/app-list.png "Snímek obrazovky se seznamem aplikací")](media/luis-get-started-node-get-intent/app-list.png)

## <a name="understand-what-luis-returns"></a>Vysvětlení toho, co služba LUIS vrací

Pokud chcete zjistit, co aplikace LUIS vrací, můžete vložit adresu URL ukázkové aplikace LUIS do okna prohlížeče. Ukázková aplikace je aplikace IoT, která rozpozná, jestli chce uživatel zapnout nebo vypnout světlo.

1. Koncový bod ukázkové aplikace má tento formát: `https://westus.api.cognitive.microsoft.com/luis/v2.0/apps/df67dcdb-c37d-46af-88e1-8b97951ca1c2?subscription-key=<YOUR_API_KEY>&verbose=false&q=turn%20on%20the%20bedroom%20light` Zkopírujte adresu URL a nahraďte hodnotu v poli `subscription-key` klíčem vašeho koncového bodu.
2. Vložte adresu URL do okna prohlížeče a stiskněte Enter. V prohlížeči se zobrazí výsledek JSON, který značí, že služba LUIS rozpoznala záměr `HomeAutomation.TurnOn` a entitu `HomeAutomation.Room` s hodnotou `bedroom`.

    ![Výsledek JSON s rozpoznaným záměrem TurnOn (Zapnout)](./media/luis-get-started-node-get-intent/turn-on-bedroom.png)
3. Změňte hodnotu parametru `q=` v adrese URL na `turn off the living room light` a stiskněte Enter. Výsledek teď značí, že služba LUIS rozpoznala záměr `HomeAutomation.TurnOff` a entitu `HomeAutomation.Room` s hodnotou `living room`. 

    ![Výsledek JSON s rozpoznaným záměrem TurnOff (Vypnout)](./media/luis-get-started-node-get-intent/turn-off-living-room.png)


## <a name="consume-a-luis-result-using-the-endpoint-api-with-python"></a>Využití výsledku ze služby LUIS pomocí rozhraní API pro koncové body a Pythonu

Pomocí Pythonu můžete získat přístup ke stejným výsledkům, jako jste viděli v okně prohlížeče v předchozím kroku.

1. Zkopírujte jeden z následujících fragmentů do souboru s názvem `quickstart-call-endpoint.py`:

   [!code-python[Console app code that calls a LUIS endpoint for Python 2.7](~/samples-luis/documentation-samples/quickstarts/analyze-text/python/2.x/quickstart-call-endpoint-2-7.py)]

   [!code-python[Console app code that calls a LUIS endpoint for Python 3.6](~/samples-luis/documentation-samples/quickstarts/analyze-text/python/3.x/quickstart-call-endpoint-3-6.py)]

2. Nahraďte hodnotu v poli `Ocp-Apim-Subscription-Key` klíčem vašeho koncového bodu LUIS.

3. Nainstalujte závislosti pomocí `pip install requests`.

4. Spusťte skript pomocí `python ./quickstart-call-endpoint.py`. Zobrazí se stejný JSON, jako jste viděli dříve v okně prohlížeče.
<!-- 
![Console window displays JSON result from LUIS](./media/luis-get-started-python-get-intent/console-turn-on.png)
-->

## <a name="clean-up-resources"></a>Vyčištění prostředků
V tomto kurzu se vytvořily dva prostředky: klíč koncového bodu LUIS a projekt jazyka C#. Na webu Azure Portal odstraňte klíč koncového bodu LUIS. Zavřete projekt sady Visual Studio a odeberte příslušný adresář ze systému souborů. 

## <a name="next-steps"></a>Další kroky
> [!div class="nextstepaction"]
> [Přidání projevů](luis-get-started-python-add-utterance.md)