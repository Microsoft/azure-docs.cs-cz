---
title: In-Memory OLTP vylepšuje výkon SQL TXN.
description: Přijmout In-Memory OLTP pro zlepšení transakčního výkonu v existující databázi v Azure SQL Database a spravované instanci Azure SQL.
services: sql-database
ms.service: sql-database
ms.custom: sqldbrb=2
ms.subservice: development
ms.topic: how-to
author: stevestein
ms.author: sstein
ms.reviewer: MightyPen
ms.date: 11/07/2018
ms.openlocfilehash: e17e98e784b7453c87814c5cce5c03568f66b1cb
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2021
ms.locfileid: "91619742"
---
# <a name="use-in-memory-oltp-to-improve-your-application-performance-in-azure-sql-database-and-azure-sql-managed-instance"></a>Použití In-Memory OLTP ke zlepšení výkonu aplikace v Azure SQL Database a spravované instanci Azure SQL
[!INCLUDE[appliesto-sqldb-sqlmi](includes/appliesto-sqldb-sqlmi.md)]

[OLTP v paměti](in-memory-oltp-overview.md) lze použít ke zlepšení výkonu zpracování transakcí, přijímání dat a přechodných scénářů dat v databázích [úrovně Premium a pro důležité obchodní informace](database/service-tiers-vcore.md) bez zvýšení cenové úrovně.

