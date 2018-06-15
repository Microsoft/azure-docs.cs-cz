---
title: Všechna témata týkající se služby SQL Data Warehouse | Microsoft Docs
description: Tabulka všechna témata týkající se služby Azure s názvem SQL Data Warehouse, který neexistuje v http://azure.microsoft.com/documentation/articles/, název a popis.
services: sql-data-warehouse
documentationcenter: ''
author: barbkess
manager: jhubbard
editor: ''
ms.assetid: a26a6dec-9c08-4415-8f58-4ee1dd41f718
ms.service: sql-data-warehouse
ms.workload: sql-data-warehouse
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.custom: reference
ms.date: 03/30/2017
ms.author: barbkess
ms.openlocfilehash: 9fe41f12960dc099700e01573b4f03ebf63f8749
ms.sourcegitcommit: 088a8788d69a63a8e1333ad272d4a299cb19316e
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/28/2018
ms.locfileid: "29678424"
---
# <a name="all-topics-for-azure-sql-data-warehouse-service"></a>Všechna témata týkající se služby Azure SQL Data Warehouse
Toto téma obsahuje seznam každých téma, které se týkají přímo **SQL Data Warehouse** služby Azure. Tato webová stránka pro klíčová slova můžete hledat pomocí **Ctrl + F**, vyhledat témata týkající se aktuální.

## <a name="new"></a>Nová
| &nbsp; | Nadpis | Popis |
| ---:|:--- |:--- |
| 1 |[Zálohování SQL Data Warehouse](sql-data-warehouse-backups.md) |Další informace o zálohování integrovanou databází SQL Data Warehouse, které vám umožní obnovit do bodu obnovení nebo jiné zeměpisné oblasti Azure SQL Data Warehouse. |

## <a name="updated-articles-sql-data-warehouse"></a>Aktualizované články, datový sklad SQL
Tato část obsahuje články, které byly aktualizovány nedávno, kde aktualizace je velký nebo důležité. Pro každý aktualizované článek se zobrazí hrubý fragment textu přidané markdownu. Články se aktualizovaly v rámci rozsah kalendářních dat **2016-08-22** k **2016-10-05**.

| &nbsp; | Článek | Aktualizovaný text, fragment kódu | Při aktualizaci |
| ---:|:--- |:--- |:--- |
| 2 |[Načtení dat z Azure blob storage do SQL Data Warehouse (PolyBase)](sql-data-warehouse-load-from-azure-blob-storage-with-polybase.md) |/-Ke sledování souborů a bajtů vyberte r.command, s.request_id, r.status, počet (odlišné input_name) jako nbr_files, sum (s.bytes_processed) / 1024/1024 jako gb_processed z sys.dm_pdw_exec_requests r vnitřní spojení sys.dm_pdw_dms_external_work s na r.request_id = s.request_id WHERE r. Popisek = ' funkce CTAS: cso zatížení. DimProduct ' OR r. Popisek = ' funkce CTAS: cso zatížení. FactOnlineSales  ' GROUP BY  r.command,  s.request_id,  r.status ORDER BY  nbr_files desc,  gb_processed desc; |2016-09-07 |
| 3 |[Obnovení SQL Data Warehouse](sql-data-warehouse-restore-database-overview.md) |** Lze obnovit pozastavenou datového skladu? ** obnovení datového skladu, který je pozastaven, je třeba nejprve převeďte ho zpátky do online režimu. Až datový sklad je zpět do režimu online, budete mít body obnovení pro výběr ze sedmi dnech. ** Obnovit do geograficky redundantní oblast ** Pokud používáte geograficky redundantní úložiště, můžete obnovit datového skladu v jiné zeměpisné oblasti spárované datové centrum. Datový sklad je obnoven ze poslední denní zálohování. ** Obnovte časová osa ** obnovení databáze do libovolného bodu obnovení v posledních sedmi dnů. Snímky spuštění každých čtyř do osmi hodin a jsou k dispozici sedm dní. Pokud snímek je starší než 7 dní, vypršení jeho platnosti a jeho bod obnovení již není k dispozici. ** Obnovte náklady **, které poplatků úložiště pro obnovená data warehouse se fakturuje rychlostí Azure Premium Storage. Pokud pozastavíte obnovené datového skladu, budou se vám účtovat pro úložiště rychlostí Azure Premium Storage. Výhodou pozastavení je, že nejste zdarma |2016-09-29 |

