---
title: Azure SQL Data Warehouse zálohování a obnovení – snímky geograficky redundantní | Microsoft Docs
description: Zjistěte, jak funguje zálohování a obnovení v Azure SQL Data Warehouse. Použijte k obnovení datového skladu pro bod obnovení v primární oblasti datového skladu zálohy nebo použijte geograficky redundantní zálohy obnovit do jiné zeměpisné oblasti.
services: sql-data-warehouse
author: ronortloff
manager: jhubbard
ms.service: sql-data-warehouse
ms.topic: conceptual
ms.component: design
ms.date: 03/28/2018
ms.author: rortloff
ms.reviewer: igorstan
ms.openlocfilehash: 7f540bca0d2eb2c9009a386bd14a5beda2912014
ms.sourcegitcommit: 34e0b4a7427f9d2a74164a18c3063c8be967b194
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/30/2018
---
# <a name="backup-and-restore-in-azure-sql-data-warehouse"></a>Zálohování a obnovení v Azure SQL Data Warehouse
Zjistěte, jak funguje zálohování a obnovení v Azure SQL Data Warehouse. Použijte k obnovení datového skladu pro bod obnovení v primární oblasti datového skladu zálohy nebo použijte geograficky redundantní zálohy obnovit do jiné zeměpisné oblasti. 

## <a name="what-is-backup-and-restore"></a>Co je zálohování a obnovení?
A *zálohování dat skladu* je kopii databáze, můžete použít k obnovení datového skladu.  Vzhledem k tomu, že datový sklad SQL je distribuovaného systému, zálohu datového skladu se skládá z mnoho souborů, které se nacházejí v úložišti Azure. Zálohu datového skladu zahrnuje snímky místní databáze a geo zálohování databáze a soubory, které jsou přidružené k datovému skladu. 

A *datového skladu obnovení* je nový datový sklad, který je vytvořený z existující zálohy nebo odstraněné datového skladu. Obnovená data warehouse znovu vytvoří skladu zálohovaná data v určitém čase. Obnovení datového skladu je nedílnou součást vámi vyžádaných jakékoli obchodní strategie pro obnovení kontinuity a po havárii, protože znovu vytvoří data po náhodného poškození nebo odstranění.

Místní a zeměpisné obnovení jsou součástí funkcí pro obnovení po havárii SQL Data Warehouse. 

## <a name="local-snapshot-backups"></a>Místní snímek zálohy
Místní snímek zálohy jsou integrované funkce služby.  Nemusíte je povolit. 

SQL Data Warehouse přebírá snímky datového skladu v průběhu dne. Snímky jsou k dispozici sedm dní. SQL Data Warehouse podporuje osm hodin plánovaného bodu obnovení (RPO). Datový sklad v primární oblasti můžete obnovit jednu snímků prováděné v posledních sedmi dnech.

Pokud chcete zobrazit při spuštění poslední snímek, spusťte tento dotaz na online SQL Data Warehouse. 

```sql
select   top 1 *
from     sys.pdw_loader_backup_runs 
order by run_id desc
;
```

### <a name="snapshot-retention-when-a-data-warehouse-is-paused"></a>Snímek uchování při datového skladu je pozastavena.
SQL Data Warehouse nevytváří žádné snímky a nevyprší snímky, zatímco datového skladu je pozastavena. Stáří snímku nedojde ke změně při datového skladu je pozastavena. Počet dnů, po který datový sklad je online, není dny kalendáře vychází uchování snímku.

Například pokud snímku spustí říjen 1 na 16: 00 a datového skladu je pozastavena. října 3 na 16: 00, jsou snímky až dva dny. Pokud datový sklad přejde do režimu online je snímek dva dny. Pokud datový sklad přechodu do režimu online. října 5 na 16: 00, snímku je dva dny a zůstane další pět dní.

Když datového skladu přejde do režimu online, SQL Data Warehouse obnoví nových snímků a vyprší platnost snímků, pokud mají víc než sedm dnů s daty.

### <a name="snapshot-retention-when-a-data-warehouse-is-dropped"></a>Snímek uchování při přetažení datového skladu
Při umístění datového skladu SQL Data Warehouse vytvoří poslední snímek a uloží jej pro sedm dní. Datový sklad můžete obnovit do bodu poslední obnovení vytvořeného v odstranění. 

