---
title: Pokyny pro pojmenovávání klíčových slov – služba pro rozpoznávání řeči
titleSuffix: Azure Cognitive Services
description: Vytvoření efektivního klíčového slova je důležité pro zajištění, že zařízení bude konzistentně a přesně reagovat.
services: cognitive-services
author: IEvangelist
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 12/11/2019
ms.author: dapine
ms.openlocfilehash: 3c3943f7269fa2d0ed25acf2215549635b5f16ac
ms.sourcegitcommit: 7b25c9981b52c385af77feb022825c1be6ff55bf
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/13/2020
ms.locfileid: "79219563"
---
# <a name="guidelines-for-creating-an-effective-keyword"></a>Pokyny pro vytvoření efektivního klíčového slova

Vytvoření efektivního klíčového slova je důležité pro zajištění, že zařízení bude konzistentně a přesně reagovat. Přizpůsobení klíčového slova je efektivní způsob, jak odlišit vaše zařízení a posílit vaše branding. V tomto článku se dozvíte o některých principech identifikátorů GUID pro vytvoření efektivního klíčového slova.

## <a name="choose-an-effective-keyword"></a>Zvolit efektivní klíčové slovo

Při volbě klíčového slova Vezměte v úvahu následující pokyny:

* Klíčové slovo by mělo být anglické slovo nebo fráze.

* By měla trvat delší než 2 sekundy říct.

* Slova 4 až 7 slabik fungují nejlépe. Například "Hey, Computer" je klíčové slovo dobrý. Právě "Hey" je nízký.

* Klíčová slova by měla dodržovat běžná pravidla pro výslovnost angličtiny.

* Jedinečný nebo dokonce zhotovené slovo, které následuje běžných pravidel anglické výslovnost může snížit počet falešně pozitivních výsledků. Například "computerama" může být dobrým klíčovým slovem.

* Nevybírejte běžné slovo. Například "jíst" a "přejděte" jsou slova, které lidé často říkají v běžném konverzace. Mohou být false aktivační události pro vaše zařízení.

* Vyhněte se použití klíčového slova, které může mít alternativní výslovnost. Uživatelé museli vědět, výslovnost "vpravo" zařízení reagovat. Například "509" se mohou projevit "pět nula devíti," "pět ale devět," nebo "pět set a devět." "R.E.I." se mohou projevit "r-e – i" nebo "ray." "Live" se mohou projevit "/līv/" nebo "/liv/".

* Nepoužívejte speciální znaky, symboly nebo číslic. Například "jít #" a "20 + kočky" mohou být problematická klíčová slova. Ale "Přejít sharp" nebo "dvacet plus kočky" mohou fungovat. Stále můžete použít symboly v brandingu a můžete posílit správné výslovnost uvádění na trh a dokumentace.

> [!NOTE]
> Pokud zvolíte klíčové slovo jako klíčové slovo, ujistěte se, že vlastníte tuto ochrannou známku nebo že máte oprávnění od vlastníka ochranné známky, abyste mohli toto slovo použít. Společnost Microsoft není odpovědná za žádné právní problémy, které mohou nastat podle vašeho výběru klíčového slova.

## <a name="next-steps"></a>Další kroky

Naučte se [vytvářet vlastní klíčová slova pomocí rozpoznávání řeči Studio](speech-devices-sdk-create-kws.md).