## <a name="get-started"></a>Začínáme
| &nbsp; | Nadpis | Popis |
| ---:|:--- |:--- |
| 4 |[Ověřování do Azure SQL Data Warehouse](sql-data-warehouse-authentication.md) |Azure Active Directory (AAD) a SQL Server ověřování pro Azure SQL Data Warehouse. |
| 5 |[Osvědčené postupy pro službu Azure SQL Data Warehouse](sql-data-warehouse-best-practices.md) |Doporučení a osvědčené postupy, které byste měli znát, když budete vyvíjet řešení pro službu Azure SQL Data Warehouse. Pomohou vám stát se úspěšnými. |
| 6 |[Ovladače pro Azure SQL Data Warehouse](sql-data-warehouse-connection-strings.md) |Připojovací řetězce a ovladače pro datový sklad SQL |
| 7 |[Připojení k Azure SQL Data Warehouse](sql-data-warehouse-connect-overview.md) |Postup vyhledání názvu serveru a připojovacího řetězce pro Azure SQL Data Warehouse |
| 8 |[Analýza dat pomocí Azure Machine Learning](sql-data-warehouse-get-started-analyze-with-azure-machine-learning.md) |Pomocí Azure Machine Learning sestavte prediktivní model Machine Learning založený na datech uložených v datovém skladu SQL Azure. |
| 9 |[Dotaz na Azure SQL Data Warehouse (sqlcmd)](sql-data-warehouse-get-started-connect-sqlcmd.md) |Dotazování SQL Azure Data Warehouse pomocí nástroje příkazového řádku sqlcmd. |
| 10 |[Vytvořit databázi SQL Data Warehouse pomocí Transact-SQL (TSQL)](sql-data-warehouse-get-started-create-database-tsql.md) |Naučte se vytvářet Azure SQL Data Warehouse pomocí TSQL. |
| 11 |[Jak vytvořit lístek podpory pro SQL Data Warehouse](sql-data-warehouse-get-started-create-support-ticket.md) |Jak vytvořit lístek podpory v Azure SQL Data Warehouse |
| 12 |[Načtení dat pomocí Azure Data Factory.](sql-data-warehouse-get-started-load-with-azure-data-factory.md) |Naučte se načítat data pomocí Azure Data Factory. |
| 13 |[Načtení dat pomocí funkce PolyBase v SQL Data Warehouse](sql-data-warehouse-get-started-load-with-polybase.md) |Zjistěte, co je PolyBase a jak tuto funkci používat pro scénáře datových skladů. |
| 14 |[Vytvoření služby Azure SQL Data Warehouse](sql-data-warehouse-get-started-provision.md) |Naučte se vytvářet Azure SQL Data Warehouse na portálu Azure |
| 15 |[Vytvoření SQL Data Warehouse pomocí prostředí PowerShell](sql-data-warehouse-get-started-provision-powershell.md) |Vytvoření SQL Data Warehouse pomocí prostředí PowerShell |
| 16 |[Vizualizace dat pomocí Power BI](sql-data-warehouse-get-started-visualize-with-power-bi.md) |Vizualizace dat SQL Data Warehouse pomocí Power BI |
| 17 |[Dotazování Azure SQL Data Warehouse (Visual Studio)](sql-data-warehouse-query-visual-studio.md) |Dotazování SQL Data Warehouse pomocí sady Visual Studio. |

