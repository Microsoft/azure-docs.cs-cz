---
title: Začínáme s rozhraním API pro rozpoznávání Zpracování řeči Bingu v JavaScriptu | Microsoft Docs
titlesuffix: Azure Cognitive Services
description: Rozhraní API pro rozpoznávání Zpracování řeči Bingu v Cognitive Services můžete použít k vývoji aplikací, které průběžně převádějí mluvený zvuk na text.
services: cognitive-services
author: nitinme
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-speech
ms.topic: article
ms.date: 09/18/2018
ms.author: nitinme
ROBOTS: NOINDEX,NOFOLLOW
ms.openlocfilehash: eef1a4885b77ae94f11d3d5bda5ded9b70ed63a4
ms.sourcegitcommit: fbea2708aab06c19524583f7fbdf35e73274f657
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/13/2019
ms.locfileid: "70965811"
---
# <a name="get-started-with-the-speech-recognition-api-in-javascript"></a>Začínáme s rozhraním API pro rozpoznávání řeči v JavaScriptu

[!INCLUDE [Deprecation note](../../../../includes/cognitive-services-bing-speech-api-deprecation-note.md)]

Můžete vyvíjet aplikace, které převádějí mluvený zvuk na text pomocí rozhraní API pro rozpoznávání řeči. Klientská knihovna JavaScript používá [protokol WebSocket služby Speech](../API-Reference-REST/websocketprotocol.md), který umožňuje hovořit a přijímat přepisu text současně. Tento článek vám pomůže začít s rozhraním API pro rozpoznávání řeči v JavaScriptu.

## <a name="prerequisites"></a>Požadavky

### <a name="subscribe-to-the-speech-recognition-api-and-get-a-free-trial-subscription-key"></a>Přihlaste se k odběru rozhraní API pro rozpoznávání řeči a získejte bezplatný zkušební klíč předplatného.

Rozhraní API pro rozpoznávání řeči je součástí Cognitive Services. Můžete získat bezplatný zkušební odběr klíčů na stránce [Cognitive Services předplatné](https://azure.microsoft.com/try/cognitive-services/) . Po výběru rozhraní API pro rozpoznávání řeči vyberte **získat klíč rozhraní API** a získejte klíč. Vrátí primární a sekundární klíč. Oba klíče jsou vázané na stejnou kvótu, takže můžete použít kteroukoli z těchto klíčů.

> [!IMPORTANT]
> Získejte klíč předplatného. Předtím, než budete moci použít klientské knihovny řeči, je nutné mít [klíč předplatného](https://azure.microsoft.com/try/cognitive-services/).

## <a name="get-started"></a>Začínáme

V této části Vás provedeme kroky potřebnými k načtení ukázkové stránky HTML. Ukázka se nachází v našem [úložišti GitHub](https://github.com/Azure-Samples/SpeechToText-WebSockets-Javascript). Ukázku můžete **otevřít přímo** z úložiště nebo **ukázku otevřít z místní kopie** úložiště.

> [!NOTE]
> Některé prohlížeče blokují přístup k mikrofonu na nezabezpečeném zdroji. Proto se doporučuje hostovat ' Sample '/' aplikaci ' na https, aby fungoval na všech podporovaných prohlížečích.

### <a name="open-the-sample-directly"></a>Otevřít ukázku přímo

Získejte klíč předplatného, jak je popsáno výše. Otevřete [odkaz na ukázku](https://htmlpreview.github.io/?https://github.com/Azure-Samples/SpeechToText-WebSockets-Javascript/blob/preview/samples/browser/Sample.html). Tím se načte stránka do výchozího prohlížeče (vykreslený pomocí [htmlPreview](https://github.com/htmlpreview/htmlpreview.github.com)).

### <a name="open-the-sample-from-a-local-copy"></a>Otevření ukázky z místní kopie

Pokud chcete ukázku vyzkoušet lokálně, naklonujte toto úložiště:

```
git clone https://github.com/Azure-Samples/SpeechToText-WebSockets-Javascript
```

Zkompilujte zdroje TypeScript a zabalí je do jednoho souboru JavaScriptu ([npm](https://www.npmjs.com/) musí být na vašem počítači nainstalované). Přejděte do kořenového adresáře klonovaného úložiště a spusťte příkazy:

```
cd SpeechToText-WebSockets-Javascript && npm run bundle
```

Otevřete `samples\browser\Sample.html` v oblíbeném prohlížeči.

## <a name="next-steps"></a>Další postup

Další informace o tom, jak sadu SDK zahrnout do vlastní webové stránky, najdete [tady](https://github.com/Azure-Samples/SpeechToText-WebSockets-Javascript).

## <a name="remarks"></a>Poznámky

- Rozhraní API pro rozpoznávání řeči podporuje tři [režimy rozpoznávání](../concepts.md#recognition-modes). Režim můžete přepnout aktualizací funkce **Setup ()** , která se nachází v ukázkovém souboru. html. Ukázka nastaví režim na `Interactive` výchozí hodnotu. Chcete-li změnit režim, aktualizujte `SR.RecognitionMode.Interactive` parametr na jiný režim. Například změňte parametr na `SR.RecognitionMode.Conversation`.
- Úplný seznam podporovaných jazyků najdete v části [podporované jazyky](../API-Reference-REST/supportedlanguages.md).

## <a name="related-topics"></a>Související témata

- [Ukázkové úložiště rozhraní API pro rozpoznávání řeči JavaScriptu](https://github.com/Azure-Samples/SpeechToText-WebSockets-Javascript)
- [Začínáme s REST API](GetStartedREST.md)
