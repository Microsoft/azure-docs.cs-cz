---
author: wolfma61
ms.service: cognitive-services
ms.topic: include
ms.date: 07/27/2018
ms.author: wolfma
ms.openlocfilehash: ee50a104a75d3cd5ff958bd49a1ff7010c5d5083
ms.sourcegitcommit: a1140e6b839ad79e454186ee95b01376233a1d1f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/28/2018
ms.locfileid: "43144569"
---
<!-- N.B. no header, language-agnostic -->

Microsoft Cognitive Services [sadou SDK pro řeč](~/articles/cognitive-services/speech-service/speech-sdk.md) poskytuje nejjednodušší způsob, jak používat **překlad řeči** ve vaší aplikaci.
Sada SDK poskytuje plnou funkčnost služby. Základní proces pro provádění překlad řeči zahrnuje následující kroky:

1. Vytvořte objekt pro vytváření řeči a zadejte klíč předplatného služby řeči nebo autorizační token a [oblasti](~/articles/cognitive-services/speech-service/regions.md) jako parametry.
   
1. Vytvořte překlad rozlišovače řeči objekt pro vytváření. Je můžete nakonfigurovat jazyků pro překlady zdroj a cíl, jakož i určit, zda má být text nebo řeči výstup. Existují různé typy podle zdroje zvuku, který můžete použít nástroje pro rozpoznávání překladu.

1. V případě potřeby vytížit události pro asynchronní operaci. Nástroj pro rozpoznávání pak zavolá obslužné rutiny události je dočasný a finální výsledky, jakož i syntézu událost pro volitelné zvukového výstupu. V opačném případě aplikace přijímá pouze určené k transkripci konečný výsledek.

1. Začněte rozpoznávání. Překlad jednorázová použít `RecognizeAsync()` metodu, která vrací první rozpoznaný utterance. Pro dlouho běžící překlady, použijte `StartContinuousRecognitionAsync()` metoda a tie nahoru události pro výsledky rozlišování asynchronní.

Viz následující fragmenty kódu pro překlad řeči scénáře použití sadou SDK pro řeč.

[!INCLUDE [Get a subscription key](cognitive-services-speech-service-get-subscription-key.md)]