## <a name="develop"></a>Vývoj
| &nbsp; | Nadpis | Popis |
| ---:|:--- |:--- |
| 18 |[Optimalizace transakcí pro SQL Data Warehouse](sql-data-warehouse-develop-best-practices-transactions.md) |Příručky nejlepších praktik o zápisu efektivní transakce aktualizace v Azure SQL Data Warehouse |
| 19 |[Souběžnost a úlohy správy v SQL Data Warehouse](sql-data-warehouse-develop-concurrency.md) |Porozumět souběžnosti a úlohy správy v Azure SQL Data Warehouse pro vývoj řešení. |
| 20 |[Vytvoření tabulky jako vyberte (funkce CTAS) v SQL Data Warehouse](sql-data-warehouse-develop-ctas.md) |Tipy pro kódování s tabulkou vytvořit jako SELECT (funkce CTAS) v Azure SQL Data Warehouse na vývoj řešení. |
| 21 |[Dynamické SQL v SQL Data Warehouse](sql-data-warehouse-develop-dynamic-sql.md) |Tipy pro používání dynamických SQL v Azure SQL Data Warehouse pro vývoj řešení. |
| 22 |[Seskupit podle možností v SQL Data Warehouse](sql-data-warehouse-develop-group-by-options.md) |Tipy pro implementaci skupiny pomocí možnosti v Azure SQL Data Warehouse na vývoj řešení. |
| 23 |[Použití popisků k nástroji dotazů v SQL Data Warehouse](sql-data-warehouse-develop-label.md) |Tipy pro používání popisky na nástrojích dotazy v Azure SQL Data Warehouse na vývoj řešení. |
| 24 |[Smyčky v SQL Data Warehouse](sql-data-warehouse-develop-loops.md) |Tipy pro smyčky Transact-SQL a nahraďte kurzory v Azure SQL Data Warehouse na vývoj řešení. |
| 25 |[Uložené procedury v SQL Data Warehouse](sql-data-warehouse-develop-stored-procedures.md) |Tipy pro implementaci uložené procedury v Azure SQL Data Warehouse na vývoj řešení. |
| 26 |[Transakce v SQL Data Warehouse](sql-data-warehouse-develop-transactions.md) |Tipy pro provádění transakcí v Azure SQL Data Warehouse na vývoj řešení. |
| 27 |[Uživatelem definované schémata v SQL Data Warehouse](sql-data-warehouse-develop-user-defined-schemas.md) |Tipy pro používání schémata Transact-SQL v Azure SQL Data Warehouse pro vývoj řešení. |
| 28 |[Přiřazení proměnné v SQL Data Warehouse](sql-data-warehouse-develop-variable-assignment.md) |Tipy pro přiřazení proměnné jazyka Transact-SQL v Azure SQL Data Warehouse na vývoj řešení. |
| 29 |[Zobrazení v SQL Data Warehouse](sql-data-warehouse-develop-views.md) |Tipy pro používání zobrazení jazyka Transact-SQL v Azure SQL Data Warehouse na vývoj řešení. |
| 30 |[Rozhodnutí o návrhu a kódování techniky pro SQL Data Warehouse](sql-data-warehouse-overview-develop.md) |Vývoj koncepty, rozhodnutí o návrhu, doporučení a kódování techniky pro SQL Data Warehouse. |

## <a name="manage"></a>Spravovat
| &nbsp; | Nadpis | Popis |
| ---:|:--- |:--- |
| 31 |[Spravovat výpočetní výkon v Azure SQL Data Warehouse (přehled)](sql-data-warehouse-manage-compute-overview.md) |Výkon škálování možnosti v Azure SQL Data Warehouse. Horizontální navýšení kapacity úpravou Dwu nebo pozastavení a obnovení výpočetní prostředky, abyste ušetřili náklady. |
| 32 |[Spravovat výpočetní výkon v Azure SQL Data Warehouse (portál Azure)](sql-data-warehouse-manage-compute-portal.md) |Azure úkoly portálu pro správu výpočetního výkonu. Škálovat výpočetní prostředky úpravou Dwu. Nebo, pozastavení a obnovení výpočetní prostředky, abyste ušetřili náklady. |
| 33 |[Spravovat výpočetní výkon v Azure SQL Data Warehouse (PowerShell)](sql-data-warehouse-manage-compute-powershell.md) |Úkoly prostředí PowerShell pro správu výpočetního výkonu. Škálovat výpočetní prostředky úpravou Dwu. Nebo, pozastavení a obnovení výpočetní prostředky, abyste ušetřili náklady. |
| 34 |[Spravovat výpočetní výkon v Azure SQL Data Warehouse (REST)](sql-data-warehouse-manage-compute-rest-api.md) |Úkoly prostředí PowerShell pro správu výpočetního výkonu. Škálovat výpočetní prostředky úpravou Dwu. Nebo, pozastavení a obnovení výpočetní prostředky, abyste ušetřili náklady. |
| 35 |[Spravovat výpočetní výkon v Azure SQL Data Warehouse (T-SQL)](sql-data-warehouse-manage-compute-tsql.md) |Příkaz Transact-SQL (T-SQL) úlohy škálovatelný výkon úpravou Dwu. Uložte náklady škálování zpět v době mimo špičku. |
| 36 |[Monitorování úloh pomocí zobrazení dynamických zpráv](sql-data-warehouse-manage-monitor.md) |Naučte se monitorovat pomocí zobrazení dynamické správy úlohy. |
| 37 |[Spravovat databáze v Azure SQL Data Warehouse](sql-data-warehouse-overview-manage.md) |Přehled správy databází SQL Data Warehouse. Zahrnuje nástroje pro správu, Dwu a Škálováním na více systémů, řešení potíží s výkon dotazů, vytvoření zásad zabezpečení a obnovení databáze z poškození dat nebo z místní výpadku výkonu. |
| 38 |[Monitorování uživatelských dotazů v Azure SQL Data Warehouse](sql-data-warehouse-overview-manage-user-queries.md) |Přehled důležité informace, osvědčené postupy a úlohy pro monitorování uživatelských dotazů v Azure SQL Data Warehouse |
| 39 |[Obnovení SQL Data Warehouse](sql-data-warehouse-restore-database-overview.md) |Přehled možností obnovení databáze pro obnovení databáze v Azure SQL Data Warehouse. |
| 40 |[Obnovit Azure SQL Data Warehouse (portál)](sql-data-warehouse-restore-database-portal.md) |Azure portálu úlohy pro obnovení Azure SQL Data Warehouse. |
| 41 |[Obnovit Azure SQL Data Warehouse (PowerShell)](sql-data-warehouse-restore-database-powershell.md) |Prostředí PowerShell úlohy pro obnovení Azure SQL Data Warehouse. |
| 42 |[Obnovit Azure SQL Data Warehouse (REST API)](sql-data-warehouse-restore-database-rest-api.md) |Úlohy REST API pro obnovení Azure SQL Data Warehouse. |

