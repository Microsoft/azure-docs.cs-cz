---
author: wolfma61
ms.service: cognitive-services
ms.topic: include
ms.date: 07/27/2018
ms.author: wolfma
ms.openlocfilehash: 74b9da1b3b81f9f35f231ef5caef8eafedc9aefc
ms.sourcegitcommit: 30fd606162804fe8ceaccbca057a6d3f8c4dd56d
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/30/2018
ms.locfileid: "39343035"
---
<!-- N.B. no header, language-agnostic -->

[Sadou SDK pro řeč](~/articles/cognitive-services/speech-service/speech-sdk.md) poskytuje nejjednodušší způsob, jak používat **překlad řeči** ve vaší aplikaci.
Sada SDK poskytuje plnou funkčnost služby. Základní proces pro provádění překlad řeči zahrnuje následující kroky:

1. Vytvořte objekt pro vytváření řeči, klíč předplatného služby řeči nebo autorizační token a [oblasti](~/articles/cognitive-services/speech-service/regions.md).
   
1. Vytvořte překlad rozlišovače řeči objekt pro vytváření. Můžete nakonfigurovat jazyků pro překlady zdroj a cíl, jakož i určující, zda se chcete o výstupní text nebo řeči. Existují různé typy překlad rozpoznávání podle zdroje zvuku, který používáte.

1. V případě potřeby vytížit události pro asynchronní operaci. Nástroj pro rozpoznávání pak zavolá obslužné rutiny události je dočasný a finální výsledky, jakož i syntézu událost pro volitelné zvukového výstupu. Jinak vaše aplikace dostanou určené k transkripci konečný výsledek.

1. Začněte rozpoznávání. Pro použití překladu jednorázová RecognizeAsync(), které vrací první utterance bude rozpoznán. Pro dlouho běžící překlady pomocí StartContinuousRecognitionAsync() a zablokovat větší události pro výsledky rozlišování asynchronní.

Podívejte se na fragmenty kódu níže pro scénáře překlad řeči pomocí sady SDK řeči.

[!include[Get a Subscription Key](cognitive-services-speech-service-get-subscription-key.md)]
