---
title: Použití transakcí v Azure SQL Data Warehouse | Microsoft Docs
description: Tipy pro provádění transakcí v Azure SQL Data Warehouse na vývoj řešení.
services: sql-data-warehouse
author: ckarst
manager: craigg-msft
ms.service: sql-data-warehouse
ms.topic: conceptual
ms.component: implement
ms.date: 04/17/2018
ms.author: cakarst
ms.reviewer: igorstan
ms.openlocfilehash: 7fa3d19cc0fca81616969773a40c3d3dbccc4a26
ms.sourcegitcommit: 59914a06e1f337399e4db3c6f3bc15c573079832
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/19/2018
---
# <a name="using-transactions-in-sql-data-warehouse"></a>Použití transakcí v SQL Data Warehouse
Tipy pro provádění transakcí v Azure SQL Data Warehouse na vývoj řešení.

## <a name="what-to-expect"></a>Co můžete očekávat
Jak jste zvyklí, SQL Data Warehouse podporuje transakce v rámci úlohy datového skladu. Ale zajistit, že výkon služby SQL Data Warehouse je udržován na úrovni škálování některé funkce omezeny ve srovnání s systému SQL Server. V tomto článku klade důraz rozdíly a uvádí ostatní. 

## <a name="transaction-isolation-levels"></a>Úrovně izolace transakce
SQL Data Warehouse implementuje transakce ACID. Úroveň izolace podpora transakcí je však omezená na READ UNCOMMITTED; Tato úroveň nelze změnit. Pokud READ UNCOMMITTED obavu, můžete implementovat několik metod, aby změny čtení dat kódování. Nejčastěji používané metody použití funkce CTAS a přepínání oddílů tabulky (často označované jako posuvné okno vzor) uživatelům zabránit v dotazování na data, která stále připraveném. Zobrazení, které předem filtrovat data jsou také oblíbených přístup.  

## <a name="transaction-size"></a>Velikost transakce
Transakce úpravy jednoho datového je omezen velikostí. Limit platí za distribuce. Proto lze vypočítat celkové přidělení vynásobením limit počtu distribučních. Přibližná maximální počet řádků v transakci rozdělíte krytky distribuční celková velikost každý řádek. Pro proměnné délka sloupce zvažte trvá délka průměrná sloupce, nikoli pomocí maximální velikosti.

V následující tabulce následující předpoklady byly provedeny:

* Rovnoměrné rozdělení dat došlo k chybě. 
* Délka průměrná řádek je 250 bajtů

| [DWU](sql-data-warehouse-overview-what-is.md) | Cap za distribuční (GiB) | Počet distribuce | MAXIMÁLNÍ velikost transakce (GiB) | # Řádků na jeden distribuce | Maximální počet řádků na transakci |
| --- | --- | --- | --- | --- | --- |
| OD DW100 |1 |60 |60 |4,000,000 |240,000,000 |
| DW200 |1.5 |60 |90 |6 000 000 |360,000,000 |
| DW300 |2.25 |60 |135 |9,000,000 |540,000,000 |
| DW400 |3 |60 |180 |12,000,000 |720,000,000 |
| DW500 |3.75 |60 |225 |15,000,000 |900,000,000 |
| DW600 |4.5 |60 |270 |18,000,000 |1,080,000,000 |
| DW1000 |7.5 |60 |450 |30,000,000 |1,800,000,000 |
| DW1200 |9 |60 |540 |36,000,000 |2,160,000,000 |
| DW1500 |11.25 |60 |675 |45,000,000 |2,700,000,000 |
| DW2000 |15 |60 |900 |60,000,000 |3,600,000,000 |
| DW3000 |22.5 |60 |1,350 |90,000,000 |5,400,000,000 |
| DW6000 |45 |60 |2,700 |180,000,000 |10,800,000,000 |

Limit velikosti transakce se použije pro transakci nebo operace. Nebude použito v rámci všech souběžných transakcí. Každou transakci je proto oprávnění k zápisu takové množství dat do protokolu. 

Můžete najít a minimalizuje množství dat, zapíšou do protokolu, [transakce osvědčené postupy](sql-data-warehouse-develop-best-practices-transactions.md) článku.

