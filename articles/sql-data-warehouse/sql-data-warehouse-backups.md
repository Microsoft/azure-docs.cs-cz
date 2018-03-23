---
title: "Zálohování Azure SQL Data Warehouse – snímky geograficky redundantní | Microsoft Docs"
description: "Další informace o zálohování integrovanou databází SQL Data Warehouse, které vám umožní obnovit do bodu obnovení nebo jiné zeměpisné oblasti Azure SQL Data Warehouse."
services: sql-data-warehouse
documentationcenter: 
author: barbkess
manager: jhubbard
editor: 
ms.assetid: b5aff094-05b2-4578-acf3-ec456656febd
ms.service: sql-data-warehouse
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.custom: backup-restore
ms.date: 10/23/2017
ms.author: jrj;barbkess
ms.openlocfilehash: 159a1d34caba829750da33dbc4ad403fb21cd147
ms.sourcegitcommit: 9d317dabf4a5cca13308c50a10349af0e72e1b7e
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/01/2018
---
# <a name="backup-and-restore-in-sql-data-warehouse"></a>Zálohování a obnovení v SQL Data Warehouse
Tento článek vysvětluje, jaké jsou specifikace záloh v SQL Data Warehouse. Použít zálohování datového skladu k obnovení databázi snímku primární oblasti nebo obnovit zálohu geograficky vaší spárovat geografické oblasti. 

## <a name="what-is-a-data-warehouse-backup"></a>Co je zálohu datového skladu?
Zálohu datového skladu je kopii databáze, můžete použít k obnovení datového skladu.  Vzhledem k tomu, že datový sklad SQL je distribuovaného systému, zálohu datového skladu se skládá z mnoho souborů, které se nacházejí v úložišti Azure. Zálohu datového skladu zahrnuje snímky místní databáze a geo zálohování databáze a soubory, které jsou přidružené k datovému skladu. 

## <a name="local-snapshot-backups"></a>Místní snímek zálohy
SQL Data Warehouse přebírá snímky datového skladu v průběhu dne. Snímky jsou k dispozici sedm dní. SQL Data Warehouse podporuje osm hodin plánovaného bodu obnovení (RPO). Datový sklad v primární oblasti můžete obnovit jednu snímků prováděné v posledních sedmi dnech.

Pokud chcete zobrazit při spuštění poslední snímek, spusťte tento dotaz na online SQL Data Warehouse. 

```sql
select   top 1 *
from     sys.pdw_loader_backup_runs 
order by run_id desc
;
```

## <a name="geo-backups"></a>Geo-backups
SQL Data Warehouse provádí zálohu geograficky jednou denně za účelem [spárované datového centra](../best-practices-availability-paired-regions.md). Plánovaný bod obnovení pro geografické obnovení je 24 hodin. Geograficky zálohování můžete obnovit na server v spárovat geografické oblasti. geograficky zálohování zajistí, že datový sklad můžete obnovit v případě, že snímky nelze získat přístup v primární oblasti.

Geograficky zálohy jsou ve výchozím. Pokud váš datový sklad je optimalizován pro pružnost, můžete [chodit](/powershell/module/azurerm.sql/set-azurermsqldatabasegeobackuppolicy) nechcete-li. Nelze vyjádření výslovného nesouhlasu geo zálohy s optimalizovaná pro výpočetní výkon vrstvě.

## <a name="backup-costs"></a>Zálohování náklady
Si všimnete, že faktury Azure má položku řádku pro Storage úrovně Premium a položku řádku pro geograficky redundantní úložiště. Zřizování úložiště Premium je celková cena pro ukládání dat v primární oblasti, která zahrnuje snímky.  Geograficky redundantní poplatků popisuje jsou náklady na ukládání geo záloh.  

Celková cena vaší primární datového skladu a sedm dní snímků objektů Blob v Azure se zaokrouhlí na nejbližší TB. Například pokud váš datový sklad je 1,5 TB a snímky používat 100 GB, se vám účtuje 2 TB dat tempem Azure Premium Storage. 

> [!NOTE]
> Každý snímek je původně prázdné a zvětšování při provádění změn do primárního datového skladu. Všechny snímky zvýšit velikost jako změny datového skladu. Proto náklady na úložiště pro snímky růst podle rychlost změny.
> 
> 

Pokud používáte geograficky redundantní úložiště, obdržíte samostatné úložiště poplatků. Geograficky redundantní úložiště se fakturuje standardní sazbou přístup pro čtení geograficky redundantní úložiště (RA-GRS).

Další informace o cenách služby SQL Data Warehouse najdete v tématu [SQL Data Warehouse – ceny](https://azure.microsoft.com/pricing/details/sql-data-warehouse/).

## <a name="snapshot-retention-when-a-data-warehouse-is-paused"></a>Snímek uchování při datového skladu je pozastavena.
SQL Data Warehouse nevytváří žádné snímky a nevyprší snímky, zatímco datového skladu je pozastavena. Stáří snímku nedojde ke změně při datového skladu je pozastavena. Počet dnů, po který datový sklad je online, není dny kalendáře vychází uchování snímku.

Například pokud snímku spustí říjen 1 na 16: 00 a datového skladu je pozastavena. října 3 na 16: 00, jsou snímky až dva dny. Pokud datový sklad přejde do režimu online je snímek dva dny. Pokud datový sklad přechodu do režimu online. října 5 na 16: 00, snímku je dva dny a zůstane další pět dní.

Když datového skladu přejde do režimu online, SQL Data Warehouse obnoví nových snímků a vyprší platnost snímků, pokud mají víc než sedm dnů s daty.

## <a name="can-i-restore-a-dropped-data-warehouse"></a>Můžete obnovit vynechaných datového skladu?
Při umístění datového skladu SQL Data Warehouse vytvoří poslední snímek a uloží jej pro sedm dní. Datový sklad můžete obnovit do bodu poslední obnovení vytvořeného v odstranění. 

> [!IMPORTANT]
> Pokud odstraníte logické instance systému SQL server, všechny databáze patřící k instanci budou také odstraněny a nelze jej obnovit. Nelze obnovit odstraněné serveru.
> 

## <a name="next-steps"></a>Další postup
Chcete-li obnovit SQL data warehouse, najdete v části [obnovení SQL data warehouse](sql-data-warehouse-restore-database-overview.md).

Přehled kontinuity obchodních, najdete v části [obchodní kontinuity přehled](../sql-database/sql-database-business-continuity.md)
