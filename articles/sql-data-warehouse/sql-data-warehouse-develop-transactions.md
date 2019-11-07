---
title: Používání transakcí
description: Tipy pro implementaci transakcí v Azure SQL Data Warehouse pro vývoj řešení.
services: sql-data-warehouse
author: XiaoyuMSFT
manager: craigg
ms.service: sql-data-warehouse
ms.topic: conceptual
ms.subservice: development
ms.date: 03/22/2019
ms.author: xiaoyul
ms.reviewer: igorstan
ms.custom: seo-lt-2019
ms.openlocfilehash: 09fc0f7cee38f799322a1914848a5176e9a223a1
ms.sourcegitcommit: 609d4bdb0467fd0af40e14a86eb40b9d03669ea1
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/06/2019
ms.locfileid: "73692783"
---
# <a name="using-transactions-in-sql-data-warehouse"></a>Použití transakcí v SQL Data Warehouse
Tipy pro implementaci transakcí v Azure SQL Data Warehouse pro vývoj řešení.

## <a name="what-to-expect"></a>Co můžete očekávat
Podle očekávání SQL Data Warehouse podporuje transakce jako součást úlohy datového skladu. Pokud ale chcete mít jistotu, že se výkon SQL Data Warehouse udržuje ve velkém měřítku, jsou některé funkce v porovnání s SQL Server omezené. Tento článek popisuje rozdíly a seznam ostatních. 

## <a name="transaction-isolation-levels"></a>Úrovně izolace transakce
SQL Data Warehouse implementuje transakce v KYSELINě. Úroveň izolace transakční podpory je však omezená na čtení bez potvrzení; tuto úroveň nelze změnit. Pokud je čtení nepotvrzeno, můžete implementovat řadu metod kódování, které zabrání nečistým čtením dat. Nejoblíbenější metody používají přepínání oddílů CTAS a Table (často označované jako posuvné okno), které uživatelům brání v dotazování na data, která stále připravují. Zobrazení, která předem filtrují data, jsou také oblíbeným přístupem.  

## <a name="transaction-size"></a>Velikost transakce
Jedna transakce změny dat má omezené velikosti. Limit se aplikuje na distribuci. Z tohoto důvodu může být celkové přidělení vypočítáno vynásobením omezení počtem distribucí. K aproximaci maximálního počtu řádků v transakci rozdělte velikost distribučního čísla celkové velikosti každého řádku. U sloupců s proměnlivou délkou zvažte použití průměrné délky sloupce, a ne omezení velikosti.

V tabulce níže byly provedeny následující předpoklady:

* Došlo k rovnoměrné distribuci dat. 
* Průměrná délka řádku je 250 bajtů.

## <a name="gen2"></a>Gen2

| [DWU](sql-data-warehouse-overview-what-is.md) | Cap na distribuci (GB) | Počet distribucí | MAXIMÁLNÍ velikost transakce (GB) | Počet řádků na distribuci | Maximální počet řádků na transakci |
| --- | --- | --- | --- | --- | --- |
| DW100c |1 |60 |60 |4 000 000 |240 000 000 |
| DW200c |1,5 |60 |90 |6 000 000 |360 000 000 |
| DW300c |2,25 |60 |135 |9 000 000 |540 000 000 |
| DW400c |3 |60 |180 |12 000 000 |720 000 000 |
| DW500c |3,75 |60 |225 |15 000 000 |900 000 000 |
| DW1000c |7,5 |60 |450 |30 000 000 |1 800 000 000 |
| DW1500c |11,25 |60 |675 |45 000 000 |2 700 000 000 |
| DW2000c |15 |60 |900 |60 000 000 |3 600 000 000 |
| DW2500c |18,75 |60 |1125 |75 000 000 |4 500 000 000 |
| DW3000c |22,5 |60 |1 350 |90 000 000 |5 400 000 000 |
| DW5000c |37,5 |60 |2 250 |150 000 000 |9 000 000 000 |
| DW6000c |45 |60 |2 700 |180 000 000 |10 800 000 000 |
| DW7500c |56,25 |60 |3 375 |225 000 000 |13 500 000 000 |
| DW10000c |75 |60 |4 500 |300 000 000 |18 000 000 000 |
| DW15000c |112,5 |60 |6 750 |450 000 000 |27 000 000 000 |
| DW30000c |225 |60 |13 500 |900 000 000 |54 000 000 000 |

## <a name="gen1"></a>Gen1

| [DWU](sql-data-warehouse-overview-what-is.md) | Cap na distribuci (GB) | Počet distribucí | MAXIMÁLNÍ velikost transakce (GB) | Počet řádků na distribuci | Maximální počet řádků na transakci |
| --- | --- | --- | --- | --- | --- |
| OD DW100 |1 |60 |60 |4 000 000 |240 000 000 |
| DW200 |1,5 |60 |90 |6 000 000 |360 000 000 |
| DW300 |2,25 |60 |135 |9 000 000 |540 000 000 |
| DW400 |3 |60 |180 |12 000 000 |720 000 000 |
| DW500 |3,75 |60 |225 |15 000 000 |900 000 000 |
| ÚROVEŇ DW600 |4,5 |60 |270 |18 000 000 |1 080 000 000 |
| DW1000 |7,5 |60 |450 |30 000 000 |1 800 000 000 |
| DW1200 |9 |60 |540 |36 000 000 |2 160 000 000 |
| DW1500 |11,25 |60 |675 |45 000 000 |2 700 000 000 |
| DW2000 |15 |60 |900 |60 000 000 |3 600 000 000 |
| DW3000 |22,5 |60 |1 350 |90 000 000 |5 400 000 000 |
| DW6000 |45 |60 |2 700 |180 000 000 |10 800 000 000 |

