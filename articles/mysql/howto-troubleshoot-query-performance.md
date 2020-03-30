---
title: Poradce při potížích s výkonem dotazu – Azure Database for MySQL
description: Přečtěte si, jak řešit výkon dotazů v Azure Database for MySQL pomocí funkce EXPLAIN.
author: ajlam
ms.author: andrela
ms.service: mysql
ms.topic: troubleshooting
ms.date: 3/18/2020
ms.openlocfilehash: 6b27e47339b80cc46290065c4d17150a301f2534
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "80067836"
---
# <a name="how-to-use-explain-to-profile-query-performance-in-azure-database-for-mysql"></a>Použití funkce VYSVĚTLIT k profilování výkonu dotazu v Azure Database for MySQL
**Explain** je šikovný nástroj pro optimalizaci dotazů. Příkaz EXPLAIN lze použít k získání informací o způsobu provádění příkazů SQL. Následující výstup ukazuje příklad provádění příkazu EXPLAIN.

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

Jak je vidět z tohoto příkladu, hodnota *klíče* je NULL. Tento výstup znamená, že MySQL nemůže najít žádné indexy optimalizované pro dotaz a provede úplnou tabulku skenování. Pojďme optimalizovat tento dotaz přidáním indexu ve sloupci **ID.**

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

Nové EXPLAIN ukazuje, že MySQL nyní používá index omezit počet řádků na 1, což zase dramaticky zkrátilčas hledání.
 
## <a name="covering-index"></a>Krycí index
Krycí index se skládá ze všech sloupců dotazu v indexu ke snížení načítání hodnoty z tabulek dat. Zde je ilustrace v následujícím **prohlášení GROUP BY.**
 
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

Jak je vidět z výstupu, MySQL nepoužívá žádné indexy, protože nejsou k dispozici žádné správné indexy. Zobrazuje také *použití dočasné; Pomocí řazení souborů*, což znamená, že MySQL vytvoří dočasnou tabulku, která uspokojí klauzuli **GROUP BY.**
 
Vytvoření indexu na sloupec **c2** sám o sobě není žádný rozdíl, a MySQL stále potřebuje vytvořit dočasnou tabulku:

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

V tomto případě lze vytvořit **krytý index** na **c1** a **c2,** čímž se přidá hodnota **c2**" přímo do indexu, aby se eliminovalo další vyhledávání dat.

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

Jak ukazuje výše uvedené vysvětlení, MySQL nyní používá krytý index a vyhněte se vytváření dočasné tabulky. 

## <a name="combined-index"></a>Kombinovaný index
Kombinovaný index se skládá z hodnot z více sloupců a lze je považovat za pole řádků, které jsou seřazeny podle zřetězení hodnot indexovaných sloupců.Tato metoda může být užitečná v příkazu **GROUP BY.**

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

MySQL provádí operaci *řazení souborů,* která je poměrně pomalá, zvláště když má seřadit mnoho řádků. Chcete-li optimalizovat tento dotaz, kombinovaný index lze vytvořit na oba sloupce, které jsou seřazeny.

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

Vysvětlit nyní ukazuje, že MySQL je schopen použít kombinovaný index, aby se zabránilo další řazení, protože index je již seřazeno.
 
## <a name="conclusion"></a>Závěr
 
Použití vysvětlit a různé typy indexů může výrazně zvýšit výkon. S index na stole nemusí nutně znamenat, MySQL by mohli použít pro vaše dotazy. Vždy ověřte své předpoklady pomocí vysvětlit a optimalizovat dotazy pomocí indexů.


## <a name="next-steps"></a>Další kroky
- Chcete-li najít odpovědi na odpovědi na vaše nejzvětšenější otázky nebo zveřejnit novou otázku / odpověď, navštivte [fórum MSDN](https://social.msdn.microsoft.com/forums/security/en-US/home?forum=AzureDatabaseforMySQL) nebo [Přetečení zásobníku](https://stackoverflow.com/questions/tagged/azure-database-mysql).
