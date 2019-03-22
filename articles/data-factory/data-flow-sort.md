---
title: Azure Data Factory mapování transformace řazení toku dat
description: Azure Data Factory mapování řazení transformace dat
author: kromerm
ms.author: makromer
ms.reviewer: douglasl
ms.service: data-factory
ms.topic: conceptual
ms.date: 10/08/2018
ms.openlocfilehash: d0482d1081c16dc89e7371c4c33de9b2bb4e4c2e
ms.sourcegitcommit: 2d0fb4f3fc8086d61e2d8e506d5c2b930ba525a7
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/18/2019
ms.locfileid: "57898750"
---
# <a name="azure-data-factory-data-flow-sort-transformations"></a>Azure Data Factory datového toku řazení transformace

[!INCLUDE [notes](../../includes/data-factory-data-flow-preview.md)]

![Nastavení řazení](media/data-flow/sort.png "řazení")

Transformace řazení umožňuje řazení příchozí řádků na aktuální datový proud. Odchozí řádky z transformace řazení se následně postupujte podle vámi nastavených pravidel řazení. Můžete vybrat jednotlivé sloupce a nebo je můžete seřadit ASC DEC, pomocí indikátorů šipky vedle jednotlivých polí. Pokud potřebujete změnit sloupec před použitím řazení, klikněte na "Vypočítaného sloupce", které chcete spustit editor výrazů. To poskytne příležitost k sestavení výrazu pro operace řazení namísto pouze implementovány sloupec pro řazení.

## <a name="case-insensitive"></a>Malá a velká písmena se nerozlišují.
Můžete zapnout "Písmen" Pokud chcete ignorovat velikost písmen při řazení pole řetězce nebo text.

"Řazení pouze v rámci oddílů" využívá, dělení dat Spark. Seřazením příchozí data pouze v rámci každého oddílu, můžete Data proudí seřadit dělená data místo řazení celého datového proudu.

Jednotlivé podmínky řazení v transformaci řazení lze vyjádřit. Proto pokud potřebujete přesunout sloupec vyšší prioritu řazení, řádku myší vzít a přesunout ho vyšší nebo nižší řazení seznamu.

Dělení dopady na řazení

Tok dat ADF provádí se ve velké objemy dat clustery Spark s daty distribuovanými napříč několika uzly a oddíly. Je důležité to vzít v úvahu při navrhování toku dat, pokud jsou v závislosti na řazení transformace uchovávat data v tomto stejné pořadí. Pokud budete chtít změnit rozdělení dat v následné transformace, můžete ztratit řazení kvůli této promísení data.

## <a name="next-steps"></a>Další postup

Po seřazení, můžete chtít použít [agregační transformace](data-flow-aggregate.md)
