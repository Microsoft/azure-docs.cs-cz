---
title: Správa historických dat pomocí zásad uchovávání informací – Azure SQL Edge
description: Naučte se spravovat historická data pomocí zásad uchovávání dat v Azure SQL Edge.
keywords: SQL Edge, uchovávání dat
services: sql-edge
ms.service: sql-edge
ms.topic: conceptual
author: SQLSourabh
ms.author: sourabha
ms.reviewer: sstein
ms.date: 09/04/2020
ms.openlocfilehash: f547f0b3219889b54364c4805c2dd5b183a9861a
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/29/2021
ms.locfileid: "93392363"
---
# <a name="manage-historical-data-with-retention-policy"></a>Správa historických dat pomocí zásad uchovávání informací

Uchovávání dat může být v databázi a kterékoli z podkladových tabulek povoleno jednotlivě a umožňuje uživatelům vytvářet flexibilní zásady stárnutí pro své tabulky a databáze. Použití uchovávání dat je jednoduché: vyžaduje pouze jeden parametr, který má být nastaven během vytváření tabulky nebo jako součást operace ALTER TABLE. 

Po definovanýchí zásad uchovávání dat pro databázi a podkladovou tabulku se spustí úloha časovače času na pozadí, která odebere všechny zastaralé záznamy z tabulky povolené pro uchovávání dat. Identifikace odpovídajících řádků a jejich odebrání z tabulky probíhá transparentně v úloze na pozadí, která je naplánována a spuštěna systémem. Podmínka stáří pro řádky tabulky je kontrolována na základě sloupce použitého jako `filter_column` v definici tabulky. Pokud je doba uchování například nastavená na jeden týden, řádky tabulky, které mají nárok na vyčištění, splňují jednu z těchto podmínek: 

- Pokud sloupec filtru používá datový typ DATETIMEOFFSET, je podmínka `filter_column < DATEADD(WEEK, -1, SYSUTCDATETIME())`
- V opačném případě je podmínka `filter_column < DATEADD(WEEK, -1, SYSDATETIME())`

## <a name="data-retention-cleanup-phases"></a>Fáze čištění uchovávání dat

Operace čištění uchovávání dat se skládá ze dvou fází. 
- Fáze zjišťování – v této fázi operace vyčištění identifikuje všechny tabulky v uživatelských databázích a vytvoří seznam pro vyčištění. Zjišťování se spouští jednou denně.
- Fáze čištění – v této fázi se čištění spustí na všech tabulkách s omezenou dobou uchovávání dat, která se identifikuje ve fázi zjišťování. Pokud nelze operaci čištění provést v tabulce, bude tato tabulka v aktuálním běhu vynechána a bude opakována v další iteraci. Během čištění se používají následující principy.
    - Pokud je zastaralý řádek uzamčen jinou transakcí, tento řádek je vynechán. 
    - Vyčistěte spuštění s výchozím nastavením časového limitu zámku 5 sekund. Pokud se zámky v tabulkách v časovém intervalu nedají získat, tabulka se v aktuálním běhu přeskočí a v další iteraci se zopakuje.
    - Pokud při čištění tabulky dojde k chybě, tato tabulka se přeskočí a v další iteraci se vyzvednutí.

## <a name="manual-cleanup"></a>Ruční vyčištění

V závislosti na nastavení uchovávání dat v tabulce a povaze úloh v databázi je možné, že automatické vyčištění vlákna nemusí během běhu zcela odebrat všechny zastaralé řádky. Aby vám pomohl s tímto a mohly uživatelům ručně odebrat zastaralé řádky, je `sys.sp_cleanup_data_retention` uložená procedura zavedená v Azure SQL Edge. 

Tato uložená procedura používá tři parametry. 
    - Název schématu – název vlastnícího schématu pro tabulku. Toto je povinný parametr. 
    - Název tabulky – název tabulky, pro kterou se spouští ruční vyčištění. Toto je povinný parametr. 
    - počet řádků (Output) – výstupní proměnná. Vrátí počet řádků vyčištěný ručním čištěním SP. Toto je volitelný parametr. 

