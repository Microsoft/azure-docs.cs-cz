---
title: 'Vybrat sloupce transformace: odkaz na modul'
titleSuffix: Azure Machine Learning service
description: Naučte se, jak pomocí modulu výběr sloupců transformace ve službě Azure Machine Learning vytvořit transformaci, která vybere stejnou podmnožinu sloupců jako v dané datové sadě.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: likebupt
ms.author: keli19
ms.date: 10/10/2019
ms.openlocfilehash: def15b3c28b2278f884b36acc39fa75982fcf8c5
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/04/2019
ms.locfileid: "73516198"
---
# <a name="select-columns-transform"></a>Transformovat sloupce pro výběr

Tento článek popisuje, jak použít modul pro **Výběr sloupců transformace** v Návrháři Azure Machine Learning (Preview). Účelem modulu **transformace sloupce Select** je zajistit, aby se v operacích navazujícího strojového učení vždy používala předvídatelná a konzistentní sada sloupců.

Tento modul je užitečný pro úlohy, jako je například bodování, které vyžadují konkrétní sloupce. Změny v dostupných sloupcích můžou kanál přerušit nebo změnit výsledky.

Pomocí **transformace vybrat sloupce** můžete vytvořit a uložit sadu sloupců. Pak použijte modul [použít transformaci](apply-transformation.md) k použití těchto výběrů pro nová data.

## <a name="how-to-use-select-columns-transform"></a>Jak použít transformaci Select Columns

V tomto scénáři se předpokládá, že máte v úmyslu použít výběr funkcí k vygenerování dynamické sady sloupců, které se použijí pro školení modelu. Chcete-li zajistit, aby výběry sloupců byly pro proces bodování stejné, použijte modul **Výběr sloupců transformace** k zachycení výběrů sloupců a použijte je jinde v kanálu.

1. Přidejte vstupní datovou sadu do kanálu v návrháři.

2. Přidejte instanci [výběru funkcí založenou na filtrech](filter-based-feature-selection.md).

3. Připojte moduly a nakonfigurujte modul pro výběr funkcí tak, aby automaticky hledal určitý počet nejlepších funkcí ve vstupní datové sadě.

4. Přidejte instanci [výukového modelu](train-model.md) a jako vstup pro školení použijte výstup [výběru funkcí založeného na filtrech](filter-based-feature-selection.md) .

    > [!IMPORTANT]
    > Vzhledem k tomu, že důležitost funkcí se rozhoduje na základě hodnot ve sloupci, nemůžete předem zjistit, které sloupce mohou být k dispozici pro vstup do [modelu výuky](train-model.md).  
5. Nyní připojte instanci **transformačního modulu výběr sloupců** . 

    Tím se vygeneruje výběr sloupce jako transformace, která se dá uložit nebo použít pro jiné datové sady. Tento krok zajistí, že sloupce identifikované výběrem funkce budou uloženy pro opakované použití jinými moduly.

6. Přidejte modul určení [skóre modelu](score-model.md) . 

    **Nepřipojujte vstupní datovou sadu.**

    Místo toho přidejte modul [použít transformaci](apply-transformation.md) a připojte výstup transformace výběru funkce.

   > [!IMPORTANT]
   > Pro datovou sadu bodování nelze očekávat použití [výběru funkcí založených na filtrech](filter-based-feature-selection.md) a získat stejné výsledky. Vzhledem k tomu, že výběr funkcí je založený na hodnotách, může zvolit jinou sadu sloupců, což by způsobilo selhání operace bodování.
7. Spuštění kanálu

Tento proces ukládání a následného výběru sloupce zajišťuje, aby bylo pro účely školení a bodování dostupné stejné schéma dat.


## <a name="next-steps"></a>Další kroky

Podívejte se na [sadu modulů, které jsou k dispozici](module-reference.md) pro Azure Machine Learning služby. 
