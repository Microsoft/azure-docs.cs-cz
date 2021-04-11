---
title: Sloupcová ukázka tabulkového úložiště – Citus (Velká) – Azure Database for PostgreSQL
description: Komprimace dat pomocí sloupcového úložiště (Preview)
author: jonels-msft
ms.author: jonels
ms.service: postgresql
ms.subservice: hyperscale-citus
ms.topic: conceptual
ms.date: 04/07/2021
ms.openlocfilehash: c60c398d49a802dbe051ca37c4aea2092ab5144b
ms.sourcegitcommit: 6ed3928efe4734513bad388737dd6d27c4c602fd
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/07/2021
ms.locfileid: "107023948"
---
# <a name="columnar-table-storage-preview"></a>Sloupcové úložiště tabulek (Preview)

> [!IMPORTANT]
> Sloupcové úložiště tabulek v Citus () je aktuálně ve verzi Preview. Tato verze Preview se poskytuje bez smlouvy o úrovni služeb a nedoporučuje se pro úlohy v produkčním prostředí. Některé funkce se nemusí podporovat nebo mohou mít omezené možnosti.
>
> Úplný seznam dalších nových funkcí najdete v části [funkce ve verzi Preview pro Citus (škálování)](hyperscale-preview-features.md).

Azure Database for PostgreSQL – Citus () podporuje pouze připojení sloupcového úložiště tabulky pro analytické a datové sklady. Když jsou sloupce (spíše než řádky) uloženy souvisle na disku, data budou více komprimovat a dotazy mohou rychleji vyžádat podmnožinu sloupců.

Pokud chcete použít sloupcové úložiště, určete `USING columnar` při vytváření tabulky:

```postgresql
CREATE TABLE contestant (
    handle TEXT,
    birthdate DATE,
    rating INT,
    percentile FLOAT,
    country CHAR(3),
    achievements TEXT[]
) USING columnar;
```

Citus) převede při vložení řádky na sloupcové úložiště v "prokládaných". Každý pruh obsahuje jednu transakci data nebo 150000 řádků, podle toho, co je méně.  (Velikost pruhu a další parametry sloupcové tabulky lze změnit pomocí funkce [alter_columnar_table_set](reference-hyperscale-functions.md#alter_columnar_table_set) .)

Například následující příkaz vloží všechny pět řádků do stejného pruhu, protože všechny hodnoty jsou vloženy do jedné transakce:

```postgresql
-- insert these values into a single columnar stripe

INSERT INTO contestant VALUES
  ('a','1990-01-10',2090,97.1,'XA','{a}'),
  ('b','1990-11-01',2203,98.1,'XA','{a,b}'),
  ('c','1988-11-01',2907,99.4,'XB','{w,y}'),
  ('d','1985-05-05',2314,98.3,'XB','{}'),
  ('e','1995-05-05',2236,98.2,'XC','{a}');
```

Je nejlepší vytvořit velké pruhy, pokud je to možné, protože Citus komprimuje sloupcová data samostatně na pruzích. K zobrazení informací o naší sloupcové tabulce, jako je kompresní frekvence, počet pruhů a průměrných řádků na jeden svazek, můžete použít `VACUUM VERBOSE` :

```postgresql
VACUUM VERBOSE contestant;
```
```
INFO:  statistics for "contestant":
storage id: 10000000000
total file size: 24576, total data size: 248
compression rate: 1.31x
total row count: 5, stripe count: 1, average rows per stripe: 5
chunk count: 6, containing data for dropped columns: 0, zstd compressed: 6
```

Výstup ukazuje, že Citus používá kompresní algoritmus zstd pro získání komprese dat 1.31 x. Rychlost komprese porovnává a) velikost vložených dat, jak byla provedena v paměti proti b) velikost dat, která jsou komprimována v jejich případném přístupnosti.

Vzhledem k tomu, jak se měří, se míra komprese může nebo nemusí shodovat s rozdílem velikosti mezi řádkem a sloupcovým úložištěm tabulky. Jediným způsobem, jak tento rozdíl skutečně najít, je vytvořit řádek a sloupcovou tabulku, která obsahuje stejná data, a porovnat tyto údaje:

```postgresql
CREATE TABLE contestant_row AS
    SELECT * FROM contestant;

SELECT pg_total_relation_size('contestant_row') as row_size,
       pg_total_relation_size('contestant') as columnar_size;
```
```
 row_size | columnar_size
----------+---------------
    16384 |         24576
```

V naší malé tabulce ve skutečnosti používá sloupcové úložiště více místa, ale při zvětšování dat se komprese podaří.

## <a name="example"></a>Příklad

Sloupcové a tabulkové úložiště funguje s vytvářením oddílů tabulky. Příklad najdete v dokumentaci komunity Citus Engine, [archivace se sloupcovým úložištěm](https://docs.citusdata.com/en/stable/use_cases/timeseries.html#archiving-with-columnar-storage).

## <a name="gotchas"></a>Možná úskalí

* Sloupcové úložiště se komprimuje na pruzích. Pruhy se vytvářejí na transakci, takže vložení jednoho řádku na transakci do jejich vlastních pruhů vloží jednotlivé řádky. Komprese a výkon jednoduchých pruhových svazků bude horší než tabulka řádků. Vždy vkládejte hromadně do sloupcové tabulky.
* Pokud seřadíte a columnarize spoustu drobných pruhů, jste zablokované.
  Jedinou opravou je vytvoření nové sloupcové tabulky a zkopírování dat z originálu v jedné transakci:
  ```postgresql
  BEGIN;
  CREATE TABLE foo_compacted (LIKE foo) USING columnar;
  INSERT INTO foo_compacted SELECT * FROM foo;
  DROP TABLE foo;
  ALTER TABLE foo_compacted RENAME TO foo;
  COMMIT;
  ```
* V podstatě Nekomprimovat data může být problém, přestože sloupcové úložiště je stále užitečné při výběru určitých sloupců. Není nutné načítat ostatní sloupce do paměti.
* U dělené tabulky se smíšenou částmi oddílů řádků a sloupců musí být aktualizace pečlivě cílené. Filtrujte je tak, aby se dosáhly jenom oddílů řádků.
   * Pokud je operace cílena na určitý oddíl řádku (například `UPDATE p2 SET i = i + 1` ), bude úspěšná. Pokud je cílová v zadaném sloupcovém oddílu (například `UPDATE p1 SET i = i + 1` ), nezdaří se.
   * Pokud je operace cílena na dělenou tabulku a má klauzuli WHERE, která vylučuje všechny sloupcové oddíly (například `UPDATE parent SET i = i + 1 WHERE timestamp = '2020-03-15'` ), bude úspěšná.
   * Pokud je operace cílena na dělenou tabulku, ale nefiltruje na sloupce klíče oddílu, dojde k selhání. I v případě, že existují klauzule WHERE, které odpovídají řádkům v pouze sloupcových oddílech, není dostatek. klíč oddílu musí být také filtrován.

## <a name="limitations"></a>Omezení

Tato funkce stále obsahuje řadu významných omezení. Viz omezení [a omezení Citus (škálování na úrovni)](concepts-hyperscale-limits.md#columnar-storage).

## <a name="next-steps"></a>Další kroky

* Podívejte se na příklad sloupcového úložiště v [kurzu Citus časové řady](https://docs.citusdata.com/en/stable/use_cases/timeseries.html#archiving-with-columnar-storage) (externí odkaz).