Následující příklad ukazuje provedení ručního vyčištění SP pro tabulku `dbo.data_retention_table` .

```sql
declare @rowcnt bigint 
EXEC sys.sp_cleanup_data_retention 'dbo', 'data_retention_table', @rowcnt output 
select @rowcnt 
```

## <a name="how-obsolete-rows-are-deleted"></a>Způsob odstranění zastaralých řádků

Proces čištění závisí na rozložení indexu v tabulce. Vytvoří se úloha na pozadí, která provede zastaralé vyčištění dat pro všechny tabulky s omezenou dobou uchovávání. Vyčištění logiky pro index rowstore (B-Tree nebo Heap) odstraní zastaralý řádek v menších blocích (až 10 000) minimalizuje tlak na protokol databáze a podsystém v/v. I když logika vyčištění používá požadovaný index B-Tree, pořadí odstranění pro řádky, které jsou starší než doba uchování, nelze pevně zaručit. Proto neprovádějte žádnou závislost na pořadí čištění v aplikacích.

Úloha čištění pro clusterovaný columnstore odebere celou skupinu řádků najednou (obvykle obsahuje 1 000 000 řádků každého), což je velmi efektivní, zejména při vygenerování dat a výpadku při vysokém tempu.

![Vyčištění uchovávání dat](./media/data-retention-cleanup/data-retention-cleanup.png)

Vynikající komprese dat a efektivní vyčištění pro uchovávání informací vytváří clusterový index columnstore ideální volbou pro scénáře, kdy vaše zatížení rychle generuje vysoké množství dat.

> [!Note]
> V případě indexů a haldy B-stromu spustí uchovávání dat v podkladových tabulkách odstraňovací dotaz, který může být v konfliktu s triggery odstranění v tabulkách. Doporučuje se buď odebrat triggery odstranění z tabulek, nebo Nepovolit uchovávání dat v tabulkách, které mají aktivační událost DML odstranit.

## <a name="monitoring-data-retention-cleanup"></a>Vyčištění uchovávání dat monitorování

Operace čištění zásad uchovávání dat se dají monitorovat pomocí rozšířených událostí (XEvents) ve službě Azure SQL Edge. Další informace o rozšířených událostech najdete v tématu [XEvents Overview](/sql/relational-databases/extended-events/extended-events). 

Následující šest rozšířených událostí vám pomůžou sledovat stav operací čištění. 

| Název | Popis |
|------| ------------|
| data_retention_task_started  | Nastane, pokud se spustí úloha na pozadí pro vyčištění tabulek se zachováním zásad uchovávání informací. |
| data_retention_task_completed  | Vyvolá se v případě, že je úloha na pozadí pro vyčištění tabulek se zakončením zásad uchovávání informací. |
| data_retention_task_exception  | Vyvolá se v případě, že úloha na pozadí pro vyčištění tabulek se zásadami uchovávání neproběhne mimo proces čištění uchovávání, který je specifický pro tabulku. |
| data_retention_cleanup_started  | Vyvolá se v případě, že se spustí vyčištění procesu tabulky se zásadami uchovávání dat. |
| data_retention_cleanup_exception  | Dojde k chybě procesu čištění tabulky se zásadami uchovávání informací. |
| data_retention_cleanup_completed  | Vyvolá se v případě, že dojde k ukončení procesu čištění tabulky se zásadami uchovávání dat. |  

Kromě toho `RING_BUFFER_DATA_RETENTION_CLEANUP` byl do sys.dm_os_ring_buffers zobrazení dynamické správy přidán nový typ kruhové vyrovnávací paměti s názvem. Toto zobrazení lze použít k monitorování operací čištění uchovávání dat. 


## <a name="next-steps"></a>Další kroky
- [Zásady uchovávání dat](data-retention-overview.md)
- [Povolit a zakázat zásady uchovávání dat](data-retention-enable-disable.md)