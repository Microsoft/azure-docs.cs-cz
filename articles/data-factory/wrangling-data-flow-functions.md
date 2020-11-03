---
title: Tahání transformační funkce toku dat v Azure Data Factory
description: Přehled dostupných funkcí toku dat tahání v Azure Data Factory
author: djpmsft
ms.author: daperlov
ms.reviewer: gamal
ms.service: data-factory
ms.topic: conceptual
ms.date: 11/01/2019
ms.openlocfilehash: c56c52193f433571f16e4acf7bd6e7b89641b26f
ms.sourcegitcommit: bbd66b477d0c8cb9adf967606a2df97176f6460b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/03/2020
ms.locfileid: "93233946"
---
# <a name="transformation-functions-in-wrangling-data-flow"></a>Transformační funkce v toku dat tahání

[!INCLUDE[appliesto-adf-xxx-md](includes/appliesto-adf-xxx-md.md)]

Tahání data flow v Azure Data Factory umožňuje provádět přípravu agilních dat a tahání v cloudovém měřítku bez kódu. Tahání data Flow se integruje s [Power Query online](/powerquery-m/power-query-m-reference) a zpřístupňuje Power Query M funkcí pro data tahání prostřednictvím spuštění Sparku. 

> [!NOTE]
> Tok dat tahání je aktuálně dostupných ve verzi Public Preview.

V současné době nejsou všechny funkce Power Query M podporovány pro tahání dat, přestože jsou k dispozici při vytváření obsahu. Při sestavování datových toků tahání se zobrazí výzva s následující chybovou zprávou, pokud funkce není podporována:

`The Wrangling Data Flow is invalid. Expression.Error: The transformation logic is not supported. Please try a simpler expression.`

Níže je uvedený seznam podporovaných funkcí Power Query M.

## <a name="column-management"></a>Správa sloupců

* Výběr: [Table. SelectColumns](/powerquery-m/table-selectcolumns)
* Odebrání: [Table. RemoveColumns](/powerquery-m/table-removecolumns)
* Přejmenování: [Table. RenameColumns](/powerquery-m/table-renamecolumns), [Table. PrefixColumns](/powerquery-m/table-prefixcolumns), [Table. TransformColumnNames](/powerquery-m/table-transformcolumnnames)
* Změna pořadí: [Table. ReorderColumns](/powerquery-m/table-reordercolumns)

## <a name="row-filtering"></a>Filtrování řádků

K filtrování následujících podmínek použijte funkci M Functions [Table. SelectRows](/powerquery-m/table-selectrows) :

* Rovnost a nerovnost
* Porovnávání čísel, textů a kalendářních dat (ale ne hodnoty DateTime)
* Číselné informace, jako je [číslo. sudé](/powerquery-m/number-iseven) / [liché](/powerquery-m/number-iseven)
* Text je obsažený pomocí [textu. obsahuje](/powerquery-m/text-contains), [text. StartsWith](/powerquery-m/text-startswith)nebo [text. EndsWith](/powerquery-m/text-endswith) .
* Rozsahy dat včetně všech funkcí IsIn' [Date](/powerquery-m/date-functions) 
* Kombinace těchto podmínek pomocí a, nebo nebo not

## <a name="adding-and-transforming-columns"></a>Přidávání a transformace sloupců

Následující funkce M přidají nebo transformují sloupce: [Table. AddColumn](/powerquery-m/table-addcolumn), [Table. TransformColumns](/powerquery-m/table-transformcolumns), [Table. ReplaceValue](/powerquery-m/table-replacevalue), [Table. DuplicateColumn](/powerquery-m/table-duplicatecolumn). Níže jsou uvedené podporované transformační funkce.

