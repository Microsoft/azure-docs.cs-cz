---
title: Začínáme s rozhraním API rozpoznávání řeči Microsoft v jazyce JavaScript | Microsoft Docs
description: Použití rozhraní API pro rozpoznávání řeči Microsoft v kognitivní služby k vývoji aplikací, které průběžně mluvené zvuk převedeno na text.
services: cognitive-services
author: zhouwangzw
manager: wolfma
ms.service: cognitive-services
ms.component: bing-speech
ms.topic: article
ms.date: 12/21/2017
ms.author: zhouwang
ms.openlocfilehash: 56c41fd7f6a00d80bc6bccd61894654e057e926e
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/23/2018
ms.locfileid: "35342633"
---
# <a name="get-started-with-the-speech-recognition-api-in-javascript"></a>Začínáme s rozhraním API pro rozpoznávání řeči v jazyce JavaScript

Můžete vyvíjet aplikace, které převést mluvené zvuk pomocí rozhraní API pro rozpoznávání řeči na text. Knihovny JavaScript klienta používá [protokolu WebSocket služby řeči](../API-Reference-REST/websocketprotocol.md), která umožňuje komunikovat a přijímat textové přepisována současně. Tento článek pomůže vám umožní začít s rozhraním API pro rozpoznávání řeči v jazyce JavaScript.

## <a name="prerequisites"></a>Požadavky

### <a name="subscribe-to-the-speech-recognition-api-and-get-a-free-trial-subscription-key"></a>Přihlášení k odběru do rozhraní API pro rozpoznávání řeči a získat klíč bezplatné předplatné zkušební verze

Rozhraní API pro rozpoznávání řeči je součástí kognitivní služby. Můžete získat zkušební předplatné klíče z [kognitivní služby předplatného](https://azure.microsoft.com/try/cognitive-services/) stránky. Po výběru rozhraní API pro rozpoznávání řeči, vyberte **získat klíč rozhraní API** k získání klíče. Vrátí primární a sekundární klíč. Oba klíče jsou svázané s stejné kvótu, takže můžete použít buď klíč.

> [!IMPORTANT]
> Získáte klíč předplatného. Než budete moci použít řeči klientské knihovny, musíte mít [klíč předplatného](https://azure.microsoft.com/try/cognitive-services/).

## <a name="get-started"></a>Začínáme

V této části jsme se vás provede procesem nezbytných kroků k načtení stránky HTML ukázka. Tato ukázka se nachází v našem [úložiště github](https://github.com/Azure-Samples/SpeechToText-WebSockets-Javascript). Můžete **otevřete ukázku přímo** z úložiště, nebo **otevřete ukázku z místní kopie** úložiště. 

> [!NOTE]
> Některé prohlížeče blokovat přístup mikrofon na zrušení bezpečný původ. Ano, se doporučuje pro hostování "vzorkem" nebo "vaší aplikace' protokolu HTTPS se dá stáhnout pracující na všech podporovaných prohlížečích. 

### <a name="open-the-sample-directly"></a>Otevřete ukázku přímo

Získejte klíč předplatného, jak je popsáno výše. Otevřete [odkaz na ukázku](https://htmlpreview.github.io/?https://github.com/Azure-Samples/SpeechToText-WebSockets-Javascript/blob/preview/samples/browser/Sample.html). To bude načtení stránky do výchozí prohlížeč (vykreslen pomocí [htmlPreview](https://github.com/htmlpreview/htmlpreview.github.com)).

### <a name="open-the-sample-from-a-local-copy"></a>Otevřete ukázku z místní kopie

Pokud chcete vyzkoušet ukázkový místně, klonování toto úložiště:

```
git clone https://github.com/Azure-Samples/SpeechToText-WebSockets-Javascript
```

kompilaci TypeScript zdroje a sady nebo browserfy je do jednoho souboru JavaScript ([npm](https://www.npmjs.com/) musí být nainstalovaný na počítači). Přejděte do kořenové klonovaný úložiště a spusťte příkazy:

```
cd SpeechToText-WebSockets-Javascript && npm run bundle
```

Otevřete `samples\browser\Sample.html` v oblíbeném prohlížeči.

## <a name="next-steps"></a>Další postup

Další informace o tom, aby používaly sadu SDK do vlastní webová stránka je k dispozici [zde](https://github.com/Azure-Samples/SpeechToText-WebSockets-Javascript).

## <a name="remarks"></a>Poznámky

- Rozhraní API pro rozpoznávání řeči podporuje tři [režimy rozpoznávání](../concepts.md#recognition-modes). Můžete přepnout režim aktualizací **Setup()** funkce nacházejí v souboru Sample.html. Ukázka nastaví režim `Interactive` ve výchozím nastavení. Ke změně režimu, aktualizovat parametr `SR.RecognitionMode.Interactive` na jiný režim. Můžete například změnit parametru `SR.RecognitionMode.Conversation`.
- Úplný seznam podporovaných jazyků najdete v tématu [podporované jazyky](../API-Reference-REST/supportedlanguages.md).

## <a name="related-topics"></a>Související témata

- [Ukázka úložiště API pro rozpoznávání řeči jazyka JavaScript](https://github.com/Azure-Samples/SpeechToText-WebSockets-Javascript)
- [Začínáme s rozhraní REST API](GetStartedREST.md)