## <a name="tables-and-indexes"></a>Tabulky a indexy
| &nbsp; | Nadpis | Popis |
| ---:|:--- |:--- |
| 43 |[Datové typy pro tabulky v SQL Data Warehouse](sql-data-warehouse-tables-data-types.md) |Začínáme s datovými typy pro tabulky Azure SQL Data Warehouse. |
| 44 |[Distribuce tabulek v SQL Data Warehouse](sql-data-warehouse-tables-distribute.md) |Začínáme s distribuci tabulek v Azure SQL Data Warehouse. |
| 45 |[Indexování tabulek v SQL Data Warehouse](sql-data-warehouse-tables-index.md) |Začínáme s tabulkou indexování v Azure SQL Data Warehouse. |
| 46 |[Přehled tabulek v SQL Data Warehouse](sql-data-warehouse-tables-overview.md) |Začínáme s Azure SQL Data Warehouse tabulky. |
| 47 |[Vytváření oddílů tabulky v SQL Data Warehouse](sql-data-warehouse-tables-partition.md) |Začínáme s vytváření oddílů tabulky v Azure SQL Data Warehouse. |
| 48 |[Správa statistiky u tabulek v SQL Data Warehouse](sql-data-warehouse-tables-statistics.md) |Začínáme s statistiky pro tabulky v Azure SQL Data Warehouse. |
| 49 |[Dočasné tabulky v SQL Data Warehouse](sql-data-warehouse-tables-temporary.md) |Začínáme s dočasných tabulek v Azure SQL Data Warehouse. |

## <a name="integrate"></a>Integrace
| &nbsp; | Nadpis | Popis |
| ---:|:--- |:--- |
| 50 |[Použít pro vytváření dat Azure s datovým skladem SQL](sql-data-warehouse-integrate-azure-data-factory.md) |Tipy pro používání Azure Data Factory (ADF) s datovým skladem SQL Azure pro vývoj řešení. |
| 51 |[SQL Data Warehouse pomocí Azure Machine Learning](sql-data-warehouse-integrate-azure-machine-learning.md) |Kurz pro používání Azure Machine Learning s Azure SQL Data Warehousem pro vývoj řešení. |
| 52 |[Použití služby Azure Stream Analytics s SQL Data Warehouse](sql-data-warehouse-integrate-azure-stream-analytics.md) |Tipy pro používání Azure Stream Analytics s datovým skladem SQL Azure pro vývoj řešení. |
| 53 |[SQL Data Warehouse pomocí Power BI](sql-data-warehouse-integrate-power-bi.md) |Tipy pro používání Power BI s datovým skladem SQL Azure pro vývoj řešení. |
| 54 |[Využít dalším službám pomocí SQL Data Warehouse](sql-data-warehouse-overview-integrate.md) |Nástroje a partnery, se řešení, které se integrují se službou SQL Data Warehouse. |

