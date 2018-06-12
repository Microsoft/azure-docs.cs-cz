---
title: Postup řešení potíží s výkonem dotazu v Azure databáze pro databázi MySQL
description: Tento článek popisuje, jak používat VYSVĚTLIT k řešení potíží s výkonem dotazu v databázi Azure pro databázi MySQL.
services: mysql
author: ajlam
ms.author: andrela
manager: kfile
editor: jasonwhowell
ms.service: mysql
ms.topic: article
ms.date: 02/28/2018
ms.openlocfilehash: 72b047c37ac88e4b33c8723f8df14c6794e84399
ms.sourcegitcommit: 1b8665f1fff36a13af0cbc4c399c16f62e9884f3
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/11/2018
ms.locfileid: "35266172"
---
# <a name="how-to-use-explain-to-profile-query-performance-in-azure-database-for-mysql"></a>Jak používat VYSVĚTLIT k profilu výkon dotazů v databázi Azure pro databázi MySQL
**VYSVĚTLUJÍ** je užitečný nástroj pro optimalizaci dotazů. VYSVĚTLUJÍ, že příkaz lze použít k načtení informací o tom, jak jsou vykonány příkazů SQL. Tento výstup ukazuje příklad spuštění příkazu VYSVĚTLIT.

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

Jak je vidět z tohoto příkladu, hodnota *klíč* má hodnotu NULL. Tento výstup znamená MySQL nemůže najít žádné indexy optimalizované pro dotaz a provádí kontrolu úplné tabulky. Umožňuje optimalizovat tento dotaz přidáním indexu na **ID** sloupce.

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

Nové VYSVĚTLIT ukazuje, že MySQL teď používá index omezit počet řádků na 1, který pak výrazně zkrátit čas potřebný k vyhledávání.
 
## <a name="covering-index"></a>Zahrnut indexu
Index zahrnut se skládá z všechny sloupce v indexu ke snížení načtení hodnoty z tabulky dat dotazu. Tady je obrázek v následujícím **GROUP BY** příkaz.
 
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

Jak je vidět z výstupu, MySQL nepoužívá žádné indexy protože nejsou k dispozici žádné indexy. správné. Také ukazuje *pomocí dočasného; Pomocí souboru řazení*, což znamená MySQL vytvoří dočasné tabulky pro uspokojení **GROUP BY** klauzule.
 
Vytvoření indexu na sloupec **c2** samostatně díky žádný rozdíl a databáze MySQL stále potřebuje k vytvoření dočasné tabulky:

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

V takovém případě **zahrnuté index** na obou **c1** a **c2** mohou být vytvořeny, jímž přidání hodnotu **c2**"přímo v indexu pro Eliminujte další data vyhledávání.

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

Jak ukazuje výše VYSVĚTLIT, MySQL teď používá zahrnuté index a vyhněte se vytváření dočasné tabulky. 

## <a name="combined-index"></a>Kombinovaná indexu
Kombinovaná index obsahuje hodnoty z více sloupců a lze považovat za pole řádků, které jsou seřazené podle zřetězení hodnoty indexovaného sloupce. Tato metoda může být užitečné při **GROUP BY** příkaz.

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

Provede MySQL *řazení souborů* operace, která je docela pomalý, zvlášť pokud je k seřazení mnoho řádků. K optimalizaci tento dotaz, lze vytvořit kombinované indexu na oba sloupce, které jsou právě seřazeny.

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

VYSVĚTLIT nyní ukazuje, že je možné použít kombinované index předejdete další řazení, protože index je už seřazený MySQL.
 
## <a name="conclusion"></a>Závěr
 
Pomocí VYSVĚTLIT a jiný typ indexů zvýšit výkon výrazně. Právě, protože máte index na tabulce však nemusí znamenat, že by bylo možné používat pro své dotazy MySQL. Vždy ověření předpokladů pomocí VYSVĚTLIT a optimalizovat své dotazy pomocí indexů.


## <a name="next-steps"></a>Další postup
- Vyhledání partnera odpovědí na nejvíce dotčených dotazy nebo nové otázek a odpovědí, najdete [fórum MSDN](https://social.msdn.microsoft.com/forums/security/en-US/home?forum=AzureDatabaseforMySQL) nebo [Stack Overflow](https://stackoverflow.com/questions/tagged/azure-database-mysql).
