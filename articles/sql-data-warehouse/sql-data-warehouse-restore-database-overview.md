---
title: "Obnovení Azure datového skladu, a-místní a geograficky redundantní | Microsoft Docs"
description: "Přehled možností obnovení databáze pro obnovení databáze v Azure SQL Data Warehouse."
services: sql-data-warehouse
documentationcenter: NA
author: barbkess
manager: jenniehubbard
editor: 
ms.assetid: 3e01c65c-6708-4fd7-82f5-4e1b5f61d304
ms.service: sql-data-warehouse
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: data-services
ms.custom: backup-restore
ms.date: 12/06/2017
ms.author: barbkess
ms.openlocfilehash: abf8f0b1005aec71812dc8ebfd12fe65250d7a0e
ms.sourcegitcommit: b5c6197f997aa6858f420302d375896360dd7ceb
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/21/2017
---
# <a name="sql-data-warehouse-restore"></a>Obnovení SQL Data Warehouse
> [!div class="op_single_selector"]
> * [Přehled][Overview]
> * [Portál][Portal]
> * [Prostředí PowerShell][PowerShell]
> * [REST][REST]
> 
> 

SQL Data Warehouse nabízí místní i zeměpisné obnovení jako součást po havárii jeho datového skladu obnovení. Použijte k obnovení datového skladu pro bod obnovení v primární oblasti datového skladu zálohy nebo použijte geograficky redundantní zálohy obnovit do jiné zeměpisné oblasti. Tento článek vysvětluje, jaké jsou specifikace obnovení datového skladu.

## <a name="what-is-a-data-warehouse-restore"></a>Co je datový sklad obnovení?
Obnovení datového skladu je nový datový sklad, který je vytvořený z existující zálohy nebo odstraněné datového skladu. Obnovená data warehouse znovu vytvoří skladu zálohovaná data v určitém čase. Vzhledem k tomu, že SQL Data Warehouse je distribuovaný systém, obnovení datového skladu je vytvořený z mnoha záložní soubory, které jsou uložené v Azure BLOB. 

Obnovení databáze je nedílnou součást vámi vyžádaných jakékoli obchodní strategie pro obnovení kontinuity a po havárii, protože znovu vytváří data po náhodného poškození nebo odstranění.

Další informace naleznete v tématu:

* [Zálohování SQL Data Warehouse](sql-data-warehouse-backups.md)
* [Přehled kontinuity obchodních](../sql-database/sql-database-business-continuity.md)

## <a name="data-warehouse-restore-points"></a>Body obnovení datového skladu
Jako výhodou používání Azure Premium Storage SQL Data Warehouse používá snímky Azure Storage Blob pro zálohování primárního datového skladu. Každý snímek je bod obnovení, který představuje spuštění snímku. Chcete-li obnovit datového skladu, vyberte bod obnovení a vydejte příkaz restore.  

SQL Data Warehouse vždy obnoví zálohování na nový datový sklad. Můžete buď ponechat obnovené datový sklad a stávající nebo odstraňte jednu z nich. Pokud chcete nahradit aktuální datového skladu obnovené datového skladu, můžete ho změnit.

Pokud potřebujete obnovit odstraněné nebo pozastavený datového skladu, můžete [vytvořit lístek podpory](sql-data-warehouse-get-started-create-support-ticket.md). 

<!-- 
### Can I restore a deleted data warehouse?

Yes, you can restore the last available restore point.

Yes, for the next seven calendar days. When you delete a data warehouse, SQL Data Warehouse actually keeps the data warehouse and its snapshots for seven days just in case you need the data. After seven days, you won't be able to restore to any of the restore points. -->

## <a name="geo-redundant-restore"></a>Geograficky redundantní obnovení
Datový sklad můžete obnovit do libovolné oblasti podpora Azure SQL Data Warehouse stejné úrovni zvolené výkonu. Upozorňujeme, že 9000 a 18000 DWU nejsou podporovány ve všech oblastech ve verzi Preview.

> [!NOTE]
> Geograficky redundantní obnovení nesmí Nesouhlasili jste tuto funkci.
> 
> 

## <a name="restore-timeline"></a>Obnovení časové osy
Databázi můžete obnovit do libovolného bodu obnovení k dispozici v posledních sedmi dnů. Snímky spuštění každých čtyř do osmi hodin a jsou k dispozici sedm dní. Pokud snímek je starší než 7 dní, vypršení jeho platnosti a jeho bod obnovení již není k dispozici.

## <a name="restore-costs"></a>Obnovení náklady
Zřizování úložiště pro obnovená data warehouse se fakturuje rychlostí Azure Premium Storage. 

Pokud pozastavíte obnovené datového skladu, budou se vám účtovat pro úložiště rychlostí Azure Premium Storage. Výhodou pozastavení je, že se vám neúčtují poplatky za výpočetní prostředky DWU.

Další informace o cenách služby SQL Data Warehouse najdete v tématu [SQL Data Warehouse – ceny](https://azure.microsoft.com/pricing/details/sql-data-warehouse/).

## <a name="uses-for-restore"></a>Používá pro obnovení
Primární použití pro obnovení datového skladu je k obnovení dat po před náhodnou ztrátou dat nebo poškození.

Obnovení datového skladu můžete taky uchovávat zálohu po dobu delší než 7 dní. Po obnovení zálohy online máte datového skladu a můžete ho po neomezenou dobu, abyste ušetřili náklady výpočetní pozastavit. Pozastavený databáze způsobuje poplatky za úložiště rychlostí Azure Premium Storage. 

## <a name="related-topics"></a>Související témata
### <a name="scenarios"></a>Scénáře
* Přehled kontinuity obchodních, najdete v části [obchodní kontinuity přehled](../sql-database/sql-database-business-continuity.md)

<!-- ### Tasks -->

Pokud chcete provést obnovení datového skladu, obnovte pomocí:

* Azure portálu, najdete v tématu [obnovit data warehouse pomocí portálu Azure](sql-data-warehouse-restore-database-portal.md)
* Rutiny prostředí PowerShell najdete v části [obnovení datového skladu pomocí rutin prostředí PowerShell](sql-data-warehouse-restore-database-powershell.md)
* Rozhraní REST API najdete v tématu [obnovit data warehouse pomocí rozhraní REST API](sql-data-warehouse-restore-database-rest-api.md)

<!-- ### Tutorials -->

<!--Image references-->

<!--Article references-->
[Azure SQL Database business continuity overview]: ../sql-database/sql-database-business-continuity.md
[Overview]: ./sql-data-warehouse-restore-database-overview.md
[Portal]: ./sql-data-warehouse-restore-database-portal.md
[PowerShell]: ./sql-data-warehouse-restore-database-powershell.md
[REST]: ./sql-data-warehouse-restore-database-rest-api.md

<!--MSDN references-->


<!--Other Web references-->
