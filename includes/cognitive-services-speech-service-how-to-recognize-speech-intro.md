---
author: wolfma61
ms.service: cognitive-services
ms.topic: include
ms.date: 07/27/2018
ms.author: wolfma
ms.openlocfilehash: ca342a812f6a8de19c732b5df4fab14a825f6c51
ms.sourcegitcommit: 7ad9db3d5f5fd35cfaa9f0735e8c0187b9c32ab1
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/27/2018
ms.locfileid: "39330789"
---
<!-- N.B. no header, no intents here, language-agnostic -->

[Sadou SDK pro řeč](~/articles/cognitive-services/speech-service/speech-sdk.md) poskytuje nejjednodušší způsob, jak používat **převod řeči na Text** ve vaší aplikaci se všemi funkcemi.

1. Vytvořte objekt pro vytváření řeči, klíč předplatného služby řeči nebo autorizační token a [oblasti](~/articles/cognitive-services/speech-service/regions.md).
   Můžete také nakonfigurovat možnosti, jako je například rozpoznávání jazyka nebo vlastní koncový bod pro vlastní modely pro rozpoznávání řeči.

1. Získání modulu pro rozpoznávání řeči z řeči objekt pro vytváření.
   Rozlišovače můžete použít výchozí mikrofon zařízení, zvukový datový proud nebo zvuk ze souboru.

1. V případě potřeby vytížit události pro asynchronní operaci.
   Nástroj pro rozpoznávání pak zavolá obslužné rutiny události je dočasný a finální výsledky.
   Jinak vaše aplikace dostanou určené k transkripci konečný výsledek.

1. Začněte rozpoznávání.
   Rozpoznávání jednorázová, jako je rozpoznávání příkaz nebo dotaz, použijte `RecognizeAsync()`, která vrací první utterance bude rozpoznán.
   Pro dlouho běžící rozpoznávání, jako jsou určené k transkripci, použijte `StartContinuousRecognitionAsync()` a provázat postupy události pro asynchronní rozpoznávání výsledky.

Tady vám ukážeme, několik fragmentů kódu pro scénáře rozpoznávání řeči pomocí sady SDK řeči.

[!include[Get a Subscription Key](cognitive-services-speech-service-get-subscription-key.md)]
