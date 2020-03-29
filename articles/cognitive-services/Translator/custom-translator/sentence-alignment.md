---
title: Párování vět a zarovnání – vlastní překladač
titleSuffix: Azure Cognitive Services
description: Během tréninku provádění věty přítomné v paralelních dokumentech jsou spárovány nebo zarovnány. Vlastní překladač se učí překlady jednu větu najednou, čtením věty, překlad této věty. Pak se zarovná slova a fráze v těchto dvou větách k sobě navzájem.
author: swmachan
manager: nitinme
ms.service: cognitive-services
ms.subservice: translator-text
ms.date: 02/21/2019
ms.author: swmachan
ms.topic: conceptual
ms.openlocfilehash: cf5b2b84142c9104ea5b3afa3ad179fd0ec07449
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "80370138"
---
# <a name="sentence-pairing-and-alignment-in-parallel-documents"></a>Párování vět a zarovnání v paralelních dokumentech

Během školení jsou věty přítomné v paralelních dokumentech spárovány nebo zarovnány. Vlastní překladač hlásí počet vět, které byl schopen spárovat jako zarovnané věty v každé z datových sad.

## <a name="pairing-and-alignment-process"></a>Proces párování a zarovnání

Vlastní překladač se učí překlady vět jednu větu najednou. To čte větu ze zdroje, a pak překlad této věty z cíle. Pak se zarovná slova a fráze v těchto dvou větách k sobě navzájem. Tento proces umožňuje vytvořit mapu slov a frází v jedné větě na ekvivalentní slova a fráze v překladu této věty. Zarovnání se snaží zajistit, aby systém vlaky na věty, které jsou překlady navzájem.

## <a name="pre-aligned-documents"></a>Předem zarovnané dokumenty

Pokud víte, že máte paralelní dokumenty, můžete zarovnání věty přepsat zadáním předem zarovnaných textových souborů. Všechny věty z obou dokumentů můžete extrahovat do textového souboru, uspořádat jednu větu na řádek a nahrát s příponou. `.align` Rozšíření `.align` signalizuje vlastní překladač, že by měl přeskočit zarovnání věty.

Chcete-li dosáhnout nejlepších výsledků, pokuste se zajistit, aby v souborech byla jedna věta na řádek.Ve větě nejsou znaky nového řádku, protože to způsobí špatné zarovnání.

## <a name="suggested-minimum-number-of-sentences"></a>Doporučený minimální počet vět

Aby školení proběhlo úspěšně, v následující tabulce je uveden minimální počet vět požadovaných v každém typu dokumentu.Toto omezení je záchranná síť, která zajišťuje, že paralelní věty obsahují dostatek jedinečné slovní zásoby pro úspěšné trénování překladového modelu. Obecným vodítkem je mít více in-domain paralelní věty kvality lidského překladu by měly produkovat vyšší kvalitu modelů.

| Typ dokumentu   | Navrhovaný minimální počet trestů | Maximální počet trestů |
|------------|--------------------------------------------|--------------------------------|
| Školení   | 10 000                                     | Bez horníhranice                 |
| Ladění     | 500                                      | 2,500       |
| Testování    | 500                                      | 2,500  |
| Slovník | 0                                          | Bez horníhranice                 |

> [!NOTE]
> - Školení se nespustí a selže, pokud není splněn minimální trest 10,000 pro školení. 
> - Ladění a testování jsou volitelné. Pokud je neposkytnete, systém odebere odpovídající procento z školení, které bude používáno pro ověření a testování. 
> - Model můžete trénovat pouze pomocí dat slovníku. Informace o [tom, co je slovník](https://docs.microsoft.com/azure/cognitive-services/translator/custom-translator/what-is-dictionary), naleznete v

## <a name="next-steps"></a>Další kroky

- Přečtěte si, jak používat [slovník](what-is-dictionary.md) v custom translatoru.
