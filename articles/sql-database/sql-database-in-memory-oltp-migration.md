---
title: V paměti OLTP zlepšuje SQL txn perf
description: Přijmout V paměti OLTP zlepšit transakční výkon v existující databázi SQL.
services: sql-database
ms.service: sql-database
ms.subservice: development
ms.topic: conceptual
author: stevestein
ms.author: sstein
ms.reviewer: MightyPen
ms.date: 11/07/2018
ms.openlocfilehash: 653ed75341d5d56ecbe06cb59f0efafa1e68aa0f
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "80067270"
---
# <a name="use-in-memory-oltp-to-improve-your-application-performance-in-sql-database"></a>Zlepšení výkonu aplikací v databázi SQL Database pomocí oltp v paměti

[Oltp v paměti](sql-database-in-memory.md) lze použít ke zlepšení výkonu zpracování transakcí, ingestování dat a přechodných datových scénářů v databázích [úrovně Premium a Business Critical](sql-database-service-tiers-vcore.md) bez zvýšení cenové úrovně. 

> [!NOTE] 
> Zjistěte, jak [kvorum zdvojnásobuje úlohu databáze klíčů a zároveň snižuje DTU o 70 % díky databázi SQL Database](https://customers.microsoft.com/story/quorum-doubles-key-databases-workload-while-lowering-dtu-with-sql-database)


Následujícím postupem můžete přijmout v existující databázi protokol OLTP v paměti.

## <a name="step-1-ensure-you-are-using-a-premium-and-business-critical-tier-database"></a>Krok 1: Ujistěte se, že používáte databázi úrovně Premium a Business Critical

Oltp v paměti je podporován pouze v databázích úrovně Premium a Business Critical. In-Memory je podporována, pokud je vrácený výsledek 1 (ne 0):

```
SELECT DatabasePropertyEx(Db_Name(), 'IsXTPSupported');
```

*XTP* je *zkratka* pro Extreme Transaction Processing



## <a name="step-2-identify-objects-to-migrate-to-in-memory-oltp"></a>Krok 2: Identifikace objektů, které mají být migrovány do oltp v paměti
SSMS obsahuje sestavu **přehledu analýzy výkonu transakcí,** kterou lze spustit proti databázi s aktivníúlohou. Sestava identifikuje tabulky a uložené procedury, které jsou kandidáty pro migraci do OLTP v paměti.

V SSMS, generovat zprávu:

* V **Průzkumníkovi objektů**klepněte pravým tlačítkem myši na uzel databáze.
* Klepněte na **položku Přehled** > **analýzy výkonu transakcí**se**stavy se stavy** > .

Další informace naleznete [v tématu Určení, zda by tabulka nebo uložená procedura měla být přenesena do oltp v paměti](https://msdn.microsoft.com/library/dn205133.aspx).

## <a name="step-3-create-a-comparable-test-database"></a>Krok 3: Vytvoření srovnatelné testovací databáze
Předpokládejme, že sestava označuje, že databáze obsahuje tabulku, která by měla prospěch z převodu na tabulku optimalizovanou pro paměť. Doporučujeme, abyste nejprve test potvrdit indikaci testováním.

Potřebujete zkušební kopii produkční databáze. Testovací databáze by měla být na stejné úrovni úrovně služeb jako produkční databáze.

Chcete-li usnadnit testování, vyladit testovací databázi takto:

1. Připojte se k testovací databázi pomocí SSMS.
2. Chcete-li se vyhnout nutnosti možnost i s (SNÍMEK) v dotazech, nastavte možnost databáze, jak je znázorněno v následujícím příkazu T-SQL:
   
   ```
   ALTER DATABASE CURRENT
    SET
        MEMORY_OPTIMIZED_ELEVATE_TO_SNAPSHOT = ON;
   ```

## <a name="step-4-migrate-tables"></a>Krok 4: Migrace tabulek
Je nutné vytvořit a naplnit kopii tabulky optimalizovanou pro paměť, kterou chcete testovat. Můžete jej vytvořit pomocí:

* Praktický průvodce optimalizací paměti v SSMS.
* Ruční T-SQL.

#### <a name="memory-optimization-wizard-in-ssms"></a>Průvodce optimalizací paměti v SSMS
Použití této možnosti migrace:

1. Připojte se k testovací databázi pomocí SSMS.
2. V **Průzkumníkovi objektů**klepněte pravým tlačítkem myši na tabulku a potom klepněte na příkaz **Poradce pro optimalizaci paměti**.
   
   * Zobrazí se Průvodce **Poradce pro optimalizaci paměti tabulky.**
3. V průvodci klikněte na **ověření migrace** (nebo tlačítko **Další)** a zjistěte, zda tabulka obsahuje nějaké nepodporované funkce, které nejsou podporovány v tabulkách optimalizovaných pro paměť. Další informace naleznete v tématu:
   
   * *Kontrolní seznam optimalizace paměti* v [Poradci pro optimalizaci paměti](https://msdn.microsoft.com/library/dn284308.aspx).
   * [Konstrukce Transact-SQL nejsou podporovány v paměti OLTP](https://msdn.microsoft.com/library/dn246937.aspx).
   * [Migrace na oltp v paměti](https://msdn.microsoft.com/library/dn247639.aspx).
4. Pokud tabulka nemá žádné nepodporované funkce, může poradce provést skutečné schéma a migraci dat za vás.

#### <a name="manual-t-sql"></a>Ruční T-SQL
Použití této možnosti migrace:

1. Připojte se k testovací databázi pomocí SSMS (nebo podobného nástroje).
2. Získejte kompletní Skript T-SQL pro tabulku a její indexy.
   
   * V SSMS klikněte pravým tlačítkem myši na uzel tabulky.
   * Klepněte na **položku Tabulka skriptů jako** > **vytvořit do** > **nového okna dotazu**.
3. V okně skriptu přidejte WITH (MEMORY_OPTIMIZED = ON) do příkazu CREATE TABLE.
4. Pokud je clusterovaný index, změňte jej na NESESKUPENÝ.
5. Přejmenujte existující tabulku pomocí SP_RENAME.
6. Vytvořte novou paměťově optimalizovanou kopii tabulky spuštěním upraveného skriptu CREATE TABLE.
7. Zkopírujte data do tabulky optimalizované pro paměť pomocí funkce INSERT... VYBERTE * DO:

```
INSERT INTO <new_memory_optimized_table>
        SELECT * FROM <old_disk_based_table>;
```


## <a name="step-5-optional-migrate-stored-procedures"></a>Krok 5 (volitelné): Migrace uložených procedur
Funkce V paměti můžete také upravit uloženou proceduru pro zlepšení výkonu.

### <a name="considerations-with-natively-compiled-stored-procedures"></a>Důležité informace s nativně zkompilované uložené procedury
Nativně zkompilované uložené procedury musí mít následující možnosti na jeho T-SQL WITH klauzule:

* NATIVE_COMPILATION
* SCHEMABINDING: což znamená, že tabulky, které uložená procedura nemůže mít jejich definice sloupců změnit žádným způsobem, který by ovlivnil uloženou proceduru, pokud nepřepustíte uloženou proceduru.

Nativní modul musí používat jeden velký [atomic bloky](https://msdn.microsoft.com/library/dn452281.aspx) pro správu transakcí. Neexistuje žádná role pro explicitní BEGIN transakce nebo pro transakci VRÁCENÍ ZPĚT. Pokud váš kód zjistí porušení obchodního pravidla, může ukončit atomový blok s [příkazem THROW.](https://msdn.microsoft.com/library/ee677615.aspx)

### <a name="typical-create-procedure-for-natively-compiled"></a>Typický postup VYTVOŘENÍ pro nativně zkompilované
Obvykle T-SQL vytvořit nativně kompilované uložené procedury je podobný následující šablony:

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

* Pro TRANSACTION_ISOLATION_LEVEL SNAPSHOT je nejběžnější hodnota pro nativně zkompilované uložené procedury. Podporována je však také podmnožina ostatních hodnot:
  
  * OPAKOVATELNÉ ČTENÍ
  * Serializovatelný
* Hodnota LANGUAGE musí být k dispozici v zobrazení sys.languages.

### <a name="how-to-migrate-a-stored-procedure"></a>Jak migrovat uloženou proceduru
Kroky migrace jsou:

1. Získejte skript CREATE PROCEDURE do pravidelně interpretované uložené procedury.
2. Přepište jeho záhlaví tak, aby odpovídalo předchozí šabloně.
3. Zjišťte, zda uložená procedura T-SQL kód používá všechny funkce, které nejsou podporovány pro nativně kompilované uložené procedury. V případě potřeby implementujte zástupná řešení.
   
   * Podrobnosti naleznete v [tématu Problémy s migrací pro nativně zkompilované uložené procedury](https://msdn.microsoft.com/library/dn296678.aspx).
4. Přejmenujte starou uloženou proceduru pomocí SP_RENAME. Nebo prostě drop to.
5. Spusťte upravený skript CREATE PROCEDURE T-SQL.

## <a name="step-6-run-your-workload-in-test"></a>Krok 6: Spuštění úlohy v testu
Spusťte úlohu v testovací databázi, která je podobná úlohě, která běží v produkční databázi. To by mělo odhalit zvýšení výkonu dosažené použitím funkce V paměti pro tabulky a uložené procedury.

Hlavní atributy pracovní ho zatížení jsou:

* Počet souběžných připojení.
* Poměr čtení a zápisu.

Chcete-li přizpůsobit a spustit testovací úlohy, zvažte použití nástroje handy ostress.exe, který je znázorněn [zde](sql-database-in-memory.md).

Chcete-li minimalizovat latenci sítě, spusťte test ve stejné geografické oblasti Azure, kde databáze existuje.

## <a name="step-7-post-implementation-monitoring"></a>Krok 7: Monitorování po provedení
Zvažte sledování účinků výkonu implementací v paměti v produkčním prostředí:

* [Monitor v paměti](sql-database-in-memory-oltp-monitoring.md).
* [Monitorování databáze Azure SQL Database pomocí zobrazení dynamické správy](sql-database-monitoring-with-dmvs.md)

## <a name="related-links"></a>Související odkazy
* [Oltp v paměti (optimalizace v paměti)](https://msdn.microsoft.com/library/dn133186.aspx)
* [Úvod do nativně kompilovaných uložených procedur](https://msdn.microsoft.com/library/dn133184.aspx)
* [Poradce pro optimalizaci paměti](https://msdn.microsoft.com/library/dn284308.aspx)

