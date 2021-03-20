---
title: Škálovatelnost a přizpůsobení výkonu
titleSuffix: Azure Cognitive Services
description: 'Vysoce výkonné a vysoce náročné weby a aplikace mají dva hlavní faktory, které je potřeba zvážit v oblasti škálovatelnosti a výkonu: propustnost a školení.'
services: cognitive-services
manager: nitinme
ms.service: cognitive-services
ms.subservice: personalizer
ms.topic: conceptual
ms.date: 10/24/2019
ms.openlocfilehash: 52d22fce64a26267e73cb9a1df8614129bb96c3e
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2021
ms.locfileid: "87132683"
---
# <a name="scalability-and-performance"></a>Škálovatelnost a výkon

Vysoce výkonné a vysoce náročné weby a aplikace mají dva hlavní faktory, které je potřeba zvážit s možností přizpůsobovat škálovatelnost a výkon:

* Udržování nízké latence při vytváření volání rozhraní API pořadí
* Zajištění zajištění propustnosti školení pomocí vstupu události

Individuální nastavení může rychle vracet pořadí a většinu doby trvání volání, která je vyhrazena pro komunikaci prostřednictvím REST API. Azure vám umožní rychle reagovat na požadavky.

##  <a name="low-latency-scenarios"></a>Scénáře s nízkou latencí

Některé aplikace vyžadují při vracení pořadí nízkou latenci. Jsou nezbytné nízké latence:

* Aby uživatel mohl před zobrazením hodnoceného obsahu čekat na znatelné množství času.
* Aby bylo možné zajistit, že server má extrémní provoz, vyhněte se tomu omezených výpočetním časem a síťovým připojením.


## <a name="scalability-and-training-throughput"></a>Škálovatelnost a propustnost školení

Přizpůsobení funguje tak, že aktualizuje model, který se překládá na základě zpráv odeslaných asynchronně pomocí přizpůsobeného rozhraní API pro řazení a odměnu. Tyto zprávy jsou odesílány pomocí služby Azure EventHub pro aplikaci.

 Nepravděpodobné, že většina aplikací dosáhne maximálního počtu připojení a školení k propustnosti přizpůsobeného. I když se toto maximum nezpomalí, může to znamenat, že se fronty centra událostí vyplňují rychleji, než se dají vyčistit.

## <a name="how-to-estimate-your-throughput-requirements"></a>Odhad požadavků na propustnost

* Odhadem průměrného počtu bajtů na událost řazení přidejte délky dokumentů JSON kontextu a akce.
* Rozdělte 20MB za sekundu za tento předpokládaný průměrný počet bajtů.

Pokud například vaše průměrná datová část obsahuje 500 funkcí a každá z nich má odhadované 20 znaků, pak je každá událost přibližně 10 KB. V těchto odhadech 20 000 000/10 000 = 2 000 události/s, což znamená o událostech 173 000 000 za den. 

Pokud dosáhnou těchto limitů, obraťte se na náš tým podpory, kde najdete doporučení pro architekturu.

## <a name="next-steps"></a>Další kroky

[Vytvořte a nakonfigurujte přizpůsobeného přizpůsobování](how-to-settings.md).