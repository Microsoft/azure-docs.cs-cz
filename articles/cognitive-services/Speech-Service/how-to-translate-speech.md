---
title: Překlad řeči, hlasové služby pomocí | Dokumentace Microsoftu
description: Naučte se používat pro překlad řeči ve službě řeči.
titleSuffix: Microsoft Cognitive Services
services: cognitive-services
author: v-jerkin
manager: noellelacharite
ms.service: cognitive-services
ms.component: speech-service
ms.topic: article
ms.date: 07/16/2018
ms.author: v-jerkin
ms.openlocfilehash: 6acfcf0ae8ab4c63e6cc943f93da6b947f3d118c
ms.sourcegitcommit: 0b05bdeb22a06c91823bd1933ac65b2e0c2d6553
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/17/2018
ms.locfileid: "39071092"
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
