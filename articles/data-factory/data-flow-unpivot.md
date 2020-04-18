---
title: Mapování transformace unpivot toku dat
description: Transformace unpivot mapování datové továrny Azure
author: kromerm
ms.author: makromer
ms.reviewer: douglasl
ms.service: data-factory
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 01/30/2019
ms.openlocfilehash: c3e769334beb6a5739eebb8d7e8dc370533c2dc6
ms.sourcegitcommit: 5e49f45571aeb1232a3e0bd44725cc17c06d1452
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/17/2020
ms.locfileid: "81606247"
---
# <a name="azure-data-factory-unpivot-transformation"></a>Transformace unpivot Azure Data Factory

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

Použijte unpivot v toku dat mapování ADF jako způsob, jak přeměnit nenormalizovanou datovou sadu na více normalizovanou verzi rozbalením hodnot z více sloupců v jednom záznamu do více záznamů se stejnými hodnotami v jednom sloupci.

![Unpivot transformace](media/data-flow/unpivot1.png "Možnosti zrušení kontingenčního nastavení 1")

## <a name="ungroup-by"></a>Oddělení podle

![Unpivot transformace](media/data-flow/unpivot5.png "Možnosti zrušení kontingenčního nastavení 2")

Nejprve nastavte sloupce, podle kterých chcete seskupit pro pivot agregaci. Nastavte jeden nebo více sloupců pro oddělení se znaménkem + vedle seznamu sloupců.

## <a name="unpivot-key"></a>Zrušit kontingenční klíč

![Unpivot transformace](media/data-flow/unpivot6.png "Možnosti zrušení kontingenčního nastavení 3")

Kontingenční klávesa je sloupec, ve které se adf přenese z řádku na sloupec. Ve výchozím nastavení se každá jedinečná hodnota v datové sadě pro toto pole překloní ke sloupci. Volitelně však můžete zadat hodnoty z datové sady, kterou chcete přejít k hodnotám sloupců.

## <a name="unpivoted-columns"></a>Neotálné sloupce

![Unpivot transformace](media/data-flow//unpivot7.png "Možnosti zrušení kontingenčního nastavení 4")

Nakonec zvolte agregaci, kterou chcete použít pro otočné hodnoty a jak chcete, aby se sloupce zobrazily v nové výstupní projekci z transformace.

(Nepovinné) Vzor pojmenování můžete nastavit s předponou, prostřední a příponou, která má být přidána ke každému novému názvu sloupce z hodnot řádků.

Například pivoting "Prodej" podle "Region" by jednoduše dát nové hodnoty sloupců z každé prodejní hodnoty. Například: "25", "50", "1000", ... Pokud však nastavíte hodnotu předpony "Prodej", bude předponou "Prodej" k hodnotám.

<img src="media/data-flow/unpivot3.png" width="400">

Nastavení uspořádání sloupců na hodnotu Normální seskupí všechny otočné sloupce s jejich agregovanými hodnotami. Nastavení uspořádání sloupců na "Laterální" se bude střídat mezi sloupcem a hodnotou.

![Unpivot transformace](media/data-flow//unpivot7.png "Možnosti zrušení kontingenčního nastavení 5")

Konečná sada výsledků nekontingenčních dat zobrazuje součty sloupců, které jsou nyní nekontovány do samostatných hodnot řádků.

## <a name="next-steps"></a>Další kroky

Transformace [pivotu](data-flow-pivot.md) slouží k přemění řádků ke sloupcům.
