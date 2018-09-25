---
author: wolfma61
ms.service: cognitive-services
ms.topic: include
ms.date: 09/24/2018
ms.author: wolfma
ms.openlocfilehash: dc2e025cdd9fcc153f3cb81988a9ca3ec729c934
ms.sourcegitcommit: 32d218f5bd74f1cd106f4248115985df631d0a8c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/24/2018
ms.locfileid: "47021126"
---
<!-- N.B. no header, language-agnostic -->

Microsoft Cognitive Services [sadou SDK pro řeč](~/articles/cognitive-services/speech-service/speech-sdk.md) poskytuje nejjednodušší způsob, jak používat **překlad řeči** ve vaší aplikaci.
Sada SDK poskytuje plnou funkčnost služby. Základní proces pro provádění překlad řeči zahrnuje následující kroky:

1. Vytvoření konfigurace překladu řeči a zadejte klíč předplatného Speech service (nebo autorizační token) a [oblasti](~/articles/cognitive-services/speech-service/regions.md) jako parametry. Podle potřeby změňte konfiguraci. Například můžete můžete nakonfigurovat jazyků pro překlady zdroj a cíl, také zadat, jestli se chcete o výstupní text nebo řeči.

1. Vytvořte překlad rozlišovače řeči objekt pro vytváření. Pokud chcete zjistit z jiného zdroje než výchozí mikrofon (například zvukový datový proud nebo zvukový soubor), poskytují zvuku konfigurace.

1. V případě potřeby vytížit události pro asynchronní operaci. Nástroj pro rozpoznávání pak zavolá obslužné rutiny události je dočasný a finální výsledky, jakož i syntézu událost pro volitelné zvukového výstupu. V opačném případě aplikace přijímá pouze určené k transkripci konečný výsledek.

1. Začněte rozpoznávání. Překlad jednorázová použít `RecognizeOnceAsync()` metodu, která vrací první rozpoznaný utterance. Pro dlouho běžící překlady, použijte `StartContinuousRecognitionAsync()` metoda a tie nahoru události pro výsledky rozlišování asynchronní.

Viz následující fragmenty kódu pro překlad řeči scénáře použití sadou SDK pro řeč.

[!INCLUDE [Get a subscription key](cognitive-services-speech-service-get-subscription-key.md)]
