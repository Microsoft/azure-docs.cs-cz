---
author: baanders
description: zahrnout soubor pro omezení digitálních vláken Azure
ms.service: digital-twins
ms.topic: include
ms.date: 6/9/2020
ms.author: baanders
ms.openlocfilehash: 3035bd71a91f7cad6fb951d74081b77d8445a81f
ms.sourcegitcommit: 0e8a4671aa3f5a9a54231fea48bcfb432a1e528c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/24/2020
ms.locfileid: "87133798"
---
### <a name="functional-limits"></a>Funkční omezení

Následující tabulka uvádí funkční omezení digitálních vláken Azure v aktuální verzi Preview.

| Oblast | Schopnost | Výchozí omezení | Měnitelný? |
| --- | --- | --- | --- |
| Prostředek Azure | Počet instancí digitálních vláken Azure v oblasti v rámci předplatného | 10 | Yes |
| Digitální vlákna | Počet vláken v instanci digitálních vláken Azure | 200 000 | Yes |
| Směrování | Počet koncových bodů pro jednu instanci digitálních vláken Azure | 6 | No |
| Směrování | Počet tras pro jednu instanci digitálních vláken Azure | 6 | Yes |
| Modely | Počet modelů v rámci jedné instance digitálního vlákna Azure | 10 000 | Yes |
| Modely | Počet modelů, které se dají nahrát v jednom volání rozhraní API | 250 | No |
| Modely | Počet položek vrácených na jednu stránku | 100 | No |
| Dotaz | Počet položek vrácených na jednu stránku | 100 | No |
| Dotaz | Počet `AND`  /  `OR` výrazů v dotazu | 50 | Ano |
| Dotaz | Počet položek pole v `IN`  /  `NOT IN` klauzuli | 50 | Ano |
| Dotaz | Počet znaků v dotazu | 8 000 | Ano |
| Dotaz | Počet `JOINS` v dotazu | 1 | Yes |

### <a name="rate-limits"></a>Omezení přenosové rychlosti

Tato tabulka odráží omezení přenosové rychlosti různých rozhraní API.

| Rozhraní API | Schopnost | Výchozí omezení | Měnitelný? |
| --- | --- | --- | --- |
| Rozhraní API modelů | Počet požadavků za sekundu | 100 | Yes |
| Rozhraní API pro digitální vlákna | Počet požadavků za sekundu | 1 000 | Yes |
| Rozhraní API pro dotazy | Počet požadavků za sekundu | 500 | Yes |
| Rozhraní API pro dotazy | Jednotky dotazů za sekundu | 4 000 | Yes |
| Rozhraní API pro trasy událostí | Počet požadavků za sekundu | 100 | Yes |

### <a name="other-limits"></a>Další omezení

Omezení datových typů a polí v dokumentech DTDL pro modely digitálních vláken Azure je možné najít v dokumentaci k specifikaci v GitHubu: [*Digital NeDTDL Definition Language () – verze 2*](https://github.com/Azure/opendigitaltwins-dtdl/blob/master/DTDL/v2/dtdlv2.md).
 
Podrobnosti latence dotazu a další pokyny k zápisu dotazů ve verzi Preview najdete v tématu [*Postupy: vytvoření dotazu na vydaný graf*](../articles/digital-twins/how-to-query-graph.md).