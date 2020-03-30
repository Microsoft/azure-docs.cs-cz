---
title: Diagnostika výkonu v hyperškálovatnovém měřítku
description: Tento článek popisuje, jak řešit problémy s výkonem hyperškálování v Azure SQL Database.
services: sql-database
ms.service: sql-database
ms.subservice: service
ms.custom: seo-lt-2019
ms.topic: troubleshooting
author: denzilribeiro
ms.author: denzilr
ms.reviewer: sstein
ms.date: 10/18/2019
ms.openlocfilehash: 26bd6ddb9d8255b8e2510133fc4b6aa645f89f68
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "75615069"
---
# <a name="sql-hyperscale-performance-troubleshooting-diagnostics"></a>Diagnostika řešení potíží s výkonem v hyperškálování SQL

Chcete-li řešit problémy s výkonem v databázi Hyperscale, [obecné metodiky optimalizace výkonu](sql-database-monitor-tune-overview.md) na výpočetníuzel databáze Azure SQL je výchozím bodem šetření výkonu. Vzhledem k [distribuované architektuře](sql-database-service-tier-hyperscale.md#distributed-functions-architecture) hyperškálování však byla přidána další diagnostika, která pomáhá. Tento článek popisuje diagnostická data specifická pro hyperškálování.

## <a name="log-rate-throttling-waits"></a>Omezení rychlosti protokolu čeká

Každá úroveň služby Azure SQL Database má omezení rychlosti generování protokolu vynucená prostřednictvím [zásad správného řízení rychlosti protokolu](sql-database-resource-limits-database-server.md#transaction-log-rate-governance). V hyperškálování je aktuálně nastaven limit generování protokolu na 100 MB/s bez ohledu na úroveň služby. Existují však časy, kdy rychlost generování protokolu na primární výpočetní repliky musí být omezena udržovat obnovitelné sla. K tomuto omezení dochází, když [stránkovací server nebo jiné výpočetní repliky](sql-database-service-tier-hyperscale.md#distributed-functions-architecture) je výrazně za použití nové záznamy protokolu ze služby Protokolu.

Následující typy čekání (v [sys.dm_os_wait_stats)](/sql/relational-databases/system-dynamic-management-views/sys-dm-os-wait-stats-transact-sql/)popisují důvody, proč lze rychlost protokolu omezit na primární výpočetní repliku:

|Typ čekání    |Popis                         |
|-------------          |------------------------------------|
|RBIO_RG_STORAGE        | Vyvolá se, když je rychlost generování primárního výpočetního uzlu databáze Hyperscale omezena z důvodu zpožděné spotřeby protokolu na stránkovacím serveru(y).         |
|RBIO_RG_DESTAGE        | Vyvolá se, když je rychlost generování protokolu výpočetního uzlu databáze Hyperscale omezena z důvodu zpožděné spotřeby protokolu úložištěm dlouhodobého protokolu.         |
|RBIO_RG_REPLICA        | Vyvolá se, když je rychlost generování protokolu výpočetního uzlu databáze Hyperscale omezena z důvodu zpožděné spotřeby protokolu čitelnými sekundárními replikami.         |
|RBIO_RG_LOCALDESTAGE   | Vyvolá se, když je rychlost generování protokolu výpočetního uzlu databáze Hyperscale omezena z důvodu zpožděné spotřeby protokolu službou protokolu.         |

## <a name="page-server-reads"></a>Čtení stránkového serveru

Výpočetní repliky nejsou mezipaměti úplnou kopii databáze místně. Data místní výpočetní repliky je uložena ve fondu vyrovnávací paměti (v paměti) a v místní odolné vyrovnávací paměti rozšíření fondu (RBPEX) mezipaměti, která je částečná (non-krycí) mezipaměti datových stránek. Tato místní mezipaměť RBPEX je dimenzována úměrně k velikosti výpočetních prostředků a je třikrát paměti výpočetní vrstvy. RBPEX je podobný fondu vyrovnávací paměti v tom, že má nejčastěji přístupná data. Každý stránkový server má na druhé straně krycí mezipaměť RBPEX pro část databáze, kterou udržuje.
 
Při čtení je vydána na výpočetní repliky, pokud data neexistuje ve fondu vyrovnávací paměti nebo místní mezipaměti RBPEX, je vydána funkce getPage(pageId, LSN) a stránka je načtena z odpovídající stránkový server. Čtení ze stránkových serverů jsou vzdálená čtení a jsou tedy pomalejší než čtení z místního RBPEX. Při řešení problémů s výkonem souvisejícís iO, musíme být schopni říct, kolik VOs byly provedeny prostřednictvím relativně pomalejší vzdálené stránky serveru čte.

Několik dmv a rozšířené události mají sloupce a pole, které určují počet vzdálených čtení ze stránkového serveru, které lze porovnat s celkovým čtením. Úložiště dotazů také zachycuje vzdálené čtení jako součást statistiky času spuštění dotazu.

- Sloupce pro čtení serveru stránky sestavy jsou k dispozici v spuštění dmv a zobrazení katalogu, například:
    - [Sys.dm_exec_requests](/sql/relational-databases/system-dynamic-management-views/sys-dm-exec-requests-transact-sql/)
    - [Sys.dm_exec_query_stats](/sql/relational-databases/system-dynamic-management-views/sys-dm-exec-query-stats-transact-sql/)
    - [sys.dm_exec_procedure_stats](/sql/relational-databases/system-dynamic-management-views/sys-dm-exec-procedure-stats-transact-sql/)
    - [sys.dm_exec_trigger_stats](/sql/relational-databases/system-dynamic-management-views/sys-dm-exec-trigger-stats-transact-sql/)
    - [sys.query_store_runtime_stats](/sql/relational-databases/system-catalog-views/sys-query-store-runtime-stats-transact-sql/)
- Čtení stránkového serveru jsou přidána k následujícím rozšířeným událostem:
    - sql_statement_completed
    - sp_statement_completed
    - sql_batch_completed
    - rpc_completed
    - scan_stopped
    - query_store_begin_persist_runtime_stat
    - store_execution_runtime_info dotazu
- ActualPageServerReads/ActualPageServerReadAheads jsou přidány do xml plánu dotazů pro skutečné plány. Například:

`<RunTimeCountersPerThread Thread="8" ActualRows="90466461" ActualRowsRead="90466461" Batches="0" ActualEndOfScans="1" ActualExecutions="1" ActualExecutionMode="Row" ActualElapsedms="133645" ActualCPUms="85105" ActualScans="1" ActualLogicalReads="6032256" ActualPhysicalReads="0" ActualPageServerReads="0" ActualReadAheads="6027814" ActualPageServerReadAheads="5687297" ActualLobLogicalReads="0" ActualLobPhysicalReads="0" ActualLobPageServerReads="0" ActualLobReadAheads="0" ActualLobPageServerReadAheads="0" />`

> [!NOTE]
> Chcete-li zobrazit tyto atributy v okně vlastností plánu dotazu, je vyžadována hodnota SSMS 18.3 nebo novější.

## <a name="virtual-file-stats-and-io-accounting"></a>Statistiky virtuálních souborů a účtování vi

V Azure SQL Database je DMF [sys.dm_io_virtual_file_stats()](/sql/relational-databases/system-dynamic-management-views/sys-dm-io-virtual-file-stats-transact-sql/) primárním způsobem monitorování vi. Vlastnosti vstupně-upv v hyperměřítku se liší díky jeho [distribuované architektuře](sql-database-service-tier-hyperscale.md#distributed-functions-architecture). V této části se zaměřujeme na IO (čte a zapisuje) do datových souborů, jak je vidět v tomto DMF. V hyperscale každý datový soubor viditelný v tomto DMF odpovídá vzdálenému stránkovacímu serveru. Zde uvedená mezipaměť RBPEX je místní mezipaměť založená na SSD, která je nezakrývanou mezipamětí na výpočetní replice.

### <a name="local-rbpex-cache-usage"></a>Místní využití mezipaměti RBPEX

Místní mezipaměť RBPEX existuje v výpočetní replice v místním úložišti SSD. Proto iO proti této mezipaměti je rychlejší než IO proti vzdálené stránky serverů. V současné době [sys.dm_io_virtual_file_stats()](/sql/relational-databases/system-dynamic-management-views/sys-dm-io-virtual-file-stats-transact-sql/) v databázi Hyperscale má speciální řádek hlášení vstupně-up proti místní mezipaměti RBPEX na výpočetní repliky. Tento řádek má hodnotu `database_id` 0 `file_id` pro sloupce a sloupce. Například níže uvedený dotaz vrátí statistiky využití RBPEX od spuštění databáze.

`select * from sys.dm_io_virtual_file_stats(0,NULL);`

Poměr čtení provedených na RBPEX a agregovaných čtení provedených na všech ostatních datových souborech poskytuje poměr přístupů do mezipaměti RBPEX.

### <a name="data-reads"></a>Čtení dat

- Při čtení jsou vydány sql server motoru na výpočetní repliky, mohou být obsluhovány buď místní mezipaměti RBPEX nebo vzdálené stránkovací servery nebo kombinací dvou při čtení více stránek.
- Když výpočetní replika přečte některé stránky z určitého souboru, například file_id 1, pokud tato data jsou umístěna výhradně v místní mezipaměti RBPEX, všechny vstupně-vím než toto čtení je započítána proti file_id 0 (RBPEX). Pokud je některá část těchto dat v místní mezipaměti RBPEX a část je na vzdáleném stránkovacím serveru, pak je VO zaúčtována k file_id 0 pro část obsluhovanou z RBPEX a část obsluhovaná ze vzdáleného stránkovacího serveru je započítána k file_id 1. 
- Když počítačová replika požaduje stránku na určitém [lsn](/sql/relational-databases/sql-server-transaction-log-architecture-and-management-guide/) ze stránkového serveru, pokud stránkový server nedohonil požadovanou službu LSN, čtení na výpočetní replice počká, dokud stránkový server nezachytí stránku, než se stránka vrátí do výpočetní repliky. Pro všechny čtení ze stránkovacího serveru na výpočetní repliky, uvidíte typ čekání PAGEIOLATCH_ *, pokud čeká na tento vi. V hyperscale tato čekací doba zahrnuje jak čas dohnat požadovanou stránku na stránkovém serveru na lsn požadované a čas potřebný k přenosu stránky ze stránkovacího serveru do výpočetní repliky.
- Velké čtení, jako je například čtení dopředu se často provádí pomocí ["Scatter-Gather" čtení](/sql/relational-databases/reading-pages/). To umožňuje čtení až 4 MB stránek najednou, za jeden čtení v modulu SQL Server. Však při čtení dat je v RBPEX, tyto čtení jsou zaúčtovány jako více jednotlivých 8 KB čtení, protože fond vyrovnávací paměti a RBPEX vždy používat 8 kb stránky. V důsledku toho může být počet čtených iOs vidět proti RBPEX může být větší než skutečný počet IOs provádí motor.

### <a name="data-writes"></a>Zápisy dat

- Primární výpočetní replika nezapisuje přímo na stránkové servery. Místo toho protokol záznamy ze služby Protokolu jsou přehrány na odpovídající stránkové servery. 
- Zápisy, které se dějí na výpočetní repliky jsou převážně zápisy do místní RBPEX (file_id 0). Pro zápisy na logické soubory, které jsou větší než 8 KB, jinými slovy ty, které se provádí pomocí [Gather-write](/sql/relational-databases/writing-pages/), každá operace zápisu je přeložen do více 8 KB jednotlivé zápisy do RBPEX, protože fond vyrovnávací paměti a RBPEX vždy používat 8 kB stránky. V důsledku toho může být počet iO s zápisem, který je pozorován proti RBPEX, větší než skutečný počet i0s prováděný motorem.
- Non-RBPEX soubory, nebo datové soubory jiné než file_id 0, které odpovídají stránkovací servery, také zobrazit zápisy. Ve vrstvě služby Hyperscale jsou tyto zápisy simulovány, protože výpočetní repliky nikdy nezapisují přímo na stránkové servery. Zápis VOPS a propustnost jsou zaúčtovány, jakmile k nim dojde v výpočetní replice, ale latence pro datové soubory než file_id 0 neodráží skutečnou latenci zápisů stránkovacího serveru.

### <a name="log-writes"></a>Protokol zápisy

- Na primární výpočetní, zápis protokolu se účtuje v file_id 2 sys.dm_io_virtual_file_stats. Zápis protokolu na primární výpočetní je zápis do zóny přihlášení protokolu.
- Záznamy protokolu nejsou posíleny na sekundární repliku v potvrzení. V hyperškálování je protokol použit službou Log na sekundární repliky asynchronně. Vzhledem k tomu, že zápisy protokolu ve skutečnosti nedochází na sekundární repliky, jakékoli účtování Vo protokolu na sekundární repliky je pouze pro účely sledování.

## <a name="data-io-in-resource-utilization-statistics"></a>Vodicí úzravci dat ve statistice využití prostředků

V databázi bez stupně Hyperscale jsou v [zobrazeních sys.dm_db_resource_stats](/sql/relational-databases/system-dynamic-management-views/sys-dm-db-resource-stats-azure-sql-database) a [sys.resource_stats](/sql/relational-databases/system-catalog-views/sys-resource-stats-azure-sql-database) ve sloupci uvedeny kombinované čtení a zápis `avg_data_io_percent` vstupně-diagramů proti datovým souborům vzhledem k limitu vstupně-veřejných prostředků pro [vstupně-line.](/azure/sql-database/sql-database-resource-limits-database-server#resource-governance) Stejná hodnota je hlášena na portálu jako _procento vio dat_. 

V databázi Hyperscale tento sloupec hlásí využití datových vstupně-upů vzhledem k limitu pro místní `tempdb`úložiště pouze na výpočetní repliky, konkrétně IO proti RBPEX a . Hodnota 100 % v tomto sloupci označuje, že zásadsprávné řízení prostředků omezuje viposlužby místního úložiště. Pokud to souvisí s problémem s výkonem, vylaďte zatížení generovat méně vi nebo zvýšit cíl databázové služby zvýšit [omezení](sql-database-vcore-resource-limits-single-databases.md)řízení prostředků _Max data VOPS_ . Pro řízení prostředků RBPEX čtení a zápisy, systém počítá jednotlivé 8 KB IOs, spíše než větší vstupně-maily, které mohou být vydány sql server motoru. 

Vavicí úsečné položky proti vzdáleným stránkovacím serverům nejsou vykazovány v zobrazení využití prostředků ani na portálu, ale jsou uvedeny v [souboru Sys.dm_io_virtual_file_stats()](/sql/relational-databases/system-dynamic-management-views/sys-dm-io-virtual-file-stats-transact-sql/) DMF, jak již bylo uvedeno dříve.


## <a name="additional-resources"></a>Další zdroje

- Omezení prostředků virtuálních jader pro jednu databázi hyperškálování naleznete v tématu [Limity virtuálních jader úrovně služby Hyperscale](sql-database-vcore-resource-limits-single-databases.md#hyperscale---provisioned-compute---gen5)
- Optimalizace výkonu Azure SQL Database najdete [v tématu Výkon dotazu v Azure SQL Database](sql-database-performance-guidance.md)
- Optimalizace výkonu pomocí úložiště dotazů najdete v [tématu Sledování výkonu pomocí úložiště dotazů](/sql/relational-databases/performance/monitoring-performance-by-using-the-query-store/)
- Skripty monitorování DMV najdete [v tématu Sledování výkonu Azure SQL Database pomocí zobrazení dynamické správy](sql-database-monitoring-with-dmvs.md)
