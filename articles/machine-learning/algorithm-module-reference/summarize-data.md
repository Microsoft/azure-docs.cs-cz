---
title: Sumarizace dat
titleSuffix: Azure Machine Learning
description: Naučte se používat modul shrnutí dat v Azure Machine Learning k vygenerování základní sestavy statistiky pro sloupce v datové sadě.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: likebupt
ms.author: keli19
ms.date: 09/09/2019
ms.openlocfilehash: c8051126fc4a895c6e72e90942fac65d777afd8e
ms.sourcegitcommit: 87781a4207c25c4831421c7309c03fce5fb5793f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/23/2020
ms.locfileid: "76546482"
---
# <a name="summarize-data"></a>Sumarizace dat

Tento článek popisuje modul návrháře Azure Machine Learning (Preview).

Modul shrnutí dat slouží k vytvoření sady standardních statistických měr, které popisují jednotlivé sloupce ve vstupní tabulce.

Souhrnná statistika je užitečná, když chcete pochopit vlastnosti kompletní datové sady. Můžete například potřebovat znát:

- Kolik chybějících hodnot je v každém sloupci?
- Kolik jedinečných hodnot ve sloupci funkce existuje?
- Jaká je střední a směrodatná odchylka pro každý sloupec?

Modul vypočítá důležité skóre pro jednotlivé sloupce a vrátí řádek souhrnných statistik pro každou proměnnou (datový sloupec), který je zadaný jako vstup.

## <a name="how-to-configure-summarize-data"></a>Jak konfigurovat souhrnná data  

1. Přidejte modul **Shrnutí dat** do vašeho kanálu. Tento modul můžete najít v kategorii **statistické funkce** v návrháři.

1. Připojte datovou sadu, pro kterou chcete vygenerovat sestavu.

    Pokud chcete vytvořit sestavu pouze pro některé sloupce, použijte modul [Výběr sloupců v datové sadě](select-columns-in-dataset.md) k vytvoření podmnožiny sloupců, se kterými chcete pracovat.

1. Nejsou vyžadovány žádné další parametry. Ve výchozím nastavení modul analyzuje všechny sloupce, které jsou zadány jako vstup, a v závislosti na typu hodnot ve sloupcích vypíše relevantní sadu statistik, jak je popsáno v části [výsledky](#results) .

1. Spuštění kanálu

## <a name="results"></a>Výsledky

Sestava z modulu může zahrnovat následující statistiky. 

|Název sloupce|Popis|
|------|------|  
|**Funkce**|Název sloupce|
|**Výpočtu**|Počet všech řádků|
|**Počet jedinečných hodnot**|Počet jedinečných hodnot ve sloupci|
|**Chybějící počet hodnot**|Počet jedinečných hodnot ve sloupci|
|**min**|Nejnižší hodnota ve sloupci|  
|**Max**|Nejvyšší hodnota ve sloupci|
|**Průměrná**|Střední hodnota všech hodnot sloupců|
|**Střední odchylka**|Střední odchylka hodnot sloupců|
|**1. kvartil**|Hodnota při první kvartil|
|**Medián**|Hodnota sloupce mediánu|
|**třetí kvartil**|Hodnota při třetí kvartil|
|**Režim**|Režim hodnot sloupců|
|**Rozsah**|Celé číslo představující počet hodnot mezi maximální a minimální hodnotou|
|**Odchylka vzorku**|Variance pro sloupec; Viz Poznámka|
|**Vzorová směrodatná odchylka**|Směrodatná odchylka pro sloupec; Viz Poznámka|
|**Dezkosení vzorku**|Zešikmení sloupce; Viz Poznámka|
|**Ukázka špičatosti**|Špičatost pro sloupec; Viz Poznámka|
|**P 0,5**|0,5% percentil|
|**P1**|1% percentil|
|**P5**|5% percentil|
|**P95**|95% percentil|
|**P 99,5**|99,5% percentil |

## <a name="next-steps"></a>Další kroky

Podívejte se na [sadu modulů, které jsou k dispozici](module-reference.md) pro Azure Machine Learning.  
