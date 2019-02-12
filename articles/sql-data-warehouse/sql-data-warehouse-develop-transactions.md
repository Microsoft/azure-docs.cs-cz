---
title: Použití transakcí ve službě Azure SQL Data Warehouse | Dokumentace Microsoftu
description: Tipy pro provádění transakcí ve službě Azure SQL Data Warehouse pro vývoj řešení.
services: sql-data-warehouse
author: ckarst
manager: craigg
ms.service: sql-data-warehouse
ms.topic: conceptual
ms.subservice: implement
ms.date: 02/10/2019
ms.author: cakarst
ms.reviewer: igorstan
ms.openlocfilehash: ac2bc9fc05a7014cd575ec531ef0a6ba08b2609e
ms.sourcegitcommit: 39397603c8534d3d0623ae4efbeca153df8ed791
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/12/2019
ms.locfileid: "56100138"
---
# <a name="using-transactions-in-sql-data-warehouse"></a>Použití transakcí v SQL Data Warehouse
Tipy pro provádění transakcí ve službě Azure SQL Data Warehouse pro vývoj řešení.

## <a name="what-to-expect"></a>Co můžete očekávat
Jak by jste očekávali, SQL Data Warehouse podporuje transakce jako součást úlohy datového skladu. Však zajistit, že výkon služby SQL Data Warehouse je udržován na úrovni škálovací některé funkce omezeny oproti serveru SQL Server. Tento článek zdůrazňuje rozdíly a uvádí ostatní. 

## <a name="transaction-isolation-levels"></a>Úrovně izolace transakce
SQL Data Warehouse implementuje transakce ACID. Úroveň izolace transakční podporu je však omezená na READ UNCOMMITTED; Tato úroveň se nedá změnit. Pokud READ UNCOMMITTED je důležitý, můžete implementovat řadu psaní kódu metod zabránit nepřesné data. Nejoblíbenější metody používají CTAS a přepínání oddílů tabulky (často označované jako posuvné okno vzor) uživatelům zabránit v dotazování na data, která se stále připravuje. Zobrazení, která předem filtrovat data jsou také oblíbené přístup.  

## <a name="transaction-size"></a>Velikost transakce
Transakce změny jednoho datového má omezenou velikost. Omezení se použijí na distribuci. Proto bude celkový přidělený lze vypočítat vynásobením počtu distribučních limit. K přibližné maximální počet řádků v transakci dělení zakončení distribuce celková velikost každého řádku. Pro sloupce s proměnlivou délkou vezměte v úvahu trvá o délce hodnotu ze sloupce average spíš než maximální velikost.

V následující tabulce následujících předpokladů byly provedeny:

* Rovnoměrnou distribuci přenosu dat došlo k chybě. 
* Průměrná délka řádku je 250 bajtů

| [DWU](sql-data-warehouse-overview-what-is.md) | Limit na distribuci (GiB) | Počet distribucí | MAXIMÁLNÍ velikost transakce (GiB) | # Řádků na distribuci | Maximální počet řádků na transakci |
| --- | --- | --- | --- | --- | --- |
| DW100 |1 |60 |60 |4,000,000 |240,000,000 |
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

Omezení velikosti transakce se vztahují na transakci nebo operace. Není použita ve všech souběžných transakcí. Proto každou transakci je povolený zápis takové množství dat do protokolu. 

A minimalizuje množství dat zapsaných do protokolu, najdete [transakce osvědčené postupy](sql-data-warehouse-develop-best-practices-transactions.md) článku.

> [!WARNING]
> Velikost maximální transakce jde dosáhnout jenom pro hodnoty HASH nebo dokonce ROUND_ROBIN distribuované tabulky, kde rozšíření dat je. Pokud transakce zapisuje data výrazně nerovnoměrnou distribucí způsobem do distribuce limit je pravděpodobně dosáhl před transakce maximální velikost.
> <!--REPLICATED_TABLE-->
> 
> 

