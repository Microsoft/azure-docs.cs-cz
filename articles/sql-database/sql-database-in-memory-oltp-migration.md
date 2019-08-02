---
title: OLTP v paměti zlepšuje výkon SQL TXN | Microsoft Docs
description: Přijmout OLTP v paměti pro zlepšení transakčního výkonu v existující databázi SQL.
services: sql-database
ms.service: sql-database
ms.subservice: development
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: stevestein
ms.author: sstein
ms.reviewer: MightyPen
ms.date: 11/07/2018
ms.openlocfilehash: e869b2bba3bd64b58d9063e9445889ef709efdc3
ms.sourcegitcommit: 7c4de3e22b8e9d71c579f31cbfcea9f22d43721a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/26/2019
ms.locfileid: "68567950"
---
# <a name="use-in-memory-oltp-to-improve-your-application-performance-in-sql-database"></a>Pomocí OLTP v paměti můžete zlepšit výkon aplikace v SQL Database

[OLTP v paměti](sql-database-in-memory.md) lze použít ke zlepšení výkonu zpracování transakcí, přijímání dat a přechodných scénářů dat v databázích [úrovně Premium a pro důležité obchodní informace](sql-database-service-tiers-vcore.md) bez zvýšení cenové úrovně. 

> [!NOTE] 
> Přečtěte si, jak kvorum podvoje [úlohy klíčové databáze při snižování DTU o 70% s SQL Database](https://customers.microsoft.com/story/quorum-doubles-key-databases-workload-while-lowering-dtu-with-sql-database)


Pomocí těchto kroků můžete v existující databázi přijmout OLTP v paměti.

## <a name="step-1-ensure-you-are-using-a-premium-and-business-critical-tier-database"></a>Krok 1: Ujistěte se, že používáte databázi úrovně Premium a Pro důležité obchodní informace.

OLTP v paměti je podporováno pouze v databázích úrovně Premium a Pro důležité obchodní informace. V paměti je podporována, pokud je vrácený výsledek 1 (ne 0):

```
SELECT DatabasePropertyEx(Db_Name(), 'IsXTPSupported');
```

*XTP* pro *extrémní zpracování transakcí*



## <a name="step-2-identify-objects-to-migrate-to-in-memory-oltp"></a>Krok 2: Identifikace objektů, které se mají migrovat do OLTP v paměti
SSMS zahrnuje sestavu s **přehledem analýzy výkonu transakcí** , kterou můžete spustit pro databázi s aktivním zatížením. Tato sestava identifikuje tabulky a uložené procedury, které jsou kandidáty na migraci do OLTP v paměti.

V SSMS vygenerujte sestavu:

* V **Průzkumník objektů**klikněte pravým tlačítkem myši na uzel databáze.
* Klikněte na **sestavy** > **standardní** > **Přehled analýzy výkonu transakce**.

Další informace najdete v tématu [určení, zda by měla být tabulka nebo uložená procedura převoditelné do OLTP v paměti](https://msdn.microsoft.com/library/dn205133.aspx).

## <a name="step-3-create-a-comparable-test-database"></a>Krok 3: Vytvoření srovnatelné testovací databáze
Předpokládejme, že se v sestavě zobrazuje vaše databáze, která by mohla být převedena na paměťově optimalizovanou tabulku. Doporučujeme, abyste nejprve otestovali, abyste ověřili indikaci testováním.

Potřebujete testovací kopii vaší provozní databáze. Testovací databáze by měla být na stejné úrovni úrovně služeb jako provozní databáze.

Chcete-li se snadno rozzkoušet, vylepšit testovací databázi následujícím způsobem:

1. Připojte se k testovací databázi pomocí SSMS.
2. Abyste se vyhnuli nutnosti používat v dotazech možnost WITH (SNAPSHOT), nastavte možnost databáze, jak je znázorněno v následujícím příkazu T-SQL:
   
   ```
   ALTER DATABASE CURRENT
    SET
        MEMORY_OPTIMIZED_ELEVATE_TO_SNAPSHOT = ON;
   ```

## <a name="step-4-migrate-tables"></a>Krok 4: Migrace tabulek
Musíte vytvořit a naplnit paměťově optimalizovanou kopii tabulky, kterou chcete testovat. Můžete ho vytvořit pomocí některého z těchto:

* Průvodce užitečnou optimalizací paměti v SSMS.
* Ruční T-SQL.

#### <a name="memory-optimization-wizard-in-ssms"></a>Průvodce optimalizací paměti v SSMS
Postup použití této možnosti migrace:

1. Připojte se k testovací databázi pomocí SSMS.
2. V **Průzkumník objektů**klikněte pravým tlačítkem myši na tabulku a potom klikněte na možnost **Poradce optimalizace paměti**.
   
   * Zobrazí se průvodce **poradcem pro optimalizaci paměti tabulky** .
3. V průvodci klikněte na **Ověření migrace** (nebo na tlačítko **Další** ) a zjistěte, jestli má tabulka nějaké nepodporované funkce, které nejsou podporované v paměťově optimalizovaných tabulkách. Další informace naleznete v tématu:
   
   * *Kontrolní seznam optimalizace paměti* v [poradci optimalizace paměti](https://msdn.microsoft.com/library/dn284308.aspx).
   * [OLTP v paměti nepodporují konstrukce jazyka Transact-SQL](https://msdn.microsoft.com/library/dn246937.aspx).
   * [Migrace do OLTP v paměti](https://msdn.microsoft.com/library/dn247639.aspx).
4. Pokud tabulka neobsahuje žádné nepodporované funkce, může poradce pro vás provést vlastní migraci schématu a dat.

#### <a name="manual-t-sql"></a>Ruční T-SQL
Postup použití této možnosti migrace:

1. Připojte se k testovací databázi pomocí SSMS (nebo podobného nástroje).
2. Získejte kompletní skript T-SQL pro tabulku a její indexy.
   
   * V SSMS klikněte pravým tlačítkem myši na uzel tabulky.
   * Klikněte na tlačítko **tabulka skriptů jako** > **vytvořit pro** > **nové okno dotazu**.
3. V okně skriptu přidejte do příkazu CREATE TABLE příkaz WITH (MEMORY_OPTIMIZED = ON).
4. Pokud existuje CLUSTEROVANÝ index, změňte ho na neclusterovaný.
5. Přejmenujte existující tabulku pomocí SP_RENAME.
6. Vytvořte novou paměťově optimalizovanou kopii tabulky spuštěním upravovaného CREATE TABLE skriptu.
7. Zkopírujte data do paměťově optimalizované tabulky pomocí INSERT... VYBERTE * DO:

```
INSERT INTO <new_memory_optimized_table>
        SELECT * FROM <old_disk_based_table>;
```


## <a name="step-5-optional-migrate-stored-procedures"></a>Krok 5 (volitelné): Migrace uložených procedur
Funkce v paměti může také upravovat uloženou proceduru pro zlepšení výkonu.

### <a name="considerations-with-natively-compiled-stored-procedures"></a>Otázky s nativně kompilovanými uloženými procedurami
Nativně kompilovaná uložená procedura musí mít v klauzuli T-SQL WITH následující možnosti:

* NATIVE_COMPILATION
* SCHEMABINDING: to znamená, že se v tabulkách, v nichž uložená procedura nemůže naměnit definice sloupců, jakýmkoli způsobem změnil, který by ovlivnil uloženou proceduru, pokud neodstraníte uloženou proceduru.

Nativní modul musí používat jeden z těchto velkých [atomických bloků](https://msdn.microsoft.com/library/dn452281.aspx) pro správu transakcí. Neexistuje žádná role pro explicitní BEGIN TRANSACTION nebo transakce vrácení zpět. Pokud váš kód zjistí porušení obchodního pravidla, může ukončit atomický blok s příkazem [throw](https://msdn.microsoft.com/library/ee677615.aspx) .

### <a name="typical-create-procedure-for-natively-compiled"></a>Typická procedura CREATE pro nativně kompilování
T-SQL je typicky vytvořit nativně zkompilované uložené procedury podobně jako v následující šabloně:

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

* Pro TRANSACTION_ISOLATION_LEVEL je snímek nejběžnější hodnotou pro nativně kompilovaných uložených procedur. Nicméně podmnožina dalších hodnot je také podporována:
  
  * OPAKOVANÉ ČTENÍ
  * SERIALIZOVATELNÝ
* Hodnota jazyka musí být uvedena v zobrazení sys. Languages.

### <a name="how-to-migrate-a-stored-procedure"></a>Postup migrace uložené procedury
Postup migrace je následující:

1. Získání skriptu pro vytvoření procedury v běžné interpretované uložené proceduře.
2. Přepište hlavičku tak, aby odpovídala předchozí šabloně.
3. Zjišťuje, zda kód T-SQL uložené procedury používá funkce, které nejsou podporovány pro nativně kompilované uložené procedury. V případě potřeby implementujte alternativní řešení.
   
   * Podrobnosti najdete v tématu [problémy s migrací u nativně kompilovaných uložených procedur](https://msdn.microsoft.com/library/dn296678.aspx).
4. Přejmenujte starou uloženou proceduru pomocí SP_RENAME. Nebo ho jednoduše přetáhněte.
5. Spusťte upravený skript T-SQL VYTVOŘENého postupu.

## <a name="step-6-run-your-workload-in-test"></a>Krok 6: Spuštění úlohy v testu
Spusťte v testovací databázi úlohu, která bude podobná zatížení, které běží ve vaší provozní databázi. To by mělo odhalit nárůst výkonu dosažený využitím funkce v paměti pro tabulky a uložené procedury.

Hlavní atributy úlohy jsou:

* Počet souběžných připojení.
* Poměr pro čtení a zápis.

Chcete-li přizpůsobit a spustit úlohu testování, zvažte použití praktického nástroje OSTRESS. exe, který je znázorněn v [tomto](sql-database-in-memory.md)příkladu.

Chcete-li minimalizovat latenci sítě, spusťte test ve stejné geografické oblasti Azure, kde databáze existuje.

## <a name="step-7-post-implementation-monitoring"></a>Krok 7: Monitorování po implementaci
Zvažte monitorování vlivu implementace v paměti v produkčním prostředí:

* [Monitorujte úložiště v paměti](sql-database-in-memory-oltp-monitoring.md).
* [Monitorování databáze Azure SQL Database pomocí zobrazení dynamické správy](sql-database-monitoring-with-dmvs.md)

## <a name="related-links"></a>Související odkazy
* [OLTP v paměti (optimalizace v paměti)](https://msdn.microsoft.com/library/dn133186.aspx)
* [Seznámení s nativně kompilovanými uloženými procedurami](https://msdn.microsoft.com/library/dn133184.aspx)
* [Poradce pro optimalizaci paměti](https://msdn.microsoft.com/library/dn284308.aspx)

