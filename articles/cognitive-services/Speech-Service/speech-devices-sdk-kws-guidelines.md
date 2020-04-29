---
title: Pokyny pro pojmenovávání klíčových slov – služba pro rozpoznávání řeči
titleSuffix: Azure Cognitive Services
description: Vytvoření efektivního klíčového slova je důležité pro zajištění, že zařízení bude konzistentně a přesně reagovat.
services: cognitive-services
author: trevorbye
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 03/11/2020
ms.author: trbye
ms.openlocfilehash: c00d27e5e7b7a8d614ce94ea4024b6093669757c
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/29/2020
ms.locfileid: "81399850"
---
# <a name="guidelines-for-creating-an-effective-keyword"></a>Pokyny pro vytvoření efektivního klíčového slova

Vytvoření efektivního klíčového slova je důležité pro zajištění, že zařízení bude konzistentně a přesně reagovat. Přizpůsobení klíčového slova je efektivní způsob, jak odlišit vaše zařízení a posílit vaše branding. V tomto článku se dozvíte o některých principech identifikátorů GUID pro vytvoření efektivního klíčového slova.

## <a name="choose-an-effective-keyword"></a>Zvolit efektivní klíčové slovo

Při volbě klíčového slova Vezměte v úvahu následující pokyny:

> [!div class="checklist"]
> * Klíčové slovo by mělo být anglické slovo nebo fráze.
> * Řekněme, že by neměl trvat déle než dvě sekundy.
> * Slova 4 až 7 slabiky fungují nejlépe. Například "Hey, Computer" je klíčové slovo dobrý. Pouze "Hey" je nekvalitní.
> * Klíčová slova by měla dodržovat běžná pravidla pro výslovnost angličtiny.
> * Jedinečné nebo dokonce zavedené slovo, které následuje po běžných pravidlech výslovnosti angličtiny, může snížit falešně pozitivní výsledky. Například "computerama" může být dobrým klíčovým slovem.
> * Nevybírejte běžné slovo. Například "EAT" a "jít" jsou slova, která lidé v běžné konverzaci často říkají. Pro vaše zařízení můžou být triggery nepravdivé.
> * Vyhněte se použití klíčového slova, které může mít alternativní výslovnost. Uživatelé by museli znát správné výslovnost, aby zařízení mohla reagovat. Například "509" lze vyslovit "5 0 9", "5 0 9" nebo "509". "R.E.I." lze vyslovit "r-e-i" nebo "Ray". "Živý" může být vyslovný jako "/līv/" nebo "/Liv/".
> * Nepoužívejte speciální znaky, symboly a číslice. Například "jít #" a "20 + kočky" mohou být problematická klíčová slova. Může se ale stát, že "jít prudce" nebo "dvacet plus kočky". Můžete pořád používat symboly v brandingu a používat marketing a dokumentaci k posílení správné výslovnosti.

> [!NOTE]
> Pokud zvolíte klíčové slovo jako klíčové slovo, ujistěte se, že vlastníte tuto ochrannou známku nebo že máte oprávnění od vlastníka ochranné známky, abyste mohli toto slovo použít. Společnost Microsoft není odpovědná za žádné právní problémy, které mohou nastat podle vašeho výběru klíčového slova.

## <a name="next-steps"></a>Další kroky

Naučte se [vytvářet vlastní klíčová slova pomocí rozpoznávání řeči Studio](speech-devices-sdk-create-kws.md).
