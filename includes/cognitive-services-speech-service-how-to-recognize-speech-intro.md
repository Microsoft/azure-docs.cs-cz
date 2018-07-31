---
author: wolfma61
ms.service: cognitive-services
ms.topic: include
ms.date: 07/27/2018
ms.author: wolfma
ms.openlocfilehash: d7d73d9c5e85ac550b24c73797d536dc4d0fc6ed
ms.sourcegitcommit: 30fd606162804fe8ceaccbca057a6d3f8c4dd56d
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/30/2018
ms.locfileid: "39346269"
---
<!-- N.B. no header, no intents here, language-agnostic -->

[Sadou SDK pro řeč](~/articles/cognitive-services/speech-service/speech-sdk.md) poskytuje nejjednodušší způsob, jak používat **převod řeči na Text** ve vaší aplikaci se všemi funkcemi.

1. Vytvořte objekt pro vytváření řeči, klíč předplatného služby řeči nebo autorizační token a [oblasti](~/articles/cognitive-services/speech-service/regions.md). Můžete také určit vlastní koncový bod k určení koncového bodu služby nestandardní.

1. Získání modulu pro rozpoznávání řeči z řeči objekt pro vytváření. Můžete nakonfigurovat jazyka vstupní i výstupní formát.  Rozlišovače můžete použít výchozí mikrofon zařízení, zvukový datový proud nebo zvuk ze souboru.

1. V případě potřeby vytížit události pro asynchronní operaci. Nástroj pro rozpoznávání pak zavolá obslužné rutiny události je dočasný a finální výsledky.  Jinak vaše aplikace dostanou určené k transkripci konečný výsledek.

1. Začněte rozpoznávání. Rozpoznávání jednorázová, jako je rozpoznávání příkaz nebo dotaz, použijte `RecognizeAsync()`, která vrací první utterance bude rozpoznán. Pro dlouho běžící rozpoznávání, jako jsou určené k transkripci, použijte `StartContinuousRecognitionAsync()` a provázat postupy události pro asynchronní rozpoznávání výsledky.

Podívejte se na fragmenty kódu níže pro scénáře rozpoznávání řeči pomocí sady SDK řeči.

[!include[Get a Subscription Key](cognitive-services-speech-service-get-subscription-key.md)]
