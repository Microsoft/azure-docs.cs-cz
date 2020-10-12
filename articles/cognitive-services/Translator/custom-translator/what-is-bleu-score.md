---
title: Co je BLEU skóre? – Vlastní Překladatel
titleSuffix: Azure Cognitive Services
description: BLEU je měření rozdílů mezi strojovým překladem a uživatelsky vytvořenými překlady odkazů stejné zdrojové věty.
author: swmachan
manager: nitinme
ms.service: cognitive-services
ms.subservice: translator-text
ms.topic: conceptual
ms.date: 08/17/2020
ms.author: swmachan
ms.openlocfilehash: 61c1efb7337bee5fe329c9d548e23f3931e6ce3c
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "88510772"
---
# <a name="what-is-a-bleu-score"></a>Co je BLEU skóre?

[Bleu (dvojjazyčné hodnocení destudie)](https://en.wikipedia.org/wiki/BLEU) je měření rozdílů mezi automatickým překladem a jedním nebo více uživatelsky vytvořenými překlady odkazů stejné zdrojové věty.

## <a name="scoring-process"></a>Proces bodování

BLEU algoritmus porovnává po sobě jdoucí fráze automatického překladu se po sobě jdoucích frázích, které najde v překladu referencí, a vypočítává počet shod, které se váže. Tyto shody jsou nezávislé na poloze. Vyšší stupeň shody indikuje vyšší úroveň podobnosti s překladem odkazů a vyšším skóre. Intelligibility a gramatické správnosti se neberou v úvahu.

## <a name="how-bleu-works"></a>Jak BLEU funguje?

BLEU je v tom, že se dobře koreluje s lidským rozsudkem, a to pomocí průměrného počtu chyb v rámci zkušebních corpus, a ne při pokusu o vytvoření přesného lidského rozhodnutí pro každou větu.

Podrobnější diskuzi o BLEU hodnoceních [najdete tady](https://youtu.be/-UqDljMymMg).

Výsledky BLEU jsou silně závislé na šířce vaší domény, konzistenci testovacích dat s daty školení a optimalizace a množství dat, která máte k dispozici pro vlak. Pokud byly vaše modely vyškoleny na úzké doméně a vaše školicí data jsou v souladu s vašimi testovacími daty, můžete očekávat vysoké BLEU skóre.

>[!NOTE]
>Porovnání výsledků BLEU je oprávněné jenom v případě, že se výsledky BLEU porovnávají se stejnou sadou testů, stejnou dvojici jazyků a stejným modulem MT. Skóre BLEU z jiné sady testů je vázáno na jiné.
