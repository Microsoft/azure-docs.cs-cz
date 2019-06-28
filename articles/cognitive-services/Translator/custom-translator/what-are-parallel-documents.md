---
title: Co jsou paralelní dokumenty? – Vlastní Translator
titleSuffix: Azure Cognitive Services
description: Paralelní dokumenty jsou páry dokumentů, pokud je překlad druhé. Jeden dokument v páru obsahuje vět Zdrojový jazyk a druhý dokument obsahuje tyto věty přeloženy do cílového jazyka.
author: swmachan
manager: christw
ms.service: cognitive-services
ms.subservice: translator-text
ms.date: 02/21/2019
ms.author: swmachan
ms.topic: conceptual
ms.openlocfilehash: 3b7997adfa38fa24bdf41f22c99f06e9a0ba1bf0
ms.sourcegitcommit: f56b267b11f23ac8f6284bb662b38c7a8336e99b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/28/2019
ms.locfileid: "67447394"
---
# <a name="what-are-parallel-documents"></a>Co jsou paralelní dokumenty?

Paralelní dokumenty jsou páry dokumentů, pokud je překlad druhé. Jeden dokument v páru obsahuje vět Zdrojový jazyk a druhý dokument obsahuje tyto věty přeloženy do cílového jazyka.
Nezáleží na jazyk, který je označen jako "zdroj" a jazyk, který je označen jako "cíl" – paralelní dokumentu lze použít k trénování překladový systém v obou směrech.

## <a name="requirements"></a>Požadavky

Budete potřebovat minimálně 10 000 jedinečných paralelní věty a vytrénovat systém. Jako osvědčený postup, můžete průběžně přidat více paralelních obsahu a obsloužených a zlepšovat tak kvalitu překladu systému.

Microsoft vyžaduje od dokumenty nahráli do vlastní překladač nebudou porušovat třetích stran o autorských právech nebo duševního vlastnosti. Další informace najdete v tématu [Terms of Use](https://azure.microsoft.com/support/legal/cognitive-services-terms/).
Nahrání dokumentu s použitím portálu nezmění vlastnictví duševního vlastnictví v dokumentu.

## <a name="use-of-parallel-documents"></a>Používání paralelních dokumentů

Paralelní dokumentů se používají v systému:

1.  Další informace o způsobu slov a vět věty běžně mapování mezi dva jazyků

2.  Další postupy zpracování kontextu vhodné v závislosti na okolní frází. Slovo nemusí vždy přeložit na stejnou slovo v přesném znění v jiném jazyce.

Jako osvědčený postup Ujistěte se, že je 1:1 věty propojeni zdrojové a cílové jazykové verze dokumentů.

Pokud váš projekt je specifických pro doménu (kategorie), dokumenty musí být konzistentní vzhledem k aplikacím v, řečeno terminologií v rámci dané kategorie. Kvality výsledný překladový systém závisí na počtu věty ve vaší sadě dokumentu a kvalitu vět. Další příklady dokumenty obsahují s různými použití slova specifická pro danou kategorii, lepší úlohy systému můžete provést při překladu.

Dokumenty, nahraje jsou privátní pro každý pracovní prostor a můžou používat libovolný počet projektů nebo školení jak potřebujete. Věty extrahovat z vašich dokumentů samostatně uložených v úložišti jako prostý text soubory Unicode a jsou k dispozici pro odstranění. Nepoužívejte vlastní překladač jako úložiště dokumentů, nebudete moct stahovat dokumenty, které jste nahráli ve formátu je nahráli.



## <a name="next-steps"></a>Další postup

- Další informace o použití [slovníku](what-is-dictionary.md) ve vlastní překladač.
