---
title: Transakce v SQL Data Warehouse | Microsoft Docs
description: Tipy pro provádění transakcí v Azure SQL Data Warehouse na vývoj řešení.
services: sql-data-warehouse
documentationcenter: NA
author: jrowlandjones
manager: jhubbard
editor: ''
ms.assetid: ae621788-e575-41f5-8bfe-fa04dc4b0b53
ms.service: sql-data-warehouse
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: data-services
ms.custom: t-sql
ms.date: 10/31/2016
ms.author: jrj;barbkess
ms.openlocfilehash: 29d53e18539f2c24dd64090b2ac6f9dd4c783961
ms.sourcegitcommit: 6fcd9e220b9cd4cb2d4365de0299bf48fbb18c17
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/05/2018
---
# <a name="transactions-in-sql-data-warehouse"></a>Transakce v SQL Data Warehouse
Jak jste zvyklí, SQL Data Warehouse podporuje transakce v rámci úlohy datového skladu. Ale zajistit, že výkon služby SQL Data Warehouse je udržován na úrovni škálování některé funkce omezeny ve srovnání s systému SQL Server. V tomto článku klade důraz rozdíly a uvádí ostatní. 

## <a name="transaction-isolation-levels"></a>Úrovně izolace transakce
SQL Data Warehouse implementuje transakce ACID. Izolaci podpora transakcí je však omezená na `READ UNCOMMITTED` a toto nastavení nelze změnit. Můžete implementovat řadu kódování metody, aby se zabránilo nekonzistence čtení dat, pokud se jedná o problém pro vás. Nejčastěji používané metody využít uživatelům zabránit v dotazování na data, která stále připraveném funkce CTAS a přepínání oddílů tabulky (často označované jako posuvné okno vzor). Zobrazení, které předem filtrovat data je také oblíbených přístup.  

## <a name="transaction-size"></a>Velikost transakce
Transakce úpravy jednoho datového je omezen velikostí. Limit dnes platí "za distribuční". Proto lze vypočítat celkové přidělení vynásobením limit počtu distribučních. Přibližná maximální počet řádků v transakci rozdělíte krytky distribuční celková velikost každý řádek. Pro proměnnou délkou zvažte trvá délka průměrná sloupce, nikoli pomocí maximální velikosti.

V následující tabulce následující předpoklady byly provedeny:

* Rovnoměrné rozdělení dat došlo k chybě. 
* Délka průměrná řádek je 250 bajtů

| [DWU][DWU] | Cap za distribuční (GiB) | Počet distribuce | MAXIMÁLNÍ velikost transakce (GiB) | # Řádků na jeden distribuce | Maximální počet řádků na transakci |
| --- | --- | --- | --- | --- | --- |
| OD DW100 |1 |60 |60 |4,000,000 |240,000,000 |
| DW200 |1,5 |60 |90 |6 000 000 |360,000,000 |
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

K a minimalizuje množství dat, zapíšou do protokolu naleznete [transakce osvědčené postupy] [ Transactions best practices] článku.

> [!WARNING]
> Transakce maximální velikost lze dosáhnout pouze pro hodnoty HASH nebo dokonce ROUND_ROBIN distribuované tabulky, kde je šíření data. Pokud transakce je zápis dat zkreslilo způsobem do distribucí limit je pravděpodobně dosažitelná před transakce maximální velikost.
> <!--REPLICATED_TABLE-->
> 
> 

## <a name="transaction-state"></a>Stav transakce
SQL Data Warehouse používá funkci XACT_STATE() k hlášení selhání transakce pomocí hodnoty -2. To znamená, že transakce se nezdařilo a je označen pro vrácení zpět pouze

> [!NOTE]
> Použití -2 funkcí XACT_STATE k označení selhání transakce představuje různé chování v systému SQL Server. SQL Server používá k reprezentaci rámci transakce hodnota -1. SQL Server může tolerovat chybami v transakci bez nutnosti označit jako rámci. Například `SELECT 1/0` by způsobit chybu, ale bez vynucení transakce v rámci stavu. SQL Server také umožňuje čtení v rámci transakce. Ale SQL Data Warehouse, nebudou vám to. Pokud dojde k chybě v transakci SQL Data Warehouse automaticky přejde do stavu-2 a nebudete moct provádět žádné další výběr příkazy, dokud příkaz byla vrácena zpět. Proto je důležité zkontrolovat, že kód aplikace zobrazíte, pokud používá XACT_STATE() jako je třeba provést změny kódu.
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

Pokud necháte kódu, protože je výše a zobrazí se následující chybová zpráva:

Msg 111233, Level 16, stav 1, řádek 1 111233; Aktuální transakce byla přerušena a všechny čekající změny byly vráceny zpět. Příčina: Transakce ve stavu jen pro vrácení zpět nebyla vrácena explicitně před DDL, DML nebo příkazu SELECT.

Také nebude získat výstup funkce ERROR_ *.

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

Všechny, které se změnily je, že `ROLLBACK` transakce museli dojít před čtení informací o chybě v `CATCH` bloku.

## <a name="errorline-function"></a>Error_Line() – funkce
Je také vhodné poznamenat, že SQL Data Warehouse implementovat nebo podporují funkci ERROR_LINE(). Pokud máte ve vašem kódu, budete muset odebrat tak, aby vyhovoval s SQL Data Warehouse to. Použijte místo toho popisky dotazu v kódu k implementaci ekvivalentní funkce. Podrobnosti najdete [popisek] [ LABEL] další podrobnosti o této funkci najdete v článku.

## <a name="using-throw-and-raiserror"></a>Pomocí THROW a RAISERROR
THROW je více moderní implementaci pro vyvolávání výjimek v SQL Data Warehouse, ale RAISERROR je také podporována. Existuje několik rozdílů, které jsou vhodné důrazem ale.

* Uživatelem definované chybové zprávy, které čísla nemůže být v rozsahu 100 000-150 000 pro THROW
* Chybové zprávy RAISERROR stanoví na 50 000
* Použití systémových není podporováno.

## <a name="limitiations"></a>Limitiations
SQL Data Warehouse má několik omezení, které se týkají transakce.

Ty jsou následující:

* Žádné distribuovaných transakcí
* Žádné vnořené transakce povolené
* Bez uložení bodů povoleno
* Žádné pojmenované transakce
* Žádné označené transakce
* Žádná podpora pro DDL jako `CREATE TABLE` uvnitř uživatele definované transakce

## <a name="next-steps"></a>Další postup
Další informace o optimalizaci transakce, najdete v části [transakce osvědčené postupy][Transactions best practices].  Další informace o ostatní osvědčené postupy pro SQL Data Warehouse najdete v tématu [osvědčené postupy pro SQL Data Warehouse][SQL Data Warehouse best practices].

<!--Image references-->

<!--Article references-->
[DWU]: ./sql-data-warehouse-overview-what-is.md
[development overview]: ./sql-data-warehouse-overview-develop.md
[Transactions best practices]: ./sql-data-warehouse-develop-best-practices-transactions.md
[SQL Data Warehouse best practices]: ./sql-data-warehouse-best-practices.md
[LABEL]: ./sql-data-warehouse-develop-label.md

<!--MSDN references-->

<!--Other Web references-->
