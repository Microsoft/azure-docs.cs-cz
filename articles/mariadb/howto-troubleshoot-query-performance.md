---
title: Řešení potíží s výkonem dotazů ve službě Azure Database pro MariaDB
description: Tento článek popisuje postup řešení potíží s výkonem dotazů ve službě Azure Database pro MariaDB pomocí VYSVĚTLIT.
author: ajlam
ms.author: andrela
ms.service: mariadb
ms.topic: conceptual
ms.date: 11/09/2018
ms.openlocfilehash: 672635c8d8c84fa16c106ae79e97332fd740928d
ms.sourcegitcommit: 21466e845ceab74aff3ebfd541e020e0313e43d9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/21/2018
ms.locfileid: "53541077"
---
# <a name="how-to-use-explain-to-profile-query-performance-in-azure-database-for-mariadb"></a>Jak používat VYSVĚTLIT profil výkonu dotazů ve službě Azure Database pro MariaDB
**VYSVĚTLUJÍ** je užitečný nástroj pro optimalizaci dotazů. VYSVĚTLUJE, že příkaz je možné získat informace o tom, jak jsou spouštěny příkazy SQL. Následující výstup je příkladem spuštění příkazu VYSVĚTLIT.

```sql
mysql> EXPLAIN SELECT * FROM tb1 WHERE id=100\G
*************************** 1. row ***************************
           id: 1
  select_type: SIMPLE
        table: tb1
   partitions: NULL
         type: ALL
possible_keys: NULL
          key: NULL
      key_len: NULL
          ref: NULL
         rows: 995789
     filtered: 10.00
        Extra: Using where
```

Jak je vidět v tomto příkladu, hodnota proměnné *klíč* má hodnotu NULL. Tento výstup znamená, že MariaDB nelze najít žádné indexy optimalizované pro dotaz a provede ke skenování celé tabulky. Pojďme optimalizovat tak, že přidáte indexu na tento dotaz **ID** sloupce.

```sql
mysql> ALTER TABLE tb1 ADD KEY (id);
mysql> EXPLAIN SELECT * FROM tb1 WHERE id=100\G
*************************** 1. row ***************************
           id: 1
  select_type: SIMPLE
        table: tb1
   partitions: NULL
         type: ref
possible_keys: id
          key: id
      key_len: 4
          ref: const
         rows: 1
     filtered: 100.00
        Extra: NULL
```

Nové VYSVĚTLIT ukazuje, že MariaDB nyní používá index omezit počet řádků, které mají 1, což zase výrazně zkrátit čas hledání.
 
## <a name="covering-index"></a>Pokrývající indexu
Pokrytí indexu se skládá z dotazu v indexu ke snížení načtení hodnoty z tabulky dat všechny sloupce. Tady je v následující ilustraci **Group** příkazu.
 
```sql
mysql> EXPLAIN SELECT MAX(c1), c2 FROM tb1 WHERE c2 LIKE '%100' GROUP BY c1\G
*************************** 1. row ***************************
           id: 1
  select_type: SIMPLE
        table: tb1
   partitions: NULL
         type: ALL
possible_keys: NULL
          key: NULL
      key_len: NULL
          ref: NULL
         rows: 995789
     filtered: 11.11
        Extra: Using where; Using temporary; Using filesort
```

Jak je vidět z výstupu, není použít MariaDB žádné indexy, protože nejsou k dispozici žádné správné indexy. Profil také ukazuje *pomocí dočasného; Pomocí souboru řazení*, což znamená, že MariaDB vytvoří dočasnou tabulku tím se uspokojí **Group** klauzuli.
 
Vytvoření indexu pro sloupec **c2** samostatně neposkytuje žádný rozdíl a MariaDB stále potřebuje k vytvoření dočasné tabulky:

```sql 
mysql> ALTER TABLE tb1 ADD KEY (c2);
mysql> EXPLAIN SELECT MAX(c1), c2 FROM tb1 WHERE c2 LIKE '%100' GROUP BY c1\G
*************************** 1. row ***************************
           id: 1
  select_type: SIMPLE
        table: tb1
   partitions: NULL
         type: ALL
possible_keys: NULL
          key: NULL
      key_len: NULL
          ref: NULL
         rows: 995789
     filtered: 11.11
        Extra: Using where; Using temporary; Using filesort
```

V tomto případě **zahrnuté index** u obou **c1** a **c2** mohou být vytvořeny, kterým přidáte hodnotu **c2**"přímo v indexu s Eliminujte další data vyhledávání.

```sql 
mysql> ALTER TABLE tb1 ADD KEY covered(c1,c2);
mysql> EXPLAIN SELECT MAX(c1), c2 FROM tb1 WHERE c2 LIKE '%100' GROUP BY c1\G
*************************** 1. row ***************************
           id: 1
  select_type: SIMPLE
        table: tb1
   partitions: NULL
         type: index
possible_keys: covered
          key: covered
      key_len: 108
          ref: NULL
         rows: 995789
     filtered: 11.11
        Extra: Using where; Using index
```

Jak ukazuje výše VYSVĚTLIT, MariaDB nyní používá zahrnuté index a vyhněte se vytváření dočasné tabulky. 

## <a name="combined-index"></a>Kombinované indexu
Kombinované indexu obsahuje hodnoty z více sloupců a lze považovat za pole řádků, které jsou seřazené zřetězením hodnoty sloupce s fulltextovými indexy. Tato metoda může být užitečné při **Group** příkazu.

```sql
mysql> EXPLAIN SELECT c1, c2 from tb1 WHERE c2 LIKE '%100' ORDER BY c1 DESC LIMIT 10\G
*************************** 1. row ***************************
           id: 1
  select_type: SIMPLE
        table: tb1
   partitions: NULL
         type: ALL
possible_keys: NULL
          key: NULL
      key_len: NULL
          ref: NULL
         rows: 995789
     filtered: 11.11
        Extra: Using where; Using filesort
```

Provádí MariaDB *řazení souborů* operace, která je velmi pomalé, zejména když má řazení mnoho řádků. Pokud chcete optimalizovat tento dotaz, lze vytvořit kombinované index na oba sloupce, které jsou právě seřazeny.

```sql 
mysql> ALTER TABLE tb1 ADD KEY my_sort2 (c1, c2);
mysql> EXPLAIN SELECT c1, c2 from tb1 WHERE c2 LIKE '%100' ORDER BY c1 DESC LIMIT 10\G
*************************** 1. row ***************************
           id: 1
  select_type: SIMPLE
        table: tb1
   partitions: NULL
         type: index
possible_keys: NULL
          key: my_sort2
      key_len: 108
          ref: NULL
         rows: 10
     filtered: 11.11
        Extra: Using where; Using index
```

VYSVĚTLIT teď zobrazuje, že je možné použít kombinované index další řazení, protože index je už seřazený, aby MariaDB.
 
## <a name="conclusion"></a>Závěr
 
Pomocí VYSVĚTLIT a jiný typ indexy zvýšit výkon výrazně. Pouze z důvodu máte indexu v tabulce neznamená, že MariaDB bylo by možné ho použít pro vaše dotazy. Vždy ověření předpokladů pomocí VYSVĚTLIT a optimalizaci dotazů pomocí indexů.

## <a name="next-steps"></a>Další postup
- Najít partnera odpovědi na své otázky oblasti vašeho zájmu nebo odeslat nové otázek a odpovědí, najdete v tématu [fórum na webu MSDN](https://social.msdn.microsoft.com/Forums/en-US/home?forum=AzureDatabaseforMariadb) nebo [Stack Overflow](https://stackoverflow.com/questions/tagged/azure-database-mariadb).
