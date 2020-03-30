---
title: Omezení kapacity – Azure Synapse Analytics (dříve SQL DW)
description: Maximální povolené hodnoty pro různé součásti SQL Analytics v Azure Synapse.
services: synapse-analytics
author: mlee3gsd
manager: craigg
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: ''
ms.date: 2/19/2020
ms.author: martinle
ms.reviewer: igorstan
ms.custom: azure-synapse
ms.openlocfilehash: a0343dd55149fa3c2c17af1f246595a56e4c6ec2
ms.sourcegitcommit: 8a9c54c82ab8f922be54fb2fcfd880815f25de77
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "80350083"
---
# <a name="azure-synapse-analytics-formerly-sql-dw-capacity-limits"></a>Omezení kapacity Azure Synapse Analytics (dříve SQL DW)

Maximální povolené hodnoty pro různé součásti Azure Synapse.

## <a name="workload-management"></a>Správa úloh

| Kategorie | Popis | Maximum |
|:--- |:--- |:--- |
| [Jednotky datového skladu (DWU)](what-is-a-data-warehouse-unit-dwu-cdwu.md) |Maximální DWU pro jednu jednotku fondu SQL (datový sklad) | Gen1: DW6000<br></br>Gen2: DW30000c |
| [Jednotky datového skladu (DWU)](what-is-a-data-warehouse-unit-dwu-cdwu.md) |Výchozí DTU na server |54,000<br></br>Ve výchozím nastavení má každý server SQL (například myserver.database.windows.net) kvótu DTU 54 000, což umožňuje až DW5000c. Tato kvóta je jednoduše bezpečnostní omezení. Kvótu můžete zvýšit [vytvořením lístku podpory](sql-data-warehouse-get-started-create-support-ticket.md) a výběrem *kvóty* jako typu požadavku.  Chcete-li vypočítat potřeby DTU, vynásobte 7,5 celkovým potřebným DWU nebo vynásobte 9,5 celkovým potřebným cDWU. Například:<br></br>DW6000 x 7,5 = 45 000 DTU<br></br>DW5000c x 9,5 = 47 500 DTU.<br></br>Aktuální spotřebu DTU můžete zobrazit z možnosti serveru SQL na portálu. Pozastavené i nepozastavené databáze se započítávají do kvóty DTU. |
| Připojení k databázi |Maximální počet souběžných otevřených relací |1024<br/><br/>Počet souběžných otevřených relací se bude lišit v závislosti na vybraném dwu. DWU600c a vyšší podporují maximálně 1024 otevřených relací. DWU500c a nižší podporují maximální limit souběžné otevřené relace 512. Všimněte si, že existují omezení počtu dotazů, které lze spustit souběžně. Při překročení limitu souběžnosti požadavek přejde do vnitřní fronty, kde čeká na zpracování. |
| Připojení k databázi |Maximální paměť pro připravené příkazy |20 MB |
| [Správa úloh](resource-classes-for-workload-management.md) |Maximální počet souběžných dotazů |128<br/><br/>  Spustí se maximálně 128 souběžných dotazů a zbývající dotazy budou zařazeny do fronty.<br/><br/>Počet souběžných dotazů může snížit, když jsou uživatelé přiřazeni k vyšším třídám prostředků nebo když je sníženo nastavení [jednotky datového skladu.](memory-concurrency-limits.md) Některé dotazy, jako jsou dotazy DMV, jsou vždy povoleny ke spuštění a nemají vliv na limit souběžných dotazů. Další podrobnosti o souběžném provádění dotazu naleznete v článku [maximální souběžnosti.](memory-concurrency-limits.md) |
| [Tempdb](sql-data-warehouse-tables-temporary.md) |Maximální GB |399 GB za DW100c. Proto na DWU1000c, tempdb je dimenzován na 3,99 TB. |
||||

## <a name="database-objects"></a>Databázové objekty

