---
title: Hašteření funkce transformace toku dat v Azure Data Factory
description: Přehled dostupných funkcí toku dat ve službě Azure Data Factory
author: djpmsft
ms.author: daperlov
ms.reviewer: gamal
ms.service: data-factory
ms.topic: conceptual
ms.date: 11/01/2019
ms.openlocfilehash: e2517ec4a02a5d61fb3ce1d9ca9ffa2b5f4e8bf8
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "74287031"
---
# <a name="transformation-functions-in-wrangling-data-flow"></a>Transformační funkce v toku dat hádání

Tok dat ve službě Azure Data Factory umožňuje provést agilní přípravu agilních dat bez kódu a hašteření v cloudovém měřítku. Tahanice o tok dat se integruje s [Power Query Online](https://docs.microsoft.com/powerquery-m/power-query-m-reference) a zpřístupňuje funkce Power Query M pro hádání dat prostřednictvím spuštění jiskry. 

V současné době nejsou podporovány všechny funkce Power Query M pro hádání dat, přestože jsou k dispozici během vytváření. Při vytváření toků dat hádání se zobrazí výzva s následující chybovou zprávou, pokud funkce není podporována:

`The wrangling data flow is invalid. Expression.Error: The transformation logic isn't supported. Please try a simpler expression`

Níže je seznam podporovaných funkcí Power Query M.

## <a name="column-management"></a>Správa sloupců

* Výběr: [Table.SelectColumns](https://docs.microsoft.com/powerquery-m/table-selectcolumns)
* Odebrání: [Table.RemoveColumns](https://docs.microsoft.com/powerquery-m/table-removecolumns)
* Přejmenování: [Table.RenameColumns](https://docs.microsoft.com/powerquery-m/table-renamecolumns), [Table.PrefixColumns](https://docs.microsoft.com/powerquery-m/table-prefixcolumns), [Table.TransformColumnNames](https://docs.microsoft.com/powerquery-m/table-transformcolumnnames)
* Změna pořadí: [Table.ReorderColumns](https://docs.microsoft.com/powerquery-m/table-reordercolumns)

## <a name="row-filtering"></a>Filtrování řádků

Pomocí funkce M [Table.SelectRows](https://docs.microsoft.com/powerquery-m/table-selectrows) filtrovat za následujících podmínek:

* Rovnost a nerovnost
* Číselná porovnání, porovnání textu a kalendářních dat (nikoli však DateTime)
* Číselné informace, například [Number.IsEven](https://docs.microsoft.com/powerquery-m/number-iseven)/[Liché](https://docs.microsoft.com/powerquery-m/number-iseven)
* Omezení textu pomocí [text.contains](https://docs.microsoft.com/powerquery-m/text-contains), [Text.StartsWith](https://docs.microsoft.com/powerquery-m/text-startswith)nebo [Text.EndsWith](https://docs.microsoft.com/powerquery-m/text-endswith)
* Rozsahy dat včetně všech [funkcí Data](https://docs.microsoft.com/powerquery-m/date-functions)"IsIn" ) 
* Kombinace těchto použití a, nebo, nebo ne podmínky

## <a name="adding-and-transforming-columns"></a>Přidání a transformace sloupců

Následující funkce M přidávají nebo transformují sloupce: [Table.AddColumn](https://docs.microsoft.com/powerquery-m/table-addcolumn), [Table.TransformColumns](https://docs.microsoft.com/powerquery-m/table-transformcolumns), [Table.ReplaceValue](https://docs.microsoft.com/powerquery-m/table-replacevalue), [Table.DuplicateColumn](https://docs.microsoft.com/powerquery-m/table-duplicatecolumn). Níže jsou podporovány transformační funkce.

* Numerická aritmetika
* Zřetězení textu
* Datum aAritmetika času (aritmetické operátory, [Date.AddDays](https://docs.microsoft.com/powerquery-m/date-adddays), [Date.AddMonths](https://docs.microsoft.com/powerquery-m/date-addmonths), [Date.AddQuarters](https://docs.microsoft.com/powerquery-m/date-addquarters), [Date.AddWeeks](https://docs.microsoft.com/powerquery-m/date-addweeks), [Date.AddYears)](https://docs.microsoft.com/powerquery-m/date-addyears)
* Doby trvání lze použít pro aritmetiku data a času, ale před zápisem do jímky musí být transformovány do jiného typu (Aritmetické operátory, [#duration](https://docs.microsoft.com/powerquery-m/sharpduration), [Doba.Days](https://docs.microsoft.com/powerquery-m/duration-days), [Doba trvání.Hodiny](https://docs.microsoft.com/powerquery-m/duration-hours), [Doba trvání.Minuty](https://docs.microsoft.com/powerquery-m/duration-minutes), [Doba trvání.Sekundy](https://docs.microsoft.com/powerquery-m/duration-seconds), [Duration.TotalDays](https://docs.microsoft.com/powerquery-m/duration-totaldays), [Duration.TotalHours](https://docs.microsoft.com/powerquery-m/duration-totalhours), [Duration.TotalMinutes](https://docs.microsoft.com/powerquery-m/duration-totalminutes), [Duration.TotalSeconds](https://docs.microsoft.com/powerquery-m/duration-totalseconds))    
* Většina standardních, vědeckých a goniometrických číselných funkcí (Všechny funkce v části [Operace](https://docs.microsoft.com/powerquery-m/number-functions#operations), [Zaokrouhlení](https://docs.microsoft.com/powerquery-m/number-functions#rounding)a [trigonometrie](https://docs.microsoft.com/powerquery-m/number-functions#trigonometry) *s výjimkou* Number.Factorial, Number.Permutations a Number.Combinations)
* Nahrazení ([Replacer.ReplaceText](https://docs.microsoft.com/powerquery-m/replacer-replacetext), [Replacer.ReplaceValue](https://docs.microsoft.com/powerquery-m/replacer-replacevalue), [Text.Replace](https://docs.microsoft.com/powerquery-m/text-replace), [Text.Remove)](https://docs.microsoft.com/powerquery-m/text-remove)
* Extrakce pozičního textu ([Text.PositionOf](https://docs.microsoft.com/powerquery-m/text-positionof), [Text.Length](https://docs.microsoft.com/powerquery-m/text-length), [Text.Start](https://docs.microsoft.com/powerquery-m/text-start), [Text.End](https://docs.microsoft.com/powerquery-m/text-end), [Text.Middle](https://docs.microsoft.com/powerquery-m/text-middle), [Text.ReplaceRange](https://docs.microsoft.com/powerquery-m/text-replacerange), [Text.RemoveRange)](https://docs.microsoft.com/powerquery-m/text-removerange)
* Základní formátování textu ([Text.Lower](https://docs.microsoft.com/powerquery-m/text-lower), [Text.Horní](https://docs.microsoft.com/powerquery-m/text-upper), [Text.Trim](https://docs.microsoft.com/powerquery-m/text-trim)/[Začátek,](https://docs.microsoft.com/powerquery-m/text-trimstart)/[End](https://docs.microsoft.com/powerquery-m/text-trimend) [Text.PadStart](https://docs.microsoft.com/powerquery-m/text-padstart)/[End](https://docs.microsoft.com/powerquery-m/text-padend), [Text.Reverse)](https://docs.microsoft.com/powerquery-m/text-reverse)
* Funkce data a času ([datum.den](https://docs.microsoft.com/powerquery-m/date-day), [datum.měsíc](https://docs.microsoft.com/powerquery-m/date-month), [datum.rok](https://docs.microsoft.com/powerquery-m/date-year) [Čas.hodina](https://docs.microsoft.com/powerquery-m/time-hour), [time.minute](https://docs.microsoft.com/powerquery-m/time-minute), [čas.sekunda](https://docs.microsoft.com/powerquery-m/time-second), [datum.denofweek](https://docs.microsoft.com/powerquery-m/date-dayofweek), [datum.denroku](https://docs.microsoft.com/powerquery-m/date-dayofyear), [datum.dny v měsíci](https://docs.microsoft.com/powerquery-m/date-daysinmonth))
* Pokud výrazy (ale větve musí mít odpovídající typy)
* Filtry řádků jako logický sloupec
* Konstanty číslo, text, logické, datum a datum

<a name="mergingjoining-tables"></a>Slučování/spojování tabulek
----------------------
* Power Query vygeneruje vnořené spojení (Table.NestedJoin; uživatelé mohou také ručně napsat [Table.AddJoinColumn).](https://docs.microsoft.com/powerquery-m/table-addjoincolumn)
    Uživatelé pak musí rozbalit vnořený sloupec spojení do nevnořeného spojení (Table.ExpandTableColumn, není podporován v žádném jiném kontextu).
* Funkce M [Table.Join](https://docs.microsoft.com/powerquery-m/table-join) může být napsána přímo, aby se zabránilo nutnosti dalšího kroku rozšíření, ale uživatel musí zajistit, že mezi spojenými tabulkami nejsou žádné duplicitní názvy sloupců
* Podporované druhy spojení: [Vnitřní](https://docs.microsoft.com/powerquery-m/joinkind-inner), [LeftOuter](https://docs.microsoft.com/powerquery-m/joinkind-leftouter), [RightOuter](https://docs.microsoft.com/powerquery-m/joinkind-rightouter), [FullOuter](https://docs.microsoft.com/powerquery-m/joinkind-fullouter)
* Jako porovnávače rovnosti klíčů jsou podporovány [hodnoty Value.Equals](https://docs.microsoft.com/powerquery-m/value-equals) a [Hodnota.NullableEquals.](https://docs.microsoft.com/powerquery-m/value-nullableequals)

## <a name="group-by"></a>Seskupit podle

K agregaci hodnot použijte [soubor Table.Group.](https://docs.microsoft.com/powerquery-m/table-group)
* Musí být použit s funkcí agregace
* Podporované funkce agregace: [Table.RowCount](https://docs.microsoft.com/powerquery-m/table-rowcount), [List.Sum](https://docs.microsoft.com/powerquery-m/list-sum), [List.Count](https://docs.microsoft.com/powerquery-m/list-count), [List.Average](https://docs.microsoft.com/powerquery-m/list-average), [List.Min](https://docs.microsoft.com/powerquery-m/list-min), [List.Max](https://docs.microsoft.com/powerquery-m/list-max), [List.StandardDeviation](https://docs.microsoft.com/powerquery-m/list-standarddeviation), [List.First](https://docs.microsoft.com/powerquery-m/list-first), [List.Last](https://docs.microsoft.com/powerquery-m/list-last)

## <a name="sorting"></a>Řazení

K řazení hodnot použijte [soubor Table.Sort.](https://docs.microsoft.com/powerquery-m/table-sort)

## <a name="reducing-rows"></a>Redukce řádků

Zachovat a odebrat horní, zachovat rozsah (odpovídající M funkce, pouze podpůrné počty, ne podmínky: [Table.FirstN](https://docs.microsoft.com/powerquery-m/table-firstn), [Table.Skip](https://docs.microsoft.com/powerquery-m/table-skip), [Table.RemoveFirstN](https://docs.microsoft.com/powerquery-m/table-removefirstn), [Table.Range](https://docs.microsoft.com/powerquery-m/table-range), [Table.MinN](https://docs.microsoft.com/powerquery-m/table-minn), [Table.MaxN](https://docs.microsoft.com/powerquery-m/table-maxn))

## <a name="known-unsupported-functions"></a>Známé nepodporované funkce

| Funkce | Status |
| -- | -- |
| Table.PromoteHeaders | Není podporováno. Stejného výsledku lze dosáhnout nastavením "První řádek jako záhlaví" v datové sadě. |
| Table.CombineColumns | Jedná se o běžný scénář, který není přímo podporován, ale lze jej dosáhnout přidáním nového sloupce, který zřetězí dva dané sloupce.  Například Table.AddColumn(RemoveEmailColumn, "Name", každý [FirstName] & " " & [LastName]) |
| Table.TransformColumnTypes | To je podporováno ve většině případů. Následující scénáře nejsou podporovány: transformace řetězce na typ měny, transformace řetězce na typ času, transformace řetězce na typ procenta. |
| Table.NestedJoin | Jen dělá spojení bude mít za následek chybu ověření. Aby sloupce fungovaly, musí být rozbaleny. |
| Table.Distinct | Odebrání duplicitních řádků není podporováno. |
| Table.RemoveLastN | Odebrání dolních řádků není podporováno. |
| Table.RowCount | Není podporováno, ale lze jej dosáhnout přidáním sloupce se všemi prázdnými buňkami (lze použít sloupec podmínky) a potom pomocí skupiny v tomto sloupci. Tabulka.Skupina je podporována. | 
| Zpracování chyb na úrovni řádků | Zpracování chyb na úrovni řádku není aktuálně podporováno. Chcete-li například odfiltrovat nečíselné hodnoty ze sloupce, jedním z přístupů by byla transformace textového sloupce na číslo. Každá buňka, která se nepodaří transformovat, bude v chybovém stavu a je třeba ji filtrovat. Tento scénář není možné v hašteření toku dat. |
| Table.Transpose | Nepodporuje se |
| Table.Pivot | Nepodporuje se |

## <a name="next-steps"></a>Další kroky

Přečtěte si, jak [vytvořit tok dat v hašteření](wrangling-data-flow-tutorial.md).