---
author: wolfma61
ms.service: cognitive-services
ms.topic: include
ms.date: 07/27/2018
ms.author: wolfma
ms.openlocfilehash: 79dc1fe9fffd8c44c97cf68a12bb90655b672577
ms.sourcegitcommit: 615403e8c5045ff6629c0433ef19e8e127fe58ac
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/06/2018
ms.locfileid: "39577976"
---
<!-- N.B. no header, language-agnostic -->

[Sadou SDK pro řeč](~/articles/cognitive-services/speech-service/speech-sdk.md) poskytuje způsob, jak rozpoznat **záměry z řeči**, s podporou technologie Speech service a [služby Language Understanding (LUIS)](https://www.luis.ai/home).

1. Vytvořte objekt pro vytváření řeči, poskytuje klíče předplatného služby Language Understanding a [oblasti](~/articles/cognitive-services/speech-service/regions.md#regions-for-intent-recognition). Klíč předplatného služby Language Understanding se nazývá **klíče koncového bodu** v dokumentaci služby. Služba Language Understanding vytváření klíč nelze použít. Viz také **Poznámka** níže.

1. Získání záměru rozlišovač z řeči objekt pro vytváření. Rozlišovače můžete použít výchozí mikrofon zařízení, zvukový datový proud nebo zvuk ze souboru.

1. Získejte jazykový model Principy podle vašeho ID aplikace a přidání záměrů, které potřebujete. 

1. V případě potřeby vytížit události pro asynchronní operaci. Nástroj pro rozpoznávání pak zavolá obslužné rutiny události je dočasný a finální výsledky (včetně záměry). Jinak vaše aplikace dostanou určené k transkripci konečný výsledek.

1. Začněte rozpoznání záměru. Rozpoznávání jednorázová, jako je rozpoznávání příkaz nebo dotaz, použijte `RecognizeAsync()`, která vrací první utterance bude rozpoznán. Pro dlouho běžící rozpoznávání použijte `StartContinuousRecognitionAsync()` a provázat postupy události pro asynchronní rozpoznávání výsledky.

Podívejte se na fragmenty kódu níže pro rozpoznání záměru scénáře s využitím sadou SDK pro řeč. Nahraďte vlastní klíč předplatného Language Understanding (klíče koncového bodu), [oblasti vašeho předplatného](~/articles/cognitive-services/speech-service/regions.md#regions-for-intent-recognition)a AppId záměru modelu na příslušných místech ve vzorcích.

> [!NOTE]
> Na rozdíl od jiných služeb podporovaných službou sadou SDK pro řeč rozpoznání záměru vyžaduje konkrétní předplatné key (klíč koncového bodu služby Language Understanding). [Tady](https://www.luis.ai) najdete další informace o technologii rozpoznávání záměru. Jak získat **klíče koncového bodu** popsaného [tady](https://docs.microsoft.com/en-us/azure/cognitive-services/LUIS/luis-how-to-azure-subscription#create-luis-endpoint-key).