| Kategorie | Popis | Maximum |
|:--- |:--- |:--- |
| Databáze |Maximální velikost | Gen1: 240 TB komprimované na disku. Tento prostor je nezávislý na tempdb nebo protokolu prostoru, a proto je tento prostor vyhrazen pro trvalé tabulky.  Kompresi clusterovaného columnstore se odhaduje na 5X.  Tato komprese umožňuje databázi zvětšit na přibližně 1 PB, pokud jsou všechny tabulky seskupené columnstore (výchozí typ tabulky). <br/><br/> Gen2: Neomezené úložiště pro tabulky columnstore.  Rowstore část databáze je stále omezena na 240 TB komprimované na disku. |
| Table |Maximální velikost |Neomezená velikost pro tabulky columnstore. <br>60 TB pro tabulky rowstore komprimované na disku. |
| Table |Tabulky na databázi | 100 000 |
| Table |Sloupce v tabulce |1024 sloupců |
| Table |Bajty na sloupec |V závislosti na [datovém typu](sql-data-warehouse-tables-data-types.md)sloupce . U datových typů znaků může max limit uložit až 2 GB do úložiště mimo stránku (přetečení řádků).  Non-Unicode znaky, jako je char nebo varchar limit je 8000 v datové stránce, Unicode znaky, jako je nchar nebo nvarchar limit je 4000 v datové stránce.  Ke zvýšení výkonu použijte velikost úložiště datové stránky. |
| Table |Počet bajtů na řádek, definovaná velikost |8060 bajtů<br/><br/>Počet bajtů na řádek se vypočítá stejným způsobem jako pro SQL Server s kompresí stránky. Podobně jako SQL Server je podporováno úložiště přetečení řádků, které umožňuje **sloupce proměnné délky,** které mají být posunuty mimo řádek. Pokud jsou řádky proměnné délky posunuty mimo řádek, je v hlavním záznamu uložen pouze 24bajtový kořen. Další informace naleznete v [tématu Přetečení řádků data přesahující 8-KB](https://msdn.microsoft.com/library/ms186981.aspx). |
| Table |Oddíly na tabulku |15 000<br/><br/>Pro vysoký výkon doporučujeme minimalizovat počet oddílů, které potřebujete, a zároveň podporovat vaše obchodní požadavky. S růstem počtu oddílů se zvyšuje režie pro operace jazyka definice dat (DDL) a jazyka dml (Data Manipulation Language) a způsobuje pomalejší výkon. |
| Table |Znaky na hodnotu hranice oddílu. |4000 |
| Index |Indexy bez seskupení pro tabulku. |50<br/><br/>Platí pouze pro tabulky obchodů řádků. |
| Index |Seskupené indexy podle tabulky. |1<br><br/>Platí pro tabulky rowstore i columnstore. |
| Index |Velikost klíče indexu. |900 bajtů.<br/><br/>Platí pouze pro indexy rowstore.<br/><br/>Indexy na varchar sloupce s maximální velikostí více než 900 bajtů lze vytvořit, pokud existující data ve sloupcích nepřesahuje 900 bajtů při vytvoření indexu. Pozdější akce INSERT nebo UPDATE ve sloupcích, které způsobí, že celková velikost překročí 900 bajtů, se však nezdaří. |
| Index |Klíčové sloupce na index. |16<br/><br/>Platí pouze pro indexy rowstore. Clustered columnstore indexy zahrnují všechny sloupce. |
| Statistika |Velikost kombinovaných hodnot sloupců. |900 bajtů. |
| Statistika |Sloupce na objekt statistiky. |32 |
| Statistika |Statistiky vytvořené ve sloupcích podle tabulky. |30,000 |
| Uložené procedury |Maximální úrovně vnoření. |8 |
| Zobrazení |Sloupce v zobrazení |1,024 |
||||

## <a name="loads"></a>Načte

| Kategorie | Popis | Maximum |
|:--- |:--- |:--- |
| Polybase zatížení |MB na řádek |1<br/><br/>Polybase načte řádky, které jsou menší než 1 MB. Načítání obchodních datových typů do tabulek s indexem clusterovaného úložiště sloupců (CCI) není podporováno.<br/><br/> |
||||

## <a name="queries"></a>Dotazy

| Kategorie | Popis | Maximum |
|:--- |:--- |:--- |
| Dotaz |Dotazy zařazené do fronty na uživatelské tabulky. |1000 |
| Dotaz |Souběžné dotazy na zobrazení systému. |100 |
| Dotaz |Dotazy ve frontě na systémová zobrazení |1000 |
| Dotaz |Maximální parametry |2098 |
| Batch |Maximální velikost |65,536*4096 |
| Výsledky SELECT |Sloupce na řádek |4 096<br/><br/>Ve výsledku SELECT nelze mít nikdy více než 4096 sloupců na řádek. Neexistuje žádná záruka, že můžete mít vždy 4096. Pokud plán dotazů vyžaduje dočasnou tabulku, může se použít maximální počet sloupců 1024 na tabulku. |
| SELECT |Vnořené poddotazy |32<br/><br/>Nikdy nemůžete mít více než 32 vnořených poddotazů v příkazu SELECT. Neexistuje žádná záruka, že můžete mít vždy 32. Například JOIN můžete zavést poddotaz do plánu dotazu. Počet poddotazů může být také omezen dostupnou pamětí. |
| SELECT |Sloupce na spojení |1024 sloupců<br/><br/>Nikdy nemůžete mít více než 1024 sloupců v JOIN. Neexistuje žádná záruka, že můžete mít vždy 1024. Pokud plán JOIN vyžaduje dočasnou tabulku s více sloupci než výsledek JOIN, vztahuje se na dočasnou tabulku limit 1024. |
| SELECT |Bajtů podle sloupců SKUPINY PODLE. |8060<br/><br/>Sloupce v klauzuli GROUP BY mohou mít maximálně 8060 bajtů. |
| SELECT |Bajty na sloupce POŘADÍ PODLE |8060 bajtů<br/><br/>Sloupce v klauzuli ORDER BY mohou mít maximálně 8060 bajtů. |
| Identifikátory podle výpisu |Počet odkazovaných identifikátorů |65,535<br/><br/> Počet identifikátorů, které mohou být obsaženy v jednom výrazu dotazu je omezen. Překročení tohoto čísla má za následek chybu serveru SQL Server 8632. Další informace naleznete [v tématu Vnitřní chyba: Bylo dosaženo limitu služby výrazu](https://support.microsoft.com/help/913050/error-message-when-you-run-a-query-in-sql-server-2005-internal-error-a). |
| Řetězcové literály | Počet řetězcových literál v příkazu | 20 000 <br/><br/>Počet konstant řetězce v jednom výrazu dotazu je omezen. Překročení tohoto čísla má za následek chybu serveru SQL Server 8632.|
||||

## <a name="metadata"></a>Metadata

| Systémové zobrazení | Maximální počet řádků |
|:--- |:--- |
| sys.dm_pdw_component_health_alerts |10 000 |
| sys.dm_pdw_dms_cores |100 |
| sys.dm_pdw_dms_workers |Celkový počet pracovníků DMS pro nejnovější 1000 požadavků SQL. |
| sys.dm_pdw_errors |10 000 |
| sys.dm_pdw_exec_requests |10 000 |
| sys.dm_pdw_exec_sessions |10 000 |
| sys.dm_pdw_request_steps |Celkový počet kroků pro nejnovější 1000 požadavků SQL, které jsou uloženy v sys.dm_pdw_exec_requests. |
| sys.dm_pdw_os_event_logs |10 000 |
| sys.dm_pdw_sql_requests |Nejnovější 1000 požadavků SQL, které jsou uloženy v sys.dm_pdw_exec_requests. |
|||

## <a name="next-steps"></a>Další kroky

Doporučení týkající se používání Azure Synapse najdete v tématu [Cheat Sheet](cheat-sheet.md).
