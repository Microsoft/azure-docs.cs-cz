---
title: Omezení kapacity pro vyhrazený fond SQL
description: Maximální povolené hodnoty pro různé komponenty vyhrazeného fondu SQL ve službě Azure synapse Analytics.
services: synapse-analytics
author: mlee3gsd
manager: craigg
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: sql-dw
ms.date: 2/19/2020
ms.author: martinle
ms.reviewer: igorstan
ms.custom: azure-synapse
ms.openlocfilehash: 7f0eff28533d8cf736d032aff61454a49bcf379e
ms.sourcegitcommit: 6a350f39e2f04500ecb7235f5d88682eb4910ae8
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/01/2020
ms.locfileid: "96449715"
---
# <a name="capacity-limits-for-dedicated-sql-pool-in-azure-synapse-analytics"></a>Omezení kapacity pro vyhrazený fond SQL ve službě Azure synapse Analytics

Maximální povolené hodnoty pro různé komponenty vyhrazeného fondu SQL ve službě Azure synapse Analytics.

## <a name="workload-management"></a>Správa úloh

| Kategorie | Popis | Maximum |
|:--- |:--- |:--- |
| [Jednotky datového skladu (DWU)](what-is-a-data-warehouse-unit-dwu-cdwu.md) |Maximální DWU pro jeden vyhrazený fond SQL  | Gen1: DW6000<br></br>Gen2: DW30000c |
| [Jednotky datového skladu (DWU)](what-is-a-data-warehouse-unit-dwu-cdwu.md) |Výchozí DTU na server |54 000<br></br>Ve výchozím nastavení má každý SQL Server (například myserver.database.windows.net) kvótu DTU 54 000, což umožňuje až DW5000c. Tato kvóta je jednoduše bezpečnostní omezení. Kvótu můžete zvýšit tak, že [vytvoříte lístek podpory](sql-data-warehouse-get-started-create-support-ticket.md) a jako typ žádosti vyberete *kvótu* .  Chcete-li vypočítat potřebné DTU, vynásobte hodnotu 7,5 celkovým DWU, nebo vynásobte 9,5 celkovým počtem potřebných cDWU. Příklad:<br></br>DW6000 x 7,5 = 45 000 DTU<br></br>DW5000c x 9,5 = 47 500 DTU.<br></br>Aktuální spotřebu DTU můžete zobrazit z možnosti SQL serveru na portálu. Pozastavené i nepozastavené databáze se započítávají do kvóty DTU. |
| Připojení k databázi |Maximální počet souběžných otevřených relací |1024<br/><br/>Počet souběžných otevřených relací se bude lišit v závislosti na vybrané DWU. DWU600c a vyšší podporují maximálně 1024 otevřených relací. DWU500c a níže podporují maximální počet souběžných otevřených relací 512. Všimněte si, že počet dotazů, které mohou být spuštěny současně, je omezen. Pokud je překročen limit souběžnosti, požadavek přejde do interní fronty, ve které čeká na zpracování. |
| Připojení k databázi |Maximální velikost paměti pro připravené příkazy |20 MB |
| [Správa úloh](resource-classes-for-workload-management.md) |Maximální počet souběžných dotazů |128<br/><br/>  Vykoná se maximálně 128 souběžných dotazů a zbývající dotazy budou zařazeny do fronty.<br/><br/>Počet souběžných dotazů se může snížit, když jsou uživatelé přiřazeni k vyšším třídám prostředků nebo když je nastavení [jednotky datového skladu](memory-concurrency-limits.md) snížené. Některé dotazy, například dotazy DMV, vždy umožňují spuštění a nemají vliv na souběžný limit dotazu. Další informace o souběžném provádění dotazů naleznete v článku [Maximum](memory-concurrency-limits.md) souběžnosti. |
| [tempdb](sql-data-warehouse-tables-temporary.md) |Maximální GB |399 GB na DW100c. V DWU1000c má databáze tempdb velikost až 3,99 TB. |
||||

## <a name="database-objects"></a>Objekty databáze

