---
title: Pokyny pro pojmenování klíčových slov – služba Speech Service
titleSuffix: Azure Cognitive Services
description: Vytvoření efektivního klíčového slova je nezbytné pro zajištění toho, aby vaše zařízení důsledně a přesně reagovalo.
services: cognitive-services
author: IEvangelist
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 03/11/2020
ms.author: dapine
ms.openlocfilehash: 1d363f9f6f9cb5b1ea28e6ae15a9cef857304cab
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "79370671"
---
# <a name="guidelines-for-creating-an-effective-keyword"></a>Pokyny pro vytvoření efektivního klíčového slova

Vytvoření efektivního klíčového slova je nezbytné pro zajištění toho, aby vaše zařízení důsledně a přesně reagovalo. Přizpůsobení klíčového slova je účinný způsob, jak odlišit vaše zařízení a posílit vaši značku. V tomto článku se dozvíte některé hlavní zásady pro vytvoření efektivní klíčové slovo.

## <a name="choose-an-effective-keyword"></a>Výběr efektivního klíčového slova

Při výběru klíčového slova zvažte následující pokyny:

> [!div class="checklist"]
> * Klíčové slovo by mělo být anglické slovo nebo fráze.
> * Nemělo by to trvat déle než dvě sekundy.
> * Slova 4 až 7 slabik fungují nejlépe. Například "Hey, Computer" je dobré klíčové slovo. Jen "Hej" je chudá.
> * Klíčová slova by se měla řídit běžnými pravidly výslovnosti v angličtině.
> * Jedinečné nebo dokonce vymyšlené slovo, které se řídí běžnými pravidly výslovnosti angličtiny, může snížit falešně pozitivní výsledky. Například "computerama" může být dobré klíčové slovo.
> * Nevybírejte společné slovo. Například "jíst" a "jít" jsou slova, která lidé často říkají v běžné konverzaci. Mohou to být falešné spouštěče pro vaše zařízení.
> * Nepoužívejte klíčové slovo, které může mít alternativní výslovnosti. Uživatelé by museli znát "správnou" výslovnost, aby jejich zařízení reagovalo. Například "509" lze vyslovit "pět nula devět,""pět oh devět," nebo "pět set devět." "R.E.I." lze vyslovit "r-e-i" nebo "ray". "Live" může být vyslovováno "/līv/" nebo "/liv/".
> * Nepoužívejte speciální znaky, symboly ani číslice. Problematická klíčová slova mohou být například "Go#" a "20 + cats". Nicméně, "jít ostrý" nebo "dvacet plus kočky" může fungovat. Stále můžete používat symboly ve vaší značce a používat marketing a dokumentaci k posílení správné výslovnosti.

> [!NOTE]
> Pokud jako klíčové slovo zvolíte slovo chráněné ochrannou známkou, ujistěte se, že tuto ochrannou známku vlastníte nebo že máte od vlastníka ochranné známky oprávnění k použití tohoto slova. Společnost Microsoft nenese odpovědnost za žádné právní problémy, které by mohly vzniknout při výběru klíčového slova.

## <a name="next-steps"></a>Další kroky

Přečtěte si, jak [vytvořit vlastní klíčové slovo pomocí aplikace Speech Studio](speech-devices-sdk-create-kws.md).
