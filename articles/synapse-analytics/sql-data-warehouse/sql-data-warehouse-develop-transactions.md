---
title: Použití transakcí
description: Tipy pro implementaci transakcí v Azure SQL Data Warehouse pro vývoj řešení.
services: synapse-analytics
author: XiaoyuMSFT
manager: craigg
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: ''
ms.date: 03/22/2019
ms.author: xiaoyul
ms.reviewer: igorstan
ms.custom: seo-lt-2019
ms.openlocfilehash: a14201131eac5ce1efc4020c9ce0f40a80cac8a3
ms.sourcegitcommit: 8a9c54c82ab8f922be54fb2fcfd880815f25de77
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "80351572"
---
# <a name="using-transactions-in-sql-data-warehouse"></a>Použití transakcí v datovém skladu SQL
Tipy pro implementaci transakcí v Azure SQL Data Warehouse pro vývoj řešení.

## <a name="what-to-expect"></a>Co očekávat
Jak byste očekávali, SQL Data Warehouse podporuje transakce jako součást úlohy datového skladu. Chcete-li však zajistit výkon sql data warehouse je udržována ve velkém měřítku některé funkce jsou omezené ve srovnání s SQL Server. Tento článek zdůrazňuje rozdíly a uvádí ostatní. 

## <a name="transaction-isolation-levels"></a>Úrovně izolace transakcí
SQL Data Warehouse implementuje transakce ACID. Úroveň izolace transakční podpory je výchozí pro čtení UNCOMMITTED.  Můžete změnit na číst potvrzený snímek izolace zapnutím možnosti READ_COMMITTED_SNAPSHOT databáze pro databázi uživatele při připojení k hlavní databázi.  Po povolení jsou všechny transakce v této databázi provedeny v rámci čtení potvrzený snímek izolace a nastavení READ UNCOMMITTED na úrovni relace nebude dodržena. Zkontrolujte [možnosti ALTER DATABASE SET (Transact-SQL)](https://docs.microsoft.com/sql/t-sql/statements/alter-database-transact-sql-set-options?view=azure-sqldw-latest) podrobnosti.

## <a name="transaction-size"></a>Velikost transakce
Jedna transakce změny dat je omezena na velikost. Limit se použije na distribuci. Proto lze vypočítat celkové přidělení vynásobením limitu počtem rozdělení. Chcete-li aproximovat maximální počet řádků v transakci, vydělte distribuční limit celkovou velikostí každého řádku. U sloupců s proměnnou délkou zvažte použití průměrné délky sloupce namísto použití maximální velikosti.

V následující tabulce byly učiněny tyto předpoklady:

* Došlo k rovnoměrnému rozdělení dat 
* Průměrná délka řádku je 250 bajtů

## <a name="gen2"></a>Gen2 (Gen2)

| [DWU](sql-data-warehouse-overview-what-is.md) | Limit na distribuci (GB) | Počet distribucí | Velikost transakce MAX (GB) | # Řádky na distribuci | Maximální počet řádků na transakci |
| --- | --- | --- | --- | --- | --- |
| DW100c |1 |60 |60 |4,000,000 |240,000,000 |
| DW200c |1,5 |60 |90 |6 000 000 |360,000,000 |
| DW300c |2.25 |60 |135 |9,000,000 |540,000,000 |
| DW400c |3 |60 |180 |12,000,000 |720,000,000 |
| DW500c. |3,75 |60 |225 |15,000,000 |900,000,000 |
| DW1000c |7,5 |60 |450 |30,000,000 |1,800,000,000 |
| DW1500c |11.25 |60 |675 |45,000,000 |2,700,000,000 |
| DW2000c |15 |60 |900 |60,000,000 |3,600,000,000 |
| DW2500c |18.75 |60 |1125 |75,000,000 |4,500,000,000 |
| DW3000c |22.5 |60 |1,350 |90,000,000 |5,400,000,000 |
| DW5000c |37.5 |60 |2,250 |150,000,000 |9,000,000,000 |
| DW6000c |45 |60 |2,700 |180,000,000 |10,800,000,000 |
| DW7500c |56.25 |60 |3,375 |225,000,000 |13,500,000,000 |
| DW10000c |75 |60 |4 500 |300 000 000 |18,000,000,000 |
| DW15000c |112.5 |60 |6 750 |450,000,000 |27,000,000,000 |
| DW30000c |225 |60 |13,500 |900,000,000 |54,000,000,000 |

## <a name="gen1"></a>Gen1 (Gen1)

| [DWU](sql-data-warehouse-overview-what-is.md) | Limit na distribuci (GB) | Počet distribucí | Velikost transakce MAX (GB) | # Řádky na distribuci | Maximální počet řádků na transakci |
| --- | --- | --- | --- | --- | --- |
| DW100 |1 |60 |60 |4,000,000 |240,000,000 |
| DW200 |1,5 |60 |90 |6 000 000 |360,000,000 |
| DW300 |2.25 |60 |135 |9,000,000 |540,000,000 |
| DW400 |3 |60 |180 |12,000,000 |720,000,000 |
| DW500 |3,75 |60 |225 |15,000,000 |900,000,000 |
| DW600 |4.5 |60 |270 |18,000,000 |1,080,000,000 |
| DW1000 |7,5 |60 |450 |30,000,000 |1,800,000,000 |
| DW1200 |9 |60 |540 |36,000,000 |2,160,000,000 |
| DW1500 |11.25 |60 |675 |45,000,000 |2,700,000,000 |
| DW2000 |15 |60 |900 |60,000,000 |3,600,000,000 |
| DW3000 |22.5 |60 |1,350 |90,000,000 |5,400,000,000 |
| DW6000 |45 |60 |2,700 |180,000,000 |10,800,000,000 |

Limit velikosti transakce se použije pro transakci nebo operaci. Není použita ve všech souběžných transakcích. Proto každá transakce je povoleno zapisovat toto množství dat do protokolu. 

Chcete-li optimalizovat a minimalizovat množství dat zapsaných do protokolu, naleznete v článku [Doporučené postupy transakce.](sql-data-warehouse-develop-best-practices-transactions.md)

> [!WARNING]
> Maximální velikost transakce lze dosáhnout pouze pro hash nebo ROUND_ROBIN distribuovaných tabulek, kde je rovnoměrné rozložení dat. Pokud transakce je zápis dat zkosený způsobem do distribuce pak limit je pravděpodobné, že bude dosaženo před maximální velikost transakce.
> <!--REPLICATED_TABLE-->
> 
> 

## <a name="transaction-state"></a>Stav transakce
SQL Data Warehouse používá funkci XACT_STATE() k vykazování neúspěšné transakce pomocí hodnoty -2. Tato hodnota znamená, že transakce selhala a je označena pouze pro vrácení zpět.

> [!NOTE]
> Použití funkce -2 XACT_STATE k označení neúspěšné transakce představuje odlišné chování serveru SQL Server. SQL Server používá hodnotu -1 představují uncommittable transakce. SQL Server může tolerovat některé chyby uvnitř transakce, aniž by musel být označen jako uncommittable. Například `SELECT 1/0` by způsobit chybu, ale ne vynutit transakci do stavu uncommittable. SQL Server také umožňuje čtení v uncommittable transakce. Sql Data Warehouse však neumožňuje provést. Pokud dojde k chybě uvnitř transakce SQL Data Warehouse, automaticky vstoupí do stavu -2 a nebudete moci provádět žádné další příkazy select, dokud nebude příkaz vrácen zpět. Je proto důležité zkontrolovat, zda kód aplikace, chcete-li zjistit, zda používá XACT_STATE(), jak budete muset provést změny kódu.
> 
> 

Například v SQL Server může zobrazit transakce, která vypadá takto:

```sql
SET NOCOUNT ON;
DECLARE @xact_state smallint = 0;

BEGIN TRAN
    BEGIN TRY
        DECLARE @i INT;
        SET     @i = CONVERT(INT,'ABC');
    END TRY
    BEGIN CATCH
        SET @xact_state = XACT_STATE();

        SELECT  ERROR_NUMBER()    AS ErrNumber
        ,       ERROR_SEVERITY()  AS ErrSeverity
        ,       ERROR_STATE()     AS ErrState
        ,       ERROR_PROCEDURE() AS ErrProcedure
        ,       ERROR_MESSAGE()   AS ErrMessage
        ;

        IF @@TRANCOUNT > 0
        BEGIN
            ROLLBACK TRAN;
            PRINT 'ROLLBACK';
        END

    END CATCH;

IF @@TRANCOUNT >0
BEGIN
    PRINT 'COMMIT';
    COMMIT TRAN;
END

SELECT @xact_state AS TransactionState;
```

Předchozí kód obsahuje následující chybovou zprávu:

Msg 111233, úroveň 16, stát 1, řádek 1 111233; Aktuální transakce byla přerušena a všechny čekající změny byly vráceny zpět. Příčina: Transakce ve stavu pouze pro vrácení zpět nebyla explicitně vrácena zpět před příkazem DDL, DML nebo SELECT.

Nezískáte výstup funkcí ERROR_*.

V datovém skladu SQL je třeba kód mírně změnit:

```sql
SET NOCOUNT ON;
DECLARE @xact_state smallint = 0;

BEGIN TRAN
    BEGIN TRY
        DECLARE @i INT;
        SET     @i = CONVERT(INT,'ABC');
    END TRY
    BEGIN CATCH
        SET @xact_state = XACT_STATE();

        IF @@TRANCOUNT > 0
        BEGIN
            ROLLBACK TRAN;
            PRINT 'ROLLBACK';
        END

        SELECT  ERROR_NUMBER()    AS ErrNumber
        ,       ERROR_SEVERITY()  AS ErrSeverity
        ,       ERROR_STATE()     AS ErrState
        ,       ERROR_PROCEDURE() AS ErrProcedure
        ,       ERROR_MESSAGE()   AS ErrMessage
        ;
    END CATCH;

IF @@TRANCOUNT >0
BEGIN
    PRINT 'COMMIT';
    COMMIT TRAN;
END

SELECT @xact_state AS TransactionState;
```

Očekávané chování je nyní pozorováno. Chyba v transakci je spravována a funkce ERROR_* poskytují hodnoty podle očekávání.

Vše, co se změnilo, je, že vrácení zpět transakce muselo dojít před čtení informace o chybě v catch bloku.

## <a name="error_line-function"></a>Error_Line()
Je také třeba poznamenat, že SQL Data Warehouse neimplementuje nebo nepodporuje funkci ERROR_LINE(). Pokud máte to to v kódu, je nutné odebrat, aby byly kompatibilní s SQL Data Warehouse. Místo toho použijte popisky dotazů v kódu k implementaci ekvivalentních funkcí. Další podrobnosti naleznete v článku [LABEL.](sql-data-warehouse-develop-label.md)

## <a name="using-throw-and-raiserror"></a>Použití THROW a RAISERROR
THROW je modernější implementace pro vyvolání výjimek v sql datovém skladu, ale RAISERROR je také podporována. Existuje několik rozdílů, které stojí za to věnovat pozornost však.

* Uživatelem definovaná čísla chybových zpráv nemohou být v rozsahu 100 000 – 150 000 pro THROW.
* Chybové zprávy RAISERROR jsou stanoveny na 50 000
* Použití sys.messages není podporováno

## <a name="limitations"></a>Omezení
SQL Data Warehouse má několik dalších omezení, které se vztahují k transakcím.

Jsou to tyto:

* Žádné distribuované transakce
* Nejsou povoleny žádné vnořené transakce.
* Nejsou povoleny žádné uložené body.
* Žádné pojmenované transakce
* Žádné označené transakce
* Žádná podpora pro DDL, například vytvořit tabulku uvnitř uživatelem definované transakce

## <a name="next-steps"></a>Další kroky
Další informace o optimalizaci transakcí najdete v tématu [Doporučené postupy pro transakce](sql-data-warehouse-develop-best-practices-transactions.md). Další informace o dalších doporučených postupech pro SQL Data Warehouse najdete v [tématu Sql Data Warehouse.](sql-data-warehouse-best-practices.md)