> [!WARNING]
> Transakce maximální velikost lze dosáhnout pouze pro hodnoty HASH nebo dokonce ROUND_ROBIN distribuované tabulky, kde je šíření data. Pokud transakce je zápis dat zkreslilo způsobem do distribucí limit je pravděpodobně dosažitelná před transakce maximální velikost.
> <!--REPLICATED_TABLE-->
> 
> 

## <a name="transaction-state"></a>Stav transakce
SQL Data Warehouse používá funkci XACT_STATE() k hlášení selhání transakce pomocí hodnoty -2. Tato hodnota znamená transakce se nezdařilo a je označen pro pouze vrácení zpět.

> [!NOTE]
> Použití -2 funkcí XACT_STATE k označení selhání transakce představuje různé chování v systému SQL Server. SQL Server používá k reprezentaci nezapsatelná transakce hodnota -1. SQL Server tolerovat bez nutnosti označit jako nezapsatelná chybami v transakci. Například `SELECT 1/0` by způsobit chybu, ale bez vynucení transakci do nezapsatelná stavu. SQL Server také umožňuje čtení v nezapsatelná transakce. Ale SQL Data Warehouse, nebudou vám to. Pokud dojde k chybě v transakci SQL Data Warehouse automaticky přejde do stavu-2 a nebudete moct provádět žádné další výběr příkazy, dokud příkaz byla vrácena zpět. Proto je důležité zkontrolovat, že kód aplikace zobrazíte, pokud používá XACT_STATE() jako je třeba provést změny kódu.
> 
> 

V systému SQL Server se může zobrazit transakci, která vypadá takto:

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
            PRINT 'ROLLBACK';
            ROLLBACK TRAN;
        END

    END CATCH;

IF @@TRANCOUNT >0
BEGIN
    PRINT 'COMMIT';
    COMMIT TRAN;
END

SELECT @xact_state AS TransactionState;
```

Předchozí kód poskytuje následující chybová zpráva:

Msg 111233, Level 16, stav 1, řádek 1 111233; Aktuální transakce byla přerušena a všechny čekající změny byly vráceny zpět. Příčina: Transakce ve stavu jen pro vrácení zpět nebyla vrácena explicitně před DDL, DML nebo příkazu SELECT.

Nebude-li získat výstup funkce ERROR_ *.

V SQL Data Warehouse musí být mírně změněn kód:

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
            PRINT 'ROLLBACK';
            ROLLBACK TRAN;
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

Nyní je zaznamenali očekávané chování. Chyba v transakci je spravovat a funkce ERROR_ * zadejte hodnoty, podle očekávání.

Všechny, které se změnily je, že vrácení transakce museli dojít před čtení informací o chybě v bloku CATCH.

## <a name="errorline-function"></a>Error_Line() – funkce
Je také vhodné poznamenat, že SQL Data Warehouse implementovat nebo podporují funkci ERROR_LINE(). Pokud budete mít ve svém kódu, budete muset odebrat tak, aby vyhovoval s SQL Data Warehouse. Použijte místo toho popisky dotazu v kódu k implementaci ekvivalentní funkce. Další podrobnosti najdete v tématu [popisek](sql-data-warehouse-develop-label.md) článku.

## <a name="using-throw-and-raiserror"></a>Pomocí THROW a RAISERROR
THROW je více moderní implementaci pro vyvolávání výjimek v SQL Data Warehouse, ale RAISERROR je také podporována. Existuje několik rozdílů, které jsou vhodné důrazem ale.

* Uživatelem definované chybové zprávy čísla nemůže být v rozsahu 100 000-150 000 pro THROW
* Chybové zprávy RAISERROR stanoví na 50 000
* Použití systémových není podporováno.

## <a name="limitations"></a>Omezení
SQL Data Warehouse má několik omezení, které se týkají transakce.

Ty jsou následující:

* Žádné distribuovaných transakcí
* Žádné vnořené transakce povolené
* Bez uložení bodů povoleno
* Žádné pojmenované transakce
* Žádné označené transakce
* Žádná podpora pro DDL například CREATE TABLE uvnitř uživatelské transakce

## <a name="next-steps"></a>Další postup
Další informace o optimalizaci transakce, najdete v části [transakce osvědčené postupy](sql-data-warehouse-develop-best-practices-transactions.md). Další informace o ostatní osvědčené postupy pro SQL Data Warehouse najdete v tématu [osvědčené postupy pro SQL Data Warehouse](sql-data-warehouse-best-practices.md).

