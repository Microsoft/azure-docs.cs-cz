---
title: Přidat API kontrola pravopisu Bingu v7 LUIS dotazy | Dokumentace Microsoftu
titleSuffix: Azure
description: Správné slova s překlepem v projevy tak, že přidáte API V7 kontrola pravopisu Bingu dotazy koncový bod služby LUIS.
services: cognitive-services
author: v-geberr
manager: kamran.iqbal
ms.service: cognitive-services
ms.component: language-understanding
ms.topic: article
ms.date: 02/27/2018
ms.author: v-geberr
ms.openlocfilehash: e7bf447dafecf090f610f670539ca4673827953a
ms.sourcegitcommit: 756f866be058a8223332d91c86139eb7edea80cc
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/02/2018
ms.locfileid: "37343974"
---
# <a name="correct-misspelled-words-with-bing-spell-check"></a>Správné chybně napsaná slova s kontrolu pravopisu Bingu

Můžete integrovat aplikace LUIS s využitím [API V7 kontrola pravopisu Bingu](https://azure.microsoft.com/services/cognitive-services/spell-check/) k opravovat slova s překlepem v projevy před předpoví LUIS skóre a entity utterance. 

## <a name="create-first-key-for-bing-spell-check-v7"></a>Vytvoření první klíč pro V7 kontrola pravopisu Bingu
Vaše [první klíč API kontrola pravopisu Bingu v7](https://azure.microsoft.com/try/cognitive-services/?api=spellcheck-api) je zdarma. 

![Vytvořit bezplatný klíč](./media/luis-tutorial-bing-spellcheck/free-key.png)

< s názvem "Vytvoření subscription-key" ></a>
## <a name="create-endpoint-key"></a>Vytvoření klíče koncového bodu
Je-li klíč volný vypršela platnost, vytvořte klíč koncového bodu.

1. Přihlaste se k portálu [Azure Portal](https://portal.azure.com). 

2. Vyberte **vytvořit prostředek** v levém horním rohu.

3. Do vyhledávacího pole zadejte `Bing Spell Check API V7`.

    ![Zkontrolujte vyhledávání pro kontrolu pravopisu Bingu, rozhraní API v7 nabízí](./media/luis-tutorial-bing-spellcheck/portal-search.png)

4. Vyberte službu. 

5. Panelu informací se zobrazí napravo obsahující informace, včetně právní upozornění. Vyberte **vytvořit** zahájíte proces vytváření předplatného. 

6. V panelu Další zadejte nastavení služby. Počkejte na dokončení procesu vytváření služby.

    ![Zadejte nastavení služby](./media/luis-tutorial-bing-spellcheck/subscription-settings.png)

7. Vyberte **všechny prostředky** pod **Oblíbené** title v navigačním panelu na levé straně.

8. Vyberte novou službu. Jejím typem je **služeb Cognitive Services** a umístění je **globální**. 

9. Na hlavním panelu, vyberte **klíče** zobrazíte nové klíče.

    ![Získejte klíče](./media/luis-tutorial-bing-spellcheck/grab-keys.png)

10. První klíč si zkopírujte. Potřebujete jenom jeden ze dvou klíčů. 

## <a name="using-the-key-in-luis-test-panel"></a>V panelu testu LUIS pomocí klíče
Existují dvě místa v LUIS používat klíč. První je v [test panelu](interactive-test.md#view-bing-spell-check-corrections-in-test-panel). Klíč se neuloží do LUIS, ale místo toho je proměnná relace. Je potřeba nastavit klíč pokaždé, když chcete, aby panelu testu použít službu API kontrola pravopisu Bingu v7 utterance. Zobrazit [pokyny](interactive-test.md#view-bing-spell-check-corrections-in-test-panel) v panelu testu pro nastavení klíče.

## <a name="adding-the-key-to-the-endpoint-url"></a>Přidávání klíče do adresu URL koncového bodu
Dotaz koncový bod, musí klíč předávaný do parametrů řetězce dotazu pro každý dotaz, že který chcete použít opravy pravopisu. Můžete mít chatovací robot, který volá LUIS nebo koncový bod rozhraní API LUIS může volat přímo. Bez ohledu na to, jak se nazývá koncový bod musí obsahovat každého volání požadované informace pro opravy pravopisu fungovala správně.

Koncový bod adresy URL obsahuje několik hodnot, které je potřeba předávat správně. Klíč API kontrola pravopisu Bingu v7 je jenom další jeden z nich. Je nutné nastavit **kontrola pravopisu** parametr na hodnotu true a je nutné nastavit hodnotu **Bingu – pravopisu – kontrola subscription-key** hodnotě klíče:

https://{region}.API.cognitive.microsoft.com/luis/v2.0/Apps/{appID}?Subscription-Key={luisKey}&spellCheck=**true**& Bingu – pravopisu – kontrola subscription-key =**{bingKey}**& Podrobné = true & timezoneOffset = 0 & q = {utterance}

## <a name="send-misspelled-utterance-to-luis"></a>Chybně napsaná utterance posílala LUIS
1. Ve webovém prohlížeči, zkopírujte předchozí řetězec a nahraďte `region`, `appId`, `luisKey`, a `bingKey` vlastními hodnotami. Nezapomeňte použít oblast koncový bod, pokud se liší od vašeho publikování [oblasti](luis-reference-regions.md).

2. Přidání chybně utterance jako například "jak daleko je mountainn?". V angličtině `mountain`, s jednou `n`, je správně. 

3. Vyberte zadejte odeslat dotaz do služby LUIS.

4. Služba LUIS jako odpověď vrátí výsledek JSON pro `How far is the mountain?`. Pokud API kontrola pravopisu Bingu v7 zjistí chyba, `query` pole v odpovědi JSON aplikace LUIS obsahuje původní dotaz a `alteredQuery` pole obsahuje opravený dotaz odeslaný do služby LUIS.

```
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
Pokud už nechcete používat službu API kontrola pravopisu Bingu v7, lze označit projevy, které mají pravopisné chyby hned, takže správné pravopisu, jakož i překlepy další LUIS. Tato možnost vyžaduje další popisování úsilí než použití kontroly pravopisu.

## <a name="publishing-page"></a>Stránka publikování
[Publikování](luis-how-to-publish-app.md) stránka má **kontrolu pravopisu Bingu povolit** zaškrtávací políčko. Toto je usnadnění a vytvořit klíč a pochopit, jak se změní adresa URL koncového bodu. Budete stále muset použít parametry správný koncový bod aby měla kontrolu pravopisu pro každý utterance. 

> [!div class="nextstepaction"]
> [Další informace o příklad projevy](luis-how-to-add-example-utterances.md)
