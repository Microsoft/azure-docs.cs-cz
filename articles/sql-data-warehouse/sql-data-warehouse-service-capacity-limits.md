---
title: Omezení kapacity – Azure SQL Data Warehouse | Dokumentace Microsoftu
description: Maximální hodnoty povolené pro různé komponenty služby Azure SQL Data Warehouse.
services: sql-data-warehouse
author: sachinpMSFT
manager: craigg
ms.service: sql-data-warehouse
ms.topic: conceptual
ms.component: implement
ms.date: 11/14/2018
ms.author: anvang
ms.reviewer: igorstan
ms.openlocfilehash: aa1d98f5ea2db0cc549b60e33769c8628181721b
ms.sourcegitcommit: db2cb1c4add355074c384f403c8d9fcd03d12b0c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/15/2018
ms.locfileid: "51686598"
---
# <a name="sql-data-warehouse-capacity-limits"></a>Limity kapacity SQL Data Warehouse
Maximální hodnoty povolené pro různé komponenty služby Azure SQL Data Warehouse.

## <a name="workload-management"></a>Správa úloh
| Kategorie | Popis | Maximum |
|:--- |:--- |:--- |
| [Jednotky datového skladu (DWU)](what-is-a-data-warehouse-unit-dwu-cdwu.md) |Maximální počet DWU pro jeden SQL Data Warehouse | Gen1: DW6000<br></br>Gen2: DW30000c |
| [Jednotky datového skladu (DWU)](what-is-a-data-warehouse-unit-dwu-cdwu.md) |Výchozí jednotek DTU na server |54,000<br></br>Každý server SQL (např. myserver.database.windows.net) má ve výchozím nastavení kvóty DTU o 54 000, což umožňuje až DW6000c. Tato kvóta je jednoduše bezpečnostní omezení. Můžete zvýšit kvótu podle [vytvoření lístku podpory](sql-data-warehouse-get-started-create-support-ticket.md) a vyberete *kvóty* jako typu požadavku.  K výpočtu vaší DTU potřebuje, vynásobte celkovou nutná DWU 7.5 nebo 9.0 vynásobte celkové cDWU potřeby. Příklad:<br></br>DW6000 x 7.5 = hodnotě 45 000 Dtu<br></br>DW6000c x 54 9.0 = 000 Dtu.<br></br>Vaši aktuální spotřebu DTU z možnosti SQL serveru můžete zobrazit na portálu. Pozastavené i nepozastavené databáze se započítávají do kvóty DTU. |
| Připojení databáze |Otevřít souběžných relací |1024<br/><br/>Všechny aktivní relace 1024 můžete poslat požadavek na databázi SQL Data Warehouse ve stejnou dobu. Mějte na paměti, existují omezení počtu dotazů, které mohou být prováděna současně. Při překročení limitu souběžnosti, požadavek přejde do vnitřní fronty, kde čeká na zpracování. |
| Připojení databáze |Maximální velikost paměti pro připravené příkazy |20 MB |
| [Správa úloh](resource-classes-for-workload-management.md) |Maximální počet souběžných dotazů |128<br/><br/> SQL Data Warehouse mohou spouštět maximálně 128 souběžných dotazů a fronty zbývající dotazy.<br/><br/>Počet souběžných dotazů můžete snížit, když uživatelé přiřazeni k vyšší třídy prostředků nebo pokud SQL Data Warehouse má nižší [jednotka datového skladu](memory-and-concurrency-limits.md) nastavení. Některé dotazy, jako jsou dotazy na zobrazení dynamické správy, jsou vždy povoleny ke spuštění a provést negativní vliv na omezení souběžných dotazu. Další podrobnosti o spuštění souběžných dotazů, najdete v článku [souběžnosti maxima](memory-and-concurrency-limits.md#concurrency-maximums) článku. |
| [databáze tempdb](sql-data-warehouse-tables-temporary.md) |Maximální GB |399 GB na DW100. Proto v DWU1000, tempdb přizpůsoben pro 3,99 TB. |

## <a name="database-objects"></a>Databázové objekty
| Kategorie | Popis | Maximum |
|:--- |:--- |:--- |
| Databáze |Maximální velikost | Gen1: 240 TB na disk komprimován. Toto místo je nezávislý na místa protokolu nebo databáze tempdb, a proto je tento prostor vyhrazený pro trvalé tabulky.  Komprese columnstore clusteru se odhadují 5 X.  Tato komprese umožňuje databáze, kterou chcete dosáhnout přibližně 1 PB všech tabulek po Clusterované columnstore (výchozí typ tabulky). <br/><br/> Gen2: 240TB rowstore a neomezené úložiště tabulek columnstore |
| Table |Maximální velikost |60 TB komprimované na disku |
| Table |Tabulky na databázi | 100 000 |
| Table |Sloupce na tabulku |1024 sloupců |
| Table |Bajtů na sloupec |Závislé na sloupci [datový typ](sql-data-warehouse-tables-data-types.md). Limit je 8000 pro datové typy char, 4000 pro nvarchar, nebo 2 GB pro maximální počet datových typů. |
| Table |Řádek, definovaná velikost v bajtech |8 060 bajtů<br/><br/>Počet bajtů na každém řádku se počítá stejným způsobem, jako je komprese stránky pro SQL Server. Jako je SQL Server, SQL Data Warehouse podporuje úložiště řádek přetečení, což umožňuje **sloupců s proměnnou délkou** doručí mimo řádek. Když řádky s proměnnou délkou jsou vložena mimo řádek, uloží se pouze 24 bajtů kořenové hlavního záznamu. Další informace najdete v tématu [přetečení řádek dat překročení velikosti 8 KB](https://msdn.microsoft.com/library/ms186981.aspx). |
| Table |Oddíly na tabulku |15 000<br/><br/>Pro vysoký výkon, doporučujeme minimalizovat počet oddílů musí při zároveň podporovat vaše obchodní požadavky. Roste počet oddílů, nároků na operace jazyka DDL (Data Definition) a manipulace dat jazyka (DML) roste a způsobí pomalejší výkon. |
| Table |Znaky za hodnoty hranice oddílu. |4000 |
| Index |V clusteru bez indexů na tabulku. |50<br/><br/>Platí pro pouze rowstore tabulky. |
| Index |Clusterované indexy na tabulku. |1<br><br/>Platí pro tabulky rowstore a columnstore. |
| Index |Velikost klíče indexu. |900 bajtů.<br/><br/>Platí pro pouze rowstore indexy.<br/><br/>Pokud se stávajícími daty v sloupce, které není delší než 900 bajtů při vytváření indexu, nejde vytvořit indexy pro sloupce varchar a maximální velikosti více než 900 bajtů. Ale později vložit nebo se nezdaří akce aktualizace pro sloupce, které způsobují celková velikost přesahovat 900 bajtů. |
| Index |Klíčové sloupce každý index. |16<br/><br/>Platí pro pouze rowstore indexy. Clusterované indexy columnstore zahrňte všechny sloupce. |
| Statistika |Velikost hodnoty kombinované sloupců. |900 bajtů. |
| Statistika |Sloupce podle objektu statistiky. |32 |
| Statistika |Statistiky pro sloupce na tabulku vytvořit. |30,000 |
| Uložené procedury |Maximální úrovně vnoření. |8 |
| Zobrazení |Sloupce na zobrazení |1,024 |

## <a name="loads"></a>Načtení
| Kategorie | Popis | Maximum |
|:--- |:--- |:--- |
| Načítání Polybase |MB na řádek |1<br/><br/>Polybase načte jenom pro řádky, které jsou menší než 1 MB a nelze načíst do VARCHAR(MAX) či NVARCHAR(MAX) nebo VARBINARY(MAX).<br/><br/> |

## <a name="queries"></a>Dotazy
| Kategorie | Popis | Maximum |
|:--- |:--- |:--- |
| Dotaz |Ve frontě dotazů v případě uživatelských tabulek. |1000 |
| Dotaz |Souběžné dotazy na systémová zobrazení. |100 |
| Dotaz |Ve frontě dotazů na systémová zobrazení |1000 |
| Dotaz |Maximální parametry |2098 |
| Batch |Maximální velikost |65,536*4096 |
| Vyberte výsledky |Sloupce na každém řádku |4 096<br/><br/>Ve výsledcích vyberte nikdy nemůžete mít více než 4096 sloupců na řádek. Neexistuje žádná záruka, že budete mít vždy 4096. Pokud plán dotazu vyžaduje dočasné tabulky, sloupce 1024 jednu tabulku maximální použít. |
| SELECT |Vnořené poddotazů |32<br/><br/>V příkazu SELECT nikdy nemůžete mít více než 32 vnořené poddotazy. Neexistuje žádná záruka, že budete mít vždy 32. Například spojení, můžou představovat poddotaz na plán dotazu. Počet poddotazy může také omezit dostupnou pamětí. |
| SELECT |Sloupce na spojení |1024 sloupců<br/><br/>Nikdy nemůžete mít více než 1024 sloupců ve spojení. Neexistuje žádná záruka, že budete mít vždy 1024. Pokud plán spojení vyžaduje dočasnou tabulku s více sloupců než výsledek spojení, platí limit 1 024 do dočasné tabulky. |
| SELECT |Počet bajtů za Seskupit podle sloupce. |8060<br/><br/>Sloupce v klauzuli GROUP BY může mít maximálně 8 060 bajtů. |
| SELECT |Bajtů na pořadí podle sloupce |8 060 bajtů<br/><br/>Sloupce v klauzuli ORDER by může mít maximálně 8 060 bajtů |
| Identifikátory na – příkaz |Počet identifikátorů odkazované |65,535<br/><br/>SQL Data Warehouse omezuje počet identifikátorů, které mohou být obsaženy v jednom výrazu dotazu. Vyšší než tento počet výsledků v chybě systému SQL Server 8632. Další informace najdete v tématu [vnitřní chyba: bylo dosaženo limitu služby výrazu](https://support.microsoft.com/en-us/help/913050/error-message-when-you-run-a-query-in-sql-server-2005-internal-error-a). |
| Řetězcové literály | Počet řetězcových literálů v příkazu | 20,000 <br/><br/>SQL Data Warehouse omezuje počet řetězcové konstanty v jednom výrazu dotazu. Vyšší než tento počet výsledků v chybě systému SQL Server 8632.|

## <a name="metadata"></a>Metadata
| Systémové zobrazení | Maximální počet řádků |
|:--- |:--- |
| sys.dm_pdw_component_health_alerts |10 000 |
| sys.dm_pdw_dms_cores |100 |
| sys.dm_pdw_dms_workers |Celkový počet pracovních procesů DMS pro nejnovější 1000 požadavků SQL. |
| sys.dm_pdw_errors |10 000 |
| sys.dm_pdw_exec_requests |10 000 |
| sys.dm_pdw_exec_sessions |10 000 |
| sys.dm_pdw_request_steps |Celkový počet kroků pro nejnovější 1000 SQL žádosti, které jsou uloženy v sys.dm_pdw_exec_requests. |
| sys.dm_pdw_os_event_logs |10 000 |
| sys.dm_pdw_sql_requests |Posledních 1000 SQL požadavků, do které jsou uloženy v sys.dm_pdw_exec_requests. |

## <a name="next-steps"></a>Další postup
Doporučení pro použití služby SQL Data Warehouse, najdete v článku [Ošidit list](cheat-sheet.md).
