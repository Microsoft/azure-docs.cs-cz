---
author: baanders
description: zahrnout soubor pro omezení digitálních vláken Azure
ms.service: digital-twins
ms.topic: include
ms.date: 6/9/2020
ms.author: baanders
ms.openlocfilehash: 21d910fb0e0992b35aa19ce65fc216734e30265c
ms.sourcegitcommit: 3543d3b4f6c6f496d22ea5f97d8cd2700ac9a481
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/20/2020
ms.locfileid: "86515850"
---
### <a name="functional-limits"></a>Funkční omezení

Následující tabulka uvádí funkční omezení digitálních vláken Azure v aktuální verzi Preview.

| Oblast | Schopnost | Výchozí omezení | Měnitelný? |
| --- | --- | --- | --- |
| Prostředek Azure | Počet instancí digitálních vláken Azure v oblasti v rámci předplatného | 10 | Ano |
| Digitální vlákna | Počet vláken v instanci digitálních vláken Azure | 200 000 | Ano |
| Směrování | Počet koncových bodů pro jednu instanci digitálních vláken Azure | 6 | No |
| Směrování | Počet tras pro jednu instanci digitálních vláken Azure | 6 | Ano |
| Modely | Počet modelů v rámci jedné instance digitálního vlákna Azure | 10 000 | Ano |
| Modely | Počet modelů, které se dají nahrát v jednom volání rozhraní API | 250 | No |
| Modely | Počet položek vrácených na jednu stránku | 100 | No |
| Dotazy | Počet položek vrácených na jednu stránku | 100 | No |
| Dotazy | Počet `AND`  /  `OR` výrazů v dotazu | 50 | Ano |
| Dotaz | Počet položek pole v `IN`  /  `NOT IN` klauzuli | 50 | Ano |
| Dotaz | Počet znaků v dotazu | 8 000 | Ano |
| Dotaz | Počet `JOINS` v dotazu | 1 | Ano |

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

Omezení datových typů a polí v dokumentech DTDL pro modely digitálních vláken Azure je možné najít v dokumentaci k specifikaci v GitHubu: [Digital NeDTDL Definition Language () – verze 2](https://github.com/Azure/opendigitaltwins-dtdl/blob/master/DTDL/v2/dtdlv2.md).
 
Podrobnosti latence dotazu a další pokyny k zápisu dotazů ve verzi Preview najdete v tématu [*Postupy: vytvoření dotazu na vydaný graf*](../articles/digital-twins/how-to-query-graph.md).