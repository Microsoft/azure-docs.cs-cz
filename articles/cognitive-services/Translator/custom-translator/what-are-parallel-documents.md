---
title: Co jsou paralelní dokumenty? – Vlastní Překladatel
titleSuffix: Azure Cognitive Services
description: Paralelní dokumenty jsou páry dokumentů, kde jeden je převod druhé. Jeden dokument ve dvojici obsahuje ve zdrojovém jazyce věty a druhý dokument obsahuje tyto věty přeložené do cílového jazyka.
author: laujan
manager: nitinme
ms.service: cognitive-services
ms.subservice: translator-text
ms.date: 08/17/2020
ms.author: lajanuar
ms.topic: conceptual
ms.openlocfilehash: 64192014c9dfe5bb958e730dae62e504f0976fa1
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2021
ms.locfileid: "98895590"
---
# <a name="what-are-parallel-documents"></a>Co jsou paralelní dokumenty?

Paralelní dokumenty jsou páry dokumentů, kde jeden je převod druhé. Jeden dokument ve dvojici obsahuje ve zdrojovém jazyce věty a druhý dokument obsahuje tyto věty přeložené do cílového jazyka.
Nezáleží na tom, který jazyk je označený jako "zdroj" a který jazyk je označený jako "cíl" – paralelní dokument se dá použít k tomu, aby se v obou směrech mohl naučit systém překladu.

## <a name="requirements"></a>Požadavky

Abyste mohli naučit systém, budete potřebovat minimálně 10 000 jedinečných zarovnaných a paralelních vět. Toto omezení je bezpečnostní síť, aby bylo zajištěno, že vaše paralelní věty obsahují dostatek jedinečného slovníku, aby bylo možné přeškolit model překladu. Osvědčeným postupem je průběžně přidávat více paralelních obsahu a přeškolovat se, aby se zlepšila kvalita vašeho systému překladu. Přečtěte si prosím [Zarovnání vět](./sentence-alignment.md).

Microsoft vyžaduje, aby dokumenty odeslané do vlastního překladatele nenarušily autorské právo nebo duševní vlastnictví třetí strany. Další informace najdete v tématu věnovaném [podmínkám použití](https://azure.microsoft.com/support/legal/cognitive-services-terms/).
Nahrání dokumentu pomocí portálu nemění vlastnictví duševního vlastnictví v samotném dokumentu.

## <a name="use-of-parallel-documents"></a>Použití paralelních dokumentů

Systém používá paralelní dokumenty:

1.  Informace o tom, jak jsou slova, fráze a věty často mapované mezi oběma jazyky.

2.  Zjistíte, jak zpracovat příslušný kontext v závislosti na okolních frázích. Slovo se nemusí vždycky překládat na stejné slovo v jiném jazyce.

V souladu s osvědčeným postupem se ujistěte, že mezi zdrojovými a cílovými jazykovými verzemi dokumentů je 1:1 věta.

Pokud je váš projekt specifický pro doménu (kategorie), měly by být dokumenty v terminologii v této kategorii konzistentní. Kvalita výsledného systému překladu závisí na počtu vět v sadě dokumentů a na kvalitě vět. V dalších příkladech jsou dokumenty, které obsahují různé použití pro konkrétní kategorii, lepší úlohu, kterou může systém provádět během překladu.

Nahrané dokumenty jsou pro každý pracovní prostor privátní a dají se použít v tolika projektech nebo školeních, kolik vám vyhovuje. Věty extrahované z vašich dokumentů se ukládají samostatně v úložišti jako textové soubory s kódováním Unicode a jsou dostupné pro odstranění. Nepoužívejte vlastní překladatele jako úložiště dokumentů, takže nebudete moct stahovat dokumenty, které jste nahráli ve formátu, který jste nahráli.



## <a name="next-steps"></a>Další kroky

- Naučte se používat [slovník](what-is-dictionary.md) ve vlastním překladateli.