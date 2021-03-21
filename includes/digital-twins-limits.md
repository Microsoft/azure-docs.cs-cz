---
author: baanders
description: zahrnout soubor pro omezení digitálních vláken Azure
ms.service: digital-twins
ms.topic: include
ms.date: 6/9/2020
ms.author: baanders
ms.openlocfilehash: 4a69b2ff15fc4857e9fb292d2f753aa68ed875d4
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2021
ms.locfileid: "100370089"
---
### <a name="functional-limits"></a>Funkční omezení

Následující tabulka uvádí funkční omezení digitálních vláken Azure. 

> [!TIP]
> Doporučení modelování pro provoz v těchto funkčních omezeních najdete v tématu [osvědčené postupy pro navrhování modelů](../articles/digital-twins/concepts-models.md#best-practices-for-designing-models).

| Plošný | Schopnost | Výchozí omezení | Měnitelný? |
| --- | --- | --- | --- |
| Prostředek Azure | Počet instancí digitálních vláken Azure v oblasti v rámci předplatného | 10 | Yes |
| Digitální dvojčata | Počet vláken v instanci digitálních vláken Azure | 200 000 | Yes |
| Digitální dvojčata | Počet příchozích vztahů k jednomu vlákna | 5 000 | No |
| Digitální dvojčata | Počet odchozích relací z jednoho vlákna | 5 000 | No |
| Digitální dvojčata | Maximální velikost (tělo JSON v žádosti o vložení nebo opravu) jednoho vlákna | 32 KB | No |
| Digitální dvojčata | Maximální velikost datové části požadavku | 32 KB | No | 
| Směrování | Počet koncových bodů pro jednu instanci digitálních vláken Azure | 6 | No |
| Směrování | Počet tras pro jednu instanci digitálních vláken Azure | 6 | Yes |
| Modely | Počet modelů v rámci jedné instance digitálního vlákna Azure | 10 000 | Yes |
| Modely | Počet modelů, které se dají nahrát v jednom volání rozhraní API | 250 | No |
| Modely | Maximální velikost (tělo JSON v žádosti PUT nebo PATCH) jednoho modelu | 1 MB | No |
| Modely | Počet položek vrácených na jednu stránku | 100 | No |
| Dotaz | Počet položek vrácených na jednu stránku | 100 | Ano |
| Dotaz | Počet `AND`  /  `OR` výrazů v dotazu | 50 | Ano |
| Dotaz | Počet položek pole v `IN`  /  `NOT IN` klauzuli | 50 | Ano |
| Dotaz | Počet znaků v dotazu | 8 000 | Ano |
| Dotaz | Počet `JOINS` v dotazu | 5 | Yes |

### <a name="rate-limits"></a>Omezení přenosové rychlosti

Následující tabulka odráží přenosová omezení různých rozhraní API.

| Rozhraní API | Schopnost | Výchozí omezení | Měnitelný? |
| --- | --- | --- | --- |
| Rozhraní API modelů | Počet požadavků za sekundu | 100 | Yes |
| Rozhraní API pro digitální vlákna | Počet požadavků za sekundu | 2 000 | Yes |
| Rozhraní API pro digitální vlákna | Počet operací vytvoření/odstranění za sekundu napříč **všemi zdvojenými a relacemi** | 50 | Yes |
| Rozhraní API pro digitální vlákna | Počet operací vytvoření/aktualizace/odstranění za sekundu pro **jednu dvojitou** nebo jeho relaci | 10 | No |
| Rozhraní API pro dotazy | Počet požadavků za sekundu | 500 | Yes |
| Rozhraní API pro dotazy | [Jednotky dotazů](../articles/digital-twins/concepts-query-units.md) za sekundu | 4 000 | Yes |
| Rozhraní API pro trasy událostí | Počet požadavků za sekundu | 100 | Yes |

### <a name="other-limits"></a>Další omezení

Omezení datových typů a polí v dokumentech DTDL pro modely digitálních vláken Azure je možné najít v dokumentaci k specifikaci v GitHubu: [*Digital NeDTDL Definition Language () – verze 2*](https://github.com/Azure/opendigitaltwins-dtdl/blob/master/DTDL/v2/dtdlv2.md).
 
Podrobnosti latence dotazu a další omezení dotazů najdete v tématu [*Postupy: dotazování na nevlákenný graf*](../articles/digital-twins/how-to-query-graph.md).
