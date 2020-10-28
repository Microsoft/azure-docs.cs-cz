---
title: Diagnostika výkonu v měřítku
description: Tento článek popisuje, jak řešit problémy s výkonem s výkonným škálováním v Azure SQL Database.
services: sql-database
ms.service: sql-database
ms.subservice: service
ms.custom: seo-lt-2019 sqldbrb=1
ms.topic: troubleshooting
author: denzilribeiro
ms.author: denzilr
ms.reviewer: sstein
ms.date: 10/18/2019
ms.openlocfilehash: ed31ff5d77b258d141a77fc174c2d5452adf7d01
ms.sourcegitcommit: 400f473e8aa6301539179d4b320ffbe7dfae42fe
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/28/2020
ms.locfileid: "92791711"
---
# <a name="sql-hyperscale-performance-troubleshooting-diagnostics"></a>Diagnostika řešení potíží s výkonem s škálovatelným škálováním SQL
[!INCLUDE[appliesto-sqldb](../includes/appliesto-sqldb.md)]

Aby bylo možné řešit problémy s výkonem v databázi s škálovatelným škálováním, je obecným bodem pro Azure SQL Database výpočetní uzel [Obecné metodologie ladění výkonu](monitor-tune-overview.md) . S ohledem na [distribuovanou architekturu](service-tier-hyperscale.md#distributed-functions-architecture) v rámci škálování jsme ale přidali další diagnostiku, která vám pomůže. Tento článek popisuje diagnostická data specifická pro škálování.

## <a name="log-rate-throttling-waits"></a>Čekání na omezení rychlosti protokolu

Každá Azure SQL Database úroveň služby má omezení četnosti generování protokolu vyhodnocená prostřednictvím [zásad správného řízení přenosů protokolů](resource-limits-logical-server.md#transaction-log-rate-governance). V měřítku je limit generování protokolu aktuálně nastaven na 100 MB/s bez ohledu na úroveň služby. Existují však situace, kdy je potřeba omezit rychlost generování protokolu u primární repliky výpočetních dat, aby se Slaa obnovitelnost. Toto omezení se stane, když je [Server stránky nebo jiná výpočetní replika](service-tier-hyperscale.md#distributed-functions-architecture) významně za použití nových záznamů protokolu z protokolovací služby.

Následující typy čekání (v [Sys.dm_os_wait_stats](/sql/relational-databases/system-dynamic-management-views/sys-dm-os-wait-stats-transact-sql/)) popisují důvody, proč je možné omezit rychlost protokolu u primární repliky Compute:

|Typ čekání    |Popis                         |
|-------------          |------------------------------------|
|RBIO_RG_STORAGE        | Vyvolá se v případě, že je míra generování protokolu primárního výpočetního uzlu databáze škálováním v důsledku omezené spotřeby protokolu na stránkách.         |
|RBIO_RG_DESTAGE        | Vyvolá se v případě, že se omezuje rychlost generování protokolu databáze výpočetních uzlů v rámci škálování z důvodu opožděné spotřeby protokolu pomocí dlouhodobého úložiště protokolů.         |
|RBIO_RG_REPLICA        | Vyvolá se v případě, že je v důsledku omezené spotřeby protokolu pro čitelné sekundární repliky omezována rychlost generování protokolu databáze výpočetního uzlu.         |
|RBIO_RG_LOCALDESTAGE   | Vyvolá se v případě, že je omezena rychlost generování protokolu databáze výpočetních uzlů v rámci škálování z důvodu opožděné spotřeby protokolu službou Log Service.         |

## <a name="page-server-reads"></a>Čtení stránkového serveru

Výpočetní repliky neukládá do mezipaměti úplnou kopii databáze místně. Data, která jsou místní pro výpočetní repliku, se ukládají do fondu vyrovnávací paměti (v paměti) a do místní mezipaměti rozšíření odolného fondu vyrovnávací paměti (RBPEX), která je částečnou mezipamětí datových stránek (bez pokrytí). Tato místní mezipaměť RBPEX má úměrně velikost výpočetní kapacity a je trojnásobná paměť výpočetní vrstvy. RBPEX se podobá fondu vyrovnávací paměti v tom, že obsahuje nejčastěji používaná data. Každý server stránky na druhé straně má pokrývání mezipaměti RBPEX pro část databáze, kterou udržuje.

Pokud je ve výpočetní replice vydaný objekt pro čtení, pokud data ve fondu vyrovnávací paměti nebo v místní mezipaměti RBPEX neexistují, volání funkce GetPage (pageId, LSN) a stránka se načte z odpovídajícího serveru stránky. Čtení ze stránkovacích serverů je vzdálené čtení a proto je pomalejší než čtení z místní RBPEX. Při řešení potíží s výkonem souvisejících s vstupně-výstupních operací potřebujeme vědět, kolik IOs bylo provedeno prostřednictvím poměrně pomalejšího čtení vzdáleného serveru stránky.

Některá dynamická spravovaná zobrazení (zobrazení dynamické správy) a rozšířené události obsahují sloupce a pole, která určují počet vzdálených čtení ze stránky, který se dá porovnat s celkovými čteními. Úložiště dotazů také zachycuje vzdálené čtení v rámci statistik doby běhu dotazu.

- Sloupce pro čtení na serveru sestav jsou k dispozici v zobrazení dynamické správy provádění a zobrazení katalogu, jako je například:

  - [sys.dm_exec_requests](/sql/relational-databases/system-dynamic-management-views/sys-dm-exec-requests-transact-sql/)
  - [sys.dm_exec_query_stats](/sql/relational-databases/system-dynamic-management-views/sys-dm-exec-query-stats-transact-sql/)
  - [sys.dm_exec_procedure_stats](/sql/relational-databases/system-dynamic-management-views/sys-dm-exec-procedure-stats-transact-sql/)
  - [sys.dm_exec_trigger_stats](/sql/relational-databases/system-dynamic-management-views/sys-dm-exec-trigger-stats-transact-sql/)
  - [sys.query_store_runtime_stats](/sql/relational-databases/system-catalog-views/sys-query-store-runtime-stats-transact-sql/)
- Do následujících rozšířených událostí se přidají čtení stránkového serveru:
  - sql_statement_completed
  - sp_statement_completed
  - sql_batch_completed
  - rpc_completed
  - scan_stopped
  - query_store_begin_persist_runtime_stat
  - dotaz – store_execution_runtime_info
- ActualPageServerReads/ActualPageServerReadAheads se přidají do XML plánu dotazů pro skutečné plány. Příklad:

`<RunTimeCountersPerThread Thread="8" ActualRows="90466461" ActualRowsRead="90466461" Batches="0" ActualEndOfScans="1" ActualExecutions="1" ActualExecutionMode="Row" ActualElapsedms="133645" ActualCPUms="85105" ActualScans="1" ActualLogicalReads="6032256" ActualPhysicalReads="0" ActualPageServerReads="0" ActualReadAheads="6027814" ActualPageServerReadAheads="5687297" ActualLobLogicalReads="0" ActualLobPhysicalReads="0" ActualLobPageServerReads="0" ActualLobReadAheads="0" ActualLobPageServerReadAheads="0" />`

> [!NOTE]
> Chcete-li tyto atributy zobrazit v okně vlastnosti plánu dotazu, je vyžadována SSMS 18,3 nebo novější.

## <a name="virtual-file-stats-and-io-accounting"></a>Statistika virtuálních souborů a monitorování vstupně-výstupních operací

V Azure SQL Database je [Sys.dm_io_virtual_file_stats ()](/sql/relational-databases/system-dynamic-management-views/sys-dm-io-virtual-file-stats-transact-sql/) DMF hlavním způsobem, jak monitorovat SQL Database v/v. Vlastnosti v/v v měřítku se liší v důsledku [distribuované architektury](service-tier-hyperscale.md#distributed-functions-architecture). V této části se zaměřujeme na vstupně-výstupní operace (čtení a zápisy) do datových souborů, jak je vidět v tomto DMF. V rámci škálování každý datový soubor, který je viditelný v tomto DMF, odpovídá serveru vzdálené stránky. Mezipaměť RBPEX, na kterou se odkazuje tady, je místní mezipaměť založená na SSD, která je na výpočetní replice nepokrývá mezipaměť.

### <a name="local-rbpex-cache-usage"></a>Použití místní mezipaměti RBPEX

Místní mezipaměť RBPEX existuje ve službě COMPUTE repliky v místním úložišti SSD. Proto je vstupně-výstupní operace s touto mezipamětí rychlejší než v/v proti serverům vzdálené stránky. V současné době [Sys.dm_io_virtual_file_stats ()](/sql/relational-databases/system-dynamic-management-views/sys-dm-io-virtual-file-stats-transact-sql/) v databázi s velkým měřítkem obsahuje speciální řádek, který hlásí vstupně-výstupní operace s místní mezipamětí RBPEX na výpočetní replice. Tento řádek má hodnotu 0 pro `database_id` `file_id` sloupec i. Například následující dotaz vrátí statistiku využití RBPEX od spuštění databáze.

`select * from sys.dm_io_virtual_file_stats(0,NULL);`

Poměr čtení provedených v RBPEX pro agregované čtení provedené u všech ostatních datových souborů poskytuje poměr přístupů do mezipaměti RBPEX.

### <a name="data-reads"></a>Čtení dat

- Když jsou čtení vydávány databázovým strojem SQL Server na výpočetní replice, mohou být obsluhovány buď místní mezipamětí RBPEX, nebo pomocí vzdálené stránky nebo kombinací dvou, pokud je čteno více stránek.
- Když výpočetní replika přečte některé stránky z konkrétního souboru, například file_id 1, pokud se tato data nacházejí výhradně v místní mezipaměti RBPEX, všechny vstupně-výstupní operace pro toto čtení se započítávají na file_id 0 (RBPEX). Pokud je některá část těchto dat v místní mezipaměti RBPEX a některá část je na vzdáleném serveru stránky, v/v je k dis 0 pro součást obsluhované z RBPEX a část obsluhovaná ze serveru vzdálené stránky je k disfile_id k file_id 1.
- Když výpočetní replika požádá o stránku na konkrétní hodnotu [LSN](/sql/relational-databases/sql-server-transaction-log-architecture-and-management-guide/) ze stránky serveru, pokud server stránky nezachytil požadavek na hodnotu LSN, přečtení na výpočetní replice počká, dokud se server stránky nepřed vrátí do výpočetní repliky. Pro všechny čtení ze stránky serveru na výpočetní replice se zobrazí PAGEIOLATCH_ * typ čekání, pokud čeká na tuto vstupně-výstupní operaci. V měřítku tato čekací doba zahrnuje čas potřebný k zaznamenání požadované stránky na straně serveru na požadovanou hodnotu LSN a čas potřebný k přenosu stránky ze serveru stránky do výpočetní repliky.
- Velké čtení, jako je čtení předem, se často provádí pomocí [čtení "bodového shromažďování"](/sql/relational-databases/reading-pages/). To umožňuje čtení až 4 MB stránek najednou, považuje se za jeden načtený v modulu SQL Server Database Engine. Pokud jsou však čtena data v RBPEX, jsou tyto čtení účtovány jako více individuálních čtení 8 KB, protože fond vyrovnávací paměti a RBPEX vždy používají stránky 8 KB. V důsledku toho může být počet čtení v IOs, který se zobrazuje u RBPEX, větší než skutečný počet IOs, který modul provedl.

### <a name="data-writes"></a>Zápisy dat

- Primární replika COMPUTE neprovádí zápis přímo na stránky serverů. Místo toho se záznamy protokolu z protokolovací služby přehrávají na odpovídajících stránkách.
- Zápisy, ke kterým dochází ve výpočetní replice, se převážně zapisují do místního RBPEX (file_id 0). V případě zápisů na logických souborech, které jsou větší než 8 KB, jinými slovy, které se prováděly pomocí funkce [shromáždit-zápis](/sql/relational-databases/writing-pages/), jsou jednotlivé operace zápisu přeloženy do více než jednoho zápisu na více 8 KB na RBPEX od fondu vyrovnávací paměti a RBPEX vždy používají stránky 8 KB. V důsledku toho může být počet IOs pro zápis, který se zobrazuje u RBPEX, větší než skutečný počet IOs provedený modulem.
- Soubory, které nejsou RBPEX, nebo datové soubory jiné než file_id 0, které odpovídají stránkovým serverům, zobrazují také zápisy. Ve vrstvě služeb s škálovatelným škálováním se tyto zápisy simulují, protože výpočetní repliky Nikdy nezapisovat přímo na stránky serverů. Zápisu vstupně-výstupních operací a propustnosti se účtují tak, jak se vyskytují na výpočetní replice, ale latence u datových souborů jiných než file_id 0 neodráží skutečnou latenci zápisů na straně serveru.

### <a name="log-writes"></a>Zápisy do protokolu

- Ve službě Primary COMPUTE je zápis do protokolu v file_id 2 sys.dm_io_virtual_file_stats. Zápis do protokolu primárního COMPUTE je zápis do zóny pro odpočívadlo protokolu.
- Záznamy protokolu nejsou u sekundární repliky na potvrzení zabezpečení posíleny. V měřítku je protokol protokolovací služby aplikován na asynchronní repliky do sekundární repliky. Vzhledem k tomu, že zápisy protokolů se ve skutečnosti nevyskytují na sekundárních replikách, je jakékoli monitorování protokolu IOs na sekundárních replikách pouze pro účely sledování.

## <a name="data-io-in-resource-utilization-statistics"></a>Data v/v statistiky využití prostředků

V Neškálovatelné databázi, kombinované čtení a zápis IOPS proti datovým souborům, které odpovídají limitu datových IOPS [zásad správného řízení prostředků](./resource-limits-logical-server.md#resource-governance) , se ve sloupci hlásí v [Sys.dm_db_resource_stats](/sql/relational-databases/system-dynamic-management-views/sys-dm-db-resource-stats-azure-sql-database) a [Sys.resource_stats](/sql/relational-databases/system-catalog-views/sys-resource-stats-azure-sql-database) zobrazení `avg_data_io_percent` . Stejná hodnota je hlášena v Azure Portal jako _procento vstupně-výstupních dat_ .

V databázi s velkým měřítkem se v tomto sloupci zprávy týkají využití data IOPS vzhledem k limitu pro místní úložiště pouze v případě výpočetní repliky, konkrétně v/v na RBPEX a `tempdb` . Hodnota 100% v tomto sloupci udává, že zásady správného řízení prostředků omezují místní úložiště IOPS. Pokud se to koreluje s problémem s výkonem, vyladěním úlohy vygenerujte méně vstupně-výstupní operace nebo zvyšte cíl databázové služby, abyste zvýšili _maximální počet datových IOPS_ pro řízení [prostředků.](resource-limits-vcore-single-databases.md) V případě zásad správného řízení prostředků čtení a zápisu RBPEX systém počítá jednotlivé systémy 8 – KB a nikoli větší IOs, které může vydávat SQL Server databázový stroj.

Data v datovém vstupu proti serverům vzdálené stránky nejsou uvedena v zobrazeních využití prostředků nebo na portálu, ale jsou uvedena v [Sys.dm_io_virtual_file_stats ()](/sql/relational-databases/system-dynamic-management-views/sys-dm-io-virtual-file-stats-transact-sql/) DMF, jak je uvedeno výše.

## <a name="additional-resources"></a>Další zdroje informací

- Pro omezení prostředků vCore pro izolovanou databázi s jedním škálováním najdete v tématu [limity Vcore úrovně služby škálování na úrovni služeb](resource-limits-vcore-single-databases.md#hyperscale---provisioned-compute---gen5) .
- Azure SQL Database ladění výkonu najdete v tématu [výkon dotazů v Azure SQL Database](performance-guidance.md)
- Ladění výkonu pomocí úložiště dotazů najdete v tématu [monitorování výkonu pomocí úložiště dotazů](/sql/relational-databases/performance/monitoring-performance-by-using-the-query-store/) .
- DMV pro sledování skriptů najdete v tématu [monitorování výkonu Azure SQL Database pomocí zobrazení dynamické správy](monitoring-with-dmvs.md) .