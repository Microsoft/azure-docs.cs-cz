---
title: Tahání transformační funkce toku dat v Azure Data Factory
description: Přehled dostupných funkcí toku dat tahání v Azure Data Factory
author: djpmsft
ms.author: daperlov
ms.reviewer: gamal
ms.service: data-factory
ms.topic: conceptual
ms.date: 11/01/2019
ms.openlocfilehash: e63c3f329cb9c1fd5ca91274540f5145c3ad098a
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "85921555"
---
# <a name="transformation-functions-in-wrangling-data-flow"></a>Transformační funkce v toku dat tahání

[!INCLUDE[appliesto-adf-xxx-md](includes/appliesto-adf-xxx-md.md)]

Tahání data flow v Azure Data Factory umožňuje provádět přípravu agilních dat a tahání v cloudovém měřítku bez kódu. Tahání data Flow se integruje s [Power Query online](https://docs.microsoft.com/powerquery-m/power-query-m-reference) a zpřístupňuje Power Query M funkcí pro data tahání prostřednictvím spuštění Sparku. 

> [!NOTE]
> Tok dat tahání je aktuálně dostupných ve verzi Public Preview.

V současné době nejsou všechny funkce Power Query M podporovány pro tahání dat, přestože jsou k dispozici při vytváření obsahu. Při sestavování datových toků tahání se zobrazí výzva s následující chybovou zprávou, pokud funkce není podporována:

`The wrangling data flow is invalid. Expression.Error: The transformation logic isn't supported. Please try a simpler expression`

Níže je uvedený seznam podporovaných funkcí Power Query M.

## <a name="column-management"></a>Správa sloupců

* Výběr: [Table. SelectColumns](https://docs.microsoft.com/powerquery-m/table-selectcolumns)
* Odebrání: [Table. RemoveColumns](https://docs.microsoft.com/powerquery-m/table-removecolumns)
* Přejmenování: [Table. RenameColumns](https://docs.microsoft.com/powerquery-m/table-renamecolumns), [Table. PrefixColumns](https://docs.microsoft.com/powerquery-m/table-prefixcolumns), [Table. TransformColumnNames](https://docs.microsoft.com/powerquery-m/table-transformcolumnnames)
* Změna pořadí: [Table. ReorderColumns](https://docs.microsoft.com/powerquery-m/table-reordercolumns)

## <a name="row-filtering"></a>Filtrování řádků

K filtrování následujících podmínek použijte funkci M Functions [Table. SelectRows](https://docs.microsoft.com/powerquery-m/table-selectrows) :

* Rovnost a nerovnost
* Porovnávání čísel, textů a kalendářních dat (ale ne hodnoty DateTime)
* Číselné informace, jako je [číslo. sudé](https://docs.microsoft.com/powerquery-m/number-iseven) / [liché](https://docs.microsoft.com/powerquery-m/number-iseven)
* Text je obsažený pomocí [textu. obsahuje](https://docs.microsoft.com/powerquery-m/text-contains), [text. StartsWith](https://docs.microsoft.com/powerquery-m/text-startswith)nebo [text. EndsWith](https://docs.microsoft.com/powerquery-m/text-endswith) .
* Rozsahy dat včetně všech funkcí IsIn' [Date](https://docs.microsoft.com/powerquery-m/date-functions) 
* Kombinace těchto podmínek pomocí a, nebo nebo not

## <a name="adding-and-transforming-columns"></a>Přidávání a transformace sloupců

Následující funkce M přidají nebo transformují sloupce: [Table. AddColumn](https://docs.microsoft.com/powerquery-m/table-addcolumn), [Table. TransformColumns](https://docs.microsoft.com/powerquery-m/table-transformcolumns), [Table. ReplaceValue](https://docs.microsoft.com/powerquery-m/table-replacevalue), [Table. DuplicateColumn](https://docs.microsoft.com/powerquery-m/table-duplicatecolumn). Níže jsou uvedené podporované transformační funkce.

* Číselná aritmetická operace
* Zřetězení textu
* Datum andTime aritmetické operace (aritmetické operátory, [datum. AddDays](https://docs.microsoft.com/powerquery-m/date-adddays), [datum. AddMonths](https://docs.microsoft.com/powerquery-m/date-addmonths), [datum. AddQuarters](https://docs.microsoft.com/powerquery-m/date-addquarters), [datum. AddWeeks](https://docs.microsoft.com/powerquery-m/date-addweeks), [datum. AddYears](https://docs.microsoft.com/powerquery-m/date-addyears))
* Dobu trvání lze použít pro aritmetické operace data a času, ale musí být transformovány do jiného typu před zápisem do jímky (aritmetické operátory [, #duration](https://docs.microsoft.com/powerquery-m/sharpduration), [Duration. Days](https://docs.microsoft.com/powerquery-m/duration-days) [,](https://docs.microsoft.com/powerquery-m/duration-totaldays) [Duration. hours](https://docs.microsoft.com/powerquery-m/duration-hours) [,](https://docs.microsoft.com/powerquery-m/duration-totalhours) [Duration](https://docs.microsoft.com/powerquery-m/duration-minutes). [TotalMinutes](https://docs.microsoft.com/powerquery-m/duration-totalminutes) [, Duration.](https://docs.microsoft.com/powerquery-m/duration-seconds) [TotalSeconds](https://docs.microsoft.com/powerquery-m/duration-totalseconds)).    
* Většina standardních, vědeckých a trigonometrických numerických funkcí (všechny funkce v rámci [operací](https://docs.microsoft.com/powerquery-m/number-functions#operations), [zaokrouhlování](https://docs.microsoft.com/powerquery-m/number-functions#rounding)a [trigonometrické](https://docs.microsoft.com/powerquery-m/number-functions#trigonometry) *s výjimkou* Number. faktoriál, Number. permutaces a Number. kombinací)
* Náhrada ([replacer. ReplaceText](https://docs.microsoft.com/powerquery-m/replacer-replacetext), [replacer. ReplaceValue](https://docs.microsoft.com/powerquery-m/replacer-replacevalue), [text. Replace](https://docs.microsoft.com/powerquery-m/text-replace), [text. Remove](https://docs.microsoft.com/powerquery-m/text-remove))
* Extrakce umístění textu ([text. PositionOf](https://docs.microsoft.com/powerquery-m/text-positionof), [text. Length](https://docs.microsoft.com/powerquery-m/text-length), [text. Start](https://docs.microsoft.com/powerquery-m/text-start), [text. end](https://docs.microsoft.com/powerquery-m/text-end), [text. Middle](https://docs.microsoft.com/powerquery-m/text-middle), [text. ReplaceRange](https://docs.microsoft.com/powerquery-m/text-replacerange), [text. RemoveRange](https://docs.microsoft.com/powerquery-m/text-removerange))
* Základní formátování textu ([text. Lower](https://docs.microsoft.com/powerquery-m/text-lower), [text. Upper](https://docs.microsoft.com/powerquery-m/text-upper), [text. trim](https://docs.microsoft.com/powerquery-m/text-trim) / [počáteční](https://docs.microsoft.com/powerquery-m/text-trimstart) / [konec](https://docs.microsoft.com/powerquery-m/text-trimend), [text. PadStart](https://docs.microsoft.com/powerquery-m/text-padstart) / [End](https://docs.microsoft.com/powerquery-m/text-padend), [text. Reverted](https://docs.microsoft.com/powerquery-m/text-reverse))
* Funkce data a času ([datum. den](https://docs.microsoft.com/powerquery-m/date-day), [datum. měsíc](https://docs.microsoft.com/powerquery-m/date-month), [datum. rok](https://docs.microsoft.com/powerquery-m/date-year) [čas. hodina](https://docs.microsoft.com/powerquery-m/time-hour), [čas. minute](https://docs.microsoft.com/powerquery-m/time-minute), [čas. sekund](https://docs.microsoft.com/powerquery-m/time-second), [datum. DayOfWeek](https://docs.microsoft.com/powerquery-m/date-dayofweek), [datum. DAYOFYEAR](https://docs.microsoft.com/powerquery-m/date-dayofyear), [datum. DAYSINMONTH](https://docs.microsoft.com/powerquery-m/date-daysinmonth))
* Výrazy if (ale větve musí mít stejné typy)
* Filtry řádků jako logický sloupec
* Konstanty Number, text, Logic, Date a DateTime

<a name="mergingjoining-tables"></a>Sloučení a spojování tabulek
----------------------
* Power Query vygeneruje vnořené spojení (Table. NestedJoin; uživatelé můžou také ručně zapisovat [Table. AddJoinColumn](https://docs.microsoft.com/powerquery-m/table-addjoincolumn)).
    Uživatelé pak musí rozšířit vnořený sloupec JOIN do nevnořeného typu Join (Table. ExpandTableColumn, který není podporován v žádném jiném kontextu).
* Tabulka funkcí M   [. spojení](https://docs.microsoft.com/powerquery-m/table-join) lze zapsat přímo, aby nedocházelo k nutnosti dalšího kroku rozšíření, ale uživatel musí zajistit, aby mezi připojenými tabulkami nebyly žádné duplicitní názvy sloupců.
* Podporované typy spojení:   [vnitřní](https://docs.microsoft.com/powerquery-m/joinkind-inner),   [LeftOuter](https://docs.microsoft.com/powerquery-m/joinkind-leftouter),   [RightOuter](https://docs.microsoft.com/powerquery-m/joinkind-rightouter),   [FullOuter](https://docs.microsoft.com/powerquery-m/joinkind-fullouter)
* [Hodnota. Equals](https://docs.microsoft.com/powerquery-m/value-equals) a [Value. NullableEquals](https://docs.microsoft.com/powerquery-m/value-nullableequals) jsou podporovány jako porovnávače pro klíčová rovnost.

## <a name="group-by"></a>Seskupit podle

K agregaci hodnot použijte [Table. Group](https://docs.microsoft.com/powerquery-m/table-group) .
* Musí se používat s agregační funkcí
* Podporované agregační funkce:   [Table. RowCount](https://docs.microsoft.com/powerquery-m/table-rowcount),   [list. Sum](https://docs.microsoft.com/powerquery-m/list-sum),   [list. Count](https://docs.microsoft.com/powerquery-m/list-count),   [list. Average](https://docs.microsoft.com/powerquery-m/list-average),   [list. min](https://docs.microsoft.com/powerquery-m/list-min),   [list. Max](https://docs.microsoft.com/powerquery-m/list-max),   [list. StandardDeviation](https://docs.microsoft.com/powerquery-m/list-standarddeviation),   [list. First](https://docs.microsoft.com/powerquery-m/list-first),   [list. Last](https://docs.microsoft.com/powerquery-m/list-last)

## <a name="sorting"></a>Řazení

K řazení hodnot použijte [Table. Sort](https://docs.microsoft.com/powerquery-m/table-sort) .

## <a name="reducing-rows"></a>Zmenšení řádků

Zachovat a odebrat horní, zachovat rozsah (odpovídající funkce M, jenom počty, nikoli podmínky: [Table. FirstN](https://docs.microsoft.com/powerquery-m/table-firstn), [Table. Skip](https://docs.microsoft.com/powerquery-m/table-skip), [Table. RemoveFirstN](https://docs.microsoft.com/powerquery-m/table-removefirstn), [Table. Range](https://docs.microsoft.com/powerquery-m/table-range), [Table. MinN](https://docs.microsoft.com/powerquery-m/table-minn), [Table. MaxN](https://docs.microsoft.com/powerquery-m/table-maxn))

## <a name="known-unsupported-functions"></a>Známé nepodporované funkce

| Funkce | Status |
| -- | -- |
| Table.PromoteHeaders | Nepodporováno Stejný výsledek lze dosáhnout nastavením "první řádek jako záhlaví" v datové sadě. |
| Table.CombineColumns | Toto je běžný scénář, který není přímo podporován, ale je možné ho dosáhnout přidáním nového sloupce, který zřetězí dva sloupce.  Například Table. AddColumn (RemoveEmailColumn; "Name"; Each [FirstName] & "" & [LastName]) |
| Table.TransformColumnTypes | To se ve většině případů podporuje. Následující scénáře nejsou podporovány: transformuje řetězec na typ měny, transformuje řetězec na typ času a transformuje řetězec na typ procent. |
| Table.NestedJoin | Pouhým připojením dojde k chybě ověření. Aby bylo možné tyto sloupce fungovat, musí být rozbaleny. |
| Table.Distinct | Odstranění duplicitních řádků není podporováno. |
| Table.RemoveLastN | Odebrat dolní řádky nejsou podporovány. |
| Table.RowCount | Nepodporuje se, ale dá se dosáhnout pomocí výrazu přidat sloupec se všemi buňkami s prázdným sloupcem (můžete použít sloupec podmínka) a potom v tomto sloupci použít Group by. Tabulka. Group je podporována. | 
| Zpracování chyb na úrovni řádků | Zpracování chyb na úrovni řádků se momentálně nepodporuje. Chcete-li například vyfiltrovat nečíselné hodnoty ze sloupce, může být jedním z přístupů převod textového sloupce na číslo. Každá buňka, která se nedokáže transformovat, bude v chybovém stavu a bude nutné ji filtrovat. Tento scénář není možný v toku dat tahání. |
| Table.Transpose | Nepodporováno |
| Table.Pivot | Nepodporováno |

## <a name="next-steps"></a>Další kroky

Naučte se [vytvářet tahání tok dat](wrangling-data-flow-tutorial.md).