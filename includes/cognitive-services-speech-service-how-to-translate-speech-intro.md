---
author: wolfma61
ms.service: cognitive-services
ms.topic: include
ms.date: 07/27/2018
ms.author: wolfma
ms.openlocfilehash: 4b823a8dc15d33e0537fae348b34c3a3fee84558
ms.sourcegitcommit: 7ad9db3d5f5fd35cfaa9f0735e8c0187b9c32ab1
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/27/2018
ms.locfileid: "39330787"
---
<!-- N.B. no header, language-agnostic -->

[Sadou SDK pro řeč](~/articles/cognitive-services/speech-service/speech-sdk.md) poskytuje nejjednodušší způsob, jak používat pro překlad řeči ve vaší aplikaci.
Sada SDK poskytuje plnou funkčnost služby.
Základní proces pro provádění překlad řeči zahrnuje následující kroky:

1. Vytvořte objekt pro vytváření řeči, poskytuje klíče předplatného služby řeči a [oblasti](~/articles/cognitive-services/speech-service/regions.md).
   Můžete také nakonfigurovat jazyků pro překlady zdroj a cíl, jakož i určující, zda se chcete o výstupní text nebo řeči.

1. Získání rozlišovač překlad z řeči objekt pro vytváření.
   Existují různé typy překlad rozpoznávání podle zdroje zvuku, který používáte.

1. V případě potřeby vytížit události pro asynchronní operaci.
   Nástroj pro rozpoznávání pak zavolá obslužné rutiny události je dočasný a finální výsledky.
   Jinak vaše aplikace dostanou určené k transkripci konečný výsledek.

1. Začněte rozpoznávání a překladu.

Dole ukážeme několik fragmentů kódu pro scénáře překlad řeči s využitím sadou SDK pro řeč.

[!include[Get a Subscription Key](cognitive-services-speech-service-get-subscription-key.md)]
