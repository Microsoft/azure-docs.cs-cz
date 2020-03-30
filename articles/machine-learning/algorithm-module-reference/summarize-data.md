---
title: Sumarizace dat
titleSuffix: Azure Machine Learning
description: Zjistěte, jak pomocí modulu Sumarizovat data v Azure Machine Learning generovat základní popisné statistiky sestavy pro sloupce v datové sadě.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: likebupt
ms.author: keli19
ms.date: 01/27/2020
ms.openlocfilehash: b0def12582dd3795e1b17334406e28d77c3c5656
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "79477438"
---
# <a name="summarize-data"></a>Sumarizace dat

Tento článek popisuje modul Návrháře Azure Machine Learning (preview).

Pomocí modulu Sumarizovat data vytvořte sadu standardních statistických měr, které popisují každý sloupec ve vstupní tabulce.

Souhrnné statistiky jsou užitečné, pokud chcete porozumět charakteristikám úplné datové sady. Můžete například potřebovat vědět:

- Kolik chybějících hodnot je v každém sloupci?
- Kolik jedinečných hodnot je ve sloupci prvku?
- Jaká je střední hodnota a směrodatná odchylka pro každý sloupec?

Modul vypočítá důležité skóre pro každý sloupec a vrátí řádek souhrnné statistiky pro každou proměnnou (sloupec dat) zadaný jako vstup.

## <a name="how-to-configure-summarize-data"></a>Konfigurace souhrnu dat  

1. Přidejte do kanálu modul **Sumarizovat data.** Tento modul naleznete v kategorii **Statistické funkce** v návrháři.

1. Připojte datovou sadu, pro kterou chcete generovat sestavu.

    Pokud chcete vykazovat pouze některé sloupce, použijte modul [Vybrat sloupce v datové sadě](select-columns-in-dataset.md) k promítání podmnožiny sloupců, se kterými chcete pracovat.

1. Nejsou vyžadovány žádné další parametry. Ve výchozím nastavení modul analyzuje všechny sloupce, které jsou k dispozici jako vstup a v závislosti na typu hodnot ve sloupcích, výstupy relevantní sadu statistik, jak je popsáno v části [Výsledky.](#results)

1. Odešlete potrubí.

## <a name="results"></a>Výsledky

Sestava z modulu může obsahovat následující statistiky. 

|Název sloupce|Popis|
|------|------|  
|**Funkce**|Název sloupce|
|**Počet**|Počet všech řádků|
|**Počet jedinečných hodnot**|Počet jedinečných hodnot ve sloupci|
|**Chybějící počet hodnot**|Počet jedinečných hodnot ve sloupci|
|**Min**|Nejnižší hodnota ve sloupci|  
|**Max**|Nejvyšší hodnota ve sloupci|
|**Znamená**|Průměr všech hodnot sloupců|
|**Průměrná odchylka**|Průměrná odchylka hodnot sloupců|
|**1. kvartil**|Hodnota na prvním kvartilu|
|**Medián**|Medián hodnoty sloupce|
|**3. Kvartil**|Hodnota ve třetím kvartilu|
|**Mode**|Režim hodnot sloupců|
|**Rozsah**|Celé číslo představující počet hodnot mezi maximální a minimální hodnotou|
|**Odchylka vzorku**|Odchylka pro sloupec; viz Poznámka|
|**Směrodatná odchylka vzorku**|Směrodatná odchylka pro kolonu; viz Poznámka|
|**Šikmost vzorku**|Šikmost pro sloupec; viz Poznámka|
|**Ukázka špičatost**|Špičatost pro sloupec; viz Poznámka|
|**P0,5**|0,5% percentil|
|**P1**|1% percentil|
|**P5**|5% percentil|
|**P95**|95% percentil|
|**P99,5**|99,5% percentil |

## <a name="technical-notes"></a>Technické poznámky

- Pro nečíselné sloupce jsou vypočteny pouze hodnoty count, Unique value count a Missing value count. Pro ostatní statistiky je vrácena hodnota null.

- Sloupce obsahující logické hodnoty jsou zpracovány pomocí těchto pravidel:

    - Při výpočtu Min se použije logické and.
    
    - Při výpočtu Maxe se použije logická hodnota OR
    
    - Při výpočtu rozsahu modul nejprve zkontroluje, zda se počet jedinečných hodnot ve sloupci rovná 2.
    
    - Při výpočtu jakékoli statistiky, která vyžaduje výpočty s plovoucí desetinnou desetinnou desetinnou desetinnou hodnotou, jsou hodnoty True považovány za 1,0 a hodnoty False jsou považovány za 0,0.

## <a name="next-steps"></a>Další kroky

Podívejte se na [sadu modulů, které jsou k dispozici](module-reference.md) pro Azure Machine Learning.  
