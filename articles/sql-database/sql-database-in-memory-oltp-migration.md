---
title: OLTP v paměti zlepšuje výkon transakcí SQL | Dokumentace Microsoftu
description: Přijmout OLTP v paměti pro zvýšení výkonu transakcí ve stávající databázi SQL.
services: sql-database
ms.service: sql-database
ms.subservice: development
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: jodebrui
ms.author: jodebrui
ms.reviewer: MightyPen
manager: craigg
ms.date: 11/07/2018
ms.openlocfilehash: fbe05186b317d3c24dca55197c2989155b5543bd
ms.sourcegitcommit: ba035bfe9fab85dd1e6134a98af1ad7cf6891033
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/01/2019
ms.locfileid: "55565917"
---
# <a name="use-in-memory-oltp-to-improve-your-application-performance-in-sql-database"></a>OLTP v paměti použijte, chcete-li zvýšit výkon vašich aplikací ve službě SQL Database

[OLTP v paměti](sql-database-in-memory.md) slouží ke zvýšení výkonu při zpracování transakcí, ingestování a případech přechodných dat v [úrovně Premium a pro důležité obchodní informace](sql-database-service-tiers-vcore.md) databází bez zvýšení cenové úrovně. 

> [!NOTE] 
> Zjistěte, jak [kvorum zdvojnásobuje klíčové databázové úlohy při současném snižování DTU o 70 % službou SQL Database](https://customers.microsoft.com/story/quorum-doubles-key-databases-workload-while-lowering-dtu-with-sql-database)


Následujícím postupem implementace OLTP v paměti v existující databázi.

## <a name="step-1-ensure-you-are-using-a-premium-and-business-critical-tier-database"></a>Krok 1: Ujistěte se, že používáte databáze úrovně Premium a pro důležité obchodní informace

OLTP v paměti je podporováno pouze v databázích úrovně Premium a pro důležité obchodní informace. V paměti je podporováno, pokud vrácený výsledek je 1 (není 0):

```
SELECT DatabasePropertyEx(Db_Name(), 'IsXTPSupported');
```

*XTP* zastupuje *extrémní zpracování transakcí*



## <a name="step-2-identify-objects-to-migrate-to-in-memory-oltp"></a>Krok 2: Identifikace objektů pro migraci OLTP v paměti
SSMS zahrnuje **přehled analýzy výkonu transakce** sestavu, kterou můžete spouštět proti databázi s aktivní úlohy. Sestava identifikuje tabulky a uložené procedury, které jsou kandidáty pro migraci do OLTP v paměti.

V aplikaci SSMS, generování sestav:

* V **Průzkumník objektů**, klikněte pravým tlačítkem na uzel vaší databáze.
* Klikněte na tlačítko **sestavy** > **standardních sestav** > **přehled analýzy výkonu transakce**.

Další informace najdete v tématu [stanovení, pokud tabulka nebo uložené procedury by měl být přenést do OLTP v paměti](https://msdn.microsoft.com/library/dn205133.aspx).

## <a name="step-3-create-a-comparable-test-database"></a>Krok 3: Vytvoření databáze srovnatelné testu
Předpokládejme, že sestava označuje, že vaše databáze má tabulka, která je výhodná převáděn na paměťově optimalizované tabulky. Doporučujeme nejprve vyzkoušet k potvrzení o testování.

Potřebujete zkušební kopii vaši produkční databázi. Test databáze by měla být na stejné úrovni úroveň služby jako produkční databázi.

K usnadnění, testování, upravit testovací databázi následujícím způsobem:

1. Připojení k databázi testu s použitím aplikace SSMS.
2. Abyste nemuseli možnost WITH (SNAPSHOT) v dotazech, nastavte možnost databáze, jak je znázorněno v následujícím příkazu T-SQL:
   
   ```
   ALTER DATABASE CURRENT
    SET
        MEMORY_OPTIMIZED_ELEVATE_TO_SNAPSHOT = ON;
   ```

## <a name="step-4-migrate-tables"></a>Krok 4: Migrace tabulky
Musíte vytvořit a naplnit kopii paměťově optimalizované tabulky, ve které chcete testovat. Můžete ho vytvořit pomocí:

* Po ruce Průvodce optimalizace paměti v aplikaci SSMS.
* Ruční T-SQL.

#### <a name="memory-optimization-wizard-in-ssms"></a>Průvodce optimalizace paměti v aplikaci SSMS
Použití této možnosti migrace:

1. Připojení k testovací databázi pomocí SSMS.
2. V **Průzkumník objektů**, klikněte pravým tlačítkem na tabulku a pak klikněte na tlačítko **Advisor optimalizace paměti**.
   
   * **Advisor optimalizace paměti tabulky** průvodce se zobrazí.
3. V průvodci klikněte na tlačítko **ověření migrace** (nebo **Další** tlačítko) Chcete-li zobrazit, jestli tabulka obsahuje všechny nepodporované funkce, které nejsou podporovány v paměťově optimalizovaných tabulkách. Další informace naleznete v tématu:
   
   * *Kontrolní seznam optimalizace paměti* v [Advisor optimalizace paměti](https://msdn.microsoft.com/library/dn284308.aspx).
   * [Konstrukce jazyka Transact-SQL nepodporuje OLTP v paměti](https://msdn.microsoft.com/library/dn246937.aspx).
   * [Migrace na OLTP v paměti](https://msdn.microsoft.com/library/dn247639.aspx).
4. Pokud tabulka nemá žádné nepodporované funkce, Poradce pro vás provádět skutečné schéma a data migrace.

#### <a name="manual-t-sql"></a>Ruční T-SQL
Použití této možnosti migrace:

1. Připojení k testovací databázi pomocí aplikace SSMS (nebo podobného nástroje).
2. Získáte celý skript T-SQL pro tabulky a jeho indexy.
   
   * V aplikaci SSMS klikněte pravým tlačítkem na uzel tabulky.
   * Klikněte na tlačítko **skriptu tabulku jako** > **vytvořit** > **nové okno dotazu**.
3. V okně skriptu přidat WITH (hodnotou MEMORY_OPTIMIZED = ON) k příkazu CREATE TABLE.
4. Pokud je SESKUPENÝ index, změňte ho na NONCLUSTERED.
5. Přejmenujte existující tabulku s použitím proceduru SP_RENAME.
6. Vytvořte novou kopii paměťově optimalizované tabulky spuštěním svůj upravený skript CREATE TABLE.
7. Kopírování dat do paměťově optimalizované tabulky pomocí vložení... VYBERTE * DO:

```
INSERT INTO <new_memory_optimized_table>
        SELECT * FROM <old_disk_based_table>;
```


## <a name="step-5-optional-migrate-stored-procedures"></a>Krok 5 (volitelné): Migrace uložených procedur
Funkce v paměti můžete také upravit uložené procedury za účelem vylepšení výkonu.

### <a name="considerations-with-natively-compiled-stored-procedures"></a>Důležité informace o s nativně kompilovanými uloženými procedurami
Nativně kompilované uložené procedury musí mít na jeho klauzule T-SQL pomocí následujících možností:

* NATIVE_COMPILATION
* SCHEMABINDING: význam tabulky, uložené procedury nesmí obsahovat jejich definice sloupců změnit žádným způsobem, který by došlo k ovlivnění uloženou proceduru, pokud odstraníte uloženou proceduru.

Nativní modul musí používat některý velké objemy [ATOMICKÝCH bloků](https://msdn.microsoft.com/library/dn452281.aspx) pro správu transakce. Neexistuje žádná role pro explicitní BEGIN TRANSACTION, nebo pro vrácení transakce zpět. Pokud váš kód zjistí porušení obchodní pravidlo, můžete ukončit Atomický blok s [THROW](https://msdn.microsoft.com/library/ee677615.aspx) příkazu.

### <a name="typical-create-procedure-for-natively-compiled"></a>Typický postup vytvoření pro nativně kompilované
Obvykle se podobá následující šablony T-SQL k vytvoření nativně kompilované uložené procedury:

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

* SNÍMEK pro TRANSACTION_ISOLATION_LEVEL, je nejběžnější hodnotu pro nativně kompilované uložené procedury. Ale podmnožinou jiné hodnoty jsou také podporovány:
  
  * REPEATABLE READ
  * SERIALIZOVATELNÉ
* Hodnota jazyka musí být k dispozici v zobrazení sys.languages.

### <a name="how-to-migrate-a-stored-procedure"></a>Jak migrovat uložené procedury
Kroky migrace jsou:

1. Získáte skript CREATE PROCEDURE interpretovaných regulárních uložené procedury.
2. Přepište záhlaví tak, aby odpovídaly předchozí šablony.
3. Zjistit, zda uložené procedury kód T-SQL využívá všechny funkce, které nejsou podporovány pro nativně kompilované uložené procedury. Implementovat řešení v případě potřeby.
   
   * Podrobnosti najdete v tématu [problémy s migrací nativně zkompilován uložené procedury](https://msdn.microsoft.com/library/dn296678.aspx).
4. Přejmenujte staré uložené procedury pomocí proceduru SP_RENAME. Nebo jednoduše VYŘADIT.
5. Spusťte upravený skript vytvořit PROCEDURU T-SQL.

## <a name="step-6-run-your-workload-in-test"></a>Krok 6: Spuštění úlohy v testu
Spuštění úlohy v testovací databázi, která se podobá úloze, která běží v provozní databázi. To byste měli odhalit, zvýšení výkonu dosáhnout použití funkce v paměti pro tabulky a uložené procedury.

Hlavní atributy sady funkcí jsou:

* Počet souběžných připojení.
* Poměr čtení/zápis.

Chcete-li přizpůsobit a spusťte test zatížení, zvažte použití ostress.exe užitečný nástroj, který ukazuje [tady](sql-database-in-memory.md).

Kvůli minimalizaci latence sítě, spuštění testu ve stejné zeměpisné oblasti Azure ve kterém databáze existuje.

## <a name="step-7-post-implementation-monitoring"></a>Krok 7: Po implementaci monitorování
Vezměte v úvahu monitorování výkonu účinky vaší implementace v paměti v produkčním prostředí:

* [Monitorování úložiště v paměti](sql-database-in-memory-oltp-monitoring.md).
* [Monitorování databáze Azure SQL Database pomocí zobrazení dynamické správy](sql-database-monitoring-with-dmvs.md)

## <a name="related-links"></a>Související odkazy
* [Paměti OLTP (Optimalizace v paměti)](https://msdn.microsoft.com/library/dn133186.aspx)
* [Seznámení s nativně Kompilovanými uloženými procedurami](https://msdn.microsoft.com/library/dn133184.aspx)
* [Advisor optimalizace paměti](https://msdn.microsoft.com/library/dn284308.aspx)

