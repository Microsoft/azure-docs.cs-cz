---
title: Oprava nesprávně napsaných slov – LUIS
titleSuffix: Azure Cognitive Services
description: Opravte nesprávně napsaná slova v projevy přidáním rozhraní API Bingu pro kontrolu pravopisu v7 do dotazů na koncový bod LUIS.
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: conceptual
ms.date: 10/25/2019
ms.author: diberry
ms.openlocfilehash: 9c8babac8450bdfd170d3d18b338ba3d64383a67
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/04/2019
ms.locfileid: "73499025"
---
# <a name="correct-misspelled-words-with-bing-spell-check"></a>Oprava nesprávně napsaných slov pomocí Kontrola pravopisu Bingu

Aplikaci LUIS můžete integrovat s [rozhraní API Bingu pro kontrolu pravopisu v7](https://azure.microsoft.com/services/cognitive-services/spell-check/) , aby se opravila nesprávně napsaná slova v projevy předtím, než Luis předpovídá skóre a entity utterance. 

[!INCLUDE [Waiting for LUIS portal refresh](./includes/wait-v3-upgrade.md)]

[!INCLUDE [Not supported in V3 API prediction endpoint](./includes/v2-support-only.md)]


## <a name="create-first-key-for-bing-spell-check-v7"></a>Vytvořit první klíč pro Kontrola pravopisu Bingu v7
Váš [první rozhraní API Bingu pro kontrolu pravopisu klíč v7](https://azure.microsoft.com/try/cognitive-services/?api=spellcheck-api) je zdarma. 

![Vytvořit bezplatný klíč](./media/luis-tutorial-bing-spellcheck/free-key.png)

< název "Create-Subscription-Key" ></a>
## <a name="create-endpoint-key"></a>Vytvořit klíč koncového bodu
Pokud váš bezplatný klíč vypršel, vytvořte klíč koncového bodu.

1. Přihlaste se k webu [Azure Portal](https://portal.azure.com). 

2. V levém horním rohu vyberte **vytvořit prostředek** .

3. Do vyhledávacího pole zadejte `Bing Spell Check API V7`.

    ![Hledat rozhraní API Bingu pro kontrolu pravopisu v7](./media/luis-tutorial-bing-spellcheck/portal-search.png)

4. Vyberte službu. 

5. Informační panel se zobrazí vpravo obsahující informace, včetně právního oznámení. Výběrem **vytvořit** zahájíte proces vytváření předplatného. 

6. Na dalším panelu zadejte nastavení služby. Počkejte na dokončení procesu vytváření služby.

    ![Zadat nastavení služby](./media/luis-tutorial-bing-spellcheck/subscription-settings.png)

7. Vyberte **všechny prostředky** pod nadpisem **Oblíbené položky** na levé straně navigace.

8. Vyberte novou službu. Jeho typ je **Cognitive Services** a umístění je **globální**. 

9. Na hlavním panelu vyberte **klíče** a zobrazte si nové klíče.

    ![Přitáhněte klíče](./media/luis-tutorial-bing-spellcheck/grab-keys.png)

10. Zkopírujte první klíč. Potřebujete jenom jeden ze dvou klíčů. 

## <a name="using-the-key-in-luis-test-panel"></a>Použití klíče v LUIS test panel
Existují dvě místa v LUIS k použití klíče. První je na [panelu Test](luis-interactive-test.md#view-bing-spell-check-corrections-in-test-panel). Klíč se neuloží do LUIS, ale místo toho je proměnná relace. Je nutné nastavit klíč pokaždé, když chcete, aby na panelu Test byla použita služba rozhraní API Bingu pro kontrolu pravopisu v7 na utterance. Chcete-li nastavit klíč, přečtěte si [pokyny](luis-interactive-test.md#view-bing-spell-check-corrections-in-test-panel) na panelu Test.

## <a name="adding-the-key-to-the-endpoint-url"></a>Přidání klíče k adrese URL koncového bodu
Dotaz koncového bodu potřebuje klíč předaný v parametrech řetězce dotazu pro každý dotaz, který chcete použít pro opravu pravopisu. Můžete mít chatovací robot, který volá LUIS, nebo můžete volat rozhraní API koncového bodu LUIS přímo. Bez ohledu na to, jak je koncový bod volán, každé a každé volání musí obsahovat požadované informace pro správné fungování oprav pravopisu.

Adresa URL koncového bodu má několik hodnot, které je potřeba předávat správně. Klíč rozhraní API Bingu pro kontrolu pravopisu v7 je právě jiný. Je nutné nastavit parametr **kontroly pravopisu** na hodnotu true a je nutné nastavit hodnotu klíče **Bing-check-Subscription-Key** na hodnotu Key:

`https://{region}.api.cognitive.microsoft.com/luis/v2.0/apps/{appID}?subscription-key={luisKey}&spellCheck=**true**&bing-spell-check-subscription-key=**{bingKey}**&verbose=true&timezoneOffset=0&q={utterance}`

## <a name="send-misspelled-utterance-to-luis"></a>Odeslání nesprávně napsaného utteranceu do LUIS
1. Ve webovém prohlížeči zkopírujte předchozí řetězec a nahraďte `region`, `appId`, `luisKey`a `bingKey` vlastními hodnotami. Pokud se liší od [oblasti](luis-reference-regions.md)publikování, nezapomeňte použít oblast koncového bodu.

2. Přidejte nesprávně napsaný utterance, například "jak daleko je mountainn?". V angličtině, `mountain`s jedním `n`, je správný pravopis. 

3. Vyberte ENTER a odešlete dotaz do LUIS.

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

## <a name="ignore-spelling-mistakes"></a>Ignorovat pravopisné chyby

Pokud nechcete používat službu rozhraní API Bingu pro kontrolu pravopisu v7, je nutné přidat správné a nesprávné zadání pravopisu. 

Existují dvě řešení:

* Popisek – příklad projevy, který má všechny různé pravopisy, aby LUIS mohl zjistit správné pravopisy a také překlepy. Tato možnost vyžaduje větší úsilí při označování než použití kontroly pravopisu.
* Vytvoří seznam frází se všemi variantami slova. V tomto řešení není nutné označovat variace slov v příkladu projevy. 

## <a name="publishing-page"></a>Stránka publikování
Stránka [publikování](luis-how-to-publish-app.md) má zaškrtávací políčko **Povolit kontrolu pravopisu Bingu** . To je pohodlí pro vytvoření klíče a pochopení, jak se adresa URL koncového bodu mění. Aby bylo možné opravit pravopis každého utterance, je stále nutné použít správné parametry koncového bodu. 

> [!div class="nextstepaction"]
> [Další informace o příkladu projevy](luis-how-to-add-example-utterances.md)
