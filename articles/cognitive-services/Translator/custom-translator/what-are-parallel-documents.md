---
title: Co jsou paralelní dokumenty? - Vlastní Překladatel
titleSuffix: Azure Cognitive Services
description: Paralelní dokumenty jsou dvojice dokumentů, kde jeden je překlad druhého. Jeden dokument v páru obsahuje věty ve zdrojovém jazyce a druhý dokument obsahuje tyto věty přeložené do cílového jazyka.
author: swmachan
manager: nitinme
ms.service: cognitive-services
ms.subservice: translator-text
ms.date: 02/21/2019
ms.author: swmachan
ms.topic: conceptual
ms.openlocfilehash: d7c38a44e3111a319e4146b3c9b71a22b0d31bfd
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "72675471"
---
# <a name="what-are-parallel-documents"></a>Co jsou paralelní dokumenty?

Paralelní dokumenty jsou dvojice dokumentů, kde jeden je překlad druhého. Jeden dokument v páru obsahuje věty ve zdrojovém jazyce a druhý dokument obsahuje tyto věty přeložené do cílového jazyka.
Nezáleží na tom, který jazyk je označen jako "zdroj" a který jazyk je označen jako "cíl" – paralelní dokument lze použít k trénování překladového systému v obou směrech.

## <a name="requirements"></a>Požadavky

Budete potřebovat minimálně 10 000 jedinečných paralelních vět pro trénování systému. Toto omezení je záchranná síť, která zajišťuje, že paralelní věty obsahují dostatek jedinečné slovní zásoby pro úspěšné trénování překladového modelu. Jako osvědčený postup průběžně přidávejte další paralelní obsah a přeškolujte se, abyste zlepšili kvalitu vašeho překladatelského systému. Viz [zarovnání věty](https://docs.microsoft.com/azure/cognitive-services/translator/custom-translator/sentence-alignment).

Společnost Microsoft vyžaduje, aby dokumenty nahrané do vlastního překladače neporušovaly autorská práva nebo duševní vlastnictví třetí strany. Další informace naleznete v [podmínkách použití](https://azure.microsoft.com/support/legal/cognitive-services-terms/).
Nahrání dokumentu pomocí portálu nemění vlastnictví duševního vlastnictví v samotném dokumentu.

## <a name="use-of-parallel-documents"></a>Použití paralelních dokumentů

Paralelní dokumenty jsou používány systémem:

1.  Chcete-li se dozvědět, jak jsou slova, fráze a věty běžně mapovány mezi těmito dvěma jazyky.

2.  Chcete-li se dozvědět, jak zpracovat příslušný kontext v závislosti na okolní fráze. Slovo nemusí vždy přeložit na přesně stejné slovo v jiném jazyce.

Osvědčeným postupem je zajistit, aby mezi zdrojovými a cílovými jazykovými verzemi dokumentů existovala shoda vět 1:1.

Pokud je projekt specifický pro doménu (kategorii), měly by být dokumenty v terminologii v rámci dané kategorie konzistentní. Kvalita výsledného překladového systému závisí na počtu vět v sadě dokumentů a kvalitě vět. Čím více příkladů dokumenty obsahují s různými způsoby použití pro slovo specifické pro vaši kategorii, tím lepší práci může systém dělat během překladu.

Nahrané dokumenty jsou soukromé pro každý pracovní prostor a lze je použít v tolika projektech nebo školeních, kolik chcete. Věty extrahované z vašich dokumentů jsou uloženy samostatně ve vašem úložišti jako prosté textové soubory Unicode a jsou k dispozici pro odstranění. Nepoužívejte vlastní překladač jako úložiště dokumentů, nebudete moci stáhnout dokumenty, které jste nahráli ve formátu, který jste nahráli.



## <a name="next-steps"></a>Další kroky

- Přečtěte si, jak používat [slovník](what-is-dictionary.md) v custom translatoru.