Limit velikosti transakce je použit na transakci nebo operaci. Není aplikováno napříč všemi souběžnými transakcemi. Proto každá transakce má povoleno zapsat toto množství dat do protokolu. 

Pokud chcete optimalizovat a minimalizovat množství dat zapsaných do protokolu, přečtěte si článek věnované [osvědčeným postupům pro transakce](sql-data-warehouse-develop-best-practices-transactions.md) .

> [!WARNING]
> Maximální velikost transakce lze dosáhnout pouze pro distribuované tabulky HASH nebo ROUND_ROBIN, kde je rozprostření dat sudé. Pokud transakce zapisuje data šikmým způsobem na rozdělení, je pravděpodobně dosaženo limitu před maximální velikostí transakce.
> <!--REPLICATED_TABLE-->
> 
> 

## <a name="transaction-state"></a>Stav transakce
SQL Data Warehouse používá funkci XACT_STATE () k ohlášení neúspěšné transakce pomocí hodnoty 2. Tato hodnota znamená, že transakce se nezdařila a je označena pouze pro vrácení zpět.

> [!NOTE]
> Použití-2 funkcí XACT_STATE k označení neúspěšné transakce představuje odlišné chování pro SQL Server. SQL Server používá hodnotu-1 pro reprezentaci transakce nesvěřené. SQL Server může tolerovat některé chyby v transakci, aniž by bylo nutné je označit jako nepotvrzené. Například `SELECT 1/0` by způsobil chybu, ale nevynutila transakci do nepotvrzeného stavu. SQL Server také povoluje čtení v nesvěřené transakci. SQL Data Warehouse to ale neumožňuje. Pokud dojde k chybě uvnitř SQL Data Warehouse transakce, automaticky vstoupí do stavu-2 a dokud se příkaz nevrátí zpět, nebude možné provést žádné další příkazy SELECT. Proto je důležité zkontrolovat, zda kód aplikace používá XACT_STATE (), protože může být nutné provést úpravy kódu.
> 
> 

Například v SQL Server se může zobrazit transakce, která vypadá takto:

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

Msg 111233, úroveň 16, stav 1, řádek 1 111233; Aktuální transakce byla přerušena a všechny probíhající změny byly vráceny zpět. Příčina: transakce ve stavu vrácení zpět nebyla explicitně vrácena zpět před příkazem DDL, DML nebo SELECT.

Nebudete mít výstup funkcí ERROR_ *.

V SQL Data Warehouse musí být kód mírně pozměněn:

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

Nyní je zjištěno očekávané chování. Chyba v transakci je spravovaná a funkce ERROR_ * poskytují hodnoty podle očekávání.

Všechny, které se změnily, je, že vrácení transakce se musí nacházet před čtením informací o chybě v bloku CATCH.

## <a name="error_line-function"></a>Error_Line () – funkce
Je také potřeba poznamenat, že SQL Data Warehouse neimplementuje nebo nepodporuje funkci ERROR_LINE (). Pokud máte ve svém kódu, musíte ho odebrat, aby byl kompatibilní s SQL Data Warehouse. Místo toho použijte pro implementaci ekvivalentních funkcí popisky dotazů ve svém kódu. Další podrobnosti najdete v článku s [popisem](sql-data-warehouse-develop-label.md) .

## <a name="using-throw-and-raiserror"></a>Použití THROW a RAISERROR
THROW je moderní implementace pro vyvolávání výjimek v SQL Data Warehouse, ale také podporuje funkci RAISERROR. Existuje několik rozdílů, které jsou pro vás za platební pozornost.

* Uživatelem definované chybové zprávy nemohou být v rozsahu 100 000-150 000 pro THROW.
* Chybové zprávy RAISERROR jsou opraveny na 50 000
* Použití sys. Messages se nepodporuje.

## <a name="limitations"></a>Omezení
SQL Data Warehouse má několik dalších omezení, která se vztahují na transakce.

Jsou následující:

* Žádné distribuované transakce
* Nejsou povolené žádné vnořené transakce.
* Nejsou povoleny žádné body ukládání.
* Žádné pojmenované transakce
* Žádné označené transakce
* V uživatelsky definované transakci není žádná podpora elementu DDL, jako je CREATE TABLE.

## <a name="next-steps"></a>Další kroky
Další informace o optimalizaci transakcí naleznete v tématu [osvědčené postupy pro transakce](sql-data-warehouse-develop-best-practices-transactions.md). Další informace o dalších SQL Data Warehouse osvědčených postupech najdete v článku [SQL Data Warehouse osvědčené postupy](sql-data-warehouse-best-practices.md).

