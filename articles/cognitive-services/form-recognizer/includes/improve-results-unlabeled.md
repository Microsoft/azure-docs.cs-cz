---
author: PatrickFarley
ms.service: cognitive-services
ms.subservice: forms-recognizer
ms.topic: include
ms.date: 06/12/2019
ms.author: pafarley
ms.openlocfilehash: f0761847c3677b324ef16c5987eb9a1561dbcbe0
ms.sourcegitcommit: 9ee0cbaf3a67f9c7442b79f5ae2e97a4dfc8227b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "75379283"
---
Zkontrolujte `"confidence"` hodnoty pro každý výsledek `"pageResults"` klíče/hodnoty pod uzlem. Měli byste se také podívat `"readResults"` na skóre spolehlivosti v uzlu, které odpovídají operaci čtení textu. Důvěra výsledků čtení nemá vliv na důvěru výsledků extrakce klíče/hodnoty, takže byste měli zkontrolovat obojí.
* Pokud jsou skóre spolehlivosti pro operaci čtení nízké, pokuste se zlepšit kvalitu vstupních dokumentů (viz [Požadavky na vstup](../overview.md#input-requirements)).
* Pokud jsou skóre spolehlivosti pro operaci extrakce klíč/hodnota nízká, ujistěte se, že analyzované dokumenty jsou stejného typu jako dokumenty použité v trénovací sadě. Pokud dokumenty v trénovací sadě mají varianty vzhledu, zvažte jejich rozdělení do různých složek a školení jeden model pro každou variantu.

Skóre spolehlivosti, na které cílíte, bude záviset na vašem případu použití, ale obecně je vhodné cílit na skóre 80 % nebo vyšší. V případě citlivějších případů, jako je čtení lékařských záznamů nebo fakturačních výpisů, se doporučuje skóre 100 %.