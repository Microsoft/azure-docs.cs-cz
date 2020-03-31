---
title: 'Vybrat transformaci sloupců: Odkaz na modul'
titleSuffix: Azure Machine Learning
description: Zjistěte, jak pomocí modulu Vybrat sloupce transformace v Azure Machine Learning vytvořit transformaci, která vybere stejnou podmnožinu sloupců jako v dané datové sady.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: likebupt
ms.author: keli19
ms.date: 10/10/2019
ms.openlocfilehash: a5264c14294f84858cd489f5892b8cdd19e117d0
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "79455906"
---
# <a name="select-columns-transform"></a>Transformace pro výběr sloupců

Tento článek popisuje, jak používat modul Vyberte sloupce transformace v návrháři Azure Machine Learning (preview). Účelem modulu Select Columns Transform je zajistit, aby se v následných operacích strojového učení používala předvídatelná a konzistentní sada sloupců.

Tento modul je užitečný pro úkoly, jako je například vyhodnocování, které vyžadují určité sloupce. Změny v dostupných sloupcích mohou přerušit kanál nebo změnit výsledky.

Pomocí příkazu Vybrat transformaci sloupců můžete vytvořit a uložit sadu sloupců. Potom použijte [modul Použít transformaci](apply-transformation.md) k použití těchto výběrů na nová data.

## <a name="how-to-use-select-columns-transform"></a>Použití transformace vybraných sloupců

Tento scénář předpokládá, že chcete použít výběr funkce ke generování dynamické sady sloupců, které budou použity pro trénování modelu. Chcete-li zajistit, aby výběry sloupců byly pro proces vyhodnocování stejné, použijte modul Vybrat transformaci sloupců k zachycení výběru sloupců a jejich použití jinde v kanálu.

1. Přidejte vstupní datovou sadu do kanálu v návrháři.

2. Přidejte instanci [výběru funkcí založených na filtru](filter-based-feature-selection.md).

3. Připojte moduly a nakonfigurujte modul pro výběr funkcí tak, aby automaticky našel řadu nejlepších funkcí ve vstupní datové sadě.

4. Přidejte instanci [modelu vlaku](train-model.md) a použijte výstup [výběru funkcí založených na filtru](filter-based-feature-selection.md) jako vstup pro školení.

    > [!IMPORTANT]
    > Vzhledem k tomu, že důležitost funkce je založena na hodnotách ve sloupci, nemůžete předem vědět, které sloupce mohou být k dispozici pro vstup do [modelu vlaku](train-model.md).  
5. Připojte instanci modulu Vyberte sloupce transformace. 

    Tento krok generuje výběr sloupce jako transformaci, kterou lze uložit nebo použít na jiné datové sady. Tento krok zajistí, že sloupce uvedené ve výběru funkcí jsou uloženy pro další moduly k opakovanému použití.

6. Přidejte modul [Model skóre.](score-model.md) 

   *Nepřipojujte vstupní datovou sadu.* Místo toho přidejte modul [Použít transformaci](apply-transformation.md) a připojte výstup transformace výběru prvku.

   > [!IMPORTANT]
   > Nelze očekávat, že použít [filtr na základě funkce výběru](filter-based-feature-selection.md) vyhodnocování datové sady a získat stejné výsledky. Vzhledem k tomu, že výběr funkcí je založen na hodnotách, může zvolit jinou sadu sloupců, což by způsobilo selhání operace vyhodnocování.
7. Odešlete potrubí.

Tento proces ukládání a následnépoužití výběru sloupců zajišťuje, že stejné schéma dat je k dispozici pro školení a vyhodnocování.


## <a name="next-steps"></a>Další kroky

Podívejte se na [sadu modulů, které jsou k dispozici](module-reference.md) pro Azure Machine Learning. 