## <a name="load"></a>Načtení
| &nbsp; | Nadpis | Popis |
| ---:|:--- |:--- |
| 55 |[Načtení dat z Azure blob storage do Azure SQL Data Warehouse (Azure Data Factory)](sql-data-warehouse-load-from-azure-blob-storage-with-data-factory.md) |Naučte se načítat data pomocí Azure Data Factory. |
| 56 |[Načtení dat z Azure blob storage do SQL Data Warehouse (PolyBase)](sql-data-warehouse-load-from-azure-blob-storage-with-polybase.md) |Naučte se používat k načtení dat z Azure blob storage do SQL Data Warehouse PolyBase. Načtení několik tabulek z veřejná data do schématu Contoso prodejní datového skladu. |
| 57 |[Načtení dat z SQL serveru do Azure SQL Data Warehouse (AZCopy)](sql-data-warehouse-load-from-sql-server-with-azcopy.md) |Využívá bcp k exportu dat z SQL Serveru do plochých souborů, AZCopy k importu dat do Azure Blob Storage a PolyBase ke zpracování příjmu dat do Azure SQL Data Warehouse. |
| 58 |[Načtení dat z SQL serveru do Azure SQL Data Warehouse (ploché soubory)](sql-data-warehouse-load-from-sql-server-with-bcp.md) |Pro malou velikost dat využívá bcp k exportu data z SQL Serveru do plochých souborů a k importu dat přímo do Azure SQL Data Warehouse. |
| 59 |[Načtení dat z SQL serveru do Azure SQL Data Warehouse služby SSIS)](sql-data-warehouse-load-from-sql-server-with-integration-services.md) |Ukazuje, jak vytvořit balíček integrační služby SSIS (SQL Server) pro přesun dat z široké škály zdrojů dat do SQL Data Warehouse. |
| 60 |[Načtení dat pomocí funkce PolyBase v SQL Data Warehouse](sql-data-warehouse-load-from-sql-server-with-polybase.md) |Využívá bcp k exportu dat z SQL Serveru do plochých souborů, AZCopy k importu dat do Azure Blob Storage a PolyBase ke zpracování příjmu dat do Azure SQL Data Warehouse. |
| 61 |[Příručka pro používání funkce PolyBase v SQL Data Warehouse](sql-data-warehouse-load-polybase-guide.md) |Pokyny a doporučení pro používání funkce PolyBase v SQL Data Warehouse scénáře. |
| 62 |[Načtení ukázkových dat do SQL Data Warehouse](sql-data-warehouse-load-sample-databases.md) |Načtení ukázkových dat do SQL Data Warehouse |
| 63 |[Načtení dat pomocí bcp](sql-data-warehouse-load-with-bcp.md) |Zjistěte, co je bcp a jak tento nástroj používat pro scénáře datových skladů. |
| 64 |[Načtení dat do Azure SQL Data Warehouse](sql-data-warehouse-overview-load.md) |Další běžné scénáře pro data načítání do SQL Data Warehouse. Patří sem pomocí PolyBase, úložiště objektů blob v Azure, plochých souborů a přesouvání disku. Můžete také použít nástroje třetích stran. |

## <a name="migrate"></a>Migrace
| &nbsp; | Nadpis | Popis |
| ---:|:--- |:--- |
| 65 |[Migrace do SQL Data Warehouse kódu SQL](sql-data-warehouse-migrate-code.md) |Tipy k migraci kódu SQL Azure SQL Data Warehouse na vývoj řešení. |
| 66 |[Migrace dat](sql-data-warehouse-migrate-data.md) |Tipy pro migraci dat do Azure SQL Data Warehouse na vývoj řešení. |
| 67 |[Nástroj migrace data Warehouse (Preview)](sql-data-warehouse-migrate-migration-utility.md) |Migrace do SQL Data Warehouse. |
| 68 |[Migrace vašeho schématu do SQL Data Warehouse](sql-data-warehouse-migrate-schema.md) |Tipy pro migraci schématu do Azure SQL Data Warehouse na vývoj řešení. |
| 69 |[Migrace řešení do SQL Data Warehouse](sql-data-warehouse-overview-migrate.md) |Migrace pokyny k uvedení řešení pro platformu Azure SQL Data Warehouse. |

