---
title: Použití transakcí
description: Tipy pro implementaci transakcí s vyhrazeným fondem SQL ve službě Azure synapse Analytics pro vývoj řešení
services: synapse-analytics
author: XiaoyuMSFT
manager: craigg
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: sql
ms.date: 04/15/2020
ms.author: xiaoyul
ms.reviewer: igorstan
ms.openlocfilehash: a2597a4bc6c5ed44f0e0050be3f69d7e840665e5
ms.sourcegitcommit: 96918333d87f4029d4d6af7ac44635c833abb3da
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/04/2020
ms.locfileid: "93323844"
---
# <a name="use-transactions-with-dedicated-sql-pool-in-azure-synapse-analytics"></a>Použití transakcí s vyhrazeným fondem SQL ve službě Azure synapse Analytics

Tipy pro implementaci transakcí s vyhrazeným fondem SQL ve službě Azure synapse Analytics pro vývoj řešení

## <a name="what-to-expect"></a>Co očekávat

Jak očekáváte, vyhrazený fond SQL podporuje transakce jako součást úlohy datového skladu. Pokud ale chcete mít jistotu, že se výkon vyhrazeného fondu SQL zachová, jsou v porovnání s SQL Server omezeny některé funkce. Tento článek popisuje rozdíly a seznam ostatních.

## <a name="transaction-isolation-levels"></a>Úrovně izolace transakce

Fond SQL implementuje transakce v KYSELINě. Úroveň izolace transakční podpory je výchozí pro čtení nepotvrzených.  Můžete ji změnit na čtení POTVRZENé izolace snímku tím, že zapnete možnost READ_COMMITTED_SNAPSHOT Database pro uživatelskou databázi, když se připojíte k hlavní databázi.  