| Kategorie | Popis | Maximum |
|:--- |:--- |:--- |
| databáze |Maximální velikost | Gen1: na disku je komprimováno 240 TB. Toto místo je nezávislé na tempdb nebo v prostoru protokolu, a proto je toto místo vyhrazeno pro trvalé tabulky.  Clusterovaná komprese columnstore je odhadnuta na pětinásobné.  Tato komprese umožňuje databázi zvětšit na přibližně 1 PB, pokud jsou všechny tabulky clusterovaný cluster columnstore (výchozí typ tabulky). <br/><br/> Gen2: neomezené úložiště pro tabulky columnstore.  Rowstore část databáze je na disku stále omezená na 240 TB. |
| Table |Maximální velikost |Neomezená velikost tabulek columnstore. <br>60 TB pro tabulky rowstore komprimované na disku. |
| Table |Tabulky na databázi | 100 000 |
| Table |Sloupce na tabulku |sloupce 1024 |
| Table |Počet bajtů na sloupec |Závisí na [datovém typu](sql-data-warehouse-tables-data-types.md)sloupce. Limit je 8000 pro datové typy Char, 4000 pro nvarchar nebo 2 GB pro maximum datových typů. |
| Table |Počet bajtů na řádek, definovaná velikost |8060 bajtů<br/><br/>Počet bajtů na řádek se počítá stejným způsobem jako SQL Server s kompresí stránky. Podobně jako SQL Server podporuje úložiště přetečení řádku, které umožňuje vložit **sloupce proměnné délky** mimo řádek. Pokud jsou řádky proměnlivé délky vloženy mimo řádek, v hlavním záznamu je uložen pouze kořenový adresář 24 bajtů. Další informace najdete v tématu [data přetečení řádků přesahují 8 KB](https://msdn.microsoft.com/library/ms186981.aspx). |
| Table |Počet oddílů na tabulku |15 000<br/><br/>Pro vysoký výkon doporučujeme minimalizovat počet oddílů, které potřebujete, a přitom pořád podporovat vaše podnikové požadavky. Vzhledem k tomu, že počet oddílů roste, režie pro operace DDL (Data Definition Language) a jazyk manipulace s daty (DML) roste a způsobuje pomalejší výkon. |
| Table |Hodnota ohraničení znaků na oddíl. |4000 |
| Index |Neclusterované indexy na tabulku |50<br/><br/>Platí jenom pro tabulky rowstore. |
| Index |Clusterované indexy na tabulku |1<br><br/>Platí pro rowstore i tabulky columnstore. |
| Index |Velikost klíče indexu |900 bajtů.<br/><br/>Platí jenom pro indexy rowstore.<br/><br/>Indexy pro sloupce varchar s maximální velikostí větší než 900 bajtů lze vytvořit, pokud existující data ve sloupcích nepřekračují 900 bajtů při vytvoření indexu. Později ale nebude možné VKLÁDAT nebo aktualizovat akce na sloupcích, které způsobí, že celková velikost překročí 900 bajtů. |
| Index |Klíčové sloupce na index |16<br/><br/>Platí jenom pro indexy rowstore. Clusterované indexy columnstore obsahují všechny sloupce. |
| Statistika |Velikost kombinovaných hodnot sloupců |900 bajtů. |
| Statistika |Sloupce na objekt statistiky |32 |
| Statistika |Statistiky se vytvořily pro sloupce na tabulce. |30 000 |
| Uložené procedury |Maximální úroveň vnoření. |8 |
| Zobrazení |Sloupce na zobrazení |1 024 |
||||

## <a name="loads"></a>Zaveden

| Kategorie | Popis | Maximum |
|:--- |:--- |:--- |
| Základní načtení |MB na řádek |1<br/><br/>Základ databáze načítá řádky, které jsou menší než 1 MB. Načítání datových typů LOB do tabulek s clusterovaným indexem columnstore (Ski) není podporováno.<br/> |
|Základní načtení|Celkový počet souborů|1 000 000<br/><br/>Základní načtení nemůže být větší než 1 milion souborů. Může dojít k následující chybě: **operace se nezdařila, protože počet rozdělení přesahuje horní mez 1000000**.|

## <a name="queries"></a>Dotazy

| Kategorie | Popis | Maximum |
|:--- |:--- |:--- |
| Dotaz |Dotazy zařazené do fronty v uživatelských tabulkách |1000 |
| Dotaz |Souběžné dotazy na systémová zobrazení. |100 |
| Dotaz |Dotazy zařazené do fronty pro systémová zobrazení |1000 |
| Dotaz |Maximální počet parametrů |2098 |
| Batch |Maximální velikost |65536 × 4096 |
| VYBRAT výsledky |Sloupce na řádek |4 096<br/><br/>V výsledku výběru nikdy nemůžete mít v jednom řádku více než 4096 sloupců. Neexistuje žádná záruka, že můžete mít vždycky 4096. Pokud plán dotazu vyžaduje dočasnou tabulku, mohou platit sloupce 1024 na maximum v tabulce. |
| SELECT |Vnořené poddotazy |32<br/><br/>V příkazu SELECT nikdy nemůžete mít více než 32 vnořených poddotazů. Neexistuje žádná záruka, že můžete mít vždycky 32. Například spojení může do plánu dotazu zavést poddotaz. Počet poddotazů může být také omezen pamětí, která je k dispozici. |
| SELECT |Sloupce na spojení |sloupce 1024<br/><br/>Ve spojení nikdy nemůžete mít více než 1024 sloupců. Neexistuje žádná záruka, že můžete mít vždycky 1024. Pokud plán spojení vyžaduje dočasnou tabulku s více sloupci, než je výsledek spojení, vztahuje se limit 1024 na dočasnou tabulku. |
| SELECT |Počet bajtů na skupinu podle sloupců. |8060<br/><br/>Sloupce v klauzuli GROUP BY mohou mít maximálně 8060 bajtů. |
| SELECT |Počet bajtů na objednávku podle sloupců |8060 bajtů<br/><br/>Sloupce v klauzuli ORDER BY mohou mít maximálně 8060 bajtů. |
| Počet identifikátorů na příkaz |Počet odkazovaných identifikátorů |65 535<br/><br/> Počet identifikátorů, které mohou být obsaženy v jednom výrazu dotazu, je omezen. Výsledkem překročení tohoto čísla je SQL Server chyba 8632. Další informace najdete v tématu [vnitřní chyba: bylo dosaženo limitu služeb výrazů](https://support.microsoft.com/help/913050/error-message-when-you-run-a-query-in-sql-server-2005-internal-error-a). |
| Řetězcové literály | Počet řetězcových literálů v příkazu | 20 000 <br/><br/>Počet řetězcových konstant v jednom výrazu dotazu je omezen. Výsledkem překročení tohoto čísla je SQL Server chyba 8632.|
||||

## <a name="metadata"></a>Metadata

| Systémové zobrazení | Maximální počet řádků |
|:--- |:--- |
| sys.dm_pdw_component_health_alerts |10 000 |
| sys.dm_pdw_dms_cores |100 |
| sys.dm_pdw_dms_workers |Celkový počet pracovních procesů DMS pro nejnovější požadavky 1000 SQL. |
| sys.dm_pdw_errors |10 000 |
| sys.dm_pdw_exec_requests |10 000 |
| sys.dm_pdw_exec_sessions |10 000 |
| sys.dm_pdw_request_steps |Celkový počet kroků pro nejnovější požadavky 1000 SQL, které jsou uloženy v sys.dm_pdw_exec_requests. |
| sys.dm_pdw_os_event_logs |10 000 |
| sys.dm_pdw_sql_requests |Nejnovější požadavky 1000 SQL, které jsou uloženy v sys.dm_pdw_exec_requests. |
|||

## <a name="next-steps"></a>Další kroky

Doporučení k používání Azure synapse najdete v [listu tahák](cheat-sheet.md).
