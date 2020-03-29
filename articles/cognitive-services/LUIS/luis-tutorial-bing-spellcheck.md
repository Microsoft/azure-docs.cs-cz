---
title: Oprava chybně napsaných slov – LUIS
titleSuffix: Azure Cognitive Services
description: Opravte chybně napsaná slova v projevech přidáním rozhraní API pro kontrolu pravopisu Bingu V7 do dotazů koncového bodu LUIS.
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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "74225448"
---
# <a name="correct-misspelled-words-with-bing-spell-check"></a>Oprava chybně napsaných slov pomocí kontroly pravopisu Bingu

Aplikaci LUIS můžete integrovat s [rozhraním API pro kontrolu pravopisu Bingu V7](https://azure.microsoft.com/services/cognitive-services/spell-check/) a opravit chybně napsaná slova v projevech dříve, než LUIS předpovídá skóre a entity utterance. 

[!INCLUDE [Not supported in V3 API prediction endpoint](./includes/v2-support-only.md)]


## <a name="create-first-key-for-bing-spell-check-v7"></a>Vytvořit první klíč pro kontrolu pravopisu Bingu V7

Váš [první klíč Rozhraní API pro kontrolu pravopisu Bingu v7](https://azure.microsoft.com/try/cognitive-services/?api=spellcheck-api) je zdarma. 

![Vytvořit volný klíč](./media/luis-tutorial-bing-spellcheck/free-key.png)

<a name="create-subscription-key"></a>

## <a name="create-endpoint-key"></a>Vytvořit klíč koncového bodu
Pokud platnost volného klíče vypršela, vytvořte klíč koncového bodu.

1. Přihlaste se k [portálu Azure](https://portal.azure.com). 

2. V levém horním rohu vyberte **Vytvořit zdroj.**

3. Do vyhledávacího pole zadejte `Bing Spell Check API V7`.

    ![Hledání rozhraní API pro kontrolu pravopisu Bingu V7](./media/luis-tutorial-bing-spellcheck/portal-search.png)

4. Vyberte službu. 

5. Vpravo se zobrazí informační panel obsahující informace včetně právního upozornění. Výběrem **možnosti Vytvořit** zahájíte proces vytváření předplatného. 

6. Na dalším panelu zadejte nastavení služby. Počkejte na dokončení procesu vytváření služby.

    ![Zadat nastavení služby](./media/luis-tutorial-bing-spellcheck/subscription-settings.png)

7. V nabídce **Oblíbené položky** vyberte všechny **zdroje** v navigaci na levé straně.

8. Vyberte novou službu. Jeho typ je **Cognitive Services** a umístění je **globální**. 

9. V hlavním panelu vyberte **Klávesy,** abyste viděli nové klávesy.

    ![Uchopit klíče](./media/luis-tutorial-bing-spellcheck/grab-keys.png)

10. Zkopírujte první klíč. Potřebujete pouze jeden ze dvou klíčů. 

<!--
## Using the key in LUIS test panel
There are two places in LUIS to use the key. The first is in the [test panel](luis-interactive-test.md#view-bing-spell-check-corrections-in-test-panel). The key isn't saved into LUIS but instead is a session variable. You need to set the key every time you want the test panel to apply the Bing Spell Check API v7 service to the utterance. See [instructions](luis-interactive-test.md#view-bing-spell-check-corrections-in-test-panel) in the test panel for setting the key.
-->
## <a name="adding-the-key-to-the-endpoint-url"></a>Přidání klíče do adresy URL koncového bodu
Dotaz koncového bodu potřebuje klíč předaný v parametrech řetězce dotazu pro každý dotaz, který chcete použít opravu pravopisu. Můžete mít chatbot, který volá LUIS nebo můžete volat rozhraní API koncového bodu LUIS přímo. Bez ohledu na to, jak je koncový bod volán, musí každé volání obsahovat požadované informace pro správné fungování oprav pravopisu.

Adresa URL koncového bodu má několik hodnot, které je třeba předat správně. Bing Kontrola pravopisu API v7 klíč je jen další z nich. Je nutné nastavit parametr **spellCheck** na hodnotu true a je nutné nastavit hodnotu **bing-spell-check-subscription-key** na hodnotu klíče:

`https://{region}.api.cognitive.microsoft.com/luis/v2.0/apps/{appID}?subscription-key={luisKey}&spellCheck=**true**&bing-spell-check-subscription-key=**{bingKey}**&verbose=true&timezoneOffset=0&q={utterance}`

## <a name="send-misspelled-utterance-to-luis"></a>Odeslat chybně napsanou promluvu službě LUIS
1. Ve webovém prohlížeči zkopírujte předchozí `region`řetězec `appId` `luisKey`a `bingKey` nahraďte , , a vlastními hodnotami. Ujistěte se, že používáte oblast koncového bodu, pokud se liší od [oblasti](luis-reference-regions.md)publikování .

2. Přidejte chybně napsanou promluvu, například "Jak daleko je hora?". V angličtině, `mountain`, `n`s jedním , je správný pravopis. 

3. Výběrem možnosti Enter odešlete dotaz službě LUIS.

4. Služba LUIS odpoví s výsledkem `How far is the mountain?`JSON pro . Pokud rozhraní API kontroly pravopisu Bingu `query` v7 zjistí chybně, pole v odpovědi JSON `alteredQuery` aplikace LUIS obsahuje původní dotaz a pole obsahuje opravený dotaz odeslaný službě LUIS.

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

Pokud nechcete používat službu Rozhraní API pro kontrolu pravopisu Bingu v7, je třeba přidat správný a nesprávný pravopis. 

Dvě řešení jsou:

* Popisek příklad projevy, které mají všechny různé pravopisy tak, aby LUIS můžete naučit správné pravopisu, stejně jako překlepy. Tato možnost vyžaduje více úsilí o označování než použití kontroly pravopisu.
* Vytvořte seznam frází se všemi variantami slova. S tímto řešením není nutné označit slovo varianty v příkladu projevy. 

## <a name="publishing-page"></a>Stránka publikování
Stránka [publikování](luis-how-to-publish-app.md) má **zaškrtávací** políčko Povolit kontrolu pravopisu Bingu. Toto je pohodlí k vytvoření klíče a pochopit, jak se změní adresa URL koncového bodu. Stále musíte použít správné parametry koncového bodu, aby byl pravopis opraven pro každou utterance. 

> [!div class="nextstepaction"]
> [Další informace o příkladech promluv](luis-how-to-add-example-utterances.md)