Po povolení se všechny transakce v této databázi spustí v režimu čtení POTVRZENé izolace snímku a nastavení číst nepotvrzené na úrovni relace se nerespektuje. Podrobnosti naleznete v [příkazu ALTER DATABASE set Options (Transact-SQL)](https://docs.microsoft.com/sql/t-sql/statements/alter-database-transact-sql-set-options?view=azure-sqldw-latest&preserve-view=true) .

## <a name="transaction-size"></a>Velikost transakce
Jedna transakce změny dat má omezené velikosti. Limit se aplikuje na distribuci. V takovém případě lze celkové přidělení vypočítat vynásobením limitu distribucí. 

K aproximaci maximálního počtu řádků v transakci rozdělte velikost distribučního čísla celkové velikosti každého řádku. U sloupců s proměnlivou délkou zvažte použití průměrné délky sloupce, a ne omezení velikosti.

V tabulce níže byly provedeny následující předpoklady:

* Došlo k rovnoměrné distribuci dat.
* Průměrná délka řádku je 250 bajtů.

## <a name="gen2"></a>Gen2

| [DWU](../sql-data-warehouse/sql-data-warehouse-overview-what-is.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json) | Cap na distribuci (GB) | Počet distribucí | MAXIMÁLNÍ velikost transakce (GB) | Počet řádků na distribuci | Maximální počet řádků na transakci |
| --- | --- | --- | --- | --- | --- |
| DW100c |1 |60 |60 |4 000 000 |240 000 000 |
| DW200c |1.5 |60 |90 |6 000 000 |360 000 000 |
| DW300c |2.25 |60 |135 |9 000 000 |540 000 000 |
| DW400c |3 |60 |180 |12 000 000 |720 000 000 |
| DW500c. |3,75 |60 |225 |15 000 000 |900 000 000 |
| DW1000c |7,5 |60 |450 |30 000 000 |1 800 000 000 |
| DW1500c |11,25 |60 |675 |45 000 000 |2 700 000 000 |
| DW2000c |15 |60 |900 |60 000 000 |3 600 000 000 |
| DW2500c |18,75 |60 |1125 |75 000 000 |4 500 000 000 |
| DW3000c |22,5 |60 |1 350 |90 000 000 |5 400 000 000 |
| DW5000c |37,5 |60 |2 250 |150 000 000 |9 000 000 000 |
| DW6000c |45 |60 |2 700 |180 000 000 |10 800 000 000 |
| DW7500c |56,25 |60 |3 375 |225 000 000 |13 500 000 000 |
| DW10000c |75 |60 |4 500 |300 000 000 |18 000 000 000 |
| DW15000c |112,5 |60 |6 750 |450 000 000 |27 000 000 000 |
| DW30000c |225 |60 |13 500 |900 000 000 |54 000 000 000 |

## <a name="gen1"></a>Gen1

| [DWU](../sql-data-warehouse/sql-data-warehouse-overview-what-is.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json) | Cap na distribuci (GB) | Počet distribucí | MAXIMÁLNÍ velikost transakce (GB) | Počet řádků na distribuci | Maximální počet řádků na transakci |
| --- | --- | --- | --- | --- | --- |
| OD DW100 |1 |60 |60 |4 000 000 |240 000 000 |
| DW200 |1.5 |60 |90 |6 000 000 |360 000 000 |
| DW300 |2.25 |60 |135 |9 000 000 |540 000 000 |
| DW400 |3 |60 |180 |12 000 000 |720 000 000 |
| DW500 |3,75 |60 |225 |15 000 000 |900 000 000 |
| ÚROVEŇ DW600 |4.5 |60 |270 |18 000 000 |1 080 000 000 |
| DW1000 |7,5 |60 |450 |30 000 000 |1 800 000 000 |
| DW1200 |9 |60 |540 |36 000 000 |2 160 000 000 |
| DW1500 |11,25 |60 |675 |45 000 000 |2 700 000 000 |
| DW2000 |15 |60 |900 |60 000 000 |3 600 000 000 |
| DW3000 |22,5 |60 |1 350 |90 000 000 |5 400 000 000 |
| DW6000 |45 |60 |2 700 |180 000 000 |10 800 000 000 |

Limit velikosti transakce je použit na transakci nebo operaci. Není aplikováno napříč všemi souběžnými transakcemi. Proto každá transakce má povoleno zapsat toto množství dat do protokolu.

Chcete-li optimalizovat a minimalizovat množství dat zapsaných do protokolu, přečtěte si článek věnované [osvědčeným postupům pro transakce](../sql-data-warehouse/sql-data-warehouse-develop-best-practices-transactions.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json) .

> [!WARNING]
> Maximální velikost transakce lze dosáhnout pouze pro hodnoty HASH nebo ROUND_ROBIN distribuované tabulky, kde je rozprostření dat sudé. Pokud transakce zapisuje data šikmým způsobem na rozdělení, je pravděpodobně dosaženo limitu před maximální velikostí transakce.
> <!--REPLICATED_TABLE-->

## <a name="transaction-state"></a>Stav transakce

Fond SQL používá funkci XACT_STATE () k ohlášení neúspěšné transakce pomocí hodnoty 2. Tato hodnota znamená, že transakce se nezdařila a je označena pouze pro vrácení zpět.

> [!NOTE]
> Použití-2 funkcí XACT_STATE k označení neúspěšné transakce představuje jiné chování pro SQL Server. SQL Server používá hodnotu-1 pro reprezentaci transakce nesvěřené. SQL Server může tolerovat některé chyby v transakci, aniž by bylo nutné je označit jako nepotvrzené. Například `SELECT 1/0` by došlo k chybě, ale nevynucují transakci do nepotvrzeného stavu. SQL Server také povoluje čtení v nesvěřené transakci. Vyhrazený fond SQL ho ale neumožňuje. Pokud dojde k chybě uvnitř vyhrazené transakce fondu SQL, automaticky vstoupí do stavu-2 a dokud se příkaz nevrátí zpět, nebude možné provést žádné další příkazy SELECT. Je proto důležité zkontrolovat, zda kód aplikace používá XACT_STATE (), protože může být nutné provést úpravy kódu.

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

Výstup funkcí ERROR_ * se nezobrazuje.

V vyhrazeném fondu SQL musí být kód mírně pozměněn:

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

Nyní je zjištěno očekávané chování. Chyba v transakci je spravovaná a funkce ERROR_ * poskytují hodnoty podle očekávání.

Všechny, které se změnily, je, že vrácení transakce se musí nacházet před čtením informací o chybě v bloku CATCH.

## <a name="error_line-function"></a>Error_Line () – funkce

Také je třeba poznamenat, že vyhrazený fond SQL neimplementuje ani nepodporuje funkci ERROR_LINE (). Pokud máte tuto funkci v kódu, je nutné ji odebrat, aby byla kompatibilní s vyhrazeným fondem SQL. Místo toho použijte pro implementaci ekvivalentních funkcí popisky dotazů ve svém kódu. Další informace najdete v článku s [popisem](develop-label.md) .

## <a name="use-of-throw-and-raiserror"></a>Použití THROW a RAISERROR

THROW je moderní implementace pro vyvolávání výjimek ve vyhrazeném fondu SQL, ale je také podporována příkaz RAISERROR. Existuje několik rozdílů, které jsou pro vás za platební pozornost.

* Uživatelem definované chybové zprávy nemohou být v rozsahu 100 000-150 000 pro THROW
* Chybové zprávy RAISERROR jsou opraveny na 50 000
* Použití sys. Messages se nepodporuje.

## <a name="limitations"></a>Omezení

Fond SQL má několik dalších omezení týkajících se transakcí. Jsou to tyto:

* Žádné distribuované transakce
* Nejsou povolené žádné vnořené transakce.
* Nejsou povoleny žádné body ukládání.
* Žádné pojmenované transakce
* Žádné označené transakce
* V uživatelsky definované transakci není žádná podpora elementu DDL, jako je CREATE TABLE.

## <a name="next-steps"></a>Další kroky

Další informace o optimalizaci transakcí naleznete v tématu [osvědčené postupy pro transakce](../sql-data-warehouse/sql-data-warehouse-develop-best-practices-transactions.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json). K dispozici jsou i další Příručky k osvědčeným postupům pro [fond SQL](best-practices-sql-pool.md) a [SQL Server bez serveru (Preview)](best-practices-sql-on-demand.md).
