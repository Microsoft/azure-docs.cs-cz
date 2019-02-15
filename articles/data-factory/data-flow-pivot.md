---
title: Azure Data Factory mapování transformace Pivot toku dat
description: Azure Data Factory mapování transformace Pivot toku dat
author: kromerm
ms.author: makromer
ms.reviewer: douglasl
ms.service: data-factory
ms.topic: conceptual
ms.date: 01/30/2019
ms.openlocfilehash: 86404f4eb2eb2de4243c6bb725f4292e7b560d18
ms.sourcegitcommit: f715dcc29873aeae40110a1803294a122dfb4c6a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/14/2019
ms.locfileid: "56271739"
---
# <a name="azure-data-factory-mapping-data-flow-pivot-transformation"></a>Azure Data Factory mapování transformace Pivot toku dat

[!INCLUDE [notes](../../includes/data-factory-data-flow-preview.md)]

Použijte Pivotu v toku dat ADF jako agregace kde má jeden nebo více sloupců seskupení jeho hodnoty jedinečných řádků transformuje na jednotlivé sloupce. V podstatě můžete otáčení hodnoty řádků do nového sloupce (dat čerpají metadata).

![Otáčení možnosti](media/data-flow/pivot1.png "otáčení 1")

## <a name="group-by"></a>Seskupit podle

![Otáčení možnosti](media/data-flow/pivot2.png "otáčení 2")

Nejprve nastavte sloupce, které chcete seskupit podle vaší pivot agregace. Můžete nastavit více než 1 sloupec tady se + znaménko vedle seznamu sloupců.

## <a name="pivot-key"></a>Klíče kontingenční tabulky

![Otáčení možnosti](media/data-flow/pivot3.png "otáčení 3")

Klíče kontingenční tabulky je sloupec, který bude ADF otáčení z řádku na sloupec. Ve výchozím nastavení bude otáčení každou jedinečnou hodnotu v datové sadě pro toto pole na sloupec. Však můžete volitelně zadat hodnoty z datové sady, kterou chcete přenechte obranu hodnot sloupců.

## <a name="pivoted-columns"></a>Převedený sloupce

![Otáčení možnosti](media/data-flow/pivot4.png "otáčení 4")

A konečně zvolíte agregaci, kterou chcete použít pro pivotovaná hodnoty a jak byste chtěli sloupce, který se má zobrazit v nové projekce výstup z transformace.

(Volitelné) Můžete nastavit vzoru pro pojmenovávání s předponou, střední a příponu je třeba přidat do každého nový název sloupce z hodnoty řádků.

Například "Oblasti" Přesun "Prodeje" výsledkem by byla hodnoty nového sloupce od každé hodnoty prodeje, tj "25", "50", "1000" atd. Ale pokud nastavíte hodnotu předpony "Prodeje" 

![Otáčení možnosti](media/data-flow/pivot5.png "otáčení 5")

Nastavení uspořádání sloupců "Normální" seskupí dohromady pivotovaná sloupce s jejich agregované hodnoty. Mezi sloupci a hodnota se alternativní nastavení uspořádání sloupců na "Laterální".

### <a name="aggregation"></a>Agregace

Nastavit agregaci, kterou chcete použít pro pivot hodnoty, klikněte na pole v dolní části podokna neseskupené sloupce. Zadáte do Tvůrce toku dat ADF kde agregační výraz sestavit a zadejte alias popisný název vaší nové agregované hodnoty.

Jazyk výrazů tok ADF Data použít k podrobnému popisu transformace převedený sloupec v Tvůrce: https://aka.ms/dataflowexpressions.

### <a name="how-to-rejoin-original-fields"></a>Jak se znovu připojit původní pole
> [!NOTE]
> Transformace Pivot bude pouze projektu sloupci používanými v agregaci, seskupení a otáčení akce. Pokud budete chtít zahrnout ostatní sloupce z předchozího kroku do vašeho toku, použít novou větev z předchozího kroku a použití vzoru spojení sama na sebe na připojení toku s původní metadata
