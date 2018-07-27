---
title: Překlad řeči pomocí hlasové služby
description: Naučte se používat pro překlad řeči ve službě řeči.
titleSuffix: Microsoft Cognitive Services
services: cognitive-services
author: v-jerkin
ms.service: cognitive-services
ms.component: speech-service
ms.topic: article
ms.date: 07/16/2018
ms.author: v-jerkin
ms.openlocfilehash: d539fe5a1a031c196c0d40e989d83575715b278a
ms.sourcegitcommit: 068fc623c1bb7fb767919c4882280cad8bc33e3a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/27/2018
ms.locfileid: "39285257"
---
# <a name="translate-speech-using-speech-service"></a>Překlad řeči pomocí Speech service

[Sadou SDK pro řeč](speech-sdk.md) je nejjednodušší způsob, jak používat pro překlad řeči ve vaší aplikaci. Sada SDK poskytuje plnou funkčnost služby. Základní proces pro provádění překlad řeči zahrnuje následující kroky:

1. Vytvořte objekt pro vytváření řeči a zadejte klíč předplatného služby řeči a [oblasti](regions.md) nebo autorizační token. Můžete také nakonfigurovat jazyků pro překlady zdroj a cíl v tomto okamžiku a určení, zda se chcete o výstupní text nebo řeči.

2. Získání nástroje pro rozpoznávání z objekt pro vytváření. Pro překlad vyberte překlad rozlišovače. (Jiné jazyky jsou určené pro *převod řeči na Text*.) Existují různé typy překladu modulu pro rozpoznávání podle zdroje zvuku, který používáte.

4. V případě potřeby vytížit události pro asynchronní operaci. Nástroj pro rozpoznávání volá vaší obslužné rutiny událostí, když má průběžné a konečné výsledky. Jinak vaše aplikace obdrží překlad konečný výsledek.

5. Začněte rozpoznávání a překladu.

# <a name="sdk-samples"></a>Ukázky sady SDK

Nejnovější sadu ukázek, najdete v článku [úložiště GitHub SDK ukázka Cognitive Services řeči](https://aka.ms/csspeech/samples).

# <a name="next-steps"></a>Další postup

- [Získání zkušebního předplatného služby Speech](https://azure.microsoft.com/try/cognitive-services/)
- [Rozpoznávání řeči v jazyce C#](quickstart-csharp-dotnet-windows.md)
