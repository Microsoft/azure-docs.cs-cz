---
title: Transformace okna toku dat Azure Data Factory mapování
description: Transformace okna toku dat Azure Data Factory mapování
author: kromerm
ms.author: makromer
ms.reviewer: douglasl
ms.service: data-factory
ms.topic: conceptual
ms.date: 01/30/2019
ms.openlocfilehash: 222f97afd4346b9e4980e41303aeb683f431cd68
ms.sourcegitcommit: bb65043d5e49b8af94bba0e96c36796987f5a2be
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/16/2019
ms.locfileid: "72387076"
---
# <a name="azure-data-factory-window-transformation"></a>Transformace okna Azure Data Factory



Transformace okna je místo, kde definujete agregace sloupců v datových proudech na základě okna. V Tvůrci výrazů můžete definovat různé typy agregací, které jsou založeny na datech nebo časových oknech (klauzule SQL OVER), jako je například olovo, LAG, NTILE, CUMEDIST, RANK atd.). Ve výstupu bude vygenerováno nové pole, které zahrnuje tyto agregace. Můžete také zahrnout volitelná pole Seskupit podle.

![Možnosti okna](media/data-flow/windows1.png "systém Windows 1")

## <a name="over"></a>Časem
Nastavte dělení dat sloupce pro transformaci oken. Ekvivalent SQL je ```Partition By``` v klauzuli over v SQL. Pokud chcete vytvořit výpočet nebo vytvořit výraz, který se má použít pro dělení, můžete to udělat najetím myší na název sloupce a vybráním "vypočítaný sloupec".

![Možnosti okna](media/data-flow/windows4.png "systém Windows 4")

## <a name="sort"></a>Seřadit
Další část klauzule over nastavuje ```Order By```. Tím se nastaví řazení dat. Můžete také vytvořit výraz pro výpočet hodnoty v tomto poli sloupce pro řazení.

![Možnosti okna](media/data-flow/windows5.png "systém Windows 5")

## <a name="range-by"></a>Rozsah podle
Dále nastavte rámec okna jako neohraničený nebo svázaný. Chcete-li nastavit rámeček neohraničeného okna, nastavte posuvník na hodnotu bez vazby na obou koncích. Pokud zvolíte nastavení mezi neohraničeným a aktuálním řádkem, je nutné nastavit počáteční a koncové hodnoty posunutí. Obě hodnoty budou kladná celá čísla. Z vašich dat můžete použít buď relativní čísla, nebo hodnoty.

Posuvník okna má dvě hodnoty k nastavení: hodnoty před aktuálním řádkem a hodnotami za aktuálním řádkem. Posun začátku a konce odpovídá dvěma selektorům na posuvníku.

![Možnosti okna](media/data-flow/windows6.png "systém Windows 6")

## <a name="window-columns"></a>Sloupce oken
Nakonec použijte Tvůrce výrazů k definování agregací, které chcete použít s daty, jako je například pořadí, počet, minimum, maximum, ZÚŽENí pořadí, vedoucí, PRODLEVa atd.

![Možnosti okna](media/data-flow/windows7.png "Systém Windows 7")

Úplný seznam agregačních a analytických funkcí, které jsou k dispozici pro použití v jazyce výrazů toku dat ADF prostřednictvím Tvůrce výrazů, jsou uvedeny zde: https://aka.ms/dataflowexpressions.

## <a name="next-steps"></a>Další kroky

Pokud hledáte jednoduchou agregaci Group by, použijte [agregační transformaci](data-flow-aggregate.md) .
