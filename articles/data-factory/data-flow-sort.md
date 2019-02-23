---
title: Azure Data Factory datového toku řazení transformace
description: Azure Data Factory Data seřadit spojení transformace
author: kromerm
ms.author: makromer
ms.reviewer: douglasl
ms.service: data-factory
ms.topic: conceptual
ms.date: 10/08/2018
ms.openlocfilehash: 7adc8c80ecca12364fb7f761092b1a5d9f528e19
ms.sourcegitcommit: 90c6b63552f6b7f8efac7f5c375e77526841a678
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/23/2019
ms.locfileid: "56729759"
---
# <a name="azure-data-factory-data-sort-transformations"></a>Transformace řazení dat Azure Data Factory

[!INCLUDE [notes](../../includes/data-factory-data-flow-preview.md)]

![Nastavení řazení](media/data-flow/sort.png "řazení")

Transformace řazení umožňuje řazení příchozí řádků na aktuální datový proud. Odchozí řádky z transformace řazení se následně postupujte podle vámi nastavených pravidel řazení. Můžete vybrat jednotlivé sloupce a nebo je můžete seřadit ASC DEC, pomocí indikátorů šipky vedle jednotlivých polí. Pokud potřebujete změnit sloupec před použitím řazení, klikněte na "Vypočítaného sloupce", které chcete spustit editor výrazů. To poskytne příležitost k sestavení výrazu pro operace řazení namísto pouze implementovány sloupec pro řazení.

Můžete zapnout "Písmen" Pokud chcete ignorovat velikost písmen při řazení pole řetězce nebo text.

"Řazení pouze v rámci oddílů" využívá, dělení dat Spark. Seřazením příchozí data pouze v rámci každého oddílu, můžete Data proudí seřadit dělená data místo řazení celého datového proudu.

Jednotlivé podmínky řazení v transformaci řazení lze vyjádřit. Proto pokud potřebujete přesunout sloupec vyšší prioritu řazení, řádku myší vzít a přesunout ho vyšší nebo nižší řazení seznamu.

Dělení dopady na řazení

Tok dat ADF provádí se ve velké objemy dat clustery Spark s daty distribuovanými napříč několika uzly a oddíly. Je důležité to vzít v úvahu při navrhování toku dat, pokud jsou v závislosti na řazení transformace uchovávat data v tomto stejné pořadí. Pokud budete chtít změnit rozdělení dat v následné transformace, můžete ztratit řazení kvůli této promísení data.
