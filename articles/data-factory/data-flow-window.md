---
title: Azure Data Factory mapování transformace okno toku dat
description: Azure Data Factory mapování transformace okno toku dat
author: kromerm
ms.author: makromer
ms.reviewer: douglasl
ms.service: data-factory
ms.topic: conceptual
ms.date: 01/30/2019
ms.openlocfilehash: 6f3f06ff54fc76416ba63f4f09835897d546f8dc
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/23/2019
ms.locfileid: "61349747"
---
# <a name="azure-data-factory-window-transformation"></a>Transformace okno objekt pro vytváření dat Azure

[!INCLUDE [notes](../../includes/data-factory-data-flow-preview.md)]

Okno transformace je, kde budou definovat založených na oknech agregace sloupců v datové proudy. V okně Tvůrce výrazů můžete definovat různé druhy agregace, které jsou založeny na data nebo času systému windows (klauzuli SQL OVER) například potenciálních zákazníků, PRODLEVA, ntile povolen, CUMEDIST, řazení atd.). Nové pole se vygeneruje výstup, který zahrnuje tyto agregace. Můžete použít také volitelné Group pole.

![Možnosti okna](media/data-flow/windows1.png "windows 1")

## <a name="over"></a>Selhání
Nastavte, dělení dat sloupců pro vaše okno transformace. Ekvivalent SQL je ```Partition By``` v klauzuli Over v SQL. Pokud chcete vytvořit výpočtu nebo výraz, který se použijte pro rozdělení, můžete to udělat tak, že ukazatel myši na název sloupce a vyberte "počítaný sloupec".

![Možnosti okna](media/data-flow/windows4.png "windows 4")

## <a name="sort"></a>Seřadit
Nastavení jiné části v klauzuli Over ```Order By```. Nastaví pořadí řazení data. Můžete také vytvořit výraz pro výpočet hodnoty v tomto poli sloupec pro řazení.

![Možnosti okna](media/data-flow/windows5.png "windows 5")

## <a name="range-by"></a>Podle rozsahu
V dalším kroku nastavte rámec okna nástrojů Unbounded nebo omezená. Nastavení bez vazby okno rámce, nastavte posuvník na nástrojů Unbounded na obou koncích. Pokud se rozhodnete nastavení mezi nástrojů Unbounded a Current Row, musíte nastavit počáteční posun a konec hodnoty. Obě hodnoty bude kladná celá čísla. Ze svých dat můžete použít relativní čísla nebo hodnoty.

Jezdec okno má dvě hodnoty pro nastavení: hodnoty před aktuální řádek a hodnoty po aktuálního řádku. Posun počáteční a koncové odpovídá dvou selektorů na posuvníku.

![Možnosti okna](media/data-flow/windows6.png "windows 6")

## <a name="window-columns"></a>Sloupce v okně
A konečně použijte Tvůrce výrazů k definování agregace, který chcete použít s daty windows, jako jsou hodnocení, COUNT, MIN, MAX, DENSE RANK, ZÁJEMCE, PRODLEVA atd.

![Možnosti okna](media/data-flow/windows7.png "windows 7")

Tady jsou uvedené na seznam všech agregaci a analytické funkce, které jsou k dispozici pro použití v ADF tok výraz jazyk Data prostřednictvím Tvůrce: https://aka.ms/dataflowexpressions.

## <a name="next-steps"></a>Další postup

Pokud hledáte jednoduchý Group agregace, použijte [agregovat transformace](data-flow-aggregate.md)
