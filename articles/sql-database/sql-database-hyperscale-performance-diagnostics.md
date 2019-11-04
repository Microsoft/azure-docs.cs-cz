---
title: Diagnostika výkonu Azure SQL Database v úrovni služby pro škálování na více úrovní | Microsoft Docs
description: Tento článek popisuje, jak řešit problémy s výkonem s výkonným škálováním v Azure SQL Database.
services: sql-database
ms.service: sql-database
ms.subservice: service
ms.topic: troubleshooting
author: denzilribeiro
ms.author: denzilr
ms.reviewer: sstein
ms.date: 10/18/2019
ms.openlocfilehash: fa8b02cf5ee6f24bcc77aae057f41842da721981
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/04/2019
ms.locfileid: "73521137"
---
# <a name="sql-hyperscale-performance-troubleshooting-diagnostics"></a>Diagnostika řešení potíží s výkonem s škálovatelným škálováním SQL


Pro řešení problémů s výkonem v databázi s škálovatelným škálováním je na výpočetním uzlu Azure SQL Database k dispočátečnímu bodu pro šetření výkonu [Obecné metodologie ladění výkonu](sql-database-monitor-tune-overview.md) . S ohledem na [distribuovanou architekturu](sql-database-service-tier-hyperscale.md#distributed-functions-architecture) v rámci škálování jsme ale přidali další diagnostiku, která vám pomůže. Tento článek popisuje diagnostická data specifická pro škálování.


## <a name="log-rate-throttling-waits"></a>Čekání na omezení rychlosti protokolu


Každá Azure SQL Database úroveň služby má omezení četnosti generování protokolu vyhodnocená prostřednictvím [zásad správného řízení přenosů protokolů](sql-database-resource-limits-database-server.md#transaction-log-rate-governance). V měřítku je limit generování protokolu aktuálně nastaven na 100 MB/s bez ohledu na úroveň služby. Existují však situace, kdy je potřeba omezit rychlost generování protokolu u primární repliky výpočetních dat, aby se Slaa obnovitelnost. Toto omezení se stane, když je [Server stránky nebo jiná výpočetní replika](sql-database-service-tier-hyperscale.md#distributed-functions-architecture) významně za použití nových záznamů protokolu z protokolovací služby.

Následující typy čekání (v [Sys. DM _os_wait_stats](/sql/relational-databases/system-dynamic-management-views/sys-dm-os-wait-stats-transact-sql/)) popisují důvody, proč je možné omezit rychlost protokolu u primární repliky Compute:

|Typ čekání    |Popis                         |
|-------------          |------------------------------------|
|RBIO_RG_STORAGE        | Vyvolá se v případě, že je míra generování protokolu primárního výpočetního uzlu databáze škálováním v důsledku omezené spotřeby protokolu na stránkách.         |
|RBIO_RG_DESTAGE        | Vyvolá se v případě, že se omezuje rychlost generování protokolu databáze výpočetních uzlů v rámci škálování z důvodu opožděné spotřeby protokolu pomocí dlouhodobého úložiště protokolů.         |
|RBIO_RG_REPLICA        | Vyvolá se v případě, že je v důsledku omezené spotřeby protokolu pro čitelné sekundární repliky omezována rychlost generování protokolu databáze výpočetního uzlu.         |
|RBIO_RG_LOCALDESTAGE   | Vyvolá se v případě, že je omezena rychlost generování protokolu databáze výpočetních uzlů v rámci škálování z důvodu opožděné spotřeby protokolu službou Log Service.         |


## <a name="page-server-reads"></a>Čtení stránkového serveru

Výpočetní repliky neukládá do mezipaměti úplnou kopii databáze místně. Data, která jsou místní pro výpočetní repliku, se ukládají do fondu vyrovnávací paměti (v paměti) a do místní mezipaměti rozšíření odolného fondu vyrovnávací paměti (RBPEX), která je částečnou mezipamětí datových stránek (bez pokrytí). Tato místní mezipaměť RBPEX má úměrně velikost výpočetní kapacity a je 3 časy paměti výpočetní vrstvy. RBPEX se podobá fondu vyrovnávací paměti v tom, že obsahuje nejčastěji používaná data. Každý server stránky na druhé straně má pokrývání mezipaměti RBPEX pro část databáze, kterou udržuje.
 
Pokud je ve výpočetní replice vydaný objekt pro čtení, pokud data ve fondu vyrovnávací paměti nebo v místní mezipaměti RBPEX neexistují, volání funkce GetPage (pageId, LSN) a stránka se načte z odpovídajícího serveru stránky. Čtení ze stránkovacích serverů je vzdálené čtení a proto je pomalejší než čtení z místní RBPEX. Při řešení potíží s výkonem souvisejících s vstupně-výstupních operací potřebujeme vědět, kolik IOs bylo provedeno prostřednictvím poměrně pomalejšího čtení vzdáleného serveru stránky.

Několik zobrazení dynamické správy a rozšířených událostí obsahuje sloupce a pole, která určují počet vzdálených čtení ze stránkového serveru, který se dá porovnat s celkovými čteními. 

- Sloupce pro čtení serveru stránky sestavy jsou k dispozici ve zobrazení dynamické správy spouštění, například:
    - [sys. DM _exec_requests](/sql/relational-databases/system-dynamic-management-views/sys-dm-exec-requests-transact-sql/)
    - [sys. DM _exec_query_stats](/sql/relational-databases/system-dynamic-management-views/sys-dm-exec-query-stats-transact-sql/)
    - [sys. DM _exec_procedure_stats](/sql/relational-databases/system-dynamic-management-views/sys-dm-exec-procedure-stats-transact-sql/)
    - [sys. DM _exec_trigger_stats](/sql/relational-databases/system-dynamic-management-views/sys-dm-exec-trigger-stats-transact-sql/)
- Do následujících rozšířených událostí se přidají čtení stránkového serveru:
    - sql_statement_completed
    - sp_statement_completed
    - sql_batch_completed
    - rpc_completed
    - scan_stopped
    - query_store_begin_persist_runtime_stat
    - dotaz – store_execution_runtime_info
- ActualPageServerReads/ActualPageServerReadAheads se přidají do XML plánu dotazů pro skutečné plány. Například:

`<RunTimeCountersPerThread Thread="8" ActualRows="90466461" ActualRowsRead="90466461" Batches="0" ActualEndOfScans="1" ActualExecutions="1" ActualExecutionMode="Row" ActualElapsedms="133645" ActualCPUms="85105" ActualScans="1" ActualLogicalReads="6032256" ActualPhysicalReads="0" ActualPageServerReads="0" ActualReadAheads="6027814" ActualPageServerReadAheads="5687297" ActualLobLogicalReads="0" ActualLobPhysicalReads="0" ActualLobPageServerReads="0" ActualLobReadAheads="0" ActualLobPageServerReadAheads="0" />`

> [!NOTE]
> Chcete-li tyto atributy zobrazit v okně vlastnosti plánu dotazu v SSMS, budete potřebovat SSMS 18,3 nebo novější.


## <a name="virtual-file-stats-and-io-accounting"></a>Statistika virtuálních souborů a monitorování vstupně-výstupních operací

V Azure SQL Database je [Sys. DM _io_virtual_file_stats ()](/sql/relational-databases/system-dynamic-management-views/sys-dm-io-virtual-file-stats-transact-sql/) DMF primárním způsobem, jak monitorovat SQL Server v/v. Vlastnosti v/v na měřítku se liší v důsledku [distribuované architektury](sql-database-service-tier-hyperscale.md#distributed-functions-architecture). V této části se zaměřujeme na vstupně-výstupní operace (čtení a zápisy) do datových souborů, jak je vidět v tomto DMF. V rámci škálování každý datový soubor, který je viditelný v tomto DMF, odpovídá serveru vzdálené stránky. Mezipaměť RBPEX, kterou tady uvádíme, je místní mezipaměť založená na SSD, která je v replikě COMPUTE nepokrývá mezipaměť.


### <a name="local-rbpex-cache-usage"></a>Použití místní mezipaměti RBPEX

Místní mezipaměť RBPEX existuje ve výpočetním uzlu v místním úložišti SSD. V této mezipaměti RBPEX je tedy v této mezipaměti rychlejší než v/v na vzdálených serverech. V současné době má [Sys. DM _io_virtual_file_stats ()](/sql/relational-databases/system-dynamic-management-views/sys-dm-io-virtual-file-stats-transact-sql/) v databázi s velkým škálováním speciální řádek, ve kterém se hlásí v/v místní mezipaměť RBPEX na výpočetní replice. Tento řádek má hodnotu 0 pro sloupec `database_id` i `file_id`. Například následující dotaz vrátí statistiku využití RBPEX od spuštění databáze.

`select * from sys.dm_io_virtual_file_stats(0,NULL);`

Poměr operací čtení provedených v RBPEX a agregovaných čtení provedených u všech ostatních datových souborů poskytuje poměr přístupů do mezipaměti RBPEX.


### <a name="data-reads"></a>Čtení dat

- Pokud jsou čtení vydávány modulem SQL Server na výpočetní replice, mohou být obsluhovány buď místní mezipamětí RBPEX, nebo pomocí vzdálené stránky, nebo kombinací dvou, pokud se čte více stránek.
- Když výpočetní replika přečte některé stránky z konkrétního souboru, například file_id 1, pokud se tato data nacházejí výhradně v místní mezipaměti RBPEX, všechny vstupně-výstupní operace pro toto čtení se týkají file_id 0 (RBPEX). Pokud jsou některá část těchto dat v místní mezipaměti RBPEX a některá část je na vzdáleném serveru stránky, v/v je k disfile_id 0 pro součást obsluhované z RBPEX a část obsluhovaná ze serveru vzdálené stránky je poskytována k hodnotě file_id 1. 
- Když výpočetní replika požádá o stránku na konkrétní hodnotu [LSN](/sql/relational-databases/sql-server-transaction-log-architecture-and-management-guide/) ze stránky serveru, pokud server stránky nezachytil požadavek na hodnotu LSN, přečtení na výpočetní replice počká, dokud se server stránky nepřed vrátí do výpočetní repliky. Pro všechny čtení ze stránky serveru na výpočetní replice se zobrazí typ čekání PAGEIOLATCH_ *, pokud čeká na tuto vstupně-výstupní operaci. Tato doba čekání zahrnuje dobu potřebnou k zaznamenání požadované stránky na straně serveru na požadovanou hodnotu LSN a dobu potřebnou k přenosu stránky ze serveru stránky do repliky služby Compute.
- Velké čtení, jako je čtení předem, se často provádí pomocí [čtení "bodového shromažďování"](/sql/relational-databases/reading-pages/). To umožňuje čtení až 4 MB stránek najednou, považuje se za jeden načtený v modulu SQL Server. Pokud jsou však čtena data v RBPEX, jsou tyto čtení účtovány jako více než jeden jednotlivý čtení 8 KB od fondu vyrovnávacích pamětí a RBPEX vždy používá stránky 8 KB. V důsledku toho může být počet čtení v IOs, který se zobrazuje u RBPEX, větší než skutečný počet IOs, který modul provedl.


### <a name="data-writes"></a>Zápisy dat

- Primární replika COMPUTE neprovádí zápis přímo na stránky serverů. Místo toho se záznamy protokolu z protokolovací služby přehrávají na odpovídajících stránkách. 
- Zápisy, ke kterým dochází ve výpočetní replice, se převážně zapisují do místního RBPEX (file_id 0). Pro zápisy na logických souborech, které jsou větší než 8 KB, tj. ty, které se prováděly pomocí funkce [shromáždit-zápis](/sql/relational-databases/writing-pages/), se každá operace zápisu převede na více než 8 KB jednotlivých zápisů do RBPEX, protože fond vyrovnávací paměti a RBPEX vždycky používají stránky 8 KB. V důsledku toho může být počet IOs pro zápis, který se zobrazuje u RBPEX, větší než skutečný počet IOs provedený modulem.
- Soubory jiné než RBPEX nebo datové soubory jiné než file_id 0, které odpovídají stránkovým serverům, zobrazují také zápisy. Ve vrstvě služeb s škálovatelným škálováním se tyto zápisy simulují, protože výpočetní repliky Nikdy nezapisovat přímo na stránky serverů. Zápisu vstupně-výstupních operací a propustnosti se účtují, když se vyskytují na výpočetní replice, ale latence u datových souborů jiných než file_id 0 neodráží skutečnou latenci zápisů na straně serveru.

### <a name="log-writes"></a>Zápisy do protokolu

- Na primární výpočetní prostředky se za zápis protokolu používá file_id 2 v sys. DM _io_virtual_file_stats. Zápis do protokolu primárního COMPUTE je zápis do zóny pro odpočívadlo protokolu.
- Záznamy protokolu nejsou u sekundární repliky na potvrzení zabezpečení posíleny. V škálování je protokol aplikován službou xlog na vzdálené repliky. Vzhledem k tomu, že zápisy protokolů se ve skutečnosti nevyskytují na sekundárních replikách, je jakékoli monitorování v/v protokolu na sekundárních replikách pouze pro účely sledování.

## <a name="additional-resources"></a>Další materiály

- Pro omezení prostředků vCore pro izolovanou databázi s jedním škálováním najdete v tématu [limity Vcore úrovně služby škálování na úrovni služeb](sql-database-vcore-resource-limits-single-databases.md#hyperscale---provisioned-compute---gen5) .
- Azure SQL Database ladění výkonu najdete v tématu [výkon dotazů v Azure SQL Database](sql-database-performance-guidance.md)
- Ladění výkonu pomocí úložiště dotazů najdete v tématu [monitorování výkonu pomocí úložiště dotazů](/sql/relational-databases/performance/monitoring-performance-by-using-the-query-store/) .
- DMV pro sledování skriptů najdete v tématu [monitorování výkonu Azure SQL Database pomocí zobrazení dynamické správy](sql-database-monitoring-with-dmvs.md) .