## <a name="partners"></a>Partneři
| &nbsp; | Nadpis | Popis |
| ---:|:--- |:--- |
| 70 |[SQL Data Warehouse business intelligence partnery](sql-data-warehouse-partner-business-intelligence.md) |Seznamy partnerů třetích stran business intelligence s řešení, které podporují SQL Data Warehouse. |
| 71 |[Partneři integraci dat SQL Data Warehouse](sql-data-warehouse-partner-data-integration.md) |Seznam partnerů třetích stran s řešeními integraci dat, které podporují Azure SQL Data Warehouse. |
| 72 |[Partneři správy dat SQL Data Warehouse](sql-data-warehouse-partner-data-management.md) |Seznamy z partnerských serverů pro správu dat třetích stran s řešení, které podporují SQL Data Warehouse. |

## <a name="reference"></a>Referenční informace
| &nbsp; | Nadpis | Popis |
| ---:|:--- |:--- |
| 73 |[Témata odkazů pro SQL Data Warehouse](sql-data-warehouse-overview-reference.md) |Referenční obsahu odkazy pro SQL Data Warehouse. |
| 74 |[Rutiny prostředí PowerShell a rozhraní REST API pro SQL Data Warehouse](sql-data-warehouse-reference-powershell-cmdlets.md) |Najít nejvyšší rutin prostředí PowerShell pro Azure SQL Data Warehouse, včetně toho, jak pozastavení a obnovení databáze. |
| 75 |[Jazykové elementy](sql-data-warehouse-reference-tsql-language-elements.md) |Seznam odkazů na referenční obsah pro elementy jazyka Transact-SQL použít pro SQL Data Warehouse. |
| 76 |[Témata Transact-SQL](sql-data-warehouse-reference-tsql-statements.md) |Odkazy na referenční obsah pro SQL Data Warehouse používá témata Transact-SQL. |
| 77 |[Systémová zobrazení](sql-data-warehouse-reference-tsql-system-views.md) |Odkazy na systémové zobrazení obsahu pro SQL Data Warehouse. |

## <a name="security"></a>Zabezpečení
| &nbsp; | Nadpis | Popis |
| ---:|:--- |:--- |
| 78 |[SQL Data Warehouse – podpora klientů nižší úrovně pro auditování a dynamické maskování dat](sql-data-warehouse-auditing-downlevel-clients.md) |Další informace o podpoře klientů nižší úrovně SQL Data Warehouse pro auditování dat |
| 79 |[Auditování v Azure SQL Data Warehouse](sql-data-warehouse-auditing-overview.md) |Začínáme s auditování v Azure SQL Data Warehouse |
| 80 |[Začínáme s transparentní dat šifrování (TDE) v SQL Data Warehouse](sql-data-warehouse-encryption-tde.md) |Transparentní šifrování dat (šifrování TDE) v SQL Data Warehouse |
| 81 |[Začínáme s transparentní šifrování dat (TDE)](sql-data-warehouse-encryption-tde-tsql.md) |Transparentní šifrování dat (šifrování TDE) v datovém skladu SQL (T-SQL) |
| 82 |[Zabezpečení databáze v SQL Data Warehouse](sql-data-warehouse-overview-manage-security.md) |Tipy pro zabezpečení databáze v Azure SQL Data Warehouse na vývoj řešení. |

## <a name="miscellaneous"></a>Různé
| &nbsp; | Nadpis | Popis |
| ---:|:--- |:--- |
| 83 |[Instalace sady Visual Studio a SSDT pro SQL Data Warehouse](sql-data-warehouse-install-visual-studio.md) |Instalace sady Visual Studio a SQL Server Development Tools (SSDT) pro Azure SQL Data Warehouse |
| 84 |[Migrace na podrobnosti o úložiště Premium](sql-data-warehouse-migrate-to-premium-storage.md) |Pokyny k migraci existující datový sklad SQL do úložiště úrovně premium |
| 85 |[Začínáme s detekce hrozeb.](sql-data-warehouse-security-threat-detection.md) |Jak začít pracovat s detekce hrozeb. |
| 86 |[Limity kapacity SQL Data Warehouse](sql-data-warehouse-service-capacity-limits.md) |Maximální hodnoty pro připojení, databáze, tabulky a dotazy pro SQL Data Warehouse. |
| 87 |[Řešení potíží s datovým skladem Azure SQL](sql-data-warehouse-troubleshoot.md) |Řešení potíží s datovým skladem Azure SQL. |