## <a name="transaction-state"></a>Stav transakce
SQL Data Warehouse používá funkci XACT_STATE() oznámit neúspěšné transakce pomocí hodnoty -2. Tato hodnota znamená, že transakce se nezdařilo a je označen pro vrácení pouze.

> [!NOTE]
> Použití -2 XACT_STATE funkcí k označení selhání transakce představuje různé chování systému SQL Server. SQL Server používá k reprezentaci nezapsatelná transakce hodnotu -1. SQL Server může tolerovat možnost chybami v transakci, aniž by ji museli být označen jako nezapsatelná. Například `SELECT 1/0` by způsobit chybu, ale bez vynucení do stavu nezapsatelná transakce. SQL Server také umožňuje čtení v nezapsatelná transakce. Nicméně SQL Data Warehouse, nebudou vám s tím. Pokud dojde k chybě v transakci SQL Data Warehouse automaticky přejde do stavu-2 a nebude možné provést jakékoli další výběr příkazů, dokud příkaz byla vrácena zpět. Proto je důležité zkontrolovat, že kódu aplikace zobrazíte, pokud používá XACT_STATE() jako je třeba provést změny kódu.
> 
> 

V systému SQL Server se může zobrazit transakce, která vypadá takto:

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

Předchozí kód poskytuje následující chybová zpráva:

Msg – 111233, Level 16, stav 1 řádek 1 111233; Aktuální transakce byla přerušena a všechny probíhající změny byly vráceny zpět. Příčina: Transakce ve stavu jen pro vrácení zpět nebyla vrácena explicitně před DDL, DML nebo příkazu SELECT.

Nezískáte výstup funkce ERROR_ *.

Ve službě SQL Data Warehouse musí být mírně změnit kód:

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

Nyní se vyskytuje očekávané chování. Chyba v transakci spravuje a funkce ERROR_ * zadejte hodnoty, podle očekávání.

Vše, co došlo ke změně je, že vrácení transakce museli stát před číst informace o chybě v bloku CATCH.

## <a name="errorline-function"></a>Error_Line() – funkce
Je také vhodné poznamenat, že SQL Data Warehouse neimplementuje ani podporu ERROR_LINE() funkce. Pokud budete mít ve vašem kódu, budete muset odebrat bude kompatibilní s SQL Data Warehouse. Místo toho použijte k implementaci ekvivalentní funkce dotazu popisky ve vašem kódu. Další podrobnosti najdete v tématu [popisek](sql-data-warehouse-develop-label.md) článku.

## <a name="using-throw-and-raiserror"></a>Použití vyvolání výjimky a RAISERROR
Vyvolání výjimky je Modernější implementace pro vyvolání výjimek v SQL Data Warehouse, ale RAISERROR je také podporována. Existuje několik rozdílů, které stojí za to ale dejte pozor na.

* Uživatelem definované chybové zprávy čísla nesmějí být v rozsahu 100 000 150 000 pro vyvolání výjimky
* RAISERROR chybové zprávy jsou opraveny s 50 000
* Použití systémových není podporováno.

## <a name="limitations"></a>Omezení
SQL Data Warehouse má několik omezení, které se týkají transakce.

Jsou následující:

* Bez distribuované transakce
* Žádné vnořené transakce povoleno
* Bez uložení bodů povoleno
* Žádné pojmenované transakce
* Žádné označené transakce
* Žádná podpora pro DDL, jako je například vytvoření tabulky v uživatelské transakci

## <a name="next-steps"></a>Další postup
Další informace o optimalizaci transakce, naleznete v tématu [transakce osvědčené postupy](sql-data-warehouse-develop-best-practices-transactions.md). Další informace o dalších osvědčených postupech pro SQL Data Warehouse, najdete v článku [osvědčené postupy SQL Data Warehouse](sql-data-warehouse-best-practices.md).

