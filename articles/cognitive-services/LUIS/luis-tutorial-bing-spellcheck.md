---
title: Přidat API kontrola pravopisu Bing v7 LEOŠ dotazy | Microsoft Docs
titleSuffix: Azure
description: Správné překlepu slova v utterances přidáním API V7 kontrola pravopisu Bing LEOŠ koncový bod dotazy.
services: cognitive-services
author: v-geberr
manager: kamran.iqbal
ms.service: cognitive-services
ms.component: language-understanding
ms.topic: article
ms.date: 02/27/2018
ms.author: v-geberr
ms.openlocfilehash: 340fb34c234a12f93fcfc3182ac3fd44fce324fe
ms.sourcegitcommit: b7290b2cede85db346bb88fe3a5b3b316620808d
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/05/2018
ms.locfileid: "35343928"
---
# <a name="correct-misspelled-words-with-bing-spell-check"></a>Správné překlepu slova s Bing kontrola pravopisu

Můžete provést integraci aplikace LEOŠ s [API V7 kontrola pravopisu Bing](https://azure.microsoft.com/services/cognitive-services/spell-check/) opravit slovo obsahující chyby v utterances před LEOŠ předpovídá skóre a entity utterance. 

## <a name="create-first-key-for-bing-spell-check-v7"></a>Vytvoření první klíč pro V7 kontrola pravopisu Bing
Vaše [první v7 klíč API kontrola pravopisu Bing](https://azure.microsoft.com/try/cognitive-services/?api=spellcheck-api) je zdarma. 

![Vytvoření volné klíče](./media/luis-tutorial-bing-spellcheck/free-key.png)

## <a name="create-subscription-key"></a>Vytvořte klíč předplatného
Pokud platnost vašeho volné klíče, vytvořte klíč předplatného.

1. Přihlaste se k portálu [Azure Portal](https://portal.azure.com). 

2. Vyberte **vytvořit prostředek** v levém horním rohu.

3. Do vyhledávacího pole zadejte `Bing Spell Check API V7`.

    ![Vyhledejte Bing pravopisu zkontrolujte V7 rozhraní API](./media/luis-tutorial-bing-spellcheck/portal-search.png)

4. Vyberte službu. 

5. Panelu informací se zobrazí vpravo obsahující informace, včetně právní upozornění. Vyberte **vytvořit** zahájíte proces vytváření předplatného. 

6. V panelu Další zadejte nastavení služeb. Počkejte na dokončení procesu vytváření služby.

    ![Zadejte nastavení služby](./media/luis-tutorial-bing-spellcheck/subscription-settings.png)

7. Vyberte **všechny prostředky** pod **Oblíbené** title na levé straně.

8. Vyberte novou službu. Je její typ **kognitivní služby** a umístění je **globální**. 

9. Na hlavním panelu vyberte **klíče** zobrazíte nové klíče.

    ![Získat klíče](./media/luis-tutorial-bing-spellcheck/grab-keys.png)

10. Zkopírujte první klíč. Potřebujete jenom jednu z dva klíče. 

## <a name="using-the-key-in-luis-test-panel"></a>Pomocí klíče panelu LEOŠ testu
Existují dvě místech LEOŠ používat klíč. První je v [testovací panely](train-test.md#view-bing-spell-check-corrections-in-test-panel). Klíč se neuloží do LEOŠ, ale místo toho je proměnné relace. Je nutné nastavit klíč pokaždé, když chcete panelu testu pro použití služby API kontrola pravopisu Bing v7 utterance. V tématu [pokyny](train-test.md#view-bing-spell-check-corrections-in-test-panel) panelu testu pro nastavení klíče.

## <a name="adding-the-key-to-the-endpoint-url"></a>Přidání klíče do adresu URL koncového bodu
Dotaz koncový bod potřebuje klíč předávaný do parametrů řetězce dotazu pro každý dotaz, že který chcete použít oprava pravopis. Můžete mít chatbot, který volá LEOŠ nebo LEOŠ koncový bod rozhraní API může volat přímo. Bez ohledu na to, jak se nazývá koncový bod musí každé volání zahrnout požadované informace z kontroly pravopisu správně fungovat.

Koncový bod adresy URL obsahuje několik hodnot, které musí být předán správně. Klíč API kontrola pravopisu Bing v7 je jenom další jeden z nich. Je nutné nastavit **kontrola pravopisu** parametr na hodnotu true a je nutné nastavit hodnotu **bing pravopisu – kontrola subscription-key** k hodnotě klíče:

https://{region}.API.cognitive.microsoft.com/luis/v2.0/Apps/{appID}?Subscription-Key={luisKey}&spellCheck=**true**& bing pravopisu – kontrola subscription-key =**{bingKey}**& Podrobné = true & timezoneOffset = 0 & q = {utterance}

## <a name="send-misspelled-utterance-to-luis"></a>Poslat LEOŠ překlepu utterance
1. Ve webovém prohlížeči, zkopírujte předchozí řetězec a nahraďte `region`, `appId`, `luisKey`, a `bingKey` vlastními hodnotami. Nezapomeňte použít oblast, koncový bod, pokud se liší od vašeho publikování [oblast](luis-reference-regions.md).

2. Přidejte překlepu utterance jako "jak daleko je mountainn?". V angličtině `mountain`, s jedním `n`, je správně. 

3. Vyberte zadejte odeslat dotaz na LEOŠ.

4. LEOŠ odpoví výsledku JSON pro `How far is the mountain?`. Pokud API kontrola pravopisu Bing v7 zjistí chybné, `query` pole v aplikaci LEOŠ JSON odpovědi obsahuje původní dotaz a `alteredQuery` pole obsahuje opravené dotaz posílá LEOŠ.

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

## <a name="ignore-spelling-mistakes"></a>Ignorovat pravopisné chyby
Pokud nechcete používat službu API kontrola pravopisu Bing v7, můžete označit utterances, které mají pravopisné chyby, aby LEOŠ poznala správné pravopis a také překlepům. Tato možnost vyžaduje další označování úsilí než použití nástroj pro kontrolu pravopisu.

## <a name="publishing-page"></a>Stránky publikování
[Publikování](publishapp.md) stránce **kontrola pravopisu povolit Bing** zaškrtávací políčko. Toto je pro vaše pohodlí vytvořte klíč a pochopit, jak se změní adresa URL koncového bodu. Stále je nutné použít správný koncový bod parametry aby bylo možné používat pravopis opravě pro každý utterance. 

> [!div class="nextstepaction"]
> [Další informace o příklad utterances](luis-how-to-add-example-utterances.md)
