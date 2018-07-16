---
title: Začínáme s API pro rozpoznávání řeči Microsoft v jazyce JavaScript | Dokumentace Microsoftu
description: Použití rozhraní API pro rozpoznávání řeči Microsoft ve službě Cognitive Services k vývoji aplikací, které plynule převést mluvené slovo na text.
services: cognitive-services
author: zhouwangzw
manager: wolfma
ms.service: cognitive-services
ms.component: bing-speech
ms.topic: article
ms.date: 12/21/2017
ms.author: zhouwang
ms.openlocfilehash: 04332c453d22122e65a758a65b09e17300e07f02
ms.sourcegitcommit: 7208bfe8878f83d5ec92e54e2f1222ffd41bf931
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/14/2018
ms.locfileid: "39040534"
---
# <a name="get-started-with-the-speech-recognition-api-in-javascript"></a>Začínáme s rozhraním API pro rozpoznávání řeči v jazyce JavaScript

Můžete vyvíjet aplikace, které převést mluvené slovo na text pomocí rozhraní API pro rozpoznávání řeči. Klientská knihovna pro JavaScript používá [protokol Speech Service WebSocket](../API-Reference-REST/websocketprotocol.md), která umožňuje komunikaci a přijímat textové přepisována současně. Tento článek pomůže vám umožní začít s rozhraním API pro rozpoznávání řeči v jazyce JavaScript.

## <a name="prerequisites"></a>Požadavky

### <a name="subscribe-to-the-speech-recognition-api-and-get-a-free-trial-subscription-key"></a>Přihlaste se k rozhraní API pro rozpoznávání řeči odběru a získání klíče bezplatné předplatné zkušební verze

Rozhraní Speech API je součástí služeb Cognitive Services. Můžete získat bezplatné předplatné zkušební verze klíče z [předplatné služeb Cognitive Services](https://azure.microsoft.com/try/cognitive-services/) stránky. Po výběru rozhraní API pro rozpoznávání řeči, vyberte **získat klíč rozhraní API** získat klíč. Vrátí primární a sekundární klíč. Oba klíče jsou svázány se stejnou kvótu, abyste mohli používat ani jeden klíč.

> [!IMPORTANT]
> Získáte klíč předplatného. Než budete moct použít klientské knihovny pro zpracování řeči, musíte mít [klíč předplatného](https://azure.microsoft.com/try/cognitive-services/).

## <a name="get-started"></a>Začínáme

V této části jsme vás provede kroky potřebné ke načíst ukázkovou stránku HTML. Tato ukázka se nachází v našich [úložiště github](https://github.com/Azure-Samples/SpeechToText-WebSockets-Javascript). Je možné **otevřete ukázku přímo** z úložiště, nebo **otevřete ukázku z místní kopie** úložiště. 

> [!NOTE]
> Některé prohlížeče blokovat přístup k mikrofonu na zrušení bezpečný původ. Proto se doporučuje pro hostování "ukázkový" nebo "aplikace" na https její práce na všech podporovaných prohlížečů. 

### <a name="open-the-sample-directly"></a>Otevřete ukázku přímo

Získejte klíč předplatného, jak je popsáno výše. Otevřete [odkaz na ukázku](https://htmlpreview.github.io/?https://github.com/Azure-Samples/SpeechToText-WebSockets-Javascript/blob/preview/samples/browser/Sample.html). Tím se načtou do svého výchozího prohlížeče na stránce (vykreslen pomocí [htmlPreview](https://github.com/htmlpreview/htmlpreview.github.com)).

### <a name="open-the-sample-from-a-local-copy"></a>Otevřete ukázku z místní kopie

Pokud chcete vyzkoušet ukázky místně, klonujte toto úložiště:

```
git clone https://github.com/Azure-Samples/SpeechToText-WebSockets-Javascript
```

kompilace zdrojů TypeScript a seskupí se do jednoho souboru jazyka JavaScript ([npm](https://www.npmjs.com/) musí být nainstalovaný na svém počítači). Přejděte do kořenového adresáře naklonovaného úložiště a spusťte příkazy:

```
cd SpeechToText-WebSockets-Javascript && npm run bundle
```

Otevřít `samples\browser\Sample.html` v oblíbeném prohlížeči.

## <a name="next-steps"></a>Další postup

Další informace o tom, jak zahrnout sady SDK do vaší vlastní webová stránka je k dispozici [tady](https://github.com/Azure-Samples/SpeechToText-WebSockets-Javascript).

## <a name="remarks"></a>Poznámky

- Rozhraní API pro rozpoznávání řeči podporuje tři [rozpoznávání režimy](../concepts.md#recognition-modes). Můžete přepnout režim aktualizací **Setup()** funkce v souboru Sample.html nalezen. Ukázka nastaví režim na `Interactive` ve výchozím nastavení. Chcete-li změnit režim, aktualizujte parametr `SR.RecognitionMode.Interactive` do jiného režimu. Například, změňte parametr `SR.RecognitionMode.Conversation`.
- Úplný seznam podporovaných jazyků najdete v části [podporované jazyky](../API-Reference-REST/supportedlanguages.md).

## <a name="related-topics"></a>Související témata

- [Úložiště ukázkové rozhraní API pro rozpoznávání řeči jazyka JavaScript](https://github.com/Azure-Samples/SpeechToText-WebSockets-Javascript)
- [Začínáme s rozhraním REST API](GetStartedREST.md)
