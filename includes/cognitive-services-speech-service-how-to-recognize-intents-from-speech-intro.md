---
author: wolfma61
ms.service: cognitive-services
ms.topic: include
ms.date: 07/27/2018
ms.author: wolfma
ms.openlocfilehash: 1b55576581ebddc90c0af6b99a04dbe66d2e3b87
ms.sourcegitcommit: 7ad9db3d5f5fd35cfaa9f0735e8c0187b9c32ab1
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/27/2018
ms.locfileid: "39330783"
---
<!-- N.B. no header, language-agnostic -->

[Sadou SDK pro řeč](~/articles/cognitive-services/speech-service/speech-sdk.md) poskytuje způsob, jak rozpoznat záměry z řeči Speech service používá technologii a [služby Language Understanding (LUIS)](https://luis.ai).

1. Vytvořte objekt pro vytváření řeči, poskytuje klíče předplatného služby Language Understanding a [oblasti](~/articles/cognitive-services/speech-service/regions.md#regions-for-intent-recognition).


1. Získání záměru rozlišovač z řeči objekt pro vytváření.
   Rozlišovače můžete použít výchozí mikrofon zařízení, zvukový datový proud nebo zvuk ze souboru.

1. V případě potřeby vytížit události pro asynchronní operaci.
   Nástroj pro rozpoznávání pak zavolá obslužné rutiny události je dočasný a finální výsledky.
   Jinak vaše aplikace dostanou určené k transkripci konečný výsledek.

1. Začněte rozpoznávání.
   Rozpoznávání jednorázová, jako je rozpoznávání příkaz nebo dotaz, použijte `RecognizeAsync()`, která vrací první utterance bude rozpoznán.
   Pro dlouho běžící rozpoznávání, jako jsou určené k transkripci, použijte `StartContinuousRecognitionAsync()` a provázat postupy události pro asynchronní rozpoznávání výsledky.

Dole ukážeme několik fragmentů kódu pro rozpoznání záměru scénáře s využitím sadou SDK pro řeč.

> [!NOTE]
> Nejdřív získáte klíč předplatného.
> Na rozdíl od jiných služeb podporovaných službou sadou SDK pro řeč rozpoznání záměru vyžaduje klíč konkrétní předplatné.
> [Tady](https://www.luis.ai) najdete další informace o technologii rozpoznávání záměru, jakož i informace o tom, jak získat klíč předplatného.
> Nahraďte vlastní klíč předplatného Language Understanding [oblasti vašeho předplatného](~/articles/cognitive-services/speech-service/regions.md#regions-for-intent-recognition)a AppId záměru modelu na příslušných místech ve vzorcích.
