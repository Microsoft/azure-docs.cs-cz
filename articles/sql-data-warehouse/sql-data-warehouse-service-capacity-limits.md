---
title: Limity kapacity SQL Data Warehouse | Microsoft Docs
description: Maximální hodnoty pro připojení, databáze, tabulky a dotazy pro SQL Data Warehouse.
services: sql-data-warehouse
documentationcenter: NA
author: barbkess
manager: jhubbard
editor: ''
ms.assetid: e1eac122-baee-4200-a2ed-f38bfa0f67ce
ms.service: sql-data-warehouse
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: data-services
ms.custom: reference
ms.date: 03/27/2018
ms.author: kevin;barbkess
ms.openlocfilehash: fa7d8a9880ff97f30dc583d792e39aa914ea5435
ms.sourcegitcommit: d74657d1926467210454f58970c45b2fd3ca088d
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2018
---
# <a name="sql-data-warehouse-capacity-limits"></a>Limity kapacity SQL Data Warehouse
Následující tabulky obsahují maximální hodnoty povolené pro různé komponenty služby Azure SQL Data Warehouse.

## <a name="workload-management"></a>Správa zatížení
| Kategorie | Popis | Maximum |
|:--- |:--- |:--- |
| [Jednotky datového skladu (DWU)][Data Warehouse Units (DWU)] |Maximální počet DWU pro jeden datový sklad SQL | Optimalizovaná pro pružnost [úroveň výkonu](performance-tiers.md): DW6000<br></br>Optimalizovaná pro výpočet [úroveň výkonu](performance-tiers.md): DW30000c |
| [Jednotky datového skladu (DWU)][Data Warehouse Units (DWU)] |Výchozí počet jednotek DTU na server |54,000<br></br>Ve výchozím nastavení má každého serveru SQL server (například myserver.database.windows.net) DTU kvótu 54 000, což umožňuje až DW6000c. Tato kvóta je jednoduše bezpečnostní omezení. Můžete zvýšit kvótu podle [vytvoření lístku podpory] [ creating a support ticket] a výběrem *kvóty* jako typ požadavku.  K výpočtu vaší DTU potřebuje, celkový počet DWU potřeby vynásobit 7.5 nebo 9.0 vynásobit celkový cDWU potřeby. Příklad:<br></br>DW6000 x 7.5 = 45,000 Dtu<br></br>DW600c x 9.0 = 54 000 Dtu.<br></br>Vaše aktuální spotřeba DTU z možnost SQL serveru můžete zobrazit na portálu. Pozastavené i nepozastavené databáze se započítávají do kvóty DTU. |
| Připojení databáze |Souběžné otevřených relací |1024<br/><br/>Všechny aktivní relace 1024 mohou odesílat požadavky na databázi SQL Data Warehouse ve stejnou dobu. Všimněte si, existují omezení počtu dotazy, které mohou být prováděny současně. Při překročení limitu souběžnosti, žádost přejde do vnitřní fronty, kde se čeká na zpracování. |
| Připojení databáze |Maximální velikost paměti pro připravené příkazy |20 MB |
| [Úlohy správy][Workload management] |Maximální počet souběžných dotazů |32<br/><br/> Ve výchozím nastavení můžete spustit SQL Data Warehouse maximálně 32 souběžných dotazů a fronty zbývající dotazy.<br/><br/>Počet souběžných dotazů můžete descrease uživatelé přiřazení k vyšší třídy prostředku nebo když SQL Data Warehouse má nižší [služby úroveň](performance-tiers.md#service-levels). Některé dotazy, jako jsou dotazy DMV, je vždy povoleno spustit. |
| [tempdb][Tempdb] |Maximální GB |399 GB za od DW100. Proto je v DWU1000, 3,99 TB velikosti databáze tempdb |

## <a name="database-objects"></a>Objekty databáze.
| Kategorie | Popis | Maximum |
|:--- |:--- |:--- |
| Databáze |Maximální velikost |240 TB komprimované na disku<br/><br/>Tento prostor je nezávislý na prostor tempdb nebo protokolu, a proto je tento prostor vyhrazený pro trvalé tabulky.  Komprese Clusterové columnstore se odhaduje na 5 X.  Tato komprese umožňuje databázi do dosáhnout přibližně 1 PB všech tabulek po clusterových columnstore (výchozí typ tabulky). |
| Table |Maximální velikost |60 TB komprimované na disku |
| Table |Tabulky na databázi |10 000 |
| Table |Sloupců v tabulce |1024 sloupců |
| Table |Počet bajtů za sloupce |Závislé na sloupci [datový typ][data type].  Limit je 8000 pro datové typy char, 4000 pro nvarchar, nebo 2 GB pro maximální počet datových typů. |
| Table |Bajtů na řádek, definovaná velikost |8 060 bajtů<br/><br/>Počet bajtů na řádek je vypočítána stejným způsobem, jako je pro SQL Server s kompresí stránky. Podobně jako SQL Server, SQL Data Warehouse podporuje řádek přetečení úložiště, což umožňuje **proměnnou délkou** na poslat mimo řádek. Když proměnlivou délkou řádky odesílají mimo řádek, uloží se pouze 24 bajtů kořenové hlavní záznam. Další informace najdete v tématu [přetečení řádek dat překročení 8 KB][Row-Overflow Data Exceeding 8 KB]. |
| Table |Oddíly na tabulky |15,000<br/><br/>Pro vysoký výkon, doporučujeme minimalizovat počet oddílů musí při zároveň podporovat vaše podnikové požadavky. S růstem počet oddílů režii pro operace jazyka DDL (Data Definition) a manipulace dat jazyk (DML) zvětšování a způsobí snížení výkonu. |
| Table |Znaků na jednu hodnotu hranice oddílu. |4000 |
| Index |Bez Clusterované indexy na jednu tabulku. |50<br/><br/>Platí pro pouze rowstore tabulky. |
| Index |Clusterované indexy na jednu tabulku. |1<br><br/>Platí pro rowstore a columnstore tabulky. |
| Index |Velikost klíče indexu. |900 bajtů.<br/><br/>Platí pro pouze rowstore indexy.<br/><br/>Indexy v varchar sloupce s maximální velikost více než 900 bajtů lze vytvořit, pokud existující data v sloupce, které není delší než 900 bajtů při vytvoření indexu. Však vložit později nebo se nezdaří aktualizace akcí u sloupců, které způsobí celková velikost přesahovat 900 bajtů. |
| Index |Klíčových sloupců na index. |16<br/><br/>Platí pro pouze rowstore indexy. Clusterované indexy columnstore zahrňte všechny sloupce. |
| Statistika |Velikost hodnoty kombinované sloupců. |900 bajtů. |
| Statistika |Sloupce podle statistiku objektu. |32 |
| Statistika |Statistika na sloupců v tabulce vytvoří. |30,000 |
| Uložené procedury |Maximální úrovně vnoření. |8 |
| Zobrazení |Sloupců v zobrazení |1,024 |

## <a name="loads"></a>Načítání
| Kategorie | Popis | Maximum |
|:--- |:--- |:--- |
| Polybase zatížení |MB na řádek |1<br/><br/>Polybase načte jenom na řádky, které jsou menší než 1 MB a nelze načíst VARCHAR(MAX), NVARCHAR(MAX) nebo VARBINARY(MAX).<br/><br/> |

## <a name="queries"></a>Dotazy
| Kategorie | Popis | Maximum |
|:--- |:--- |:--- |
| Dotaz |Ve frontě dotazů v případě uživatelských tabulek. |1000 |
| Dotaz |Počet souběžných dotazů na systémová zobrazení. |100 |
| Dotaz |Ve frontě dotazů na systémová zobrazení |1000 |
| Dotaz |Maximální parametry |2098 |
| Batch |Maximální velikost |65,536*4096 |
| Vyberte výsledky |Sloupců na řádek |4 096<br/><br/>Nikdy můžete mít více než 4096 sloupců na řádek v vyberte výsledek. Není zaručeno, že budete mít vždy 4096. Pokud plán dotazu vyžaduje dočasné tabulky, může použít 1024 sloupců na maximální tabulky. |
| SELECT |Vnořené poddotazy |32<br/><br/>Nikdy můžete mít víc než 32 vnořené poddotazy v příkazu SELECT. Není zaručeno, že budete mít vždy 32. Například spojení můžou představovat poddotazu na plán dotazu. Počet poddotazy může být také omezena dostupné paměti. |
| SELECT |Sloupců na připojení k |1024 sloupců<br/><br/>Nikdy může mít maximálně 1 024 sloupce ve spojení. Není zaručeno, že budete mít vždy 1024. Pokud plán spojení vyžaduje dočasné tabulky s více sloupců než výsledek spojení, platí 1024 limit pro dočasnou tabulku. |
| SELECT |Počet bajtů za sloupce GROUP BY. |8060<br/><br/>Sloupce v klauzuli GROUP BY může mít maximálně 8 060 bajtů. |
| SELECT |Počet bajtů za sloupců ORDER BY |8 060 bajtů<br/><br/>Sloupce v klauzuli ORDER by může mít maximálně 8 060 bajtů |
| Identifikátory na – příkaz |Počet odkazované identifikátorů |65,535<br/><br/>SQL Data Warehouse omezí počet identifikátorů, které může obsahovat jeden výraz dotazu. Překročení výsledkem chyba systému SQL Server 8632 číslo. Další informace najdete v tématu [vnitřní chyba: byl dosažen limit služby výrazu][Internal error: An expression services limit has been reached]. |
| Textové literály | Počet textové literály v příkazu | 20,000 <br/><br/>SQL Data Warehouse limites počet řetězcové konstanty, které může obsahovat jeden výraz dotazu. Překročení výsledkem chyba systému SQL Server 8632 číslo. Další informace najdete v tématu [vnitřní chyba: byl dosažen limit služby výrazu][Internal error: An expression services limit has been reached]. |

## <a name="metadata"></a>Metadata
| Zobrazení systému | Maximální počet řádků |
|:--- |:--- |
| sys.dm_pdw_component_health_alerts |10 000 |
| sys.dm_pdw_dms_cores |100 |
| sys.dm_pdw_dms_workers |Celkový počet pracovních procesů DMS pro poslední 1000 požadavků SQL. |
| sys.dm_pdw_errors |10 000 |
| sys.dm_pdw_exec_requests |10 000 |
| sys.dm_pdw_exec_sessions |10 000 |
| sys.dm_pdw_request_steps |Celkový počet kroků pro poslední žádosti SQL 1000, které jsou uložené v sys.dm_pdw_exec_requests. |
| sys.dm_pdw_os_event_logs |10 000 |
| sys.dm_pdw_sql_requests |Poslední 1 000 SQL požadavků, do které jsou uložené v sys.dm_pdw_exec_requests. |

## <a name="next-steps"></a>Další postup
Další informace najdete v části [Přehled odkaz SQL Data Warehouse][SQL Data Warehouse reference overview].

<!--Image references-->

<!--Article references-->
[Data Warehouse Units (DWU)]: ./sql-data-warehouse-overview-what-is.md
[SQL Data Warehouse reference overview]: ./sql-data-warehouse-overview-reference.md
[Workload management]: ./resource-classes-for-workload-management.md
[Tempdb]: ./sql-data-warehouse-tables-temporary.md
[data type]: ./sql-data-warehouse-tables-data-types.md
[creating a support ticket]: /sql-data-warehouse-get-started-create-support-ticket.md

<!--MSDN references-->
[Row-Overflow Data Exceeding 8 KB]: https://msdn.microsoft.com/library/ms186981.aspx
[Internal error: An expression services limit has been reached]: https://support.microsoft.com/kb/913050
