---
title: OLTP v paměti zlepšuje SQL txn výkonu | Microsoft Docs
description: Přijímat OLTP v paměti pro zlepšení výkonu transakcí v existující databázi SQL.
services: sql-database
author: jodebrui
manager: craigg
ms.reviewer: MightyPen
ms.service: sql-database
ms.custom: develop databases
ms.topic: article
ms.date: 04/01/2018
ms.author: jodebrui
ms.openlocfilehash: cd76f475f330c49dec737d2c4a25aa75a18b41a7
ms.sourcegitcommit: 3a4ebcb58192f5bf7969482393090cb356294399
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/06/2018
---
# <a name="use-in-memory-oltp-to-improve-your-application-performance-in-sql-database"></a>Použití OLTP v paměti pro zlepšení výkonu vaší aplikace v databázi SQL
[OLTP v paměti](sql-database-in-memory.md) lze zlepšit výkon zpracování transakcí, přijímání dat a scénáře přechodný dat v [úroveň Premium a kritické obchodní](sql-database-service-tiers.md) databází bez zvýšení cenovou úroveň. 

> [!NOTE] 
> Zjistěte, jak [kvora zdvojnásobí klíče databáze zatížení při snížení DTU 70 % s databází SQL](https://customers.microsoft.com/story/quorum-doubles-key-databases-workload-while-lowering-dtu-with-sql-database)


Postupujte podle těchto kroků přijmout OLTP v paměti v existující databázi.

## <a name="step-1-ensure-you-are-using-a-premium-and-business-critical-tier-database"></a>Krok 1: Ujistěte se, že používáte databáze Premium a kritické obchodní vrstvy
OLTP v paměti je podporována pouze v databázích Premium a kritické obchodní vrstvy. V paměti je podporována, pokud je výsledek vrácený 1 (ne 0):

```
SELECT DatabasePropertyEx(Db_Name(), 'IsXTPSupported');
```

*XTP* znamená *extrémně zpracování transakcí*



## <a name="step-2-identify-objects-to-migrate-to-in-memory-oltp"></a>Krok 2: Určení objekty, které chcete migrovat do OLTP v paměti
Zahrnuje SSMS **přehled analýzy výkonu transakce** sestavy, který můžete spustit na databázi s aktivní úlohu. Sestava identifikuje tabulky a uložené procedury, které jsou kandidáty pro migraci OLTP v paměti.

V aplikaci SSMS pro generování sestavy:

* V **Průzkumník objektů**, klikněte pravým tlačítkem na uzel vaší databáze.
* Klikněte na tlačítko **sestavy** > **standardních sestav** > **přehled analýzy výkonu transakce**.

Další informace najdete v tématu [stanovení u tabulek a uložené procedury by měl být přesně do OLTP v paměti](http://msdn.microsoft.com/library/dn205133.aspx).

## <a name="step-3-create-a-comparable-test-database"></a>Krok 3: Vytvoření databáze porovnatelný z hlediska testu
Předpokládejme, že sestava indikuje, že vaše databáze má tabulku, která by využívat převáděn na paměťově optimalizované tabulky. Doporučujeme, abyste nejdřív otestovali k testování potvrzení informacemi.

Je třeba testovací kopii vaši produkční databázi. Testovací databáze by měla být na stejné úrovni vrstvy služby jako vaši produkční databázi.

K usnadnění, testování, upravit testovací databáze následujícím způsobem:

1. Připojení k databázi test pomocí aplikace SSMS.
2. Abyste se vyhnuli nutnosti možnost WITH (SNAPSHOT) v dotazech, nastavte možnost databáze, jak je znázorněno v následujícím příkazu T-SQL:
   
   ```
   ALTER DATABASE CURRENT
    SET
        MEMORY_OPTIMIZED_ELEVATE_TO_SNAPSHOT = ON;
   ```

## <a name="step-4-migrate-tables"></a>Krok 4: Migrace tabulky
Musíte vytvořit a naplnit kopii paměťově optimalizované tabulky, kterou chcete testovat. Můžete ho vytvořit pomocí:

* Užitečný průvodce optimalizace paměti v aplikaci SSMS.
* Ruční T-SQL.

#### <a name="memory-optimization-wizard-in-ssms"></a>Průvodce optimalizace paměti v aplikaci SSMS
Použití této možnosti migrace:

1. Připojení k databázi test pomocí SSMS.
2. V **Průzkumník objektů**, klikněte pravým tlačítkem na tabulky a pak klikněte na tlačítko **Advisor optimalizace paměti**.
   
   * **Tabulky paměti Optimalizátor Advisor** průvodce se zobrazí.
3. V průvodci klikněte na tlačítko **ověření migrace** (nebo **Další** tlačítko) Chcete-li zobrazit, pokud tabulka obsahuje všechny nepodporované funkce, které nejsou podporovány v paměťově optimalizovaných tabulkách. Další informace naleznete v tématu:
   
   * *Kontrolní seznam optimalizace paměti* v [Advisor optimalizace paměti](http://msdn.microsoft.com/library/dn284308.aspx).
   * [Konstrukce Transact-SQL nepodporuje OLTP v paměti](http://msdn.microsoft.com/library/dn246937.aspx).
   * [Migrace na OLTP v paměti](http://msdn.microsoft.com/library/dn247639.aspx).
4. Pokud tabulka nemá žádné nepodporované funkce, advisor můžete provádět skutečné schéma a migraci dat za vás.

#### <a name="manual-t-sql"></a>Ruční T-SQL
Použití této možnosti migrace:

1. Připojte k vaší databázi test pomocí aplikace SSMS (nebo podobného nástroje).
2. Získáte dokončení skriptu T-SQL pro tabulku a jeho indexů.
   
   * V aplikaci SSMS klikněte pravým tlačítkem na uzel vaší tabulky.
   * Klikněte na tlačítko **skript tabulky jako** > **vytvořit** > **nové okno dotazu**.
3. V okně skriptu přidat WITH (hodnotou MEMORY_OPTIMIZED = ON) k příkazu CREATE TABLE.
4. Pokud je index na clusteru, změňte jej na NONCLUSTERED.
5. Pomocí procedura SP_RENAME přejmenujte existující tabulce.
6. Vytvořte novou kopii paměťově optimalizované tabulky pomocí upravená CREATE TABLE skriptu.
7. Zkopírujte data do vaší paměťově optimalizované tabulky pomocí INSERT... VYBERTE * DO:

```
INSERT INTO <new_memory_optimized_table>
        SELECT * FROM <old_disk_based_table>;
```


## <a name="step-5-optional-migrate-stored-procedures"></a>Krok 5 (volitelné): migrace uložené procedury
Funkce v paměti můžete také upravit uložené procedury pro zlepšení výkonu.

### <a name="considerations-with-natively-compiled-stored-procedures"></a>Informace o nativně kompilovaných uložených procedur
Nativně kompilované uložené procedury musí mít na jeho klauzule T-SQL pomocí následujících možností:

* MOŽNOST NATIVE_COMPILATION
* Svázání se SCHÉMATEM: znamená tabulek, které uložené procedury nelze mít jejich definice sloupců změnit žádným způsobem, který by došlo k ovlivnění uložené procedury, dokud se vyřadit uložené procedury.

Nativní modul musí používat jednu velký [ATOMICKÝCH bloků](http://msdn.microsoft.com/library/dn452281.aspx) pro správu transakce. Neexistuje žádný atribut role pro explicitní BEGIN TRANSACTION, nebo pro vrácení transakce zpět. Pokud váš kód zjistí narušení obchodní pravidlo, je ukončen Atomický blok s [THROW](http://msdn.microsoft.com/library/ee677615.aspx) příkaz.

### <a name="typical-create-procedure-for-natively-compiled"></a>Typické CREATE PROCEDURE pro nativně kompilované
Obvykle je podobná následující šablony T-SQL k vytvoření nativně kompilované uložené procedury:

```
CREATE PROCEDURE schemaname.procedurename
    @param1 type1, …
    WITH NATIVE_COMPILATION, SCHEMABINDING
    AS
        BEGIN ATOMIC WITH
            (TRANSACTION ISOLATION LEVEL = SNAPSHOT,
            LANGUAGE = N'your_language__see_sys.languages'
            )
        …
        END;
```

* Pro TRANSACTION_ISOLATION_LEVEL je SNÍMEK nejběžnější hodnota pro nativně kompilované uložené procedury. Nicméně jsou podporovány také podmnožinu ostatní hodnoty:
  
  * REPEATABLE READ
  * SERIALIZOVATELNÉ
* Hodnotu jazyka musí být v sys.languages zobrazení.

### <a name="how-to-migrate-a-stored-procedure"></a>Postup migrace uložené procedury
Kroky migrace jsou:

1. Získáte skript CREATE PROCEDURE regulární interpretovaný uložené procedury.
2. Přepisování jeho hlavičky k porovnání shody předchozí šablony.
3. Ověření, zda uložené procedury kód T-SQL používá všechny funkce, které nejsou podporovány pro nativně kompilované uložené procedury. Implementace řešení, v případě potřeby.
   
   * Podrobnosti najdete v tématu [problémy s migrací pro nativně kompilované uložené procedury](http://msdn.microsoft.com/library/dn296678.aspx).
4. Přejmenujte staré uložené procedury pomocí procedura SP_RENAME. Nebo jednoduše ho VYŘADIT.
5. Spusťte skript upravená vytvořit postup T-SQL.

## <a name="step-6-run-your-workload-in-test"></a>Krok 6: Spuštění úlohy v testu
Spusťte úlohu v vaše testovací databázi, která je podobná se zatížením, které běží v produkční databázi. To by měl odhalit výkonnější dosáhnout používání funkce v paměti pro tabulky a uložené procedury.

Hlavní atributy pro úlohy, jsou:

* Počet souběžných připojení.
* Poměr pro čtení a zápis.

Pokud chcete přizpůsobit a spustit testovací úlohy, zvažte použití užitečný ostress.exe nástroj, který ukazuje [zde](sql-database-in-memory.md).

Pro minimalizaci latence sítě, spusťte test ve stejné geografické oblasti Azure kde databázi existuje.

## <a name="step-7-post-implementation-monitoring"></a>Krok 7: Po implementaci monitorování
Vezměte v úvahu monitorování výkonu důsledky vaší implementace v paměti v produkčním prostředí:

* [Monitorování úložiště v paměti](sql-database-in-memory-oltp-monitoring.md).
* [Monitorování databáze Azure SQL Database pomocí zobrazení dynamické správy](sql-database-monitoring-with-dmvs.md)

## <a name="related-links"></a>Související odkazy
* [Paměť OLTP (v paměti optimalizace)](http://msdn.microsoft.com/library/dn133186.aspx)
* [Úvod do nativně kompilovaných uložených procedur](http://msdn.microsoft.com/library/dn133184.aspx)
* [Advisor optimalizace paměti](http://msdn.microsoft.com/library/dn284308.aspx)

