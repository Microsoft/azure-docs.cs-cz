---
title: Řešení potíží s výkonem dotazů – Azure Database for MySQL
description: Naučte se používat vysvětlení pro řešení potíží s výkonem dotazů v Azure Database for MySQL.
author: savjani
ms.author: pariks
ms.service: mysql
ms.topic: troubleshooting
ms.date: 3/18/2020
ms.openlocfilehash: d01febec3972dcc26c6e9b5aa8d0c4cca5f32d0a
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/30/2021
ms.locfileid: "105606075"
---
# <a name="how-to-use-explain-to-profile-query-performance-in-azure-database-for-mysql"></a>Jak používat vysvětlení k profilování výkonu dotazů v Azure Database for MySQL
[!INCLUDE[applies-to-single-flexible-server](includes/applies-to-single-flexible-server.md)]

**Vysvětlujeme** užitečný nástroj pro optimalizaci dotazů. Příkaz vysvětlit lze použít k získání informací o tom, jak jsou příkazy jazyka SQL provedeny. Následující výstup ukazuje příklad provedení VYSVĚTLUJÍCÍho příkazu.

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

Jak je vidět v tomto příkladu, hodnota *klíče* je null. Tento výstup znamená, že MySQL nemůže najít žádné indexy optimalizované pro dotaz a provede úplnou kontrolu tabulky. Pojďme tento dotaz optimalizovat přidáním indexu do sloupce **ID** .

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

Nový VYSVĚTLUJE, že MySQL nyní používá index k omezení počtu řádků na 1, což výrazně zkracuje dobu hledání.
 
## <a name="covering-index"></a>Index pokrytí
Index pokrývání se skládá ze všech sloupců dotazu v indexu, aby se snížilo načítání hodnot z tabulek dat. Tady je ilustrace v následujícím příkazu **Group by** .
 
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

Jak je vidět ve výstupu, MySQL nepoužívá žádné indexy, protože nejsou k dispozici žádné správné indexy. Zobrazuje se také jako *dočasné použití; Pomocí řazení souborů*, což znamená, že MySQL vytvoří dočasnou tabulku pro splnění klauzule **Group by** .
 
Vytvoření indexu na samostatném sloupci **C2** neposkytuje žádný rozdíl a MySQL stále potřebuje vytvořit dočasnou tabulku:

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

V tomto případě je možné vytvořit **zahrnutý index** v **C1** i **C2** , přičemž při přidání hodnoty **C2** přímo v indexu se eliminuje další vyhledávání dat.

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

Jak je VYSVĚTLENo výše, MySQL nyní používá zahrnutý index a nevytváří dočasnou tabulku. 

## <a name="combined-index"></a>Kombinovaný index
Kombinovaný index obsahuje hodnoty z více sloupců a lze jej považovat za pole řádků, které jsou seřazeny podle zřetězení hodnot indexovaných sloupců. Tato metoda může být užitečná v příkazu **Group by** .

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

MySQL provádí operaci *řazení souborů* , která je poměrně pomalá, zejména v případě, že je potřeba seřadit mnoho řádků. Pro optimalizaci tohoto dotazu lze vytvořit kombinovaný index v obou sloupcích, které jsou seřazeny.

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

VYSVĚTLENÍ teď ukazuje, že MySQL dokáže pomocí kombinovaného indexu zabránit dalšímu řazení, protože index je už seřazený.
 
## <a name="conclusion"></a>Závěr
 
Použití vysvětlení a různých typů indexů může výrazně zvýšit výkon. Použití indexu v tabulce nutně neznamená, že MySQL by ho mohl použít pro vaše dotazy. Vysvětlete a Optimalizujte své dotazy pomocí indexů.


## <a name="next-steps"></a>Další kroky
- Pokud chcete najít rovnocenné odpovědi na příslušné otázky nebo Odeslat novou otázku nebo odpověď, navštivte [Stack Overflow](https://stackoverflow.com/questions/tagged/azure-database-mysql).