> [!IMPORTANT]
> Pokud odstraníte logické instance systému SQL server, všechny databáze patřící k instanci budou také odstraněny a nelze jej obnovit. Nelze obnovit odstraněné serveru.
> 

## <a name="geo-backups"></a>Geo-backups
SQL Data Warehouse provádí zálohu geograficky jednou denně za účelem [spárované datového centra](../best-practices-availability-paired-regions.md). Plánovaný bod obnovení pro geografické obnovení je 24 hodin. Geograficky zálohování můžete obnovit na server v spárovat geografické oblasti. Zálohu geograficky zajistí, že datový sklad můžete obnovit v případě, že snímky nelze získat přístup v primární oblasti.

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

## <a name="restoring-from-restore-points"></a>Obnovení z bodů obnovení
Každý snímek je bod obnovení, který představuje spuštění snímku. Chcete-li obnovit datového skladu, vyberte bod obnovení a vydejte příkaz restore.  

SQL Data Warehouse vždy obnoví zálohování na nový datový sklad. Můžete buď ponechat obnovené datový sklad a stávající nebo odstraňte jednu z nich. Pokud chcete nahradit aktuální datového skladu obnovené datového skladu, můžete přejmenovat pomocí [ALTER DATABASE (Azure SQL Data Warehouse)](/sql/t-sql/statements/alter-database-azure-sql-data-warehouse) s možností změnit název. 

Chcete-li obnovit datového skladu, přečtěte si téma [obnovit data warehouse pomocí portálu Azure](sql-data-warehouse-restore-database-portal.md), [obnovit data warehouse pomocí prostředí PowerShell](sql-data-warehouse-restore-database-powershell.md), nebo [obnovit data warehouse pomocí T-SQL](sql-data-warehouse-restore-database-rest-api.md) .

Chcete-li obnovit odstraněné nebo pozastavený datového skladu, můžete [vytvořit lístek podpory](sql-data-warehouse-get-started-create-support-ticket.md). 


## <a name="geo-redundant-restore"></a>Geograficky redundantní obnovení
Datový sklad můžete obnovit do libovolné oblasti podpora Azure SQL Data Warehouse stejné úrovni zvolené výkonu. 

> [!NOTE]
> Geograficky redundantní obnovení nesmí Nesouhlasili jste tuto funkci.
> 
> 

## <a name="restore-timeline"></a>Obnovení časové osy
Databázi můžete obnovit do libovolného bodu obnovení k dispozici v posledních sedmi dnů. Snímky spusťte každých čtyř do osmi hodin a jsou k dispozici sedm dní. Pokud snímek je starší než 7 dní, vypršení jeho platnosti a jeho bod obnovení již není k dispozici. 

Není zálohám proti pozastavený datového skladu. Pokud váš datový sklad je pozastavena pro více než 7 dní, nebudete mít všechny body obnovení. 

## <a name="restore-costs"></a>Obnovení náklady
Zřizování úložiště pro obnovená data warehouse se fakturuje rychlostí Azure Premium Storage. 

Pokud pozastavíte obnovené datového skladu, budou se vám účtovat pro úložiště rychlostí Azure Premium Storage. Výhodou pozastavení je, že se vám neúčtují poplatky za výpočetní prostředky.

Další informace o cenách služby SQL Data Warehouse najdete v tématu [SQL Data Warehouse – ceny](https://azure.microsoft.com/pricing/details/sql-data-warehouse/).

## <a name="restore-use-cases"></a>Obnovení případy použití
Primární použití pro obnovení datového skladu je k obnovení dat po před náhodnou ztrátou dat nebo poškození. Obnovení datového skladu můžete taky uchovávat zálohu po dobu delší než 7 dní. Po obnovení zálohy online máte datového skladu a můžete ho po neomezenou dobu, abyste ušetřili náklady výpočetní pozastavit. Pozastavený databáze způsobuje poplatky za úložiště rychlostí Azure Premium Storage. 

## <a name="next-steps"></a>Další postup
Další informace o plánování po havárii, najdete v části [obchodní kontinuity přehled](../sql-database/sql-database-business-continuity.md)
