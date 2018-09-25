---
author: wolfma61
ms.service: cognitive-services
ms.topic: include
ms.date: 09/24/2018
ms.author: wolfma
ms.openlocfilehash: 3508f809ab89188e46145df064cbb53ca78c8f9f
ms.sourcegitcommit: 32d218f5bd74f1cd106f4248115985df631d0a8c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/24/2018
ms.locfileid: "47021695"
---
<!-- N.B. no header, language-agnostic -->

Microsoft Cognitive Services [sadou SDK pro řeč](~/articles/cognitive-services/speech-service/speech-sdk.md) poskytuje způsob, jak rozpoznat **záměry z řeči** a podporované službou Cognitive Services [služby Language Understanding (LUIS)](https://www.luis.ai/home).

1. Vytvořte konfiguraci řeči s klíčem služby LUIS předplatného a [oblasti](~/articles/cognitive-services/speech-service/regions.md#regions-for-intent-recognition) jako parametry. Klíč předplatného služby LUIS se nazývá **klíče koncového bodu** v dokumentaci služby. LUIS vytváření klíč nelze použít. (Viz poznámka dál v této části.)

1. Vytvořte záměru rozlišovače řeči konfigurace. Pokud chcete zjistit z jiného zdroje než výchozí mikrofon (například zvukový datový proud nebo zvukový soubor), poskytují zvuku konfigurace.

1. Získat jazykový model principy, která je založena na vaše **AppId**. Přidání záměrů, které budete potřebovat. 

1. V případě potřeby vytížit události pro asynchronní operaci. Nástroj pro rozpoznávání pak zavolá obslužné rutiny události je dočasný a finální výsledky (včetně záměry). Pokud nemáte vytížit událostí, vaše aplikace přijímá pouze určené k transkripci konečný výsledek.

1. Začněte rozpoznání záměru. Rozpoznávání jednorázová, jako je například rozpoznávání příkaz nebo dotaz, použijte `RecognizeOnceAsync()` metody. Tato metoda vrátí první rozpoznaný utterance. Pro dlouho běžící rozpoznávání, použijte `StartContinuousRecognitionAsync()` metody. Spojit události pro asynchronní rozpoznávání výsledky.

Viz následující fragmenty kódu pro rozpoznání záměru scénáře, které používají sadou SDK pro řeč. Nahraďte hodnotami v ukázce si vlastní klíč předplatného služby LUIS (klíče koncového bodu), [oblasti vašeho předplatného](~/articles/cognitive-services/speech-service/regions.md#regions-for-intent-recognition)a **AppId** záměru modelu.

> [!NOTE]
> Na rozdíl od jiných služeb podporovaných službou sadou SDK pro řeč rozpoznání záměru vyžaduje konkrétní předplatné key (klíč koncového bodu služby LUIS). Informace o technologii rozpoznávání záměru, najdete v článku [LUIS webu](https://www.luis.ai). Informace o tom, jak získat **klíče koncového bodu**, naleznete v tématu [vytvořit klíče koncového bodu služby LUIS](https://docs.microsoft.com/azure/cognitive-services/LUIS/luis-how-to-azure-subscription#create-luis-endpoint-key).