> [!NOTE]
> Přečtěte si [, jak kvorum podvoje úlohy klíčové databáze při snižování DTU o 70% s Azure SQL Database](https://customers.microsoft.com/story/quorum-doubles-key-databases-workload-while-lowering-dtu-with-sql-database)

Pomocí těchto kroků můžete v existující databázi přijmout In-Memory OLTP.

## <a name="step-1-ensure-you-are-using-a-premium-and-business-critical-tier-database"></a>Krok 1: Ujistěte se, že používáte databázi úrovně Premium a Pro důležité obchodní informace.

In-Memory OLTP je podporováno pouze v databázích úrovně Premium a Pro důležité obchodní informace. In-Memory se podporuje, pokud je vrácený výsledek 1 (ne 0):

```sql
SELECT DatabasePropertyEx(Db_Name(), 'IsXTPSupported');
```

*XTP* pro *extrémní zpracování transakcí*

## <a name="step-2-identify-objects-to-migrate-to-in-memory-oltp"></a>Krok 2: určení objektů, které se mají migrovat do In-Memory OLTP

SSMS zahrnuje sestavu s **přehledem analýzy výkonu transakcí** , kterou můžete spustit pro databázi s aktivním zatížením. Tato sestava identifikuje tabulky a uložené procedury, které jsou kandidáty na migraci In-Memory OLTP.

V SSMS vygenerujte sestavu:

* V **Průzkumník objektů** klikněte pravým tlačítkem myši na uzel databáze.
* Klikněte na **sestavy**  >  **standardní**  >  **Přehled analýzy výkonu transakce**.

Další informace najdete v tématu [určení, zda by měla být tabulka nebo uložená procedura In-Memory OLTP](/sql/relational-databases/in-memory-oltp/determining-if-a-table-or-stored-procedure-should-be-ported-to-in-memory-oltp).

## <a name="step-3-create-a-comparable-test-database"></a>Krok 3: vytvoření srovnatelné testovací databáze

Předpokládejme, že se v sestavě zobrazuje vaše databáze, která by mohla být převedena na paměťově optimalizovanou tabulku. Doporučujeme, abyste nejprve otestovali, abyste ověřili indikaci testováním.

Potřebujete testovací kopii vaší provozní databáze. Testovací databáze by měla být na stejné úrovni úrovně služeb jako provozní databáze.

Chcete-li se snadno rozzkoušet, vylepšit testovací databázi následujícím způsobem:

1. Připojte se k testovací databázi pomocí SSMS.
2. Abyste se vyhnuli nutnosti používat v dotazech možnost WITH (SNAPSHOT), nastavte možnost databáze, jak je znázorněno v následujícím příkazu T-SQL:

   ```sql
   ALTER DATABASE CURRENT
    SET
        MEMORY_OPTIMIZED_ELEVATE_TO_SNAPSHOT = ON;
   ```

## <a name="step-4-migrate-tables"></a>Krok 4: migrace tabulek

Musíte vytvořit a naplnit paměťově optimalizovanou kopii tabulky, kterou chcete testovat. Můžete ho vytvořit pomocí některého z těchto:

* Průvodce užitečnou optimalizací paměti v SSMS.
* Ruční T-SQL.

### <a name="memory-optimization-wizard-in-ssms"></a>Průvodce optimalizací paměti v SSMS

Postup použití této možnosti migrace:

1. Připojte se k testovací databázi pomocí SSMS.
2. V **Průzkumník objektů** klikněte pravým tlačítkem myši na tabulku a potom klikněte na možnost **Poradce optimalizace paměti**.

   Zobrazí se průvodce **poradcem pro optimalizaci paměti tabulky** .
3. V průvodci klikněte na **Ověření migrace** (nebo na tlačítko **Další** ) a zjistěte, jestli má tabulka nějaké nepodporované funkce, které nejsou podporované v paměťově optimalizovaných tabulkách. Další informace naleznete v tématu:

   * *Kontrolní seznam optimalizace paměti* v [poradci optimalizace paměti](/sql/relational-databases/in-memory-oltp/memory-optimization-advisor).
   * [In-Memory OLTP nepodporuje konstrukce Transact-SQL](/sql/relational-databases/in-memory-oltp/transact-sql-constructs-not-supported-by-in-memory-oltp).
   * [Migruje se na In-Memory OLTP](/sql/relational-databases/in-memory-oltp/plan-your-adoption-of-in-memory-oltp-features-in-sql-server).
4. Pokud tabulka neobsahuje žádné nepodporované funkce, může poradce pro vás provést vlastní migraci schématu a dat.

### <a name="manual-t-sql"></a>Ruční T-SQL

Postup použití této možnosti migrace:

1. Připojte se k testovací databázi pomocí SSMS (nebo podobného nástroje).
2. Získejte kompletní skript T-SQL pro tabulku a její indexy.

   * V SSMS klikněte pravým tlačítkem myši na uzel tabulky.
   * Klikněte na tlačítko **tabulka skriptů jako**  >  **vytvořit pro**  >  **nové okno dotazu**.
3. V okně skriptu přidejte do příkazu CREATE TABLE (MEMORY_OPTIMIZED = ON).
4. Pokud existuje CLUSTEROVANÝ index, změňte ho na neclusterovaný.
5. Přejmenujte existující tabulku pomocí SP_RENAME.
6. Vytvořte novou paměťově optimalizovanou kopii tabulky spuštěním upravovaného CREATE TABLE skriptu.
7. Zkopírujte data do paměťově optimalizované tabulky pomocí INSERT... VYBERTE * DO:

```sql
INSERT INTO <new_memory_optimized_table>
        SELECT * FROM <old_disk_based_table>;
```

## <a name="step-5-optional-migrate-stored-procedures"></a>Krok 5 (volitelné): migrace uložených procedur

Funkce In-Memory může také upravit uloženou proceduru pro zlepšení výkonu.

### <a name="considerations-with-natively-compiled-stored-procedures"></a>Otázky s nativně kompilovanými uloženými procedurami

Nativně kompilovaná uložená procedura musí mít v klauzuli T-SQL WITH následující možnosti:

* NATIVE_COMPILATION
* SCHEMABINDING: to znamená, že se v tabulkách, v nichž uložená procedura nemůže naměnit definice sloupců, jakýmkoli způsobem změnil, který by ovlivnil uloženou proceduru, pokud neodstraníte uloženou proceduru.

Nativní modul musí používat jeden z těchto velkých [atomických bloků](/sql/relational-databases/in-memory-oltp/atomic-blocks-in-native-procedures) pro správu transakcí. Neexistuje žádná role pro explicitní BEGIN TRANSACTION nebo transakce vrácení zpět. Pokud váš kód zjistí porušení obchodního pravidla, může ukončit atomický blok s příkazem [throw](/sql/t-sql/language-elements/throw-transact-sql) .

### <a name="typical-create-procedure-for-natively-compiled"></a>Typická procedura CREATE pro nativně kompilování

T-SQL je typicky vytvořit nativně zkompilované uložené procedury podobně jako v následující šabloně:

```sql
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

* Pro TRANSACTION_ISOLATION_LEVEL je snímek nejběžnější hodnotou pro nativně kompilovaných uložených procedur. Nicméně podmnožina dalších hodnot je také podporována:
  
  * OPAKOVANÉ ČTENÍ
  * SERIALIZOVATELNÝ
* Hodnota jazyka musí být uvedena v zobrazení sys. Languages.

### <a name="how-to-migrate-a-stored-procedure"></a>Postup migrace uložené procedury

Postup migrace je následující:

1. Získání skriptu pro vytvoření procedury v běžné interpretované uložené proceduře.
2. Přepište hlavičku tak, aby odpovídala předchozí šabloně.
3. Zjišťuje, zda kód T-SQL uložené procedury používá funkce, které nejsou podporovány pro nativně kompilované uložené procedury. V případě potřeby implementujte alternativní řešení.

   Podrobnosti najdete v tématu [problémy s migrací u nativně kompilovaných uložených procedur](/sql/relational-databases/in-memory-oltp/a-guide-to-query-processing-for-memory-optimized-tables).
4. Přejmenujte starou uloženou proceduru pomocí SP_RENAME. Nebo ho jednoduše přetáhněte.
5. Spusťte upravený skript T-SQL VYTVOŘENého postupu.

## <a name="step-6-run-your-workload-in-test"></a>Krok 6: spuštění úlohy v testu

Spusťte v testovací databázi úlohu, která bude podobná zatížení, které běží ve vaší provozní databázi. To by mělo odhalit zvýšení výkonu dosažené při používání funkce In-Memory pro tabulky a uložené procedury.

Hlavní atributy úlohy jsou:

* Počet souběžných připojení.
* Poměr pro čtení a zápis.

Chcete-li přizpůsobit a spustit testovací úlohu, zvažte použití praktického `ostress.exe` nástroje, který je znázorněn v tomto článku [v paměti](in-memory-oltp-overview.md) .

Chcete-li minimalizovat latenci sítě, spusťte test ve stejné geografické oblasti Azure, kde databáze existuje.

## <a name="step-7-post-implementation-monitoring"></a>Krok 7: monitorování po implementaci

Zvažte monitorování vlivu In-Memory implementace v produkčním prostředí:

* [Monitorujte In-Memory úložiště](in-memory-oltp-monitor-space.md).
* [Monitorování pomocí zobrazení dynamické správy](database/monitoring-with-dmvs.md)

## <a name="related-links"></a>Související odkazy

* [OLTP v paměti (optimalizace v paměti)](/sql/relational-databases/in-memory-oltp/in-memory-oltp-in-memory-optimization)
* [Seznámení s nativně kompilovanými uloženými procedurami](/sql/relational-databases/in-memory-oltp/a-guide-to-query-processing-for-memory-optimized-tables)
* [Poradce pro optimalizaci paměti](/sql/relational-databases/in-memory-oltp/memory-optimization-advisor)