* Číselná aritmetická operace
* Zřetězení textu
* Datum andTime aritmetické operace (aritmetické operátory, [datum. AddDays](/powerquery-m/date-adddays), [datum. AddMonths](/powerquery-m/date-addmonths), [datum. AddQuarters](/powerquery-m/date-addquarters), [datum. AddWeeks](/powerquery-m/date-addweeks), [datum. AddYears](/powerquery-m/date-addyears))
* Dobu trvání lze použít pro aritmetické operace data a času, ale musí být transformovány do jiného typu před zápisem do jímky (aritmetické operátory [, #duration](/powerquery-m/sharpduration), [Duration. Days](/powerquery-m/duration-days) [,](/powerquery-m/duration-totaldays) [Duration. hours](/powerquery-m/duration-hours) [,](/powerquery-m/duration-totalhours) [Duration](/powerquery-m/duration-minutes). [TotalMinutes](/powerquery-m/duration-totalminutes) [, Duration.](/powerquery-m/duration-seconds) [TotalSeconds](/powerquery-m/duration-totalseconds)).    
* Většina standardních, vědeckých a trigonometrických numerických funkcí (všechny funkce v rámci [operací](/powerquery-m/number-functions#operations), [zaokrouhlování](/powerquery-m/number-functions#rounding)a [trigonometrické](/powerquery-m/number-functions#trigonometry) *s výjimkou* Number. faktoriál, Number. permutaces a Number. kombinací)
* Náhrada ([replacer. ReplaceText](/powerquery-m/replacer-replacetext), [replacer. ReplaceValue](/powerquery-m/replacer-replacevalue), [text. Replace](/powerquery-m/text-replace), [text. Remove](/powerquery-m/text-remove))
* Extrakce umístění textu ([text. PositionOf](/powerquery-m/text-positionof), [text. Length](/powerquery-m/text-length), [text. Start](/powerquery-m/text-start), [text. end](/powerquery-m/text-end), [text. Middle](/powerquery-m/text-middle), [text. ReplaceRange](/powerquery-m/text-replacerange), [text. RemoveRange](/powerquery-m/text-removerange))
* Základní formátování textu ([text. Lower](/powerquery-m/text-lower), [text. Upper](/powerquery-m/text-upper), [text. trim](/powerquery-m/text-trim) / [počáteční](/powerquery-m/text-trimstart) / [konec](/powerquery-m/text-trimend), [text. PadStart](/powerquery-m/text-padstart) / [End](/powerquery-m/text-padend), [text. Reverted](/powerquery-m/text-reverse))
* Funkce data a času ([datum. den](/powerquery-m/date-day), [datum. měsíc](/powerquery-m/date-month), [datum. rok](/powerquery-m/date-year) [čas. hodina](/powerquery-m/time-hour), [čas. minute](/powerquery-m/time-minute), [čas. sekund](/powerquery-m/time-second), [datum. DayOfWeek](/powerquery-m/date-dayofweek), [datum. DAYOFYEAR](/powerquery-m/date-dayofyear), [datum. DAYSINMONTH](/powerquery-m/date-daysinmonth))
* Výrazy if (ale větve musí mít stejné typy)
* Filtry řádků jako logický sloupec
* Konstanty Number, text, Logic, Date a DateTime

<a name="mergingjoining-tables"></a>Sloučení a spojování tabulek
----------------------
* Power Query vygeneruje vnořené spojení (Table. NestedJoin; uživatelé můžou také ručně zapisovat [Table. AddJoinColumn](/powerquery-m/table-addjoincolumn)).
    Uživatelé pak musí rozšířit vnořený sloupec JOIN do nevnořeného typu Join (Table. ExpandTableColumn, který není podporován v žádném jiném kontextu).
* Tabulka funkcí M   [. spojení](/powerquery-m/table-join) lze zapsat přímo, aby nedocházelo k nutnosti dalšího kroku rozšíření, ale uživatel musí zajistit, aby mezi připojenými tabulkami nebyly žádné duplicitní názvy sloupců.
* Podporované typy spojení:   [vnitřní](/powerquery-m/joinkind-inner),   [LeftOuter](/powerquery-m/joinkind-leftouter),   [RightOuter](/powerquery-m/joinkind-rightouter),   [FullOuter](/powerquery-m/joinkind-fullouter)
* [Hodnota. Equals](/powerquery-m/value-equals) a [Value. NullableEquals](/powerquery-m/value-nullableequals) jsou podporovány jako porovnávače pro klíčová rovnost.

## <a name="group-by"></a>Seskupit podle

K agregaci hodnot použijte [Table. Group](/powerquery-m/table-group) .
* Musí se používat s agregační funkcí
* Podporované agregační funkce:   [list. Sum](/powerquery-m/list-sum),   [list. Count](/powerquery-m/list-count),   [list. Average](/powerquery-m/list-average),   [list. min](/powerquery-m/list-min),   [list. Max](/powerquery-m/list-max),   [list. StandardDeviation](/powerquery-m/list-standarddeviation),   [list. First](/powerquery-m/list-first),   [list. Last](/powerquery-m/list-last)

## <a name="sorting"></a>Řazení

K řazení hodnot použijte [Table. Sort](/powerquery-m/table-sort) .

## <a name="reducing-rows"></a>Zmenšení řádků

Zachovat a odebrat horní, zachovat rozsah (odpovídající funkce M, jenom počty, nikoli podmínky: [Table. FirstN](/powerquery-m/table-firstn), [Table. Skip](/powerquery-m/table-skip), [Table. RemoveFirstN](/powerquery-m/table-removefirstn), [Table. Range](/powerquery-m/table-range), [Table. MinN](/powerquery-m/table-minn), [Table. MaxN](/powerquery-m/table-maxn))

## <a name="known-unsupported-functions"></a>Známé nepodporované funkce

| Funkce | Status |
| -- | -- |
| Table.PromoteHeaders | Nepodporováno Stejný výsledek lze dosáhnout nastavením "první řádek jako záhlaví" v datové sadě. |
| Table.CombineColumns | Toto je běžný scénář, který není přímo podporován, ale je možné ho dosáhnout přidáním nového sloupce, který zřetězí dva sloupce.  Například Table. AddColumn (RemoveEmailColumn; "Name"; Each [FirstName] & "" & [LastName]) |
| Table.TransformColumnTypes | To se ve většině případů podporuje. Následující scénáře nejsou podporovány: transformuje řetězec na typ měny, transformuje řetězec na typ času a transformuje řetězec na typ procent. |
| Table.NestedJoin | Pouhým připojením dojde k chybě ověření. Aby bylo možné tyto sloupce fungovat, musí být rozbaleny. |
| Table.Distinct | Odstranění duplicitních řádků není podporováno. |
| Table.RemoveLastN | Odebrat dolní řádky nejsou podporovány. |
| Table.RowCount | Nepodporováno, ale lze je dosáhnout přidáním vlastního sloupce obsahujícího hodnotu 1 a následným agregacím tohoto sloupce se seznamem. Sum. Tabulka. Group je podporována. | 
| Zpracování chyb na úrovni řádků | Zpracování chyb na úrovni řádků se momentálně nepodporuje. Chcete-li například vyfiltrovat nečíselné hodnoty ze sloupce, může být jedním z přístupů převod textového sloupce na číslo. Každá buňka, která se nedokáže transformovat, bude v chybovém stavu a bude nutné ji filtrovat. Tento scénář není možný v toku dat tahání. |
| Table.Transpose | Nepodporováno |
| Table.Pivot | Nepodporováno |

## <a name="next-steps"></a>Další kroky

Naučte se [vytvářet tahání tok dat](wrangling-data-flow-tutorial.md).
