---
author: wolfma61
ms.service: cognitive-services
ms.topic: include
ms.date: 07/27/2018
ms.author: wolfma
ms.openlocfilehash: b46eba502462e0cdbcb222082c5c885f84a8d1e5
ms.sourcegitcommit: f6e2a03076679d53b550a24828141c4fb978dcf9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/27/2018
ms.locfileid: "43090677"
---
<!-- N.B. no header, no intents here, language-agnostic -->

Cognitive Services [sadou SDK pro řeč](~/articles/cognitive-services/speech-service/speech-sdk.md) poskytuje nejjednodušší způsob, jak používat **převod řeči na Text** ve vaší aplikaci se všemi funkcemi.

1. Vytvořte objekt pro vytváření řeči a zadejte klíč předplatného Speech service (nebo autorizační token) a [oblasti](~/articles/cognitive-services/speech-service/regions.md) jako parametry. Můžete také zadat vlastní koncový bod k určení koncového bodu služby nestandardní.

1. Získání modulu pro rozpoznávání řeči z řeči objekt pro vytváření. Můžete nakonfigurovat jazyk a formát výstupu. Rozlišovače můžete použít výchozí mikrofon zařízení, zvukový datový proud nebo zvuk ze souboru.

1. V případě potřeby vytížit události pro asynchronní operaci. Nástroj pro rozpoznávání pak zavolá obslužné rutiny události je dočasný a finální výsledky. V opačném případě aplikace přijímá pouze určené k transkripci konečný výsledek.

1. Začněte rozpoznávání. Rozpoznávání jednorázová, jako je například rozpoznávání příkaz nebo dotaz, použijte `RecognizeAsync()` metody. Tato metoda vrátí první rozpoznaný utterance. Pro dlouho běžící rozpoznávání jako určené k transkripci, použijte `StartContinuousRecognitionAsync()` metody. Spojit události pro asynchronní rozpoznávání výsledky.

Viz následující fragmenty kódu pro scénáře rozpoznávání řeči, používající sadou SDK pro řeč.

[!include[Get a subscription key](cognitive-services-speech-service-get-subscription-key.md)]
