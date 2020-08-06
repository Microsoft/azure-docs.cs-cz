---
author: baanders
description: zahrnout soubor pro omezení digitálních vláken Azure
ms.service: digital-twins
ms.topic: include
ms.date: 6/9/2020
ms.author: baanders
ms.openlocfilehash: 22b00b41c7fce0af57fd9f92b0f42bbd9412afda
ms.sourcegitcommit: 5a37753456bc2e152c3cb765b90dc7815c27a0a8
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/04/2020
ms.locfileid: "87771047"
---
### <a name="functional-limits"></a>Funkční omezení

Následující tabulka uvádí funkční omezení digitálních vláken Azure v aktuální verzi Preview.

| Oblast | Schopnost | Výchozí omezení | Měnitelný? |
| --- | --- | --- | --- |
| Prostředek Azure | Počet instancí digitálních vláken Azure v oblasti v rámci předplatného | 10 | Ano |
| Digitální dvojčata | Počet vláken v instanci digitálních vláken Azure | 200 000 | Ano |
| Digitální dvojčata | Počet příchozích vztahů k jednomu vlákna | 5 000 | Ne |
| Digitální dvojčata | Počet odchozích relací z jednoho vlákna | 5 000 | Ne |
| Směrování | Počet koncových bodů pro jednu instanci digitálních vláken Azure | 6 | Ne |
| Směrování | Počet tras pro jednu instanci digitálních vláken Azure | 6 | Ano |
| Modely | Počet modelů v rámci jedné instance digitálního vlákna Azure | 10 000 | Ano |
| Modely | Počet modelů, které se dají nahrát v jednom volání rozhraní API | 250 | Ne |
| Modely | Počet položek vrácených na jednu stránku | 100 | Ne |
| Dotaz | Počet položek vrácených na jednu stránku | 100 | Ne |
| Dotaz | Počet `AND`  /  `OR` výrazů v dotazu | 50 | Ano |
| Dotaz | Počet položek pole v `IN`  /  `NOT IN` klauzuli | 50 | Ano |
| Dotaz | Počet znaků v dotazu | 8 000 | Ano |
| Dotaz | Počet `JOINS` v dotazu | 5 | Ano |

### <a name="rate-limits"></a>Omezení přenosové rychlosti

Tato tabulka odráží omezení přenosové rychlosti různých rozhraní API.

| Rozhraní API | Schopnost | Výchozí omezení | Měnitelný? |
| --- | --- | --- | --- |
| Rozhraní API modelů | Počet požadavků za sekundu | 100 | Ano |
| Rozhraní API pro digitální vlákna | Počet požadavků za sekundu | 1 000 | Ano |
| Rozhraní API pro dotazy | Počet požadavků za sekundu | 500 | Ano |
| Rozhraní API pro dotazy | Jednotky dotazů za sekundu | 4 000 | Ano |
| Rozhraní API pro trasy událostí | Počet požadavků za sekundu | 100 | Ano |

### <a name="other-limits"></a>Další omezení

Omezení datových typů a polí v dokumentech DTDL pro modely digitálních vláken Azure je možné najít v dokumentaci k specifikaci v GitHubu: [*Digital NeDTDL Definition Language () – verze 2*](https://github.com/Azure/opendigitaltwins-dtdl/blob/master/DTDL/v2/dtdlv2.md).
 
Podrobnosti latence dotazu a další pokyny k zápisu dotazů ve verzi Preview najdete v tématu [*Postupy: vytvoření dotazu na vydaný graf*](../articles/digital-twins/how-to-query-graph.md).