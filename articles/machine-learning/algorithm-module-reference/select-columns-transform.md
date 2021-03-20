---
title: 'Vybrat sloupce transformace: odkaz na modul'
titleSuffix: Azure Machine Learning
description: Naučte se používat transformační modul Select Columns v Návrháři Azure Machine Learning k provedení transformace SELECT.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: likebupt
ms.author: keli19
ms.date: 10/10/2020
ms.openlocfilehash: f03840e55366d7f105ca4b57bd60061c82833e72
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2021
ms.locfileid: "93420712"
---
# <a name="select-columns-transform"></a>Transformace pro výběr sloupců

Tento článek popisuje, jak použít modul pro výběr sloupců transformace v Návrháři Azure Machine Learning. Účelem modulu transformace sloupce Select je zajistit, aby se v operacích navazujícího strojového učení používala předvídatelná a konzistentní sada sloupců.

Tento modul je užitečný pro úlohy, jako je například bodování, které vyžadují konkrétní sloupce. Změny v dostupných sloupcích můžou kanál přerušit nebo změnit výsledky.

Pomocí transformace vybrat sloupce můžete vytvořit a uložit sadu sloupců. Pak použijte modul [použít transformaci](apply-transformation.md) k použití těchto výběrů pro nová data.

## <a name="how-to-use-select-columns-transform"></a>Jak použít transformaci Select Columns

V tomto scénáři se předpokládá, že chcete pomocí výběru funkcí vygenerovat dynamickou sadu sloupců, které se použijí pro školení modelu. Chcete-li zajistit, aby výběry sloupců byly pro proces bodování stejné, použijte modul Výběr sloupců transformace k zachycení výběrů sloupců a použijte je jinde v kanálu.

1. Přidejte vstupní datovou sadu do kanálu v návrháři.

2. Přidejte instanci [výběru funkcí založenou na filtrech](filter-based-feature-selection.md).

3. Propojte moduly a nakonfigurujte modul pro výběr funkcí tak, aby automaticky hledal počet nejlepších funkcí ve vstupní datové sadě.

4. Přidejte instanci [výukového modelu](train-model.md) a jako vstup pro školení použijte výstup [výběru funkcí založeného na filtrech](filter-based-feature-selection.md) .

    > [!IMPORTANT]
    > Vzhledem k tomu, že důležitost funkcí je založena na hodnotách ve sloupci, nemůžete předem zjistit, které sloupce mohou být k dispozici pro vstup do [modelu výuky](train-model.md).  
5. Připojte instanci modulu výběr sloupců transformace. 

    Tento krok vygeneruje výběr sloupce jako transformaci, kterou lze uložit nebo použít pro jiné datové sady. Tento krok zajistí, že sloupce identifikované v výběru funkcí budou uloženy pro jiné moduly pro opakované použití.

6. Přidejte modul určení [skóre modelu](score-model.md) . 

   *Nepřipojujte vstupní datovou sadu.* Místo toho přidejte modul [použít transformaci](apply-transformation.md) a připojte výstup transformace výběru funkce.

   Struktura kanálu by měla vypadat nějak takto:

   > [!div class="mx-imgBorder"]
   > ![Vzorový kanál](media/module/filter-based-feature-selection-score.png)

   > [!IMPORTANT]
   > Pro datovou sadu bodování nelze očekávat použití [výběru funkcí založených na filtrech](filter-based-feature-selection.md) a získat stejné výsledky. Vzhledem k tomu, že výběr funkcí je založený na hodnotách, může zvolit jinou sadu sloupců, což by způsobilo selhání operace bodování.
    
7. Odešlete kanál.

Tento proces ukládání a následného výběru sloupce zajišťuje, aby bylo pro účely školení a bodování dostupné stejné schéma dat.


## <a name="next-steps"></a>Další kroky

Podívejte se na [sadu modulů, které jsou k dispozici](module-reference.md) pro Azure Machine Learning. 
