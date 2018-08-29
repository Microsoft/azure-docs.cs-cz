---
author: wolfma61
ms.service: cognitive-services
ms.topic: include
ms.date: 07/27/2018
ms.author: wolfma
ms.openlocfilehash: a4accb0846ec9cf5efc5ca5aa00c731edd41d4d4
ms.sourcegitcommit: a1140e6b839ad79e454186ee95b01376233a1d1f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/28/2018
ms.locfileid: "43143895"
---
<!-- N.B. no header, language-agnostic -->

Microsoft Cognitive Services [sadou SDK pro řeč](~/articles/cognitive-services/speech-service/speech-sdk.md) poskytuje způsob, jak rozpoznat **záměry z řeči** a podporované službou Cognitive Services [služby Language Understanding (LUIS)](https://www.luis.ai/home).

1. Vytvořte objekt pro vytváření řeči s LUIS klíč předplatného a [oblasti](~/articles/cognitive-services/speech-service/regions.md#regions-for-intent-recognition) jako parametry. Klíč předplatného služby LUIS se nazývá **klíče koncového bodu** v dokumentaci služby. LUIS vytváření klíč nelze použít. (Viz poznámka dál v této části.)

1. Získání záměru rozlišovač z řeči objekt pro vytváření. Rozlišovače můžete použít výchozí mikrofon zařízení, zvukový datový proud nebo zvuk ze souboru.

1. Získat jazykový model principy, která je založena na vaše **AppId**. Přidání záměrů, které budete potřebovat. 

1. V případě potřeby vytížit události pro asynchronní operaci. Nástroj pro rozpoznávání pak zavolá obslužné rutiny události je dočasný a finální výsledky (včetně záměry). Pokud nemáte vytížit událostí, vaše aplikace přijímá pouze určené k transkripci konečný výsledek.

1. Začněte rozpoznání záměru. Rozpoznávání jednorázová, jako je například rozpoznávání příkaz nebo dotaz, použijte `RecognizeAsync()` metody. Tato metoda vrátí první rozpoznaný utterance. Pro dlouho běžící rozpoznávání, použijte `StartContinuousRecognitionAsync()` metody. Spojit události pro asynchronní rozpoznávání výsledky.

Viz následující fragmenty kódu pro rozpoznání záměru scénáře, které používají sadou SDK pro řeč. Nahraďte hodnotami v ukázce si vlastní klíč předplatného služby LUIS (klíče koncového bodu), [oblasti vašeho předplatného](~/articles/cognitive-services/speech-service/regions.md#regions-for-intent-recognition)a **AppId** záměru modelu.

> [!NOTE]
> Na rozdíl od jiných služeb podporovaných službou sadou SDK pro řeč rozpoznání záměru vyžaduje konkrétní předplatné key (klíč koncového bodu služby LUIS). Informace o technologii rozpoznávání záměru, najdete v článku [LUIS webu](https://www.luis.ai). Informace o tom, jak získat **klíče koncového bodu**, naleznete v tématu [vytvořit klíče koncového bodu služby LUIS](https://docs.microsoft.com/azure/cognitive-services/LUIS/luis-how-to-azure-subscription#create-luis-endpoint-key).
