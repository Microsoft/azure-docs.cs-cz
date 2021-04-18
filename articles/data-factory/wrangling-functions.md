---
title: Funkce tahání dat v Azure Data Factory
description: Přehled dostupných funkcí tahání dat v Azure Data Factory
author: kromerm
ms.author: makromer
ms.service: data-factory
ms.topic: conceptual
ms.date: 04/16/2021
ms.openlocfilehash: f7a4041d87e00fa01ae5ae4dff0cade3b9755d31
ms.sourcegitcommit: 950e98d5b3e9984b884673e59e0d2c9aaeabb5bb
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/18/2021
ms.locfileid: "107600929"
---
# <a name="transformation-functions-in-power-query-for-data-wrangling"></a>Transformační funkce v Power Query pro tahání dat

[!INCLUDE[appliesto-adf-xxx-md](includes/appliesto-adf-xxx-md.md)]

Data tahání v Azure Data Factory umožňují provádět přípravu agilních dat bez kódu a tahání v cloudovém měřítku posunutím Power Query ```M``` skriptů do skriptu toku dat. ADF se integruje s [Power Query online](/powerquery-m/power-query-m-reference) a zpřístupňuje Power Query ```M``` funkce pro data tahání prostřednictvím spuštění Sparku pomocí infrastruktury toku dat pro data Spark. 

> [!NOTE]
> Power Query v ADF je aktuálně dostupných ve verzi Public Preview.

V současné době nejsou všechny funkce Power Query M podporovány pro tahání dat, přestože jsou k dispozici při vytváření obsahu. Při sestavování zpracování se zobrazí výzva s následující chybovou zprávou, pokud funkce není podporována:

`UserQuery : Expression.Error: The transformation logic is not supported as it requires dynamic access to rows of data, which cannot be scaled out.`

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
* Aritmetické operace data a času (aritmetické operátory, [Date. AddDays](/powerquery-m/date-adddays), [Date. AddMonths](/powerquery-m/date-addmonths), [Date. AddQuarters](/powerquery-m/date-addquarters), [Date. AddWeeks](/powerquery-m/date-addweeks), [Date. AddYears](/powerquery-m/date-addyears))
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
| Zpracování chyb na úrovni řádků | Zpracování chyb na úrovni řádků se momentálně nepodporuje. Chcete-li například vyfiltrovat nečíselné hodnoty ze sloupce, může být jedním z přístupů převod textového sloupce na číslo. Každá buňka, která se nedokáže transformovat, bude v chybovém stavu a bude nutné ji filtrovat. Tento scénář není možné škálovat M. |
| Table.Transpose | Nepodporováno |
| Table.Pivot | Nepodporováno |
| Table.SplitColumn | Částečně podporované |

## <a name="m-script-workarounds"></a>M alternativní řešení skriptů

### <a name="for-splitcolumn-there-is-an-alternate-for-split-by-length-and-by-position"></a>Pro ```SplitColumn``` rozdělení podle délky a podle pozice je alternativa.

* Table. AddColumn (Source, "First Characters"; Each text. Start ([email]; 7); typ text)
* Table. AddColumn (# "vložil první znaky", "textový rozsah", každý text. Middle ([E-mail], 4, 9), text typu)

Tato možnost je přístupná z možnosti extrakce na pásu karet

![Power Query přidat sloupec](media/wrangling-data-flow/pq-split.png)

### <a name="for-tablecombinecolumns"></a>For ```Table.CombineColumns```

* Table. AddColumn (RemoveEmailColumn; "Name"; Each [FirstName] & "" & [LastName])


## <a name="next-steps"></a>Další kroky

Naučte se, jak [vytvořit datovou tahání Power Query v ADF](wrangling-tutorial.md).
