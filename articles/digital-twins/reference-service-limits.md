---
title: Omezení služby ve verzi Public Preview
titleSuffix: Azure Digital Twins
description: Graf znázorňující omezení služby digitálních vláken Azure v rámci verze Public Preview.
author: baanders
ms.author: baanders
ms.date: 05/05/2020
ms.topic: article
ms.service: digital-twins
ROBOTS: NOINDEX, NOFOLLOW
ms.openlocfilehash: 7847964d77e5dc09d2e09f207c47f9504c48e1db
ms.sourcegitcommit: c4ad4ba9c9aaed81dfab9ca2cc744930abd91298
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/12/2020
ms.locfileid: "84729196"
---
# <a name="service-limits-in-public-preview"></a>Omezení služby ve verzi Public Preview

[!INCLUDE [Azure Digital Twins current preview status](../../includes/digital-twins-preview-status.md)]

Toto jsou omezení služeb digitálních vláken Azure v rámci verze Public Preview.

> [!NOTE]
> Některé služby mají nastavitelná omezení reprezentovaná v tabulkách níže a *upravitelný sloupec?* . Pokud je možné limit upravit, je hodnota *seřiditelná?* *Ano*.
>
> Pokud vaše firma vyžaduje zvýšení limitu nebo kvóty nad rámec výchozího omezení, můžete požádat o další prostředky [otevřením lístku podpory](https://ms.portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest).

## <a name="functional-limits"></a>Funkční omezení

Následující tabulka uvádí funkční omezení digitálních vláken Azure v aktuální verzi Preview.

| Oblast | Schopnost | Omezení | Měnitelný? |
| --- | --- | --- | --- |
| Prostředek Azure | Počet instancí digitálních vláken Azure v oblasti v rámci předplatného | 10 | Ano |
| Digitální vlákna | Počet vláken v instanci digitálních vláken Azure | 200 000 | Ano |
| Směrování | Počet koncových bodů pro jednu instanci digitálních vláken Azure | 6 | Ne |
| Směrování | Počet tras pro jednu instanci digitálních vláken Azure | 6 | Ano |
| Modely | Počet modelů v rámci jedné instance digitálního vlákna Azure | 10 000 | Ano |
| Modely | Počet modelů, které se dají nahrát v jednom volání rozhraní API | 250 | Ne |
| Modely | Počet položek vrácených na jednu stránku | 100 | Ne |
| Dotaz | Počet položek vrácených na jednu stránku | 100 | Ne |
| Dotaz | Počet `AND`  /  `OR` výrazů v dotazu | 50 | Ano |
| Dotaz | Počet položek pole v `IN`  /  `NOT IN` klauzuli | 50 | Ano |
| Dotaz | Počet znaků v dotazu | 8 000 | Ano |
| Dotaz | Počet `JOINS` v dotazu | 1 | Ano |

## <a name="rate-limits"></a>Omezení rychlosti

Tato tabulka odráží omezení přenosové rychlosti různých rozhraní API.

| Rozhraní API | Schopnost | Omezení | Měnitelný? |
| --- | --- | --- | --- |
| Rozhraní API modelů | Počet požadavků za sekundu | 100 | Ano |
| Rozhraní API pro digitální vlákna | Počet požadavků za sekundu | 1 000 | Ano |
| Rozhraní API pro dotazy | Počet požadavků za sekundu | 500 | Ano |
| Rozhraní API pro dotazy | Jednotky dotazů za sekundu | 4 000 | Ano |
| Rozhraní API pro trasy událostí | Počet požadavků za sekundu | 100 | Ano |

## <a name="other-limits"></a>Další omezení

Omezení datových typů a polí v dokumentech DTDL pro modely digitálních vláken Azure je možné najít v dokumentaci k specifikaci v GitHubu: [Digital NeDTDL Definition Language () – verze 2](https://github.com/Azure/opendigitaltwins-dtdl/blob/master/DTDL/v2/dtdlv2.md).
 
Podrobnosti latence dotazu a další pokyny k zápisu dotazů ve verzi Preview najdete v tématu [Postupy: vytvoření dotazu na vydaný graf](how-to-query-graph.md).

## <a name="next-steps"></a>Další kroky

Přečtěte si další informace o aktuální verzi Preview digitálních vláken Azure v přehledu služby:
* [Přehled: co je Azure Digital revláken?](overview.md)
