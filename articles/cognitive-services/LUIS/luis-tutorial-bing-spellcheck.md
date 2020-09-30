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
ms.date: 11/19/2019
ms.openlocfilehash: 120852c3e66594b1cf5cd28bb52ce06634211060
ms.sourcegitcommit: f5580dd1d1799de15646e195f0120b9f9255617b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/29/2020
ms.locfileid: "91541084"
---
# <a name="correct-misspelled-words-with-bing-spell-check"></a>Oprava nesprávně napsaných slov pomocí Kontrola pravopisu Bingu

Aplikaci LUIS můžete integrovat s [rozhraní API Bingu pro kontrolu pravopisu v7](https://azure.microsoft.com/services/cognitive-services/spell-check/) , aby se opravila nesprávně napsaná slova v projevy předtím, než Luis předpovídá skóre a entity utterance.

[!INCLUDE [Not supported in V3 API prediction endpoint](./includes/v2-support-only.md)]

## <a name="create-endpoint-key"></a>Vytvořit klíč koncového bodu

Pokud chcete vytvořit prostředek Kontrola pravopisu Bingu v Azure Portal, postupujte podle těchto pokynů:

1. Přihlaste se k [Azure Portal](https://portal.azure.com).

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

<!--
## Using the key in LUIS test panel
There are two places in LUIS to use the key. The first is in the [test panel](luis-interactive-test.md#view-bing-spell-check-corrections-in-test-panel). The key isn't saved into LUIS but instead is a session variable. You need to set the key every time you want the test panel to apply the Bing Spell Check API v7 service to the utterance. See [instructions](luis-interactive-test.md#view-bing-spell-check-corrections-in-test-panel) in the test panel for setting the key.
-->
## <a name="adding-the-key-to-the-endpoint-url"></a>Přidání klíče k adrese URL koncového bodu
Dotaz koncového bodu potřebuje klíč předaný v parametrech řetězce dotazu pro každý dotaz, který chcete použít pro opravu pravopisu. Můžete mít chatovací robot, který volá LUIS, nebo můžete volat rozhraní API koncového bodu LUIS přímo. Bez ohledu na to, jak je koncový bod volán, každé a každé volání musí obsahovat požadované informace pro správné fungování oprav pravopisu.

Adresa URL koncového bodu má několik hodnot, které je potřeba předávat správně. Klíč rozhraní API Bingu pro kontrolu pravopisu v7 je právě jiný. Je nutné nastavit parametr **kontroly pravopisu** na hodnotu true a je nutné nastavit hodnotu klíče **Bing-check-Subscription-Key** na hodnotu Key:

`https://{region}.api.cognitive.microsoft.com/luis/v2.0/apps/{appID}?subscription-key={luisKey}&spellCheck=true&bing-spell-check-subscription-key={bingKey}&verbose=true&timezoneOffset=0&q={utterance}`

## <a name="send-misspelled-utterance-to-luis"></a>Odeslání nesprávně napsaného utteranceu do LUIS
1. Ve webovém prohlížeči zkopírujte předchozí řetězec a nahraďte `region` , `appId` , a `luisKey` `bingKey` vlastními hodnotami. Pokud se liší od [oblasti](luis-reference-regions.md)publikování, nezapomeňte použít oblast koncového bodu.

2. Přidejte nesprávně napsaný utterance, například "jak daleko je mountainn?". V angličtině `mountain` je u jedné `n` správné pravopisné zadání.

3. Vyberte ENTER a odešlete dotaz do LUIS.

4. LUIS odpoví s výsledkem JSON pro `How far is the mountain?` . Pokud rozhraní API Bingu pro kontrolu pravopisu v7 detekuje chybu, `query` pole v odpovědi JSON aplikace Luis obsahuje původní dotaz a `alteredQuery` pole obsahuje opravený dotaz odeslaný do Luis.

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
