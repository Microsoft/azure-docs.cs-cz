---
title: Oprava nesprávně napsaných slov – LUIS
titleSuffix: Azure Cognitive Services
description: Opravte nesprávně napsaná slova v projevy přidáním rozhraní API Bingu pro kontrolu pravopisu v7 do dotazů na koncový bod LUIS.
services: cognitive-services
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: how-to
ms.date: 01/12/2021
ms.openlocfilehash: f416fe8ef4f6e89d07e6065d4c9435642d9bacb9
ms.sourcegitcommit: c136985b3733640892fee4d7c557d40665a660af
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/13/2021
ms.locfileid: "98179635"
---
# <a name="correct-misspelled-words-with-bing-search-resource"></a>Oprava nesprávně napsaných slov pomocí Vyhledávání Bingu prostředku

Můžete integrovat aplikaci LUIS s [vyhledávání Bingu](https://ms.portal.azure.com/#create/Microsoft.BingSearch) , aby opravila nesprávně napsaná slova v projevy před tím, než Luis předpovídá skóre a entity utterance.

## <a name="create-endpoint-key"></a>Vytvořit klíč koncového bodu

Pokud chcete vytvořit prostředek Vyhledávání Bingu v Azure Portal, postupujte podle těchto pokynů:

1. Přihlaste se k [Azure Portal](https://portal.azure.com).

2. V levém horním rohu vyberte **vytvořit prostředek** .

3. Do vyhledávacího pole zadejte `Bing Search V7` a vyberte službu.

4. Informační panel se zobrazí vpravo obsahující informace, včetně právního oznámení. Výběrem **vytvořit** zahájíte proces vytváření předplatného.

    :::image type="content" source="./media/luis-tutorial-bing-spellcheck/bing-search-resource-portal.png" alt-text="Prostředek rozhraní API Bingu pro kontrolu pravopisu v7":::

5. Na dalším panelu zadejte nastavení služby. Počkejte na dokončení procesu vytváření služby.

6. Po vytvoření prostředku v levé části otevřete okno **klíče a koncový bod** . 

7. Zkopírujte jeden z klíčů, který se přidá do hlavičky žádosti o předpověď. Budete potřebovat jenom jeden ze dvou klíčů.

8. Přidejte klíč do `mkt-bing-spell-check-key` hlavičky žádosti o předpověď.

<!--
## Using the key in LUIS test panel
There are two places in LUIS to use the key. The first is in the [test panel](luis-interactive-test.md#view-bing-spell-check-corrections-in-test-panel). The key isn't saved into LUIS but instead is a session variable. You need to set the key every time you want the test panel to apply the Bing Spell Check API v7 service to the utterance. See [instructions](luis-interactive-test.md#view-bing-spell-check-corrections-in-test-panel) in the test panel for setting the key.
-->
## <a name="adding-the-key-to-the-endpoint-url"></a>Přidání klíče k adrese URL koncového bodu
Pro každý dotaz, na který chcete použít korekci pravopisu, vyžaduje dotaz na koncový bod klíč prostředku kontroly pravopisu Bingu předaný do parametru záhlaví dotazu. Můžete mít chatovací robot, který volá LUIS, nebo můžete volat rozhraní API koncového bodu LUIS přímo. Bez ohledu na to, jakým způsobem je koncový bod volán, každé a každé volání musí obsahovat požadované informace v žádosti hlavičky pro správné fungování oprav pravopisu. Je nutné nastavit hodnotu pomocí **MKT-Bing-check-Key** na hodnotu klíče.


## <a name="send-misspelled-utterance-to-luis"></a>Odeslání nesprávně napsaného utteranceu do LUIS
1. Přidejte nesprávně napsaný utterance do dotazu předpovědi, který budete odesílat, například "jak daleko je mountainn?". V angličtině `mountain` je u jedné `n` správné pravopisné zadání.

2. LUIS odpoví s výsledkem JSON pro `How far is the mountain?` . Pokud rozhraní API Bingu pro kontrolu pravopisu v7 detekuje chybu, `query` pole v odpovědi JSON aplikace Luis obsahuje původní dotaz a `alteredQuery` pole obsahuje opravený dotaz odeslaný do Luis.

```json
{
  "query": "How far is the mountainn?",
  "alteredQuery": "How far is the mountain?",
  "topScoringIntent": {
    "intent": "Concierge",
    "score": 0.183866
  },
  "entities": []
}
```

## <a name="ignore-spelling-mistakes"></a>Ignorovat pravopisné chyby

Pokud nechcete používat službu Vyhledávání Bingu API v7, je nutné přidat správné a nesprávné zadání pravopisu.

Existují dvě řešení:

* Popisek – příklad projevy, který má všechny různé pravopisy, aby LUIS mohl zjistit správné pravopisy a také překlepy. Tato možnost vyžaduje větší úsilí při označování než použití kontroly pravopisu.
* Vytvoří seznam frází se všemi variantami slova. V tomto řešení není nutné označovat variace slov v příkladu projevy.


> [!div class="nextstepaction"]
> [Další informace o příkladu projevy](./luis-how-to-add-entities.md)
