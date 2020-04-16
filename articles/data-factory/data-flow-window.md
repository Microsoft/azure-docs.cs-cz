---
title: Mapování transformace okna toku dat
description: Transformace okna mapování dat Azure Data Factory
author: kromerm
ms.author: makromer
ms.reviewer: douglasl
ms.service: data-factory
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 01/30/2019
ms.openlocfilehash: 67e966e3b3dd274e993797ed37e17c5490d632c0
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/16/2020
ms.locfileid: "81416491"
---
# <a name="azure-data-factory-window-transformation"></a>Transformace okna Azure Data Factory

[!INCLUDE[appliesto-adf-xxx-md](includes/appliesto-adf-xxx-md.md)]

Window transformace je místo, kde budete definovat agregace na základě okna sloupců v datových proudech. V Tvůrce výrazů můžete definovat různé typy agregací, které jsou založeny na datech nebo časových oknech (klauzule SQL OVER), jako je olovo, mas, ntile, cumedist, rank atd.). Nové pole bude generováno ve výstupu, který zahrnuje tyto agregace. Můžete také zahrnout volitelná pole podle skupiny.

![Možnosti okna](media/data-flow/windows1.png "windows 1")

## <a name="over"></a>Přes
Nastavte rozdělení dat sloupce pro transformaci okna. Ekvivalent SQL je ```Partition By``` v Over klauzule v SQL. Pokud chcete vytvořit výpočet nebo vytvořit výraz pro dělení, můžete to udělat tak, že najedete ukazatelem na název sloupce a vyberte "vypočítaný sloupec".

![Možnosti okna](media/data-flow/windows4.png "windows 4")

## <a name="sort"></a>Seřadit
Další část Over klauzule ```Order By```je nastavení . Tím nastavíte řazení dat. Můžete také vytvořit výraz pro hodnotu výpočtu v tomto sloupcovém poli pro řazení.

![Možnosti okna](media/data-flow/windows5.png "windows 5")

## <a name="range-by"></a>Rozsah podle
Dále nastavte rámeček okna jako Neomezený nebo Ohraničený. Chcete-li nastavit neohraničený rámeček okna, nastavte jezdec na Neohraničený na obou koncích. Pokud zvolíte nastavení mezi Neomezený a Aktuální řádek, musíte nastavit počáteční a koncové hodnoty Posun. Obě hodnoty budou kladná celá čísla. Můžete použít buď relativní čísla nebo hodnoty z vašich dat.

Jezdec okna má nastavené dvě hodnoty: hodnoty před aktuálním řádkem a hodnoty za aktuálním řádkem. Odsazení Začátek a Konec odpovídá dvěma selektorům na posuvníku.

![Možnosti okna](media/data-flow/windows6.png "windows 6")

## <a name="window-columns"></a>Sloupce okna
Nakonec pomocí Tvůrce výrazů definujte agregace, které chcete použít s datovými okny, jako jsou RANK, COUNT, MIN, MAX, DENSE RANK, LEAD, Lag atd.

![Možnosti okna](media/data-flow/windows7.png "windows 7")

Úplný seznam agregačních a analytických funkcí, které jsou k dispozici v jazyce exprese https://aka.ms/dataflowexpressionstoku dat adf prostřednictvím Tvůrce výrazů, je uveden zde: .

## <a name="next-steps"></a>Další kroky

Pokud hledáte jednoduchou agregaci podle skupiny, použijte [agregační transformaci](data-flow-aggregate.md)
