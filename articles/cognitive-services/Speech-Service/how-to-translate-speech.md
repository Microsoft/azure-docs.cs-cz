---
title: Převede řeči pomocí služby řeči | Microsoft Docs
description: Další informace o použití řeči překlad ve službě řeči.
titleSuffix: Microsoft Cognitive Services
services: cognitive-services
author: v-jerkin
manager: noellelacharite
ms.service: cognitive-services
ms.component: speech-service
ms.topic: article
ms.date: 05/07/2018
ms.author: v-jerkin
ms.openlocfilehash: 7f39f284998489574049d82c44b3d3a0a3797adb
ms.sourcegitcommit: 3c3488fb16a3c3287c3e1cd11435174711e92126
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/07/2018
ms.locfileid: "35343969"
---
# <a name="translate-speech-using-speech-service"></a>Převede řeči pomocí služby řeči

[Řeči SDK](speech-sdk.md) je nejjednodušší způsob, jak použít převod řeči v aplikaci. Sada SDK poskytuje všechny funkce služby. Základní proces provádí překlad řeči zahrnuje následující kroky:

1. Vytvořte objekt pro vytváření řeči a zadejte klíč předplatného služby řeči nebo autorizační token. Můžete také nakonfigurovat překlad jazyky zdrojové a cílové v tomto okamžiku a určení, zda má být text nebo řečovém výstupu.

2. Získání nástroje pro rozpoznávání z objektu pro vytváření. Pro překlad vyberte pro rozpoznávání překlad. (Jiné jazyky jsou pro *řeči na Text*.) Existují různé charakteristikami překlad rozpoznávání rukopisu na základě zvuk zdroje, které používáte.

4. V případě potřeby vytížit události pro asynchronní operace. Nástroj pro rozpoznávání volá vaší obslužné rutiny událostí, pokud má průběžné a konečné výsledky. Vaše aplikace přijímá, jinak hodnota výsledku konečné překlad.

5. Spusťte rozpoznávání a překlad.

# <a name="sdk-samples"></a>Ukázky sady SDK

Nejnovější sadu vzorků, najdete v článku [úložiště GitHub SDK ukázka kognitivní služby řeči](https://aka.ms/csspeech/samples).

# <a name="next-steps"></a>Další postup

- [Získat zkušební verze předplatného řeči](https://azure.microsoft.com/try/cognitive-services/)
- [Rozpoznávat řeč v jazyce C#](quickstart-csharp-windows.md)
