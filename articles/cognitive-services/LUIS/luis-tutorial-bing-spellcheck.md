---
title: Oprava nesprávně napsaných slov – LUIS
titleSuffix: Azure Cognitive Services
description: Správné slova s překlepem v projevy tak, že přidáte API V7 kontrola pravopisu Bingu dotazy koncový bod služby LUIS.
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: conceptual
ms.date: 11/19/2019
ms.author: diberry
ms.openlocfilehash: 51b0d02443df872a7fae13116ea77b13d05055fa
ms.sourcegitcommit: d6b68b907e5158b451239e4c09bb55eccb5fef89
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/20/2019
ms.locfileid: "74225448"
---
# <a name="correct-misspelled-words-with-bing-spell-check"></a>Správné chybně napsaná slova s kontrolu pravopisu Bingu

Aplikaci LUIS můžete integrovat s [rozhraní API Bingu pro kontrolu pravopisu v7](https://azure.microsoft.com/services/cognitive-services/spell-check/) , aby se opravila nesprávně napsaná slova v projevy předtím, než Luis předpovídá skóre a entity utterance. 

[!INCLUDE [Not supported in V3 API prediction endpoint](./includes/v2-support-only.md)]


## <a name="create-first-key-for-bing-spell-check-v7"></a>Vytvoření první klíč pro V7 kontrola pravopisu Bingu

Váš [první rozhraní API Bingu pro kontrolu pravopisu klíč v7](https://azure.microsoft.com/try/cognitive-services/?api=spellcheck-api) je zdarma. 

![Vytvořit bezplatný klíč](./media/luis-tutorial-bing-spellcheck/free-key.png)

<a name="create-subscription-key"></a>

## <a name="create-endpoint-key"></a>Vytvoření klíče koncového bodu
Je-li klíč volný vypršela platnost, vytvořte klíč koncového bodu.

1. Přihlaste se k [portálu Azure](https://portal.azure.com). 

2. V levém horním rohu vyberte **vytvořit prostředek** .

3. Do vyhledávacího pole zadejte `Bing Spell Check API V7`.

    ![Zkontrolujte vyhledávání pro kontrolu pravopisu Bingu, rozhraní API v7 nabízí](./media/luis-tutorial-bing-spellcheck/portal-search.png)

4. Vyberte službu. 

5. Panelu informací se zobrazí napravo obsahující informace, včetně právní upozornění. Výběrem **vytvořit** zahájíte proces vytváření předplatného. 

6. V panelu Další zadejte nastavení služby. Počkejte na dokončení procesu vytváření služby.

    ![Zadejte nastavení služby](./media/luis-tutorial-bing-spellcheck/subscription-settings.png)

7. Vyberte **všechny prostředky** pod nadpisem **Oblíbené položky** na levé straně navigace.

8. Vyberte novou službu. Jeho typ je **Cognitive Services** a umístění je **globální**. 

9. Na hlavním panelu vyberte **klíče** a zobrazte si nové klíče.

    ![Získejte klíče](./media/luis-tutorial-bing-spellcheck/grab-keys.png)

10. První klíč si zkopírujte. Potřebujete jenom jeden ze dvou klíčů. 

<!--
## Using the key in LUIS test panel
There are two places in LUIS to use the key. The first is in the [test panel](luis-interactive-test.md#view-bing-spell-check-corrections-in-test-panel). The key isn't saved into LUIS but instead is a session variable. You need to set the key every time you want the test panel to apply the Bing Spell Check API v7 service to the utterance. See [instructions](luis-interactive-test.md#view-bing-spell-check-corrections-in-test-panel) in the test panel for setting the key.
-->
## <a name="adding-the-key-to-the-endpoint-url"></a>Přidávání klíče do adresu URL koncového bodu
Dotaz koncový bod, musí klíč předávaný do parametrů řetězce dotazu pro každý dotaz, že který chcete použít opravy pravopisu. Můžete mít chatovací robot, který volá LUIS nebo koncový bod rozhraní API LUIS může volat přímo. Bez ohledu na to, jak se nazývá koncový bod musí obsahovat každého volání požadované informace pro opravy pravopisu fungovala správně.

Koncový bod adresy URL obsahuje několik hodnot, které je potřeba předávat správně. Klíč API kontrola pravopisu Bingu v7 je jenom další jeden z nich. Je nutné nastavit parametr **kontroly pravopisu** na hodnotu true a je nutné nastavit hodnotu klíče **Bing-check-Subscription-Key** na hodnotu Key:

`https://{region}.api.cognitive.microsoft.com/luis/v2.0/apps/{appID}?subscription-key={luisKey}&spellCheck=**true**&bing-spell-check-subscription-key=**{bingKey}**&verbose=true&timezoneOffset=0&q={utterance}`

## <a name="send-misspelled-utterance-to-luis"></a>Chybně napsaná utterance posílala LUIS
1. Ve webovém prohlížeči zkopírujte předchozí řetězec a nahraďte `region`, `appId`, `luisKey`a `bingKey` vlastními hodnotami. Pokud se liší od [oblasti](luis-reference-regions.md)publikování, nezapomeňte použít oblast koncového bodu.

2. Přidání chybně utterance jako například "jak daleko je mountainn?". V angličtině, `mountain`s jedním `n`, je správný pravopis. 

3. Vyberte zadejte odeslat dotaz do služby LUIS.

4. LUIS odpoví s výsledkem JSON pro `How far is the mountain?`. Pokud rozhraní API Bingu pro kontrolu pravopisu v7 zjistí chybu, `query` pole v odpovědi JSON aplikace LUIS obsahuje původní dotaz a pole `alteredQuery` obsahuje opravený dotaz odeslaný do LUIS.

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

## <a name="ignore-spelling-mistakes"></a>Ignorovat pravopisné chyby hned

Pokud nechcete používat službu rozhraní API Bingu pro kontrolu pravopisu v7, je nutné přidat správné a nesprávné zadání pravopisu. 

Existují dvě řešení:

* Popisek – příklad projevy, který má všechny různé pravopisy, aby LUIS mohl zjistit správné pravopisy a také překlepy. Tato možnost vyžaduje další popisování úsilí než použití kontroly pravopisu.
* Vytvoří seznam frází se všemi variantami slova. V tomto řešení není nutné označovat variace slov v příkladu projevy. 

## <a name="publishing-page"></a>Stránka publikování
Stránka [publikování](luis-how-to-publish-app.md) má zaškrtávací políčko **Povolit kontrolu pravopisu Bingu** . Toto je usnadnění a vytvořit klíč a pochopit, jak se změní adresa URL koncového bodu. Budete stále muset použít parametry správný koncový bod aby měla kontrolu pravopisu pro každý utterance. 

> [!div class="nextstepaction"]
> [Další informace o příkladu projevy](luis-how-to-add-example-utterances.md)
